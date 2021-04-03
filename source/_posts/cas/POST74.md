---
title: 基于Redis的CAS集群
tags: CAS
category: CAS
abbrlink: 34811
date: 2018-01-08 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0081.jpg)

单点登录（SSO）是复杂应用系统的基本需求，Yale CAS是目前常用的开源解决方案。
<!--more-->
CAS认证中心，基于其特殊作用，自然会成为整个应用系统的核心，所有应用系统的认证工作，都将请求到CAS来完成。因此CAS服务器是整个应用的关键节点，CAS发生故障，所有系统都将陷入瘫痪。同时，CAS的负载能力要足够强，能够承担所有的认证请求响应。利用负载均衡和集群技术，不仅能克服CAS单点故障，同时将认证请求分布到多台CAS服务器上，有效减轻单台CAS服务器的请求压力。下面将基于CAS 3.4.5来讨论下CAS集群。

CAS的工作原理，主要是基于票据(Ticket)来实现的（参见 CAS基本原理）。CAS票据，存储在TicketRegistry中，因此要想实现CAS Cluster, 必须要多台CAS之间共享所有的Ticket，采用统一的TicketRegistry，可以达到此目的。  缺省的CAS实现中，TicketRegistry在内存中实现，不同的CAS服务器有自己单独的TicketRegistry，因此是不支持分布式集群的。但CAS提供了支持TicketRegistry分布式的接口org.jasig.cas.ticket.registry.AbstractDistributedTicketRegistry，我们可以实现这个接口实现多台CAS服务器TicketRegistry共享，从而实现CAS集群。

同时，较新版本CAS使用SpringWebFlow作为认证流程，而webflow需要使用session存储流程相关信息，因此实现CAS集群，我们还得需要让不同服务器的session进行共享。

我们采用内存数据库Redis来实现TicketRegistry,让多个CAS服务器共用同一个TicketRegistry。同样方法，我们让session也存储在Redis中，达到共享session的目的。下面就说说如何用Redis来实现TicketRegistry，我们使用Java调用接口Jedis来操作Redis，代码如下：

 
```java
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.Collection;
import org.jasig.cas.ticket.Ticket;
import org.jasig.cas.ticket.TicketGrantingTicket;
import org.jasig.cas.ticket.registry.AbstractDistributedTicketRegistry;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

/*  
 *  TicketRegistry using Redis, to solve CAS Cluster.   
 */

public class RedisTicketRegistry extends AbstractDistributedTicketRegistry {
	private static int redisDatabaseNum;
	private static String hosts;
	private static int port;
	private static int st_time; // ST最大空闲时间
	private static int tgt_time; // TGT最大空闲时间
	private static JedisPool cachePool;

	static {
		redisDatabaseNum = PropertiesConfigUtil.getPropertyInt("redis_database_num");
		hosts = PropertiesConfigUtil.getProperty("hosts");
		port = PropertiesConfigUtil.getPropertyInt("port");
		st_time = PropertiesConfigUtil.getPropertyInt("st_time");
		tgt_time = PropertiesConfigUtil.getPropertyInt("tgt_time");
		cachePool = new JedisPool(new JedisPoolConfig(), hosts, port);
	}

	public void addTicket(Ticket ticket) {
		Jedis jedis = cachePool.getResource();
		jedis.select(redisDatabaseNum);
		int seconds = 0;
		String key = ticket.getId();
		if (ticket instanceof TicketGrantingTicket) {
			// key =
			// ((TicketGrantingTicket)ticket).getAuthentication().getPrincipal().getId();
			seconds = tgt_time / 1000;
		} else {
			seconds = st_time / 1000;
		}
		ByteArrayOutputStream bos = new ByteArrayOutputStream();
		ObjectOutputStream oos = null;
		try {
			oos = new ObjectOutputStream(bos);
			oos.writeObject(ticket);
		} catch (Exception e) {
			log.error("adding ticket to redis error.");
		} finally {
			try {
				if (null != oos)
					oos.close();
			} catch (Exception e) {
				log.error("oos closing error when adding ticket to redis.");
			}
		}
		jedis.set(key.getBytes(), bos.toByteArray());
		jedis.expire(key.getBytes(), seconds);
		cachePool.returnResource(jedis);
	}
	public Ticket getTicket(final String ticketId) {
		return getProxiedTicketInstance(getRawTicket(ticketId));
	}
	private Ticket getRawTicket(final String ticketId) {
		if (null == ticketId)
			return null;
		Jedis jedis = cachePool.getResource();
		jedis.select(redisDatabaseNum);
		Ticket ticket = null;
		ByteArrayInputStream bais = new ByteArrayInputStream(jedis.get(ticketId.getBytes()));
		ObjectInputStream ois = null;
		try {
			ois = new ObjectInputStream(bais);
			ticket = (Ticket) ois.readObject();
		} catch (Exception e) {
			log.error("getting ticket to redis error.");
		} finally {
			try {
				if (null != ois)
					ois.close();
			} catch (Exception e) {
				log.error("ois closing error when getting ticket to redis.");
			}
		}
		cachePool.returnResource(jedis);
		return ticket;
	}
	public boolean deleteTicket(final String ticketId) {
		if (ticketId == null) {
			return false;
		}
		Jedis jedis = cachePool.getResource();
		jedis.select(redisDatabaseNum);
		jedis.del(ticketId.getBytes());
		cachePool.returnResource(jedis);
		return true;
	}
	public Collection<Ticket> getTickets() {
	throw new UnsupportedOperationException("GetTickets not supported.");
	}
	protected boolean needsCallback() {
		return false;
	}
	protected void updateTicket(final Ticket ticket) {
		addTicket(ticket);
	}
}

```
同时，我们在ticketRegistry.xml配置文件中，将TicketRegistry实现类指定为上述实现。即修改下面的class值


```xml
 <!-- Ticket Registry -->  
    <bean id="ticketRegistry" class="org.jasig.cas.util.RedisTicketRegistry" />  
<!--     <bean id="ticketRegistry" class="org.jasig.cas.ticket.registry.DefaultTicketRegistry" />  -->   
```
因为使用了Redis的expire功能，注释掉如下代码：


```xml
<!-- TICKET REGISTRY CLEANER -->  
lt;!--  <bean id="ticketRegistryCleaner" class="org.jasig.cas.ticket.registry.support.DefaultTicketRegistryCleaner"  
    p:ticketRegistry-ref="ticketRegistry" />  
<bean id="jobDetailTicketRegistryCleaner" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean"  
    p:targetObject-ref="ticketRegistryCleaner"  
    p:targetMethod="clean" />  
<bean id="triggerJobDetailTicketRegistryCleaner" class="org.springframework.scheduling.quartz.SimpleTriggerBean"  
    p:jobDetail-ref="jobDetailTicketRegistryCleaner"  
    p:startDelay="20000"  
    p:repeatInterval="5000000" /> -->  

```
通过上述实现TicketRegistry,多台CAS服务器就可以共用同一个TicketRegistry。对于如何共享session，我们可以采用现成的第三方工具tomcat-redis-session-manager直接集成即可。对于前端web服务器(如nginx)，做好负载均衡配置，将认证请求分布转发给后面多台CAS，实现负载均衡和容错目的。

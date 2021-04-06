---
title: 定时任务框架
tags:
  - cms
  - java
copyright: true
category: cms
abbrlink: 35236
date: 2017-12-13 20:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0018.jpg)

ScheduleIterator接口 import java.util.Date;public interface ScheduleIterator {    public Date next();//获取下一个触发的时间点} Scheduler类 import java.
<!--more-->

定时任务框架代码。 
整个框架就3个类： 
- ScheduleIterator.java 


```java
import java.util.Date;

public interface ScheduleIterator {
	Date next();
}
```


- Scheduler.java 


```java
import java.util.Date;
import java.util.Timer;
import java.util.TimerTask;

public class Scheduler {
	class SchedulerTimerTask extends TimerTask {
		private SchedulerTask schedulerTask;
		private ScheduleIterator iterator;

		public SchedulerTimerTask(SchedulerTask schedulerTask, ScheduleIterator iterator) {
			this.schedulerTask = schedulerTask;
			this.iterator = iterator;
		}

		public void run() {
			schedulerTask.run();
			reschedule(schedulerTask, iterator);
		}
	}

	private final Timer timer = new Timer();

	public Scheduler() {
	}

	public void cancel() {
		timer.cancel();
	}

	public void schedule(SchedulerTask schedulerTask, ScheduleIterator iterator) {

		Date time = iterator.next();
		if (time == null) {
			schedulerTask.cancel();
		} else {
			synchronized (schedulerTask.lock) {
				if (schedulerTask.state != SchedulerTask.VIRGIN) {
					throw new IllegalStateException("Task already scheduled or cancelled");
				}
				schedulerTask.state = SchedulerTask.SCHEDULED;
				schedulerTask.timerTask = new SchedulerTimerTask(schedulerTask, iterator);
				timer.schedule(schedulerTask.timerTask, time);
			}
		}
	}

	private void reschedule(SchedulerTask schedulerTask, ScheduleIterator iterator) {

		Date time = iterator.next();
		if (time == null) {
			schedulerTask.cancel();
		} else {
			synchronized (schedulerTask.lock) {
				if (schedulerTask.state != SchedulerTask.CANCELLED) {
					schedulerTask.timerTask = new SchedulerTimerTask(schedulerTask, iterator);
					timer.schedule(schedulerTask.timerTask, time);
				}
			}
		}
	}

}
```


- SchedulerTask.java 


```java
import java.util.TimerTask;

public abstract class SchedulerTask implements Runnable {
	final Object lock = new Object();

	int state = VIRGIN;
	static final int VIRGIN = 0;
	static final int SCHEDULED = 1;
	static final int CANCELLED = 2;

	TimerTask timerTask;

	protected SchedulerTask() {
	}

	public abstract void run();

	public boolean cancel() {
		synchronized (lock) {
			if (timerTask != null) {
				timerTask.cancel();
			}
			boolean result = (state == SCHEDULED);
			state = CANCELLED;
			return result;
		}
	}

	public long scheduledExecutionTime() {
		synchronized (lock) {
			return timerTask == null ? 0 : timerTask.scheduledExecutionTime();
		}
	}

}
```

自己写的ScheduleIterator  实现类 以及一个测试类 

- SimpleScheduleIterator.java 

```java
import java.util.Calendar;
import java.util.Date;
import java.util.GregorianCalendar;

import com.jeecms.common.task.scheduling.ScheduleIterator;

/**
 * 时间进度迭代器 <li>返回 月/周/天/小时/分钟/秒 计划的下一次执行时间</li> <li>约定：参数以逗号分隔,*号表示无值</li> <li>
 * 参数解释： <br>
 * 第一位：每个月的第几周</br> <br>
 * 第二位：每周的第几天</br> <br>
 * 第三位：天(几号)</br> <br>
 * 第四位：小时(24小时制)</br> <br>
 * 第五位：分钟</br> <br>
 * 第六位：秒</br></li> <li>参数样例： <br>
 * 1,6,4,15,20,30 表示 从今天的15:20:30开始，每隔一个月执行一次,即下次执行时间是 下个月的第一周的第6天的15:20:30</br>
 * <br>
 * *,6,4,15,20,30 表示 从今天的15:20:30开始，每隔一周执行一次,即下次执行时间是 下一周的第6天的15:20:30</br> <br>
 * *,*,4,15,20,30 表示 从今天的15:20:30开始，每隔一天执行一次,即下次执行时间是 下一天的15:20:30</br> <br>
 * *,*,*,15,20,30 表示 从今天的15:20:30开始，每隔一小时执行一次,即下次执行时间是 16:20:30</br> <br>
 * *,*,*,*,20,30 表示 从这个小时的20:30开始，每隔一分钟执行一次,即下次执行时间是 *:21:30</br> <br>
 * *,*,*,*,*,30 表示 从当前时间的30秒开始，每隔一秒执行一次,即下次执行时间是 *:*:31</br></li>
 * 
 * @author javacoo
 * @since 2011-11-03
 */
public class SimpleScheduleIterator implements ScheduleIterator {
	private final ScheduleParamBean scheduleParamBean;
	private final Calendar calendar = Calendar.getInstance();
	private final Calendar orginCalendar = Calendar.getInstance();

	public SimpleScheduleIterator(final ScheduleParamBean scheduleParamBean) {
		this(scheduleParamBean, new Date());
	}

	public SimpleScheduleIterator(final ScheduleParamBean scheduleParamBean, Date date) {
		this.scheduleParamBean = scheduleParamBean;

		orginCalendar.setTime(date);
		calendar.setTime(date);
		if (null != scheduleParamBean.getWeekOfMonth()) {
			calendar.set(Calendar.WEEK_OF_MONTH, scheduleParamBean.getWeekOfMonth());
		}
		// 如果设置了每周的第几天和一个月的第几天，则忽略一个月的第几天
		if (null != scheduleParamBean.getDayOfWeek()) {
			calendar.set(Calendar.DAY_OF_WEEK, scheduleParamBean.getDayOfWeek());
		} else if (null != scheduleParamBean.getDayOfMonth()) {
			calendar.set(Calendar.DAY_OF_MONTH, scheduleParamBean.getDayOfMonth());
		}
		if (null != scheduleParamBean.getHourOfDay()) {
			calendar.set(Calendar.HOUR_OF_DAY, scheduleParamBean.getHourOfDay());
		}
		if (null != scheduleParamBean.getMinute()) {
			calendar.set(Calendar.MINUTE, scheduleParamBean.getMinute());
		}
		if (null != scheduleParamBean.getSecond()) {
			calendar.set(Calendar.SECOND, scheduleParamBean.getSecond());
		}
		calendar.set(Calendar.MILLISECOND, 0);

		if (!calendar.getTime().before(date)) {
			System.out.println(calendar.getTime() + "大于当前时间：" + date);
			if (null != scheduleParamBean.getWeekOfMonth()) {
				calendar.add(Calendar.MONTH, -1);
			} else if (null != scheduleParamBean.getDayOfWeek()) {
				calendar.add(Calendar.DAY_OF_WEEK, -6);
			} else if (null != scheduleParamBean.getDayOfMonth()) {
				calendar.add(Calendar.DAY_OF_MONTH, -1);
			} else if (null != scheduleParamBean.getHourOfDay()) {
				calendar.add(Calendar.HOUR_OF_DAY, -1);
			} else if (null != scheduleParamBean.getMinute()) {
				calendar.add(Calendar.MINUTE, -1);
			} else if (null != scheduleParamBean.getSecond()) {
				calendar.add(Calendar.SECOND, -1);
			}
		} else {
			System.out.println(calendar.getTime() + "小于当前时间：" + date);
			if (null != scheduleParamBean.getDayOfMonth()) {
				calendar.add(Calendar.DAY_OF_MONTH, orginCalendar.get(Calendar.DAY_OF_MONTH) - scheduleParamBean.getDayOfMonth());
			} else if (null != scheduleParamBean.getHourOfDay()) {
				calendar.add(Calendar.HOUR_OF_DAY, orginCalendar.get(Calendar.HOUR_OF_DAY) - scheduleParamBean.getHourOfDay());
			} else if (null != scheduleParamBean.getMinute()) {
				calendar.add(Calendar.MINUTE, orginCalendar.get(Calendar.MINUTE) - scheduleParamBean.getMinute());
			} else if (null != scheduleParamBean.getSecond()) {
				calendar.add(Calendar.SECOND, orginCalendar.get(Calendar.SECOND) - scheduleParamBean.getSecond());
			}
		}
	}

	public Date next() {
		if (null != scheduleParamBean.getWeekOfMonth()) {
			calendar.add(Calendar.MONTH, 1);
		} else if (null != scheduleParamBean.getDayOfWeek()) {
			calendar.add(Calendar.DAY_OF_WEEK, 6);
		} else if (null != scheduleParamBean.getDayOfMonth()) {
			calendar.add(Calendar.DAY_OF_MONTH, 1);
		} else if (null != scheduleParamBean.getHourOfDay()) {
			calendar.add(Calendar.HOUR_OF_DAY, 1);
		} else if (null != scheduleParamBean.getMinute()) {
			calendar.add(Calendar.MINUTE, 1);
		} else if (null != scheduleParamBean.getSecond()) {
			calendar.add(Calendar.SECOND, 1);
		}
		System.out.println("下次执行时间:" + calendar.getTime());
		return calendar.getTime();
	}

}
```

- ScheduleParamBean.java 

```java
/**
 * 时间计划参数bean
 * 
 * @author javacoo
 * @since 2011-11-04
 */
public class ScheduleParamBean {
	/** 每个月的第几周,每周的第几天,每个月的第几天,小时(24小时制),分钟,秒 */
	private Integer weekOfMonth, dayOfWeek, dayOfMonth, hourOfDay, minute, second;

	public ScheduleParamBean() {

	}

	public ScheduleParamBean(Integer weekOfMonth, Integer dayOfWeek, Integer dayOfMonth, Integer hourOfDay, Integer minute, Integer second) {
		super();
		this.weekOfMonth = weekOfMonth;
		this.dayOfWeek = dayOfWeek;
		this.dayOfMonth = dayOfMonth;
		this.hourOfDay = hourOfDay;
		this.minute = minute;
		this.second = second;
	}

	public Integer getWeekOfMonth() {
		return weekOfMonth;
	}

	public void setWeekOfMonth(Integer weekOfMonth) {
		this.weekOfMonth = weekOfMonth;
	}

	public Integer getDayOfWeek() {
		return dayOfWeek;
	}

	public void setDayOfWeek(Integer dayOfWeek) {
		this.dayOfWeek = dayOfWeek;
	}

	public Integer getDayOfMonth() {
		return dayOfMonth;
	}

	public void setDayOfMonth(Integer dayOfMonth) {
		this.dayOfMonth = dayOfMonth;
	}

	public Integer getHourOfDay() {
		return hourOfDay;
	}

	public void setHourOfDay(Integer hourOfDay) {
		this.hourOfDay = hourOfDay;
	}

	public Integer getMinute() {
		return minute;
	}

	public void setMinute(Integer minute) {
		this.minute = minute;
	}

	public Integer getSecond() {
		return second;
	}

	public void setSecond(Integer second) {
		this.second = second;
	}

	@Override
	public String toString() {
		return "ScheduleParamBean [dayOfMonth=" + dayOfMonth + ", dayOfWeek=" + dayOfWeek + ", hourOfDay=" + hourOfDay + ", minute=" + minute
				+ ", second=" + second + ", weekOfMonth=" + weekOfMonth + "]";
	}

}

```

- TestSchedule.java 

```java
/**
 * 测试
 * 
 * @author javacoo
 * @since 2011-11-03
 */
public class TestSchedule {
	private final Scheduler scheduler = new Scheduler();

	private final ScheduleParamBean scheduleParamBean;

	public TestSchedule(final ScheduleParamBean scheduleParamBean) {
		this.scheduleParamBean = scheduleParamBean;
	}

	public void start() {
		scheduler.schedule(new SchedulerTask() {
			public void run() {
				execute();
			}

			private void execute() {
				System.out.println("任务执行");
			}
		}, new SimpleScheduleIterator(scheduleParamBean));
	}

	public static void main(String[] args) {

		ScheduleParamBean scheduleParamBean = new ScheduleParamBean();
		scheduleParamBean.setWeekOfMonth(1);
		scheduleParamBean.setDayOfWeek(6);
		scheduleParamBean.setDayOfMonth(4);
		scheduleParamBean.setHourOfDay(16);
		scheduleParamBean.setMinute(22);
		scheduleParamBean.setSecond(0);
		TestSchedule test = new TestSchedule(scheduleParamBean);
		test.start();

	}

}
```


以下是整合到JEECMS采集器多线程版的代码片段 

```java
/** 
 * 开始执行采集任务 
 */ 
public boolean start(Integer id) { 
CmsAcquisition acqu = cmsAcquisitionMng.findById(id); 
if (acqu == null || acqu.getStatus() == CmsAcquisition.START) { 
return false; 
} 
TaskSchedulerManage taskManage = new TaskSchedulerManage(this,acqu); 
taskManage.start(); 
return true; 
} 


private class TaskSchedulerManage { 
private final Scheduler scheduler = new Scheduler(); 
private final MultiThreadAcquisitionSvcImpl multiThreadAcquisitionSvc; 
private final CmsAcquisition acqu; 
public TaskSchedulerManage(MultiThreadAcquisitionSvcImpl multiThreadAcquisitionSvc,CmsAcquisition acqu) { 
this.multiThreadAcquisitionSvc = multiThreadAcquisitionSvc; 
this.acqu = acqu; 
} 
public void start() { 
scheduler.schedule(new SchedulerTask() { 
public void run() { 
processer(); 
} 
private void processer() { 
System.out.println("============开始执行计划任务================="); 
new Thread(new MainThreadProcesser(multiThreadAcquisitionSvc,acqu)).start(); 
} 
}, new SimpleScheduleIterator(new ScheduleParamBean(1,6,4,17,24,30))); 
} 
} 
```

注：关键是 ScheduleIterator实现类了，框架有多强大 完全取决于你的ScheduleIterator实现类了。 

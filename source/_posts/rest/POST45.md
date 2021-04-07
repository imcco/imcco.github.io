---
title: REST客户端编程实例
category:
  - BackEnd
tags: restClient
abbrlink: 14233
date: 2017-09-18 19:41:52
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0055.jpg)

# 导读

JAX-RS 2.0 又称 JSR 339 不仅定义了一套用于构建 RESTful 网络服务的 API，同时也通过增强客户端 API 功能简化了REST 客户端的构建过程。
<!--more-->
> JAX-RS: Java API for RESTful Web Services是一个[Java编程语言](http://zh.wikipedia.org/wiki/Java)的[应用程序接口](http://zh.wikipedia.org/wiki/%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E6%8E%A5%E5%8F%A3),支持按照 [表象化状态转变](http://zh.wikipedia.org/wiki/REST) (REST)架构风格创建Web服务[Web服务](http://zh.wikipedia.org/wiki/Web%E6%9C%8D%E5%8A%A1)[[1\]](http://zh.wikipedia.org/wiki/JAX-RS#cite_note-1). JAX-RS使用了[Java SE 5](http://zh.wikipedia.org/wiki/Java_SE)引入的[Java 标注](http://zh.wikipedia.org/wiki/Java_%E6%A0%87%E6%B3%A8)来简化Web服务客户端和服务端的开发和部署 [[wikipedia](http://zh.wikipedia.org/wiki/JAX-RS)]。

在下面的教程中，我们将为一个预先设置好的 REST 服务构建一个客户端，并在这个过程中探索新的构建选项。例如，如何处理同步或者异步的请求，如何给一个请求注册一个回调，如何指定调用对象来构建一个请求使得请求可以被延迟执行。再或者比如，如何使用客户端请求和相应的过滤方法来过滤客户端与服务器之前的通信。

# 我们开始吧

对于想要重建下述客户端例子的读者，我已经使用 Maven 创建好了一个完整的 RESTful 网络服务程序。程序中有内嵌的应用程序服务器，以及一个可独立运行的应用服务器 （war-file 可以通过下文中的下载地址获取）。

请根据下面的一系列命令来下载并启动 REST 服务器 （下载所有依赖可能会耗费些时间……）：

```xml
clone https://bitbucket.org/hascode/jaxrs2-client-tutorial.git && cd jaxrs2-client-tutorial && make rest-server
```

现在，让我们先来看看这个 REST 服务的一些实现细节和我们的客户端示例中要用到的对象。如果你对这些没什么兴趣，大可以略过服务端的细节直接去看客户端示例。

## REST 服务

下面的代码就是个客户端提供服务的 REST 服务。这里的 BookRepository 就是一个由 *@Singleton* 和 *@Startup* 修饰的简单 session bean，这个 bean 用来模拟存储或获取 Book Entity。服务对外提供了保存一本书、删除一本书、根据标识查找书籍和获取所有可用书籍的接口。当一本书被保存在服务端时，服务器会为该书生成一个 id，并会返回一个 entity 或一组 entity 的 JSON 数据。

```java
package com.hascode.tutorial.jaxrs.server;

import java.util.List;

import javax.ejb.EJB;
import javax.ejb.Stateless;
import javax.ws.rs.Consumes;
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.GenericEntity;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;

import com.hascode.tutorial.jaxrs.entity.Book;

@Stateless
@Path("/book")
public class BookStoreService {
    @EJB
    private BookRepository bookRepository;

    @POST
    @Consumes(MediaType.APPLICATION_JSON)
    @Produces(MediaType.APPLICATION_JSON)
    public Response saveBook(final Book book) {
        Book bookPersisted = bookRepository.saveBook(book);
        return Response.ok(bookPersisted).build();
    }

    @DELETE
    @Path("/{id}")
    public Response deleteBook(final @PathParam("id") String id) {
        bookRepository.deleteBook(id);
        return Response.ok().build();
    }

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public Response getAll() {
        List<Book> books = bookRepository.getAll();
        GenericEntity<List<Book>> bookWrapper = new GenericEntity<List<Book>>(books) {};
        return Response.ok(bookWrapper).build();
    }

    @GET
    @Path("/{id}")
    @Produces(MediaType.APPLICATION_JSON)
    public Response getById(final @PathParam("id") String id) {
        Book book = bookRepository.getById(id);
        return Response.ok(book).build();
    }
}
```

**备注：**我修改了应用服务器，以便使用 Jackson 提供的服务发现机制处理 JSON 数据。

## Book Entity

下面代码中的 bean 就是贯穿本教程的 Book Entity，它包含id、书名、价格和出版日期属性。

```java
package com.hascode.tutorial.jaxrs.entity;

import java.io.Serializable;
import java.math.BigDecimal;
import java.util.Calendar;

public class Book implements Serializable {
    private static final long serialVersionUID = 1L;

    private String id;
    private String title;
    private BigDecimal price;
    private Calendar published;

    // getter+setter..
}
```

# 创建并绑定一个客户端

我们可以创建一个 REST 客户端，将其绑定到一个特定的目标 URL 上。并且为它指定专属的、参数化的路径。具体步骤如下：

- **通过 ClientBuilder 获取一个客户端的引用：***Client client = ClientBuilder.newClient();*
- **使用 target() 方法将客户端绑定到 REST 服务上提供的某个 URL：***client.target(“http://localhost:8080/myrestservice”);*
- **通过 path() 和 resolveTemplate() 方法来处理动态的 URL 路径参数：***client.target(..).path(“{id}”).resolveTemplate(“id”, someId);*
- 使用 request() 函数来**初始化一个请求**并用后续的 post 或者 get 等方法来指定请求的类型，例如：*client.target(..).request().get();*
- 每一步都提供了**多样的可选择的参数和配置选项**，稍后的教程中我将用到其中的一些配置像异步请求、回调处理、还有过滤器注册和特性类等。

现在，让我们先看一些具备说明性的例子。

# 客户端例子

由于我把所有客户端示例都融进了 jUnit 和 Hamcrest 驱动的测试用例，因此下面的代码实际上在每一个测试用例中都有使用。不过为了让文章尽量简练，重复代码将在后面的代码示例中省略。

```java
private static final String REST_SERVICE_URL = "http://localhost:8080/tutorial/rs/book";

private static final String TITLE = "One big book";
private static final BigDecimal PRICE = new BigDecimal("20.0");
private static final GregorianCalendar PUBLISHED = new GregorianCalendar(
, 12, 24);

Client client = ClientBuilder.newClient().register(JacksonFeature.class);

public Book mockBook() {
	Book book = new Book();
	book.setTitle(TITLE);
	book.setPrice(PRICE);
	book.setPublished(PUBLISHED);
	return book;
}
```

唯一值得注意的是，我在客户端运行时中加入了 Jackson 框架，因此可以通过* javax.ws.rs.client.ClientBuilder* 来获取客户端实例。

## Maven 整合

所有代码示例运行都需要用到下面依赖：

```xml
<dependency>
	<groupId>org.glassfish.jersey.core</groupId>
	<artifactId>jersey-client</artifactId>
	<version>2.5</version>
</dependency>
<dependency>
	<groupId>org.glassfish.jersey.media</groupId>
	<artifactId>jersey-media-json-jackson</artifactId>
	<version>2.5</version>
</dependency>
```

## 基础操作

下面的示例中我们首先将一个书本实体的信息序列化成 JSON 格式，通过 POST 请求发送到服务端来保存这本书。

之后，我们使用客户端提供的* path() *和 *resolveTemplate() *方法通过匹配服务端返回值的协议来获取该本书的标识。

第三步， 我们获取所有可用图书的列表，并在最后删除掉刚才保存的那本书。

```java
@Test
public void crudExample() {
	// 1. Save a new book
	Book book = mockBook();
	Book bookPersisted = client
			.target(REST_SERVICE_URL)
			.request()
			.post(Entity.entity(book, MediaType.APPLICATION_JSON),
					Book.class);

	String bookId = bookPersisted.getId();
	assertThat(bookId, notNullValue());

	// 2. Fetch book by id
	Book book2 = client.target(REST_SERVICE_URL).path("/{bookId}")
			.resolveTemplate("bookId", bookId).request().get(Book.class);
	assertThat(book2, notNullValue());
	assertThat(book2.getTitle(), equalTo(TITLE));
	assertThat(book2.getPrice(), equalTo(PRICE));
	assertThat(book2.getPublished().getTime(), equalTo(PUBLISHED.getTime()));

	// 3. Fetch all books
	GenericType<List<Book>> bookType = new GenericType<List<Book>>() {
	}; // generic type to wrap a generic list of books
	List<Book> books = client.target(REST_SERVICE_URL).request()
			.get(bookType);
	assertThat(books.size(), equalTo(1));

	// 4. Delete a book
	client.target(REST_SERVICE_URL).path("/{bookId}")
			.resolveTemplate("bookId", bookId).request().delete();
	List<Book> books2 = client.target(REST_SERVICE_URL).request()
			.get(bookType);
	assertThat(books2.isEmpty(), equalTo(true));
}
```

## 异步处理

只要给请求构造器加一个简单的 *async() *方法，我们就可以使用 Java 的 *Future *API 提供的多种途径来异步地处理请求。

下面的例子中，我们在第一个请求中添加一本书，然后再删除它。最后获取所有可用图书的列表。

```java
@Test
public void asyncExample() throws Exception {
	Book book = mockBook();

	Future<Book> fb = client
			.target(REST_SERVICE_URL)
			.request()
			.async()
			.post(Entity.entity(book, MediaType.APPLICATION_JSON),
					Book.class);

	Book bookPersisted = fb.get();

	String bookId = bookPersisted.getId();
	assertThat(bookId, notNullValue());

	client.target(REST_SERVICE_URL).path("/{bookId}")
			.resolveTemplate("bookId", bookId).request().async().delete()
			.get();

	Future<List<Book>> bookRequest = client.target(REST_SERVICE_URL)
			.request().async().get(new GenericType<List<Book>>() {
			});
	List<Book> books2 = bookRequest.get();
	assertThat(books2.isEmpty(), equalTo(true));
}
```

## 发起回调

在客户端与服务器通信过程中，我们还有另一种方式可以对服务器的相应进行修改，那就是在请求中加入一个 InvocationCallback 回调处理。

可以看到，下面代码段中有着很多缩进那部分就是我们的回调函数了，这些回调可以打印保存成功的图书的完整信息，或者在出现错误的情况下则打印错误和堆栈信息。

```java
@Test
public void invocationCallbackExample() throws Exception {
	Book book = mockBook();
	client.target(REST_SERVICE_URL)
			.request()
			.async()
			.post(Entity.entity(book, MediaType.APPLICATION_JSON),
					new InvocationCallback<Book>() {
						@Override
						public void completed(final Book bookPersisted) {
							System.out.println("book saved: "
									+ bookPersisted);
							assertThat(bookPersisted.getId(),
									notNullValue());
						}

						@Override
						public void failed(final Throwable throwable) {
							throwable.printStackTrace();
						}
					}).get();

	client.target(REST_SERVICE_URL).request().async()
			.get(new InvocationCallback<List<Book>>() {
				@Override
				public void completed(final List<Book> books) {
					System.out.println(books.size() + " books received");
					assertThat(books.size(), greaterThanOrEqualTo(1));
				}

				@Override
				public void failed(final Throwable throwable) {
					throwable.printStackTrace();
				}
			}).get();
}
```

## 延迟调用 / 请求构建

通过 *javax.ws.rs.client.Invocation* 类，我们可以先构建一个请求而不用即时发送。这个请求可以是同步的, 也可以是异步的。

在下面的示例中，我们构建了两个调用但并不马上使用—— 一个请求用来保存图书，另一个请求则是获取所有可用的图书。然后，我们在后面调用时才使用这两个构建好的请求。

我们应当使用* invoke() *方法来同步地调用一个请求。当需要使用异步请求时，则需要用* submit() *方法——两种调用都会返回一个* javax.ws.rs.core.Response *对象。如果调用者在调用参数中给定了返回实体的类，则上述方法会返回该类。

```java
@Test
public void requestPreparationExample() throws Exception {
	Book book = mockBook();
	Invocation saveBook = client.target(REST_SERVICE_URL).request()
			.buildPost(Entity.entity(book, MediaType.APPLICATION_JSON));
	Invocation listBooks = client.target(REST_SERVICE_URL).request()
			.buildGet();

	Response response = saveBook.invoke();
	Book b1 = response.readEntity(Book.class);

	// alternative: Book b1 = saveBook.invoke(Book.class);
	assertThat(b1.getId(), notNullValue());

	// async invocation
	Future<List<Book>> b = listBooks.submit(new GenericType<List<Book>>() {
	});
	List<Book> books = b.get();
	assertThat(books.size(), greaterThanOrEqualTo(2));
}
```

## 客户端请求过滤器

JAX-RS 允许我们使用请求过滤器来截获客户端发送到服务器的请求。

为了达成这个目标，只需要实现* javax.ws.rs.client.ClientRequestFilter *这个接口。当创建客户端时，使用客户端的* register() *方法将* ClientRequestFilter *的具体实现注册到客户端中。

*javax.ws.rs.client.ClientRequestContext *对象将赋予访问信息请求足够的权限。

下面就是一个客户端请求过滤的例子。这个例子中，所有客户端发出的 POST 请求中如果包含书籍实体，则书籍价格都会被这个过滤器修改（虽然这不是一个好的实际示例）。对价格的修改则依据相应的税率。

```java
package com.hascode.tutorial.client;

import java.io.IOException;
import java.math.BigDecimal;

import javax.ws.rs.client.ClientRequestContext;
import javax.ws.rs.client.ClientRequestFilter;

import com.hascode.tutorial.jaxrs.entity.Book;

public class TaxAdjustmentFilter implements ClientRequestFilter {
	public static final BigDecimal TAX_RATE = new BigDecimal("2.5");

	@Override
	public void filter(final ClientRequestContext rc) throws IOException {
		String method = rc.getMethod();
		if ("POST".equals(method) && rc.hasEntity()) {
			Book book = (Book) rc.getEntity();
			BigDecimal priceWithTaxes = book.getPrice().multiply(TAX_RATE);
			book.setPrice(priceWithTaxes);
			rc.setEntity(book);
		}
	}

}
```

在我们的测试用例中，只要把这个过滤器注册到客户端上，随后就会看到：保存书籍时候，书本的价格就会根据税率进行的调整。

```java
@Test
public void clientRequestFilterExample() {
	Book book = mockBook();

	Client client = ClientBuilder.newClient()
			.register(JacksonFeature.class)
			.register(TaxAdjustmentFilter.class);
	Book bookPersisted = client
			.target(REST_SERVICE_URL)
			.request()
			.post(Entity.entity(book, MediaType.APPLICATION_JSON),
					Book.class);

	String bookId = bookPersisted.getId();
	assertThat(bookId, notNullValue());
	assertThat(bookPersisted.getPrice(),
			equalTo(PRICE.multiply(TaxAdjustmentFilter.TAX_RATE)));

}
```

## 客户端响应过滤器

为了获得对服务器相应的控制，有一个十分类似的办法：客户端相应过滤器。

同样地，只要实现* javax.ws.rs.client.ClientResponseFilter *这个接口，就能够修改或者截获服务器返回的响应。

下面这个响应过滤器能够将一些 HTTP 响应头打印到标准输出（STDOUT）：

```java
package com.hascode.tutorial.client;

import java.io.IOException;
import java.util.List;
import java.util.Map.Entry;

import javax.ws.rs.client.ClientRequestContext;
import javax.ws.rs.client.ClientResponseContext;
import javax.ws.rs.client.ClientResponseFilter;

public class ClientResponseLoggingFilter implements ClientResponseFilter {

	@Override
	public void filter(final ClientRequestContext reqCtx,
			final ClientResponseContext resCtx) throws IOException {
		System.out.println("status: " + resCtx.getStatus());
		System.out.println("date: " + resCtx.getDate());
		System.out.println("last-modified: " + resCtx.getLastModified());
		System.out.println("location: " + resCtx.getLocation());
		System.out.println("headers:");
		for (Entry<String, List<String>> header : resCtx.getHeaders()
				.entrySet()) {
			System.out.print("\t" + header.getKey() + " :");
			for (String value : header.getValue()) {
				System.out.print(value + ", ");
			}
			System.out.print("\n");
		}
		System.out.println("media-type: " + resCtx.getMediaType().getType());
	}

}
```

要使用这个过滤器，只需要把它注册到我们的客户端程序中：

```java
@Test
public void clientResponseFilterExample() {
	Book book = mockBook();

	Client client = ClientBuilder.newClient()
			.register(JacksonFeature.class)
			.register(ClientResponseLoggingFilter.class);
	client.target(REST_SERVICE_URL)
			.request()
			.post(Entity.entity(book, MediaType.APPLICATION_JSON),
					Book.class);
}
```

使用内嵌的 GlassFish 服务，POST 请求将有如下结果：

```xml
status: 200
date: Sat Dec 28 18:50:16 CET 2013
last-modified: null
location: null
headers:
 Date :Sat, 28 Dec 2013 17:50:16 GMT,
 Transfer-Encoding :chunked,
 Content-Type :application/json,
 Server :GlassFish Server Open Source Edition 3.1,
 X-Powered-By :Servlet/3.0 JSP/2.2 (GlassFish Server Open Source Edition 3.1 Java/Oracle Corporation/1.7),
media-type: application
```

*译注：GlassFish是SUN所研发的开放源代码应用服务器，GlassFish以Java编写以增加跨平台性[wikipedia]。*

# 教程源码

欢迎下载本教程中的源码，你可以用 Git 来 Fork 或者直接 Clone：[Bitbucket代码仓库](https://bitbucket.org/hascode/jaxrs2-client-tutorial)。

# 下载 war-File REST 服务器

你可以从这里下载 war-file 然后运行自己的 RESTful 服务：<https://bitbucket.org/hascode/jaxrs2-client-tutorial/downloads>

# JAX-RS 1.0 and JAX-B

如果你对旧版本的协议感兴趣，[这篇文章](http://www.hascode.com/2010/11/creating-a-rest-client-step-by-step-using-jax-rs-jax-b-and-jersey/)正是你需要的。

 翻译： ImportNew.com  - 靳禹

译文链接： http://www.importnew.com/8939.html

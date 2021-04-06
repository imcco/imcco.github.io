---
title: Spring Batch 4.0.0批处理框架
tags:
  - Spring Batch
category: Spring
abbrlink: 31932
date: 2017-12-04 23:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0021.jpg)

Spring Batch 4.0.0  已发布，Spring Batch 是一个轻量级的，完全面向 Spring 的批处理框架，可以应用于企业级大量的数据处理系统。Spring Batch 以 POJO 和  Spring 框架为基础，使开发者更容易的访问和利用企业级服务。Spring Batch 可以提供大量的，可重复的数据处理功能，包括日志记录/跟踪，事务管理，作业处理统计工作重新启动、跳过，和资源管理等重要功能。
<!--more-->
Spring Batch 4.0 是自2014年 3.0 发布以来的又一个主要版本。3.0 发布至今，Spring 生态系统发生了很大变化，4.0 版本旨在让 Spring Batch 更新这些变化。

New Baseline

Spring Batch 3 基于 Spring Framework 4 ，Spring Batch 4 重新审视了它的依赖关系树，将它与即将到来的 Spring Boot 2 依赖关系树进行了更新，包括将 Spring Framework 5 和 Java 8 作为基线要求。

### 改进的 Java 配置

如前所述，Spring Batch 4 是 Spring Boot 发布以来的第一个主要版本。在这个版本中，改善了让用户的 Java 配置体验。所有 ItemReader 和 ItemWriter 实现现在都可以使用构建器。使用 Spring Batch 3 的 Java 配置功能，将需要如下所示：

```java
@Bean
public FlatFileItemReader<Foo> reader(Resource resource) 
    throws Exception {

        FlatFileItemReader<Foo> reader = new FlatFileItemReader<>();

        reader.setName(“fooReader”);
        reader.setResource(resource);

        BeanWrapperFieldSetMapper<Foo> fieldSetMapper = 
            new BeanWrapperFieldSetMapper<>();
        fieldSetMapper.setTargetType(Foo.class);
        fieldSetMapper.afterPropertiesSet();

        DelimitedLineTokenizer tokenizer = new DelimitedLineTokenizer();
        tokenizer.setNames(new String[] {“first”, “second”, “third”});
        tokenizer.afterPropertiesSet();

        DefaultLineMapper lineMapper = new DefaultLineMapper();
        lineMapper.setLineTokenizer(tokenizer);
        lineMapper.setFieldSetMapper(fieldSetMapper);

        reader.setLineMapper(lineMapper);

        return reader;
    }
```
使用 Spring Batch 4，配置简化如下：

```java
@Bean
public FlatFileItemReader<Foo> reader(Resource resource) {
        return new FlatFileItemReaderBuilder<Foo>()
            .name(“fooReader”)
            .resource(resource)
            .delimited()
            .names(new String[]{“first”, “second”, “third”})
            .targetType(Foo.class)
            .build();
}
```

## 开始
### 创建批处理服务
    本指南将引导您完成创建基本批处理驱动解决方案的过程。

### 你要建什么
    您将构建一个服务，该服务从CSV电子表格中导入数据，用自定义代码对其进行转换，并将最终结果存储在数据库中。

### 你需要什么
    大约15分钟

### 最喜欢的文本编辑器或IDE

    JDK 1.8或以后

    Gradle 2.3或Maven 3.0+

    您还可以直接将代码导入IDE中：

    Spring Tool Suite (STS)

    IntelliJ IDEA

### 如何完成本指南
    像大多数Spring一样入门指南，您可以从头开始并完成每个步骤，也可以绕过您已经熟悉的基本设置步骤。无论哪种方式，你最终都会得到工作代码。

到白手兴家，继续前进用Gradle建造...

到跳过基础，做以下工作：

下载并解压缩本指南的源存储库，或用Git:
```
git clone https://github.com/spring-guides/gs-batch-processing.git
```
cd into gs-batch-processing/initial

跳到创建一个business类...

当你完成中的代码检查结果，gs-batch-processing/complete.
### 用Gradle创建
首先，您设置了一个基本的构建脚本。在使用Spring构建应用程序时，您可以使用任何您喜欢的构建系统，但是您需要使用的代码用Gradle和Maven包括在这里。如果您对这两种情况都不熟悉，请参阅用Gradle构建Java项目或使用Maven构建Java项目...

#### 创建目录结构
在您选择的项目目录中，创建以下子目录结构；例如，使用mkdir-p src/main/java/hello关于*nix系统：


```
└── src
    └── main
        └── java
            └── hello
```
创建一个Gradle构建文件
下面是initial Gradle build file....

**build.gradle**


```java
buildscript{repositories{mavenCentral()
    }dependencies{classpath("org.springframework.boot:spring-boot-gradle-plugin:1.5.9.RELEASE")
    }
}apply plugin: 'java'apply plugin: 'eclipse'apply plugin: 'idea'apply plugin: 'org.springframework.boot'jar{baseName= 'gs-batch-processing'version=  '0.1.0'
}repositories{mavenCentral()
}sourceCompatibility= 1.8targetCompatibility= 1.8dependencies{compile("org.springframework.boot:spring-boot-starter-batch")compile("org.hsqldb:hsqldb")testCompile("junit:junit")
}
```
#### Spring Boot gradle plugin提供了许多方便的功能：

- 它收集类路径上的所有JAR，并构建一个可运行的“über-jar”，这使得执行和传输服务更加方便。
- 它搜索public static void main()方法标记为可运行的类。
- 它提供了一个内置的依赖项解析器，它将版本号设置为匹配。Spring Boot dependencies您可以覆盖任何版本，但它将默认为Boot所选的一组版本。

## 用Maven构建
首先，您设置了一个基本的构建脚本。在使用Spring构建应用程序时，您可以使用任何您喜欢的构建系统，但是您需要使用的代码马文包括在这里。如果您不熟悉Maven，请参阅使用Maven构建Java项目...

创建目录结构
在您选择的项目目录中，创建以下子目录结构；例如，使用mkdir-p src/main/java/hello关于*nix系统：


```
└── src
    └── main
        └── java
            └── hello
```
**pom.xml**

```xml
<?xml version="1.0"encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-batch-processing</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-batch</artifactId>
        </dependency>
        <dependency>
            <groupId>org.hsqldb</groupId>
            <artifactId>hsqldb</artifactId>
        </dependency>
    </dependencies>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```
#### 大Spring Boot Maven插件提供了许多方便的功能：

- 它收集类路径上的所有JAR，并构建一个可运行的“über-jar”，这使得执行和传输服务更加方便。

- 它搜索public static void main()方法标记为可运行的类。

- 它提供了一个内置的依赖项解析器，它将版本号设置为匹配。Spring Boot dependencies您可以覆盖任何版本，但它将默认为Boot所选的一组版本。
## 用IDE构建

阅读如何将本指南直接导入Spring Tool Suite...

阅读如何使用本指南IntelliJ IDEA...
### 业务数据
通常，您的客户或业务分析师提供电子表格。在这种情况下，你自己编出来的。

**SRC/Main/Resources/Sample-data.csv**

```
Jill,Doe
Joe,Doe
Justin,Doe
Jane,Doe
John,Doe
```
此电子表格包含每行的名称和姓氏，以逗号分隔。正如您所看到的，这是Spring处理的一个非常常见的模式。

接下来，编写一个SQL脚本来创建一个表来存储数据。

**SRC/main/resources/schema-all.sql**

```sql
DROP TABLE people IF EXISTS;CREATE TABLE people(person_id BIGINT IDENTITY NOT NULL PRIMARY KEY,first_name VARCHAR(20),last_name VARCHAR(20)
);
```
    Spring Boot模式schema-@@platform@@.sql启动时自动。-all是所有平台的默认设置。
### 创建一个business类
现在您已经看到了数据输入和输出的格式，您可以编写代码来表示一行数据.


```java
packagehello;

public class Person {
    private StringlastName;
    private StringfirstName;

    public Person() {

    }

    public Person(StringfirstName, StringlastName) {
        this.firstName=firstName;
        this.lastName=lastName;
    }

    public voidsetFirstName(StringfirstName) {
        this.firstName=firstName;
    }

    public StringgetFirstName() {
        returnfirstName;
    }

    public StringgetLastName() {
        returnlastName;
    }

    public voidsetLastName(StringlastName) {
        this.lastName=lastName;
    }

    @Override
    public StringtoString() {
        return "firstName: " +firstName+ ", lastName: " +lastName;
    }

}
```
您可以实例化person通过构造函数，或通过设置属性，使用姓和名初始化。

### 创建中间处理器（ intermediate processor）
批处理中的一个常见范例是摄取数据，转换数据，然后将其输送到其他地方。在这里，您将编写一个简单的转换器，将名称转换为大写。

**SRC/main/java/hello/PersonItemProcessor.java**

```java
packagehello;

importorg.slf4j.Logger;
importorg.slf4j.LoggerFactory;

importorg.springframework.batch.item.ItemProcessor;

public class PersonItemProcessor implements ItemProcessor<Person, Person> {

    private static final Loggerlog= LoggerFactory.getLogger(PersonItemProcessor.class);

    @Override
    public Personprocess(final Personperson) throws Exception {
        final StringfirstName=person.getFirstName().toUpperCase();
        final StringlastName=person.getLastName().toUpperCase();

        final PersontransformedPerson= new Person(firstName,lastName);log.info("Converting (" +person+ ") into (" +transformedPerson+ ")");

        returntransformedPerson;
    }

}
```
PersonItemProcessor器实现Spring批处理ItemProcessor接口。这使代码很容易连接到一个批处理作业中，您可以在本指南中进一步对其进行定义。根据接口，您将收到一个传入的person对象，然后将其转换upper-cased Person.

    不需要输入和输出类型相同。事实上，在读取一个数据源之后，有时应用程序的数据流需要不同的数据类型。

### 把一批工作放在一起
现在，您将实际的批处理作业组合在一起。SpringBatch提供了许多实用程序类，这些类减少了编写自定义代码的需要。相反，您可以专注于业务逻辑。

**SRC/main/java/hello/BatchConfiguration.java**


```java
packagehello;

importjavax.sql.DataSource;

importorg.springframework.batch.core.Job;
importorg.springframework.batch.core.JobExecutionListener;
importorg.springframework.batch.core.Step;
importorg.springframework.batch.core.configuration.annotation.EnableBatchProcessing;
importorg.springframework.batch.core.configuration.annotation.JobBuilderFactory;
importorg.springframework.batch.core.configuration.annotation.StepBuilderFactory;
importorg.springframework.batch.core.launch.support.RunIdIncrementer;
importorg.springframework.batch.item.database.BeanPropertyItemSqlParameterSourceProvider;
importorg.springframework.batch.item.database.JdbcBatchItemWriter;
importorg.springframework.batch.item.file.FlatFileItemReader;
importorg.springframework.batch.item.file.mapping.BeanWrapperFieldSetMapper;
importorg.springframework.batch.item.file.mapping.DefaultLineMapper;
importorg.springframework.batch.item.file.transform.DelimitedLineTokenizer;
importorg.springframework.beans.factory.annotation.Autowired;
importorg.springframework.context.annotation.Bean;
importorg.springframework.context.annotation.Configuration;
importorg.springframework.core.io.ClassPathResource;
importorg.springframework.jdbc.core.JdbcTemplate;

@Configuration
@EnableBatchProcessing
public class BatchConfiguration {

    @Autowired
    public JobBuilderFactoryjobBuilderFactory;

    @Autowired
    public StepBuilderFactorystepBuilderFactory;

    @Autowired
    public DataSourcedataSource;

    // tag::readerwriterprocessor[]
    @Bean
    public FlatFileItemReader<Person>reader() {
        FlatFileItemReader<Person>reader= new FlatFileItemReader<Person>();reader.setResource(new ClassPathResource("sample-data.csv"));reader.setLineMapper(new DefaultLineMapper<Person>() {{setLineTokenizer(new DelimitedLineTokenizer() {{setNames(new String[] { "firstName", "lastName" });
            }});setFieldSetMapper(new BeanWrapperFieldSetMapper<Person>() {{setTargetType(Person.class);
            }});
        }});
        returnreader;
    }

    @Bean
    public PersonItemProcessorprocessor() {
        return new PersonItemProcessor();
    }

    @Bean
    public JdbcBatchItemWriter<Person>writer() {
        JdbcBatchItemWriter<Person>writer= new JdbcBatchItemWriter<Person>();writer.setItemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<Person>());writer.setSql("INSERT INTO people (first_name, last_name) VALUES (:firstName, :lastName)");writer.setDataSource(dataSource);
        returnwriter;
    }
    // end::readerwriterprocessor[]

    // tag::jobstep[]
    @Bean
    public JobimportUserJob(JobCompletionNotificationListenerlistener) {
        returnjobBuilderFactory.get("importUserJob")
                .incrementer(new RunIdIncrementer())
                .listener(listener)
                .flow(step1())
                .end()
                .build();
    }

    @Bean
    public Stepstep1() {
        returnstepBuilderFactory.get("step1")
                .<Person, Person>chunk(10)
                .reader(reader())
                .processor(processor())
                .writer(writer())
                .build();
    }
    // end::jobstep[]
}
```
  首先，@EnableBatchProcessing注释添加了许多支持作业的关键bean，并为您节省了大量的腿工作。此示例使用基于内存的数据库(由@EnableBatchProcessing)，这意味着当它完成时，数据就会消失。

把它分解：

**SRC/main/java/hello/BatchConfiguration.java**


```java
    @Bean
    public FlatFileItemReader<Person>reader() {
        FlatFileItemReader<Person>reader= new FlatFileItemReader<Person>();reader.setResource(new ClassPathResource("sample-data.csv"));reader.setLineMapper(new DefaultLineMapper<Person>() {{setLineTokenizer(new DelimitedLineTokenizer() {{setNames(new String[] { "firstName", "lastName" });
            }});setFieldSetMapper(new BeanWrapperFieldSetMapper<Person>() {{setTargetType(Person.class);
            }});
        }});
        returnreader;
    }

    @Bean
    public PersonItemProcessorprocessor() {
        return new PersonItemProcessor();
    }

    @Bean
    public JdbcBatchItemWriter<Person>writer() {
        JdbcBatchItemWriter<Person>writer= new JdbcBatchItemWriter<Person>();writer.setItemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<Person>());writer.setSql("INSERT INTO people (first_name, last_name) VALUES (:firstName, :lastName)");writer.setDataSource(dataSource);
        returnwriter;
    }
```
第一个代码块定义输入、处理器和输出。---reader()创建ItemReader.它查找一个名为sample-data.csv并使用足够的信息解析每一行项，从而将其转换为person...processor()创建我们的PersonItemProcessor您在前面定义，目的是大写数据。---write(DataSource)创建ItemWriter。此对象针对JDBC目标，并自动获取由@EnableBatchProcessing它包括插入单个person由Javabean属性驱动。

下一部分重点关注实际的作业配置。
**SRC/main/java/hello/BatchConfiguration.java**

```java
    @Bean
    public JobimportUserJob(JobCompletionNotificationListenerlistener) {
        returnjobBuilderFactory.get("importUserJob")
                .incrementer(new RunIdIncrementer())
                .listener(listener)
                .flow(step1())
                .end()
                .build();
    }

    @Bean
    public Stepstep1() {
        returnstepBuilderFactory.get("step1")
                .<Person, Person>chunk(10)
                .reader(reader())
                .processor(processor())
                .writer(writer())
                .build();
    }
```

第一个方法定义作业，三定义一个步骤。作业是由步骤构建的，每个步骤都可以包含一个阅读器、一个处理器和一个编写器。

在此作业定义中，您需要一个增量器，因为作业使用数据库来维护执行状态。然后列出每个步骤，其中此作业只有一个步骤。作业结束，JavaAPI生成一个配置完美的作业。

在步骤定义中，定义一次写入多少数据。在这种情况下，它一次最多写十条记录。接下来，使用前面注入的位来配置读取器、处理器和写入器。

    chunk()是前缀<Person,Person>因为这是一个通用的方法。这表示处理的每个“块”的输入和输出类型，并与ItemReader和ItemWriter...

**SRC/main/java/hello/JobCompletionNotificationListener.java**


```java
packagehello;

importjava.sql.ResultSet;
importjava.sql.SQLException;
importjava.util.List;

importorg.slf4j.Logger;
importorg.slf4j.LoggerFactory;

importorg.springframework.batch.core.BatchStatus;
importorg.springframework.batch.core.JobExecution;
importorg.springframework.batch.core.listener.JobExecutionListenerSupport;
importorg.springframework.beans.factory.annotation.Autowired;
importorg.springframework.jdbc.core.JdbcTemplate;
importorg.springframework.jdbc.core.RowMapper;
importorg.springframework.stereotype.Component;

@Component
public class JobCompletionNotificationListener extends JobExecutionListenerSupport {

	private static final Loggerlog= LoggerFactory.getLogger(JobCompletionNotificationListener.class);

	private final JdbcTemplatejdbcTemplate;

	@Autowired
	public JobCompletionNotificationListener(JdbcTemplatejdbcTemplate) {
		this.jdbcTemplate=jdbcTemplate;
	}

	@Override
	public voidafterJob(JobExecutionjobExecution) {
		if(jobExecution.getStatus() == BatchStatus.COMPLETED) {log.info("!!! JOB FINISHED! Time to verify the results");

			List<Person>results=jdbcTemplate.query("SELECT first_name, last_name FROM people", new RowMapper<Person>() {
				@Override
				public PersonmapRow(ResultSetrs, introw) throws SQLException {
					return new Person(rs.getString(1),rs.getString(2));
				}
			});

			for (Personperson:results) {log.info("Found <" +person+ "> in the database.");
			}

		}
	}
}
```
此代码监听作业为BatchStatus.COMPLETED，然后使用JdbcTemplate检查结果。

### 使应用程序可执行
虽然批处理可以嵌入到Web应用程序和WAR文件中，但下面演示的更简单的方法创建了一个独立的应用程序。您将所有东西打包到一个可执行的JAR文件中，该文件由一个好的旧Java驱动。main()方法。

**src/main/java/hello/Application.java**


```java
packagehello;

importorg.springframework.boot.SpringApplication;
importorg.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static voidmain(String[]args) throws Exception {
        SpringApplication.run(Application.class,args);
    }
}
```

@SpringBootApplication是一个方便的注释，它添加了以下所有内容：

- @Configuration将类标记为应用程序上下文的bean定义的源。
- @EnableAutoConfiguration告诉SpringBoot根据类路径设置、其他bean和各种属性设置开始添加bean。
- 通常你会添加@EnableWebMvc对于SpringMVC应用程序，但是SpringBoot在看到Springwebmvc在类路径上。这会将应用程序标记为web应用程序，并激活关键行为，例如设置DispatcherServlet...
- @ComponentScan告诉Spring在hello包，让它找到控制器。

main()方法使用SpringBoot的SpringApplication.run()方法来启动应用程序。您注意到没有一行XML吗？不web.xml也要归档。这个Web应用程序是100%纯Java，您不必处理配置任何管道或基础设施的问题。

出于演示目的，有一些代码可以创建一个JdbcTemplate，查询数据库，并打印批处理作业插入的人员的名称。

构建一个可执行的JAR
您可以使用Gradle或Maven从命令行运行应用程序。或者，您可以构建一个包含所有必需的依赖项、类和资源的单个可执行JAR文件，并运行该文件。这使得在整个开发生命周期、跨不同环境等将服务作为应用程序进行发布、版本和部署变得非常容易。

##### 如果使用Gradle
则可以使用./gradlew bootRun.或者您可以使用以下方法构建JAR文件./gradlew build然后可以运行JAR文件：


```
java -jar build/libs/gs-batch-processing-0.1.0.jar
```
##### 如果您使用的是Maven
则可以使用./mvnw Spring-boot：运行.或者您可以用./mvnw clean package然后可以运行JAR文件：

```
java -jar target/gs-batch-processing-0.1.0.jar
```
    上面的过程将创建一个可运行的JAR。你也可以选择构建一个经典的WAR文件相反。

该职务为每个被转换的人打印一行。作业运行后，还可以看到查询数据库的输出。

```java
Converting (firstName: Jill, lastName: Doe) into (firstName: JILL, lastName: DOE)
Converting (firstName: Joe, lastName: Doe) into (firstName: JOE, lastName: DOE)
Converting (firstName: Justin, lastName: Doe) into (firstName: JUSTIN, lastName: DOE)
Converting (firstName: Jane, lastName: Doe) into (firstName: JANE, lastName: DOE)
Converting (firstName: John, lastName: Doe) into (firstName: JOHN, lastName: DOE)
Found  in the database.
Found  in the database.
Found  in the database.
Found  in the database.
Found  in the database.
```
### 摘要
恭喜你！您构建了一个批处理作业，该作业从电子表格中摄取数据，并对其进行处理，并将其写入数据库。

See also
- The following guides may also be helpful:

- Building an Application with Spring Boot

- Accessing Data with GemFire

- Accessing Data with JPA

- Accessing Data with MongoDB

- Accessing data with MySQL

---
title: "🚀 Java 返回 Xml 格式数据"
description: ""
lead: ""
summary: ""
date: 2023-09-16T13:45:39+08:00
lastmod: 2023-09-16T13:45:39+08:00
draft: false
weight: 99
images: []
categories: []
tags: []
contributors: ["QiaoSe FenNv"]
pinned: false
homepage: false
---




公司最近要求对接，返回响应格式为`Xml`类型的数据。我们最近经常接触的请求就是`Json`格式的数据，它体量小、传输速度快、支持的工具多种多样。而`Xml`已经是很久之类的格式了。不过，`xml`格式也需要了解一下，后面也可能出现更多的新的格式，多了解一些就自己的进度也就越快。那么，长话短说，开始我们今天的学习之旅吧。😀



## 常见的XML处理方式以及相关的库

   - **DOM (Document Object Model)**：
     - Java内置支持的XML处理方式之一。
     - 使用DOM，XML文档被解析为一个树形结构，可以通过操作节点来访问和修改XML内容。
     - Java标准库提供了`javax.xml.parsers.DocumentBuilder`用于创建DOM树。
   - **SAX (Simple API for XML)**：
     - 另一种Java内置的XML处理方式。
     - SAX是一种事件驱动的处理方式，逐行解析XML文档并触发事件处理程序。
     - 使用SAX时，不需要构建整个XML树，适用于大型XML文档或需要一次处理一部分的情况。
     - Java标准库提供了`org.xml.sax.XMLReader`用于SAX处理。
   - **JAXB (Java Architecture for XML Binding)**：
     - JAXB是Java的一个标准，用于将Java对象与XML之间进行相互转换。
     - 使用JAXB，您可以通过注解标记Java类，然后将其自动序列化为XML，或者将XML反序列化为Java对象。
     - JAXB提供了`javax.xml.bind`包，可以用于创建JAXB上下文并进行数据绑定。
   - **JDOM**：
     - JDOM是一个用于处理XML的开源Java库，它提供了更友好和易用的API。
     - JDOM允许您以更直观的方式操作XML文档，而不需要处理底层的DOM或SAX事件。
     - 您可以使用JDOM来创建、解析和操作XML文档。
   - **DOM4J**：
     - DOM4J是另一个流行的用于处理XML的开源Java库。
     - 它提供了一种灵活而高性能的方式来操作XML文档。
     - DOM4J支持XPath查询、XML写入和许多其他功能。
   - **StAX (Streaming API for XML)**：
     - StAX是一种基于流的XML处理方式，允许您按顺序读取或写入XML数据。
     - StAX提供了`javax.xml.stream`包，包括`XMLStreamReader`和`XMLStreamWriter`用于处理XML流。



总类很多，我们就选择最经常使用的**JAXB (Java Architecture for XML Binding)**。



## 实战

Springboot项目接口请求返回Xml格式响应

### 引入POM文件

```pom
        <!-- JAXB API -->
        <dependency>
            <groupId>javax.xml.bind</groupId>
            <artifactId>jaxb-api</artifactId>
            <version>2.3.1</version>
        </dependency>

        <!-- JAXB Implementation (Reference Implementation) -->
        <dependency>
            <groupId>org.glassfish.jaxb</groupId>
            <artifactId>jaxb-runtime</artifactId>
            <version>2.3.1</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
        </dependency>

```



### 编写实体类

```java
@XmlRootElement
@Entity
@Table(name = "employee")
public class Employee {
    @Id
    @GeneratedValue(generator = "uuid2")
    @GenericGenerator(name = "uuid2", strategy = "org.hibernate.id.UUIDGenerator") //uuid自增
    @Column(name = "id", columnDefinition = "BINARY(16)" ,nullable = false)
    private String id;

    @XmlElement
    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    @XmlElement
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    @XmlElement
    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
    @XmlElement
    public Gen getGen() {
        return gen;
    }

    public void setGen(Gen gen) {
        this.gen = gen;
    }

    @Column(name = "name")
    private String name;

    @Column(name = "age")
    private int age;

    @Enumerated(EnumType.STRING)
    @Column(name = "gen")
    private Gen gen;



}
```

>注意：   @XmlElement 标签 会与 lombok 的@Data 和 @Setter 、@Getter 注解发生冲突，大致原因就是@Data产生的注解会使@JAXB认为产生重复的名称。

```java
@XmlRootElement
@XmlAccessorType(XmlAccessType.FIELD)
public class EmployeeList {

    @XmlElementWrapper(name = "employees")
    @XmlElement(name = "employee")
    private List<Employee> employees;

    // Getter and setter for employees list

    public EmployeeList() {
        this.employees = new ArrayList<>();
    }

    public EmployeeList(List<Employee> employees) {
        this.employees = employees;
    }

    public List<Employee> getEmployees() {
        return employees;
    }

    public void setEmployees(List<Employee> employees) {
        this.employees = employees;
    }
}
```

>注：如果想返回list集合类型的数据，则需要使用创建一个专门转换list的对象。不然，jaxb 无法转换具有超类的对象。并且，需要为其对象添加无参构造函数，不然也会出现错误。

### 配置Xml转换器

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        // 添加XML消息转换器
        MappingJackson2XmlHttpMessageConverter xmlConverter = new MappingJackson2XmlHttpMessageConverter();
        // 配置XmlMapper
        XmlMapper xmlMapper = new XmlMapper();
        xmlConverter.setObjectMapper(xmlMapper);
        converters.add(xmlConverter);

        // 添加JSON消息转换器（可选）
        MappingJackson2HttpMessageConverter jsonConverter = new MappingJackson2HttpMessageConverter();
        converters.add(jsonConverter);
    }
}
```

>通过，该转换器就可以实现自定义，返回类型了。

### 编写接口

#### 返回单个xml对象

```java
    @GetMapping(value = "/findPojo", produces = {"application/xml;charset=UTF-8"}) //通过上面的Xml转换器，我们只需要再次添加返回类型即可，就不需要手动设置header为xml格式的了
    @ApiOperation("查询数据")
    public Result<?> findPojoToXml() {
        TypedQuery<Employee> query = entityManager.createQuery(
                "SELECT * FROM Employee e WHERE e.age = :age", Employee.class);

        query.setParameter("age", 18);
        List<Employee> resultList = query.getResultList();

        entityManager.close();

        EmployeeList employeeList = new EmployeeList(resultList);


        JAXBContext context = null;
        String xmlString = "";
        try {

            context = JAXBContext.newInstance(EmployeeList.class);
            // 使用Marshaller将对象转换为XML字符串
            Marshaller marshaller = context.createMarshaller();


            StringWriter writer = new StringWriter();
            marshaller.marshal(employeeList, writer);
            // 打印XML字符串
            xmlString = writer.toString();
            log.info(xmlString);


        } catch (JAXBException e) {
            throw new RuntimeException(e);
        }

        //如果没有添加转换器，那么就需要手动设置，并且无法使用自己定义的返回
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_XML);
        return new ResponseEntity<>(result, headers, HttpStatus.OK);

        //return Result.success(xmlString);

    }
```




#### 返回多个list集合对象

```java
    @GetMapping(value = "/findPojo", produces = {"application/xml;charset=UTF-8"})
    @ApiOperation("查询数据")
    public Result<?> findPojoToXml() {
        TypedQuery<Employee> query = entityManager.createQuery(
                "SELECT e FROM Employee e WHERE e.age = :age", Employee.class);

        query.setParameter("age", 18);
        List<Employee> resultList = query.getResultList();

        entityManager.close();

        EmployeeList employeeList = new EmployeeList(resultList);


        JAXBContext context = null;
        String xmlString = "";
        try {

            context = JAXBContext.newInstance(EmployeeList.class);
            // 使用Marshaller将对象转换为XML字符串
            Marshaller marshaller = context.createMarshaller();


            StringWriter writer = new StringWriter();
            marshaller.marshal(employeeList, writer);
            // 打印XML字符串
            xmlString = writer.toString();
            log.info(xmlString);


        } catch (JAXBException e) {
            throw new RuntimeException(e);
        }

        return Result.success(xmlString);

    }
```




#### 添加namespace

```java

@XmlAccessorType(XmlAccessType.FIELD)
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Data
@Table(name="service_resource_platform")
@ToString
@ApiModel( description = "资源平台对象" )
public class Service {


    private String serviceContent;

    private String domains;

    public String getDomains() {
        return domains;
    }

    public void setDomains(String domains) {
        this.domains = domains;
    }

    public String getServiceContent() {
        return serviceContent;
    }

    public void setServiceContent(String serviceContent) {
        this.serviceContent = serviceContent;
    }
}

```

>还是来样子，这边有一个更好的注解：
>
>@XmlAccessorType(XmlAccessType.FIELD)
>
>意味着，他会将根据字段的名称来创建对于xml标签。不过，这个注解如果直接使用确实会方便很多。但是，有的时候就无法进行自动化处理

```java
@XmlRootElement
@XmlAccessorType(XmlAccessType.FIELD)
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class UserInfo {

    @XmlAttribute(name = "xmlns:xsi")
    private String xsiNamespace = "http://www.w3.org/2001/XMLSchema-instance";
    private String ircsId;


    private List<Service> service;


    public String getIrcsId() {
        return ircsId;
    }

    public void setIrcsId(String ircsId) {
        this.ircsId = ircsId;
    }

    public List<Service> getService() {
        return service;
    }

    public void setService(List<Service> service) {
        this.service = service;
    }
}

```

```xml
<userInfo
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <ircsId>78946</ircsId>
    <service>
        <serviceContent>1</serviceContent>
        <domains>baidu.com;www.788.com</domains>
    </service>
    <service>
        <serviceContent>1</serviceContent>
        <domains>baidu.com;www.788.com</domains>
    </service>
</userInfo>

```

>通过，以上两个对象使用的注解，组合就可以返回这样结构的xml。
>
>以上内容，应该包括了最常见的使用方法了。具体的变更，使用下面提到的注解应该都有办法实现。

## Java Xml

```java
@GetMapping("/findXml")
    @ApiOperation("查询数据")
    public ResponseEntity<?> findXml() {
        // 创建一个XML文档
        DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
        DocumentBuilder dBuilder = null;
        try {
            dBuilder = dbFactory.newDocumentBuilder();
        } catch (ParserConfigurationException e) {
            throw new RuntimeException(e);
        }
        Document doc = dBuilder.newDocument();

        // 创建根元素和子元素
        Element rootElement = doc.createElement("data");
        doc.appendChild(rootElement);

        Element nameElement = doc.createElement("name");
        nameElement.appendChild(doc.createTextNode("John"));
        rootElement.appendChild(nameElement);

        Element ageElement = doc.createElement("age");
        ageElement.appendChild(doc.createTextNode("30"));
        rootElement.appendChild(ageElement);

        // 将XML文档转换为字符串
        TransformerFactory transformerFactory = TransformerFactory.newInstance();
        Transformer transformer = null;
        try {
            transformer = transformerFactory.newTransformer();
        } catch (TransformerConfigurationException e) {
            throw new RuntimeException(e);
        }
        transformer.setOutputProperty(OutputKeys.INDENT, "yes");
        DOMSource source = new DOMSource(doc);
        StringWriter writer = new StringWriter();
        StreamResult result = new StreamResult(writer);
        try {
            transformer.transform(source, result);
        } catch (TransformerException e) {
            throw new RuntimeException(e);
        }
        String xmlData = writer.toString();
        log.info("xmlData:{}", xmlData);
        // 设置HTTP响应标头为XML
        return ResponseEntity.status(HttpStatus.OK)
                .header("Content-Type", "application/xml")
                .body(xmlData);

//        return Result.success(xmlData);

    }
```



## JAXB

当您需要在Java中进行XML和Java对象之间的转换时，JAXB（Java Architecture for XML Binding）是一个非常有用的工具。JAXB提供了一种将Java类映射到XML表示（序列化）以及将XML表示映射回Java对象（反序列化）的机制。

常用的JAXB注解包括：

- `@XmlRootElement`：用于标记根XML元素。
- `@XmlElement`：用于标记Java字段或属性，指示将其映射为XML元素。 --- 强调，这个注解作用在属性上时，会把属性变为一个元素！！！ 这两个出现的xml样式，差别巨大
- `@XmlAttribute`：用于标记Java字段或属性，指示将其映射为XML属性。--- 强调，这个注解作用在属性上时，会把属性变为一个属性！！！ 这两个出现的xml样式，差别巨大
- `@XmlType`：用于定义类型信息。
- `@XmlAccessorType`：用于指定如何访问Java类的字段或属性。



**创建JAXB上下文**：要使用JAXB，您需要创建一个JAXB上下文，它负责处理XML和Java对象之间的映射。通常，您会使用`javax.xml.bind.JAXBContext`类来创建上下文。例如：

```java
JAXBContext context = JAXBContext.newInstance(Employee.class);
```

**序列化**：将Java对象转换为XML表示是JAXB的序列化过程。要执行序列化，您可以创建一个`Marshaller`实例，然后使用它将Java对象转换为XML。例如：

```java
Employee employee = new Employee("12345", "John Doe", 30);
Marshaller marshaller = context.createMarshaller();
marshaller.marshal(employee, System.out); // 输出XML表示
```

**反序列化**：将XML表示转换回Java对象是JAXB的反序列化过程。要执行反序列化，您可以创建一个`Unmarshaller`实例，然后使用它将XML转换为Java对象。例如：

```java
Unmarshaller unmarshaller = context.createUnmarshaller();
Employee employee = (Employee) unmarshaller.unmarshal(new File("employee.xml"));
```

**JAXB上下文缓存**：JAXB上下文创建是一个开销较高的操作，因此通常建议将JAXB上下文缓存起来，以便在整个应用程序中重复使用。

```java
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBException;
import java.util.HashMap;
import java.util.Map;

public class JAXBContextCache {
    private static final Map<Class<?>, JAXBContext> contextCache = new HashMap<>();

    public static JAXBContext getContext(Class<?> clazz) throws JAXBException {
        JAXBContext context = contextCache.get(clazz);

        if (context == null) {
            context = JAXBContext.newInstance(clazz);
            contextCache.put(clazz, context);
        }

        return context;
    }

    public static void main(String[] args) throws JAXBException {
        // 示例使用
        Class<Employee> employeeClass = Employee.class;

        // 从缓存获取JAXB上下文
        JAXBContext context = JAXBContextCache.getContext(employeeClass);

        // 使用上下文进行序列化或反序列化操作
        // ...
    }
}

```

**处理集合**：JAXB还支持处理集合，例如`List`或`Set`，可以将多个对象序列化为XML元素的列表，或者将XML元素列表反序列化为Java集合。



**自定义映射**：如果需要更复杂的映射，可以使用JAXB提供的自定义绑定选项，或者使用外部绑定文件（例如XML绑定语言文件）来自定义映射规则。

我们有以下XML文档表示一个Book对象

```xml

<book>
    <title>Java Programming</title>
    <author>John Doe</author>
</book>
```

 我们的Book类如下

```java
@XmlRootElement
public class Book {
    private String title;
    private String author;

    // 省略构造函数和访问器方法
}
```

假设我们想要自定义映射规则，以将`<title>`元素映射到`title`属性，将`<author>`元素映射到`author`属性之外，还要将`<publicationYear>`元素映射到`year`属性。我们可以使用XML绑定语言文件来实现这个自定义映射：

```java
<?xml version="1.0" encoding="UTF-8"?>
<jaxb:bindings xmlns:jaxb="http://java.sun.com/xml/ns/jaxb"
               xmlns:xsd="http://www.w3.org/2001/XMLSchema"
               xmlns:xjc="http://java.sun.com/xml/ns/jaxb/xjc"
               xmlns:annox="http://annox.dev.java.net"
               xmlns:namespace="http://jaxb2-commons.dev.java.net/namespace-prefix"
               xmlns:inheritance="http://jaxb2-commons.dev.java.net/basic/inheritance"
               jaxb:extensionBindingPrefixes="xjc annox namespace inheritance"
               version="2.1">

    <jaxb:bindings schemaLocation="your-schema.xsd">
        <jaxb:bindings node="/xs:schema/xs:element[@name='book']">
            <jaxb:bindings node="xs:element[@name='title']">
                <jaxb:property name="title" />
            </jaxb:bindings>
            <jaxb:bindings node="xs:element[@name='author']">
                <jaxb:property name="author" />
            </jaxb:bindings>
            <jaxb:bindings node="xs:element[@name='publicationYear']">
                <jaxb:property name="year" />
            </jaxb:bindings>
        </jaxb:bindings>
    </jaxb:bindings>
</jaxb:bindings>

```

使用`xjc`工具来生成自定义映射的Java类。运行以下命令：

```
xjc -d src -b customBindings.xml your-schema.xsd
```

这将生成与自定义映射规则匹配的`Book`类，包括`title`、`author`和`year`属性。现在，可以使用新生成的`Book`类来进行序列化和反序列化，JAXB将按照自定义映射规则处理XML数据


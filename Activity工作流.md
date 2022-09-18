# BPMN

## 使用步骤

Activity是一个工作流引擎（本质是一堆jar包API），业务系统访问（操作）activity的接口，就可以方便的操作流程相关数据，这样就可以把工作流环境与业务系统集成在一起。

## 流程定义

使用activity流程建模工具（activity-designer）定义业务流程（.bpmn文件）。

## 流程定义部署

使用activity提供的api把流程定义内容存储起来，在Activity执行过程中可以查询定义的内容。

**Activity执行把流程定义内容存储在数据库中。**

## 启动一个流程实例

### 1、依赖

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.activiti</groupId>
      <artifactId>activiti-dependencies</artifactId>
      <version>7.0.0.Beta1</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

值得注意的是：activiti的运行必须支持相关的数据库（mysql、oracle...）

### 2、数据库

#### 2.1创建数据库：

> 创建mysql数据库activiti（名字任意）
>
> create database activiti default character set utf8;

#### 2.2java代码生成表

>创建java工程：
>
>> 使用idea创建java maven工程
>
>加入maven依赖的坐标：
>
>> avtiviti-engine-7.0.0.beta.jar
>>
>> activi依赖的mybatis、afl4j、log4j等
>>
>> activi依赖的spring包
>>
>> mysql数据驱动
>>
>> 第三方连接池dbcp
>>
>> 单元测试

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
</dependency>
<!--bpmn模型处理-->
<dependency>
    <groupId>org.activiti</groupId>
    <artifactId>activiti-bpmn-model</artifactId>
</dependency>
<!--bpmn转换-->
<dependency>
    <groupId>org.activiti</groupId>
    <artifactId>activiti-bpmn-converter</artifactId>
</dependency>
<dependency>
    <groupId>org.activiti</groupId>
    <artifactId>activiti-json-converter</artifactId>
</dependency>
<dependency>
    <groupId>org.activiti</groupId>
    <artifactId>activiti-bpmn-layout</artifactId>
</dependency>
<dependency>
    <groupId>org.activiti.cloud</groupId>
    <artifactId>activiti-cloud-services-api</artifactId>
    <version>7.0.0.Beta1</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.5</version>
</dependency>
<dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>1.4</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.25</version>
</dependency>
```

#### 2.3activiti配置文件

我们使用activiti提供的默认方式来创建mysql的表。

默认方式的要求是在resource下创建activiti.cfg.xml文件，注意：默认方式目录和文件名不能修改，因为activiti的源码中已经设置到固定的读取固定位置的文件名和文件（约定大于配置）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans   http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="processEngineConfiguration" class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration">
        
        <!--配置数据库的相关数据-->
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/activiti" />
        <property name="jdbcDriver" value="com.mysql.jdbc.Driver" />
        <property name="jdbcUsername" value="root" />
        <property name="jdbcPassword" value="" />
        <!-- 配置模式  true 自动创建和更新表(如果存在这些表就使用，不存在就自动创建) -->
        <property name="databaseSchemaUpdate" value="true" />
    </bean>
</beans>
```

#### 2.4使用activiti工具类生成25张表

```java
public void testCrateDbTable() {
    // 第一次创建processEngine时就会创建25张表
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
}
```

![image-20220727000154676](img/image-20220727000154676.png)
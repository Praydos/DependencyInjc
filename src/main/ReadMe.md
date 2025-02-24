# Java Dependency Injection Examples

This repository demonstrates various approaches to implementing Dependency Injection (DI) in Java, including static coupling, dynamic instantiation, and Spring-based methods (XML and annotations).

## Table of Contents

- [Introduction](#introduction)
- [Project Structure](#project-structure)
- [Dependency Injection Approaches](#dependency-injection-approaches)
  - [Static Coupling](#static-coupling)
  - [Dynamic Instantiation](#dynamic-instantiation)
  - [Spring XML Configuration](#spring-xml-configuration)
  - [Spring Annotations](#spring-annotations)
- [Dependencies](#dependencies)
- [Execution Guide](#execution-guide)

## Introduction

Dependency Injection (DI) is a design pattern that decouples components by externalizing their dependencies. This project illustrates four DI techniques:

1. **Static Coupling**: Direct instantiation without flexibility.
2. **Dynamic Instantiation**: Runtime dependency loading via reflection and configuration files.
3. **Spring XML Configuration**: Traditional XML-based DI with Spring Framework.
4. **Spring Annotations**: Modern annotation-driven DI using Spring.

## Project Structure

```
src/main/java/com/jee/
├── static_instanciation/
│   ├── dao/
│   ├── metier/
│   └── presentation/
├── dynamic_instanciation/
│   ├── dao/
│   ├── metier/
│   └── presentation/
├── spring/
│   ├── xml/
│   │   ├── dao/
│   │   ├── metier/
│   │   └── presentation/
│   └── annotations/
│       ├── dao/
│       ├── metier/
│       └── presentation/
└── resources/
    ├── config.yaml    # Dynamic instantiation config
    └── config.xml     # Spring XML configuration
```

## Dependency Injection Approaches

### Static Coupling

Dependencies are hard-coded directly in classes, leading to tight coupling.

**Example Code**:
```java
// Presentation class
IDao dao = new DaoImpl();
IMetier metier = new MetierImpl(dao);
System.out.println(metier.calculate());
```

### Dynamic Instantiation

Dependencies are loaded at runtime using reflection, based on a YAML configuration file.

**Configuration (config.yaml)**:
```yaml
classes:
  dao: "com.jee.dynamic_instanciation.dao.DaoImpl"
  metier: "com.jee.dynamic_instanciation.metier.MetierImpl"
```

**Java Implementation**:
```java
// Load classes dynamically
IDao dao = (IDao) Class.forName(daoClass).getConstructor().newInstance();
IMetier metier = (IMetier) Class.forName(metierClass).getConstructor().newInstance();

// Inject dependency via reflection
Method setDao = metier.getClass().getMethod("setDao", IDao.class);
setDao.invoke(metier, dao);

System.out.println(metier.calculate());
```

### Spring XML Configuration

Uses Spring's XML configuration to define and inject beans.

**XML Configuration (config.xml)**:
```xml
<beans>
    <bean id="dao" class="com.jee.spring.xml.dao.DaoImpl" />
    <bean id="metier" class="com.jee.spring.xml.metier.MetierImpl">
        <constructor-arg ref="dao" />
    </bean>
</beans>
```

**Java Implementation**:
```java
ApplicationContext context = new ClassPathXmlApplicationContext("config.xml");
IMetier metier = context.getBean(IMetier.class);
System.out.println(metier.calculate());
```

### Spring Annotations

Leverages Spring's annotation-driven DI (e.g., `@Component`, `@Autowired`).

**Annotation-Based Configuration**:
```java
@Component("dao")
public class DaoImpl implements IDao { ... }

@Component
public class MetierImpl implements IMetier {
    @Autowired
    private IDao dao;
    // ...
}
```

**Java Implementation**:
```java
ApplicationContext context = new AnnotationConfigApplicationContext("com.jee.spring.annotations");
IMetier metier = context.getBean(IMetier.class);
System.out.println(metier.calculate());
```

## Dependencies

- **Spring Framework**: Core, Context, and Beans modules (v6.2.3)
- **Jakarta Annotation API**: v2.1.1

Managed via Maven in `pom.xml`.

## Execution Guide

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/Praydos/DependencyInjc.git
   ```

2. **Build the Project**:
   ```bash
   cd DependencyInjc
   mvn clean install
   ```

3. **Run Examples**:
   - **Static Coupling**:
     ```
     mvn exec:java -Dexec.mainClass="com.jee.static_instanciation.presentation.Presentation"
     ```
   - **Dynamic Instantiation**:
     ```
     mvn exec:java -Dexec.mainClass="com.jee.dynamic_instanciation.presentation.Presentation"
     ```
   - **Spring XML**:
     ```
     mvn exec:java -Dexec.mainClass="com.jee.spring.xml.presentation.Presentation"
     ```
   - **Spring Annotations**:
     ```
     mvn exec:java -Dexec.mainClass="com.jee.spring.annotations.presentation.Presentation"
     ```

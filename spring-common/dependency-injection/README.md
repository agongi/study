## Dependency injection

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.04.15
ㅁ References:
 - http://vojtechruzicka.com/field-dependency-injection-considered-harmful/
```

### By field
```java
@Autowired
private DependencyA dependencyA;

@Autowired
private DependencyB dependencyB;

@Autowired
private DependencyC dependencyC;
```
when injecting directly to fields, you provide no direct way of instantiating the class with all its required dependencies. That means:

- There is a way (by calling the default constructor) to create object using new in a state when it is lacking some of its mandatory collaborators and usage will result in the NullPointerException.
- Such a class cannot be reused outside DI containers (tests, other modules) as there is no way except reflection to provide it with its required dependencies.
- Unlike constructor, field injection cannot be used to assign dependencies to final fields effectively rendering your objects mutable.

### By constructor
```java
private DependencyA dependencyA;
private DependencyB dependencyB;
private DependencyC dependencyC;

@Autowired
public DI(DependencyA dependencyA, DependencyB dependencyB, DependencyC dependencyC) {
    this.dependencyA = dependencyA;
    this.dependencyB = dependencyB;
    this.dependencyC = dependencyC;
}
```

- Mandatory dependencies are recommended to use constructor injection
- Implicit constructor injection is available since 4.3+
  - Don't need to declare default constructor explicitly

### By setter
```java
private DependencyA dependencyA;
private DependencyB dependencyB;
private DependencyC dependencyC;

@Autowired
public void setDependencyA(DependencyA dependencyA) {
    this.dependencyA = dependencyA;
}

@Autowired
public void setDependencyB(DependencyB dependencyB) {
    this.dependencyB = dependencyB;
}

@Autowired
public void setDependencyC(DependencyC dependencyC) {
    this.dependencyC = dependencyC;
}
```

- Optional dependencies are recommended to use setter injection

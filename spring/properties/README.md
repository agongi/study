## Properties

```
ㅁ Author: suktae.choi
- https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html
- https://www.mkyong.com/java/java-properties-file-examples/
- https://blog.outsider.ne.kr/794
```

## Resource

Resource is a physical file that stores key=pair values. Spring provides abstraction interface for better accessing as following:

- UrlResource - http:
- ClassPathResource - classpath:
- FileSystemResource - file:
- ServletContextResource
- InputStreamResource
- ByteArrayResource

## ResourceLoader

All applicationContext (ex. AnnotationConfigWebApplicationContext, ClassPathXmlApplicationContext) implement ResourceLoader interface for providing resources that corresponds to type.

> ClassPathResourceLoader provides ClassPathResource

```java
// applicationContext implements resourceLoader
public interface ResourceLoader {
  Resource getResource(String location);
}
```

ResourceLoader could be injected using ResourceLoaderAware.

```java
public interface ResourceLoaderAware {
  void setResourceLoader(ResourceLoader resourceLoader);
}
```

## System Properties

System properties are the values from environment variables and/or JVM launch options.

This can be set/get like following:

```bash
# environment variables
$ export env=dev
$ echo $env
dev

# JVM arguments (-D means define)
$ java -jar -DserverName='api-server-1' ROOT.jar
```

```java
@Test
public void systemPropertiesTest() {
  String phaseName = System.getProperty("env");
  String serverName = System.getProperty("serverName");
}
```

The table describes some of the system properties provided as default.

| Key                 | Meaning                                                      |
| ------------------- | :----------------------------------------------------------- |
| `"file.separator"`  | Character that separates components of a file path. This is "`/`" on UNIX and "`\`" on Windows |
| `"java.class.path"` | Path used to find directories and JAR archives containing class files. Elements of the class path are separated by `path.separator` property |
| `"java.home"`       | Installation directory for Java Runtime Environment (JRE)    |
| `"java.vendor"`     | JRE vendor name                                              |
| `"java.vendor.url"` | JRE vendor URL                                               |
| `"java.version"`    | JRE version number                                           |
| `"line.separator"`  | Sequence used by operating system to separate lines in text files |
| `"os.arch"`         | Operating system architecture                                |
| `"os.name"`         | Operating system name                                        |
| `"os.version"`      | Operating system version                                     |
| `"path.separator"`  | Path separator character used in `java.class.path`           |
| `"user.dir"`        | User working directory                                       |
| `"user.home"`       | User home directory                                          |
| `"user.name"`       | User account name                                            |

## Practices

### Resource

ResourceUtils 를 통해 가져올때는 prefix 가 필요하지만, 구현클래스를 직접 지정할때는 없어도 된다.

>특정 구현 클래스를 사용했다는 의미가 이미 prefix 를 명시한것이나 다름없음

- Resource
  - \#getFile
  - \#getInputStream
- File
  - FileInputStream

```java
/**
* Spring#Resource 로 properties load
*/
private static class ResourceTest {
  public ResourceTest() throws FileNotFoundException {
    Resource resource = new ClassPathResource("application.yml");
    Properties prop = new Properties();

    try {
      // from resource#1
      prop.load(new InputStreamReader(resource.getInputStream(), "UTF-8"));
      // from resource#2
      prop = PropertiesLoaderUtils.loadProperties(resource);

      // from file#1
      prop.load(new FileInputStream(resource.getFile()));
      // from file#2
      File file = ResourceUtils.getFile("classpath:application.yml");
      prop.load(new FileInputStream(file));

    } catch (IOException e) {
      throw new BaseRuntimeException("properties load 실패");
    }
  }
}
```

### PatternMatcher

** 패턴을 인식하기위해선 (즉 directory 지정) patternResolver 가 필요하다.

```java
PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
Resource[] resources = resolver.getResources("classpath:/**/*.yml");
```

### Environment/@Value

```java
/**
* Environment (and/or ApplicationContext)
*/
public class LocaleConfig implements EnvironmentAware {
  // value
  @Value("${title}")
  private String title1;
  @Value("${title:defaultTitle}")
  private String title2;

  // SpEL
  @Value("#{systemProperties['title']}")
  private String title3;
  @Value("#{systemProperties['title'] ?: defaultTitle}")
  private String title4;

  // env
  private Environment env;

  @Override
  public void setEnvironment(Environment environment) {
    this.env = environment;
  }

  public void init() {
    String title = env.getProperty("title");
  }
}
```

### PlaceHolder

```java
@BeforeClass
public static void before() throws IOException {
  ClassPathResource resource = new ClassPathResource("prop/core/config-default.properties");

  // load
  try (InputStream in = resource.getInputStream()) {
    properties.load(in);
  }

  // resolve - default system properties 
  if (PhaseResolver.getPhase() == PhaseType.local) {
    // server.home=${user.home}/apps
    // -> /home/rw_user/apps
    properties.setProperty("user.home", System.getProperty("user.home"));
  }

  // resolve - placeHolders
  PropertyPlaceholderHelper propertyPlaceholderHelper = new PropertyPlaceholderHelper(PlaceholderConfigurerSupport.DEFAULT_PLACEHOLDER_PREFIX, PlaceholderConfigurerSupport.DEFAULT_PLACEHOLDER_SUFFIX);
  for (String key : properties.stringPropertyNames()) {
    // server.home=/home/rw_user
    // server.home.api=${server.home}/api
    // -> /home/rw_user/api
    properties.setProperty(key, propertyPlaceholderHelper.replacePlaceholders(properties.getProperty(key), properties));
  }
}
```
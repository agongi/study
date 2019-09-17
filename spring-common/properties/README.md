## Properties

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html
- https://www.mkyong.com/java/java-properties-file-examples/
- https://blog.outsider.ne.kr/794
```

### Cores

#### Resource

Resource is a physical file that stores key=pair values. Spring provides abstraction interface for better accessing as following:

- UrlResource - http:
- ClassPathResource - classpath:
- FileSystemResource - file:
- ServletContextResource
- InputStreamResource
- ByteArrayResource

#### ResourceLoader

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

#### System Properties

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

### Practice

#### Resource

Resource interface 의 구현클래스 (ex. ClassPathResource) 를 이용할때는 prefix 없이 바로 URI 만 입력한다.

> 특정 구현 클래스를 사용했다는 의미가 이미 prefix 를 명시한것이나 다름없음

```java
/**
* Spring#Resource 로 properties load
*/
private static class ContextHolder {
  private static final String DEFAULT_PROPERTIES = "properties/core/application.properties";

  static {
    Resource resource = new ClassPathResource(DEFAULT_PROPERTIES);
    Properties properties = new Properties();

    try {
      properties.load(new InputStreamReader(resource.getInputStream(), StandardCharsets.UTF_8.name()));
    } catch (IOException e) {
      throw new BaseRuntimeException("properties load 실패");
    }
  }
```

#### ResourceUtils

Spring providing ResourceUtils requires `location-prefix` to distinguish where to find from

> Util is general usage such as classPath, fileSystem etc. so prefix is required.

```java
/**
 * Spring#ResourceUtils 로 properties load
 */
public static void main(String[] args) throws IOException {
  File file = ResourceUtils.getFile("classpath:application.yml");

  Properties prop1 = new Properties();
  prop1.load(new FileInputStream(file));

  Properties prop2 = new Properties();
  prop2.load(new FileReader(file, StandardCharsets.UTF_8));
}
```

#### Environment/@Value

```java
/**
* Environment (and/or ApplicationContext)
*/
public class LocaleConfig implements EnvironmentAware {
  @Value("${title}")
  private String title;
  private Environment env;
  
  @Override
  public void setEnvironment(Environment environment) {
    this.env = environment;
  }

  public void init() {
		String contents = env.getProperty("contents");
  }
}
```

#### PlaceHolder

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
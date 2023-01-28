## Boot Features Testing

```
ㅁ Author: suktae.choi
- https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-testing
```

## Configuration

### Detecting

기존 방식으로는 @ContextConfiguration 을 통해 import 할 @Configuration classes 를 지정해서 context 를 올릴수 있습니다.

> 기존 spring test framework 동작원리

### Mock Environment

- server: not started
- mock: enabled

```java
@SpringBootTest
@AutoConfigureMockMvc
class MockMvcExampleTests {
  // @AutoConfigureMockMvc 로 인해 bean 이 생성됨
  @Autowired MockMvc mvc;

  @Test
  void exampleTest() throws Exception {
    mvc.perform(get("/"))
      .andExpect(status().isOk())
      .andExpect(content().string("Hello World"));
  }
}
```

- server: started
- mock: disabled

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class RandomPortWebTestClientExampleTests {
  @Autowired WebTestClient webClient;

  @Test
  void exampleTest() throws Exception {
    webClient.get()
      .uri("/")
      .exchange()
      .expectStatus().isOk()
      .expectBody(String.class).isEqualTo("Hello World");

  }
}
```

대신 실제 embedded tomcat 을 띄우므로, 테스트  module 에서 mvc 관련 의존 전체가 필요합니다. (boot-starter-mvc)

submodule 로써 mvc 의존이 불필요한 jar 성격이라면 테스트를 위해 web 의존이 필요한데요, 별도 bootable-mock 서버로 떠서 테스트를 지원하는 wiremock 으로 대체할 수 있습니다.

그러면 test context 에서는 mvc 의존이 필요없고, 별도로 뜨는 서버 (== wiremock) 을 통해 api mock 테스트가 가능합니다.

### Mock Bean

@MockBean, @SpyBean 의 사용을 의미합니다.

기본적으로 @SpringBootTest 를 사용한다면 enabled 이고, 만약 별도 config 를 통한 테스트를 한다면, 명시적으로 활성화 하면됩니다.

```java
@TestExecutionListeners(MockitoTestExecutionListener.class)
```

## Annotations

범용적인 test context 를 올리는 @SpringBootTest 가 존재하고 각각의 scope 에 맞춰서 include/exclude (== sliced) 를 적용한 어노테이션이 같이 제공됩니다.

- Excludes

TypeExcludeFilters 를 이용해서 include/exclude 를 직접 제어합니다. 

```java
// omitted ..
@TypeExcludeFilters(WebMvcTypeExcludeFilter.class)
public @interface WebMvcTest { .. }
```

WebMvcTypeExcludeFilter 내부를 보면

```java
public final class WebMvcTypeExcludeFilter extends StandardAnnotationCustomizableTypeExcludeFilter<WebMvcTest> {

  private static final Class<?>[] NO_CONTROLLERS = {};

  private static final String[] OPTIONAL_INCLUDES = {
    "org.springframework.security.config.annotation.web.WebSecurityConfigurer" };

  private static final Set<Class<?>> DEFAULT_INCLUDES;

  static {
    Set<Class<?>> includes = new LinkedHashSet<>();
    includes.add(ControllerAdvice.class);
    includes.add(JsonComponent.class);
    includes.add(WebMvcConfigurer.class);
    includes.add(javax.servlet.Filter.class);
    includes.add(FilterRegistrationBean.class);
    includes.add(DelegatingFilterProxyRegistrationBean.class);
    includes.add(HandlerMethodArgumentResolver.class);
    includes.add(HttpMessageConverter.class);
    includes.add(ErrorAttributes.class);
    includes.add(Converter.class);
    includes.add(GenericConverter.class);
    includes.add(HandlerInterceptor.class);
    for (String optionalInclude : OPTIONAL_INCLUDES) {
      try {
        includes.add(ClassUtils.forName(optionalInclude, null));
      }
      catch (Exception ex) {
        // Ignore
      }
    }
    DEFAULT_INCLUDES = Collections.unmodifiableSet(includes);
  }

  @Override
  protected Set<Class<?>> getDefaultIncludes() {
    if (ObjectUtils.isEmpty(this.controllers)) {
      return DEFAULT_INCLUDES_AND_CONTROLLER;
    }
    return DEFAULT_INCLUDES;
  }

  @Override
  protected Set<Class<?>> getComponentIncludes() {
    return new LinkedHashSet<>(Arrays.asList(this.controllers));
  }
}
```

빈으로 올릴 Type 목록을 관리하고, 이를 통해 test-context 에서 bean 으로 올린 type 을 정의했습니다.

- Includes

```java
@AutoConfigureCache
@AutoConfigureWebMvc
@AutoConfigureMockMvc
@ImportAutoConfiguration
public @interface WebMvcTest {
```

`@AutoConfigure..` 를 통해 추가로 정의할 bean 을 추가합니다.

- Categories

| Annotation      | Desc                          | Bean Scan              |
| :-------------- | :---------------------------- | :--------------------- |
| @SpringBootTest | Integration Test              | 전체                   |
| @WebMvcTest     | Unit Test, Controller         | MVC 관련 Bean          |
| @WebFluxTest    | Unit Test, WebFlux Entrypoint | WebFlux 관련 Bean      |
| @DataJpaTest    | Unit Test, JPA                | JPA 관련 Bean          |
| @JdbcTest       | Unit Test, Jdbc               | JDBC 관련 Bean         |
| @RestClientTest | Unit Test, RestClient         | RestTemplate 관련 Bean |
| ~~@JsonTest~~   | Unit Test, Json               | Serdes 관련 Bean       |

### SpringBootTest

TestContext 로드 시점에 테스트 관련한 모든 의존성을 포함해서 bootStrap 합니다. 모든 종류의 테스트가 가능합니다.

내부 코드를 보면

```java
@BootstrapWith(SpringBootTestContextBootstrapper.class)
@ExtendWith(SpringExtension.class)
public @interface SpringBootTest { .. }
```

으로 `SpringBootTestContextBootstrapper.class` 가 호출되고 classpath:// 전체를 스캔해서 의존성을 올립니다.

범용적으로 사용 가능하므로 abstract 로 의존을 선언해줍니다.

```java
@ActiveProfiles("test")
@SpringBootTest
@TestExecutionListeners(
    value = {customListener.class},
    mergeMode = TestExecutionListeners.MergeMode.MERGE_WITH_DEFAULTS
)
public abstract class AbstractSpringTestSupport { ... }
```

```java
class customTest extends AbstractSpringTestSupport {
  // .. @Test
}
```

### WebMvcTest

context-load 시 의존에 포함되는 빈의 범위가 MVC 관련으로 한정됩니다.

- Controller
- Filter
- HandleMethodArgumentResolver
- Security

대신 Service, Repository 는 스캐닝되지 않으므로,  별도 mocking 이 필요합니다. 간단한 사용법은:

```java
@WebMvcTest(TestRestController.class)
class TestRestControllerTest {

  @Autowired
  private MockMvc mvc;
  @MockBean
  private TestService testService;

  @Test
  void getListTest() throws Exception {
		// given
    given(testService.selectUser(1234L))
      .willReturn("dummy-response");

    // when
    var actions = mvc.perform(MockMvcRequestBuilders
			.get("/user")
			.param("id", "1234")
			.contentType(MediaType.APPLICATION_JSON))
      .andDo(print());

    // then
    actions.andExpect(status().isOk())
      .andExpect(content().contentType(MediaType.APPLICATION_JSON))
      .andDo(print());
  }
}
```

Mock 으로 생성할 Controller 를 어노테이션에 지정합니다. 그러면 해당 컨트롤러가 `MockMvc` 의 빈으로 등록이 되어 (mockBean) 테스트를 수행 할 수 있습니다.

하지만 현재 프로젝트에 코드상 존재하는 Controller 만 등록가능하므로, 외부 API mocking 을 위해선 별도로 하나 만들어야하는 단점이 있습니다. 이 개념을 해결하는 것이 [WireMock](http://wiremock.org/docs/configuration/) 입니다. 간단히 설명하면 standalone and/or boot-integration 으로 뜨는 별도의 MVC 가 있고 응답으로 사용할 meta (classpath://xxx.json) 를 load 해서 요청이 오면 json 으로 응답합니다.

WireMock 을 사용하면 외부 API 통신시 개발완료를 대기할 필요없이, 간단히 모킹해서 바로 후속작업을 진행 할 수 있습니다.

### DataJpaTest

context-load 시 의존에 포함되는 빈의 범위가 JPA (a.k.a repository, jdbc 등) 관련으로 한정됩니다.

간단히 CRUD 를 확인하는 용도로 적합합니다.

> 별도로 Mock 대상을 지정하는 설정이 없습니다. 그래서 전체 JPA 관련 의존이 모두 올라갑니다.

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class MemberRepositoryTest {
  @Autowired
  private MemberRepository memberRepository;

  @Test
  void findById() {
    // given
    Long memberNo = 12334L;
    // when
    Member member = memberRepository.findById(memberNo);
    // then
		assertThat(member).isNotNull();
		assertThat(member.getId()).isEqualTo(memberNo);
  }
}
```

### @JdbcTest

DataSource 와 JdbcTemplate 만 올라가는 테스트입니다. spring-data 는 올라가지 않으므로 repository 는 사용할 수 없습니다.

```java
@AutoConfigureCache
@AutoConfigureJdbc
@AutoConfigureTestDatabase
@ImportAutoConfiguration
public @interface JdbcTest { .. }

// dataSource 와 jdbcTemplate load
@ConditionalOnClass({ DataSource.class, JdbcTemplate.class })
@ConditionalOnSingleCandidate(DataSource.class)
@AutoConfigureAfter(DataSourceAutoConfiguration.class)
@EnableConfigurationProperties(JdbcProperties.class)
@Import({ JdbcTemplateConfiguration.class, NamedParameterJdbcTemplateConfiguration.class })
public class JdbcTemplateAutoConfiguration {
	// ..
}
```

### RestClientTest

context-load 시 의존에 포함되는 빈이 RestClient, WebClient 으로 한정됩니다.

```java
@ActiveProfiles("test")
@RestClientTest(CustomWebClientConfig.class)
class CustomWebClientConfigTest {

  @Autowired
  @Qualifier("customWebClient")
  private WebClient webClient;

  @Test
  public void getUser() {
    // given
    String userId = "1234";

    // when
    Mono<UserResponse> responseMono = webClient.get()
      .uri(uriBuilder -> uriBuilder.path("/user")
           .queryParam("userId", userId)
           .build())
      .retrieve()
      .bodyToMono(UserResponse.class);

    // then
    StepVerifier.create(responseMono)
      .assertNext(actual -> {
        assertThat(actual).isNotNull();
        assertThat(actual.getId()).isEqualTo(userId);
      })
      .expectNextCount(0)
      .verifyComplete();
  }
}
```
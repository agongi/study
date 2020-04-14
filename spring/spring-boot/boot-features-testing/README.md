## Boot Features Testing

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-testing
```

| Annotation      | Desc                          | Bean Scan              |
| :-------------- | :---------------------------- | :--------------------- |
| @SpringBootTest | Integration Test              | 전체                   |
| @WebMvcTest     | Unit Test, Controller         | MVC 관련 Bean          |
| @WebFluxTest    | Unit Test, WebFlux Entrypoint | WebFlux 관련 Bean      |
| @DataJpaTest    | Unit Test, JPA                | JPA 관련 Bean          |
| @RestClientTest | Unit Test, RestClient         | RestTemplate 관련 Bean |
| ~~@JsonTest~~   | Unit Test, Json               | Serdes 관련 Bean       |

## SpringBootTest

TestContext 로드 시점에 테스트 관련한 모든 의존성을 포함해서 bootStrap 합니다. 모든 종류의 테스트가 가능합니다.

내부 코드를 보면

```java
@BootstrapWith(SpringBootTestContextBootstrapper.class)
@ExtendWith(SpringExtension.class)
public @interface SpringBootTest { .. }
```

으로 `SpringBootTestContextBootstrapper.class` 가 호출되고 classpath:// 전체를 스캔해서 의존성을 올립니다.

범용적인 패턴이므로 Support 를 하나 만들어놨습니다.

```java
@ActiveProfiles("test")
@SpringBootTest
@TestExecutionListeners(
    value = {AbstractSpringTestSupport.NoOpExecutionListener.class},
    mergeMode = TestExecutionListeners.MergeMode.MERGE_WITH_DEFAULTS
)
public abstract class AbstractSpringTestSupport { ... }
```

```java
class customTest extends AbstractSpringTestSupport {
  // .. @Test
}
```

## WebMvcTest

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

하지만 현재 프로젝트에 코드상 존재하는 Controller 만 등록가능하므로, 외부 API mocking 을 위해선 별도로 하나 만들어야하는 단점이 있습니다. 이 개념을 해결하는 것이 [WireMock](http://wiremock.org/docs/configuration/) 입니다. 간단히 설명하면 standalone and/or boot-integration 으로 뜨는 별도의 MVC 가 있고 응답을 classpath://xxx.json 에 저장해놓으면 해당값을 응답합니다.

WireMock 을 사용하면 외부 API 통신시 개발완료를 대기할 필요없이, 간단히 모킹해서 바로 후속작업을 진행 할 수 있습니다.

## DataJpaTest

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

## RestClientTest

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
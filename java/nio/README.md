## NIO (New Input/Output)

```
ㅁ Author: suktae.choi
- https://docs.oracle.com/javase/tutorial/essential/io/file.html
- https://mkyong.com/tutorials/java-io-tutorials/
- https://dog-foot-story.tistory.com/45
- http://palpit.tistory.com/640
- https://javapapers.com/java/java-nio-file-read-write-with-channels/
- http://eincs.com/2009/08/java-nio-bytebuffer-channel/
```

#### index

- [ByteBuffer](bytebuffer)

***

## Files

<img src='images/1.png'/>

| Stream                                         | Channel           |
| ---------------------------------------------- | ----------------- |
| one-way<br/> - InputStream<br/> - OutputStream | two-way           |
| 보통은 byte[]                                  | 보통은 ByteBuffer |

- Write
  - ~~Files.write~~
  - Files.newOutputStream
  - Files.newBufferedWriter
  - ~~(Channel 을 쓴다면) Files.newByteChannel#write~~

- Read
  - ~~Files.readAllByte / Files.readAllLines~~
  - Files.newInputStream
  - Files.newBufferedReader
  - ~~(Channel 을 쓴다면) Files.newByteChannel#read~~

### Resource - Files#lines

Resource => String 을 통해 처리하는 방식이다.

```java
public static void main(String[] args) throws IOException {
  Resource resource = new ClassPathResource("ips");
  Path path = Path.of(resource.getURI());
  // input
  Stream<String> lines = Files.lines(path);
  // consume
  lines.forEach(o -> {
    System.out.println(o);
  });
}
```

### InputStream - Files#lines

InputStream => String 을 통해 처리하는 방식이다.

```java
public static void main(String[] args) throws IOException {
  InputStream inputStream = ClassLoader.getSystemResourceAsStream("ips");
  BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
  // input
  Stream<String> lines = bufferedReader.lines();
  // consume
  lines.forEach(o -> {
    System.out.println(o);
  });
}
```

| 종류              | Data 형식                    | 비고    |
| ----------------- | ---------------------------- | ------- |
| InputStream       | byte                         | #read   |
| InputStreamReader | char (string 단건)           | #read   |
| BufferedReader    | string (string \n 만큼 읽음) | \#lines |

### File - Files#lines

기존 패키지 호환을 위해 java.io.File 이 필요하면, 이렇게 처리하자.

```java
public static void main(String[] args) throws IOException {
  File file = new File("any/path");
  // input
  Stream<String> lines = Files.lines(file.toPath());
  // consume
  lines.forEach(o -> {
    System.out.println(o);
  });
}
```

### InputStream - OutputStream

Stream - Stream 으로 직접 이용할때는 아래로 일괄처리.

```java
public static void main(String[] args) throws IOException {
  try (var in = new BufferedInputStream(Files.newInputStream(Path.of("any/path")));
       var out = new BufferedOutputStream(Files.newOutputStream(Path.of("any/path")))) {
    byte[] read = new byte[8192];
    // input
    if (in.read(read) != -1) {
      // consume
      out.write(read);
    }
  } catch (Exception e) {
    e.printStackTrace();
  }
}
```

해당 구현이 java 9 부터는 `InputStream#transferTo` 로 제공된다.

```java
public static void main(String[] args) throws IOException {
  try (var in = Files.newInputStream(Path.of("any/path"));
       var out = Files.newOutputStream(Path.of("any/path"))) {
    in.transferTo(out);
  } catch (Exception e) {
    e.printStackTrace();
  }
}
```

## ClassLoader

Resource 를 읽을때 1) ClassPath 2) Class 에서 읽을때의 경로 지정이 다르다.

```java
public static void main(String[] args) throws IOException {
  InputStream is1 = ClassLoader.getSystemResourceAsStream("ips");
  InputStream is2 = ClassLoader.getSystemResourceAsStream("/ips");	// error!
  InputStream is3 = Test.class.getClassLoader().getResourceAsStream("ips");
  InputStream is4 = Test.class.getClassLoader().getResourceAsStream("/ips");	// error!
  
  InputStream is5 = Test.class.getResourceAsStream("ips");	// error!
  InputStream is6 = Test.class.getResourceAsStream("/ips");
}
```

- ClassLoader 를 이용해서 읽을때는, 이미 absolute path 이므로 `/` 없이 지정해야한다.
- Class 로 부터 읽을때는, 클래스 기준에서의 relative path 이므로, `/` 지정이 필요하다.

## Paths

Path 는 기본적으로 `FileSystem 을 기준`으로 한다.

- String

```java
public static Path of(String first, String... more) {
  return FileSystems.getDefault().getPath(first, more);
}
```

그게 아니면 URI 을 통해서 가져온다. URI 는 Resource 에서 convert 가능하므로 대부분의 포맷에서 가능하다.

- URI

```java
public static Path of(URI uri) {
  String scheme =  uri.getScheme();
  // ...
}
```

- File

```java
public static void main(String[] args) throws IOException {
  File file = new File("some/path");
  Path path = file.toPath();
}
```

- Resource

```java
public static void main(String[] args) throws IOException {
  Resource resource = new ClassPathResource("some/resource");
  Path path = Path.of(resource.getURI());
}
```


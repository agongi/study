## NIO (New Input/Output)

```
ㅁ Author: suktae.choi
ㅁ References:
- http://palpit.tistory.com/640
- https://javapapers.com/java/java-nio-file-read-write-with-channels/
- http://eincs.com/2009/08/java-nio-bytebuffer-channel/
```

#### index

- [ByteBuffer](bytebuffer)

***

## Files

<img src='images/1.png'/>

| Stream                                           | Channel           |
| ------------------------------------------------ | ----------------- |
| one-way<br /> - InputStream<br /> - OutputStream | two-way           |
| 보통은 byte[]                                    | 보통은 ByteBuffer |

### Write

- ~~Files.write~~
- Files.newOutputStream
- Files.newBufferedWriter
- (Channel 을 쓴다면) Files.newByteChannel#write

### Read

- ~~Files.readAllByte / Files.readAllLines~~
- Files.newInputStream
- Files.newBufferedReader
- (Channel 을 쓴다면) Files.newByteChannel#read

> **Resource**

```java
public static void main(String[] args) throws IOException {
  Resource resource = new ClassPathResource("ips");
  Path path = Path.of(resource.getURI());
  // stream
  Stream<String> lines = Files.lines(path);

  lines.forEach(o -> {
    System.out.println(o);
  });
}
```

> **InputStream**

| 종류              | Data 형식                    | 비고    |      |
| ----------------- | ---------------------------- | ------- | ---- |
| InputStream       | byte                         | #read   |      |
| InputStreamReader | char (string 단건)           | #read   |      |
| BufferedReader    | string (string \n 만큼 읽음) | \#lines |      |

```java
public static void main(String[] args) throws IOException {
  InputStream inputStream = ClassLoader.getSystemResourceAsStream("ips");
  BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
  // stream
  Stream<String> lines = bufferedReader.lines();
  
  lines.forEach(o -> {
    System.out.println(o);
  });
}
```

Resource 를 읽을때 1) ClassPath 2) Class 에서 읽을때의 경로 지정이 다르다.

```java
public static void main(String[] args) throws IOException {
  InputStream is1 = ClassLoader.getSystemResourceAsStream("ips");	// exists!
  InputStream is2 = ClassLoader.getSystemResourceAsStream("/ips");
  InputStream is3 = Test.class.getClassLoader().getResourceAsStream("ips"); // exists!
  InputStream is4 = Test.class.getClassLoader().getResourceAsStream("/ips");
  InputStream is5 = Test.class.getResourceAsStream("ips");
  InputStream is6 = Test.class.getResourceAsStream("/ips"); // exists!
}
```

ClassLoader 를 이용해서 읽을때는, 이미 absolute path 이므로 `/` 없이 지정해야한다.

Class 로 부터 읽을때는, 클래스 기준에서의 relative path 이므로, `/` 지정이 필요하다.

> File

```java
public static void main(String[] args) throws IOException {
  File file = new File("any/path");
  // File => Path
  BufferedReader bufferedReader = Files.newBufferedReader(file.toPath());
  // File => BufferedReader
  BufferedReader bufferedReader1 = new BufferedReader(new FileReader(file));
	
  // stream
  bufferedReader.lines().forEach(o -> {
    System.out.println(o);
  });
}
```

Scanner 는 사용하지 말자. 그래서 정리하지 않음

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

> File

```java
public static void main(String[] args) throws IOException {
  File file = new File("some/path");
  Path path = file.toPath();
}
```

> Resource

```java
public static void main(String[] args) throws IOException {
  Resource resource = new ClassPathResource("some/resource");
  Path path = Path.of(resource.getURI());
}
```


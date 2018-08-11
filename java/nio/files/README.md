## Files

```
ㅁ Author: suktae.choi
```

#### Files.write()
```java
@Test
public void writeTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));
    Path file = null;

    try {
        file = Files.createTempFile(dir, "io-test", ".txt");
        log.info("{}", file);

        for (int i = 0; i < 100; i++) {
            Files.write(file, String.valueOf(i).getBytes(),
                StandardOpenOption.CREATE,
                StandardOpenOption.APPEND);
        }

    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        FileUtils.deleteQuietly(file.toFile());
    }
}
```

#### FileChannel.write()
```java
@Test
public void fileChannelTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

    ByteBuffer buffer = ByteBuffer.allocateDirect(1000);
    buffer.clear();

    try (FileChannel fileChannel = FileChannel.open(
        Files.createTempFile(dir, "io-test-", ".txt"),
        StandardOpenOption.CREATE,
        StandardOpenOption.WRITE,
        StandardOpenOption.APPEND,
        StandardOpenOption.DELETE_ON_CLOSE)) {

        for (int i = 0; i < 100; i++) {
            buffer.wrap(String.valueOf(i).getBytes());
            fileChannel.write(buffer);

            buffer.flip();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

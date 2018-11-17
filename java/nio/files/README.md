## Files

```
ㅁ Author: suktae.choi
```

### Write
#### Files.write(byte[])
```java
/**
 * Files.write(byte[])
 */
@Test
public void writeTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));
    Path file = null;

    try {
        file = Files.createTempFile(dir, "io-test", ".txt");
        log.info("{}", ToStringBuilder.reflectionToString(file));

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

#### Files.newOutputStream.write(string)
```java
/**
 * Files.newOutputStream.write(string)
 *
 * - stream is one-way
 * - outputStream flush each invocation of write
 */
 @Test
 public void outputStreamTest() {
     Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

     try (OutputStream outputStream = Files.newOutputStream(Files.createTempFile(dir, "io-test-", ".txt"),
         StandardOpenOption.CREATE,
         StandardOpenOption.APPEND)) {

         for (int i = 0; i < 100; i++) {
             outputStream.write(i);
         }
     } catch (IOException e) {
         e.printStackTrace();
     }
 }
```

#### Files.newBufferedWriter.write(string)
```java
/**
 * Files.newBufferedWriter.write(string)
 *
 * - stream is one-way
 * - bufferedOut keep bytes to flush in buffer
 */
@Test
public void bufferedWriterTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

    try (BufferedWriter bufferedWriter = Files.newBufferedWriter(Files.createTempFile(dir, "io-test-", ".txt"),
        StandardOpenOption.CREATE,
        StandardOpenOption.WRITE,
        StandardOpenOption.APPEND,
        StandardOpenOption.DELETE_ON_CLOSE)) {

        for (int i = 0; i < 100; i++) {
            bufferedWriter.write(i);
        }

        log.info("{}", ToStringBuilder.reflectionToString(bufferedWriter));

    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

#### Files.newByteChannel.write(byteBuffer)
```java
/**
 * Files.newByteChannel.write(byteBuffer)
 *
 * - channel is two-way
 */
@Test
public void byteChannelTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

    ByteBuffer buffer = ByteBuffer.allocateDirect(1000);
    buffer.clear();

    try (SeekableByteChannel byteChannel = Files.newByteChannel(Files.createTempFile(dir, "io-test-", ".txt"),
        StandardOpenOption.CREATE,
        StandardOpenOption.WRITE,
        StandardOpenOption.APPEND,
        StandardOpenOption.DELETE_ON_CLOSE)) {

        log.info("{}", ToStringBuilder.reflectionToString(byteChannel));

        for (int i = 0; i < 100; i++) {
            buffer.wrap(String.valueOf(i).getBytes());
            byteChannel.write(buffer);

            buffer.flip();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

#### FileChannel.write(byteBuffer)
```java
/**
 * FileChannel.write(byteBuffer)
 *
 * - channel is two-way
 */
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

### Read
#### Files.readAllByte(path), readAllLines(path)
```java
/**
 * Files.readAllBytes(path)
 * Files.readAllLines(path)
 */
@Test
public void readTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

    try {
        Files.walkFileTree(dir, new SimpleFileVisitor<Path>() {
            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
                if (!Files.isDirectory(file)) {
                    System.out.println(Files.readAllBytes(file));
                    System.out.println(Files.readAllLines(file));
                }

                return FileVisitResult.CONTINUE;
            }
        });
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

#### Files.newInputStream.read(bytes)
```java
/**
 * Files.newInputStream.read(bytes)
 *
 * - stream is one-way
 * - inputStream read in each invocation
 */
@Test
public void inputStreamTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

    try (InputStream inputStream = Files.newInputStream(Files.createTempFile(dir, "io-test-", ".txt"),
        StandardOpenOption.CREATE,
        StandardOpenOption.DELETE_ON_CLOSE)) {

        byte[] bytes = new byte[1024];

        while (inputStream.read(bytes) > 0) {
            System.out.println(bytes);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

#### Files.newBufferedReader.readLine()
```java
/**
 * Files.newBufferedReader.readLine()
 *
 * - stream is one-way
 * - bufferedInput keep bytes to read in buffer
 */
@Test
public void bufferedReaderTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

    try (BufferedReader bufferedReader = Files.newBufferedReader(Files.createTempFile(dir, "io-test-", ".txt"))) {

        String line = null;
        while ((line = bufferedReader.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        e.printStackTrace();

```

#### Files.newByteChannel.read(byteBuffer)
```java
/**
 * Files.newByteChannel.read(byteBuffer)
 *
 * - channel is two-way
 */
@Test
public void byteChannelTest() {
    Path dir = Paths.get(System.getProperty("java.io.tmpdir"));

    ByteBuffer buffer = ByteBuffer.allocateDirect(1000);
    buffer.clear();

    try (SeekableByteChannel byteChannel = Files.newByteChannel(Files.createTempFile(dir, "io-test-", ".txt"),
        StandardOpenOption.CREATE,
        StandardOpenOption.WRITE,
        StandardOpenOption.APPEND,
        StandardOpenOption.DELETE_ON_CLOSE)) {

        log.info("{}", ToStringBuilder.reflectionToString(byteChannel));

        for (int i = 0; i < 100; i++) {
            buffer.wrap(String.valueOf(i).getBytes());
            byteChannel.read(buffer);

            buffer.flip();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

#### FileChannel.read(byteBuffer)
```java
/**
 * FileChannel.read(byteBuffer)
 *
 * - channel is two-way
 */
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
            fileChannel.read(buffer);

            buffer.flip();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

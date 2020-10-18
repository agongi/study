## NIO (New Input/Output)

```
ㅁ Author: suktae.choi
ㅁ References:
- http://palpit.tistory.com/640
- https://javapapers.com/java/java-nio-file-read-write-with-channels/
- http://eincs.com/2009/08/java-nio-bytebuffer-channel/
```

#### index

- Buffer

***

## Files

<img src='images/1.png'/>

| Stream                                           | Channel           |
| ------------------------------------------------ | ----------------- |
| one-way<br /> - InputStream<br /> - OutputStream | two-way           |
| 보통은 byte[]                                    | 보통은 ByteBuffer |

### Write

- ~~Files.write~~
- ~~Files.newBufferedWriter~~
- Files.newOutputStream
- Files.newByteChannel#write
  - Channel 은 Buffer 를 이용해서 io 수행하므로, bufferWriter 대체가능

### Read

- ~~Files.readAllByte / Files.readAllLines~~
- ~~Files.newBufferedReader~~
- Files.newInputStream
- Files.newByteChannel#read
  - Channel 은 Buffer 를 이용해서 io 수행하므로, bufferReader 대체가능






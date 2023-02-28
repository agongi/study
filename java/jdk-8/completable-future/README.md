## CompletableFuture<T>

```
@author: suktae.choi
```

### Nutshell
- CompletableFuture.supplyAsync()
- CompletableFuture.thenApply()

- CompletableFuture.thenCompose()
- CompletableFuture.thenCombine()

- CompletableFuture.thenAccept()

- CompletableFuture.join()

```java
CompletableFuture[] futures = ...
  .toArray(CompletableFuture[size]::new);

CompletableFuture.allOf(futures).join();

// ...

List<CompletableFuture<String>> futures = ...
  .Collect(Collectors.toList());

futures.stream().map(CompletableFuture::join).collect(Collectors.toList());
```




 p.348 java 8 in action

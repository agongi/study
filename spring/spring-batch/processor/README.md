## Processor

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jojoldu.tistory.com/347?category=635883
```

Processor is used to filter or map the given one-item from Reader.

```java
@Bean
@StepScope
public ItemProcessor<String, String> processor() {
    return item -> item + "suffix"; // map
}

@Bean
@StepScope
public ItemProcessor<String, String> processor() {
    return item -> {
        if (item.startsWith("A")) {
            return item;
        } else {
            return null; // filter (== return null)
        }
    }
}
```

#### ItemProcessorAdapter/ValidatingItemProcessor

Not used nowdays. replaced by lambda

#### CompositeItemProcessor

```java
@Bean("JOB_NAME")
@JobScope
public Step step() {
    return stepBuilderFactory.get("JOB_NAME")
        .<Teacher, String>chunk(chunkSize)
        .reader(reader())
        .processor(compositeProcessor())
        .writer(writer())
        .build();
}

// compositeProcessor
@Bean
public CompositeItemProcessor compositeProcessor() {
    List<ItemProcessor> delegates = new LinkedList<>();
    delegates.add(processor1());
    delegates.add(processor2());

    CompositeItemProcessor processor = new CompositeItemProcessor<>();
    processor.setDelegates(delegates);

    return processor;
}

// processor-1
public ItemProcessor<Teacher, String> processor1() {
    return Teacher::getName;
}

// processor-2
public ItemProcessor<String, String> processor2() {
    return name -> "안녕하세요. "+ name + "입니다.";
}
```




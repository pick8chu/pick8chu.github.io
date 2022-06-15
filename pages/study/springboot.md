---

title: Spring Boot
last_updated: May 20, 2022
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: springboot.html

---

# Spring Boot feedbacks

### PostMapping
- RequestMapping X -> GetMapping/PostMapping etc.

### URL( kakao -> kakao-messages)
- REST API. URI has to be noun, and plural. No verbs.

### @ApiResponses
- it's better to set them on Common

### @RequiredArgsConstructor
- @Autowired field injection is not recommended due to reasons below:
  1. Autowired object can be changed. (it cannot be declared as 'final')
  2. Strong coupling to Spring DI(dependency Injection) container, since it relies on Spring. -> not recommended
  3. Violated SRP(Single Responsibility Principle)

### Creating Dummy Mapper when SQL is not completed
- to be able to make a test code.

### @Transactional
- Whenever it starts interacting with mappers (DB), transactional is recommended to keep on track.

### @Validate and @Valid on Containers
- It's recommended to check validation on Containers.
- @Validate is on the class and @Valid is for the variables to validate.
 
### Assert on Service
- to validate variables.
- Assert(violate) "notNull" param1
```java
  Assertions.assertThat(resultId)
    .isEqualTo(expectedId);
```

### controller test
1. test in place
```java
    MockHttpServletRequestBuilder request = get(BASE_URL)
            .contentType(MediaType.APPLICATION_JSON)
            .params(reqParam);

    mockMvc.perform(request)
            .andExpect(status().isOk())
            // $.~~ seems javascript
            .andExpect(jsonPath("$.length()").value(3))
            .andExpect(jsonPath("$.id").value(expectedId));
```
2. test after converting map
```java
    MockHttpServletRequestBuilder request = post(BASE_URL)
            .contentType(MediaType.APPLICATION_JSON)
            // param should be consumed as String
            .content(objectMapper.writeValueAsString(reqParam));
    
    String result = mockMvc.perform(request)
            .andExpect(status().isOk())
            .andReturn()
            .getResponse()
            .getContentAsString();
    
    Map<String, String> resultMap = objectMapper.readValue(result, Map.class);
```



### StringBuilder is recommended than StringBuffer
- StringBuffer is thread-safe, but StringBuilder is not. However, Spring framework guranteed thread safety, so it's more efficient on purformance to use StringBuilder.

### Optional\<T\>
- To validate null values.

### junit test
- given -> when -> then

```java
// given
vo setting

// when
SomeVO vo = createSomething();

// then
someVO resertVo = searchSomething(vo.id);
assertThat(vo).isEquals(resertVo)
```


### spring batch
- spring batch with chunk.
  - [chunk is different from paging.](https://jojoldu.tistory.com/331)

```java

    @Value("${base.batch.chunkSize:1000}")
    private int chunkSize;

    @Bean(JOB_NAME)
    public Job job() {
        return jobBuilderFactory.get(JOB_NAME)
                // to make the test easier - it will run even the arguments are the same.
                .incrementer(new RunIdIncrementer()) 
                .start(step())
                .build();
    }

    @Bean(JOB_NAME + "Step01")
    public Step step() {
        return stepBuilderFactory.get(JOB_NAME + "Step01")
                .<BaseDto, BaseDto>chunk(chunkSize)
                .reader(reader())
                .processor(processor())
                .writer(writer())
                .build();
    }


    @Bean(JOB_NAME + "Reader")
    public MyBatisCursorItemReader<BaseDto> reader() {
        return new MyBatisCursorItemReaderBuilder<BaseDto>().sqlSessionFactory(sqlSessionFactory)
                .queryId("mapper.BaseMapper.selectSomething")
                .build();
    }

    @Bean(JOB_NAME + "Processor")
    public ItemProcessor<BaseDto, BaseDto> processor() {
        // even if the result from reader() is a list, it will give one object(BaseDto as a parameter)
        return baseDto -> {
            //do something
            return baseDto;
        };
    }

    @Bean(JOB_NAME + "Writer")
    public MyBatisBatchItemWriter<BaseDto> writer() {
        return new MyBatisBatchItemWriterBuilder<BaseDto>().sqlSessionFactory(sqlSessionFactory)
                .statementId("BaseMapper.updateSomething")
                // parameter type would be automatically set as "BaseDto"
                .build();
    }
```

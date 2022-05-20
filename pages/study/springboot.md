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

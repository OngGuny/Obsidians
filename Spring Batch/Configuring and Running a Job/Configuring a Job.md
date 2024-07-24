```java
@Bean
public Job footballJob(JobRepository jobRepository) {
    return new JobBuilder("footballJob", jobRepository)
                     .start(playerLoad())
                     .next(gameLoad())
                     .next(playerSummarization())
                     .build();
}
```
3개의 *Step* 으로 이루어진 *Job*
*Step* 외에도
*Job*-related-Builder(작업 관련 빌더)에는 병렬화(분할), 선언적 흐름 제어(결정) 및 흐름 정의 외부화(Flow)를 돕는 다른 요소도 포함될 수 있습니다.

1. Job 의 재시작 옵션 설정 가능
2. Job 의 Aspect 설정 가능. before Job, after Job 동작
3. Job 상속 구성 가능. 비슷한 설정을 공유하는 *Job* 일 경우 *부모 Job* 으로 설정 가능. *Child Job* 에서 설정 공유
4. *JobParametersVaildator* 설정 가능
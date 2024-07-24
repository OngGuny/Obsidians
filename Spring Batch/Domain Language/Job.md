. A `Job` is an entity that encapsulates *An entire batch* *process*

....In Spring Batch, a `Job` is simply *a container for `Step` instances*.


```java
@Bean
public Job footballJob(JobRepository jobRepository) {
	return new JobBuilder("footballJob", jobRepository)
	.start(playerLoad() : Step 객체)
	.next(gameLoad())
	.next(playerSummarization())
	.build();
}
```
[[Step]] 객체

## **JobInstance**
: 논리적 작업 실행의 개념
: 로드할 데이터와 전혀 관련이 없다. 데이터는 [[Item related]] 와 관련이 있는것.

새 JobInstance를 사용한다는 것은 "처음부터 시작"을 의미하고, 
기존 인스턴스를 사용한다는 것은 일반적으로 "중단한 부분부터 시작"을 의미합니다.

### Q: 하나의 JobInstance는 다른 JobInstance와 어떻게 구별돼??”

A: 
## **JobParameters**
일괄 작업을 시작하는 데 사용되는 매개변수 세트를 보유


### *Conclusion*
`JobInstance` = `Job` + identifying `JobParameters`

참고 : 꼭 identifying 하지 않아도 되는 파라미터 제출도 허용된다.



## **JobExecution**
 - a single attempt to run a Job.
 -  may end in failure or success,
 - But *`JobInstance`* corresponding to a given execution is not considered to be complete unless the execution completes successfully. 

A *`Job`* defines what a job is and how it is to be executed.
A *`JobInstance`* is a purely organizational object to group executions together, primarily to enable correct restart semantics. 
A **`JobExecution`**, is the primary storage mechanism for what actually happened during a run and contains many more properties that must be controlled and persisted,

# **JobRepository**

 ~~the persistence mechanism for all of the stereotypes mentioned earlier~~
*Job*이 처음 시작되면 `JobRepository`에서 *JobExecution*을 가져옵니다. 또한 during the course of execution, *StepExecution* 및 *JobExecution* 구현은 `JobRepository`에 전달되어 유지됩니다

Java 구성을 사용할 때 `@EnableBatchProcessing` 주석은 자동으로 구성되는 구성 요소 중 하나로 *JobRepository*를 제공합니다.
# **JobLuncher**
*JobLauncher*는 주어진 *JobParameters* 세트를 사용하여 *Job*을 시작하기 위한 간단한 인터페이스를 나타냅니다.
```java
public interface JobLauncher { public JobExecution run(Job job, JobParameters jobParameters) throws JobExecutionAlreadyRunningException, JobRestartException, JobInstanceAlreadyCompleteException, JobParametersInvalidException; }
```
Implementations는 *JobRepository*에서 유효한 *JobExecution*을 얻고 *Job*을 실행할 것으로 예상됩니다.
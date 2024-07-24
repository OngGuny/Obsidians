일괄 작업의 독립적인 순차적 단계를 캡슐화하는 batch job 도메인 개체.
따라서 모든 *Job*은 전적으로 하나 이상의 **Step**으로 구성됩니다.
실제 batch를 정의하고 제어하는 ​​데 필요한 모든 정보가 포함되어 있습니다.



## **StepExecution**\
 - A new `StepExecution` is created each time a `Step` is run, similar to `JobExecution`.
 - Contains 해당 Step and JobExecution reference, transaction-related data ( 커밋/롤백회수, 시작/종료시간 등)
 - Each Step Contains *ExecutionContext*



## **ExecutionContext**
StepExecution 개체 또는 JobExecution 개체로 범위가 지정된 지속 상태를 저장할 수 있는 장소를 제공하기 위해 프레임워크에 의해 유지되고 제어되는 키/값 쌍의 컬렉션을 나타냅니다. (Quartz에 익숙한 사람들에게는 JobData*Map*과 매우 유사합니다.)

- 주어진 시간에 *StepExecution* 당 정확히 하나의 *ExecutionContext*가 존재합니다.
- *ExecutionContext*의 클라이언트는 공유 키스페이스를 생성하므로 주의해야 합니다.
- *JobExecution*당 하나 이상의 *ExecutionContext*가 있고 모든 *StepExecution*당 하나가 있습니다.
  
```java
ExecutionContext ecStep = stepExecution.getExecutionContext(); ExecutionContext ecJob = jobExecution.getExecutionContext();
```
They are two different `ExecutionContexts`.

- *Step*에서 범위가 지정된 것은 단계의 모든 커밋 지점에 저장되는 반면, 
  *Job*으로 범위가 지정된 것은 모든 단계 실행 사이에 저장됩니다.

In the `ExecutionContext`, all non-transient entries must be `Serializable`.
	실행 컨텍스트의 적절한 직렬화는 단계와 작업의 재시작 기능을 뒷받침합니다.
	실행 컨텍스트를 직렬화하지 못하면 상태 지속성 프로세스가 위태로워져 실패한 작업을 제대로 복구하는 것이 불가능해질 수 있습니다.


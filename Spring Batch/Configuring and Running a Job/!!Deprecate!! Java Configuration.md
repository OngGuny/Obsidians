# *아잇18 스프링 부트에서는 배치 5.x 버젼부터  안써서 삭제됨 ㅡㅡ*


batch 를 사용하기 위한 3가지 설정 방법이 있다. 
- @EnableBatchProcessing
- builder 1
- bulder 2


## @EnableBatchProcessing
batch jobs 구축을 위한 기본 구성을 제공합니다.
이 기본 구성 내에서 StepScope 및 JobScope의 인스턴스가 생성되며 자동 연결에 사용할 수 있는 여러 Bean도 생성됩니다.
이녀석은 컨텍스트 내에서 빈으로 제공되는 DataSource 및 PlatformTransactionManager가 필요합니다.

```java
@Configuration 
@EnableBatchProcessing(
		dataSourceRef = "batchDataSource",
		transactionManagerRef = "batchTransactionManager"
		)
public class MyJobConfiguration {
	
	@Bean public DataSource batchDataSource() {
	 return new EmbeddedDatabaseBuilder()
	 .setType(EmbeddedDatabaseType.HSQL)
	 .addScript("/org/springframework/batch/core/schema-hsqldb.sql")
	 .generateUniqueName(true).build();
	  }
	
	@Bean public JdbcTransactionManager 
		batchTransactionManager(DataSource dataSource) {
	  return new JdbcTransactionManager(dataSource);
	}
	
	@Bean public Job job(JobRepository jobRepository) {
	  return new JobBuilder("myJob", jobRepository)
	   //define job flow as needed 
		.build(); 
	} 
}
```


다른 방법으로는 
extends DefaultBatchConfiguration  을 하는 것으로 가능. 

```java
@Configuration 
class MyJobConfiguration extends DefaultBatchConfiguration { 
	
	@Bean
	 public Job job(JobRepository jobRepository) {
	  return new JobBuilder("job", jobRepository)
	   // define job flow as needed 
		  .build(); 
	  } 
	
	@Override
	protected Charset getCharset() { 
		return StandardCharsets.ISO_8859_1; 
	}
}

```

커스텀 할 설정은 Override

근데 public 이 아니넹..? 
지금 만들어진 batch 에는 위 설정이 안보이네. 
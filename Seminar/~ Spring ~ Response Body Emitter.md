---
tags:
  - 세미나주제
---
(Interface that Implemented by Sse Emitter)

# **정의** 
(A controller method)  *return value type*
 for asynchronous request processing
where one or more objects are written to the response.

= 비동기 요청에 대한 하나 이상의 작성된 응답을 처리하기 위한 컨트롤러의 리턴 타입

 `org.springframework.web.context.request.async.DeferredResult` 는 
하나의 결과를 생성하는 데 사용 되는 반면,
**ResponseBodyEmitter**는  여러 객체를 보낼 수 있으며 각 객체는 호환되는 `org.springframework.http.converter.HttpMessageConverter` 로 작성됩니다


# 이 클래스의 목적?
: helps to collect and send the response to the client

비동기 응답을 처리하고
대용량 데이터 처리나
실시간 데이터 업데이트를 하거나 

준비된 데이터를 조금씩 전송함으로써 서버의 메모리부담을 줄이고 클라이언트의 대기 시간을 단축하기 위해. 

스프링의 비동기 기능을 활용하여 위의 요구사항을 충족시키기 위해 설계. 

이녀석은 보다 일반적인 비동기 응답 처리를 위한 도구입니다. 







예시코드:
```
 @RequestMapping(value="/stream", method=RequestMethod.GET)
  public ResponseBodyEmitter handle() {
  	   ResponseBodyEmitter emitter = new ResponseBodyEmitter();
  	   // Pass the emitter to another component...
  	   return emitter;
  }
 
  // in another thread
  emitter.send(foo1);
 
  // and again
  emitter.send(foo2);
 
  // and done
  emitter.complete();
```


## 필드

- Long *timeout*
	-? 웹 서비스나 데이터베이스 서버와 같은 원격 자원에 연결할 때, 네트워크 지연이나 서버 문제로 인해 응답이 늦어질 수 있습니다. 이때 timeout을 설정하면 지정된 시간 동안만 기다리고, 그 시간을 초과하면 연결을 중단합니다.
	\<in RBE> Constructor Param: timeout – the timeout value in milliseconds 

- Handler *handler*
- Set\<DataWithMediaType> *earlySendAttempts* 
	= new LinkedHashSet<>(8);
	-? Handler가 초기화되기 전에 전송 데이터를 저장합니다.

- boolean *complete*
	-? Handler 가 초기화되기 전에 완료를 저장합니다.

- Throwable *failure*
	-? Handler 를 초기화하기 전에 오류를 저장합니다.

- boolean *sendFailed*
	-? I/O 오류가 발생한 후에는 `this.completeWithError()` 를 직접 호출하지 않고, 서블릿 컨테이너가 컨테이너 스레드에서 [[AsyncListener#onError]] 를 통해  우리에게 호출하길 기다립니다. 그 시점에서 우리는 completeWithError를 호출합니다. **이 플래그는 I/O 오류의 스레드에서 발생할 수 있는, 예를 들어 애플리케이션의 try-catch 블록에서  나오는 complete 또는 completeWithError에 대한 *추가적인 호출을 무시하는 데 사용*됩니다.**
  
- DefaultCallback *timeoutCallback*
	= new DefaultCallback();  
- ErrorCallback *errorCallback* 
	= new ErrorCallback();  
- DefaultCallback *completionCallback* 
	= new DefaultCallback();

[[CallBack]]

## 생성자
- 기본 생성자
- public ResponseBodyEmitter(Long timeout) 
  -? Params: timeout – the timeout value in milliseconds

## 메소드
- getTimeout()
- synchronized void *initialize*(Handler handler) throws IOException
  -> 응답이 즉시 생성되지 않는 상황에서 비동기 요청 처리를 관리하는 데 설계되었습니다.

- synchronized void *initializeWithError*(Throwable ex)
	-> 메소드는 비동기 요청 처리 중 오류 처리를 위해 설계
	
	- **완료 플래그 설정**: 이 메소드는 `this.complete`를 `true`로 설정합니다. 이것은 응답 처리가 완료되었음을 나타내지만, 오류로 인해 발생했습니다. 이는 더 이상의 데이터를 보내지 말고 응답을 최종화해야 한다는 신호를 주는 것이 중요합니다.
	
	- **예외 객체 할당**: 이 메소드는 `Throwable` 객체 `ex`를 파라미터로 받습니다. 이 객체는 발생한 오류를 나타냅니다. 이것은 `this.failure`에 할당되며, 이는 처리 과정을 종료시킨 예외나 오류를 저장합니다.
	    
	- **초기 전송 시도 지우기**: `this.earlySendAttempts.clear()`가 호출되어 오류가 발생하기 전에 보내려고 했던 누적된 데이터를 비웁니다. 이는 오류가 인지된 후 이 데이터를 보내려는 시도를 방지하기 위해 중요합니다.
	    
	- **오류 콜백 트리거**: 마지막으로, `this.errorCallback.accept(ex)`가 실행됩니다. 이는 제공된 `Throwable`을 사용하여 오류 처리 메커니즘을 트리거합니다. 이는 애플리케이션 또는 클라이언트에게 오류를 알리고 정의된 오류 처리 로직을 실행하기 위해 필수적입니다.

- protected void *extendResponse(ServerHttpResponse outputMessage)* 
  -> 실제 본문이 작성되기 전에 HTTP 응답을 사용자 지정하기 위한 훅

- public void *send(Object object)* throws IOException
  -> 주어진 객체를 응답으로 작성합니다. 

- public synchronized void *send(Object object, @Nullable MediaType mediaType)* throws IOException 
  -> 미디어타입에 대한 힌트를 얻어서 응답을 작성합니다. 

- private void *sendInternal(Object object, @Nullable MediaType mediaType)* throws IOException 
  -> 위의 send 메소드들의 실제 동작입니다. 
  initialize() 에서 받은 handler 가 실제로는 send 메소드로 응답을 보냅니다. 

[[Dispatch in Servlet Context]]
- public synchronized void *complete()*
	-> 이 메소드는 비동기 처리 시나리오에서 요청 처리의 끝을 표시하도록 설계되었습니다. 애플리케이션이 요청 처리를 마치고 응답을 완료할 준비가 되었을 때 사용됩니다.
	
- public synchronized void *completeWithError(Throwable ex)* 
  -> 요청 처리가 오류로 인해 종결될 때 사용됩니다. 이는 애플리케이션이 명시적으로 오류 상태를 요청 처리의 최종 상태로 신호할 수 있게 합니다.
  '오류'가 '구성된 Spring MVC 예외 처리 메커니즘'을 통해 처리되도록 보장합니다.
  
  - 기타
	  - 핸들러에 대한 interface 정의. 
	  - public static class DataWithMediaType 
			     -  **워크플로 통합**: `ResponseBodyEmitter` 워크플로 내에서 `DataWithMediaType` 객체는 메시지 변환기에 의해 처리되기 전에 데이터와 해당 `MediaType`을 보유하는 데 사용됩니다. 이를 통해 `ResponseBodyEmitter`는 비동기 컨텍스트에서 다양한 데이터 유형 및 형식을 원활하게 처리할 수 있습니다.
			     - **`Nullable` MediaType 처리**: `mediaType`이 `null`인 경우, 데이터에 특정 형식이 요청되지 않았음을 나타냅니다. 이러한 경우에는 `ResponseBodyEmitter`가 기본 설정을 사용하거나 요청의 `Accept` 헤더나 데이터 유형 자체에 기반하여 가장 적절한 `MediaType`을 추론할 수 있습니다.
	- private class DefaultCallback implements Runnable
	- private class ErrorCallback implements Consumer\<Throwable>
			  - **에러 처리** : 스프링의 에러처리 로직을 따라가기 위한 콜백함수를 정의해놓았습니다.

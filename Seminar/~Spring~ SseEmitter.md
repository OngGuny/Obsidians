extends [[~ Spring ~ Response Body Emitter]]

# 정의  
A **specialization of ResponseBodyEmitter** *for sending Server-Sent Events* .

## SseEmitter 의 목적?
- **Server-Sent Events 구현을 용이하게 하도록 설계된 Spring의 전문화된 구성 요소**입니다.
- *비동기 처리 기능을 활용하면서* SSE에 필요한 특정 기능을 추가하여 확장하였습니다.
	ResponseBodyEmitter 가 비동기 응답처리를 위한 일반적 프레임워크라면 
	SseEmitter 는 SSE 에 필요한 특정 요구사항과 관련된 기능들을 추가적으로 제공하기 위함입니다.
- 이는 실시간 데이터 스트리밍을 효율적으로 하기 위함입니다.
라이브 알림, 주식가격, 뉴스 피드 등의 시나리오에서 사용됩니다.

- 장기간 연결을 처리하는데 유리합니다.
	연결을 열어두고 타임아웃을 처리하며 재연결에 특화되어있습니다.
	자동 재연결, 응답 추척을 위한 이벤트 ID, 명명된 이벤트 전송 등의 SSE 주요 기능을 지원합니다.

## **메소드**
### 생성자
	- 기본생성자
	- public SseEmitter(Long timeout) 
	  -> call super(timeout)

### 일반 메소드
- @Override 
  protected void *extendResponse*(ServerHttpResponse outputMessage)
     -> 실제 본문이 작성되기 전에 HTTP 응답을 사용자 지정하기 위한 훅
     sseemitter 에서는 header 에 contenttype 을 event-stream 라고 명시해줍니다.
     
- @Override  
  public void *send*(Object object) throws IOException 
	-> 하나의 SSE "데이터" 행으로 포맷된 개체를 전송합니다.
	ex)
	 `SseEmitter emitter = new SseEmitter();
	    `emitter.send(event().data(myObject));`

- @Override  
 public void *send*(Object object, @Nullable MediaType mediaType) throws IOException 
	-> 데이터의 미디어타입을 정의해서 전송합니다. 
ex)   `emitter.send(event().data(myObject,MediaType.APPLICATION_JSON));`

- public void *send*(SseEventBuilder builder) throws IOException 
  -> 주어진 빌더로 준비된 SSE 이벤트를 보냅니다.  이작업은 syncronized 블록처리되어 동기적으로 처리됩니다. 
  ex) 
   `emitter.send(event().name("update").id("1").data(myObject));`

-  public static SseEventBuilder *event*() 
  -> sse 를 위한 event 객체를 만드는 builder 를 가져오는 메소드입니다. 
  이 메소드를 통해 위의 send() 메소드에 필요한 이벤트 객체를 생성 할 수 있습니다.
  
  여기서 생성되는 event 객체는 아래의 정보를 갖고 있습니다.
	  - id
	  - name
	  - reconnectTime
	  - comment
	  - data
	builder 의 주된 역할은 위 정보를 emitter 가 전송 할 수 있도록 Json 형식에 맞게 파싱해 주는 것입니다. 
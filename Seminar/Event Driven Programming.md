EDP:

# ***이벤트***
이벤트는 매우 광범위한 개념.
1. **사용자 인터페이스 이벤트**: 이것은 GUI 프로그래밍에서 가장 흔히 보는 이벤트 유형입니다. 사용자의 마우스 클릭, 키보드 입력, 터치 스크린 상의 제스처와 같은 사용자의 상호작용이 여기에 해당합니다.
    
2. **시스템 이벤트**: 운영 체제나 환경에서 발생하는 이벤트들, 예를 들어 파일 시스템의 변경, 시스템 경고, 프로세스 상태 변화 등이 이에 속합니다.
    
3. **네트워크 이벤트**: 네트워크 상태 변경, 데이터 수신, 연결 요청과 같은 네트워크 관련 이벤트들도 이벤트 주도 프로그래밍에서 중요한 역할을 합니다.
    
4. **타이머 및 스케줄 이벤트**: 특정 시간에 발생하거나, 정해진 시간 간격으로 반복되는 이벤트들입니다. 예를 들어, 일정 시간 간격으로 데이터를 갱신하거나, 특정 시간에 알림을 보내는 작업 등이 여기에 해당합니다.
    
5. **사용자 정의 이벤트**: 프로그래머가 특정 애플리케이션 로직에 따라 정의한 이벤트들입니다. 예를 들어, 특정 조건이 충족되었을 때 발생하는 이벤트 등이 이에 포함됩니다.
    
6. **하드웨어 이벤트**: 센서 데이터 변경, 장치 상태 변화와 같은 하드웨어 관련 이벤트들도 이벤트 주도 프로그래밍에서 다룰 수 있습니다.



# ***주요 객체***

#### 이벤트 리스너 / 핸들러
: 특정 이벤트가 발생 했을 때 실행되는 코드를 갖는 객체

#### 이벤트 객체
: 이벤트에 대한 정보를 담음. 위치, 유형, 발생시간, 관련데이터 등

#### 이벤트 큐
: 발생한 이벤트들을 순서대로 저장하는 역할. 
기본적으로 멀티스레드 환경에서 동작하는 EDP 는 환경에서 여러 이벤트가 동시에 발생 할 수 있다. 이벤트 큐는 이런 동시다발적 이벤트들을 관리하고 순차적으로 처리한다.

#### 이벤트 디스패처
: 이벤트 큐에서 이벤트를 가져와 적절한 핸들러나 리스너에게 전달하는 역할

#### 이벤트 루프
: 프로그램의 메인 루프에서 실행되며 이벤트 큐를 지속적으로 체크하여 새로운 이벤트를 확인하고 처리

-> 디스패처와 루프의 경우에는 저수준에서 활동하는 녀석들이다. 
이런 반복적이고 저수준의 작업을 개발자가 굳이 신경 쓰지 않게 하기 위해 많은 언어나 프레임워크에서는 이를 추상화 해 놓았다. 
~~사실 이런 중요한 부분을 개발자에게 맡긴다는 것 자체가 위험한 일이라서 가져다 쓰라고 한다.
~~
#### 이벤트 소스
: 이벤트를 발생시키는 객체. 이벤트 소스에 대한 리스너를 등록 할 수 있으며 발생 시 리스너에게 알림. 


# *EDP 철학*
: 
#### 시스템 설계 원칙
##### 2. **컴포넌트의 분리**:
    - EDP는 시스템의 컴포넌트들이 직접 호출이 아닌 이벤트를 통해 소통하는 분리된 아키텍처를 장려합니다. 이러한 분리는 컴포넌트들이 독립적으로 개발, 유지 및 교체될 수 있게 함으로써 더 큰 유연성과 확장성을 제공합니다.
##### 4. **모듈성과 확장성**:
    - EDP는 개발자들이 서로 교환 가능한 부분들로 시스템을 만들 수 있도록 모듈성을 지원합니다. 각 부분은 특정 이벤트를 독립적으로 처리할 수 있습니다. 이 모듈성은 새로운 이벤트 유형과 핸들러를 기존 코드에 최소한의 영향으로 추가할 수 있게 하여 확장성을 촉진합니다.

#### 운영 효율성
##### 3. **비동기 처리**:
    - EDP는 자연스럽게 비동기 처리에 적합합니다. 이벤트를 비동기적으로 처리함으로써 시스템은 다른 이벤트를 처리하는 동안 새로운 이벤트에 대해 반응적으로 유지될 수 있습니다. 이는 자바의 GUI 및 네트워크 프로그래밍에서 매우 중요한데, 블로킹 작업으로 인해 애플리케이션이 반응하지 않게 될 수 있기 때문입니다.
      
##### 1. **반응성과 응답성**:
    - EDP의 핵심은 다양한 이벤트에 대해 매우 반응적이고 응답적인 시스템을 구축하는 것입니다. 이러한 응답성은 사용자 또는 시스템 이벤트에 기반한 적시의 피드백이나 행동을 제공함으로써 시스템이 동적이고 상호작용적인 방식으로 동작하도록 보장합니다.
      

#### 품질 및 신뢰성
##### 5. **복잡성 관리의 단순화**:
    - 애플리케이션 로직을 더 작은 이벤트 처리 컴포넌트로 분해함으로써 EDP는 특히 대규모 애플리케이션에서 복잡성을 관리하는 데 도움이 됩니다. 각 컴포넌트는 특정 이벤트를 처리하는 데 집중하므로 코드베이스를 이해하고 유지하기가 더 쉽습니다.
      
##### 6. **강인함과 오류 처리**:
    - EDP는 오류의 영향을 그것을 일으킨 이벤트에 국한시킴으로써 더 강력한 애플리케이션을 허용합니다. 이러한 격리는 오류를 더 효과적으로 포함하고 관리하는 데 도움이 됩니다.




# 백엔드에서의 EDP 
##### 1. **비동기 및 논블로킹 작업**:
    - 백엔드 시스템에서, 특히 I/O 작업(네트워크 요청, 데이터베이스 작업 등)을 처리하는 경우, 비동기 및 논블로킹 처리는 효율성을 위해 필수적입니다. EDP는 이러한 작업을 이벤트 기반 방식으로 처리할 수 있게 하여 처리량을 개선하고 자원 소비를 줄입니다.
#####  2. **마이크로서비스 아키텍처**:
    - EDP는 서로 다른 서비스들이 이벤트를 통해 소통하는 마이크로서비스 아키텍처와 잘 어울립니다. 이를 통해 서비스 간 느슨한 결합을 가능하게 하여 아키텍처를 더 확장 가능하고 탄력적으로 만듭니다.
##### 3. **실시간 데이터 처리**:
    - 백엔드 시스템은 종종 IoT 애플리케이션, 금융 서비스 또는 소셜 미디어 플랫폼과 같은 실시간 데이터 스트림을 처리해야 합니다. EDP는 스트리밍 데이터를 효율적으로 처리하고 반응하는 데 적합합니다.
##### 4. **확장성과 유연성**
    - 백엔드 시스템은 종종 다양한 부하를 처리하기 위해 확장이 필요합니다. EDP의 분리된 성격은 시스템 전체에 영향을 주지 않고 개별 구성 요소의 확장 및 수정을 용이하게 합니다.
##### 5. **복잡한 이벤트 처리(CEP)**:
    - 사기 탐지, 알고리즘 트레이딩, 모니터링 시스템과 같은 분야에서 실시간으로 이벤트 패턴에 반응하는 것이 중요합니다. EDP는 특정 패턴과 일치하는 이벤트 시퀀스에 반응할 수 있도록 하여 CEP를 용이하게 합니다.
##### 6. **분산 시스템**
    - EDP는 네트워크를 통해 구성 요소가 이벤트를 발생시키고 듣는 분산 시스템 개발에 유리합니다. 이 접근 방식은 실패를 처리하고 일관성을 유지할 수 있는 탄력적인 분산 애플리케이션을 만드는 데 기본적입니다.
##### 7. **통합 및 워크플로 자동화**:
    - 백엔드 시스템은 종종 다양한 다른 시스템 및 서비스와 통합됩니다. EDP는 다른 통합 지점을 이벤트로 처리함으로써 통합 과정을 단순화하고 더 원활한 워크플로 자동화를 가능하게 합니다.

# MSA 와의 관계
Micro Service Architecture
: 밀접한 관련, 상호 보완적, 시너지.

**1.** MS의 소통 방식 - *느슨한 결합*
   : 이벤트 기반으로 소통하는 서비스들. -> 각 서비스의 직접적인 의존관계를 줄임
**2.** 급격하게 변동하는 요구사항들 - *확장성과 유연성*
	: 요구사항의 잦은 변동성으로인해, 독립적인 확장을 가능하도록 설계함.
	->  개별 서비스가 이벤트 부하에 따라 다른 서비스에 영향을 주지 않도록 하면서 확장도 가능하게
**3.** 예외의 격리, 탄력적 서비스 - *탄력성과 오류 허용성*
	: 특정 서비스의 예외가 전체 시스템을 멈추지 않도록 설계해야함. EDP 는 이를 방지하는데 도움을 줌
**4.** MS의 독립적인 운영에 따른 병목 현상 처리 - *비동기 통신*
	: 서비스가 응답을 기다리지 않고 독립적으로 운영되게 해야함. EDP 는 본질적으로 비동기 처리를 지원함으로 적합.
**5.** MSA 일반 - *분산 시스템*
	: 분산된 구성 요소 간의 통신과 동기화를 효과적으로 처리하는데 EDP 가 용이함.
**6.** Domain Driven Development - *DDD*
	: MSA 에서 자주 사용되는 DDD 원칙과 잘 어울림.  시스템의 상태 변경을 포착하는게 DDD의 핵심. 이는 EDP 와 매칭
**7.** 하나의 워크플로우, 다양한 서비스 - *통합 및 워크플로 자동화*
	: MS Eco System 에서는 다양한 서비스가 함께 동작해야 할 수 도 있다. 서비스가 다른 서비스를 트리거하는데 이벤트 사용
**8.** 서비스 간의 상태 및 데이터 일관성 관리 - *이벤트 소싱 & CQRS*
	: Event Sourcing  & [[CQRS]] 패턴 보완.  이벤트는 이런 패턴들을 보완하는데 사용됨. 

=> 요약하자면 EDP 와 MSA는 상호작용적. 





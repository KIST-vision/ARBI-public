# ARBI(Artificial Robot Brain Intelligence) 프레임워크
ARBI 프레임워크는 로봇의 지능적 기능 컴포넌트에 대한 통합을 지원하는 에이전트 기반 통신 지원 체계이다.

로봇이 지능적으로 의사결정을 수행하고 작업 계획을 수립하기 위해서는 작업 계획을 수립하는 기능이나 환경정보를 수집하고 가공하는 기능, 나아가 외부와 연계하기 위한 인터페이스 모듈 등 각각의 기능이 독립적으로 구성되어 독자적인 라이프사이클을 가지는 것이 유리하다. 또한 지능적 의사결정을 위한 추론, 작업계획 수립 등의 과정을 위하여 지식정보를 활용하여 데이터를 기술하는 어휘나 관계를 설정하는 방법에 있어 JSON 이나 XML과 같은 일반적인 언어로는 표현력에 한계가 있어 별도의 프로토콜을 활용하여야 한다.

ARBI 프레임워크는 지능 컴포넌트의 독자적인 라이프사이클을 보장하고 지식정보를 기반으로 Event-Driven 모델을 보장할 수 있는 ArbiAgent 인터페이스를 제공한다. 또한 BDI 아키텍처 기반 작업 계획 수립과 추론을 수행하기에 최적화 할 수 있도록 로봇과 주변 센서를 통해 수집한 데이터나 자체적인 추론의 결과 만들어진 상황정보를 저장하고 이벤트를 생성하기 위한 인터페이스를 지닌 LTM(Long Term Memory)을 포함하고 있다.

## GL(Generalized List)
각각의 Agent들이 상호 통신하는 데에 있어 약속된 프로토콜을 일관되게 사용하기 위해서는 지식기반 통신 언어가 필수적이다. GL은 Nested 표현을 지원하는 Predicate 표현 방법의 한가지로, 지식을 간단하면서도 수준 높은 표현력을 제공하는 특징을 가진다. 또한 프레임워크는 라이브러리를 통해 쉽게 데이터를 조작할 수 있도록 기능을 제공한다.

## ArbiAgent 인터페이스
ArbiAgent 인터페이스는 각각의 지능 컴포넌트가 독자적인 라이프사이클을 보장하기 위하여 비동기 메시지 기반 통신을 지원하는 인터페이스이다.
ArbiAgent의 인터페이스는 지식기반 프로토콜을 지원하기 위해서 Multi-Agent 시스템의 표준단체인 FIPA의 Communication 표준을 바탕으로 지능 컴포넌트로서 필수적인 부분을 일부 준용하여 구성되어 있다. ArbiAgent의 인터페이스는 다음과 같다.

## Send Interface
|Method|Argument|Description|
|---|---|---|
|request<td rowspan=3>String receiver : 수신자<br>String data : 메시지 내용|다른 Agent에게 새로운 Goal을 요청하는 프로토콜.|
|query|다른 Agent에게 즉시 response 가능한 새로운 Goal을 요청하는 프로토콜.|
|send|다른 Agent에게 Goal이나 지식, 정보를 전달하는 프로토콜.|
|response|X|request나 query 등의 답이 필요한 프로토콜의 응답 프로토콜. 직접 response 메시지를 보낼 수 없으며, 메시지 handler에 return시 자동으로 response 메시지가 전송된다.|
|subscribe|String receiver : 수신자<br>String subscribe : subscribe 규칙|타 에이전트에게 특정 조건이 만족되었을 때 정해진 GL을 통해 알림(notify)을 줄 수 있도록 요청하는 subscribe문. 수행 결과로 Subscription ID가 반환되어 unsubscribe시 사용할 수 있다.|
|notify|String receiver : 수신자<br>String data : 메시지 내용|Subscribe문에 의해 특정 Rule을 만족하여 subscription의 대상에게 알림을 보낼 때 사용하는 프로토콜.|
|unsubscribe|String receiver : 수신자<br>String subscriptionID : 대상 Subscription ID|subscribe 프로토콜을 통해 요청한 Subscription을 해제하기 위한 프로토콜.|

## Receive Interface
|Method|Argument|Description|
|---|---|---|
|onRequest<td rowspan=4>String sender : 송신자<br>String data : 메시지 내용<td rowspan=2>다른 Agent로부터 받은 새로운 요청에 대한 Handler 메소드.<br>return되는 String은 자동으로 sender에게 response 메시지로 전송된다.|
|onQuery|
|onData|다른 Agent로부터 받은 Data에 대한 Handler 메소드. <br>response가 발생하지 않는다.|
|onNotify|Subscribe Rule에 따라 받은 Notify에 대한 Handler 메소드.<br>response가 발생하지 않는다.|

## LTM(Long Term Memory)
로봇이 지능적인 의사결정을 수행하기 위해서는 각각의 기능 컴포넌트가 상호간에 데이터를 교환하는 것 보다 환경에 대한 정보와 이를 통해 추론한 상황 정보 등이 통합적으로 관리하는 것이 효율적이다. 하지만 통합된 데이터 저장소를 직접적으로 polling 하는 것은 추가적인 병목을 볼러올 수 있는데, LTM은 이를 위해 subscribe/notification 프로토콜을 지원하여 특정룰을 만족하는 이벤트를 에이전트가 원하는 형태의 메시지로 가공하여 비동기적으로 제공할 수 있는 방법을 제공한다. 나아가 지속적으로 제공될 필요가 있는 Streaming 등의 방법을 제공하여 실시간으로 인식된 환경에 대해 반응하거나 새로운 상황에 대한 추론을 가능하도록 하고 있다.

![LTM Interface]("./LTM.png")

LTM은 로봇이 실시간으로 데이터를 저장, 가공하면서 동시에 불시의 오류 등에 대해 데이터를 보존하기 위하여 In-Memory 데이터베이스인 Redis를 활용하고 있다. Redis에 대한 자세한 정보는 다음의 링크를 통해 확인할 수 있다.(https://redis.io/)
LTM은 데이터를 교환하기 위한 인터페이스로서 DataSource 인터페이스를 제공하고 있다.

DataSource 인터페이스는 다음과 같다.

### Send
|Method|Argument|Description|
|---|---|---|
|Post<td rowspan=2>String fact : LTM에 송신할 데이터|Post는 LTM에 데이터를 송신해 Subscription Rule에 의한 Notify 이벤트를 발생시키지만 그 값이 저장되지 않는 프로토콜이다.|
|Assert|Assert는 LTM에 데이터를 송신하는 프로토콜이다.|
|retract<td rowspan=3>String fact : LTM에 질의할 Fact|Retract는 LTM에 저장된 데이터를 삭제하는 프로토콜이다.|
|update|LTM 내의 데이터를 갱신할 때 사용하는 프로토콜이다.|
|match|LTM 내의 데이터를 수신할 때 사용하는 프로토콜이다. 전체 GL이 아닌 질의문의 변수에 해당되는 Binding이 반환된다.|
|subscribe|String rule : Subscription Rule|LTM에 특정 조건이 만족될 경우 지정한 Notify를 요청하는 프로토콜이다. Subscription ID가 반환된다.|
|unsubscribe|String id : Subscription ID|subscribe를 통해 생성된 Subscription을 해제하는 프로토콜이다.|


### Receive
|Method|Argument|Description|
|---|---|---|
|onNotify|String content : Notify 내용|Subscription Rule에 따른 Notify이다.|

LTM Monitor
ARBI Framework는 LTM을 통해서 데이터를 집적, 교환하는 방식을 제공한다. 따라서 Agent간의 요청 뿐만 아니라 LTM에 저장된 데이터의 변화가 서비스의 동작을 제어하는 중요한 요소이다. LTM Monitor는 현재 LTM에 저장된 데이터를 확인할 수 있는 UI를 제공하며, 데이터의 Filetering을 통해 필요한 데이터만 집중하여 확인하는 등 다양한 모니터링 기능을 사용할 수 있다.

![LTM Monitor]("./LTMMonitor.png")

로봇 기능을 통합하기 위해서는 우선적으로 각 Agent를 구현하여야 한다. 통합 작업 이전에 상호간의 Interaction을 검증하고 기능 수행을 확인하기 위해서는 다른 Agent의 역할을 대행할 Dummy Agent가 필요한데, ARBI Monitor는 자체적으로 이러한 기능을 수행할 수 있는 Dumy Agent Interface를 제공하고 있다. 이는 임의의 프로토콜을 특정 Agent에게 전달하는 기능을 수행할 뿐 아니라, 상호간의 프로토콜을 Agent 중심적으로 실시간 확인할 수 있는 모니터링 기능을 제공하여 기능 컴포넌트의 구현을 지원한다.

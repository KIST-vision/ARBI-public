# ContextManager
## 개요
로봇 지능 프레임워크는 매우 다양한 지능적 능력들이 요구되지만, 그 중에서도 가장 필수적인 능력 중 하나가 동적으로 변화하는 주변 환경에 대한 올바른 상황 인식과 상황 이해 능력이다. 이를 고려하여 효과적인 동적 상황 관리 및 시간-공간-사용자 복합 상황 추론을 제공하는 것이 바로 ContextManager이다.

ContextManger는 논리형 고급 프로그래밍 언어인 Prolog를 기반으로 상황 정보를 표현하고 다양한 추론 규칙들을 검증한다. PerceptionManger로부터 실시간으로 수집되는 환경, 물체, 로봇, 사용자 등의 인식 데이터를 토대로 저수준 상황 정보를 구축하고 이를 바탕으로 사무 환경, 가정 환경, 교육 환경 등에서 서비스 제공을 위해 요구되는 고수준 상황 정보들을 추론한다. 추론된 고수준 상황 정보는 TaskManager의 요청에 따라 제공된다.

## 기능
ContextManger의 기능을 소개하기 위한 상세 구조도는 아래 그림과 같다.
(./ContextManager_Architecture.png)
ContextManger의 내부에는 온톨로지 지식을 저장하는 온톨로지 메모리(context Ontology)와 동적 상황 지식을 저장 하는 작업 메모리(working memory)가 있다. 그리고 온톨로지 메모리와 작업 메모리를 참조하면서 동작하는 주요 처리 모듈로는 인식 처리기(perception handler), 시-공간 복합 추론기 (hybrid reasoner), 질의 처리기(query processor), 지식 인터페이스(knowledge interface) 등이 있으며, 이들은 모두 Prolog 규칙들과 Java 코드들로 구현된다. 인식 처리기는 다양한 센서들이나 인식 시스템들로부터 인식 데이터를 입력받아 저수준의 상황 정보를 생성하고 이것을 작업 메모리에 저장하는 역할을 수행하며, 지식 인터페이스는 외부로부터 받은 상황 질의(context query)를 분석하여 질의에 부합하는 상황 정보를 작업 메모리에서 찾아 질의 결과로 보내주는 역할을 수행한다. 때로는 지식 인터페이스가 비-동기 방식의 상황 지식 전달 서비스도 제공하는데, 이때는 상황 질의로부터 외부에서 통보 받기 원하는 상황 조건을 추출한 다음, 이 조건을 만족하는 상황 정보가 등록되는지 작업 메모리를 지속적으로 모니터링(monitoring)한다. 그리고 만약 해당 조건을 만족하는 저수준 혹은 고수준의 상황 정보가 발생하면, 즉각적으로 상황 지식을 외부에 통보(notify)하는 역할을 지식 인터페이스가 수행한다. 한편, 시-공간 복합 추론기는 작업 메모리에 등록되는 저수준의 상황 정보들에 다양한 추론 규칙들을 적용함으로써 고수준의 상황 정보를 추론해내는 역할을 수행한다. 하지만 본 ContextManger에서는 불필요한 추론을 최대한 줄이고 계산 효율성을 높이기 위해, 상황 질의를 통해 외부에서 고수준의 상황 정보를 요청할 때에만 해당 정보를 얻기 위한 후향 추론(on-demand, backward reasoning)을 수행하도록 설계하였다. 이렇게 시-공간 복합 추론기를 설계함으로써, 서비스 로봇의 행동 및 의사 결정에 당장 필요하지도 않은 관심 밖의 많은 물체들의 작은 변화에도 민감하게 반응해 이들과 연관된 모든 고수준의 상황 정보들을 추론해내는 낭비적인 계산을 피할 수 있다.

## 모델
ContextManger는 모든 서비스에서 공통적으로 요구되는 핵심적인 상황 정보를 표현하기 위한 상황 서술자들과 특정 서비스에 특화된 상황 정보를 표현하기 위한 상황 서술자들을 정의해야 한다. 또한, 각각의 상황 서술자들을 검증하기 위해 논리형 고급 프로그래밍 언어인 Prolog로 추론 규칙들을 작성하여 패키지 형태로 저장 및 관리한다.
상황 서술자 패키지는 아래와 그림과 같이 구성되어 있다.
(./ContextManager_Attribute.png)
상황 서술자는 크게 속성 서술자와 관계 서술자로 나뉜다. 속성 서술자는 주로 저수준 상황 정보를 표현하기 위한 서술자들로써 주로 개별 물체, 로봇, 사람 등의 상태를 나타낸다. 관계 서술자는 주로 고수준 상황 정보를 표현하기 위한 서술자들로써 주로 서로 다른 물체, 로봇, 사람들 간의 시간-공간-사용자 복합 관계를 나타낸다.
대표적인 속성 서술자와 관계 서술자들의 목록은 아래 표와 같다.

속성 서술자(attribute predicate)

Environment|Object|Human|Robot|Event
---|---|---|---|---
mainColorOfObject, boundingBoxSize, roomnumber, …|mainColorOfObject, boundingBoxSize, objectShapeType, stateOfObject, …|hasSex, hasAge, belongto, nameString, hasPosition, …|currentJointAngle, currentJointTorque, hasCamera, hasHand, robotHandType, …|startTime, endTime, duration, …

관계 서술자(relationship predicate)

Subject\Object|Environment|Object|Human|Robot|Event
---|---|---|---|---|---
Environment|locatedAtRelative, connectedTo, …|in-ContGeneric, outSideOf, in-CenterOf, …|in-ContGeneric, outSideOf, in-CenterOf, …|in-ContGeneric, outSideOf, in-CenterOf, …|typePrimaryFunction-containerUsed, …
Object|storedAtPlace, inRoom,  near, far, …|locatedAtRelative, piled, madeOf, …|locatedAtRelative, createdBy, …|locatedAtRelative, createdBy, …|typePrimaryFunction-itemUsedFor, ...
Human|inRoom, near, far, …|locatedAtRelative, graspedBy, isHolding, …|aboveOf, belowOf, toTheLeftOf, friend, ...|aboveOf, belowOf, toTheLeftOf, toTheRightOf, ...|do, play, act, host, …
Robot|inRoom, near, far, …|locatedAtRelative, graspedBy, isHolding, …|locatedAtRelative, performedBy, …|locatedAtRelative, sub_component, succeeding_link, …|do, play, act, host, …
Event|objectActedOn, deviseUsed, …|objectActedOn, deviseUsed, …|agentActedOn, doneBy, …|agentActedOn, doneBy, …|processStarted, processStopped, postEvent, ...

## 프로토콜
ContextManger는 TaskManger의 상황 정보 조회를 위하여 동기 또는 비동기 형식의 메시지 전송 방식을 지원한다. ContextManager가 사용 가능한 메시지 형식은 다음과 같다. 

Sender|Type|Description|Receiver|GL|Argument
---|---|---|---|---|---
TaskManager|Request|상황 정보 조회/추론 요청|ContextManager|(context($predicate $subject $object $timeOperator $time))|$predicate: 상황 서술자 <br>$subject: 서술자의 주어 <br>$object: 서술자의 목적어 <br>$timeOperator: 상황 서술자의 유효 시간 추론 서술자, BEFORE, AFTER 등 <br>$time: 상황 서술자의 유효 시간
TaskManager|Subscribe|상황 정보 구독 요청|ContextManager|(subscribe $id (rule (fact ($predicate1 $subject1 $object1))--> (notify ($predicate2 $subject2 $object2))))|$id: 구독 등록 식별자 <br>$predicate1: 구독 상황 서술자 <br>$subject1: 구독 상황 서술자의 주어 <br>$object1: 구독 상황 서술자의 목적어 <br>$predicate2: 통보 상황 서술자 <br>$subject2: 통보 상황 서술자의 주어 <br>$object2: 통보 상황 서술자의 목적어
TaskManager|Unsubscribe|상황 정보 구독 취소 요청|ContextManager|(unsubscribe $id)|$id: 구독 등록 식별자

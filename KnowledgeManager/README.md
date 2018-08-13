# KnowledgeManager
![ARBI Framework 내의 KnowledgeManager 도식](./km_structure.png)

## 개요
KnowledgeManager는 사용자, 인지, 환경, 행위, 로봇 프로파일 등을 표현하는 로봇 온톨로지를 기반으로 로봇 서비스 수행의 전반적인 지식을 제공하는 모듈이다.



## 기능
* 로봇 온톨로지의 효율적인 관리
* 타 agent가 요청하는 지식 추론 및 지식 제공



## 프로토콜

타 agent로부터 오는 메시지를 처리하기 위한 프로토콜은 다음과 같다.

|(|Operation|Target|Expression 1|Expression 2|Expression 3|Expression 4|Expression 5|)|Description|
|-|-|-|-|-|-|-|-|-|-|
|(|create|Class|$superClass|$class||||)|온톨로지에 새로운 클래스 생성|
|(|create|Individual|$typeClass|$individual||||)|온톨로지에 새로운 인디비주얼 생성|
|(|create|Property|$propertyType|$superProperty|$property|$domain|$range|)|온톨로지에 새로운 프로퍼티 생성|
|(|create|Relation|$subject|(Predicate $p1 $o1)|(Predicate $p2 $o2)|...||)|온톨로지에 새로운 관계 트리플(S - P - O) 생성|
|(|query|Relation|*$subject*|*$property*|*$object*|$result||)|$s, $p, $o 중 1개 또는 2개의 정보 필요|
|(|query|MultiRelations|( tripleSet|(triple $s $p $o)|... )|$result||)|조인되어있는 질의를 처리해주는 프로토콜|
|(|query|OnRestriction|$targetClass|$restrictionProperty|$restrictionObject|$result||)||
|(|delete|Class|$class|||||)|삭제하려는 클래스에 하위클래스가 존재하면 삭제불가 메시지 반환|
|(|delete|Individual|$individual|||||)||
|(|delete|Property|$property|||||)|삭제하려는 프로퍼티의 usage가 존재하면 삭제불가 메시지 반환|
|(|delete|Relation|$subject|$property|$object|||)||
|(|request|PredicateAnchoring|$subject|$property|$object|||)|자연어 용어를 온톨로지 용어로 매핑|
|(|request|GuideAction|$destination|$result||||)|목적지에 대한 안내 행동 요청|
|(|request|Path|$type|(currentPoint $x $y $z)|$departure|$destination||)|현재좌표로부터 목적지까지의 이동경로|



## Example

```java
import kr.ac.hanyang.agent.KmAgent;
import kr.ac.uos.ai.arbi.agent.ArbiAgentExecutor;


public class TestClass {
	public static final String JMS_BROKER_URL = "tcp://127.0.0.1:61616";
	public static final String TASKMANAGER_ADDRESS = "agent://www.arbi.com/taskManager";
	public static final String KNOWLEDGEMANAGER_ADDRESS = "agent://www.arbi.com/knowledgeManager";
	
	public static void main(String[] args) {
		KmAgent km = new KmAgent();
		ArbiAgentExecutor.execute(JMS_BROKER_URL, KNOWLEDGEMANAGER_ADDRESS, km);
	}

}
```



## 온톨로지 Ontology

* isro.owl
* isro_service.owl

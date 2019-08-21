# KnowledgeManager
![ARBI Framework 내의 KnowledgeManager 도식](./km_structure.png)

## 개요
**지식 관리자**(Knowledge Manager, __*KM*__)는 범용적인 지식이 포함되어있는 온톨로지와 더불어 사용자(User), 인지(Perception), 환경(Environment), 행위(Action), 로봇 프로파일(Robot) 등을 포함하는 로봇 서비스에 특화된 온톨로지(Ontology)를 기반으로 지능적인 로봇 서비스 수행에 필요한 지식(Knowledge)을 관리하고 제공하는 모듈이다.

**지식 관리자**는 로봇 서비스 수행에 필요한 단순 지식 정보 제공의 기능뿐만 아니라, 필요에 따라 지식 간 관계 정보(Relation)를 이용하여 새로운 지식을 생성하는 추론(Inference) 기능을 갖추고 있다. 

## 기능
* 지능 로봇 서비스와 관련된 온톨로지의 효율적인 관리.
* 타 모듈에서 요청하는 지식 제공 및 지식 추론을 통한 새로운 지식 생성.


## Knowledge Manager Action

지식 관리자는 기본적으로 온톨로지 데이터에 대한 CRUD operation을 제공하며, 지식 추론을 통한 특별한 기능도 제공한다. 타 Agent로부터의 지식 관리자에게 지식 처리를 요청하는 경우, 지식 관리자가 수행할 수 있는 Action 은 아래와 같다. 

### 지식 생성 CREATE
|GL protocol|description|
|-|-|
|(createClass $superClass $newClassID)|새로운 클래스 생성|
|(createProperty $propertyType $superProperty $propertyID $domain $range)|새로운 프로퍼티 생성|
|(createIndividual $typeClass $individualID)|새로운 인디비주얼 생성|
|(createRelation $subject (predicate $p $o) (predicate $p $o) ... (predicate $p $o))|새로운 관계 트리플(S - P - O) 생성|


#### createClass GL 문 예시
* full IRI expression
```
(createClass "http://robot-arbi.kr/ontologies/isro_medical.owl#MentalDisease" "http://robot-arbi.kr/ontologies/isro_medical.owl#Schizophrenia")
```
* prefixed name expression
```
(createClass "isro_medical:MentalDisease" "isro_medical:Schizophrenia")
```

#### createProperty GL 문 예시
* full IRI expression
```
(createProperty "ObjectProperty" "http://robot-arbi.kr/ontologies/isro_medical.owl#getDisease" "http://robot-arbi.kr/ontologies/isro_medical.owl#getPhysicalDisease" "http://knowrob.org/kb/knowrob.owl#Person" "http://robot-arbi.kr/ontologies/isro_medical.owl#PhysicalDisease")

(createProperty "DatatypeProperty" "http://www.w3.org/2002/07/owl#topDataProperty" "http://robot-arbi.kr/ontologies/isro_social.owl#testDatatypeProperty" $domain $range)
```
* prefixed name expression
```
(createProperty "ObjectProperty" "isro_social:getDisease" "isro_medical:getPhysicalDisease" "knowrob:Person" "isro_medical:PhysicalDisease")

(createProperty "DatatypeProperty" "owl:topDataProperty" "isro_social:testDatatypeProperty" $domain $range)
```

#### createIndividual GL 문 예시
* full IRI expression
```
(createIndividual "http://robot-arbi.kr/ontologies/isro_medical.owl#Schizophrenia" "http://robot-arbi.kr/ontologies/isro_medical.owl#_Schizophrenia")
```
* prefixed name expression
```
(createIndividual "isro_medical:Schizophrenia" "isro_medical:_Schizophrenia")
```

#### createRelation GL 문 예시
* full IRI expression
```
(createRelation "http://robot-arbi.kr/ontologies/isro.owl#FaceRecognition" (predicate "http://robot-arbi.kr/ontologies/isro_social.owl#faceID" "001"))

(createRelation "http://robot-arbi.kr/ontologies/complexService.owl#Person001" (predicate "http://robot-arbi.kr/ontologies/isro_medical.owl#getDisease" "http://robot-arbi.kr/ontologies/isro_medical.owl#_Schizophrenia"))
```
* prefixed name expression
```
(createRelation "isro:FaceRecognition" (predicate "isro_social:faceID" "001"))

(createRelation "service:Person001" (predicate "isro_medical:getDisease" "isro_medical:_Schizophrenia"))
```

### 지식 삭제 DELETE
|GL protocol|description|
|-|-|
|(deleteClass $superClass $targetClass)|기존 클래스 삭제|
|(deleteProperty $propertyType $superProperty $propertyID $domain $range)|기존 프로퍼티 삭제|
|(deleteIndividual $typeClass $individualID)|기존 인디비주얼 삭제|
|(deleteRelation $subject $predicate $object)|기존 관계 트리플 삭제|

#### deleteClass GL 문 예시
* full IRI expression
```
(deleteClass "http://robot-arbi.kr/ontologies/isro_medical.owl#MentalDisease" "http://robot-arbi.kr/ontologies/isro_medical.owl#Schizophrenia")
```
* prefixed name expression
```
(deleteClass "isro_medical:MentalDisease" "isro_medical:Schizophrenia")
```

#### deleteProperty GL 문 예시
* full IRI expression
```
(deleteProperty "ObjectProperty" "http://robot-arbi.kr/ontologies/isro_medical.owl#getDisease" "http://robot-arbi.kr/ontologies/isro_medical.owl#getPhysicalDisease" "http://knowrob.org/kb/knowrob.owl#Person" "http://robot-arbi.kr/ontologies/isro_medical.owl#PhysicalDisease")

(deleteProperty "DatatypeProperty" "http://www.w3.org/2002/07/owl#topDataProperty" "http://robot-arbi.kr/ontologies/isro_social.owl#testDatatypeProperty" $domain $range)
```
* prefixed name expression
```
(deleteProperty "ObjectProperty" "isro_medical:getDisease" "isro_medical:getPhysicalDisease" "knowrob:Person" "isro_medical:PhysicalDisease")

(deleteProperty "DatatypeProperty" "owl:topDataProperty" "isro_social:testDatatypeProperty" $domain $range)
```

#### deleteIndividual GL 문 예시
* full IRI expression
```
(deleteIndividual "http://robot-arbi.kr/ontologies/isro_medical.owl#Schizophrenia" "http://robot-arbi.kr/ontologies/isro_medical.owl#_Schizophrenia")
```
* prefixed name expression
```
(deleteIndividual "isro_medical:Schizophrenia" "isro_medical:_Schizophrenia")
```

#### deleteRelation GL 문 예시
* full IRI expression
```
(deleteRelation "http://robot-arbi.kr/ontologies/complexService.owl#Person001" "http://robot-arbi.kr/ontologies/isro_medical.owl#getDisease" "http://robot-arbi.kr/ontologies/isro_medical.owl#_Schizophrenia")
```
* prefixed name expression
```
(deleteRelation "service:Person001" "isro_medical:getDisease" "isro_medical:_Schizophrenia")
```

### 지식 질의 READ
|GL protocol|description|
|-|-|
|(queryRelation *$s* *$p* *$o* $result)|$s $p $o 중 1개 또는 2개를 채워 질의하면 비어있는 인자에 대한 질의 결과를 $result 에 바인딩하여 반환|
|(queryMultiRelation (tripleSet (triple $s $p $o) (triple $s $p $o) ... ) $result)|join 되어있는 질의 결과를 $result 에 바인딩하여 반환|
|(queryCloudRelation $s $p $o $result)|클라우드 정보에 접근하여 질의 결과 반환|

#### queryRelation GL 문 예시
* full IRI expression
```
(queryRelation "http://robot-arbi.kr/ontologies/complexService.owl#Person001" $p $o $result)
```
* prefixed name expression
```
(queryRelation "service:Person001" $p $o $result)
```

#### queryMultiRelation GL 문 예시
* full IRI expression
```
(queryMultiRelation (tripleSet (triple $medicalRecord "http://robot-arbi.kr/ontologies/isro.owl#targetPerson" "http://robot-arbi.kr/ontologies/complexService.owl#Person001") (triple $medicalRecord "http://robot-arbi.kr/ontologies/isro.owl#targetDisease" $disease) (triple $medicalRecord "http://knowrob.org/kb/knowrob.owl#startTime" "http://robot-arbi.kr/ontologies/complexService.owl#TimePoint_123456789")) $result)
```
* prefixed name expression
```
(queryMultiRelation (tripleSet (triple $medicalRecord "isro:targetPerson" "service:Person001") (triple $medicalRecord "isro:targetDisease" $disease) (triple $medicalRecord "knowrob:startTime" "service:TimePoint_123456789")) $result)
```

#### queryCloudRelation GL 문 예시

```
(queryCloudRelation "추석" "일시" $o $result)
```


### 지식 추론 Inference
|GL protocol|description|
|-|-|
|(requestPath $type (currentPoint $x $y $z) $departure $destination)|현재 좌표로부터 목적지까지의 최단 이동경로|
|(requestRecommendation $userID $targetAction $result)|행위에 따라 사용자에게 적절한 object 추천|

#### requestPath GL 문 예시
* full IRI expression
```
(requestPath "coordinate" (currentPoint 2.0 3.0 0.0) "http://robot-arbi.kr/ontologies/isro_map.owl#OfficeRoom001" "http://robot-arbi.kr/ontologies/isro_map.owl#OfficeRoom002")

(requestPath "name" (currentPoint 2.0 3.0 0.0) "http://robot-arbi.kr/ontologies/isro_map.owl#OfficeRoom001" "http://robot-arbi.kr/ontologies/isro_map.owl#OfficeRoom002")

(requestPath "action" (currentPoint 5.25 0.8056 0.0) "http://robot-arbi.kr/ontologies/isro_map.owl#ReceptionRoom001" "http://robot-arbi.kr/ontologies/inro_map.owl#HospitalRoom001")
```
* prefixed name expression
```
(requestPath "coordinate" (currentPoint 2.0 3.0 0.0) "isro_map:OfficeRoom001" "isro_map:OfficeRoom002")

(requestPath "name" (currentPoint 2.0 3.0 0.0) "isro_map:OfficeRoom001" "isro_map:OfficeRoom002")

(requestPath "action" (currentPoint 5.25 0.8056 0.0) "isro_map:ReceptionRoom001" "isro_map:HospitalRoom001")
```

#### requestRecommendation GL 문 예시
* full IRI expression
```
(requestRecommendation "http://robot-arbi.kr/ontologies/complexService.owl#Person001" "http://knowrob.org/kb/knowrob.owl#Drink" $result)
```
* prefixed name expression
```
(requestRecommendation "service:Person001" "knowrob:Drink" $result)
```

## Ontology list

* isro.owl
* isro_social.owl
* isro_medical.owl
* isro_vs.owl
* isro_SD.owl


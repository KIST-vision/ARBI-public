VirtualDeviceManager
===========
![Arbi Framework](./image01.png)

# 개요
버츄어 디바이스는 지능체계가 IoT 기기와 같은 네트워크상에 존재하는 센서나 액추에이터의 기능을 활용할 수 있도록 인터페이스 역할을 수행하는 에이전트이며, 버츄어 디바이스 매니저는 이러한 버츄어 디바이스들을 관리하는 에이전트이다.


지능체계 내의 다른 에이전트들이 IoT 기기의 서비스를 이용해야 할 때 버츄어 디바이스를 사용하면 인터페이스는 IoT 서비스를 사용하기 위해 사전에 정의된 IoT 모델을 참조하여 질의된 결과를 바탕으로 필요한 인증 절차나 데이터 변환 과정을 대행해주고, 서비스의 결과값을 파싱하여 GeneralizedList의 형태로 결과를 반환하게 된다.

# 설치 환경 및 설치 방법

## 시스템 요구사항

  * Hardware : 40MB 이상의 저장 용량
  * Environment : [JRE/JDK 1.8버전](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

## 설치 방법

  * [JRE/JDK 1.8버전](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 설치 진행
  * [공개 깃허브](https://github.com/KIST-vision/ARBI-public/edit/master/VirtualDevice)에서 Release 탭 내에서 다운로드 가능(예정)

# 실행 방법 및 설정 방법

  * (선택) 압축 해제된 Virtual Device 폴더 내의 Configuration.xml 파일 텍스트 에디터(메모장, Visual Studio Code, notepad++ 등)로 수정
    * [설정 방법](#설정-방법)에 관해서는 항목 참조. 기본 설정 및 예시 파일은 압축파일 내 Configuration.json 참조

## 윈도우
  1. ArbiFramework 실행(ArbiFramework 문서의 실행 항목 참조)
  2. VirtualDevice 폴더 내의 Run.bat 파일 실행

## 리눅스
  1. ArbiFramework 실행(ArbiFramework 문서의 실행 항목 참조)
  2. VirtualDevice 폴더 내의 Run.sh 파일 실행

## 개발자용
  1. 터미널 환경(cmd, PowerShell, xfce Terminal, terminator, 기본 Linux Shell 등)에서 java -jar VirtualDevice.jar

## 설정 방법
|Name|Description|Example|
|----|-----------|-------|
|--broker, -b|ARBI Framework 브로커의 URL 설정. 기본값 localhost:61616|--broker="127.0.0.1:61616|
|--configuration, -c|Configuration 파일 경로 지정. 기본값은 JAR파일과 같은 폴더 내의 config.json파일(./config.json)|--configuration="C:\configuration.json"|
|--directory, -d | Cloud Interface의 모델 경로 지정. 기본값은 JAR파일이 위치한 현재 경로. | --directory=".\model"
|--help -h|Cloud Interface 사용법 출력|--help|

## 실행 예시
```
PS C:\ARBI\VirtualSensor> run.bat

PS C:\ARBI\VirtualSensor> java -jar .\VirtualDeviceManager.jar
브로커 URL이 tcp://localhost:61616로 설정되었습니다.
버츄어 디바이스 매니저의 에이전트 URI가 agent://www.arbi.com/VirtualDeviceManager으로 설정되었습니다.
버츄어 디바이스의 URI Prefix가 agent://www.arbi.com/VirtualDevice#으로 설정되었습니다.
Broker URL : tcp://localhost:61616 / Agent Name : agent://www.arbi.com/VirtualDeviceManager
Model File List = []
.....
```

Configuration.xml을 찾지 못했을 경우
```
PS C:\ARBI\VirtualSensor> run.bat

PS C:\ARBI\VirtualSensor> java -jar .\VirtualDeviceManager.jar
Configuration 파일을 찾을 수 없습니다. 기본 설정으로 진행됩니다.
Broker URL : tcp://127.0.0.1:61616 / Agent Name : agent://www.arbi.com/VirtualSensorManager
Model File List = []
```

Command Line 인자 입력
```
PS C:\ARBI\VirtualSensor> run.bat —broker=192.168.0.1:61616

PS C:\ARBI\VirtualSensor> java –jar .\VirtualDeviceManager.jar —broker=“tcp://192.168.0.1:61616”
브로커 URL이 tcp://localhost:61616로 설정되었습니다.
버츄어 디바이스 매니저의 에이전트 URI가 agent://www.arbi.com/VirtualDeviceManager으로 설정되었습니다.
버츄어 디바이스의 URI Prefix가 agent://www.arbi.com/VirtualDevice#으로 설정되었습니다.
Broker URL : tcp://localhost:61616 / Agent Name : agent://www.arbi.com/VirtualDeviceManager
Model File List = []
.....
```

# 기능
각 Agent는 Virtual Device Manager를 통해 Virtual Device Agent를 생성, Virtual Device Agent를 통해 Virtual Device와 통신할 수 있다. 각 기능의 프로토콜은 다음과 같다.

|GLName|Type|Description|Arguments|
|----------------|---|------------|--------------|--------------|
|VirtualDeviceInitiate|Send|주어진 모델에 따라 Virtual Device Agent 생성|$virtualDeviceModel|
|VirtualDeviceQuery|Query|주어진 인자에 따라 Virtual Device Agent 검색|$argument<br>$property|

1. VirtualDeviceInitiate : 주어진 모델에 따라 Virtual Device Agent 생성
   * (VirtualDeviceInitiate ($virtualDeviceModel)
      - $virtualDeviceModel : 추가할 대상 VirtualDevice의 Model. 모델에 대한 설명은 모델 항목 참조.
   * 예시
      ```
      (VirtualDeviceInitiate
         (id "ChangeTemperature")
         (Description "")
         (Location "Room1")
         ...
      )
      ```
1. VirtualDeviceQuery : VirtualDeviceManger가 가지고 있는 Virtual Device Agent 중에서 인자로 주어진 조건을 만족시키는 개체를 반환
   * (VirtualDeviceQuery ($argument1 $property1) ($argument2 $property2)...)
      - $argument: 검색할 Virtual Device Agent의 속성. Argument의 종류는 다음과 같다
         * id
         * Name
         * Description
         * Location
         * ServiceType
      - $value : 검색할 Virtual Device Agent의 속성 값
   * 예시
      ```
      (VirtualDeviceQuery (ServiceType “Lamp”))
      ---
      (VirtualdeviceQueryResponse “virtualdevice001” “virtualdevice002”...)
      ```
      위와 같은 경우 ServiceType이 Lamp인 모든 Virtual Device Agent를 반환한다
      ```
      (VirtualDeviceQuery (ServiceType “Lamp”) (Location “Room001”))
      ---
      (VirtualdeviceQueryResponse “virtualdevice001” “virtualdevice002”...)
      ```
      위와 같은 경우 ServiceType이 Lamp이고, Location이 Room001인 모든 Virtual Device Agent를 반환한다
      
# 모델

VirtualDeviceManger는 다양한 종류의 Virtual Device를 수용하기 위해 Virtual Device를 추상화하여 구성한 VirtualDeviceModel을 사용한다. Model의 구성 요소는 아래와 같다.


|이름|설명|
|---|----|
|id|해당 Device의 id|
|Description|해당 Device에 대한 상세 설명|
|Location|해당 Device가 위치한 장소|
|Cyclic|해당 Device를 일정 주기마다 자동적으로 실행시키기 위한 옵션|
|ServiceType|해당 Device가 제공하는 Service의 종류|
|DataModel*|해당 Device와 통신하기 위해 필요한 Data의 Model. 사용하는 Data의 종류만큼 존재|
|CommunicationModel*|해당 Device와의 통신 과정에 대한 Model. Device가 제공하는 Communication의 종류만큼 존재 가능|

- Cyclic
  |이름|설명|
  |---|----|
  |$value|해당 Device의 Cyclic 적용 여부. boolean|
  |CyclicModel|해당 Device Cyclic 적용 방법|

- CyclicModel
  |이름|설명|
  |---|----|
  |Tic|Cyclic 주기. 1000 TIC = 1 초|

- DataModel
  |이름|설명|
  |---|----|
  |Name|통신에 사용될 Data의 이름|
  |Type|해당 Data의 Type|

- ComminicationModel
  |이름|설명|
  |---|----|
  |Type|해당 Communication의 Type. Type에는 HTTP등의 프로토콜이 존재하며, 이 항목을 참조하여 각 프로토콜의 통신 방법을 검색하게 됨|
  |MessageModel*|Communication Type의 Communication을 구성하는 Message의 Model. Communication이 사용하는 Message 종류만큼 존재|

- MessageModel
  |이름|설명|
  |---|----|
  |Name|해당 Message의 이름|
  |Input*|Input Message의 형식. 목적 Type으로 변환시키고자 하는 여러 Communication Type의 Input Message마다 존재 가능|
  |Output|Input Message를 변환하여 만들어질 Message의 형식|

- Input&Output
  |이름|설명|
  |---|----|
  |Template|Message의 형식|

- Template
  |이름|설명|
  |---|----|
  |Type|해당 Message의 Type|
  |Contents|해당 Message의 구성 요소|

- Contents

  Contents의 구성 요소는 Template의 Type에 따라 정해짐. 변수의 사용은 DataModel에 사용된 Name을 통해 이루어짐
  |이름|설명|
  |---|----|
  |$GL|Template의 Type이 GL일 때 사용. Input&Output으로 사용될 Message의 형태. |
  |Method|Template의 Type이 HTTPRequest일 때 사용. HTTP Method를 표시|
  |URL|Template의 Type이 HTTPRequest일 때 사용. Device의 URL |
  |Header|Template의 Type이 HTTPRequest일 때 사용. HTTP Protocol을 사용하기 위한 헤더|
  |Body|Template의 Type이 HTTPRequest&HTTPResponse일 때 사용. HTTP Protocol을 사용하기 위한 Body|
  |Status|Template의 Type이 HTTPResponse 일 때 사용. HTTP Response의 Status Code|

- 예시
  ```
  (VirtualDeviceInitiate
      (id "ChangeTemperature")
      (Description "")
      (Location "Room1")
      (Cyclic
          "True"
          (CyclicModel
              (Tic 1000)
          )
      )
      (ServiceType "온도 조절")
      (DataModel
          (Name "ActionID")
          (Type "Integer")
      )
      (DataModel
          (Name "temperature")
          (Type "Integer")
      )
      (CommunicationModel
          (Type "HTTP")
          (MessageModel
              (Name "Request")
              (Output
                  (Template
                      (Type "HTTPRequest")
                      (Contents
                          (Method "POST")
                          (URL "http://DeviceDomain/AirConditioner1?op=changeTemperatur&temperature=$temperature")
                          (Header 
                              (TestHeader "test")
                          )
                          (Body "")
                      )
                  )
              )
          )
          (MessageModel
              (Name "Response")
              (Input
                  (Template
                      (Type "HTTPResponse")
                      (Contents
                          (Status 200)
                          (Body "")
                      )
                  )
              )
              (Output
                  (Template
                      (Type "GL")
                      (Contents
                          (VirtualDeviceResponse $ActionID "OK")
                      )
                  )
              )
          )
      )
  )
  ```
## Protocol Buffers

## 1. Protocol Buffers의 탄생 배경
Protocol Buffers(protobuf)는 Google에서 개발한 데이터 직렬화 및 언어 중립적인 통신 포맷입니다. 
Google 내부 시스템 간 데이터 교환에서 사용하기 위해 개발되었으며, 이후 오픈 소스로 공개되어 개발자들이 널리 사용하게 되었습니다.
프로토콜 버퍼는 구조화된 데이터를 직렬화하기 위한 언어 중립적이고 플랫폼 중립적인 확장 가능한 메커니즘입니다.
더 작고 빠르며 네이티브 언어 바인딩을 생성한다는 점을 제외하면 JSON과 같습니다.
데이터를 한 번 구성하는 방법을 정의한 다음 특수 생성 소스 코드(generated source code)를 사용하여 다양한 데이터 스트림에서
다양한 언어를 사용하여 구조화된 데이터를 쉽게 쓰고 읽을 수 있습니다.


## 2. Protocol Buffers의 장단점
### 장점
- **효율성**: 이진 형식으로 데이터를 저장하므로 작은 크기로 데이터를 표현할 수 있습니다.
- **속도**: 직렬화 및 역직렬화 과정이 빠르며, 처리 속도가 빠릅니다.
- **언어 중립성**: 여러 프로그래밍 언어를 지원하며, 동일한 메시지 정의를 사용하여 서로 다른 언어 간에 통신이 가능합니다.
- **스키마 정의**: 데이터 구조를 스키마로 정의하여 데이터 무결성을 보장하고 버전 관리를 용이하게 합니다.


### 단점
- **가독성**: 이진 형식으로 저장되므로 사람이 읽기 어렵습니다.
- **디버깅**: 디버깅을 위해서는 역직렬화된 데이터를 다시 읽어야 하기 때문에 번거로울 수 있습니다.

## 3. Protocol Buffers가 JSON에 비해 갖는 장점
- **크기**: protobuf는 더 작은 바이너리 크기를 가지며, 데이터를 효율적으로 전송할 수 있습니다.
- **속도**: JSON보다 직렬화 및 역직렬화 과정에서 빠르며, 데이터 처리 속도가 향상됩니다.
- **스키마**: protobuf는 스키마를 사용하여 데이터 구조를 정의하므로 데이터의 일관성과 버전 관리가 용이합니다.

## Protocol Buffers 기본 문법 (.proto 파일)

### 패키지 정의

- 선택 사항이며 
- proto 파일에 명시하지 않으면 패키지는 Java 패키지로 사용됩니다

```protobuf
syntax = "proto3";
package mypackage;
```

### 메시지 정의
```protobuf
message Person {
  string name = 1;
  int32 age = 2;
}
```

### 필드 유형
- `int32`, `int64`, `uint32`, `uint64`: 정수형
- `float`, `double`: 부동 소수점 형식
- `string`: 문자열
- `bool`: 불리언 값
- `enum`: 열거형
- `repeated`: 배열 혹은 리스트
- `oneof`: 여러 필드 중 하나의 필드만 값이 존재할 수 있도록 정의할 때 사용됩
- `map`: 키-값 쌍을 저장하는데 사용됩

### 열거형 정의
```protobuf
enum Category {
  ELECTRONICS = 0;
  CLOTHING = 1;
  FOOD = 2;
}
```

### 반복 필드
```protobuf
message Product {
  string name = 1;
  float price = 2;
  repeated string tags = 3;
  Category category = 4;
}
```

### oneof 필드
```protobuf
message UserProfile {
  string username = 1;
  oneof user_data {
    string email = 2;
    int32 age = 3;
    string phone_number = 4;
  }
}
```

### map 필드
```protobuf
message AddressBook {
  map<string, Person> contacts = 1;
}
```

### Protocol Buffers 예제 파일

```protobuf
syntax = "proto3";
package tutorial;

// 주소록 정보를 저장하는 메시지 정의
message Person {
  string name = 1;
  int32 id = 2;
  string email = 3;

  // PhoneNumber 메시지 타입의 반복 필드
  repeated PhoneNumber phones = 4;
}

// 전화번호를 저장하는 메시지 정의
message PhoneNumber {
  string number = 1;
  PhoneType type = 2;  // 열거형 타입 사용

  // PhoneType 열거형 정의
  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }
}

// 주소록 정보를 저장하는 메시지 타입의 반복 필드
message AddressBook {
  repeated Person people = 1;
}

```

### 컴파일 명령어

```sh
protoc -I=프로토콜_정의_파일_디렉토리 --java_out=생성될_자바_코드_디렉토리 your_proto_file.proto
```

- protoc: Protocol Buffers 컴파일러 실행 명령어.
- --java_out: Java 코드를 생성하기 위한 옵션.
- out_directory: 생성된 코드를 저장할 디렉토리.
- input.proto: 컴파일할 프로토콜 버퍼 파일 이름.

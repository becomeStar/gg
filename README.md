## Protocol Buffers

## 1. Protocol Buffers의 탄생 배경
Protocol Buffers(protobuf)는 Google에서 개발한 데이터 직렬화 및 언어 중립적인 통신 포맷입니다. Google 내부 시스템 간 데이터 교환에서 사용하기 위해 개발되었으며, 이후 오픈 소스로 공개되어 개발자들이 널리 사용하게 되었습니다.

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

## Protocol Buffers 기본 문법

### 패키지 정의
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

### 컴파일 명령어
```sh
protoc mypackage.proto --python_out=.
```

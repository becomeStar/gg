
## gRPC-Java Interceptor

## 1. Interceptor 의 기본 개념
gRPC(구글이 개발한 RPC 프레임워크)의 Java 구현에서 Interceptor는 gRPC 호출의 여러 이벤트를 감지하고 가로채는 데 사용됩니다. 
Interceptor를 사용하여 클라이언트 및 서버 측에서 호출 전후 및 중간 단계에서 작업을 수행할 수 있습니다. 
Interceptor는 gRPC 호출의 다양한 단계에서 호출을 가로채고 조작할 수 있습니다.


## 2. Interceptor 기본 동작 방식
### 초기화(Initialization)
- ClientInterceptors 또는 ServerInterceptors를 사용하여 Interceptor를 등록하면 호출이 시작되기 전에 호출의 초기화 단계에서 Interceptor가 실행됩니다. 이 단계에서는 주로 호출 메타데이터를 설정하거나 인증과 관련된 작업을 수행하는 데 사용됩니다.
### 메서드 호출(Method Invocation)
- 클라이언트가 서버에 RPC를 호출하면 Interceptor가 호출 전에 실행됩니다. 이 단계에서는 호출에 대한 추가 정보를 설정하거나 호출을 수정할 수 있습니다.
### 응답 취득(Response Reception)
- 서버가 클라이언트에게 응답을 보낼 때, Interceptor는 응답을 가로채고 처리할 수 있습니다. 예를 들어 응답 데이터를 로깅하거나 검증할 수 있습니다.
### 호출 완료(Call Completion):
- 호출이 완료되면 Interceptor가 마지막 단계에서 실행됩니다. 이 단계에서 호출 결과를 처리하거나 오류를 처리하는 등의 작업을 수행할 수 있습니다.

- Interceptor를 구현하려면 ClientInterceptor 또는 ServerInterceptor 인터페이스를 구현해야 합니다. 각각의 이벤트에 대한 콜백 메서드가 있습니다. 예를 들어, ClientInterceptor의 경우 다음과 같은 메서드를 구현할 수 있습니다
  - interceptCall: 이 메서드는 호출을 가로채고 필요한 작업을 수행한 후 ClientCall 객체를 반환합니다. 이 객체를 통해 호출을 진행하고 응답을 처리할 수 있습니다.
  - 서버 측에서도 비슷한 방식으로 ServerInterceptor를 구현하여 서버 측 Interceptor를 작성할 수 있습니다.
- Interceptor를 사용하면 gRPC 호출에 대한 다양한 작업을 수행하거나, 호출의 로깅, 인증, 보안 등의 관련 작업을 효과적으로 처리할 수 있습니다.
- 클라이언트 Interceptor 예제
   ```import io.grpc.*;

   public class MyClientInterceptor implements ClientInterceptor {

    @Override
    public <ReqT, RespT> ClientCall<ReqT, RespT> interceptCall(
            MethodDescriptor<ReqT, RespT> method,
            CallOptions callOptions,
            Channel next) {
        
        // 클라이언트 호출을 가로채고 원하는 작업을 수행할 수 있습니다.
        
        // 예를 들어, 메서드 이름과 요청 메타데이터를 로깅하는 코드:
        System.out.println("Client Interceptor: Intercepting method " + method.getFullMethodName());
        
        // 기본 호출을 만들고 반환합니다.
        return next.newCall(method, callOptions);
    }
 }
   
```
- 서버 Interceptor 예제
   ```import io.grpc.*;

public class MyServerInterceptor implements ServerInterceptor {

    @Override
    public <ReqT, RespT> ServerCall.Listener<ReqT> interceptCall(
            ServerCall<ReqT, RespT> call,
            Metadata headers,
            ServerCallHandler<ReqT, RespT> next) {
        
        // 서버 호출을 가로채고 원하는 작업을 수행할 수 있습니다.
        
        // 예를 들어, 요청 메타데이터를 확인하고 인증을 수행하는 코드:
        String authorizationHeader = headers.get(Metadata.Key.of("Authorization", Metadata.ASCII_STRING_MARSHALLER));
        if (isValidAuthorization(authorizationHeader)) {
            // 유효한 인증이면 다음 핸들러로 호출을 전달합니다.
            return next.startCall(call, headers);
        } else {
            // 인증이 실패하면 호출을 거부합니다.
            call.close(Status.UNAUTHENTICATED, headers);
            return new ServerCall.Listener<>() {};
        }
    }

    private boolean isValidAuthorization(String authorizationHeader) {
        // 실제 인증 검증 로직을 여기에 구현합니다.
        // 예: 토큰 검증 또는 사용자 권한 확인
        return true; // 유효한 인증이라고 가정
    }
}
   
```
 
##  3. gRPC Interceptor 콜백 메소드 

- gRPC Java에서 클라이언트 및 서버 Interceptor를 사용하여 다양한 이벤트를 처리할 때 일부 특정 메서드가 호출됩니다. 아래에서 몇 가지 중요한 이벤트 및 관련 메서드를 설명하겠습니다

- 클라이언트 Interceptor 이벤트:
  - onReady(): 이 메서드는 클라이언트 측에서 호출이 준비되었을 때 호출됩니다. 즉, 요청이 서버에 전송되기 전에 실행됩니다. 이 단계에서는 메서드 호출을 수정하거나 준비 상태를 확인하는 데 사용할 수 있습니다.
  - onHeaders(): 서버에서 응답 헤더를 수신했을 때 호출됩니다. 이 메서드를 사용하여 서버 응답의 헤더를 검사하거나 처리할 수 있습니다.
  - onMessage(): 서버에서 메시지(응답 데이터)를 수신했을 때 호출됩니다. 이 메서드를 사용하여 서버 응답 메시지를 처리하거나 변환할 수 있습니다.
  - onComplete(): 호출이 완료될 때 호출됩니다. 클라이언트에서 서버로의 호출이 완료되면 이 메서드를 사용하여 완료 상태를 처리할 수 있습니다.
  - onCancel(): 호출이 취소되었을 때 호출됩니다. 클라이언트에서 호출이 취소될 때 실행됩니다.
    
- 서버 Interceptor 이벤트:
  - onReady(): 이 메서드는 서버 측에서 호출이 준비되었을 때 호출됩니다. 클라이언트의 요청이 서버에 도달하기 전에 실행됩니다. 서버에서 클라이언트 요청을 처리하기 전에 이벤트를 처리하는 데 사용할 수 있습니다.
  - onHeaders(): 클라이언트로 응답 헤더를 보낼 때 호출됩니다. 이 메서드를 사용하여 클라이언트 응답의 헤더를 검사하거나 처리할 수 있습니다.
  - onMessage(): 클라이언트로 메시지(응답 데이터)를 보낼 때 호출됩니다. 이 메서드를 사용하여 클라이언트 응답 메시지를 처리하거나 변환할 수 있습니다.
  - onHalfClose(): 클라이언트가 데이터 스트림의 절반을 닫을 때 호출됩니다. 이것은 클라이언트가 모든 메시지를 보낸 후 스트림을 닫을 때 발생하며, 서버에서 응답을 반환하기 시작할 수 있습니다.
  -  onComplete(): 호출이 완료될 때 호출됩니다. 서버에서 클라이언트로의 호출이 완료되면 이 메서드를 사용하여 완료 상태를 처리할 수 있습니다.
  - onCancel(): 호출이 취소되었을 때 호출됩니다. 서버에서 클라이언트 요청이 취소될 때 실행됩니다.

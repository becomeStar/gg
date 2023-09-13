## grpc Metadata
- 메타데이터는 키-값 쌍 목록 형식의 특정 RPC 호출(예: 인증 세부 정보)에 대한 정보입니다. 여기서 키는 문자열이고 값은 일반적으로 문자열이지만 이진 데이터일 수도 있습니다.
- 키는 대소문자를 구분하지 않으며 ASCII 문자, 숫자, 특수 문자(-, _, )로 구성됩니다. grpc-(gRPC 자체용으로 예약됨)로 시작하면 안 됩니다. 바이너리 값 키는 -bin으로 끝나지만 ASCII 값 키는 그렇지 않습니다.
- 사용자 정의 메타데이터는 gRPC에서 사용되지 않으므로 클라이언트가 서버 호출과 관련된 정보를 제공할 수 있으며 그 반대의 경우도 마찬가지입니다. 메타데이터에 대한 액세스는 언어에 따라 다릅니다.

### metadata 를 요청에 포함시키는 방법
- gRPC-Java에서 Metadata를 요청에 포함시키는 방법은 ClientInterceptor를 사용하여 요청을 수정하는 것입니다. ClientInterceptor를 구현한 클래스에서 interceptCall() 메서드를 오버라이드하고, Metadata 객체를 생성하여 요청에 추가합니다. 다음은 예시 코드입니다.
 ``` package com.tp.bookstore;
import io.grpc.CallOptions;

import io.grpc.Channel;
import io.grpc.ClientCall;
import io.grpc.ClientInterceptor;
import io.grpc.ForwardingClientCall;
import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import io.grpc.Metadata;
import io.grpc.MethodDescriptor;
import io.grpc.StatusRuntimeException;
import java.util.concurrent.TimeUnit;
import java.util.logging.Level;
import java.util.logging.Logger;
import static io.grpc.Metadata.ASCII_STRING_MARSHALLER;

import com.tp.bookstore.BookStoreOuterClass.Book;
import com.tp.bookstore.BookStoreOuterClass.BookSearch;

public class BookStoreClientUnaryBlockingMetadata {
   private static final Logger logger = Logger.getLogger(BookStoreClientUnaryBlockingMetadata.class.getName());
   private final BookStoreGrpc.BookStoreBlockingStub blockingStub;
   public BookStoreClientUnaryBlockingMetadata(Channel channel) {
      blockingStub = BookStoreGrpc.newBlockingStub(channel);
   }
   static class BookClientInterceptor implements ClientInterceptor {
      @Override
      public <ReqT, RespT> ClientCall<ReqT, RespT>
      interceptCall(
         MethodDescriptor<ReqT, RespT> method, CallOptions callOptions, Channel next
      ) {
         return new 
         ForwardingClientCall.SimpleForwardingClientCall<ReqT, RespT>(next.newCall(method, callOptions)) {
            @Override
            public void start(Listener<RespT> responseListener, Metadata headers) {
               logger.info("Added metadata");
               headers.put(Metadata.Key.of("HOSTNAME", ASCII_STRING_MARSHALLER), "MY_HOST");
               super.start(responseListener, headers);
            }
         };
      }
   }
   public void getBook(String bookName) {
      logger.info("Querying for book with title: " + bookName); 
      BookSearch request = BookSearch.newBuilder().setName(bookName).build(); 
      Book response; 
      CallOptions.Key<String> metaDataKey = CallOptions.Key.create("my_key");
      try {
         response = blockingStub.withOption(metaDataKey, "bar").first(request);
      } catch (StatusRuntimeException e) {
         logger.log(Level.WARNING, "RPC failed: {0}", e.getStatus());
         return;
      }
      logger.info("Got following book from server: " + response);
   } 
   public static void main(String[] args) throws Exception {
      String bookName = args[0];
      String serverAddress = "localhost:50051";
      ManagedChannel channel =  ManagedChannelBuilder.forTarget(serverAddress).usePlaintext().intercept(new BookClientInterceptor()).build();
      try {
         BookStoreClientUnaryBlockingMetadata client = new BookStoreClientUnaryBlockingMetadata(channel);
         client.getBook(bookName);
      } finally {
         channel.shutdownNow().awaitTermination(5, TimeUnit.SECONDS);
      }
   }
}```

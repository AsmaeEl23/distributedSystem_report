<center><img src="images/ensetLOGO.png">
<h1>Distributed System<br>Google Remote Procedure Call<br>gRPC<br></h1>
<p><br><br>Asmae EL HYANI<br> Distributed System & Artificial Intelligence Master’s<br> ENSET Mohammedia</p>
</center>
<br><br><br>
<h2>Introduction</h2>
<p>Google Remote Procedure Call (gRPC) is an open-source remote procedure call (RPC) framework developed by Google. 
It is a high-performance and lightweight framework for building distributed systems, allowing clients and servers 
to communicate transparently and efficiently. <br>gRPC uses Protocol Buffers (protobuf) as its default interface definition 
language (IDL), which allows for efficient serialization and deserialization of data across different languages and platforms. 
This makes it easier to develop and maintain cross-platform applications with different programming languages. 
<br>gRPC supports multiple programming languages, including C++, Java, Python, Go, C#, Ruby, and more. It also supports 
different types of communication patterns, including unary, server streaming, client streaming, and bidirectional 
streaming.</p>
<img src="images/grpcModels.png">
<p>In this assignment, we will go over the basics of gRPC, how to use it to build a chat application between many 
clients, and we will apply that using Java language.</p>
<br><br><br><br>
<ol type="I">
  <h2><li >gRPC communication patterns (models).</li></h2>
 <ol type="1">
  <h3><li>Source Code</li></h3>
  <a href="https://github.com/AsmaeEl23/gRPC_P1">click to see the source code</a>
 </ol>
  
<h2><li>gRPC chat application.</li></h2>
<ol type="1">
  <h2><li>gRPC chat application.</li></h2>
<ol type="1">
  <h3><li>gRPC proto file</li></h3>
    <pre style="background-color:#3b3535;color:white;">
        syntax= "proto3";
        option java_package="ma.enset.stubs"; 
        service ChatService{
            rpc fullStream(stream Message ) returns (stream Message);  
            }
        message Message{
            string messageFrom=1;
            string messageTo=2;
            string content=3;
        }
</pre>
  <h3><li>gRPC chat service</li></h3>
    <pre style="background-color:#3b3535;color:white;">
public class ChatServerImp extends ChatServiceGrpc.ChatServiceImplBase {
   HashMap<'String,StreamObserver<'Chat.Message>> clients=new HashMap<>();
   @Override
   public StreamObserver<'Chat.Message> fullStream(StreamObserver<'Chat.Message> responseObserver) {
       return new StreamObserver<'Chat.Message>() {
           @Override
           public void onNext(Chat.Message message) {
               String messageFrom = message.getMessageFrom();
               String messageTo = message.getMessageTo();
               if(!clients.containsKey(messageFrom)){
                   clients.put(messageFrom,responseObserver);
               }
               if (clients.containsKey(messageTo) && !messageTo.equals("")){
                   StreamObserver<'Chat.Message> messageStreamObserver= clients.get(messageTo);
                   messageStreamObserver.onNext(message);
               }
           }
           @Override
           public void onError(Throwable throwable) {
           }
           @Override
           public void onCompleted() {
               responseObserver.onCompleted();
           }
       };
}
}</pre>
  <h3><li>gRPC chat server</li></h3>
    <pre style="background-color:#3b3535;color:white;">
public class GrpcServer {
    public static void main(String[] args) throws Exception{
        Server server = ServerBuilder.forPort(5555)
                .addService(new ChatServerImp())
                .build();
        server.start();
        server.awaitTermination();
    }
}
    </pre>
  <h3><li>gRPC chat client</li></h3>
    <pre style="background-color:#3b3535;color:white;">
public class ChatClient {
    public static void main(String[] args) throws IOException {
        ManagedChannel managedChannel= ManagedChannelBuilder.forAddress("localhost",5555)
                .usePlaintext()
                .build();
        ChatServiceGrpc.ChatServiceStub asyncStub = ChatServiceGrpc.newStub(managedChannel);
        Scanner sc = new Scanner(System.in);
        System.out.println("Enter your name : ");
        String clientName= sc.nextLine();
        System.out.println(clientName+" connected ....");
        StreamObserver<'Chat.Message> send = asyncStub.fullStream(new StreamObserver<'Chat.Message>() {
            @Override
            public void onNext(Chat.Message message) {
                String messageFrom= message.getMessageFrom();
                String content =message.getContent();
                System.out.println(messageFrom+" : "+content);
            }
            @Override
            public void onError(Throwable throwable) {
                System.out.println(throwable.getMessage());
            }
            @Override
            public void onCompleted() {
                System.out.println("End......!");
            }
        });
        Chat.Message req=Chat.Message.newBuilder()
                .setMessageFrom(clientName)
                .setMessageTo("")
                .setContent("")
                .build();
        send.onNext(req);
        while (true){
            System.out.println("===================");
            System.out.println("Message : ");
            String message=sc.nextLine();
            System.out.println("Send to : ");
            String to=sc.nextLine();
            Chat.Message request=Chat.Message.newBuilder()
                    .setMessageFrom(clientName)
                    .setMessageTo(to)
                    .setContent(message)
                    .build();
            send.onNext(request);
        }
    }
}
</pre>
<h3><li>Source Code</li></h3>
<a href="https://github.com/AsmaeEl23/chat_grpc_TP">click to see the source code</a>
<h3><li>Test</li></h3>
<h4>With java client</h4>
<img src="images/p2/javaClient.PNG">
<h4>With bloomRPC client</h4>
<img src="images/p2/javaClient.PNG">
</ol>
<h2><li>Magic number Game with gRPC.</li></h2>
</ol>
<h2>Conclusion.</h2>

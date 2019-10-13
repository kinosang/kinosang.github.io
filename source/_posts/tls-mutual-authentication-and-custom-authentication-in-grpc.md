---
title: gRPC TLS 相互驗證和自訂驗證
date: 2018-05-28 23:37:23
categories:
  - 軟體開發
tags:
---

gRPC 目前已經支援 TLS 相互驗證、Google 驗證和自訂驗證，但說明 ([gRPC Authentication](https://grpc.io/docs/guides/auth.html)) 中的相關內容十分有限，今天踩到好幾次坑。

<!--more-->

### TLS 相互驗證

伺服器端以 Go 為例

```go
// 說明中指出可使用如下方案，但此種寫法不強制要求相互驗證
// creds := credentials.NewServerTLSFromFile(certFile, keyFile)
// s := grpc.NewServer(grpc.Creds(creds))
// 注意其實此寫法存在錯誤，NewServerTLSFromFile() 傳回值為 (TransportCredentials, error)
// 故應寫成 creds, _ := credentials.NewServerTLSFromFile(certFile, keyFile)

// 載入 TLS 伺服器憑據
cert, err := tls.LoadX509KeyPair("cert/server1.pem", "cert/server1.key")
if err != nil {
  log.Fatalf("failed to load key pair: %s", err)
}
// 載入憑據 CA
caCert, err := ioutil.ReadFile("cert/ca.pem")
if err != nil {
  log.Fatalf("failed to load key pair: %s", err)
}
caCertPool := x509.NewCertPool()
caCertPool.AppendCertsFromPEM(caCert)

// 設定 gRPC ServerOption
opts := []grpc.ServerOption{
  grpc.Creds(credentials.NewTLS(&tls.Config{
    Certificates: []tls.Certificate{cert}, // TLS 憑據
    ClientCAs:    caCertPool, // 憑據 CA
    ClientAuth:   tls.RequireAndVerifyClientCert, // 要求用戶端憑證驗證
  })),
}

// 創建新 gRPC 伺服器
s := grpc.NewServer(opts...)
```

用戶端以 NodeJS 為例

```JavaScript
import grpc from 'grpc';

const ssl_creds = grpc.credentials.createSsl(readFileSync(__dirname + '/cert/server1.pem'), readFileSync(__dirname + '/cert/client.key'), readFileSync(__dirname + '/cert/client.pem'));

const client = new services.WalletClient('localhost:8080', ssl_creds);
```

### 自訂驗證

為了加強 RPC 安全性，除 TLS 相互驗證之外，我還希望引入利用 HTTP Header 的自訂驗證。

開始時思路為傳遞一個 RPC 的 HMAC-SHA256 數位簽名，為了避免重送攻擊，在生成簽名時引入時間戳和 Nonce。

為了實現相關功能，需要用到 gRPC 的攔截器 (Interceptor)，在 gRPC 中，攔截器與各種 HTTP 伺服器軟體的中介層 (middleware) 作用類似，分為伺服器端攔截器和用戶端攔截器，可以對請求動作添加額外邏輯。

仔細分析了 gRPC 的原始碼，發現對完整 RPC 用戶端請求進行簽名的可能性極低 (伺服器端攔截器不方便獲取請求內容，用戶端攔截器無法獲取到請求內容)，遂改為對用戶端請求動作 (URI) 進行簽名。

對伺服器端進行修改

```go
func ensureValidToken(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
  md, ok := metadata.FromIncomingContext(ctx)
  if !ok {
    return nil, errMissingMetadata
  }

  // metadata.MD 中所有 key 都是小寫的
  // https://godoc.org/google.golang.org/grpc/metadata#New
  if !valid(md["authorization"], md["x-auth-timestamp"], md["x-auth-nonce"], info.FullMethod) {
    // valid 函式的實現不在此處討論
    // info.FullMethod 即用戶端請求動作的 URI
    return nil, errInvalidToken
  }

  return handler(ctx, req)
}

opts := []grpc.ServerOption{
  grpc.UnaryInterceptor(ensureValidToken), // 設定攔截器
  grpc.Creds(credentials.NewTLS(&tls.Config{
    Certificates: []tls.Certificate{cert},
    ClientCAs:    caCertPool,
    ClientAuth:   tls.RequireAndVerifyClientCert,
  })),
}
```

對用戶端進行修改

```javascript
// 如果不需要 options.method_definition.path 等資訊，可以使用 createFromMetadataGenerator
// grpc.credentials.createFromMetadataGenerator(function(args, callback) {
//   var metadata = new Metadata();
//   metadata.add('authorization', 'something-here');
//   callback(null, metadata);
// });
// 此處將 ssl_creds 和 call_creds 合併為 combined_creds
// const combined_creds = grpc.credentials.combineChannelCredentials(ssl_creds, call_creds);
// const client = new services.WalletClient('localhost:8080', combined_creds);

// 使用攔截器實現添加 metadata
const interceptor = function(options, nextCall) {
    return new grpc.InterceptingCall(nextCall(options), {
        start: function(metadata, listener, next) {
            // options.method_definition.path 即請求動作的 URI
            // 部分邏輯略去，具體實現不在此討論
            // 創建新 metadata 並且添加欄位
            var metadata = new grpc.Metadata();
            metadata.add('authorization', 'Basic: ' + Buffer.from(client_id + ':' + signature).toString('base64'));
            metadata.add('x-auth-timestamp', timestamp.toString());
            metadata.add('x-auth-nonce', nonce);

            next(metadata, listener);
        }
    });
};

const client = new services.WalletClient('localhost:8080', ssl_creds, {
    interceptors: [interceptor] // 默認攔截器
});
```

### 小結

gRPC 作為 Google 開發的 RPC 專案，目前正在蓬勃發展，文檔難免有落後於實際功能之處，如果每次都要翻閱原始碼，真的是十分不方便，所以將本次經歷記下希望其他人有遇到類似情況可以少走一些彎路。

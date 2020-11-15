---
title: Http 訊息簽章
categories:
  - 網際網路
date: 2020-10-29 23:41:37
tags:
---

雖然曾經盛行的 Web Service (SOAP / WDSL) 有對驗證與授權制訂標準, 但是隨著技術的進步, 相關技術已被多數從業者掃進歷史的垃圾堆. 因此造成了當下 Web API 通訊安全相關設計百花齊放群魔亂舞的狀況.

尤其是需要對引數進行重新排序的設計, 更是罪惡滔天, 搞到 Web 民怨聲載道.

W3C 此前對 [一份 Draft](https://tools.ietf.org/html/draft-cavage-http-signatures-12) 做了[更新](https://tools.ietf.org/html/draft-ietf-httpbis-message-signatures-00), 可以說是一種適用範圍非常廣的驗證與授權方法了.

<!--more-->

### 格式

此 Draft 允許將簽章放在 `Signature` 標頭或 `Authorization` 標頭中.

若採用 `Signature` 標頭, 則格式如下:

```http
Signature: <keyId> [algorithm] [created] [expires] [headers] <signature>
```

其中, 必須有 `keyId` 和 `signature`.

| Directives    | 說明          |
| ------------- | ------------- |
| algorithm     | 演算法, 可選 `hs2019`, `rsa-*`, `hmac-*`, `ecdsa-*`. `*` 為 `sha256` 或 `sha512`. 不建議顯示指定演算法. |
| created       | 簽署時間 (Unix timestamp) |
| expires       | 失效時間 (Unix timestamp) |
| headers       | 標頭陣列 |
| signature     | 簽章 (採用 Base64 編碼) |

例如:

```http
Signature: keyId="test-key-b", algorithm="rsa-sha256",
           created=1402170695, expires=1402170995,
           headers="(request-target) (created) host date cache-control x-emptyheader x-example",
           signature="T1l3tWH2cSP31nfuvc3nVaHQ6IAu9YLEXg2pCeEOJETXnlWbgKtBTaXV6LNQWtf4O42V2DZwDZbmVZ8xW3TFW80RrfrY0+fyjD4OLN7/zV6L6d2v7uBpuWZ8QzKuHYFaRNVXgFBXN3VJnsIOUjv20pqZMKO3phLCKX2/zQzJLCBQvF/5UKtnJiMp1ACNhG8LF0Q0FPWfe86YZBBxqrQr5WfjMu0LOO52ZAxi9KTWSlceJ2U361gDb7S5Deub8MaDrjUEpluphQeo8xyvHBoNXsqeax/WaHyRYOgaW6krxEGVaBQAfA2czYZhEA05Tb38ahq/gwDQ1bagd9rGnCHtAg=="
```

若採用 `Authorization` 標頭, 則格式如下:

```http
Authorization: Signature <keyId> [algorithm] [created] [expires] [headers] <signature>
```

### 簽署

1. 建立 `(request-target)`, `(created)` 等標頭, `(request-target)` 標頭結構為 `<METHOD> <URI><?Query>`. 其中, `METHOD` (請求方法) 為小寫
2. 根據需要新增標頭到 `headers` 陣列
3. 建立登入字串, 結構為 `<header>: <value>\n`, 按 `headers` 陣列的順序排列, 最後一項不加 `\n`. 其中, `header` (標頭名稱) 為小寫
4. 使用 keyId 指定的簽署金鑰簽署登入字串, 採用 Base64 編碼

### 驗證

1. 獲取 `headers` 陣列
2. 根據 `headers` 陣列獲取標頭
3. 建立登入字串
4. 使用 keyId 指定的簽署金鑰驗證登入字串

### 實作

為了方便後續應用, 我將此 Draft 的 .Net 實作放在 [GitHub](https://github.com/JoyMoe/HttpSig) 上, 請斧正.

---
title: 'Asp.Net Core MVC, IdentityServer 和外部登入'
date: 2019-01-18 23:18:56
tags:
---

在已有 Asp.Net Core MVC (Identity) 專案中新增 IdentityServer 可以通過 Nuget 安裝並新增程式碼，但直接使用會導致外部登入時出現 404 錯誤。

<!--more-->

```csharp
services
    .AddIdentityServer()
    .AddConfigurationStore(options =>
    {
        options.ConfigureDbContext = builder =>
            builder.UseSqlServer(connectionString,
                sql => sql.MigrationsAssembly(migrationsAssembly));
    })
    .AddOperationalStore(options =>
    {
        options.ConfigureDbContext = builder =>
            builder.UseSqlServer(connectionString,
                sql => sql.MigrationsAssembly(migrationsAssembly));
    })
    .AddAspNetIdentity<ApplicationUser>();
```

外部登入有時會出現 404 錯誤，發現系 `.Identity.External` 和 `.AspNetCore.Correlation.*` Cookies 過大所致，經調查可使用 SessionStore 解決。

```csharp
services.AddSingleton<ITicketStore, CacheTicketStore>();

var ticketStore = services.BuildServiceProvider().GetService<ITicketStore>();

services.ConfigureExternalCookie(options => {
    options.SessionStore = ticketStore;
});

services.ConfigureApplicationCookie(options =>
{
    options.SessionStore = ticketStore;
});
```

`CacheTicketStore` 中需要實作下列方法：

```csharp
Task<string> StoreAsync(AuthenticationTicket ticket);

Task RenewAsync(string key, AuthenticationTicket ticket);

Task<AuthenticationTicket> RetrieveAsync(string key);

Task RemoveAsync(string key);
```

亦可在建構函式中通過 DI 存取 `IMemoryCache`、`IDistributedCache` 服務。

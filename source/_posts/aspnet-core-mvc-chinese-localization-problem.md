---
title: Asp.Net Core MVC 中文字地化問題
date: 2019-10-13 21:22:45
tags:
---

Asp.Net Core MVC 提供了非常方便的本地化工具[^1], 但是此工具在處理中文時存在一個問題.

眾所周知, 中文存在多個不同的 `Culture`[^2], 常見“語種”如下表, 這些 `Culture` 分為 `Neutral` 和 `Specific` 兩種型別.

<table>
    <tbody>
        <tr>
            <th>繁體字</th>
            <td>zh-TW</td>
            <td>zh-CHT<sup>*</sup></td>
            <td>zh-Hant-TW</td>
            <td>zh-Hant<sup>*</sup></td>
            <td rowspan="2">zh<sup>*</sup></td>
        </tr>
        <tr>
            <th>簡體字</th>
            <td>zh-CN</td>
            <td>zh-CHS<sup>*</sup></td>
            <td>zh-Hans-CN</td>
            <td>zh-Hans<sup>*</sup></td>
        </tr>
    </tbody>
</table>

*標星的屬於 `Neutral` 型別*

而因歷史遺留問題, 瀏覽器傳送的 `Accept-Language` header 可能為上述 `Culture` 中的一種或多種, 但 Asp.Net MVC 提供的 fallback 機制不能正確 fallback 部分 `Culture`[^3][^4].

<!--more-->

多方調查後發現, 首先要修正 fallback 相關程式碼, 使 fallback 通過 `CultureInfo.Parent` 向前查詢, 而非使用字串剪下[^5].

在此基礎上, 因為 Linux 系統上 `zh-CN` 和 `zh-TW` 是“別名”, .Net Core 不能正確處理別名[^6], 所以需要額外增加程式碼進行處理[^7].

```csharp
app.Use((context, next) =>
{
    context.Request.Headers["Accept-Language"] = new StringValues(context.Request.Headers["Accept-Language"]
        .Select(lang => lang.ToLower()
            .Replace("zh-tw", "zh-hant-tw")
            .Replace("zh-cn", "zh-hans-cn")).ToArray());
    return next();
});

// 上面的程式碼必須放在此句之前.
app.UseRequestLocalization();
```

[^1]: https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization
[^2]: https://docs.microsoft.com/en-us/dotnet/api/system.globalization.cultureinfo
[^3]: https://github.com/aspnet/Localization/issues/273
[^4]: https://github.com/aspnet/AspNetCore/issues/2634
[^5]: https://github.com/aspnet/AspNetCore/issues/14452
[^6]: https://github.com/dotnet/coreclr/issues/23252#issuecomment-473994613
[^7]: https://github.com/aspnet/AspNetCore/issues/14558

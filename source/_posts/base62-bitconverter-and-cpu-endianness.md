---
title: Base62 BitConverter 以及 CPU 端序
categories:
  - 軟體開發
date: 2018-07-30 11:23:27
tags:
---

之前我實作了一個 C# 的 Base62 編碼庫 [Base62-Net](https://www.nuget.org/packages/Base62-Net)，第一個版本提供位元組陣列的編碼解碼。

於是在實際使用中，需要使用`Encoding.UTF8.GetBytes()`來將字串轉換為位元組陣列，對於數字則需要使用`BitConverter.GetBytes()`。

<!--more-->

但是，使用`BitConverter.GetBytes()`時，必須注意CPU 端序，因為 Base62 編碼使用大端序，而`BitConverter.GetBytes()`在不同機器上根據 CPU 端序而表現不同。

這就導致對數字進行 Base62 編碼時可能與其他程式語言中的結果不同。

因此需要在使用`BitConverter.GetBytes()`之後判斷`BitConverter.IsLittleEndian`，然後使用`Array.Reserve()`變換端序。

昨天釋出的 Base62-Net 1.1.0 已經包含了對字串和整型的編碼解碼功能。

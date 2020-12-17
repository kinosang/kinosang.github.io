---
title: C# 9.0 record 型別
date: 2020-12-18 01:43:20
categories:
  - 軟體開發
tags:
  - dotnet
---

```csharp
public record Person(string FirstName, string LastName);
```

如果說 C# 8.0 我最喜歡的特性是 `switch` expression, 那麼 C# 9.0 的就是 `record` 型別.

<!--more-->

### 宣告

```csharp
public record Person
{
    public string LastName { get; init; }
    public string FirstName { get; init; }
}

public record Student : Person
{
    public int Level { get; init; }
}

var person = new Student {
    LastName = "Wagner",
    FirstName = "Bill",
    Level = 1
};
```

上面的宣告方式與下面的宣告方式基本一致. 不過, 下面的宣告方式, 會自動實作 constructor, deconstructor 等.

```csharp
public record Person(string FirstName, string LastName);

public sealed record Student(string FirstName, string LastName, int Level) : Person(FirstName, LastName);

var person = new Student("Wagner", "Bill", 1);
```

值得注意的是, 上述所有型別均為不可變型 (immutable), 若要使值可變， 則需要使用上面的宣告方法並把 `init;` 改成 `set;`.

### 相等

`record` 型別覆寫了 `==` 和 `!=` 操作符, 當兩個例項的所有屬性均相等時, 兩個例項便“相等”.

`record` 也覆寫了 `GetHashCode`.

### 解構

```csharp
var (first, last) = person;
```

須實作 deconstructor, 有點像 `Tuple`.

### 複製

使用 `with` 關鍵字可以從一個 `record` 變數建立新變數並修改部分屬性.

```csharp
var copy = person with { };
var classmate = person with { FirstName = "Paul" };
```

這一特性其實依賴自動產生的 `protected` constructor, 所以可以利用這一特性快速從父類例項建立一個子類例項.

```csharp
public record Person
{
    public string LastName { get; init; }
    public string FirstName { get; init; }
}

public record Student : Person
{
    public int Level { get; set; }

    public Student(Person parent) : base(parent) { }
}

var person = new Person {
    LastName = "Wagner",
    FirstName = "Bill"
};

var student1 = new Student(person) {
    Level = 1
};

var student2 = new Student(person) {
    Level = 1
};

```

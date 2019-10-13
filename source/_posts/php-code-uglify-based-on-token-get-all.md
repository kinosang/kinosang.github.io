---
title: 基於 token_get_all 的 PHP 代碼壓縮混淆的研究
id: 2109
categories:
  - 軟體開發
date: 2015-06-17 16:37:34
tags:
---

前些日子翻 PHP 的手冊看, 發現一個神奇的函數, 手冊中的說明如下：

> Description
>
> `array token_get_all ( string $source )`
> token_get_all() parses the given source string into PHP language tokens using the Zend engine's lexical scanner.
>
> For a list of parser tokens, see List of Parser Tokens, or use token_name() to translate a token value into its string representation.

<!--more-->

仔細研究了一下發現可以用來對 PHP 代碼進行壓縮和混淆.

主要思路如下：

1.  使用 token_get_all() 解析讀入的代碼
2.  通過返回值中的 Parser Tokens 對 token 數組進行處理
3.  合併數組為字符串并輸出.

下面給出一段示例代碼：

```
function uglify($contents) {

    $tokens = token_get_all($contents);

    $last_space = false;
    foreach( $tokens as $num => $token) {
        if (is_string($token)) {
            $last_space = false;
            continue;
        }
        switch ($token[0]) {
            case T_COMMENT:
            case T_DOC_COMMENT:
                $token[1] = "";
                break;
            case T_OPEN_TAG:
                $token[1] = "<?php";
                break;
            case T_CLOSE_TAG:
                $token[1] = "?>";
                break;
            case T_WHITESPACE:
                if (!$last_space) {
                    $token[1] = " ";
                    $last_space = true;
                } else {
                    $token[1] = "";
                }
                break;
            default:
                $last_space = false;
        }
        $tokens[$num] = $token[1];
    }

    return implode("", $tokens);
}
```

註：代碼沒有實現混淆功能, 僅演示了利用 token_get_all() 分析處理 PHP 文件.

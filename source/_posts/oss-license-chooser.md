---
title: 自由軟體授權條款選擇用小工具
id: 1321
categories:
  - 軟體開發
date: 2013-07-13 18:55:34
tags:
---

這是一個可以方便你快速選擇自由軟體授權條款的小工具.
請跟隨下面的小測驗進行.

<!--more-->

<div id="da">
1.當他人對您的程式進行修改後, 是否允許其封閉源程式?
<input type="radio" name="radio1" id="a1" onclick="jQuery('.chooser2,.chooser3,.choosed').hide('fast');jQuery('#da2').slideUp('fast');jQuery('#da1').show(500);" value="a">是</input><input type="radio" name="radio1" id="a2"  onclick="jQuery('.chooser2,.chooser3,.choosed').hide('fast');jQuery('#da1').slideUp('fast');jQuery('#da2').show(500);" value="a">否</input>
</div>

<div id="da1" class="chooser2" style="display:none;">
2.被修改後的文件是否必須放置版權說明?
<input type="radio" name="radio2" id="a11" onclick="jQuery('.chooser3,.choosed').hide('fast');jQuery('#da12').slideUp('fast');jQuery('#da11').show(500);" value="a">是</input><input type="radio" name="radio2" id="a12" onclick="jQuery('.chooser3,.choosed').hide('fast');jQuery('#da11').slideUp('fast');jQuery('#da12').show(500);" value="a">否</input>
</div>

<div id="da11" class="choosed" style="display:none;">
選擇**Apache許可證(Apache License)**
[http://www.apache.org/licenses/](http://www.apache.org/licenses/)
</div>

<div id="da12" class="chooser3" style="display:none;">
3.衍生版本是否可以使用你的名義進行銷售?
<input type="radio" name="radio3" id="a121" onclick="jQuery('.choosed').hide('fast');jQuery('#da122').slideUp('fast');jQuery('#da121').show(500);" value="a">是</input><input type="radio" name="radio3" id="a122" onclick="jQuery('.choosed').hide('fast');jQuery('#da121').slideUp('fast');jQuery('#da122').show(500);" value="a">否</input>
</div>

<div id="da121" class="choosed" style="display:none;">
選擇**MIT授權條款（The MIT License）**
[http://opensource.org/licenses/mit-license.php](http://opensource.org/licenses/mit-license.php)
</div>

<div id="da122" class="choosed" style="display:none;">
選擇**BSD授權條款（Berkeley Software Distribution license）**
[http://www.opensource.org/licenses/bsd-license.php](http://www.opensource.org/licenses/bsd-license.php)
</div>

<div id="da2" class="chooser2" style="display:none;">
2.新增代碼是否採用相同許可證?
<input type="radio" name="radio2" id="a21" onclick="jQuery('.chooser3,.choosed').hide('fast');jQuery('#da22').slideUp('fast');jQuery('#da21').show(500);" value="a">是</input><input type="radio" name="radio2" id="a22" onclick="jQuery('.chooser3,.choosed').hide('fast');jQuery('#da21').slideUp('fast');jQuery('#da22').show(500);" value="a">否</input>
</div>

<div id="da21" class="choosed" style="display:none;">
選擇**GNU 通用公眾授權條款（GNU General Public License）**
[http://www.gnu.org/licenses/gpl.html](http://www.gnu.org/licenses/gpl.html)
</div>

<div id="da22" class="chooser3" style="display:none;">
3.是否需要對源程式的修改之處提供說明文檔?
<input type="radio" name="radio3" id="a221" onclick="jQuery('.choosed').hide('fast');jQuery('#da222').slideUp('fast');jQuery('#da221').show(500);" value="a">是</input><input type="radio" name="radio3" id="a222" onclick="jQuery('.choosed').hide('fast');jQuery('#da221').slideUp('fast');jQuery('#da222').show(500);" value="a">否</input>
</div>

<div id="da221" class="choosed" style="display:none;">
選擇**Mozilla公共許可證（Mozilla Public License）**
[http://www.mozilla.org/MPL/](http://www.mozilla.org/MPL/)
</div>

<div id="da222" class="choosed" style="display:none;">
選擇**GNU宽通用公共许可证（GNU Lesser General Public License）**
[http://www.gnu.org/licenses/lgpl.html](http://www.gnu.org/licenses/lgpl.html)
</div>

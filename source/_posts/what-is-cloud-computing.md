---
title: 什麼是雲端運算
id: 2287
categories:
  - 網際網路
date: 2016-10-13 14:24:46
tags:
---

現在大家都爭先恐後地搞雲端運算, 各式各樣的雲端運算服務雨後春筍般發展起來.那麼,

### 什麼是雲端運算?

<!--more-->

[caption id="attachment_2329" align="alignnone" width="748"]![openstack-software-diagram](https://blog.7in0.me/wp-content/uploads/2016/10/openstack-software-diagram.png) 圖片來源： openstack.org[/caption]

網際網路上的雲端運算服務特徵和自然界的雲, 水迴圈具有一定的相似性, 因此, 雲端是一個相當貼切的比喻.根據美國國家標準和技術研究院的定義, 雲端運算服務應該具備以下幾條特徵：

*   隨需應變自助服務.
*   隨時隨地用任何網路裝置存取.
*   多人共享資源池.
*   快速重新部署靈活度.
*   可被監控與量測的服務.

一般認為還有如下特徵：

*   基於虛擬化技術快速部署資源或獲得服務.
*   減少用戶終端的處理負擔.
*   降低了用戶對於IT專業知識的依賴.

（引自 [維基百科-雲端運算](https://zh.wikipedia.org/wiki/%E9%9B%B2%E7%AB%AF%E9%81%8B%E7%AE%97), 參考文獻 NIST. [美國國家標準與技術研究院對雲計算的定義](http://www.kekaoyunfuwu.com/%E7%BE%8E%E5%9B%BD%E5%9B%BD%E5%AE%B6%E6%A0%87%E5%87%86%E4%B8%8E%E6%8A%80%E6%9C%AF%E7%A0%94%E7%A9%B6%E9%99%A2%E5%AF%B9%E4%BA%91%E8%AE%A1%E7%AE%97%E7%9A%84%E5%AE%9A%E4%B9%89/). 美國國家標準和技術研究院. 2011年9月 [2013年1月12日].）

說的直白一些, 一般認爲, 雲端運算是對於 IaaS, PaaS, SaaS 等的更進一步發展.

目前雲端運算根據基礎設施與軟硬體資源的設置, 分爲兩大類

### 公用雲和私有雲

私有雲（Private cloud）將基礎設施與軟硬體資源建立在防火牆內, 供機構內部使用.

常見的私有雲系統主要有 VMware, Hyper-V, OpenStack, CloudStack.

[caption id="attachment_2349" align="alignnone" width="1312"]![cgh-architecture-rc0](https://blog.7in0.me/wp-content/uploads/2016/10/CGH-architecture-rc0.png) HPE Helion OpenStack(R) Deployment architecture 來源：docs.hpcloud.com[/caption]

公用雲（Public cloud）是第三方提供基礎設施, 出售雲端服務, 一般是面向網際網路（Internet）.

常見的共用雲服務主要有 Microsoft Azure, Amazon AWS, Google GCE, IBM Softlayer.

[caption id="attachment_2343" align="alignnone" width="1585"]![aws](https://blog.7in0.me/wp-content/uploads/2016/10/AWS.png) AWS 參考架構 來源：aws.amazon.com[/caption]

此外還有社群雲（Community cloud）, 由幾個組織共享基礎設施, 用於支持特定的社群.

至於混合雲（Hybrid cloud）, 是兩種或以上不同形式的雲的結合.

[caption id="attachment_2337" align="alignnone" width="1708"]![azure-hybrid-cloud-scenarios](https://blog.7in0.me/wp-content/uploads/2016/10/azure-hybrid-cloud-scenarios.png) 混合雲 （Azure Hybrid Cloud Scenarios） 來源：hybridcloudtalk-160203043228[/caption]

說了這麼多, 讓我們再回到一開始的問題

### 什麼是雲端運算?

[caption id="attachment_media-12" align="alignnone" width="1394"]![Umbraco-CMS-for-light-to-medium-traffic-sites.jpeg](https://blog.7in0.me/wp-content/uploads/2016/10/Umbraco-CMS-for-light-to-medium-traffic-sites.jpeg) Umbraco CMS for light to medium traffic sites 來源：azure.microsoft.com[/caption]

計算, 網絡, 儲存, 應用 是雲計算的核心, 依賴資源的共享達成規模, 可以方便地擴展或壓縮項目佔用的資源, 大幅度提升資源的利用效率, 降低硬體成本.

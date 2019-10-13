---
title: VMware vSphere vCenter Server 安裝備忘
id: 2214
categories:
  - 應用技巧
date: 2016-06-03 11:55:20
tags:
---

前兩日接到任務要求在 5 片 Dell 1955 上安裝 VMware vSphere Hypervisor (ESXi) 和 VMware vSphere vCenter Server。

ESXi 作為 VMware vSphere 的伺服器作業系統，直接使用 DRAC/MC 虛擬介質載入 iso 檔灌裝，然後使用 vSphere Client 登入並填寫金鑰，整個過程沒有什麼難度，但 VMware vSphere vCenter Server 的安裝有許多需要注意的細節。

<!--more-->

VMware vSphere vCenter Server 同時提供 Windows 和 Linux 兩個不同版本套件，我選擇了 Windows 版本。

首先在任一 ESXi 中創建虛擬機器，至少 45GB 硬碟空間，8192 MB RAM。

灌 Windows Server 2008 R2，開啟 Windows Update，安裝 Microsoft SQL Server 2008 Express 和Microsoft SQL Server 2008 Express Service Pack 1.

然後通過 SQL Server Configuration Manager 啟用 SQL Server Browser 和 TCP/IP 通訊協定，否則之後安裝 VMware vSphere vCenter Server 時會提示啟動 invsvc 服務出錯。

在 SQL Server Management Studio 添加新 SQL Server 驗證帳戶（如 vcenter，賦予 sysadmin 角色），創建新的資料庫，命名為 vCenterDB，擁有者為 vcenter。

打開 ODBC，創建系統 DSN，類型 SQL Server Native Client，Name 為 vCenterDB，Server 填 127.0.0.1\SQLEXPRESS，使用 SQL Server 驗證帳戶。勾選“Change the default database to”，修改默認資料庫為 vCenterDB。

最後安裝 VMware vSphere vCenter Server，vCenter Server 使用 Microsoft Windows 系統帳戶，選用外部資料庫，選擇 vCenterDB DSN，填寫 SQL Server 驗證帳戶，完成安裝。

最後使用瀏覽器訪問 vCenter Server，默認帳戶名為 administrator@vsphere.local，密碼為安裝過程設置，登入後填寫金鑰並添加 ESXi 主機到 vCenter Server。

====2017/03/10更新====

升級 vCenter 到6.0時發現另一問題。

若 server 加域，在安裝過程中選擇域帳號，提示 指定的 vcenter server 沒有“作爲伺服器登錄”。

打開“本地組策略”，找到“計算機配置”-“Windows配置”-“安全設置”-“本地策略”-“用戶權限分配”，在右側按兩下“作爲服務登錄”，“添加用戶或組”，將指定帳號鍵入。

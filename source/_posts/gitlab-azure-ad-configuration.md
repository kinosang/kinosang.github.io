---
title: GitLab Azure AD 設定
id: 2784
categories:
  - 雜物箱
date: 2017-03-10 17:42:46
tags:
---

GitLab 自帶 Azure AD Oauth2 支援，詳見 GitLab 文檔 https://docs.gitlab.com/ce/integration/azure.html

<!--more-->

### 註冊企業應用程式

首先在 Azure 入口網站 Azure AD 管理界面註冊企業應用程式，到“Azure Active Directory”-“預設目錄”-“應用程式註冊”點“加入”，填寫“名稱”和“登入URL”創建企業應用程式，然後點“端點”查看鏈接內的TENANT ID。

然後到新創建的應用“設定”-“金鑰”中創建新的金鑰並記下來。

### 設定 GitLab

<pre>$ sudo -u git -H editor config/gitlab.yml

## OmniAuth settings
  omniauth:
    # Allow login via Twitter, Google, etc. using OmniAuth providers
    enabled: true
    # CAUTION!
    # This allows users to login without having a user account first. Define the allowed providers
    # using an array, e.g. ["saml", "twitter"], or as true/false to allow all providers or none.
    # User accounts will be created automatically when authentication was successful.
    allow_single_sign_on: ["azure_oauth2"] #在這裏設定 Azure Oauth2
    auto_link_ldap_user: true
    # Locks down those users until they have been cleared by the admin (default: true).
    block_auto_created_users: true

  omniauth_providers:
    - { name: 'azure_oauth2', args: { client_id: "CLIENT ID", client_secret: "CLIENT SECRET", tenant_id: "TENANT ID" } }

$ sudo systemctl restart gitlab
</pre>
<properties 
	pageTitle="Azure Multi-Factor Authentication と Active Directory フェデレーション サービスの概要" 
	description="Azure MFA および AD FS を開始する方法について説明する Azure Multi-Factor Authentication のページです。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" ms.topic="get-started-article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication と Active Directory フェデレーション サービスの概要



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

組織がオンプレミスの Active Directory と Azure Active Directory を AD FS を使ってフェデレーションする場合、Azure Multi-Factor Authentication を使用する次の 2 つのオプションを使用できます。

- Azure Multi-Factor Authentication または Active Directory フェデレーション サービスを使用したクラウド リソースのセキュリティ保護
- Azure Multi-Factor Authentication Server を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護

Azure Multi-Factor Authentication と AD FS を使用したリソースのセキュリティ保護の認証エクスペリエンスを次の表に要約します。

|認証エクスペリエンス - ブラウザー ベースのアプリ|認証エクスペリエンス - 非ブラウザー ベースのアプリ
:------------- | :------------- | :------------- |
Azure Multi-Factor Authentication を使用した Azure AD リソースのセキュリティ保護 |<li>認証の第 1 要素は AD FS を使用してオンプレミスで実行されます。</li> <li>第 2 要素は、クラウド認証を使用して実行される電話ベースの方式です。</li>|エンドユーザーはアプリ パスワードを使用して、サインインすることができます。
Active Directory フェデレーション サービスを使用した Azure AD リソースのセキュリティ保護 |<li>認証の第 1 要素は、AD FS を使用してオンプレミスで実行されます。</li><li>第 2 要素は、要求を受け入れることによりオンプレミスで実行されます。</li>|エンドユーザーはアプリ パスワードを使用して、サインインすることができます。

フェデレーション ユーザーに対するアプリ パスワードに関する注意事項があります。

- アプリ パスワードはクラウド認証を使用して検証されるため、フェデレーションはバイパスします。フェデレーションは、アプリ パスワードを設定するときにのみアクティブに使用されます。
- オンプレミスのクライアント アクセス制御の設定は、アプリ パスワードでは受け入れられません。
- アプリ パスワードでは、オンプレミスの認証ログ機能が失われます。
- dirsync のアカウントの無効化/削除には 3 時間かかり、クラウド ID のアプリのパスワードの無効化/削除も遅延します。

AD FS を使用した Azure Multi-Factor Authentication または Azure Multi-Factor Authentication Server の設定の詳細については、次を参照してください。

- [Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-cloud.md)
- [Azure Multi-Factor Authentication Server と Windows Server 2012 R2 AD FS を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=AcomDC_0810_2016-->
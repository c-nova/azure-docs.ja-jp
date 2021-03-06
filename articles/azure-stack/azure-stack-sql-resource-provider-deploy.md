---
title: Azure Stack での SQL データベースの使用 | Microsoft Docs
description: SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.lastreviewed: 03/18/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: ee64106c97a07e1b3ceb84c4ca932b19bc6d83b8
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652605"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>SQL Server リソース プロバイダーを Azure Stack にデプロイする

Azure Stack SQL Server リソース プロバイダーを使用して、SQL データベースを Azure Stack サービスとして公開します。 SQL リソース プロバイダーは、Windows Server 2016 Server Core 仮想マシン (VM) 上でサービスとして実行されます。

> [!IMPORTANT]
> SQL または MySQL をホストするサーバー上に項目を作成できるのは、リソース プロバイダーのみです。 リソース プロバイダー以外がホスト サーバー上に項目を作成すると、不一致状態になる可能性があります。

## <a name="prerequisites"></a>前提条件

Azure Stack SQL リソース プロバイダーをデプロイする前に、いくつかの前提条件が満たされている必要があります。 これらの要件を満たすには、特権エンドポイント VM にアクセスできるコンピューターで次の手順を実行します。

- まだ登録していない場合は、Azure Marketplace 項目をダウンロードできるよう、Azure に [Azure Stack を登録](azure-stack-registration.md)します。
- Azure モジュールと Azure Stack PowerShell モジュールは、このインストールを実行するシステムにインストールする必要があります。 そのシステムは、最新バージョンの .NET ランタイムを伴う Windows 10 または Windows Server 2016 のイメージである必要があります。 [PowerShell for Azure Stack をインストールする](./azure-stack-powershell-install.md)を参照してください。
- **Windows Server 2016 Datacenter - Server Core** イメージをダウンロードして、必要な Windows Server Core VM を Azure Stack Marketplace に追加します。
- SQL リソース プロバイダー バイナリをダウンロードした後、自己展開ツールを実行してコンテンツを一時ディレクトリに抽出します。 リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。

  |最小の Azure Stack バージョン|SQL RP バージョン|
  |-----|-----|
  |バージョン 1808 (1.1808.0.97)|[SQL RP バージョン 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |バージョン 1808 (1.1808.0.97)|[SQL RP バージョン 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
  |バージョン 1804 (1.0.180513.1)|[SQL RP バージョン 1.1.24.0](https://aka.ms/azurestacksqlrp11240)
  |     |     |

- データセンターの統合の前提条件を満たしていることを確認します。

    |前提条件|リファレンス|
    |-----|-----|
    |条件付き DNS フォワーダーが正しく設定されている。|[Azure Stack とデータセンターの統合 - DNS](azure-stack-integrate-dns.md)|
    |リソース プロバイダー用の受信ポートが開いている。|[Azure Stack とデータセンターの統合 - エンドポイントの公開](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI 証明書のサブジェクトと SAN が正しく設定されている。|[Azure Stack デプロイの必須 PKI 前提条件](azure-stack-pki-certs.md#mandatory-certificates) [Azure Stack デプロイの PaaS 証明書の前提条件](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>証明書

_統合システムのインストールのみを対象_。 [Azure Stack のデプロイの PKI 要件](./azure-stack-pki-certs.md#optional-paas-certificates)に関するページの「オプションの PaaS 証明書」セクションで説明されている SQL PaaS PKI 証明書を指定する必要があります。 **DependencyFilesLocalPath** パラメーターで指定された場所に .pfx ファイルを配置します。 ASDK システムの証明書は提供しないでください。

## <a name="deploy-the-sql-resource-provider"></a>SQL リソース プロバイダーをデプロイする

前提条件がすべてインストールされたら、**DeploySqlProvider.ps1** スクリプトを実行して SQL リソース プロバイダーをデプロイできます。 DeploySqlProvider.ps1 スクリプトは、Azure Stack のバージョンに応じてダウンロードした SQL リソース プロバイダーのバイナリの一部として抽出されます。

 > [!IMPORTANT]
 > リソース プロバイダーをデプロイする前に、新しい機能、修正、デプロイに影響を与える可能性のある既知の問題に関する詳細については、リリース ノートを確認してください。
 
SQL リソース プロバイダーをデプロイするには、管理者特権で**新しい** PowerShell ウィンドウ (PowerShell ISE ではない) を開き、SQL リソース プロバイダーのバイナリ ファイルを抽出したディレクトリに変更します。 既に読み込まれている PowerShell モジュールによって発生する可能性のある問題を回避するには、新しい PowerShell ウィンドウを使用することをお勧めします。

DeploySqlProvider.ps1 スクリプトを実行すると、次のタスクが完了します。

- Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
- ギャラリー パッケージを発行して、ギャラリーを使用して SQL データベースをデプロイできるようにします。
- ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
- ダウンロードした Windows Server 2016 Core イメージを使用して VM をデプロイした後、SQL リソース プロバイダーをインストールします。
- リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
- リソース プロバイダーをオペレーター アカウントのローカルの Azure Resource Manager に登録します。

> [!NOTE]
> SQL リソース プロバイダーのデプロイが開始されると、**system.local.sqladapter** リソース グループが作成されます。 このリソース グループに対して必要なデプロイが完了するまで最大で 75 分かかる可能性があります。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 パラメーター

コマンド ラインから次のパラメーターを指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **AzureEnvironment** | Azure Stack のデプロイに使用するサービス管理者アカウントの Azure 環境。 Azure AD のデプロイでのみ必須です。 サポートされている環境名は **AzureCloud**、**AzureUSGovernment**、または中国の Azure Active Directory を使用している場合は **AzureChinaCloud** です。 | AzureCloud |
| **DependencyFilesLocalPath** | 統合システムの場合のみ、証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 必要に応じて、ここで 1 つの Windows Update MSU パッケージをコピーできます。 | _省略可能_ (統合システムでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | エラーが 発生した場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>カスタム スクリプトを使用して SQL リソース プロバイダーをデプロイする

リソース プロバイダーをデプロイする際の手動による構成を除外するには、次のスクリプトをカスタマイズできます。  

必要に応じて、Azure Stack のデプロイに適した既定のアカウント情報とパスワードを変更します。


```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

リソース プロバイダーのインストール スクリプトが終了したら、最新の更新プログラムが表示されるようにブラウザーを最新の情報に更新します。

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

次の手順を実行すると、SQL リソース プロバイダーが正常にデプロイされたことを確認できます。

1. 管理ポータルにサービス管理者としてサインインします。
2. **[リソース グループ]** を選択します。
3. **system.\<location\>.sqladapter** リソース グループを選択します。
4. リソース グループの概要の概要ページで、失敗したデプロイは表示されていないはずです。
      ![SQL リソース プロバイダーのデプロイの確認](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)
5. 最後に、管理者ポータルで**仮想マシン**を選択して、SQL リソース プロバイダー VM が正常に作成され、実行されていることを確認します。

## <a name="next-steps"></a>次の手順

[ホスティング サーバーを追加する](azure-stack-sql-resource-provider-hosting-servers.md)

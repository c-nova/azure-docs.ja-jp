---
title: App Service Environment の管理アドレス - Azure
description: App Service Environment を管理するために使用される管理アドレスを一覧表示します。
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 632fa14bd96eaee2ca58b59dd855584c1fd961e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010096"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment の管理アドレス

App Service Environment (ASE) は、ユーザーの Azure Virtual Network (VNet) 内のサブネットに Azure App Service をデプロイしたものです。  ASE には、Azure App Service によって使用される管理プランからアクセスできる必要があります。  この ASE 管理トラフィックでは、ユーザーによって制御されるネットワークを走査します。 このトラフィックがブロックまたは誤ってルーティングされていると、ASE は中断されます。 ASE ネットワークの依存関係の読み取りの詳細については、「[App Service Environment のネットワークの考慮事項][networking]」を参照してください。 最初に参照できる ASE に関する一般情報については、「[App Service 環境の概要][intro]」を参照してください。

すべての ASE に、管理トラフィックの送信先パブリック VIP があります。 こうしたアドレスからの着信管理トラフィックは、ASE のパブリック VIP のポート 454 および 455 に送信されます。 このドキュメントでは、ASE に対する管理トラフィックの APP Service ソース アドレスの一覧を示します。 これらのアドレスは、AppServiceManagement という名前のサービス タグ内にあります。

ASE への受信管理トラフィックをロック ダウンするために、ネットワーク セキュリティ グループで AppServiceManagement という名前のサービス タグを使用できます。  

下に示すアドレスは、ルート テーブルで構成できます。 これは、強制トンネリングを使用する VNet での ASE 運用時に重要で、そうしないと、非対称ルーティングの問題が発生する可能性があります。 送信トラフィックがオンプレミスで送信される環境内で運用するために ASE を構成する方法の詳細については、「[強制トンネリングを使用した App Service Environment の構成][forcedtunnel]」を参照してください。

## <a name="list-of-management-addresses"></a>管理アドレスの一覧 ##

| リージョン | アドレス |
|--------|-----------|
| すべてのパブリック リージョン | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246, 52.225.177.153, 65.52.172.237, 52.151.25.45, 40.124.47.188 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>ネットワーク セキュリティ グループの構成

ネットワーク セキュリティ グループがあれば、個々のアドレスや、独自の構成を維持することについて心配する必要がありません。 すべてのアドレスを保持して最新の状態に保たれる AppServiceManagement という名前の IP サービス タグがあります。 この IP サービス タグを NSG で使用するには、ポータルに移動し、お使いのネットワーク セキュリティ グループの UI を開き、[受信セキュリティ規則] を選択します。 受信管理トラフィック用の既存のルールがある場合は、それを編集します。 この NSG がお使いの ASE で作成されたものでない場合や、すべて新しいものの場合は、**[追加]** を選択します。 [ソース] ドロップダウンで、**[Service Tag]\(サービス タグ)** を選択します。  [ソース サービス タグ] で **AppServiceManagement** を選択します。 ソース ポートの範囲を \*、[Destination]\(宛先) を **[Any]\(任意)**、宛先ポート範囲を **[454 455]**、プロトコルを **[TCP]**、[アクション] を **[許可]** に設定します。 ルールを作成する場合は、[優先度] を設定する必要があります。 

![サービス タグでの NSG の作成][1]

## <a name="configuring-a-route-table"></a>ルート テーブルの構成

すべての受信管理トラフィックが同じパスを戻ることができるように、管理アドレスは、インターネットの次ホップを指定してルート テーブル内に配置できます。 これらのルートは、強制トンネリングの構成時に必要です。 ルート テーブルを作成するには、ポータル、PowerShell、または Azure CLI を使用できます。  PowerShell プロンプトから Azure CLI を使用してルート テーブルを作成するコマンドは、次の通りです。 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32 
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32 
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32

ルート テーブルを作成した後は、お使いの ASE サブネットでそれを設定する必要があります。  

## <a name="get-your-management-addresses-from-api"></a>API から管理アドレスを取得する ##

次の API 呼び出しで、ご自身の ASE に一致する管理アドレスを一覧表示できます。

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API によって、ご自身の ASE に対するすべての受信アドレスが含まれる JSON ドキュメントが返されます。 アドレスの一覧には、管理アドレス、ご自身の ASE によって使用される VIP、および ASE サブネットのアドレス範囲自体が含まれています。  

[armclient](https://github.com/projectkudu/ARMClient) を使用して API を呼び出すには、次のコマンドを、サブスクリプション ID、リソース グループ、および ASE 名を置き換えて使用します。  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md

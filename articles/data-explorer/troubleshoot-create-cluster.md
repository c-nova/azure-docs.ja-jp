---
title: Azure データ エクスプローラーでクラスターを作成できない
description: この記事では、Azure データ エクスプローラーでクラスターを作成できない問題を解消する手順について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b95dabbdecd98902da3bf8217a14f41019c31e82
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757691"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>トラブルシューティング:Azure データ エクスプローラーでクラスターを作成できない

めったにないことですが、もし Azure データ エクスプローラーのクラスターを作成できない場合、次の手順に従ってください。

1. 適切なアクセス許可を確保します。 クラスターを作成するには、Azure サブスクリプションの*共同作成者*または*所有者*という役割に属している必要があります。 必要であれば、サブスクリプション管理者に問い合わせ、適切な役割を割り当ててもらいます。

1. Azure portal の **[クラスターの作成]** で入力したクラスター名に関連して確実に検証エラーが出ないようにします。

1. [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)を確認します。 クラスターを作成しようしているリージョンの Azure データ エクスプローラーの状態を探します。

    状態が**良好** (緑色のチェック マーク) でない場合は、状態が改善されてから、クラスターの作成をもう一度お試しください。

1. 問題解決にさらに手助けが必要な場合、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でサポート リクエストを作成してください。
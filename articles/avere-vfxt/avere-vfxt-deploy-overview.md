---
title: デプロイの概要 - Avere vFXT for Azure
description: Avere vFXT for Azure のデプロイの概要
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0c61db5e34ba58bb767b0bda773a54c8e65cd404
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991803"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - デプロイの概要

この記事では、Avere vFXT for Azure クラスターを起動して実行するために必要な手順の概要を示します。

Azure Marketplace から vFXT クラスターを作成する前後には、いくつかのタスクが必要となります。 開始から終了までのプロセスを明確に理解すると、必要な作業量の範囲を絞り込むことができます。 

## <a name="deployment-steps"></a>デプロイメントの手順

[システムを計画](avere-vfxt-deploy-plan.md)した後は、自分の Avere vFXT クラスターの作成を開始できます。 

Azure Marketplace の Azure Resource Manager テンプレートは、必要な情報を収集し、クラスター全体を自動的にデプロイします。 

vFXT クラスターを起動して実行したら、クライアントを vFXT クラスターに接続する方法、および必要に応じて、自分のデータを新しい BLOB ストレージ コンテナーに移動する方法を理解する必要があります。  

すべての手順の概要は次のとおりです。

1. 構成の前提条件 

   VM を作成する前に、Avere vFXT プロジェクトに対して新しいサブスクリプションを作成し、サブスクリプションの所有権を構成し、クォータを確認して必要に応じて増やすように要求し、Avere vFXT ソフトウェアの使用に対する使用条件に同意する必要があります。 詳細な手順については、「[Avere vFXT の作成準備](avere-vfxt-prereqs.md)」を参照してください。

1. クラスター ノードのアクセス ロールを作成する

   Azure では、[ロールベースのアクセス制御](../role-based-access-control/index.yml) (RBAC) を使用し、クラスター ノード VM を承認して特定のタスクを実行します。 たとえば、クラスター ノードは、IP アドレスをその他のクラスター ノードに割り当てまたは再割り当てすることができる必要があります。 クラスターを作成する前に、適切なアクセス権限を与えるロールを定義する必要があります。

   手順については、「[クラスター ノードのアクセス ロールを作成する](avere-vfxt-prereqs.md#create-the-cluster-node-access-role)」を参照してください。

   クラスター コントローラーもアクセス ロールを使用しますが、独自のロールを作成する代わりに、既定のロールである所有者をそのまま使用できます。 クラスター コントローラー用にカスタム ロールを作成する場合は、「[コントローラーのアクセス ロールのカスタマイズ](avere-vfxt-controller-role.md)」を参照してください。 

1. Avere vFXT クラスターを作成する 

   Azure Marketplace を使用して、Azure クラスター用の Avere vFXT を作成します。 テンプレートが、必要な情報を収集し、最終的な製品を作成するスクリプトを実行します。

   クラスターの作成には、以下の手順が伴います。これらはすべて、マーケットプレースのテンプレートによって行われます。 

   * (必要な場合) 新しいネットワーク インフラストラクチャとリソース グループの作成
   * *クラスター コントローラー*の作成  

     クラスター コントローラーは、Avere vFXT クラスターと同じ仮想ネットワークにあるシンプルな VM で、クラスターの作成と管理のために必要なカスタム ソフトウェアを備えています。 コントローラーによって vFXT ノードが作成され、クラスターが形成されます。また、その有効期間中にクラスターを管理するために、コマンド ライン インターフェイスも提供されます。

     デプロイ中に新しい vnet を作成すると、コントローラーがパブリック IP アドレスを持ちます。 つまり、コントローラーを vnet 外からクラスターに接続するためのジャンプ ホストとして使用できます。

   * クラスター ノード VM の作成

   * クラスターを形成するためのクラスター ノード VM の構成

   * (オプション) 新しい BLOB コンテナーの作成とクラスター用バックエンド ストレージとしての構成

1. クラスターを構成する 

   Avere vFXT の構成インターフェイス (Avere Control Panel) に接続して、クラスターの設定をカスタマイズします。 サポートの監視を許可し、オンプレミスのデータ センターを使用している場合は、ご利用のストレージ システムを追加します。

   * [vFXT クラスターへのアクセス](avere-vfxt-cluster-gui.md)
   * [サポートの有効化](avere-vfxt-enable-support.md)
   * [ストレージの構成](avere-vfxt-add-storage.md) (必要に応じて)

1. クライアントをマウントする

   負荷分散について、およびクライアント マシンでクラスターをマウントする方法については、「[Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md)」 (Avere vFXT クラスターをマウントする) のガイドラインに従います。

1. データの追加 (必要に応じて)

   Avere vFXT はスケーラブルな複数のクライアントのキャッシュであるため、新しいバックエンド ストレージ コンテナーにデータを移動する最適な方法は、複数のクライアント (マルチスレッドのファイル コピーの戦略) を使用することです。 詳細については、[vFXT クラスターへのデータの移動](avere-vfxt-data-ingest.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

「[Avere vFXT の作成準備](avere-vfxt-prereqs.md)」に進み、Avere vFXT for Azure をデプロイするための事前タスクを完了します。 
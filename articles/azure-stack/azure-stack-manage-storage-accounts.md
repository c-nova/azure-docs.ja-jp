---
title: Azure Stack のストレージ アカウントを管理する | Microsoft Docs
description: Azure Stack のストレージ アカウントを検索、管理、回復、および回収する方法について説明します
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: ceb092192924b79f1584d72b307e3bc3512c0025
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484171"
---
# <a name="manage-azure-stack-storage-accounts"></a>Azure Stack のストレージ アカウントを管理する

Azure Stack のストレージ アカウントを管理する方法について説明します。 ビジネス ニーズに応じてストレージ容量を検索、回復、および回収できます。

## <a name="find-a-storage-account"></a>ストレージ アカウントの検索

リージョン内のストレージ アカウントのリストは、次の方法を使って Azure Stack で表示できます。

1. [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[すべてのサービス]** > **[ストレージ]** > **[ストレージ アカウント]** の順に選択します。

   ![Azure Stack のストレージ アカウント](media/azure-stack-manage-storage-accounts/image4.png)

既定では、最初の 10 個のアカウントが表示されます。 リストの下部にある **[さらに読み込む]** リンクをクリックすると、さらに多くのアカウントをフェッチすることができます。

または

特定のストレージ アカウントに関心がある場合は、**フィルター処理して関連するアカウントのみをフェッチ**することができます。


**アカウントをフィルター処理するには:**

1. ウィンドウの上部にある **[フィルター]** を選択します。
2. [フィルター] ウィンドウでは、**アカウント名**、**サブスクリプション ID**、または**ステータス**を指定して、表示するストレージ アカウントのリストを細かく調整できます。 必要に応じてこれらを使用します。
3. 入力中、リストに自動的にフィルターが適用されます。  。
   
    ![Azure Stack のストレージ アカウントをフィルター処理する](media/azure-stack-manage-storage-accounts/image5.png)

4. フィルターをリセットするには、**[フィルター]** を選択し、選択をオフにして更新します。

(ストレージ アカウントのリスト ウィンドウの上部の) 検索テキスト ボックスを使用すると、アカウントのリスト内で選択したテキストを強調表示することができます。 この方法は、フル ネームまたは ID が簡単には見つからないときに役立ちます。

ここでフリー テキストを使用すると、関心のあるアカウントが見つけやすくなります。

![Azure Stack のストレージ アカウントを検索する](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>アカウントの詳細の確認
表示で関心のあるアカウントが見つかったら、そのアカウントを選択して特定の詳細を表示できます。 新しいウィンドウが開き、アカウントの種類、作成時、場所などのアカウントの詳細が表示されます。

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>削除されたキーの回復
削除したアカウントを回復する必要がある場合があります。

Azure Stack では、これを行う簡単な方法があります。

1. ストレージ アカウント リストを参照します。 詳細については、この記事の「ストレージ アカウントの検索」を参照してください。
2. そのリストで特定のアカウントを検索します。 場合によっては、フィルターを使用する必要があります。
3. アカウントの*状態*を確認します。 **[削除済み]** になっているはずです。
4. アカウントを選択して、その詳細ウィンドウを開きます。
5. このウィンドウの上部にある **[回復]** ボタンを選択します。
6. **[はい]** を選択して確定します。
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. その結果が正常であったことを受けて、この回復は *[process…wait]\(処理中…お待ちください\)* と表示されます。
   ポータルの上部にある "ベル" アイコンを選択して、進行状況インジケーターを表示することもできます。
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   回復したアカウントが正常に同期されたら、もう一度使用できます。

### <a name="some-gotchas"></a>注意事項
* 削除されたアカウントの状態が、**保有期間外**として表示される。
  
  "保有期間外" は、削除されたアカウントが保有期間を超過しており、回復できない可能性があることを意味します。
* 削除されたアカウントがアカウント リストに表示されない。
  
  削除されたアカウントが既にガベージ コレクトされている場合、アカウント リストに表示されません。 この場合、回復はできません。 この記事の「[容量の回収](#reclaim)」を参照してください。

## <a name="set-the-retention-period"></a>保有期間の設定
保有期間の設定では、クラウド オペレーターは削除されたアカウントが回復できる可能性がある期間を日数で指定できます (0 から 9999 日の間)。 既定の保有期間は 0 日に設定されています。 値を "0" に設定すると、削除されたすべてのアカウントがすぐに保有期間外になり、定期的なガベージ コレクションの対象としてマークされます。

**保有期間を変更するには:**

1. [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。
2. **[管理]** の下で、**[すべてのサービス]** > **[リージョン管理]** を選択します。
3. **[リソース プロバイダー]** > **[ストレージ]** > **[設定]** の順に選択します。 パスは、[ホーム] > [<*リージョン*> - リソース プロバイダー] > [ストレージ] です。
4. **[構成]** を選択してリテンション期間の値を編集します。

   日数を設定して保存します。
   
   この値はすぐに有効になり、リージョン全体に設定されます。

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>容量の回収
保有期間を設定することの副作用の 1 つは、削除されたアカウントが、保有期間外になるまで、容量を消費し続けることです。 クラウド オペレーターには、削除されたアカウントの保有期間が終了していなくても、その領域を回収する方法が必要な場合があります。

ポータルまたは PowerShell のいずれかを使用して、容量の回収ができます。

**ポータルを使用して容量を回収するには:**
1. [ストレージ アカウント] ウィンドウに移動します。 「ストレージ アカウントの検索」を参照してください。
2. ウィンドウの上部の **[Reclaim space]\(領域の回収\)** を選択します。
3. メッセージを確認し、**[OK]** を選択します。

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. 成功を通知するベルのアイコンがポータルに表示されるまで待ちます。

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. [ストレージ アカウント] ページを更新します。 削除されたアカウントは消去されているため、リストに表示されません。

PowerShell を使用して保有期間を明示的にオーバーライドし、すぐに容量を回収することもできます。

**PowerShell を使用して容量を回収するには:**   

1. Azure PowerShell がインストールおよび構成されていることを確認します。 それ以外の場合は、次の手順を実行します。 
   * Azure PowerShell の最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、[Azure PowerShell のインストールおよび構成方法](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)に関するページをご覧ください。
   Azure Resource Manager コマンドレットの詳細については、[Azure Resource Manager での Azure PowerShell の使用](https://go.microsoft.com/fwlink/?LinkId=394767)に関するページを参照してください。
2. 次のコマンドレットを実行します。

> [!NOTE]  
> これらのコマンドレットを実行すると、アカウントとその内容が完全に削除されます。 これは復旧できません。 慎重に使用してください。

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

詳細については、[Azure Stack PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/azure-stack/overview)を参照してください。
 

## <a name="next-steps"></a>次の手順

 - アクセス許可の管理については、「[ロールベースのアクセス制御の管理](azure-stack-manage-permissions.md)」を参照してください。
 - Azure Stack のストレージ容量の管理については、「[Azure Stack のストレージ容量を管理する](azure-stack-manage-storage-shares.md)」を参照してください。

---
title: Azure Stack で Visual Studio を使用してテンプレートをデプロイする | Microsoft Docs
description: Azure Stack で Visual Studio を使用してテンプレートをデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: f4fda8bbbb1bf88934f641644f89c319317ec58f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55237520"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Visual Studio を使用した Azure Stack でのテンプレートのデプロイ

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Visual Studio を使用して、Azure Resource Manager テンプレートを Azure Stack にデプロイできます。

## <a name="to-deploy-a-template"></a>テンプレートをデプロイするには

1. Visual Studio で Azure Stack を[インストールして接続](azure-stack-install-visual-studio.md)します。
2. Visual Studio を開きます。
3. **[ファイル]** を選択してから、**[新規]** を選択します。 **[新しいプロジェクト]** で、**[Azure リソース グループ]** を選択します。
4. 新しいプロジェクトの**名前**を入力し、**[OK]** を選択します。
5. **[Azure テンプレートの選択]** で、ドロップダウン リストから **[Azure Stack クイック スタート]** を選択します。
6. **[101-create-storage-account]**、**[OK]** の順に選択します。
7. 新しいプロジェクトで、**ソリューション エクスプローラー**で **[テンプレート]** ノードを展開して、使用可能なテンプレートを表示します。
8. **ソリューション エクスプローラー**で、プロジェクトの名前を選択し、**[デプロイ]** を選択します。 **[新しいデプロイ]** を選択します。
9. **[リソース グループに配置する]** で、**[サブスクリプション]** ドロップダウン リストを使用して、自分の Microsoft Azure Stack サブスクリプションを選択します。
10. **[リソース グループ]** ボックスの一覧から、既存のリソース グループを選択するか、新しいリソース グループを作成します。
11. **[リソース グループの場所]** の一覧から場所を選択して、**[デプロイ]** を選択します。
12. **[パラメーターの編集]** で、パラメーターの値 (テンプレートごとに異なる) を入力し、**[保存]** を選択します。

## <a name="next-steps"></a>次の手順

* [コマンド ラインを使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)
* [Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)

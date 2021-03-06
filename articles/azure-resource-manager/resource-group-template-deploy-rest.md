---
title: REST API とテンプレートを使用してリソースをデプロイする | Microsoft Docs
description: Azure Resource Manager と Resource Manager REST API を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: 3468f5b625911cd637b22e2c1d35a47fb7d7b0e4
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402832"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ

この記事では、Resource Manager REST API と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。  

テンプレートは要求本文またはファイルへのリンクに含めることができます。 ファイルを使用する場合は、ローカル ファイルまたは URI を通じてアクセスできる外部ファイルを使用できます。 テンプレートがストレージ アカウントにある場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## <a name="deployment-scope"></a>デプロイのスコープ

Azure サブスクリプション、またはサブスクリプション内のリソース グループのいずれかを、デプロイの対象として指定できます。 多くの場合、リソース グループをデプロイの対象にします。 サブスクリプション デプロイを使用するのは、サブスクリプション全体にポリシーとロールの割り当てを適用するときです。 また、リソース グループを作成し、それにリソースをデプロイする場合も、サブスクリプション デプロイを使用します。 使用するコマンドは、デプロイのスコープに応じて異なります。

**リソース グループ**にデプロイするには、[デプロイ - 作成](/rest/api/resources/deployments/createorupdate)に関するページのコマンドを使用します。

**サブスクリプション**にデプロイするには、[デプロイ - サブスクリプション スコープで作成](/rest/api/resources/deployments/createorupdateatsubscriptionscope)に関するページのコマンドを使用します。

この記事の例では、リソース グループ デプロイを使用します。 サブスクリプション デプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

## <a name="deploy-with-the-rest-api"></a>REST API でデプロイする
1. [一般的なパラメーターおよびヘッダー](/rest/api/azure/) (認証トークンを含む) を設定します。

1. 既存のリソース グループがない場合は、リソース グループを作成します。 ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、場所を指定します。 詳細については、「[リソース グループの作成](/rest/api/resources/resourcegroups/createorupdate)」を参照してください。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
   ```

   次のような要求本文を使用します。
   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. [テンプレート デプロイを検証する](/rest/api/resources/deployments/validate)操作を実行して、デプロイを実行する前に検証します。 デプロイをテストする場合、(次の手順で示すように) デプロイの実行時に必要なパラメーターを正確に指定します。

1. デプロイを作成します。 サブスクリプション ID、リソース グループの名前、デプロイの名前、テンプレートへのリンクを指定します。 テンプレート ファイルについては、「[パラメーター ファイル](#parameter-file)」を参照してください。 リソース グループを作成する REST API の詳細については、「[テンプレートのデプロイを作成する](/rest/api/resources/deployments/createorupdate)」を参照してください。 **[モード]** が **[増分]** に設定されていることに注意してください。 完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。 テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

   次のような要求本文を使用します。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
   }
   ```

    応答の内容、要求の内容、またはその両方を記録するには、要求に **debugSetting** を含めます。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    ストレージ アカウントを設定して、Shared Access Signature (SAS) トークンを使用することができます。 詳細については、「[Delegating Access with a Shared Access Signature (Shared Access Signature を使用したアクセスの委任)](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)」を参照してください。

1. テンプレートとパラメーターのファイルにリンクするのではなく、それらを要求本文に含めることができます。

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

5. テンプレートのデプロイの状態を取得します。 詳細については、「[テンプレートのデプロイに関する情報を取得](/rest/api/resources/deployments/get)」を参照してください。

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

デプロイに失敗した場合、デプロイ履歴から以前に成功したデプロイを自動的に再デプロイすることができます。 再デプロイを指定するには、要求本文内で `onErrorDeployment` プロパティを使用します。

このオプションを使用するには、ご自身のデプロイを履歴で特定できるように、デプロイの名前は一意でなければなりません。 一意の名前が付いていないと、失敗した現在のデプロイによって、履歴にある以前の成功したデプロイが上書きされる可能性があります。 このオプションは、ルート レベルのデプロイでのみ使用できます。 入れ子になったテンプレートのデプロイを再デプロイに使用することはできません。

現在のデプロイが失敗した場合、前回の成功したデプロイを再デプロイするには、次を使用します。

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

現在のデプロイが失敗した場合、特定のデプロイを再デプロイするには、次を使用します。

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

指定したデプロイは成功している必要があります。

## <a name="parameter-file"></a>パラメーター ファイル

デプロイ中にパラメーター ファイルを使用してパラメーター値を渡す場合は、次の例に示すような形式の JSON ファイルを作成する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

パラメーター ファイルのサイズは、64 KB 以下である必要があります。

パラメーターに機密性の高い値(パスワードなど) を提供する必要がある場合は、その値を Key Vault に追加します。 前の例で示したように、デプロイ中に Key Vault を取得します。 詳細については、「 [デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。 

## <a name="next-steps"></a>次の手順
* リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
* 非同期 REST 操作の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、「 [Deploy resources using .NET libraries and a template](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。


---
title: Azure Sentinel プレビューで Palo Alto Networks のデータを収集する | Microsoft Docs
description: Azure Sentinel で Palo Alto Networks のデータを収集する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 130982dc6adadd22037f395635a9525bf28bcedd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877093"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Palo Alto Networks のアプライアンスを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ログ ファイルを Syslog CEF として保存することにより、Azure Sentinel を任意の Palo Alto Networks アプライアンスに接続できます。 Azure Sentinel との統合により、Palo Alto Networks のすべてのログ ファイル データを簡単に分析および照会できます。 Azure Sentinel で CEF データを取り込む方法の詳細については、[CEF アプライアンスの接続](connect-common-event-format.md)に関するページを参照してください。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>手順 1:エージェントを使用してお使いの Palo Alto Networks アプライアンスを接続する

お使いの Palo Alto Networks アプライアンスを Azure Sentinel に接続するには、エージェントを専用のマシン (VM またはオンプレミス) にデプロイして、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 エージェントのデプロイは、自動または手動で行うことができます。 自動展開は、専用マシンが Azure に作成中の新しい VM である場合にのみ使用できます。 

または、既存の Azure VM、別のクラウド内の VM、またはオンプレミスのコンピューターに、手動でエージェントをデプロイすることもできます。

両方のオプションのネットワーク図を表示するには、[データ ソースの接続](connect-data-sources.md#agent-options)に関する説明を参照してください。


### <a name="deploy-the-agent-in-azure"></a>Azure でエージェントをデプロイする

1. Azure Sentinel ポータルで、**[データ収集]** をクリックして、アプライアンスの種類を選択します。 

1. **[Linux Syslog agent configuration]\(Linux Syslog エージェント構成\)** の下で、次を実行します。
   - Azure Sentinel エージェントが事前インストールされた、すべての必要な構成が含まれた新しいマシンを作成するには、前述の **[Automatic deployment]\(自動展開\)** を選択します。 **[自動展開]** を選択して、**[Automatic agent deployment]\(エージェントの自動展開\)** をクリックします。 これにより、お使いのワークスペースに自動的に接続される専用 VM の購入ページに移動します。 VM は**標準 D2s v3 (2 vCPU、8 GB メモリ)** であり、パブリック IP アドレスを持っています。
      1. **[カスタム デプロイ]** ページで詳細を入力し、ユーザー名とパスワードを選択し、使用条件に同意する場合は、VM を購入します。
      1. 接続ページに一覧表示されている設定を使用して、ログを送信するようにアプライアンスを構成します。 標準の共通イベント形式コネクタの場合、以下の設定を使用します。
         - プロトコル = UDP
         - ポート = 514
         - ファシリティ = Local-4
         - 形式 = CEF
   - 既存の VM を Azure Sentinel エージェントをインストールする専用の Linux マシンとして使用する場合、**[Manual deployment]\(手動配置\)** を選択します。 
      1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、**[Azure Linux virtual machine]\(Azure Linux 仮想マシン\)** を選択します。 
      1. 開いた **[仮想マシン]** 画面で、使用するマシンを選択し、**[接続]** をクリックします。
      1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
      1. これらのコマンドをコピーし、お使いのアプライアンス上で実行します。
          - rsyslog.d を選択した場合:
              
            1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
            
            1. 次の syslog デーモンを再起動します:  `sudo service rsyslog restart`
             
          - syslog-ng を選択した場合:

              1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。

              3. 次の syslog デーモンを再起動します:  `sudo service syslog-ng restart`
      2. 次のコマンドを使用して、Syslog エージェントを再起動します:  `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します:  `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>オンプレミスの Linux サーバーにエージェントを配置する

Azure を使用していない場合は、専用の Linux サーバーで実行するように Azure Sentinel エージェントを手動でデプロイします。


1. Azure Sentinel ポータルで、**[データ収集]** をクリックして、アプライアンスの種類を選択します。
1. 専用の Linux VM を作成するには、**[Linux Syslog agent configuration]** \(Linux Syslog エージェント構成\) の下で **[Manual deployment]\(手動配置\)** を選択します。
   1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、**[Non-Azure Linux machine]\(Azure ではない Linux マシン\)** を選択します。 
   1. 開いた **[ダイレクト エージェント]** 画面で、**[Linux 用エージェント]** を選択して、エージェントをダウンロードするか、次のコマンドを実行してお使いの Linux マシンにダウンロードします。`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
      1. これらのコマンドをコピーし、お使いのアプライアンス上で実行します。
         - rsyslog を選択した場合:
           1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
           3. 次の syslog デーモンを再起動します:  `sudo service rsyslog restart`
         - syslog-ng を選択した場合:
            1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
            3. 次の syslog デーモンを再起動します:  `sudo service syslog-ng restart`
      1. 次のコマンドを使用して、Syslog エージェントを再起動します:  `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します:  `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>手順 2:Palo Alto Networks のログを Syslog エージェントに転送する

Syslog のエージェントを介してお使いの Azure ワークスペースに CEF 形式で Syslog メッセージを転送するように Palo Alto Networks を構成します。
1.  「[Common Event Format (CEF) Configuration Guides (Common Event Format (CEF) 構成ガイド)](https://docs.paloaltonetworks.com/resources/cef)」にアクセスし、お使いのアプライアンスの種類に応じた PDF をダウンロードします。 ガイドのすべての手順に従って、CEF イベントを収集するように Palo Alto Networks アプライアンスを設定します。 

1.  「[Configure Syslog monitoring](https://aka.ms/asi-syslog-paloalto-forwarding)」(Syslog の監視を構成する) に移動し、ステップ 2 と 3 に従って、Palo Alto Networks アプライアンスから Azure Sentinel への CEF イベントの転送を構成します。

    1. **[Syslog server format]\(Syslog サーバーの形式\)** を **[BSD]** に設定します。
    1. **[Facility number]\(機能番号\)** を Syslog エージェントで設定したものと同じ値に設定します。

> [!NOTE]
> PDF からのコピー/貼り付け操作では、テキストが変更され、ランダムな文字が挿入される可能性があります。 これを回避するには、この例で示すように、テキストをエディターにコピーし、ログの形式を崩す可能性がある文字を削除した後、貼り付けます。
 
  ![CEF テキストのコピーの問題](./media/connect-cef/paloalto-text-prob1.png)

## <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. Syslog エージェントで、自分のログが正しいポートに到達していることを確認します。 Syslog エージェント マシンで次のコマンドを実行します:`tcpdump -A -ni any  port 514 -vv` このコマンドでは、デバイスから Syslog マシンにストリーミングされるログを表示します。ログが、正しいポートとファシリティでソース アプライアンスから受信されていることを確認します。
2. Syslog デーモンとエージェント間で通信が行われていることを確認します。 Syslog エージェント マシンで次のコマンドを実行します。`tcpdump -A -ni any  port 25226 -vv` このコマンドによって、デバイスから Syslog コンピューターにストリーミングされるログが表示されます。ログがエージェント上でも受信されていることを確認します。
3. これらの両方のコマンドで正常な結果が表示された場合は、Log Analytics を調べて自分のログが到着しているかどうかを確認してください。 これらのアプライアンスからストリーミングされるすべてのイベントは、Log Analytics で `CommonSecurityLog` 型の下に未加工の形式で表示されます。
1. エラーがあるかどうか、またはログが到着しているかどうかを確認するには、次のファイルを調べます:  `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. 自分の Syslog メッセージの既定のサイズが 2048 バイト (2 KB) に制限されていることを確認します。 ログが長すぎる場合は、次のコマンドを使用して security_events.conf を更新します:  `message_length_limit 4096`
6. Log Analytics で Palo Alto Networks イベントに関連するスキーマを使用するために、**CommonSecurityLog** を検索します。







## <a name="next-steps"></a>次の手順
このドキュメントでは、Palo Alto Networks アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [お使いのデータと潜在的な脅威を可視化する](quickstart-get-visibility.md)方法を確認する。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)を開始する。


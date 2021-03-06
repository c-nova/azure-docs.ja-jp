---
title: Azure Maps でサポートされている Web ブラウザー | Microsoft Docs
description: Azure Maps Web SDK でサポートされている Web ブラウザーについて説明します
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500935"
---
# <a name="supported-web-browsers"></a>サポートされている Web ブラウザー

Azure Maps Web SDK は、Web ブラウザーがマップ コントロールの読み込みと表示をサポートするために最低限必要な WebGL 機能が搭載されているかを検出するために、ヘルパー関数 [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) を提供しています。 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

Azure Maps Web SDK は、次の Web ブラウザーをサポートしています。

## <a name="desktop"></a>デスクトップ

- Microsoft Edge の現在と以前のバージョン 
- Chrome の現在と以前のバージョン 
- Firefox の現在と以前のバージョン 
- Safari (Mac OS X) の現在と以前のバージョン 

「[レガシ ブラウザーを対象にする](#Target-Legacy-Browsers)」も参照してください。

## <a name="mobile"></a>モバイル

-  Android
    * Android 6.0 以降の Chrome の現在のバージョン
    * Android 6.0 以降の Chrome WebView
- iOS
    * 現在と以前のメジャー バージョンの iOS の Mobile Safari
    * 現在と以前のメジャー バージョンの iOS の UIWebView と WKWebView
    * 現在のバージョンの iOS 用 Chrome

> [!TIP]
> モバイル アプリケーション ビューの WebView コントロール内にマップを埋め込む場合、CDN にホストされたバージョンの SDK を参照するよりも、[Azure Maps Web SDK の npm パッケージ](https://www.npmjs.com/package/azure-maps-control)を使用することをお勧めします。 SDK が既にユーザーのデバイスに存在し、実行時にダウンロードする必要がないので、読み込み時間が減少します。

## <a name="nodejs"></a>Node.js

次の Web SDK モジュールは、Node.js もサポートします。

- サービス モジュール ([ドキュメント](how-to-use-services-module.md) | [npm モジュール](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>レガシ ブラウザーを対象にする

WebGL を一部しかサポートしていない古い Web ブラウザーを対象とする必要がある場合は、[leaflet](https://leafletjs.com/) などのオープン ソース マップ コントロールと組み合わせて Azure Maps Services を使用することをお勧めします。 

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a>」Pen を表示します。
</iframe>
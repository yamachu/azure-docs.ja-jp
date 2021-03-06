---
title: 'クイック スタート: Bing Video Search (Python)'
titlesuffix: Azure Cognitive Services
description: Bing Video Search API をすぐに使い始めるのに役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: ccc27481289ffc686e3e480685ba421c762e3718
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161084"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>クイック スタート: Python での Bing Video Search API

このチュートリアルでは、Azure 上の Microsoft Cognitive Services の一部である Bing Video Search API の使用方法を示します。 API の技術的な詳細については、[API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)に関するページをご覧ください。

バインダーの起動バッジをクリックして、次の例を [MyBinder](https://mybinder.org) 上で Jupyter Notebook として実行できます。 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>前提条件
[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search APIs** を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。または、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

## <a name="running-the-walkthrough"></a>チュートリアルの実行

まず、`subscription_key` に Bing API サービス用の API キーを設定します。


```python
subscription_key = None
assert subscription_key
```

次に、`search_url` エンドポイントが正しいことを確認します。 このドキュメントの作成時点では、Bing Search APIs にはエンドポイントは 1 つだけ使用されます。 承認エラーが発生した場合は、Azure ダッシュ ボードで Bing Search エンドポイントに対してこの値をダブルチェックしてください。


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

子猫のビデオを検索するように `search_term` を設定します


```python
search_term = "kittens"
```

次のブロックでは、Python 内の `requests` ライブラリを使用して、Bing Search API を呼び出し、JSON オブジェクトとして結果を返します。 `headers` を介して API キーを渡し、`params` ディクショナリを介して検索用語を渡していることに注意してください。 検索結果をフィルター処理するために使用できる全オプションの一覧を確認するには、「[REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)」ドキュメントをご覧ください。


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

`search_results` オブジェクトには、関連するビデオと豊富なメタデータが含まれています。 いずれかのビデオを表示するには、その `embedHtml` プロパティを使用して、`IFrame` に挿入します。


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [動画のページング](paging-videos.md)
> [サムネイル画像のサイズ変更とトリミング](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>関連項目 

 [Web でビデオを検索する](search-the-web.md)[試してみる](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)

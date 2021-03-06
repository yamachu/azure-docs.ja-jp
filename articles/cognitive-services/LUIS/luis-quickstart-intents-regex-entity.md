---
title: 'チュートリアル 3: 正規表現に一致するデータ - 正しい形式のデータを抽出する'
titleSuffix: Azure Cognitive Services
description: 正規表現エンティティを使用して一貫した形式のデータを発話から抽出する
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 82d7e5ab57d9cf12c6917386282182faacb07725
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282392"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>チュートリアル 3: 正しい形式のデータを抽出する
このチュートリアルでは、**正規表現**エンティティを使用して一貫した形式のデータを発話から抽出するよう、Human Resources アプリを修正します。

エンティティの目的は、発話に含まれている重要なデータを抽出することです。 このアプリでは正規表現エンティティを使用して、書式設定された人事 (HR) のフォーム番号を発話から抽出します。 発話の意図は常に機械学習によって決定されますが、この特定のエンティティ型は機械学習されません。 

**発話の例を次に示します。**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
正規表現がこのデータの種類に適しているのは次の場合です。

* データが正しい形式である。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する
> * FindForm 意図を追加する
> * 正規表現エンティティを追加する 
> * トレーニング
> * [発行]
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する
最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1. [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `regex` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。 

## <a name="findform-intent"></a>FindForm 意図

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **[Create new intent]\(意図の新規作成\)** を選択します。 

3. ポップアップ ダイアログ ボックスに「`FindForm`」と入力して、**[完了]** を選択します。 

    ![検索ボックスにユーティリティが表示されている [Create new intent]\(意図の新規作成\) ダイアログのスクリーンショット](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |hrf-123456 の URL は何ですか。|
    |hrf-345678 の場所はどこですか。|
    |hrf-456098 はいつ更新されましたか。|
    |John Smith は先週、hrf-234639 を更新しましたか。|
    |hrf-345123 のバージョン数はいくつですか。|
    |フォーム hrf-123456 を誰が承認する必要がありますか。|
    |hrf 345678 をサインオフする必要があるユーザー数はいくつですか。|
    |hrf-234123 の日付はいつですか。|
    |hrf 546234 の作成者は誰ですか。|
    |hrf-456234 のタイトルは何ですか。|

    [ ![新しい発話が強調表示されている [Intents]\(意図\) ページのスクリーンショット](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    アプリケーションには、前のチュートリアルで追加された事前構築済みの番号エンティティがあるため、各フォーム番号がタグ付けされています。 クライアント アプリケーションにとってはこれで十分な場合がありますが、番号は、番号の種類と共にラベル付けされません。 適切な名前で新しいエンティティを作成すると、クライアント アプリケーションは、LUIS から返されたときに適切にエンティティを処理できます。

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>正規表現エンティティ 
フォーム番号と一致する正規表現のエンティティは `hrf-[0-9]{6}` です。 この正規表現はリテラル文字の `hrf-` と一致しますが、大文字小文字およびカルチャのバリアントは無視します。 0 ～ 9 の 6 桁の数字と正確に一致します。

HRF は `human resources form` の略です。

LUIS では、発話が意図に追加されるときに、発話をトークン化します。 これらの発話のトークン化では、`Where is HRF - 123456?` のように、ハイフンの前後にスペースを追加します。トークン化される前に、正規表現が raw 形式の発話に適用されます。 _raw_ 形式に適用されているため、正規表現で単語の境界を処理する必要はありません。 

次の手順で、正規表現エンティティを作成して、HRF 番号形式を LUIS に認識させます。

1. 左のパネルで **[エンティティ]** を選びます。

2. [エンティティ] ページで **[新しいエンティティの作成]** ボタンを選択します。 

3. ポップアップ ダイアログで、新しいエンティティ名 `HRF-number` を入力して、エンティティ型として **RegEx** を選択し、**Regex** の値に「`hrf-[0-9]{6}`」と入力してから、**[完了]** を選択します。

    ![ポップアップ ダイアログ設定の新しいエンティティ プロパティのスクリーン ショット](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. 左側のメニューから **[Intents]\(意図\)**、**[FindForm]** 意図の順に選択して、発話に正規表現のラベルが付与されていることを確認します。 

    [![既存のエンティティと正規パターンでのラベル発話のスクリーンショット](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    エンティティは機械学習エンティティではないため、ラベルは、作成されるとすぐに発話に適用されて LUIS Web サイトに表示されます。

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>[発行]

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`When were HRF-123456 and hrf-234567 published in the last year?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`FindForm` 意図と 2 つのフォーム番号 `HRF-123456` と `hrf-234567` が返される必要があります。

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    発話の番号が 2 回返されます。1 回は新しいエンティティの `hrf-number` として、もう 1 回は構築済みエンティティの `number` としてです。 この例に示すように、発話は複数のエンティティを保持でき、同じ種類のエンティティが複数あってもかまいません。 正規表現のエンティティを使用することで、LUIS は名前付けされたデータを抽出します。このデータは、JSON 応答を受信するクライアント アプリケーションにとって、プログラムでより有益です。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、新しい意図を作成し、発話例を追加し、正しい形式のデータを発話から抽出するための正規表現エンティティを作成しました。 トレーニングおよびアプリの発行後、エンドポイントのクエリによって意図を識別し、抽出されたデータを取得しました。

> [!div class="nextstepaction"]
> [リスト エンティティについて学習する](luis-quickstart-intent-and-list-entity.md)


---
title: Azure CLI スクリプトのサンプル - SignalR Service の作成 | Microsoft Docs
description: Azure CLI スクリプトのサンプル - SignalR Service の作成
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: d64e43ad6dc021676fb9483574e0d710cd0a766f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998646"
---
# <a name="create-a-signalr-service"></a>SignalR Service の作成 

このサンプル スクリプトでは、ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、Azure CLI の *signalr* 拡張機能を使います。 このサンプル スクリプトを使う前に、次のコマンドを実行して Azure CLI の *signalr* 拡張機能をインストールしてください。

```azurecli-interactive
az extension add -n signalr
```

このスクリプトでは、新しい SignalR Service リソースと新しいリソース グループを作成します。 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

新しいリソース グループに対して生成された実際の名前はメモしておいてください。 このリソース グループ名は、すべてのグループ リソースを削除するときに使います。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az signalr create](/cli/azure/group#az-group-create) | Azure SignalR Service リソースを作成します。 |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | キーを一覧表示します。これらのキーは、SignalR でリアルタイム コンテンツの更新をプッシュする際、アプリケーションによって使われます。 |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure SignalR Service のその他の CLI サンプル スクリプトについては、[Azure SignalR サービスのドキュメント](../signalr-cli-samples.md)をご覧ください。

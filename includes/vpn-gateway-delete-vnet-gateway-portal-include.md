---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198147"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>ステップ 1: 仮想ネットワーク ゲートウェイへの移動

1. [Azure Portal](https://portal.azure.com) で **[すべてのリソース]** に移動します。 
2. 仮想ネットワーク ゲートウェイのページを開くために、削除する仮想ネットワーク ゲートウェイに移動して、それをクリックします。

### <a name="step-2-delete-connections"></a>ステップ 2: 接続の削除

1. 仮想ネットワーク ゲートウェイのページで、**[接続]** をクリックしてゲートウェイへのすべての接続を表示します。
2. 接続名の行で **[...]** をクリックして、ドロップダウンから **[削除]** を選択します。
3. 確認画面で **[はい]** をクリックして、接続を削除します。 複数の接続がある場合は、接続を一つずつ削除します。

### <a name="step-3-delete-the-virtual-network-gateway"></a>ステップ 3: 仮想ネットワーク ゲートウェイの削除

S2S 構成に加えてこの VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されることに注意してください。

1. 仮想ネットワーク ゲートウェイのページで、**[概要]** をクリックします。
2. **[概要]** ページで、**[削除]** をクリックしてゲートウェイを削除します。

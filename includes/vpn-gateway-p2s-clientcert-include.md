---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 01a62fe7abb8a79f9afc08c0ff707cdfbb97ddac
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44343223"
---
ポイント対サイトで VNet に接続するすべてのクライアント コンピューターには、クライアント証明書がインストールされている必要があります。 クライアント証明書はルート証明書から生成され、各クライアント コンピューターにインストールされます。 有効なクライアント証明書がインストールされていない状態でクライアントが VNet に接続した場合、認証に失敗します。

クライアントごとに一意の証明書を生成することも、複数のクライアントに同じ証明書を使用することもできます。 一意のクライアント証明書を生成する利点は、1 つの証明書を失効させることができる点です。 そうでなければ、複数のクライアントが同じクライアント証明書を使用していて、その証明書を失効させる必要がある場合は、認証にその証明書を使用するすべてのクライアントに新しい証明書を生成してインストールする必要があります。

クライアント証明書は、次の方法で生成できます。

- **エンタープライズ証明書:**

  - エンタープライズ証明書ソリューションを使用している場合は、"domain name\username" 形式ではなく、共通名の値の形式 "name@yourdomain.com" を使用してクライアント証明書を生成します。
  - クライアント証明書が、使用リストの最初の項目としてスマート カード ログオンなどではなく "クライアント認証" が指定されている "ユーザー" 証明書テンプレートに基づいていることを確認します。証明書を確認するには、クライアント証明書をダブルクリックし、**[詳細]、[拡張キー使用法]** の順に選択して表示します。

- **自己署名ルート証明書:** P2S 証明書に関する以下のいずれかの記事の手順に従うことが重要です。 そうしないと、作成するクライアント証明書と P2S 接続との互換性がなくなり、クライアントが接続しようとするとエラー メッセージを受信します。 以下のどの記事の手順でも、互換性のあるクライアント証明書が生成されます。 

  * [Windows 10 PowerShell の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): これらの手順で証明書を生成するには、Windows 10 および PowerShell が必要です。 生成される証明書は、サポートされている任意の P2S クライアントにインストールすることができます。
  * [MakeCert の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): 証明書を生成するために使用する Windows 10 コンピューターにアクセスできない場合は、MakeCert を使用します。 MakeCert は非推奨になりましたが、まだ MakeCert を使用して証明書を生成することができます。 生成される証明書は、サポートされている任意の P2S クライアントにインストールすることができます。
  * [Linux の手順](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  上記の手順で自己署名ルート証明書からクライアント証明書を生成した場合、その作業に使用したコンピューターに自動的にクライアント証明書がインストールされます。 クライアント証明書を別のクライアント コンピューターにインストールする場合は、その証明書を .pfx として、証明書チェーン全体と共にエクスポートする必要があります。 そうすることで、クライアントが正常に認証されるために必要なルート証明書情報が含まれている .pfx ファイルが作成されます。 証明書をエクスポートする手順については、[クライアント証明書のエクスポート](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)に関するページを参照してください。
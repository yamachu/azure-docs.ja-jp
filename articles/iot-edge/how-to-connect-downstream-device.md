---
title: Azure IoT Edge を使用してダウンストリーム デバイスを構成する | Microsoft Docs
description: Azure IoT Edge ゲートウェイ デバイスを経由して接続するダウンストリーム デバイスまたはリーフ デバイスを構成する方法。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bd9d8c4a75df0d9070476fb72d2b60f5cb182a87
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566229"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する

Azure IoT Edge を使用して透過的ゲートウェイのシナリオを実現し、1 つ以上のデバイスから、IoT Hub への接続を維持する単一のゲートウェイ デバイスを経由して、メッセージを渡すことができます。 ゲートウェイ デバイスを構成した後、ダウンストリーム デバイスを安全に接続する方法を知る必要があります。 

この記事では、ダウンストリーム デバイスの接続に関する一般的な問題を示し、ダウンストリーム デバイスを設定する手順を説明します。内容は次のとおりです。 

* トランスポート層セキュリティ (TLS) と証明書の基礎について説明します。 
* TLS ライブラリが異なる複数のオペレーティング システム上でどのように機能するか、また、各オペレーティング システムによって証明書がどのように処理されるかについて説明します。
* 作業を開始するときに役立つ、複数の言語で用意された Azure IoT のサンプルを紹介します。 

この記事では、"*ゲートウェイ*" および "*IoT Edge ゲートウェイ*" という用語は、透過的ゲートウェイとして構成された IoT Edge デバイスを指します。 

## <a name="prerequisites"></a>前提条件

この記事に記載された手順を行うには、2 つのデバイスが、使用できるように準備されている必要があります。

1. 透過的ゲートウェイとして設定した IoT Edge デバイス。 
    [透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)

    ゲートウェイ デバイスを構成した後、ゲートウェイから **azure-iot-test-only.root.ca.cert.pem** 証明書をコピーし、お使いのダウンストリーム デバイス上のあらゆる場所で使用できるようにします。 

2. IoT Hub から取得したデバイス ID を持つダウンストリーム デバイス。 
    IoT Edge デバイスをダウンストリーム デバイスとして使用することはできません。 代わりに、IoT Hub に通常の IoT デバイスとして登録したデバイスを使用します。 ポータルの **[IoT デバイス]** セクションで新しいデバイスを登録できます。 または、Azure CLI を使用して[デバイスを登録](../iot-hub/quickstart-send-telemetry-c.md#register-a-device)することもできます。 接続文字列をコピーし、後続のセクションで使用できるようにしておきます。 

    現在のところ、対称キー認証が適用されるダウンストリーム デバイスのみを、IoT Edge ゲートウェイ経由で接続できます。 証明機関の署名がある X.509 証明書と X.509 自己署名証明書は、現在のところサポートされていません。 

## <a name="prepare-a-downstream-device"></a>ダウンストリーム デバイスを準備する

ダウンストリーム デバイスには、[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) クラウド サービスで作成された ID を持つ任意のアプリケーションまたはプラットフォームを指定できます。 多くの場合、これらのアプリケーションでは [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md) が使用されます。 実際には、ダウンストリーム デバイスには IoT Edge ゲートウェイ デバイスそのもので実行されているアプリケーションも指定できます。 

ダウンストリーム デバイスを IoT Edge ゲートウェイに接続するには、次の 2 つのものが必要です。

1. ゲートウェイに接続するための情報を追加した IoT Hub デバイス接続文字列を使用して構成されたデバイスまたはアプリケーション。 

    接続文字列の形式は、`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;` のようになります。 ゲートウェイ デバイスのホスト名を指定した **GatewayHostName** プロパティを接続文字列の末尾に追加します。 **GatewayHostName** の値は、ゲートウェイ デバイスの config.yaml ファイル内に記述された **hostname** の値と一致している必要があります。 

    最終的な文字列は、`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com` のようになります。

2. デバイスまたはアプリケーションは、ゲートウェイ デバイスへの TLS 接続を検証するために、ゲートウェイの**ルート CA** 証明書または**所有者 CA** 証明書を信頼する必要があります。 

    この比較的複雑な手順については、この記事の以降の部分で詳しく説明します。 この手順は、次の 2 つのうちいずれかの方法で実行できます。つまり、CA 証明書をオペレーティング システムの証明書ストアにインストールする方法と、(特定の言語について) Azure IoT SDK を使用してアプリケーション内で証明書を参照する方法です。

## <a name="tls-and-certificate-fundamentals"></a>TLS と証明書の基礎

ダウンストリーム デバイスを IoT Edge に安全に接続することに関する課題は、インターネット上で発生する他の安全なクライアント/サーバー通信の課題と同様です。 クライアントとサーバーの間では、[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) を使用してインターネット上での安全な通信が行われます。 TLS は、証明書と呼ばれる標準の[公開キー基盤 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) コンストラクトを使用して構築されています。 TLS はかなり複雑な仕様であり、2 つのエンドポイントを保護することに関連した広範囲なトピックに対応していますが、以降のセクションでは、デバイスを IoT Edge ゲートウェイに安全に接続するために何が必要かについて簡潔に説明します。

クライアントがサーバーに接続するとき、*サーバー証明書チェーン*と呼ばれる証明書のチェーンがサーバーから提供されます。 証明書チェーンは一般的に、ルート証明機関 (CA) 証明書、1 つ以上の中間 CA 証明書、最後にサーバーの証明書そのものから構成されます。 クライアントは、サーバー証明書チェーン全体を暗号で検証し、サーバーとの信頼関係を確立します。 このようなクライアントによるサーバー証明書の検証は、*サーバー認証*と呼ばれます。 サーバー証明書チェーンを検証するには、サーバーの証明書を作成 (または発行) するために使われたルート CA 証明書のコピーがクライアントに必要です。 通常、Web サイトに接続する時点で、ブラウザーにはよく使われる CA 証明書が事前に構成されているので、クライアントのプロセスはシームレスです。 

デバイスが Azure IoT Hub に接続するとき、デバイスがクライアントであり、IoT Hub クラウド サービスがサーバーです。 IoT Hub クラウド サービスは、**Baltimore CyberTrust Root** と呼ばれるルート CA 証明書によってサポートされています。この証明書は公開されていて入手可能であり、広く使われています。 IoT Hub CA 証明書は、ほとんどのデバイスに既にインストールされているため、多くの TLS 実装 (OpenSSL、Schannel、LibreSSL など) において、サーバー証明書の検証時にその証明書が自動的に使用されます。 IoT Hub に正常に接続できたデバイスから IoT Edge ゲートウェイに接続しようとして、問題が発生する場合があります。

デバイスが IoT Edge ゲートウェイに接続するときは、ダウンストリーム デバイスがクライアントで、ゲートウェイ デバイスがサーバーです。 Azure IoT Edge を使用すると、オペレーター (またはユーザー) が、適合するゲートウェイ証明書チェーンを構築できます。 オペレーターは、Baltimore のような公開 CA 証明書を使用することも、自己署名 (または社内) ルート CA 証明書を使用することもできます。 多くの場合、公開 CA 証明書には、関連するコストが生じるため、一般的には運用環境シナリオで使用されます。 開発とテストに適しているのは自己署名 CA 証明書です。 前提条件のセクションに示されている透過的ゲートウェイの設定に関する記事では、自己署名のルート CA 証明書を使用しています。 

IoT Edge ゲートウェイ用に自己署名のルート CA 証明書を使用するときは、ゲートウェイに接続しようとするダウンストリーム デバイスすべてにその証明書をインストールまたは提供する必要があります。 

IoT Edge 証明書の詳細と、運用環境での実装の詳細については、「[IoT Edge 証明書の使用方法の詳細](iot-edge-certs.md)」を参照してください。

## <a name="install-certificates-using-the-os"></a>OS を使用して証明書をインストールする

この記事では、ルート CA 証明書のことを*所有者 CA* と言います (前提条件にあるゲートウェイに関する記事のスクリプトで、この用語が使用されているため)。 

オペレーティング システムの証明書ストアに所有者 CA 証明書をインストールすると、一般的に、ほとんどのアプリケーションで所有者 CA 証明書を使用できるようになります。 いくつか例外はあり、NodeJS アプリケーションのように、OS 証明書ストアを使用せずにノード ランタイムの内部証明書ストアを使用するものがあります。 オペレーティング システム レベルで証明書をインストールできない場合は、この記事の後半にある、アプリケーション内で Azure IoT SDK と証明書を使用するための言語固有の例を参照してください。 

### <a name="ubuntu"></a>Ubuntu

次のコマンドは、Ubuntu ホストに CA 証明書をインストールする方法を示す例です。 このサンプルは、前提条件にある記事の **azure-iot-test-only.root.ca.cert.pem** 証明書を使用していること、およびダウンストリーム デバイス上の場所に証明書をコピー済みであることを前提にしています。  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

"/etc/ssl/certs の証明書を更新中..." という内容のメッセージが表示されます。1 added, 0 removed; done." (/etc/ssl/certs の証明書を更新しています... 1 個追加、0 個削除されました。完了しました) というメッセージが表示されます。

### <a name="windows"></a>Windows

次の手順は、Windows ホストに CA 証明書をインストールする方法の例です。 このサンプルは、前提条件にある記事の **azure-iot-test-only.root.ca.cert.pem** 証明書を使用していること、およびダウンストリーム デバイス上の場所に証明書をコピー済みであることを前提にしています。  

1. スタート メニューで、**コンピューター証明書の管理**を検索して選択します。 **certlm** というユーティリティが開きます。
2. **[証明書 - ローカル コンピューター]** > **[信頼されたルート証明機関]** に移動します。
3. **[証明書]** を右クリックし、**[すべてのタスク]** > **[インポート]** を選択します。 証明書のインポート ウィザードが起動します。 
4. 表示される手順に従って証明書ファイル `<path>/azure-iot-test-only.root.ca.cert.pem` をインポートします。 完了すると、「正常にインポートされました」メッセージが表示されます。 

この記事の後半にある .NET のサンプルで示すように、.NET API を使用してプログラムによって証明書をインストールすることもできます。 

一般にアプリケーションでは、TLS 経由で安全に接続するために、Windows で提供される [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) という TLS スタックを使用します。 Schannel で TLS 接続の確立を試みるには、すべての証明書が Windows 証明書ストアにインストールされている*必要があります*。

## <a name="use-certificates-with-azure-iot-sdks"></a>証明書で Azure IoT SDK を使用する

この記事では、ルート CA 証明書のことを*所有者 CA* と言います (前提条件にある記事で、この用語が自己署名証明書を生成するスクリプトに使用されているため)。 

このセクションでは、Azure IoT SDK から IoT Edge デバイスに接続する方法について、簡単なサンプル アプリケーションを使用して説明します。 すべてのサンプルの目的は、デバイス クライアントを接続し、テレメトリ メッセージをゲートウェイに送信した後、接続を閉じて終了することです。 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>すべての Azure IoT SDK に共通する概念

アプリケーションレベルのサンプルを使用する前に、次の 2 つのものを準備してください。

1. ゲートウェイ デバイスをポイントするように変更した、ダウンストリーム デバイスの IoT Hub 接続文字列。

    接続文字列の形式は、`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;` のようになります。 ゲートウェイ デバイスのホスト名を指定した **GatewayHostName** プロパティを接続文字列の末尾に追加します。 **GatewayHostName** の値は、ゲートウェイ デバイスの config.yaml ファイル内に記述された **hostname** の値と一致している必要があります。 

    最終的な文字列は、`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com` のようになります。

2. コピーしてダウンストリーム デバイス上の任意の場所に保存したルート CA 証明書への完全なパス。

    たとえば、「 `<path>/azure-iot-test-only.root.ca.cert.pem` 」のように入力します。 

### <a name="nodejs"></a>NodeJS

このセクションでは、Azure IoT NodeJS デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 Linux ホストと Windows ホストの場合は、ここに示すようにアプリケーション レベルでルート CA 証明書をインストールする必要があります (NodeJS アプリケーションはシステムの証明書ストアを使用しないため)。 

1. **edge_downstream_device.js** のサンプルを [Node.js 用 Azure IoT device SDK サンプル リポジトリ](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)から入手します。 
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。 
3. edge_downstream_device.js ファイル内の **connectionString** 変数と **edge_ca_cert_path** 変数を更新します。 
4. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。 

実行するサンプルについて理解するために、次のコード スニペットを参照してください。このコードは、クライアント SDK によって証明書ファイルを読み取り、読み取った証明書ファイルを使用してセキュリティで保護された TLS 接続を確立する方法を示しています。 

```nodejs
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

このセクションでは、Azure IoT .NET デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 ただし、Linux ホストと Windows ホストの両方で、.NET アプリケーションは、システムの証明書ストアにインストールされている任意の証明書を自動的に使用できます。

1. **EdgeDownstreamDevice** のサンプルは、[IoT Edge .NET サンプル フォルダー](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)にあります。 
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。 
3. **Properties / launchSettings.json** ファイル内の **DEVICE_CONNECTION_STRING** 変数と **CA_CERTIFICATE_PATH** 変数を更新します。 ホスト システム上の信頼された証明書ストアにインストールされている証明書を使用する場合は、この変数を空白のままにします。 
4. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。 

.NET アプリケーションを使用して、信頼された証明書を証明書ストアにプログラムによってインストールするには、**EdgeDownstreamDevice / Program.cs** ファイル内の **InstallCACert()** 関数を参照してください。 この操作は、べき等であるため、その他の効果を与えずに同じ値を使用して複数回実行できます。 

### <a name="c"></a>C

このセクションでは、Azure IoT C デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 C SDK は、OpenSSL、WolfSSL、Schannel など、多くの TLS ライブラリと共に動作します。 詳細については、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のページを参照してください。 

1. **iotedge_downstream_device_sample** アプリケーションを [C 用 Azure IoT device SDK サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)のページから入手します。 
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。 
3. iotedge_downstream_device_sample.c ファイル内の **connectionString** 変数と **edge_ca_cert_path** 変数を更新します。 
4. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。 

C 用 Azure IoT device SDK には、クライアントを設定するときに CA 証明書を登録するためのオプションが用意されています。 この操作では、証明書をどこにもインストールせず、代わりに、メモリ内にある証明書の文字列形式を使用します。 接続を確立するときに、保存されている証明書が、基になる TLS スタックに提供されます。 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Windows ホストで OpenSSL または別の TLS ライブラリを使用していない場合、SDK の既定では Schannel が使用されます。 Schannel が機能するためには、IoT Edge ルート CA 証明書が、(`IoTHubDeviceClient_SetOption` 操作を使用して設定されていない) Windows 証明書ストアにインストールされている必要があります。 

### <a name="java"></a>Java

このセクションでは、Azure IoT Java デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 

1. **Send-event** のサンプルを [Java 用 Azure IoT device SDK サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)のページから入手します。 
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。 
3. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。

### <a name="python"></a>Python

このセクションでは、Azure IoT Python デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 

1. **edge_downstream_client** のサンプルを [Python 用 Azure IoT device SDK サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples)のページから入手します。 
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。 
3. edge_downstream_client.py ファイル内の **CONNECTION_STRING** 変数と **TRUSTED_ROOT_CA_CERTIFICATE_PATH** 変数を更新します。 
4. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。 


## <a name="test-the-gateway-connection"></a>ゲートウェイ接続をテストする

これは、すべて正しく設定されていることをテストするサンプル コマンドです。 "verified OK" (検証 OK) というメッセージが表示されます。

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>次の手順

IoT Edge を使用して、どのような方法でダウンストリーム デバイスに[オフライン機能](offline-capabilities.md)を拡張できるかについて学びます。 
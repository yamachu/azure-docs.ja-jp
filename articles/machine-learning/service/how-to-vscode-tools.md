---
title: Azure Machine Learning での Visual Studio Code Tools for AI 拡張機能の使用
description: Visual Studio Code Tools for AI について説明し、VS Code で Azure Machine Learning サービスを使用して機械学習とディープ ラーニングのモデルについてのトレーニングを開始し、それらを展開する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: a2f37cffb37ce7008c3e372763784240e0d4250b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945554"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>VS Code Tools for AI: Visual Studio Code から Azure Machine Learning を始める

この記事では、Visual Studio Code (VS Code) の拡張機能である **Tools for AI** について説明し、VS Code で Azure Machine Learning サービスを使用して機械学習とディープ ラーニングのモデルについてのトレーニングを開始し、それらを展開する方法を説明します。

Visual Studio コードで Tools for AI 拡張機能を使用して、Azure Machine Learning サービスを使用し、データの準備、ローカルとリモートのコンピューティング ターゲットでの ML モデルのトレーニングとテスト、それらのモデルのデプロイ、カスタム メトリックと実験の追跡を行います。

## <a name="prerequisite"></a>前提条件

+ Visual Studio Code をインストールする必要があります。 VS Code とは、デスクトップ上で実行される、軽量にもかかわらず強力なソース コード エディターです。 Python などの組み込みサポートが付属します。  [VS Code のインストール方法を確認](https://code.visualstudio.com/docs/setup/setup-overview)してください。

+ [Python 3.5 以上をインストール](https://www.anaconda.com/download/)します。

+ Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="install-vs-code-tools-for-ai-extension"></a>VS Code Tools for AI 拡張機能

**Tools for AI** 拡張機能をインストールするとき、他の 2 つの拡張機能も自動的にインストールされます (インターネットに接続している場合)。 [Azure アカウント](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)拡張機能と [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 拡張機能です。

Azure Machine Learning を使用するには、VS Code を Python IDE に変換する必要があります。 [Visual Studio Code で Python](https://code.visualstudio.com/docs/languages/python) を使用するためには、Microsoft Python 拡張機能が必要です。これは、Tools for AI と一緒に自動的にインストールされます。 この拡張機能を使用すると VS Code が優れた IDE になります。この機能は、あらゆるオペレーティング システムでさまざまな Python インタープリターと連携します。 これは、VS Code のすべての機能を活用して、オートコンプリートと IntelliSense、lint 処理、デバッグ、単体テストを提供する他に、複数の Python 環境 (仮想環境や conda 環境など) を切り替えることもできます。 Python コードを編集、実行、デバッグする手順については、[Python Hello World のチュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください

**Tools for AI 拡張機能をインストールするには:**

1. VS Code を起動します。

1. ブラウザーで http://aka.ms/vscodetoolsforai にアクセスします。 

1. この Web ページで **[インストール]** をクリックします。 

1. 拡張機能のタブで **[インストール]** をクリックします。

1. VS Code において拡張機能の [ようこそ] タブが開き、Azure のシンボルがアクティビティ バーに追加されます。

   ![Visual Studio Code のアクティビティ バーの Azure アイコン](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. ダイアログ ボックスで、**[サインイン]** をクリックし、画面に表示されるプロンプトに従い Azure の認証を行います。 
   
   VS Code Tools for AI と一緒にインストールされた Azure アカウント拡張機能が、Azure アカウントの認証に役立ちます。 [Azure アカウント拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)のページでコマンドの一覧表を参照してください。

> [!Tip] 
> [VS Code の IntelliCode 拡張機能 (プレビュー)](https://go.microsoft.com/fwlink/?linkid=2006060) を確認してください。 IntelliCode は、現在のコード コンテキストに基づいた最も関連性の高いオートコンプリートの推測など、Python における IntelliSense に一連の AI 支援機能を提供します。

## <a name="install-the-sdk"></a>SDK のインストール

1. Python 3.5 以上をインストールし、VS Code によって認識されていることを確認してください。 ここでインストールする場合は、VS Code を再起動し、 https://code.visualstudio.com/docs/python/python-tutorial の手順に従って Python インタープリターを選択します。

1. VS Code でコマンド パレットを開きます (**Ctrl + Shift + P**)。

1. 「Install Azure ML SDK」と入力して、SDK の pip インストール コマンドを探します。 Azure Machine Learning を使用するための Visual Studio Code の前提条件を備えた、ローカルのプライベート Python 環境が作成されます。
   ![インストール](./media/vscode-tools-for-ai/install-sdk.png)

1. 統合ターミナル ウィンドウで、使用する Python インタープリターを指定します。または、**Enter** を押すと既定の Python インタープリターを使用できます。

   ![インストール](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning の利用を開始

VS Code を使用して ML モードのトレーニングとデプロイを開始する前に、モデルとリソースを格納するための [Azure Machine Learning サービス ワークスペース](concept-azure-machine-learning-architecture.md#workspace)をクラウドに作成する必要があります。 1 つ作成して、そのワークスペースに最初の実験を作成する方法について説明します。

1. Visual Studio Code のアクティビティ バーの Azure アイコンをクリックします。 [Azure: Machine Learning] サイドバーが表示されます。

   ![インストール](./media/vscode-tools-for-ai/createworkspace.gif)

1. Azure サブスクリプションを右クリックし、**[ワークスペースの作成]** を選択します。 一覧が表示されます。 アニメーション画像では、サブスクリプション名は "OpenMind Studio"、ワークスペースは "MyWorkspace" になっています。 

1. 一覧から既存のリソース グループを選択するか、コマンド パレットでウィザードを使用して新しいリソース グループを作成します。

1. フィールドに、新しいワークスペースの一意でわかりやすい名前を入力します。 スクリーン ショットでは、ワークスペースの名前は "MyWorkspace" です。

1. Enter を押すと、新しいワークスペースが作成されます。 これは、ツリー内でサブスクリプション名の下に表示されます。

1. ワークスペース名を右クリックし、コンテキスト メニューから **[Create Experiment]\(実験の作成\)** を選択します。  実験は、Azure Machine Learning を使用した実行を記録します。

1. フィールドに、実験の名前を入力します。 スクリーン ショットでは、実験の名前は "MNIST" です。
 
1. Enter を押すと、新しい実験が作成されます。 これは、ツリー内でワークスペース名の下に表示されます。

1. 実験名を右クリックし、**[Attach a local folder]\(ローカル フォルダーを接続\)** を選択します。 このフォルダーには ローカルの Python スクリプトが含まれます。 さらに、フォルダーはクラウド内で実験に関連付けられます。 

   ここで、それぞれの実験を実験と一緒に実行すると、すべての主要メトリックが実験履歴に格納されます。トレーニングするモデルは、自動的に Azure Machine Learning にアップロードされ、実験のメトリックとログと一緒に格納されます。

   [![VS Code でフォルダーを接続](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>キーボード ショートカットの使用

VS Code のほとんどの機能と同様に、VS Code の Azure Machine Learning 機能もキーボードからアクセスできます。 知っておくべき最も重要なキーの組み合わせは、コマンド パレットを表示する Ctrl + Shift + P です。 ここで、よく使う操作のキーボード ショートカットを含め、VS Code のすべての機能にアクセスできます。

[![VS Code Tools for AI のキーボード ショートカット](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>次の手順

これで、Visual Studio Code を使用して Azure Machine Learning を使用できるようになりました。

[Visual Studio Code でのコンピューティング ターゲットの作成やモデルのトレーニングとデプロイ](how-to-vscode-train-deploy.md)の方法について学びます。
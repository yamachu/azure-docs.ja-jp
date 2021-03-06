---
title: 'チュートリアル: Azure Active Directory と KnowBe4 Security Awareness Training の統合 | Microsoft Docs'
description: Azure Active Directory と KnowBe4 Security Awareness Training の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: c27af4e7bc88f24b76310336859740d8795f7cbe
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449275"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>チュートリアル: Azure Active Directory と KnowBe4 Security Awareness Training の統合

このチュートリアルでは、KnowBe4 Security Awareness Training と Azure Active Directory (Azure AD) を統合する方法について説明します。

KnowBe4 Security Awareness Training と Azure AD の統合には、次の利点があります。

- KnowBe4 Security Awareness Training にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで KnowBe4 Security Awareness Training に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と KnowBe4 Security Awareness Training の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- KnowBe4 Security Awareness Training シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. KnowBe4 Security Awareness Training をギャラリーから追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>KnowBe4 Security Awareness Training をギャラリーから追加する
KnowBe4 Security Awareness Training の Azure AD への統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に KnowBe4 Security Awareness Training を追加する必要があります。

**ギャラリーから KnowBe4 Security Awareness Training を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**KnowBe4 Security Awareness Training**」と入力し、結果パネルで **[KnowBe4 Security Awareness Training]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の KnowBe4 Security Awareness Training](./media/knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、KnowBe4 Security Awareness Training で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する KnowBe4 Security Awareness Training ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと KnowBe4 Security Awareness Training の関連ユーザーの間で、リンク関係が確立されている必要があります。

KnowBe4 Security Awareness Training で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

KnowBe4 Security Awareness Training で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[KnowBe4 Security Awareness Training テスト ユーザーの作成](#create-a-knowbe4-security-awareness-training-test-user)** - KnowBe4 Security Awareness Training で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、KnowBe4 Security Awareness Training アプリケーションでシングル サインオンを構成します。

**KnowBe4 Security Awareness Training で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の**KnowBe4 Security Awareness Training** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

1. **[KnowBe4 Security Awareness Training Domain and URLs]\(KnowBe4 Security Awareness Training のドメインと URL\)** セクションで、次の手順に従います。

    ![[KnowBe4 Security Awareness Training のドメインと URL] のシングル サインオン情報](./media/knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.KnowBe4.com/auth/saml/<instancename>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[KnowBe4 Security Awareness Training Client サポート チーム](mailto:support@KnowBe4.com)に問い合わせてください。 

    b. **[識別子]** ボックスに、文字列値 `KnowBe4`を入力します。

    > [!NOTE]
    >これには大文字と小文字の区別があります。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/knowbe4-tutorial/tutorial_general_400.png)

1. **[KnowBe4 Security Awareness Training]** セクションで、**[Configure KnowBe4 Security Awareness Training]\(KnowBe4 Security Awareness Training の構成\)** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![KnowBe4 Security Awareness Training の構成](./media/knowbe4-tutorial/tutorial_knowbe4_configure.png) 

1. **KnowBe4 Security Awareness Training** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (未加工)**、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を、[KnowBe4 Security Awareness Training Client サポート チーム](mailto:support@KnowBe4.com)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/knowbe4-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/knowbe4-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/knowbe4-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/knowbe4-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>KnowBe4 Security Awareness Training のテスト ユーザーを作成する

このセクションの目的は、KnowBe4 Security Awareness Training で Britta Simon というユーザーを作成することです。 KnowBe4 Security Awareness Training では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない KnowBe4 Security Awareness Training ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[KnowBe4 Security Awareness Training のサポート チーム](mailto:support@KnowBe4.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に KnowBe4 Security Awareness Training へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**KnowBe4 Security Awareness Training に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[KnowBe4 Security Awareness Training]** を選択します。

    ![アプリケーションの一覧の KnowBe4 Security Awareness Training リンク](./media/knowbe4-tutorial/tutorial_knowbe4_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
  
アクセス パネルで [KnowBe4 Security Awareness Training] タイルをクリックすると、KnowBe4 Security Awareness Training アプリケーションに自動的にサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/knowbe4-tutorial/tutorial_general_203.png


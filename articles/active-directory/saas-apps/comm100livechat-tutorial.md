---
title: 'チュートリアル: Azure Active Directory と Comm100 Live Chat の統合 | Microsoft Docs'
description: Azure Active Directory と Comm100 Live Chat の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "42140915"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>チュートリアル: Azure Active Directory と Comm100 Live Chat の統合

このチュートリアルでは、Comm100 Live Chat と Azure Active Directory (Azure AD) を統合する方法について説明します。

Comm100 Live Chat と Azure AD の統合には、次の利点があります。

- Comm100 Live Chat にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Comm100 Live Chat にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Comm100 Live Chat と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Comm100 Live Chat でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Comm100 Live Chat を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-comm100-live-chat-from-the-gallery"></a>ギャラリーから Comm100 Live Chat を追加する
Azure AD への Comm100 Live Chat の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Comm100 Live Chat を追加する必要があります。

**ギャラリーから Comm100 Live Chat を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Comm100 Live Chat**」と入力し、結果ウィンドウで **Comm100 Live Chat** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Comm100 Live Chat で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Comm100 Live Chat ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Comm100 Live Chat の関連ユーザーの間で、リンク関係が確立されている必要があります。

Comm100 Live Chat で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Comm100 Live Chat のテスト ユーザーの作成](#create-a-comm100-live-chat-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Comm100 Live Chat で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Comm100 Live Chat アプリケーションでシングル サインオンを構成します。

**Comm100 Live Chat との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Comm100 Live Chat** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. **[Comm100 Live Chat のドメインと URL]** セクションで、次の手順に従います。

    ![[Comm100 Live Chat のドメインと URL] のシングル サインオン情報](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 サインオン URL の値は、後で実際のサインオン URL に置き換えることになります。実際の値については後で説明します。

4. Comm100 Live Chat アプリケーションは SAML アサーションを使用し、特定の属性を含みます。 このアプリケーションには、次の属性を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    |  属性名  | 属性値 |
    | --------------- | -------------------- |    
    |   email    | User.mail |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    e. **[OK]** をクリックします。

6. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. **[Comm100 Live Chat 構成]** セクションで、**[Comm100 Live Chat の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Comm100 Live Chat の構成](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Comm100 Live Chat にログインします。

10. ページの右上の **[My Account]\(マイ アカウント\)** をクリックします。

    ![Comm100 Live Chat のマイ アカウント](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. メニューの左側にある **[セキュリティ]** をクリックし、**[Agent Single Sign-On]\(エージェント シングル サインオン\)** をクリックします。

    ![Comm100 Live Chat のセキュリティ](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. **[Agent Single Sign-On]\(エージェント シングル サインオン\)** ページで、次の手順を実行します。

    ![Comm100 Live Chat のセキュリティ](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. 強調表示されている 1 つ目のリンクをコピーし、Azure Portal の **[Comm100 Live Chat のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

    b. **[SAML SSO URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    d. **[Choose a File]\(ファイルの選択\)** を選択して、Azure Portal からダウンロードした base 64 でエンコードされた証明書を **[Certificate]\(証明書\)** にアップロードします。

    e. **[Save Changes]\(変更の保存\)** をクリックします

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-comm100-live-chat-test-user"></a>Comm100 Live Chat テスト ユーザーの作成

Azure AD ユーザーが Comm100 Live Chat にログインできるようにするには、ユーザーを Comm100 Live Chat にプロビジョニングする必要があります。 Comm100 Live Chat では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Comm100 Live Chat にセキュリティ管理者としてログインします。

2. ページの右上の **[My Account]\(マイ アカウント\)** をクリックします。

    ![Comm100 Live Chat のマイ アカウント](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. メニューの左側にある **[Agents]\(エージェント\)** をクリックし、**[New Agent]\(新規エージェント\)** をクリックします。

    ![Comm100 Live Chat のエージェント](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. **[New Agent]\(新規エージェント\)** ページで、次の手順を実行します。

    ![Comm100 Live Chat の新規エージェント](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: **Brittasimon@contoso.com**)。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    d. **[Display Name]\(表示名\)** ボックスに、ユーザーの表示名を入力します (例: **Britta simon**)

    e. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    f. **[Save]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Comm100 Live Chat へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Comm100 Live Chat に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Comm100 Live Chat]** を選択します。

    ![アプリケーションの一覧の Comm100 Live Chat のリンク](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Comm100 Live Chat] タイルをクリックすると、自動的に Comm100 Live Chat アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png


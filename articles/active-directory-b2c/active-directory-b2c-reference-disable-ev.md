---
title: Azure Active Directory B2C でコンシューマーのサインアップ時の電子メール検証を無効にする | Microsoft Docs
description: Azure Active Directory B2C でコンシューマーのサインアップ時の電子メール検証を無効にする方法を示すトピック。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e36dd19aa020b8cb2a623cda904cf7fa8a0b26da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004597"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でコンシューマーのサインアップ時のメール検証を無効にする 
有効である場合、Azure Active Directory (Azure AD) B2C は、コンシューマーが電子メール アドレスを指定してローカル アカウントを作成することによってアプリケーションにサインアップできるようにします。 Azure AD B2C は、電子メール アドレスが有効であることを確認するために、コンシューマーのサインアップ中にその検証を要求します。 また、悪意のある自動化されたプロセスがアプリケーション用の偽のアカウントを生成できないようにします。

一部のアプリケーション開発者は、サインアップ プロセス中の電子メールの検証はスキップして、代わりに後でコンシューマーに電子メール アドレスを検証させようとします。 それをサポートするために、電子メールの検証を無効にするように Azure AD B2C を構成することができます。 そうすることで、サインアップ プロセスがよりスムーズになり、開発者は電子メール アドレスを検証したコンシューマーと検証していないコンシューマーを柔軟に区別することができます。

既定では、サインアップ ポリシーで電子メールの検証が有効になっています。 この機能を無効にするには、次のようにします。

1. サインアップ用に何を構成したかに応じて、**[Sign-up policies (サインアップ ポリシー)]** または **[Sign-up or sign-in policies (サインアップまたはサインイン ポリシー)]** をクリックします。
2. ポリシー (例: "B2C_1_SiUp") をクリックして開きます。 
3. ブレードの上部にある **[編集]** をクリックします。
4. **[Page UI Customization (ページ UI のカスタマイズ)]** をクリックします。
5. **[ローカル アカウントのサインアップ ページ]** をクリックします。
6. **[Sign-up attributes (サインアップ属性)]** セクションの **[Name (名前)]** 列の **[Email Address (電子メール アドレス)]** をクリックします。
7. **[Require verification (検証を要求する)]** オプションを **[No (いいえ)]** に切り替えます。
8. **[Edit policy (ポリシーの編集)]** ブレードが表示されるまで、下部の **[OK]** をクリックします。
9. ブレードの上部にある **[保存]** をクリックします。 以上で終わりです。

> [!NOTE]
> サインアップ プロセスでの電子メールの検証を無効にすると、スパムにつながる場合があります。 既定値を無効にする場合は、独自の検証システムを追加することをお勧めします。
> 
>
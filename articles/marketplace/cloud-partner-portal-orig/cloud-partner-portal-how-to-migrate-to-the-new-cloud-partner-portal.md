---
title: オファーの移行 | Microsoft Docs
description: 既存のオファーを新しいポータルに移行します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 191378898dd8afcc7853061ae7404156af2f5fd6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807446"
---
# <a name="how-to-migrate-to-the-new-cloud-partner-portal"></a>新しいクラウド パートナー ポータルに移行する方法

Cloud パートナー ポータルへの移行は、重要なステップです。 発行プロセスに関する皆様からのフィードバックに応じて、ポータルにかなりの投資を行いました。 これらの投資は、使いやすさと発行エクスペリエンスの改善に重点を置いています。 

-   組織 ID ログインのサポート
-   リアルタイムのフィールド検証によるエラー検出
-   発行ワークフローの簡素化

オファーの移行を簡単に行えるようにしました。改善された発行エクスペリエンスに対する準備ができたら、以下の移行手順を確認してください。

移行を成功させるために以下の移行ステージを確認します。



## <a name="decide-youre-ready-to-migrate"></a>移行の準備ができていることを確認する

オファーを Cloud パートナー ポータルへ移行する前に、作業の内容を十分理解しておく必要があります。

**すべてのプランが移行の対象になりますか?**

現時点では、すべての仮想マシン プランが移行の対象になります。 ドラフト、ステージング済み、掲載済みの仮想マシン オファーのそれぞれが移行されます。

他の種類のオファー (ソリューション テンプレート、体験版など) は、当面のところ、これまでどおり古いポータルで動作します。

**ライブ プランは引き続き動作しますか?**

はい。この移行によるライブ プランへの影響はありません。 また、お客様の発行元アカウント情報が事前に入力されているため、移行が完了した後すぐに新しいポータルでオファーを更新できます。 ステージング済みのオファーはドラフトになるため、新しいポータルで発行する必要があります。

**移行するときにプランの価格を変更できますか?**

いいえ。すべてのライブ オファーについて、この移行では価格を変更できないため、価格は同じままにする必要があります。

**プランがステージング中または発行中の場合どうなりますか?**

古いポータルに発行中またはステージング中のプランがあると、移行は失敗します。 移行するときは、オファーがすべて発行済みまたはステージング済みの状態であるようにする必要があります。

**新しいロールベースのアクセス制御はどのように機能しますか?**

新しいポータルでは、管理者が発行プロファイルの所有者になり、すべての共同管理者が共同作成者になります。 発行プロファイルに含まれるいずれかの情報を編集する場合は、新しいポータルを使用する必要があります。

ユーザーを管理するために、古いポータルのプランに関する作業を行うユーザーを追加する場合は、古いポータルの共同管理者として追加します。 新しいポータルのオファーに関する作業を行うユーザーを追加する場合は、新しいポータルの共同作成者として追加します。

**移行を行うと最終版になりますか?**

オファーが移行されると、オファーに関する古いポータルでのアクションはすべて読み取り専用になり、古いポータルでの作成は行うことができなくなります。 移行したすべてのオファーの編集や操作は中断した時点から再開できますが、作業を行うことができる場所は新しいポータルに限られます。

## <a name="submit-a-request-to-migrate"></a>移行の要求を送信する

移行の準備が整ったら、Cloud パートナー ポータルの [学習] ページで、お客様の発行元名と ID を使用して移行を申請します。

![発行フロー プロセス図](./media/cloud-partner-portal-how-to-migrate-to-the-new-cloud-partner-portal/migrate.png)

## <a name="confirm-a-date-to-migrate"></a>移行日を確認する

オファーは、毎週火曜日の午前 10 時から午後 1 時 (太平洋標準時) の間に移行されます。転送開始の 24 時間前までに、発行アカウントの所有者に転送時刻の確認が求められます。 ワークフローに影響を与えないよう、移行を別の時間に行う必要がある場合は、最適な時間に移行できるよう調整いたします。

移行が完了し、新しいポータルで作業を再開できるようになったことを知らせるメール通知が送られます。

## <a name="settle-in-and-enjoy"></a>新しい環境を利用する

移行が終了したら、新しいポータルを使ってみましょう。 これ以降、プランは古いポータルでは読み取り専用になり、作成や編集はすべて新しいポータルから実行します。

**移行時に問題が発生しました。どうすればよいですか?**
 
移行が失敗した場合は、変更を元に戻し、別の日時に移行スケジュールを再設定します。 移行の終了後に質問がある場合は、フィードバック ツールからお問い合わせください。

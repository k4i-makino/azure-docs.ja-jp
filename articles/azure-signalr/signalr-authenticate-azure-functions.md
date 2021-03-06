---
title: 'チュートリアル: Azure Functions を使用した Azure SignalR Service 認証 | Microsoft Docs'
description: このチュートリアルでは、Azure SignalR Service クライアントを認証する方法について説明します
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 8af657c39217f3edcadef6ec0981a31ec7e89aa6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978426"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>チュートリアル: Azure Functions を使用した Azure SignalR Service 認証

Azure Functions、App Service 認証、および SignalR Service を使用して、認証とプライベート メッセージングを使用してチャットルームを構築する手順を説明したチュートリアルです。

## <a name="introduction"></a>はじめに

### <a name="technologies-used"></a>使用されているテクノロジ

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): ユーザーを認証してチャット メッセージを送信するためのバックエンド API
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): 接続されたチャット クライアントに新しいメッセージをブロードキャストします
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): チャット クライアントの UI 用の静的な Web サイトをホストします

### <a name="prerequisites"></a>前提条件

このチュートリアルを構築するには、次のソフトウェアが必要です。

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (Version 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (Version 2.x、Functions 拡張機能に必要)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (Version 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) と次の拡張機能
    * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions): Azure Functions は VS Code で使用します
    * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer): テスト用の Web ページをローカルで提供します


## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) に移動し、資格情報を使用してサインインします。


## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service インスタンスを作成する

Azure Functions アプリをローカルでビルドしてテストします。 このアプリは Azure の SignalR Service インスタンス (事前に作成しておく必要があります) にアクセスします。

1. 新しい Azure リソースを作成するには、**[リソースの作成]** (**+**) ボタンをクリックします。

1. **SignalR Service** を探して選択します。 **[作成]** をクリックします。

    ![新しい SignalR Service](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. 次の情報を入力します。

    | Name | 値 |
    |---|---|
    | リソース名 | SignalR Service インスタンスの一意の名前 |
    | リソース グループ | 新しいリソース グループを作成する |
    | Location | 近くの場所を選択します |
    | 価格レベル | 無料 |
    
1. **[作成]** をクリックします。


## <a name="initialize-the-function-app"></a>関数アプリを初期化する

### <a name="create-a-new-azure-functions-project"></a>新しい Azure Functions プロジェクトを作成する

1. 新しい VS Code ウィンドウでメニューの `File > Open Folder` を使用し、適切な場所に空のフォルダーを作成して開きます。 これは、ビルドするアプリケーションのメイン プロジェクト フォルダーになります。

1. VS Code で Azure Functions 拡張機能を使用して、メイン プロジェクト フォルダー内の関数アプリを初期化します。
    1. VS Code でメニューから **[表示]、[コマンド パレット]** の順に選択してコマンド パレットを開きます (ショートカットは `Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。
    1. **[Azure Functions: Create New Project]\(Azure Functions: 新しいプロジェクトの作成\)** コマンドを探して選択します。
    1. メイン プロジェクト フォルダーが表示されます。 それを選択します (または [参照] を使用して選択します)。
    1. 言語を選択するプロンプトで、**[JavaScript]** を選択します。

    ![Function App を作成する](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)


### <a name="install-function-app-extensions"></a>関数アプリ拡張機能をインストールする

このチュートリアルでは、Azure SignalR Service との対話に Azure Functions バインドを使用します。 他のほとんどのバインドと同様に、SignalR Service バインドは拡張機能として使用できます。拡張機能を使用するには、Azure Functions Core Tools CLI を使用してインストールする必要があります。

1. メニューから **[表示]、[統合ターミナル]** の順に選択して (または Ctrl + ` キー) VS Code でターミナルを開きます。

1. メイン プロジェクト フォルダーが現在のディレクトリであることを確認します。

1. SignalR Service 関数アプリ拡張機能をインストールします。
    ```
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>アプリケーション設定の構成

Azure Functions ランタイムをローカルで実行してデバッグする場合、アプリケーション設定は **local.settings.json** から読み取られます。 以前に作成した SignalR Service インスタンスの接続文字列でこのファイルを更新します。

1. VS Code のエクスプローラー ウィンドウで **local.settings.json** を選択して開きます。

1. ファイルの内容を次のように置き換えます。
    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * `AzureSignalRConnectionString` という Azure SignalR Service 接続文字列を設定に入力します。 Azure portal の Azure SignalR Service リソースの **[キー]** ページから値を取得します。プライマリまたはセカンダリの接続文字列を使用できます。
    * `WEBSITE_NODE_DEFAULT_VERSION` 設定はローカルでは使用されませんが、Azure にデプロイする場合は必須です。
    * `Host` セクションでは、ローカルの Functions ホスト用のポートと CORS 設定を構成します (この設定を Azure で実行しても効果はありません)。

    ![SignalR Service キーを取得する](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. ファイルを保存します。

    ![ローカル設定を更新する](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)


## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>SignalR Service に対してユーザーを認証する関数を作成する

チャット アプリをブラウザーで初めて開く場合、Azure SignalR Service に接続できる有効な接続資格情報が必要です。 関数アプリで *SignalRInfo* という HTTP によってトリガーされる関数を作成し、この接続情報を返します。

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: 関数の作成]** コマンドを探して選択します。

1. 入力を求められたら、次の情報を指定します。

    | Name | 値 |
    |---|---|
    | 関数アプリ フォルダー | メイン プロジェクト フォルダーを選択します |
    | テンプレート | HTTP トリガー |
    | Name | SignalRInfo |
    | 承認レベル | Anonymous |
    
    新しい関数を含む **SignalRInfo** というフォルダーが作成されます。

1. **SignalRInfo/function.json** を開き、関数のバインドを構成します。 ファイルの内容を次のように変更します。 これにより、クライアントが `chat` という Azure SignalR Service ハブに接続するための有効な資格情報を生成する入力バインドが追加されます。

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    `signalRConnectionInfo` バインドの `userId` プロパティは、認証された SignalR Service 接続を作成するために使用されます。 ローカル開発の場合は、このプロパティを空白のままにします。 これは関数アプリを Azure にデプロイするときに使用します。

1. **SignalRInfo/index.js** を開き、関数の本文を表示します。 ファイルの内容を次のように変更します。

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    この関数は、入力バインドから SignalR の接続情報を受け取り、それを HTTP 応答の本文でクライアントに返します。


## <a name="create-a-function-to-send-chat-messages"></a>チャット メッセージを送信する関数を作成する

また、チャット メッセージを送信するには、Web アプリに HTTP API が必要です。 *SendMessage* という HTTP によってトリガーされる関数を作成します。この関数は、SignalR Service を使用して、接続されているすべてのクライアントにメッセージを送信します。

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: 関数の作成]** コマンドを探して選択します。

1. 入力を求められたら、次の情報を指定します。

    | Name | 値 |
    |---|---|
    | 関数アプリ フォルダー | メイン プロジェクト フォルダーを選択します |
    | テンプレート | HTTP トリガー |
    | Name | SendMessage |
    | 承認レベル | Anonymous |
    
    新しい関数を含む **SendMessage** というフォルダーが作成されます。

1. **SendMessage/function.json** を開き、関数のバインドを構成します。 ファイルの内容を次のように変更します。
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    これで、元の関数に 2 つの変更が加えられます。
    * ルートを `messages` に変更し、HTTP トリガーを **POST** HTTP メソッドに制限します。
    * `chat` という名前の SignalR Service ハブに接続されているすべてのクライアントにメッセージを送信する SignalR Service 出力バインドを追加します。

1. ファイルを保存します。

1. **SendMessage/index.js** を開き、関数の本文を表示します。 ファイルの内容を次のように変更します。
    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';
            
        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }
    
        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```
    この関数は、HTTP 要求から本文を取り出し、SignalR Service に接続されたクライアントに送信し、各クライアントで `newMessage` という関数を呼び出します。

    この関数は送信元の ID を読み取ることができます。また、メッセージ本文の *recipient* 値を受け取り、メッセージを 1 人のユーザーに限定して送信することができます。 これらの機能はこのチュートリアルの後半で使用します。

1. ファイルを保存します。


## <a name="create-and-run-the-chat-client-web-user-interface"></a>チャット クライアント Web ユーザー インターフェイスの作成と実行

チャット アプリケーションの UI は、Vue JavaScript フレームワークで作成された単純なシングルページ アプリケーション (SPA) です。 関数アプリとは別にホストされます。 ローカルでは、Live Server VS Code 拡張機能を使用して Web インターフェイスを実行します。

1. VS Code で、メイン プロジェクト フォルダーのルートに **content** という新しいフォルダーを作成します。

1. **content** フォルダーに **index.html** という新しいファイルを作成します。

1. **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)** の内容をコピーして貼り付けます。

1. ファイルを保存します。

1. **F5** キーを押して関数アプリをローカルで実行し、デバッガーをアタッチします。

1. **index.html** を開いた状態で、VS Code コマンド パレットを開き (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)、**[Live Server: Open with Live Server]\(Live Server: Live Server で開く\)** を選択して Live Server を起動します。 Live Server はブラウザーでアプリケーションを開きます。

1. アプリケーションが開きます。 チャット ボックスにメッセージを入力して Enter キーを押します。 アプリケーションを更新して新しいメッセージを表示します。 認証が構成されていないため、すべてのメッセージは "匿名" として送信されます。


## <a name="deploy-to-azure-and-enable-authentication"></a>Azure をデプロイして認証を有効にする

これまではローカルで関数アプリとチャット アプリケーションを実行してきました。 次は Azure にデプロイし、アプリケーションで認証とプライベート メッセージングを有効にします。


### <a name="log-into-azure-with-vs-code"></a>VS Code で Azure にログインする

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure: サインイン]** コマンドを探して選択します。

1. 指示に従って、ブラウザーでサインイン プロセスを完了します。


### <a name="configure-function-app-for-authentication"></a>認証のために関数アプリを構成する

これまで、チャット アプリは匿名で動作しています。 Azure では、[App Service 認証](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview)を使用してユーザーを認証します。 認証済みユーザーのユーザー ID またはユーザー名を *SignalRConnectionInfo* バインドに渡して、ユーザーとして認証される接続情報を生成することができます。

メッセージを送信すると、アプリは接続されているすべてのクライアントに送信するか、特定のユーザーに認証されているクライアントにのみ送信するかをアプリが決定できます。

1. VS Code で **SendMessage/function.json** を開きます。

1. `{headers.x-ms-client-principal-name}` のように、[バインド式](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns)を *SignalRConnectionInfo* バインドの *userId* プロパティに挿入します。 これで、認証済みユーザーのユーザー名に値が設定されます。 属性は次のようになります。

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. ファイルを保存します。


### <a name="deploy-function-app"></a>関数アプリをデプロイする

1. VS Code コマンド パレットを開き (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)、**[Azure Functions: Deploy to Function App]\(Azure Functions: 関数アプリにデプロイ\)** を選択します。 

1. 入力を求められたら、次の情報を指定します。

    | Name | 値 |
    |---|---|
    | デプロイするフォルダー | メイン プロジェクト フォルダーを選択します |
    | サブスクリプション | サブスクリプションを選択します。 |
    | 関数アプリ | **[Create New Function App]\(新しい関数アプリの作成\)** を選択します |
    | 関数アプリ名 | 一意の名前を入力します |
    | リソース グループ | SignalR Service インスタンスと同じリソース グループを選択します |
    | ストレージ アカウント | **[新しいストレージ アカウントの作成]** を選択します |
    | ストレージ アカウント名 | 一意の名前を入力します (3 から 24 文字、英数字のみ) |
    | Location | 近くの場所を選択します |
    
    新しい関数アプリが Azure に作成され、デプロイが開始されます。 デプロイが完了するまで待ちます。


### <a name="upload-function-app-local-settings"></a>関数アプリのローカル設定アップロードする

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: Upload local settings]\(Azure Functions: ローカル設定のアップロード\)** コマンドを探して選択します。

1. 入力を求められたら、次の情報を指定します。

    | Name | 値 |
    |---|---|
    | ローカル設定ファイル | local.settings.json |
    | サブスクリプション | サブスクリプションを選択します。 |
    | 関数アプリ | 以前にデプロイした関数アプリを選択します |
    | 関数アプリ名 | 一意の名前を入力します |

ローカル設定は Azure の関数アプリにアップロードされます。 既存の設定を上書きすることを確認するメッセージが表示されたら、**[すべてはい]** を選択します。


### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>関数アプリのクロス オリジン リソース共有 (CORS) を有効にする

CORS 設定は **local.settings.json** にありますが、Azure の関数アプリには反映されません。 別に設定する必要があります。

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: Open in portal]\(Azure Functions: ポータルで開く\)** コマンドを探して選択します。

1. サブスクリプションと関数アプリ名を選択して、Azure portal で関数アプリを開きます。

1. 開かれたポータルの **[プラットフォーム機能]** タブで **[CORS]** を選択します。

    ![CORS を見つける](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. 値が `*` のエントリを追加します。

1. その他すべての既存のエントリを削除します。

1. **[保存]** をクリックして CORS の設定を保持します。

    ![CORS を設定する](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> 実際のアプリケーションでは、すべての送信元 (`*`) で CORS を許可するのではなく、必要な各ドメインに特定の CORS エントリを入力する方が安全な方法です。


### <a name="update-the-web-app"></a>Web アプリを更新する

1. Azure portal で関数アプリの概要ページに移動します。

1. 関数アプリの URL をコピーします。

    ![URL を取得する](media/signalr-authenticate-azure-functions/signalr-get-url.png)


1. VS Code で **index.html** を開き、`apiBaseUrl` の値を関数アプリの URL に置き換えます。

1. Azure Active Directory、Facebook、Twitter、Microsoft アカウント、または Google を使用した認証でアプリケーションを構成できます。 `authProvider` の値を設定して、使用する認証プロバイダーを選択します。

1. ファイルを保存します。


### <a name="deploy-the-web-application-to-blob-storage"></a>BLOB ストレージに Web アプリケーションをデプロイする

Web アプリケーションは、Azure Blob Storage の静的 Web サイト機能を使用してホストされます。

1. 新しい Azure リソースを作成するには、**[新規]** (**+**) ボタンをクリックします。

1. **[ストレージ]** で **[ストレージ アカウント]** を選択します。

1. 次の情報を入力します。

    | Name | 値 |
    |---|---|
    | Name | BLOB ストレージ アカウントの一意の名前 |
    | アカウントの種類 | StorageV2 (汎用 V2) |
    | Location | 他のリソースと同じリージョンを選択します |
    | レプリケーション | ローカル冗長ストレージ (LRS) |
    | [パフォーマンス] | 標準 |
    | アクセス層 | ホット |
    | リソース グループ | このチュートリアルの他のリソースと同じリソース グループを選択します |

1. **Create** をクリックしてください。

    ![ストレージの作成](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. ストレージ アカウントが作成されたら、Azure portal でそれを開きます。

1. 左側のナビゲーションで **[静的な Web サイト (プレビュー)]** を選択します。

1. **[有効化]** を選択します。

1. **インデックス ドキュメント名**に「`index.html`」と入力します。

1. **[Save]** をクリックします。

    ![静的なサイトを構成する](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. ページの **$web** リンクをクリックして BLOB コンテナーを開きます。

1. **[アップロード]** をクリックし、**index.html** を **content** フォルダーにアップロードします。

1. **[静的な Web サイト]** ページに戻ります。 **[プライマリ エンドポイント]** をメモします。 これは作成する Web アプリケーションの URL です。


### <a name="enable-app-service-authentication"></a>App Service 認証を有効にする

App Service 認証は、Azure Active Directory、Facebook、Twitter、Microsoft アカウント、Google による認証をサポートしています。

1. 関数アプリをポータルで開いたままの状態で、**[プラットフォーム機能]** タブを選択し、**[認証/承認]** を選択します。

1. App Service 認証を**オン**にします。

1. **[要求が認証されない場合に実行するアクション]** で [{前の手順で選択した認証プロバイダー} でのログイン] を選択します。

1. **[許可される外部リダイレクト URL]** に、前の手順でメモしたストレージ アカウントのプライマリ Web エンドポイントの URL を入力します。

1. 選択したログイン プロバイダーのドキュメントに従って構成を完了します。

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)
    - [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication)
    - [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication)
    - [Microsoft アカウント](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication)
    - [Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication)


### <a name="try-the-application"></a>アプリケーションを試す

1. ブラウザーで、ストレージ アカウントのプライマリ Web エンドポイントに移動します。

1. **[ログイン]** を選択して、選択した認証プロバイダーの認証を受けます。

1. パブリック メッセージを送信するには、メインのチャット ボックスに入力します。

1. プライベート メッセージを送信するには、チャット履歴のユーザー名をクリックします。 選択した受信者のみがこれらのメッセージを受け取ります。

お疲れさまでした。 リアルタイムのサーバーレス チャット アプリをデプロイしました。

![デモ](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースをクリーンアップするには、Azure portal を使用してリソース グループを削除します。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure SignalR Service で Azure Functions を使用する方法について説明しました。 詳細については、Azure Functions に SignalR Service バインドを使用してリアルタイムのサーバーレス アプリケーションをビルドする方法を参照してください。

> [!div class="nextstepaction"]
> [Azure Functions を使用してリアルタイム アプリをビルドする](signalr-overview-azure-functions.md)

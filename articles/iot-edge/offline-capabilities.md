---
title: Azure IoT Edge のオフライン機能 |Microsoft Docs
description: IoT Edge デバイスおよびモジュールがどのようにして長時間オフラインで動作できるか、さらに IoT Edge によって通常の IoT デバイスがどのようにしてオフラインで動作できるかを理解します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb44d6b0a4ea69d92f91af7ce1d6b19deff4e753
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567028"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>IoT Edge デバイス、モジュール、子デバイスの拡張オフライン機能について理解する (プレビュー)

Azure IoT Edge では、IoT Edge デバイスでの拡張オフライン操作がサポートされており、Edge 以外の子デバイスでのオフライン操作も可能です。 IoT Edge デバイスが IoT Hub に接続できる何らかの方法がある限り、IoT Edge デバイスとすべての子デバイスは、断続的な接続により、またはインターネット接続なしに、機能し続けることができます。 

>[!NOTE]
>IoT Edge のオフラインのサポートは[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。

## <a name="how-it-works"></a>動作のしくみ

IoT Edge デバイスがオフライン モードになると、Edge ハブは 3 つのロールを担います。 第一に、アップストリーム方向のすべてのメッセージを格納し、デバイスが再接続されるまで保存します。 第二に、モジュールと子デバイスが動作を継続できるよう、IoT Hub に代わってこれらを認証するために動作します。 第三に、通常は IoT Hub を経由する、子デバイス間の通信を可能にします。 

次の例は、IoT Edge のシナリオがオフライン モードでどのように動作するかを示しています。

1. **IoT Edge デバイスを構成します。**

   IoT Edge デバイスでは、オフライン機能が自動的に有効になります。 その機能を他の IoT デバイスに拡張するには、IoT Hub でデバイス間の親子リレーションシップを宣言する必要があります。 

2. **IoT Hub と同期します。**

   IoT Edge ランタイムのインストール後、IoT Edge デバイスを少なくとも 1 回はオンラインにして、IoT Hub と同期させる必要があります。 この同期した状態で、IoT Edge デバイスにより、割り当てられているすべての子デバイスに関する詳細情報が取得されます。 また、IoT Edge デバイスにより、ローカル キャッシュが安全に更新されてオフライン操作が有効になり、利用統計情報メッセージのローカル ストレージの設定が取得されます。 

3. **オフラインにします。**

   IoT Hub から切断されている間、IoT Edge デバイス、そのデプロイ済みのモジュール、およびすべての子 IoT デバイスは無期限に動作できます。 オフライン中にモジュールと子デバイスは、Edge ハブによって認証することで起動と再起動を行うことができます。 IoT Hub にアップストリーム方向でバインドされている利用統計情報は、ローカルに格納されます。 ダイレクト メソッドまたはダイレクト メッセージによって、モジュール間または子 IoT デバイス間の通信が維持されます。 

4. **IoT Hub と再接続および再同期します。**

   IoT Hub との接続が復元されると、IoT Edge デバイスは再同期されます。 ローカルに格納されているメッセージは、格納された際と同じ順序で配信されます。 モジュールおよびデバイスにおける、必要なプロパティと報告されたプロパティとの違いが調整されます。 IoT Edge デバイスにより、割り当てられている一連の子 IoT デバイスへのすべての変更が更新されます。

## <a name="restrictions-and-limits"></a>制約と制限

この記事で説明されている拡張オフライン機能は、[IoT Edge バージョン 1.0.4 以上](https://github.com/Azure/azure-iotedge/releases)で利用できます。 それより前のバージョンには、オフライン機能のサブセットが備わっています。 拡張オフライン機能を備えていない既存の IoT Edge デバイスは、ランタイム バージョンを変更してアップグレードすることはできませんが、これらの機能を取得するために新しい IoT Edge デバイス ID を使用して再構成する必要があります。 

拡張オフラインサポートは、米国東部と西ヨーロッパを除き、IoT Hub が使用可能なすべてのリージョンで利用できます。 

Edge IoT 以外のデバイスのみを子デバイスとして追加できます。 

最初の 1 回限りの同期後、IoT Edge デバイスとそれに割り当てられている子デバイスは、無期限にオフラインで機能できます。ただし、メッセージのストレージは、有効期限 (TTL) 設定と、メッセージを格納するための空きディスク容量により異なります。 

## <a name="set-up-an-edge-device"></a>Edge デバイスを設定する

IoT Edge デバイスによってその拡張オフライン機能を子 IoT デバイスまで拡張するには、Azure portal で親子リレーションシップを宣言する必要があります。

### <a name="assign-child-devices"></a>子のデバイスを割り当てる

子デバイスには、同じ IoT Hub に登録されている、Edge 以外の任意のデバイスを指定できます。 親子リレーションシップは、新しいデバイスの作成時に、あるいは親 IoT Edge デバイスまたは子 IoT デバイスのデバイスの詳細ページから管理できます。 

   ![IoT Edge デバイスの詳細ページから子デバイスを管理する](./media/offline-capabilities/manage-child-devices.png)

親デバイスには複数の子デバイスを含めることができますが、子デバイスの親は 1 つだけです。

## <a name="optional-offline-settings"></a>オプションのオフライン設定

デバイスが長時間オフラインになることが予測され、その後、生成されたすべてのメッセージを収集する場合は、すべてのメッセージを格納できるように Edge ハブを構成します。 2 つの変更、長期的なメッセージ ストレージを有効にするために、Edge ハブに対して行える変更は 2 つあります。 まず有効期限設定を大きくしてから、メッセージ ストレージに空きディスク領域をさらに追加します。 

### <a name="time-to-live"></a>Time to Live

有効期限設定は、有効期限が切れる前にメッセージが配信されるのを待機できる時間の量 (秒単位) です。 既定値は 7200 秒 (2 時間) です。 

この設定は、モジュール ツインに格納される、Edge ハブの必要なプロパティです。 この設定は、Azure portal の **[Edge ランタイムの詳細設定を構成する]** セクションで、または配置マニフェストで直接、構成することができます。 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>追加のオフライン ストレージ

既定では、メッセージは Edge ハブのコンテナー ファイルシステムに格納されます。 ストレージの量がオフラインのニーズに見合わない場合は、IoT Edge デバイス上のローカル ストレージを割り当てることができます。 コンテナー内のストレージ フォルダーを指す、Edge ハブの環境変数を作成する必要があります。 その後、作成オプションを使用して、ホスト コンピューター上のフォルダーにそのストレージ フォルダーをバインドします。 

Edge ハブ モジュールの環境変数および作成オプションは、Azure portal の **[Edge ランタイムの設定詳細を構成する]** セクションで構成できます。 または、配置マニフェストで直接構成できます。 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"<HostStoragePath>:<ModuleStoragePath>\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

`<HostStoragePath>` と `<ModuleStoragePath>` は、ご利用のホストとモジュールのストレージ パスに置き換えます。ホストとモジュールの両方のストレージ パスは絶対パスである必要があります。  たとえば、`\"Binds\":[\"/etc/iotedge/storage/:/iotedge/storage/"` は、ホスト パス `/etc/iotedge/storage` がコンテナー パス `/iotedge/storage/` にマップされることを意味します。  createOptions の詳細は、[Docker ドキュメント](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)でも確認できます。

## <a name="next-steps"></a>次の手順

[透過的なゲートウェイ](how-to-create-transparent-gateway.md)のシナリオで、拡張オフライン操作を有効にします。

---
title: ONNX と Azure Machine Learning | モデルを作成してデプロイする
description: ONNX の詳細と、Azure Machine Learning を使用した ONNX モデルの作成とデプロイ方法について説明します
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: 97350c90ab4ce9c3623a293c3a6637edc65ced08
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345472"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX と Azure Machine Learning: 相互運用可能な AI モデルを作成してデプロイする

[Open Neural Network Exchange](http://onnx.ai) (ONNX) 形式は、機械学習モデルを表すオープン標準です。 ONNX は、Microsoft を含む、互換性のあるフレームワークやツールを開発している[パートナー コミュニティ](http://onnx.ai/supported-tools)によってサポートされています。 Microsoft では、オープンで相互運用可能な AI によって、データ サイエンティストと開発者が以下を実行できるようにしています。

+ 任意のフレームワークを使用してモデルの作成とトレーニングを行う
+ 最小限の統合作業でモデルをクロスプラットフォームにデプロイする

Microsoft では、これらの目標を達成するために、Azure と Windows を含む製品で ONNX をサポートしています。  

## <a name="why-choose-onnx"></a>ONNX を選択する理由
ONNX で獲得される相互運用性によって、優れたアイデアを短時間で製品化できます。 ONNX では、データ サイエンティストは、ジョブに対して好みのフレームワークを選択できます。 同様に、開発者は、製品化するためにモデルを準備し、クラウドとエッジにデプロイする時間を短縮できます。  

ONNX モデルは、PyTorch、Chainer、Microsoft Cognitive Toolkit (CNTK)、MXNet、ML.Net、TensorFlow、Keras、SciKit-Learn などの多数のフレームワークからエクスポートできます。

ONNX モデルの視覚化と加速化を行うためのツールのエコシステムもあります。 一般的なシナリオでは、多数の事前トレーニング済みの ONNX モデルも利用できます。

[ONNX モデルのデプロイ](#deploy)は、Azure Machine Learning と ONNX Runtime を使用して、クラウドに対して実行できます。 [Windows ML](https://docs.microsoft.com/windows/ai/) を使用して Windows 10 デバイスにデプロイすることもできます。 ONNX コミュニティから提供されるコンバーターを使用して、他のプラットフォームにもデプロイできます。 

[ ![トレーニング、コンバーター、およびデプロイを示すONNX フロー図](media/concept-onnx/onnx.png) ] (./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX モデルを取得する

ONNX モデルは、さまざまな方法で取得できます。
+ [ONNX Model Zoo](https://github.com/onnx/models) からトレーニング済みの ONNX モデルを入手する (この記事の末尾にある例を参照してください)
+ カスタマイズされた ONNX モデルを [Azure Custom Vision サービス](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)から生成する 
+ 別の形式の既存モデルを ONNX に変換する (この記事の末尾にある例を参照してください) 
+ Azure Machine Learning サービスでモデルをトレーニングし、ONNX に変換するかエクスポートする (この記事の最後にある例を参照してください)

## <a name="saveconvert-your-models-to-onnx"></a>モデルを ONNX に保存/変換する

既存のモデルを ONNX に変換するか、トレーニングの最後に ONNX として保存することができます。

|モデルのフレームワーク|変換の例またはツール|
|-----|-------|
|PyTorch|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx コンバーター](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras、ScitKit-Learn、CoreML<br/>XGBoost、および libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

[ONNX チュートリアル サイト](https://github.com/onnx/tutorials)で、サポートされているフレームワークとコンバーターの最新の一覧を見つけることができます。

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Azure 内に ONNX モデルをデプロイする

Azure Machine Learning サービスを使用して、ONNX モデルのデプロイ、管理、および監視を実行できます。 標準的な[デプロイ ワークフロー](concept-model-management-and-deployment.md)と ONNX Runtime を使用して、クラウドでホストされる REST エンドポイントを作成できます。 自分で試すには、この記事の最後にある Jupyter Notebook の完全な例を参照してください。 

### <a name="install-and-configure-the-onnx-runtime"></a>ONNX Runtime をインストールして構成する

ONNX Runtime は、ONNX モデル用の高性能な推論エンジンです。 それには Python API が付属しており、CPU と GPU の両方でハードウェアを高速化します。 現時点では、ONNX 1.2 モデルをサポートし、Ubuntu 16.04 Linux 上で実行されます。 [CPU](https://pypi.org/project/onnxruntime) パッケージと [GPU](https://pypi.org/project/onnxruntime-gpu) パッケージは、どちらも [PyPi.org](https://pypi.org) から入手できます。

ONNX Runtime をインストールするには、次を使用します。
```python
pip install onnxruntime
```

Python スクリプトで ONNX Runtime を呼び出するには、次を使用します。
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

通常は、モデルに付属しているドキュメントに、モデルを使用するための入力と出力に関する情報が記載されています。 [Netron](https://github.com/lutzroeder/Netron) などの視覚化ツールを使用してモデルを表示することもできます。 ONNX Runtime では、モデルのメタデータ、入力、および出力のクエリを実行することもできます。
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

モデルを推論するには、`run` を使用し、返してほしい出力の一覧 (すべてが必要な場合は空のままにします) と入力値のマップを渡します。 結果として出力の一覧が返されます。
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

完全な API リファレンスについては、[ONNX Runtime に関する参照ドキュメント](https://aka.ms/onnxruntime-python)をご覧ください。

### <a name="example-deployment-steps"></a>デプロイ手順の例

ここでは、ONNX モデルのデプロイの一例を示します。

1. Azure Machine Learning サービスを初期化します。 ワークスペースがまだない場合は、[こちらのクイック スタート](quickstart-get-started.md)でワークスペースの作成方法を確認してください。

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Azure Machine Learning にモデルを登録します。

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. モデルとすべての依存関係を含むイメージを作成します。

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   スコア付けロジックが含まれている `score.py` ファイルをイメージに含める必要があります。 このファイルは、イメージ内のモデルを実行するために使用されます。 スコア付けスクリプトの作成手順については、[こちら](tutorial-deploy-models-with-aml.md#create-scoring-script)を参照してください。 ONNX モデル ファイルの例を次に示します。

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   `myenv.yml` ファイルは、イメージで必要な依存関係を記述します。 このサンプル ファイルのような環境ファイルの作成手順については、[こちらのチュートリアル](tutorial-deploy-models-with-aml.md#create-environment-file)を参照してください。

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. ONNX モデルを Azure Machine Learning を使用して、次にデプロイします。
   + Azure Container Instances (ACI): [方法...](how-to-deploy-to-aci.md)

   + Azure Kubernetes Service (AKS): [方法...](how-to-deploy-to-aks.md)


## <a name="examples"></a>例
 
次のノートブックは、Azure Machine Learning で ONNX モデルを作成し、デプロイする方法を示しています。 
+ [onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb)
+ [onnx/onnx-convert-aml-deploy-tinyyolo.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-convert-aml-deploy-tinyyolo.ipynb)
+ [onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb)

次のノートブックは、Azure Machine Learning で既存の ONNX モデルをデプロイする方法を示しています。 
+ [onnx/onnx-inference-mnist-deploy.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-mnist-deploy.ipynb) 
+ [onnx/onnx-inference-facial-expression-recognition-deploy.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-facial-expression-recognition-deploy.ipynb)
 
これらの notebook を入手してください。
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>詳細情報

ONNX 詳細を確認するか、プロジェクトに協力します。
+ [ONNX プロジェクト Web サイト](http://onnx.ai)

+ [GitHub の ONNX コード](https://github.com/onnx/onnx)

---
title: "Azure Kubernetes クラスターの監視 - 操作の管理 | Microsoft Docs"
description: "Microsoft Operations Management Suite を使用した Azure Container Service での Kubernetes クラスターの監視"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 4e4a4f4e299dc2747eb48bbd2e064cd80783211c
ms.openlocfilehash: 46240f3dc99a8c8a103a1e7919ad4f5e7a8ea62a
ms.lasthandoff: 04/04/2017


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS) を使用して Azure Container Service クラスターを監視する

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、`az` Azure CLI と `kubectl` ツールをインストールしていることも想定します。

`az` ツールがインストールされていることを確認するには、次を実行します。

```console
$ az --version
```

`az` ツールをインストールしていないない場合、[ここ](https://github.com/azure/azure-cli#installation)に手順が記載されています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```console
$ kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。

```console
$ az acs kubernetes install-cli
```

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Operations Management Suite (OMS) を使用したコンテナーの監視

Microsoft Operations Management (OMS) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。 コンテナー ソリューションは OMS Log Analytics の 1 つのソリューションであり、コンテナー インベントリ、パフォーマンス、およびログを 1 つの場所で表示するのに役立ちます。 一元的な場所でログを表示して監査やコンテナーのトラブルシューティングを行い、ホスト上のノイズと消費の多いコンテナーを検索することができます。

![](media/container-service-monitoring-oms/image1.png)

コンテナー ソリューションの詳細については、[Log Analytics のコンテナー ソリューション](../log-analytics/log-analytics-containers.md)に関するページを参照してください。

## <a name="installing-oms-on-kubernetes"></a>Kubernetes への OMS のインストール

### <a name="obtain-your-workspace-id-and-key"></a>ワークスペース ID とキーを取得する
OMS エージェントからサービスに通信するには、ワークスペース ID とワークスペース キーが構成されている必要があります。 ワークスペース ID とキーを取得するには、<https://mms.microsoft.com> で OMS アカウントを作成する必要があります。
手順に従ってアカウントを作成してください。 アカウントの作成が完了したら、次のように **[設定]**、**[接続されたソース]**、**[Linux サーバー]** の順にクリックして ID とキーを取得する必要があります。

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>DaemonSet を使用して OMS エージェントをインストールする
DaemonSet は Kubernetes によって使用され、クラスターのホストごとに 1 つのコンテナーの 1 つのインスタンスを実行します。
この DaemonSet は、監視エージェントの実行に最適です。

[DaemonSet の YAML ファイル](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)を次に示します。 これを `oms-daemonset.yaml` という名前のファイルに保存し、このファイル内で、`WSID` と `KEY` のプレースホルダーの値をワークスペース ID とキーに置き換えます。

DaemonSet 構成にワークスペース ID とキーを追加したら、`kubectl` コマンド ライン ツールを使用してクラスターに OMS エージェントをインストールできます。

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>まとめ
これで完了です。 しばらくすると、OMS ダッシュボードにデータが送られたことがわかります。


---
title: "Azure CLI のサンプル スクリプト - IIS のインストール | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - IIS のインストール"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 77e17011e2f3764b70f17604337e0fcbcfd22ede
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Azure CLI を使用した仮想マシンの簡易作成

このスクリプトでは、Windows Server 2016 を実行する Azure 仮想マシンを作成してから、Azure 仮想マシンのカスタム スクリプト拡張機能を使用して IIS をインストールします。 このスクリプトを実行すると、仮想マシンのパブリック IP アドレス上にあるの既定 IIS Web サイトにアクセスできるようになります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "VM の簡易作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | 受信トラフィックを許可するネットワーク セキュリティ グループ規則を作成します。 このサンプルでは、HTTP トラフィック用にポート 80 を開きます。 |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | VM に仮想マシン拡張機能を追加して実行します。 このサンプルでは、カスタム スクリプト拡張機能を使用して IIS をインストールします。|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)のページにあります。


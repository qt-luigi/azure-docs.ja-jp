---
title: "Azure Data Lake Store の Spark パフォーマンス チューニング ガイドライン | Microsoft Docs"
description: "Azure Data Lake Store の Spark パフォーマンス チューニング ガイドライン"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 29d4a361f98c63dab30155855d603a809eb804c8
ms.openlocfilehash: 2109744fb7ffdfafb7a86bbea355e119718af099


---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>HDInsight の Spark と Azure Data Lake Store のパフォーマンス チューニング ガイダンス

Spark のパフォーマンスをチューニングするときは、クラスター上で実行されるアプリの数を考慮する必要があります。  既定では、4 つのアプリを同時に HDI クラスターで実行することができます (注: この既定の設定は変更される可能性があります)。  使用するアプリ数をより少なくすることができます。そうすれば、既定の設定をオーバーライドして、これらのアプリでより多くのクラスターを使用できます。  

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* Data Lake Store アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Store を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。
* **Azure Data Lake Store で実行中の Spark クラスター**。  詳細については、「[HDInsight Spark クラスターを使用して Data Lake Store のデータを分析する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)」を参照してください。
* **ADLS のパフォーマンス チューニング ガイドライン**。  一般的なパフォーマンスの概念については、「[Data Lake Store Performance Tuning Guidance (Data Lake Store のパフォーマンス チューニング ガイドライン)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)」を参照してください。 

## <a name="parameters"></a>parameters

Spark ジョブを実行するときは、以下が ADLS のパフォーマンスを向上させるためにチューニングできる最も重要な設定です。

* **Num-executors** - 実行できる同時実行タスクの数。

* **Executor-memory** - 各 Executor に割り当てられるメモリの量。

* **Executor-cores** - 各 Executor に割り当てられるコアの数。                     

**Num-executors**Num-executors では並列で実行できるタスクの最大数を設定します。  並列で実行できるタスクの実際の数は、メモリと、クラスターで利用できる CPU リソースによって制限されます。

**Executor-memory** 各 Executor に割り当てられるメモリの量です。  各 Executor に必要なメモリは、ジョブによって異なります。  複雑な操作では、多くのメモリが必要です。  読み取りと書き込みのような単純な操作では、必要なメモリは少なくなります。  Ambari で各 Executor のメモリの量を表示できます。  Ambari で Spark に移動し、[Configs](構成) タブを表示します。  

**Executor-cores** Executor あたりに使用するコアの量を設定します。この量で、Executor ごとに実行できる並列スレッドの数が決定されます。  たとえば、executor-cores = 2 の場合、各 Executor は 2 つの並列タスクを実行できます。  必要な executor-cores はジョブによって決まります。  大量の I/O を使用するジョブでは、タスクあたりのメモリの消費量は多くないため、各 Executor はより多くの並列タスクを処理できます。

HDInsight で Spark を実行する場合は、既定では、各物理コアに対して&2; つの仮想 YARN コアが定義されます。  この数によって、同時実行と複数スレッド間のコンテキスト切り替え量のバランスをうまくとります。  

## <a name="guidance"></a>ガイダンス

Data Lake Store 内のデータを操作する Spark 分析ワークロードを実行する場合は、Data Lake Store のパフォーマンスを最大限に引き出すために、HDInsight の最新バージョンを使用することをお勧めします。 ジョブが I/O 集中型の場合、パフォーマンス向上を目的として特定のパラメーターを構成できます。  Azure Data Lake Store は、高スループットを処理できるスケーラブルなストレージ プラットフォームです。  ジョブが主に読み取りまたは書き込みで構成されている場合は、Azure Data Lake Store との間の I/O の同時実行性を向上させると、パフォーマンスが向上する可能性があります。

I/O 集中型のジョブの同時実行性を向上させる一般的な方法はいくつかあります。

**手順 1: クラスターで実行するアプリの数を決定する** – 現在の 1 つを含めて、いくつのアプリがクラスターで実行されるかを知っておくべきです。  各 Spark 設定の既定値は、同時に実行するアプリは 4 つと想定しています。  そのため、各アプリに対して使用可能なクラスターは 25% しかありません。  優れたパフォーマンスを得るには、Executor の数を変更することで、既定の設定をオーバーライドします。  

**手順 2: Executor-memory を設定する** – 最初に設定するのは、Executor-memory です。  メモリは、実行しようとしているジョブに依存します。  Executor あたりに割り当てるメモリを少なくすることで、同時実行数を増やすことができます。  ジョブを実行したときにメモリ不足例外が発生した場合は、このパラメーターの値を大きくする必要があります。  別の方法としては、メモリ容量が大きいクラスターを使用してメモリ量を増やすか、クラスター内のサイズを増やすことが挙げられます。  メモリの量が増えると使用できる Executor の数も増えて、同時実行性が向上されます。

**手順 3: Executor-cores を設定する** – I/O 集中型ワークロードには複雑な操作がないため、Executor あたりの並列タスク数を増やすために、Executor-cores 数を多く設定して始めると便利です。  適切な出発点として、Executor-cores を 4 に設定します。   

    executor-cores = 4
Executor-cores の数を増やすと多くの並列処理を行うことができます。Executor-cores を変えて試してみてください。  さらに複雑な操作を含むジョブの場合は、Executor あたりのコアの数を減らす必要があります。  Executor-cores を 4 より大きくすると、ガベージ コレクションが非効率的になりパフォーマンスが低下する可能性があります。

**手順 4: クラスターの YARN メモリの量を決定する** – この情報は、Ambari で使用できます。  YARN に移動し、[Configs] (構成) タブを表示します。  YARN メモリは、このウィンドウに表示されます。  
注: ウィンドウには、既定の YARN コンテナーのサイズも表示されます。  YARN コンテナーのサイズは、Executor パラメーターごとのメモリと同じです。

    Total YARN memory = nodes * YARN memory per node
**手順 5: Num-executors を計算する**

**メモリの制約を計算する** - Num-executors パラメーターは、メモリまたは CPU のいずれかで指定します。  メモリの制約は、アプリケーションで使用可能な YARN メモリの量によって決まります。  合計 YARN メモリを取得して、Executor-memory で除算します。  制約は、アプリの数に応じてスケールを小さくする必要があるため、アプリの数で除算します。

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**CPU の制約を計算する** - CPU の制約は、Executor あたりのコアの数で除算した合計仮想コア数として計算します。  各物理コアに対して仮想コアは 2 つあります。  メモリの制約と同様に、アプリの数で除算しました。

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Num-executors を設定する** – Num-executors パラメーターは、最小限のメモリの制約と、CPU の制約から決定されます。 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Num-executors を大きな数に設定してもパフォーマンスは必ずしも増加しません。  Executor をさらに追加すると、追加された各 Executor に余分なオーバーヘッドが加わり、パフォーマンスを低下させる可能性がある点を考慮する必要があります。  Num-executors は、クラスター リソースによって制限されます。    

## <a name="example-calculation"></a>計算例

現在 8 つの D4v2 ノードから成るクラスターがあり、実行しようとしているアプリを含む 2 つのアプリが実行されるとします。  

**手順 1: クラスターで実行するアプリの数を決定する** – 実行しようとしているアプリを含む 2 つのアプリがクラスターにあることがわかっています。  

**手順 2: Executor-memory を設定する** – この例では、I/O 負荷の高いジョブには、6GB の Executor-memory で十分だと判断します。  

    executor-memory = 6GB
**手順 3: Executor-cores を設定する** – これは I/O 負荷の高いジョブであるため、各 Executor のコア数は 4 に設定できます。  Executor あたりのコア数を 4 より大きくすると、ガベージ コレクションの問題が発生する可能性があります。  

    executor-cores = 4
**手順 4: クラスターの YARN メモリの量を決定する** – 各 D4v2 が 25 GB の YARN メモリを持つことを確認するために Ambari に移動します。  8 つのノードがあるため、使用可能な YARN メモリ量は 8 を乗算して求めます。

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**手順 5: Num-executors を計算する** – Num-executors パラメーターは、最小メモリの制約と CPU の制約を Spark で実行されるアプリの数で除算して求めます。    

**メモリの制約を計算する** – メモリの制約は、Executor あたりのメモリで除算した YARN メモリの合計として計算します。

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**CPU の制約を計算する** - CPU の制約は、Executor あたりのコアの数で除算した合計 yarn コア数として計算します。
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Num-executors を設定する**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    




<!--HONumber=Jan17_HO2-->



---
title: "Azure Application Insights での開発、テスト、およびリリースのテレメトリの分離| Microsoft Docs"
description: "開発、テスト、および運用スタンプのテレメトリを異なるリソースに送信します。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 896bf83de9095007e4f189f50a5e13c216e6ebd2
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017


---
# <a name="separating-telemetry-from-development-test-and-production"></a>開発、テスト、および運用のテレメトリの分離

Web アプリケーションの次のバージョンを開発する際に、新しいバージョンの [Application Insights](app-insights-overview.md) テレメトリとリリース済みのバージョンのテレメトリが混在することがないように設定できます。 混乱を避けるには、分離インストルメンテーション キー (ikeys) を使用して、異なる開発段階のテレメトリを別の Application Insights リソースに送信します。 バージョンが別の段階に進むときにインストルメンテーション キーを簡単に変更できるように、構成ファイルではなくコード内に ikey を設定できます。 

(システムが Azure クラウド サービスである場合は、[個別の iKey を設定する別の方法](app-insights-cloudservices.md)もあります。)

## <a name="about-resources-and-instrumentation-keys"></a>リソースとインストルメンテーション キーについて

Web アプリに対する Application Insights の監視を設定するときは、Microsoft Azure 内に Application Insights *リソース*を作成します。 アプリから収集されたテレメトリを表示して分析するには、Azure ポータルでこのリソースを開きます。 リソースは、*インストルメンテーション キー* (iKey) によって識別されます。 アプリを監視するために Application Insights パッケージをインストールするとき、インストルメンテーション キーを構成して、テレメトリの送信先を認識できるようにします。

通常、さまざまなシナリオに応じて、複数のリソースまたは単一の共有リソースを使用することを選択します。

* 複数の独立したアプリケーション - アプリごとに別のリソースと ikey を使用します。
* 1 つのビジネス アプリケーションの複数のコンポーネントまたはロール - すべてのコンポーネント アプリに対して[単一の共有リソース](app-insights-monitor-multi-role-apps.md)を使用します。 cloud_RoleName プロパティによって、テレメトリをフィルター処理したりセグメント化したりできます。
* 開発、テスト、およびリリース - 'スタンプ' 内のシステムのバージョンまたは運用段階ごとに、別のリソースと ikey を使用します。
* A |B テスト - 単一のリソースを使用します。 TelemetryInitializer を作成して、バリアント型を識別するプロパティをテレメトリに追加します。


## <a name="dynamic-ikey"></a> 動的なインストルメンテーション キー

運用の次の段階のコードを開発するときに変更しやすくするために、ikey を構成ファイルではなくコード内に設定します。

ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーを設定します。

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

この例では、さまざまなリソースの ikeys が Web 構成ファイルのさまざまなバージョンに配置されます。 リリース スクリプトの一部として行うことができる Web 構成ファイルのスワップは、ターゲット リソースをスワップします。

### <a name="web-pages"></a>Web ページ
iKey は、アプリの Web ページや、 [クイック スタート ブレードから取得したスクリプト](app-insights-javascript.md)内でも使用されます。 スクリプトにそのままコーディングするのではなく、サーバーの状態からこれを生成します。 たとえば、ASP.NET アプリで以下の操作を行います。

*Razor の JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>追加の Application Insights リソースを作成する
複数のアプリケーション コンポーネントのテレメトリまたは同じコンポーネントの異なるスタンプ (開発/テスト/運用) のテレメトリを分離するには、新しい Application Insights リソースを作成する必要あります。

[portal.azure.com](https://portal.azure.com)で、Application Insights リソースを追加します。

![[新規]、[Application Insights] の順にクリックする](./media/app-insights-separate-resources/01-new.png)

* **アプリケーションの種類** に応じて、概要ブレードに表示されるものと [メトリック エクスプローラー](app-insights-metrics-explorer.md)で使用できるプロパティが決まります。 自分のアプリの種類が表示されない場合、Web ページの Web の種類を 1 つ選択します。
* **リソース グループ** は、 [アクセス制御](app-insights-resources-roles-access-control.md)などのプロパティを管理するのに便利です。 開発、テスト、および実稼働用に別々のリソース グループを使用できます。
* **サブスクリプション** は、Azure での支払いアカウントです。
* **場所** は、データの保存場所です。 現在、これは変更できません。 
* **ダッシュボードへの追加** は、Azure ホーム ページ上のリソースに対するクイック アクセス タイルを入れます。 

リソースの作成には数秒かかります。 完了したら、アラートが表示されます。

([PowerShell スクリプト](app-insights-powershell-script-create-resource.md) を作成して、リソースを自動で作成できます。)

### <a name="getting-the-instrumentation-key"></a>インストルメンテーション キーの取得
インストルメンテーション キーは作成したリソースを識別します。 

![Essentials、インストルメンテーション キーの順にクリックし、Ctrl キーを押しながら C キーを押します。](./media/app-insights-separate-resources/02-props.png)

アプリがデータを送信する宛先のすべてのリソースのインストルメンテーション キーが必要です。

## <a name="filter-on-build-number"></a>ビルド番号でのフィルター処理
新しいバージョンのアプリを発行するときは、異なるビルドのテレメトリを区別する必要があります。

アプリケーション バージョン プロパティを設定することで、[検索](app-insights-diagnostic-search.md)と[メトリックス エクスプローラー](app-insights-metrics-explorer.md)の結果をフィルター処理できます。

![プロパティでのフィルター処理](./media/app-insights-separate-resources/050-filter.png)

アプリケーション バージョン プロパティを設定するには複数の方法があります。

* 直接設定します。

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* その行を [テレメトリ初期化子](app-insights-api-custom-events-metrics.md#defaults) にラップして、すべての TelemetryClient インスタンスが一貫して設定されるようにします。
* [ASP.NET] `BuildInfo.config`でバージョンを設定します。 Web モジュールは BuildLabel ノードからバージョンを取得します。 このファイルをプロジェクトに追加し、ソリューション エクスプローラーで [常にコピーする] プロパティを設定します。

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] MSBuild で自動的に BuildInfo.config を生成します。 そのためには、`.csproj` ファイルに数行を追加します。

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    これにより、 *プロジェクト名*.BuildInfo.config という名前のファイルが生成されます。 これは発行プロセスで BuildInfo.config という名前に変更されます。

    Visual Studio でビルドすると、ビルド ラベルにはプレースホルダー (AutoGen_...) が含まれます。 一方、MSBuild でビルドすると、適切なバージョン番号が設定されます。

    MSBuild がバージョン番号を生成できるようにするには、AssemblyReference.cs で `1.0.*` のようなバージョンを設定します。

## <a name="next-steps"></a>次のステップ

* [複数のロール用の共有リソース](app-insights-monitor-multi-role-apps.md)
* [A |B のバリアントを区別するためのテレメトリ初期化子を作成する](app-insights-api-filtering-sampling.md#add-properties)

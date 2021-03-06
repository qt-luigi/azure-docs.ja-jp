## <a name="understand-planned-vs-unplanned-maintenance"></a>計画済み、または計画外メンテナンスについて理解する
仮想マシンの可用性に影響を与える可能性のある Microsoft Azure プラットフォーム イベントには、計画的なメンテナンスと計画外のメンテナンスの 2 種類があります。

* **計画済みメンテナンス イベント** とは、仮想マシンを実行しているプラットフォーム インフラストラクチャの全体的な信頼性、パフォーマンス、セキュリティを向上させることを目的として、基板となる Azure プラットフォームに対して Microsoft が提供する定期的な更新を意味します。 これらの更新のほとんどは、仮想マシンやクラウド サービスに影響を及ぼすことなく実行されます。 ただし、プラットフォーム インフラストラクチャに必要な更新を適用するため、仮想マシンの再起動が求められる場合もあります。
* **計画外メンテナンス イベント** は、仮想マシンの基盤となっているハードウェや物理インフラストラクチャに何らかの不具合が発生した場合に行われます。 こうした不具合には、ネットワーク障害、ローカル ディスク障害、またはその他のラック レベルでの障害が挙げられます。 そのような障害が検知されると、Azure プラットフォームは、仮想マシンをホストしている異常な物理マシンから正常な物理マシンへと、仮想マシンを自動的に移行します。 このような例が発生することはまれですが、この場合も仮想マシンの再起動が求められる場合があります。

前述のようなイベントが 1 つ以上発生した場合にダウンタイムの影響を低減するため、下記のような高可用性のためのベスト プラクティスを仮想マシンに適用することをお勧めします。

* [冗長性実現のために複数の仮想マシンを可用性セット内に構成する]
* [可用性セット内の VM に管理ディスクを使用する]
* [各アプリケーション層に対して別々の可用性セットを構成する]
* [ロード バランサーと可用性セットを結合する]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>冗長性実現のために複数の仮想マシンを可用性セット内に構成する
アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 このような構成により、計画済み、または計画外のメンテナンス イベント中に、少なくとも 1 つの仮想マシンが利用可能となり、99.95% の Azure SLA を満たします。 詳細については、「 [Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)」を参照してください。

> [!IMPORTANT]
> 可用性セット内の仮想マシンが 1 つだけにならないようにしてください。 この構成の VM は、SLA 保証の対象とはならず、単一の VM が [Azure Premium Storage](../articles/storage/storage-premium-storage.md) を使用している場合を除き、Azure の計画されたメンテナンス イベント時にダウンタイムが発生します。 Premium Storage を使用する単一 の VM では、Azure SLA が適用されます。

基盤となる Azure プラットフォームにより、可用性セット内の各仮想マシンに**更新ドメイン**と**障害ドメイン**が割り当てられます。 所定の可用性セットに対して、同時に再起動される仮想マシンのグループと物理ハードウェアを示す、ユーザーが構成できない 5 つの更新ドメインが既定で割り当てられます (その後、最大 20 の更新ドメインを提供できるように Resource Manager のデプロイを増やすことができます)。 1 つの可用性セット内に 5 つ以上の仮想マシンが構成されているとき、6 つ目の仮想マシンは 1 つ目の仮想マシンと同じ更新ドメイン内に配置され、7 つ目は 2 つ目の仮想マシンと同じ更新ドメイン内に配置されるという方法で処理されます。 計画済みメンテナンス中は、更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。

障害ドメインは電源とネットワーク スイッチを共有する仮想マシンのグループを定義します。 既定では、可用性セット内に構成された仮想マシンは、Resource Manager のデプロイ用に最大 3 つの障害ドメインに分けられます (クラシックの場合は 2 つの障害ドメイン)。 仮想マシンを可用性セットに配置しても、アプリケーションがオペレーティング システムやアプリケーションの障害から保護されるわけではありませんが、潜在的な物理ハードウェア障害、ネットワーク障害、または電力の中断の影響を低下させることができます。

<!--Image reference-->
   ![更新ドメインと障害ドメインの構成の概念図](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>可用性セット内の VM に管理ディスクを使用する
現在、管理されていないディスクを持つ VM を使用している場合は、[可用性セット内の VM を Managed Disks を使用するように変換する](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set)ことを強くお勧めします。

[管理ディスク](../articles/storage/storage-managed-disks-overview.md)では、可用性セットの VM のディスクが、単一障害点にならないように相互に十分に分離されるため、可用性セットの信頼性が向上します。 これは、ディスクをさまざまな記憶域クラスターに自動的に配置することによって実現されます。 ハードウェアやソフトウェアの障害によって記憶域クラスターが機能しなくなった場合、そのスタンプ上のディスクを使用する VM インスタンスだけが機能しなくなります。 

![管理ディスク FD](./media/virtual-machines-common-manage-availability/md-fd.png)

> [!IMPORTANT]
> 管理対象の可用性セットに使用される障害ドメインの数は、リージョンによって異なります (リージョンあたり 2 つまたは 3 つになります)。 リージョンあたりの数を以下の表に示します。

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

[管理されていないディスク](../articles/storage/storage-about-disks-and-vhds-windows.md#types-of-disks)を持つ VM を使用する計画がある場合は、VM の仮想ハード ディスク (VHD) が[ページ BLOB](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) として格納されているストレージ アカウント用の、以下のベスト プラクティスに従います。 

1. **VM に関連付けられているすべてのディスク (OS とデータ) を同じストレージ アカウント内に保持する。**
2. ストレージ アカウントにさらに VHD を追加する前に、**ストレージ アカウント内の管理されていないディスクの数に関する[制限](../articles/storage/storage-scalability-targets.md)を確認する。**
3. **可用性セット内の VM ごとに個別のストレージ アカウントを使用します。** 同じ可用性セット内の複数の VM でストレージ アカウントを共有しないでください。 上のベスト プラクティスに従っていれば、異なる可用性セットの VM でストレージ アカウントを共有してもかまいません。

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>各アプリケーション層に対して別々の可用性セットを構成する
仮想マシンがすべてほぼ同一で、アプリケーションに対する役割が同じである場合は、アプリケーションの各層に対して別々の可用性セットを構成することをお勧めします。  同じ可用性セットの中に 2 つの異なる層を配置した場合、同じアプリケーション層内にあるすべての仮想マシンを一度に再起動できます。 各層に対する別々の可用性セット内に少なくとも 2 つの仮想マシンを構成することで、各層あたり 1 つ以上の仮想マシンの可用性が確保されます。

たとえば、ISS、Apache、Nginx を実行しているアプリケーションのフロントエンド内のすべての仮想マシンを、1 つの可用性セットに配置します。 このとき、フロントエンド仮想マシンのみが同じ可用性セット内に配置されるようにします。 同様に、別の可用性セットには、複製された SQL Server 仮想マシンまたは MySQL 仮想マシンのように、データ層の仮想マシンのみが配置されるようにします。

<!--Image reference-->
   ![アプリケーション層](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>ロード バランサーと可用性セットを結合する
[Azure ロード バランサー](../articles/load-balancer/load-balancer-overview.md) と可用性セットを結合することで、アプリケーションの復元性を最大化できます。 Azure ロード バランサーは、複数の仮想マシンにトラフィックを振り分けます。 当社の標準層の仮想マシンには Azure ロード バランサーが含まれています。 すべての仮想マシン層に Azure Load Balancer が含まれているわけではありません。 仮想マシンの負荷分散の詳細については、「 [仮想マシンの負荷分散](../articles/virtual-machines/virtual-machines-linux-load-balance.md)」を参照してください。

複数の仮想マシン間でトラフィックを分散するためのロード バランサーが構成されていない場合、計画済みメンテナンス イベントによって、トラフィックを提供している仮想マシンのみに影響が生じ、アプリケーション層の機能停止が生じます。 同じ層の複数の仮想マシンを、同じロード バランサーと可用性セット以下に配置することで、少なくとも 1 つのインスタンスによってトラフィックの提供を継続することができます。


<!-- Link references -->
[冗長性実現のために複数の仮想マシンを可用性セット内に構成する]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[各アプリケーション層に対して別々の可用性セットを構成する]: #configure-each-application-tier-into-separate-availability-sets
[ロード バランサーと可用性セットを結合する]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[可用性セット内の VM に管理ディスクを使用する]: #use-managed-disks-for-vms-in-an-availability-set


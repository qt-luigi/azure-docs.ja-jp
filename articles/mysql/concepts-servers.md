---
title: "Azure Database for MySQL のサーバーの概念 | Microsoft Docs"
description: "このトピックでは、Azure Database for MySQL サーバーを操作するための考慮事項とガイドラインを示します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 33508edb1b1aee058bff4b186f76d172f11f272f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Azure Database for MySQL のサーバーの概念

このトピックでは、Azure Database for MySQL サーバーを操作するための考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーとは

Azure Database for MySQL サーバーは、複数のデータベースの中央管理ポイントです。 これは、オンプレミスで一般的な MySQL サーバー コンストラクトと同じです。 具体的には、Azure Database for MySQL サービスは管理され、パフォーマンスが保証されるほか、アクセスと機能がサーバー レベルで公開されます。

Azure Database for MySQL サーバーの特徴を次に示します。

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- サーバーおよびデータベース アクセスの接続エンドポイントを提供します。
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- 複数のバージョンで使用できます。 詳細については、[サポートされる Azure Database for MySQL データベース バージョン](./concepts-supported-versions.md)に関するページをご覧ください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーへの接続および認証方法

次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

|||
| :-- | :-- |
| **認証と権限承認** | Azure Database for MySQL サーバーは、ネイティブ MySQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。 |
| **プロトコル** | サービスは、MySQL で使用されるメッセージ ベースのプロトコルをサポートしています。 |
| **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 |
| **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーまたはそのデータベースへのすべてのアクセスを遮断します。 「[Azure Database for MySQL サーバーのファイアウォール規則](./concepts-firewall-rules.md)」を参照してください。 |
| **SSL** | アプリケーションとデータベース サーバーの間に SSL 接続を適用できます。  「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。 |
|||

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法
Azure Database for MySQL サーバーを管理するには、Azure Portal または Azure CLI を使用します。

## <a name="next-steps"></a>次のステップ
- サービスの概要については、[Azure Database for MySQL の概要](./overview.md)に関するページをご覧ください
- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-service-tiers.md)に関するページをご覧ください
- サービスへの接続については、「[Azure Database for MySQL の接続ライブラリ](./concepts-connection-libraries.md)」を参照してください。


---
title: "Azure AD: パスワードのリセット | Microsoft Docs"
description: "セルフサービスによるパスワードのリセットを使用して職場または学校アカウントへのアクセスを回復する"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 19a17951b40dcad26c846a45ee786ab5339e59b8
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---
# <a name="help-i-forgot-my-azure-ad-password"></a>Azure AD パスワードを忘れた場合

パスワードを忘れた場合や、IT スタッフからパスワードを受け取っていない場合、アカウントからロックアウトされた場合、パスワードを変更する場合は、Microsoft がお手伝いします。

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>職場または学校アカウントのパスワードをリセットまたはロック解除する

職場または学校アカウントにアクセスするには、次の手順に従って、Azure AD のセルフサービスによるパスワードのリセット (SSPR) にアクセスします。

1. 職場または学校のサインイン ページから **[アカウントにアクセスできませんか?]** リンクをクリックし、**[職場または学校アカウント]** をクリックするか、[パスワードのリセット用ページ](https://passwordreset.microsoftonline.com/)に直接アクセスします。

   > [!NOTE]
   > hotmail.com や outlook.com など、個人のアカウントを回復するには、[この記事の提案事項](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)を試してください。
   >

    ![アカウントにアクセスできない場合][Login]

2. 職場または学校の**ユーザー ID** を入力し、画面に表示された文字を入力することで自分がロボットではないことを証明して、**[次へ]** をクリックします。

   > [!NOTE]
   > この機能が IT スタッフによって有効にされていない場合は、IT スタッフが電子メールや自分の Web ポータルを通じてサポートできるように、"管理者に連絡してください" というリンクが表示されます。
   >

3. IT スタッフによる SSPR の構成内容に応じて、以下の項目のうち 1 つ以上が表示されます。 「[セルフサービスによるパスワードのリセットを登録する](active-directory-passwords-reset-register.md)」の記事で使用する前に、自分または IT スタッフがこれらの情報の一部を設定しています。

   * **連絡用電子メール アドレスにメールを送信**
   * **携帯電話に SMS 送信**
   * **携帯電話に発信**
   * **会社電話に発信**
   * **セキュリティの質問に回答する**

   オプションを選択し、正しい回答を入力し、**[次へ]** をクリックします。

   ![認証データを確認する][Verification]

4. IT スタッフが追加の確認を求めている場合もあります。その場合は、別のオプションを選択して手順 3. を繰り返す必要があります。
5. **[新しいパスワードの選択]** ページで、新しいパスワードを入力し、パスワードを確認して、**[完了]** をクリックします。 大文字、小文字、数字、および特殊文字を組み合わせた 8 ～ 16 文字のパスワードをお勧めします。

   > [!NOTE]
   > アカウントのロックを解除する必要がある場合は、この時点でロック解除のみか、パスワードの変更とロック解除を行うかを選択します。
   >

6. **[パスワードがリセットされました]** と表示されたら、新しいパスワードでサインインできます。

    ![パスワードがリセットされました][Complete]

これで、アカウントにアクセスできるようになります。アクセスできない場合は、組織の IT スタッフに連絡してサポートを依頼してください。

"Microsoft (\<組織> の代理)" などのアカウントから送信された確認メールを受け取ることがあります。 セルフサービスによるパスワードのリセットを利用してアカウントへのアクセスを回復した覚えがないのに、このようなメールが届いた場合は、組織の IT スタッフに問い合わせてください。

## <a name="change-my-password"></a>パスワードを変更する

パスワードを既に知っており、それを変更する必要がある場合は、以下の手順に従って、パスワードを変更します。

### <a name="change-your-password-from-the-office-365-portal"></a>Office 365 ポータルからパスワードを変更する

通常、Office ポータルを使用してアプリケーションにアクセスしている場合は、この方法を使用します。

1. 既存のパスワードを使用して [Office 365 アカウント](https://www.office.com)にサインインする
2. 右上にある自分のプロファイルをクリックし、**[アカウントを表示]** をクリックします。
3. **[セキュリティとプライバシー]** > **[パスワード]** の順にクリックします。
4. 古いパスワードを入力し、新しいパスワードを設定して、それを確認します。その後、**[送信]** をクリックします。

### <a name="change-your-password-from-the-azure-access-panel"></a>Azure アクセス パネルからパスワードを変更する

通常、Azure アクセス ポータルを使用してアプリケーションにアクセスしている場合は、この方法を使用します。

1. 既存のパスワードを使用して [Azure アクセス ポータル](https://myapps.microsoft.com/)にサインインします
2. 右上にある自分のプロファイルをクリックし、**[プロファイル]** をクリックします。
3. **[パスワードの変更]** をクリックします。
4. 古いパスワードを入力し、新しいパスワードを設定して、それを確認します。その後、**[送信]** をクリックします。

## <a name="common-problems-and-their-solutions"></a>一般的な問題とその解決方法

 次に、一般にエラーになる場合とその解決方法を示します。

| エラー ケース| 表示されるエラー| 解決策 |
| --- | --- | --- |
| ユーザー ID を入力した後に「管理者にお問い合わせください」と表示されます | 管理者にお問い合わせください <br> <br> お客様のユーザー アカウントのパスワードは Microsoft によって管理されていません。 そのため、パスワードを自動的にリセットすることはできません。 <br> <br> サポートを受けるには IT スタッフに問い合わせる必要があります。 | このメッセージは、IT スタッフがオンプレミス環境でパスワードを管理しており、[アカウントにアクセスできません] リンクからパスワードをリセットすることを許可していない場合に表示されます。 <br> <br> パスワードをリセットするには、IT スタッフに直接連絡して支援を依頼し、Office 365 からパスワードをリセットしたいことを伝え、この機能を有効にしてもらう必要があります。|
| ユーザー ID の入力後、「アカウントは、パスワード リセットが有効になっていません。」エラーが表示されます | このアカウントはパスワードのリセットが有効になっていません <br> <br> 申し訳ありませんが、IT スタッフはこのサービスで使用できるアカウントを設定していません。 <br> <br> ご希望の場合は、こちらで組織の管理者に連絡してパスワードをリセットできます。 | これは IT スタッフが [アカウントにアクセスできません] リンクからのパスワードのリセットを有効にしていないため、または機能を使用するライセンスを付与していないために表示されます。 <br> <br> パスワードをリセットするには、管理者に問い合わせるリンクをクリックして会社の IT スタッフに電子メールを送信するか、Office 365 からパスワードをリセットしたいことを伝え、この機能を有効にしてもらう必要があります。 |
| ユーザー ID の入力後に「お客様のアカウントを確認できませんでした」エラーが表示されます | お客様のアカウントを確認できませんでした <br> <br> ご希望の場合は、こちらで組織の管理者に連絡してパスワードをリセットできます。 | このメッセージは、パスワードのリセットは有効になっているが、サービスの使用を登録していない場合に表示されます。 パスワードのリセットを登録するには、アカウントに再アクセスできるようになった後に http://aka.ms/ssprsetup に進みます。 <br> <br> パスワードをリセットするには、管理者に問い合わせるリンクをクリックし、会社の IT スタッフに電子メールを送信します。 |

## <a name="next-steps"></a>次のステップ

* [セルフサービスによるパスワードのリセットを使用するための登録方法](active-directory-passwords-reset-register.md)
* [パスワードのリセット登録ページ](http://aka.ms/ssprsetup)
* [パスワードのリセット用ポータル](https://passwordreset.microsoftonline.com/)
* [Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "ログイン ページ: アカウントにアクセスできませんか?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "認証データを確認する"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "パスワードを変更する"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "パスワードがリセットされました"


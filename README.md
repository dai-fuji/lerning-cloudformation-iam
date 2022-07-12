# IAMユーザー自身のパス・MFAの設定変更を有効にするポリシー

## なぜこのポリシーが必要なのか
- IAMユーザーにはIAM権限を原則付与しない
- IAM権限を持っているということは、ポリシーのアタッチや内容等を制御できることになる。これはAdministrator権限を持っていることと同義となる
- ただし、IAM権限を全て拒否してしまうと、自身のMFAの登録やパスワード変更ができない。そのため、最低限これだけは付与する必要がある
  
<br>

## 権限を付与するAction一覧

|アクション|内容|
| --- | --- |
|ChangePassword|IAM ユーザーに自分のパスワードを変更する許可を付与|
|CreateAccessKey|指定された IAM ユーザーのアクセスキーとシークレットアクセスキーを作成する許可を付与|
|CreateVirtualMFADevice|新しい仮想 MFA デバイスを作成する許可を付与| 
|DeactivateMFADevice|指定された MFA デバイスを非アクティブ化し、最初に有効にされた IAM ユーザーとの関連付けを削除する許可を付与| 
|DeleteAccessKey|指定された IAM ユーザーに関連付けられたアクセスキーペアを削除する許可を付与| 
|DeleteVirtualMFADevice|仮想 MFA デバイスを削除する許可を付与| 
|EnableMFADevice|MFA デバイスを有効にして、指定された IAM ユーザーに関連付けるアクセス許可を付与します| 
|UpdateAccessKey|指定されたアクセスキーステータスをアクティブまたは非アクティブとして更新する権限を付与します| 
|UpdateSigningCertificate|指定されたユーザー署名証明書のステータスを有効または無効に更新する許可を付与|
|UploadSigningCertificate|X.509 デジタル署名用証明書をアップロードし、指定の IAM ユーザーに関連付けるアクセス許可を付与します| 
|UpdateLoginProfile|指定された IAM ユーザーのパスワードを変更する許可を付与| 
|ResyncMFADevice|指定された MFA デバイスをその IAM エンティティ (ユーザーまたはロール) に同期させるアクセス権限を付与します| 
|Get*|権限の状態を確認する、読み込み系のアクション一式|
|List*|同上|

  
<br>

## ポリシー詳細
- ポリシーのResourceとして指定するAccountId及びIAMユーザーを動的に制御する為に**IAMポリシーの変数**とCfnの**疑似パラメータ**を使用
  - IAMポリシーの変数・・・`${aws:username}` [公式：IAMポリシーの要素：変数](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/reference_policies_variables.html#policy-vars-tags)
  - 疑似パラメータ・・・・・`AWS::AccountId` [公式：疑似パラメータ](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html)  
- Cfn記述の際にポリシー要素の変数には[Sub関数](https://docs.aws.amazon.com/ja_jp/ja_jp/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-sub.html)が適用されないように`!`を付加する必要がある
```
Resource:
  - !Sub "arn:aws:iam::${AWS::AccountId}:user/${!aws:username}"
  - !Sub "arn:aws:iam::${AWS::AccountId}:mfa/${!aws:username}"
```

##### メモ
- [ARN名の（アマゾンリソースネーム）の形式](https://docs.aws.amazon.com/ja_jp/general/latest/gr/aws-arns-and-namespaces.html)<br>
  `arn:partition:service:region:account-id:resource-type/resource-id`

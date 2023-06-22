# Power Virtual Agents with Azure OpenAI Service Integration Solution

## 概要
このリポジトリは、神戸市役所でのChatGPTの試行利用に向けて作成したフロー等をソリューション化し公開するものです。
このソリューションを利用することで、Power Virtual Agents から Azure OpenAI Service にデプロイしたGPTインスタンスとやりとりするためのフローが利用できます。
また、利用状況やコストの分析のため、Azure OpenAI Service とのやりとり内容を Dataverse に作成したログ用のデータベースに保存する機能も備えています。

このソリューションを使用するためには、Power Virtual Agents が利用でき、かつ、Azure OpenAI Service において GPT-3.5-turbo(ChatGPT) または GPT-4 の利用承認を受けた上で当該モデルをデプロイしている必要があります。

また、Teams で使用する場合は、Teams を利用できる状態である必要があります。

## 導入方法
以下の手順に従って導入してください。
なお、以下の手順を行う前提として、Power Virtual Agentsが既に利用できる状態であり、かつ、Azure AD の設定および Azure OpenAI を利用するための Azureサブスクリプションの作成までが完了している必要があります。   
[(参考)Microsoft Azure](https://azure.microsoft.com/ja-jp/free/cognitive-services/)  
[(参考)Microsoft Power Virtual Agents](https://powervirtualagents.microsoft.com/ja-jp/)  

また、Teamsの特定のチームで利用する場合は、予めDataverse for Teams環境を用意しておいてください。  
Dataverse for Teams環境は、Teams上からPower Appsを開き、当該チーム上で新規アプリを作成することで環境が作成できます。(実際にキャンバスアプリ等を作成する必要はありません)  
[(参考)Microsoft Dataverse for Teams について | Microsoft Learn](https://learn.microsoft.com/ja-jp/power-platform/admin/about-teams-environment)

1. Azure OpenAI Service にてリソースを作成し、GPTのモデルをデプロイした上で、API KeyとURIを取得  
[(参考)Azure OpenAI Service を使用してリソースを作成し、モデルをデプロイする | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/cognitive-services/openai/how-to/create-resource?pivots=web-portal)   
※ API KeyとURIは、Azure OpenAI Studioの、プレイグラウンド > チャット > チャットセッション > コードの表示 から確認できます。  
[(参考)モデルのデプロイと Azure OpenAI Service を使用したテキストの生成を行う | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/cognitive-services/openai/quickstart?tabs=command-line&pivots=programming-language-studio)
3. 本リポジトリに公開しているインポート用のファイル(Zipファイル)をダウンロード
4. Power Apps を開く  
※ Teamsの特定のチームで利用する場合は、予め作成したDataverse for Teams環境に移動する
5. 画面左側のメニューから「ソリューション」を選択
6. ソリューションの画面の上部にある「ソリューションをインポート」を選択
7. ファイル選択画面が表示されるため、2.でダウンロードしたZipファイルを選択し「次へ」を選択
8. 環境変数に1.で取得したAPI KeyとURIを設定のうえ、インポートする
9. インポートにより、31つのオブジェクトがインポートされたことを確認  
※ 内訳は、クラウドフロー、チャットボット、テーブルがそれぞれ1つ、チャットボットサブコンポーネントが26つ、環境変数が2つ
10. Power Automate を開く  
※ Teamsの特定のチームで利用する場合は、予め作成したDataverse for Teams環境に移動していることを確認
11. 画面左側のメニューから「マイフロー」を選択
12. インポートしたフロー「GPT-Flow」が表示されていることを確認の上、「GPT-Flow」を選択
13. フローの概要画面から「編集」を選択し編集画面を開く
14. フローの一番最後に、Dataverseに対する「新しい行を追加する」アクションを選択
15. テーブル名の設定項目において、同じくソリューションとしてインポートした「GPT-Bot-Log」を選択  
※「詳細オプションを表示する」を選択すると、「GPT-Bot-Log」テーブルの各項目に記録するデータが設定されていることが確認できる
16. 「保存」を選択し、フローを保存する
17. Power Virtul Agents を開き、画面左側のメニューから「チャットボット」を選択
18. チャットボットの一覧の中に、「GPT-Bot」が表示されていることを確認し、選択  
※ 「GPT-Bot」> トピック >「Fallback」がメインのフローとなるが、利用にあたっては特に編集は不要
19. 画面左側のメニューから「公開」を選択
20. 「公開」ボタンを選択し、ボットを公開状態にする
21. 公開範囲を設定するため「他のユーザーがボットを利用可能にする」を選択
22. 「可用性オプション」を選択
23. Teamsアプリとして公開するにあたり、公開範囲(チーム/組織内)を選択・設定する
24. この時点で利用可能なユーザーのTeams上でアプリが利用になるはずだが、もし見つからない場合は、Teams内のアプリ検索画面から「GPT-Bot」と検索し利用することも可能
25. アプリが追加された際の初期メッセージは、Power Virtul Agents >「GPT-Bot」> トピック >「Greeting」で設定できる。現在の設定は以下のとおり

```
こんにちは！  
これは試行用のChatGPTボットです。  
以下の点に留意してお使いください。
・非公開情報は入力しないでください。
・ボットは30分以内程度の会話の文脈を汲み取って返答しますが、「リセット」と指示することでこれをリセットできます。
・一度に指示できる文字数は決まっており、これを超えるとエラーになります。文字数には入力した文字以外に会話の文脈も含まれます。  
　これが原因と考えられるエラーが続いた場合は「リセット」と指示し会話の文脈をリセットしてください。
```

```
このチャットの内容は他の職員からは見えません。  
「この使い方は便利！」「このプロンプトは使える！」といったTipsがありましたら、ぜひTeamsの「チーム」→「ChatGPT検証」→「一般」のスレッドで積極的に共有してください。
```

```
その他の留意事項等については、「生成AIの利用ガイドライン」をよくお読みいただき、正しくお使いください。
```

## 使用方法
1. Teamsのチャットまたはアプリ選択画面から「GPT-Bot」を選択しチャット画面を開く
2. チャットの入力欄に対し入力・送信することで、GPTからの返答が返ってくる
3. 会話の文脈をリセットしたい場合は、「リセット」とチャットに対して入力することでリセットできる
  
会話の履歴は Power Virtual Agents の変数(``bot.FullDialog``)に保持され、会話のセッション中は維持されます。  
会話のセッションについては Power Virtual Agents の仕様に則り、最後の会話から30分程度で切れます。  
[(参考)請求済みセッションを表示する (プレビュー) | Microsoft Learn](https://learn.microsoft.com/ja-jp/power-virtual-agents/analytics-billed-sessions)  

## ライセンス・許諾
このリポジトリで公開しているソリューションの著作権は神戸市に帰属しますが、MITのもと配布されています。
MITに従えば、どなたでも利用、改変、及び再配布が可能です。
なお、このソリューションを利用するために必要な、Microsoftの Azure 及び Azure OpenAI Service, 並びに Power Apps, Power Automate, Power Virtual Agents 等の製品は、Microsoft Corporation の登録商標です。

## クレジット
このソリューションは、Henry Jammes氏によって公開されているソリューションをベースに作成しました。
- [GitHub](https://github.com/microsoft/PowerVirtualAgentsSamples/tree/c75ea6c4e0311e59a98d70b582c8e8184edca638)
- [Blog](https://powerusers.microsoft.com/t5/Power-Platform-Integrations/Integrate-a-PVA-chatbot-with-Azure-OpenAI-ChatGPT-using-the-Chat/m-p/2146180#M249)

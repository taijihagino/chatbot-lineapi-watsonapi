# Real original character of stores can talk with customer as a LINE chatbot

## Description
Get real original character as a LINE chatbot for any stores to increase their sales. You can make the chatbot easily for Japan popular chat tool LINE. It can effect your commerce. Watson Assistant API can help building your LINE chatbot application.

## Related Blog
[How to build this application](https://medium.com/@taiponrock/line%E3%83%81%E3%83%A3%E3%83%83%E3%83%88%E3%83%9C%E3%83%83%E3%83%88%E3%81%A8watson%E3%82%92%E9%80%A3%E6%90%BA%E3%81%99%E3%82%8B-8a7d89a49e57) *now just in Japanese

## How to build
We have lots of chat/text tools such kind of WhatsApp, WeChat, Slack, Facebook, Messenger, and more...
And currently in Japan LINE is most popular chat service/application. 
Get real original character as a LINE chatbot for any stores to increase their sales. You can make the chatbot easily for Japan popular chat tool LINE. It can effect your commerce. Watson Assistant API can help building your LINE chatbot application.

## Overview
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/001.png)

## IBM Cloud Side

### Make Watson Assistant
Select Watson Assistant from IBM Cloud Catalog.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/002.png)

Fill down Service Name, and select Region(US South is recomended), Org, and Space.
You can use it as Light Plan, and then press the create button.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/003.png)

Get credencial information when Assistant API created.
If the credencial has not yet created automaticaly, you can generate new credencial on Service Credencial Menu.
Note API Endpoint URL, USER, and PASSWORD. 
※No need this operation if Node-RED will be on IBM Cloud and bind with Assistant API.

### Make Watson Assistant Workspace
This is a workspace for Watson Assistant can train definitions and flows of chat talks for chatbot application. It has a unique ID which will be used for an application to call API.
Select Assistant API you created.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/004.png)

Launch Watson Assistant Tool. Press the Launch Button.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/005.png)

Click the create button, open new flow window, but this time we use "flow import function". Click the Arrow Icon next Create button.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/006.png)

You can download [the flow definision file](https://raw.githubusercontent.com/taijihagino/chatbot-lineapi-watsonapi/master/workspace-4f12f1bc-fb3c-47db-bca4-a0d32d18d1aa.json)  Press the Coose a file button to select JSON file.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/007.png)

Complete for preparing IBM Cloud Side. Do you want to work more? Sorry, that's all. 
If you want to custom it more, you can modify it or create new workspace.

## Create Node-RED Application
Create Node-RED on IBM Cloud. Select "Node-RED Starter" from Starter Kit category on Catalog. And then you can establish your Node-RED.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/008.png)

Bind Watson Assistant API you created to your Node-RED.
Select your Node-RED App.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/009.png)

Create new Connect from Connection Menu.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/010.png)

Click Connect button (appear with mouse over)
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/011.png)

Please wait a moment for re-staging process. 
After complete the process you can access your Node-RED Flow Editor.
It is very simple to meke Node-RED application foe this application. The completed flow view is below;
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/012.png)

Create server-side application which is called from LINE webhook, on Node-RED. It will call Watson Assistant API. You can download [completed flow definition](https://github.com/taijihagino/chatbot-lineapi-watsonapi)

The first node "http in" is for configration URL to access this Node-RED application. Already set the URL "/line_hook" on the flow you downloaded.

Node-RED is just Node.js application, so the application you will create on it is also Node.js application. IBM Cloud alocated unique URL on your application. This application now you created on Node-RED has the URL below;

`
https:// *<Your Node-RED App Name>* .mybluemix.net/ *<Path you set on "http in node">* /

例）https://fillgapapp01-nodered01.mybluemix.net/line_hook/
`

次に「Function」ノードの中を確認しましょう。
1つ目の「Function」ノード「getText」は、LINE＠のAPIがNode-REDのWebhook URLを呼び出した時に渡してくる返信用トークンの値を保持しておくための処理です。

`
//flowへ格納
flow.set(“replyToken”,msg.payload.events[0].replyToken);

return msg;
`

flowへ格納した値は、同じフロー内であればいつでもどこでも取り出せます。

次に「Change」ノードです。
Watson Assistant APIが受けられる形（msg.payload）に、LINE@から投げられてきた会話文を入れ直します。

そうしたら、実際にWatson Assistant APIを呼び出します。
AssistantのワークスペースのIDを設定してください。IBM CloudでNode-REDとAssistant APIをバインドしてあればこのような設定画面となり、ユーザー、パスワードの資格情報を使った設定は不要です。

次に、2つ目の「Change」ノードです。
こちらは、Watson Assistantからの戻り値（JSON）の中の会話文のみをmsg.payload.optextへ入れ直しています。

2つ目の「Function」ノード「createReplyMessage」では、LINE@へ返却する会話文（返信）を組み立てます。

var post_request = {
 “headers”: {
 “content-type”: “application/json; charset=UTF-8”,
 “Authorization”: “ Bearer “ + “{LINEで生成するアクセストークン}”
 },
 “payload”: {
 //”replyToken”: msg.payload.events[0].replyToken,
 “replyToken”: flow.get(“replyToken”),
 “messages”: [
 {
 “type”: “text”,
 “text”: msg.payload.optext + “ฅ^•ﻌ•^ฅ”
 }
 ]
 }
};

return post_request;

会話の返事文を返すPOSTリクエストのパラメーターを設定しています。
・LINEで生成するアクセストークン（後述）
・先ほどflowへ格納したReplyトークン
・先程optextへ格納した実際に会話として返信する文章
を設定します。
ここでは返事文の最後に猫（犬？）のAAを加えてみてます。

最後に「http request」ノードです。
こちらは、LINE@のリプライ用URLを指定します。

https://api.line.me/v2/bot/message/reply

LINE側の作業

■LINE@アカウントを作成

LINE@のアカウントの作成は簡単です。こちらをご参照ください。
LINE@アカウントの作成（一般アカウント）
アカウント基本情報の入力
内容を確認して完了
LINE@MANAGERへログイン
ログイン

LINE developersへこちらからログインします。
ログインボタンをクリック
ログイン

■LINE developersでプロバイダーを作成

プロバイダー名を設定します。
プロバイダー名を設定→確認する
作成する

プロバイダーが作成されたのを確認したら、Messaging APIの「チャネルを作成する」をクリックします。
チャネルを作成する

■新規チャネル作成

チャネルに必要な情報を設定していきます。
設定項目は下記の通り、ご自身の内容に合わせ設定します。

・アプリアイコン画像：好きな画像（3MB以内, JPEG/PNG/GIF/BMP形式）
・アプリ名：最大20文字
・アプリ説明：最大500文字
・プラン：Developer Trial
・大業種・小業種：一番近いものを選択
・メールアドレス：お知らせ等の配信用

まずは、アプリアイコン画像を登録します。そのままでもOKですが、その場合はチャット画面にアイコンは表示されません。
アプリアイコン登録

次にアプリ名、アプリ説明、プランをそれぞれ設定します。プランはDeveloper Trialで行きましょう。
アプリ名、説明を記述し、プランを選択

業種とメールアドレスを登録します。
業種を選択、メールアドレスを記述

利用規約へ同意すれば完了です。
情報利用への同意
LINE@とMessaging APIの利用規約への同意項目にチェックし作成

■Channelの基本設定

作成したチャネルの設定をしていきます。作成したチャネルのパネルをクリックします。
チャネルの設定へ進む

ここで必要なのは以下の2つになります。
・アクセストークン（ロングターム）
・Webhook URL

アクセストークンを生成します。
生成したトークンは、前述のNode-REDの「Function」ノード内に記述してください。
再発行ボタンをクリック
再発行ボタンをクリック

次に、以下を設定してください。
・Webhook送信を「利用する」へ
・Webhook URLに先程Node-REDの章で説明したWebhook URLを記述
・自動応答メッセージを「利用しない」へ

これで、すべて完了しました。
動作確認

Channel設定画面の下方にQRコードが生成されているので、これを自分のスマホのLINEアプリから、友達追加→QRコードで追加してください。
友達追加用のQRコード
ネコがチャットボットで会話してくれる

無事、みなさんのお持ちのスマホ上へ、このチャットボットが登場し使えるようになったのではないでしょうか。

ぜひ、これをベースにみなさまアレンジを加えて試してみてくださいね。

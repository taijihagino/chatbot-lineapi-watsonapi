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

6月11日、think Japanにて発表がありましたが、Code Patternsの日本語サイトを公開しました。オリジナルサイト（英語）のローカライズ版になります。
https://developer.ibm.com/jp/
概要
アプリケーション構成図
IBM Cloud側の作業

■Watson Assistantの作成

IBM Cloudのカタログから、Watson Assistantを選択します。左側のカテゴリにてWatsonを選択するとすぐに見つけられます。
Watson Assistantを選択

サービス名、地域・組織・スペース、料金プランを設定して作成ボタンをクリックします。
地域：米国南部（推奨、他のリージョンでは出来ないことも米国南部だとできるケースも多いです）
組織・スペース：自分の好きな組織・スペースを設定。
料金プラン：無料のLiteで行きましょう！
必要項目を設定したら作成ボタンをクリック

Assistant APIが作成されたら資格情報を取得します。
サービス資格情報メニューから資格情報を作成し、APIエンドポイントURL、ユーザー、パスワードを確認し控えておいてください。
※この手順はNode-REDがIBM Cloud上で実行されている場合でNode-REDとAssistant APIがバインドされている場合は不要です。

■Watson Assistantワークスペースの作成

Watson Assistantはチャットボットなどで利用する会話文の定義やフローを学習させることができます。そのための作業スペースをワークスペースといいます。このワークスペースには一意のIDが割り当てられ、アプリからこのAPIを呼び出す際に利用されます。

先程作成したAssitant APIのサービスを選択します。
作成済みのWatson Assistantへ

ツールを起動します。
ツールの起動ボタンをクリック

ここで、Createボタンをクリックすると会話フローの新規作成となります。
今回は予め用意した非常に簡単な会話フロー（目的と意図も設定）の定義ファイル（JSON）をインポートしてみましょう。
Createボタンの右隣にある上向き矢印のアイコンをクリックします。
インポートアイコンをクリック

こちらからダウンロードしたJSONを読み込みます。
Choose a fileでJSONを選択

これで完了です。
カスタマイズしたい方は、これをもとに改変してもらうか、新規に作成をしてみてください。

■Node-REDの作成

Node-REDをIBM Cloudへ作成します。カタログからボイラープレート→Node-RED Starterを選択し、アプリを作成してください。
作成に失敗する場合は、IoT Platform StarterでもOKです。
Node-RED Starterを選択し作成

Node-RED作成後、先に作成したWatson Assistant APIをバインドします。
作成したNode-REDアプリの画面から、
作成したNode-REDアプリを選択
接続メニューから接続の作成
Watson Assistantを選択し「Connect」をクリック

その後、再ステージングが行われるのでしばらくお待ち下さい。

再ステージングが完了したら、フローエディタへアクセスしてください。

Node-RED自体は非常にシンプルなフローになります。まずは完成形のNode-REDフロー定義を見てみましょう。
完成形フロー

Node-REDでは、LINEからWebhookで呼び出されるサーバーサイド処理を作成します。Watson Assistant APIを呼び出すのもこのNode-REDになります。完成版のフローはこちらからダウンロード頂けます。https://github.com/taijihagino/chatbot-lineapi-watsonapi

一番最初のノードである「http in」ノードでは、このサーバーサイドアプリケーション（実態はNode.jsアプリ）を呼び出すためのURLのパスを設定します。GitHubからダウンロードした定義を復元するとここのパスには"/line_hook”が設定されています。こちらは好きな文字列へ変更して頂いても結構です。

Node-RED自体がそもそもNode.jsのウェブアプリケーションですので、IBM Cloud（PaaS）上で作成したNode-REDの環境では、既にURLが割り当てられています。このURLに「http in」で指定したパスを繋ぎ以下のようなURLになります。

https://<IBM Cloudで作った際のNode-REDのアプリ名>.mybluemix.net/<http inで指定したパス>/

例）https://fillgapapp01-nodered01.mybluemix.net/line_hook/

次に「Function」ノードの中を確認しましょう。
1つ目の「Function」ノード「getText」は、LINE＠のAPIがNode-REDのWebhook URLを呼び出した時に渡してくる返信用トークンの値を保持しておくための処理です。

//flowへ格納
flow.set(“replyToken”,msg.payload.events[0].replyToken);

return msg;

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

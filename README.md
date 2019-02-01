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

Click Connect button (appear with mouse over). Please select default authentication.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/011.png)

Please wait a moment for re-staging process. 
After complete the process you can access your Node-RED Flow Editor.
It is very simple to meke Node-RED application foe this application. The completed flow view is below;
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/012.png)

Create server-side application which is called from LINE webhook, on Node-RED. It will call Watson Assistant API. You can download [completed flow definition](https://github.com/taijihagino/chatbot-lineapi-watsonapi)

The first node "http in" is for configration URL to access this Node-RED application. Already set the URL "/line_hook" on the flow you downloaded.

Node-RED is just Node.js application, so the application you will create on it is also Node.js application. IBM Cloud alocated unique URL on your application. This application now you created on Node-RED has the URL below;

```
https://<Your Node-RED App Name>.mybluemix.net/<Path you set on "http in node">/

例）https://fillgapapp01-nodered01.mybluemix.net/line_hook/
```

The first Function node "getText" is for keeping LINE API reply token.
```
//flowへ格納
flow.set(“replyToken”,msg.payload.events[0].replyToken);

return msg;
```

You can access the value saved the flow anytime anywhere.

First Change node, it is for replacing the talking sentence from LINE to accept by Watson Assistant API.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/013.png)

Call Watson Assistant API. Place your Assistant Workspace ID. You already bind Assistant to Node-RED, so no need to config credentials.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/014.png)

Second Change node, it is for replace parameter from the response of Assistant to "msg.payload.optext".
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/015.png)

Second Function node "createReplyMessage", make talking sentence for replying to LINE App. 
```
var post_request = {
    “headers”: {
        “content-type”: “application/json; charset=UTF-8”,
        “Authorization”: “ Bearer “ + “{LINEで生成するアクセストークン}”
    },
    “payload”: {
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
```

Already set parameters for replying talking sentence as POST request, on your Node-RED flow you downloaded.
- Access Token generated by LINE (describing is below)
- Reply Token on flow
- Sentence text on text (with ascii art cat character )

Finally, set reply URL for LINE on http request node.
```
https://api.line.me/v2/bot/message/reply
```
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/016.png)


## LINE Side
### Create LINE Developer account
You can login LINE developers with your LINE account which can be created on your smartphone LINE app.

Login LINE developers [here](https://developers.line.me/).
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/022.png)


### Create Provider
Set your provider name.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/024.png)

Comfirm and create
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/025.png)

And then, click "Create Channel" button on Messaging API tile.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/026.png)

### Create New Channel
Config any informations which is needed for the channel. The each values are below; 
```
- App Icon Image: less than 3MB, JPEG/PNG/GIF/BMP
- App Name: 20 charactors or less
- App Description: 500 charactors or less
- Plan: Developer Trial
- Industries: Any
- EMail Address: Any
```

Agree the terms of service and then press the Agreement button.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/030.png)

Check the two check box for these agreements, and press create button.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/031.png)

### Basic Configration for Channel
Click the tile of the channel you created.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/032.png)

You should work just two.
- Generate Channel access token (long-lived)
- Set Webhook URL

Generate Channel access token
After the operation, you need to place this token on your Node-RED Function node.
Press the issue button twice.
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/033.png)

![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/034.png)

Config these settings below;
- Use Webhooks -> Enabled
- Webhook URL -> Your Node-RED app URL
- Auto-reply messages -> Disabled

Congrats! You finaly did it!


### Check LINE Chat Application
Add this chatbot to your LINE friends with QR Code. You can find QR Code bottom of LINE developers basic information page.
Mobile LINE App: Add Friend -> QR Code
![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/035.png)

![Application Image](https://github.com/taijihagino/chatbot-lineapi-watsonapi/blob/master/images/036.png)

I believe you got your chatbot on your smartphone LINE app.
Good luck modify this application more!

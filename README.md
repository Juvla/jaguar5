# jaguar5
linebot


$ pip install line-bot-sdk
from flask import Flask, request, abort

from linebot import (
    LineBotApi, WebhookHandler
)
from linebot.exceptions import (
    InvalidSignatureError
)
from linebot.models import (
    MessageEvent, TextMessage, TextSendMessage,
)

app = Flask(__name__)

line_bot_api = LineBotApi('YOUR_CHANNEL_ACCESS_TOKEN')
handler = WebhookHandler('YOUR_CHANNEL_SECRET')


@app.route("/callback", methods=['POST'])
def callback():
    # get X-Line-Signature header value
    signature = request.headers['X-Line-Signature']

    # get request body as text
    body = request.get_data(as_text=True)
    app.logger.info("Request body: " + body)

    # handle webhook body
    try:
        handler.handle(body, signature)
    except InvalidSignatureError:
        print("Invalid signature. Please check your channel access token/channel secret.")
        abort(400)

    return 'OK'


@handler.add(MessageEvent, message=TextMessage)
def handle_message(event):
    line_bot_api.reply_message(
        event.reply_token,
        TextSendMessage(text=event.message.text))


if __name__ == "__main__":
    app.run()

__init__(self, channel_access_token, endpoint='https://api.line.me', timeout=5, http_client=RequestsHttpClient)
Create a new LineBotApi instance.

line_bot_api = linebot.LineBotApi('YOUR_CHANNEL_ACCESS_TOKEN')

reply_message(self, reply_token, messages, notification_disabled=False, timeout=None)
Respond to events from users, groups, and rooms. You can get a reply_token from a webhook event object.

https://developers.line.biz/en/reference/messaging-api/#send-reply-message

line_bot_api.reply_message(reply_token, TextSendMessage(text='Hello World!'))


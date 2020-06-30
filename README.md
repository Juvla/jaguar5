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

multicast(self, to, messages, notification_disabled=False, timeout=None)
Send push messages to multiple users at any time. Messages cannot be sent to groups or rooms.

https://developers.line.biz/en/reference/messaging-api/#send-multicast-message

line_bot_api.multicast(['to1', 'to2'], TextSendMessage(text='Hello World!'))
broadcast(self, messages, notification_disabled=False, timeout=None)
Send push messages to multiple users at any time.

https://developers.line.biz/en/reference/messaging-api/#send-broadcast-message

line_bot_api.broadcast(TextSendMessage(text='Hello World!'))
narrowcast(self, messages, recipient=None, filter=None, limit=None, timeout=None)
Sends a push message to multiple users specified by attributes (such as age, gender, OS, and region) or retargeting (audiences).

https://developers.line.biz/en/reference/messaging-api/#send-narrowcast-message

line_bot_api.narrowcast(
    messages=TextSendMessage(text='Hello World!'),
    recipient=AudienceRecipient(group_id=5614991017776),
    filter=Filter(demographic=AgeFilter(gte="age_35", lt="age_40")),
    limit=Limit(max=10)
)
get_progress_status_narrowcast(self, request_id, timeout=None)
Get progress status of narrowcast messages sent.

https://developers.line.biz/en/reference/messaging-api/#get-narrowcast-progress-status

narrowcast_progress = line_bot_api.get_progress_status_narrowcast(request_id)
assert narrowcast_progress.phase == 'succeeded'
print(narrowcast.success_count)
print(narrowcast.failure_count)
print(narrowcast.target_count)
get_profile(self, user_id, timeout=None)
Get user profile information.

https://developers.line.biz/en/reference/messaging-api/#get-profile

profile = line_bot_api.get_profile(user_id)

print(profile.display_name)
print(profile.user_id)
print(profile.picture_url)
print(profile.status_message)
get_group_summary(self, group_id, timeout=None)
Gets the group ID, group name, and group icon URL of a group where the LINE Official Account is a member.

https://developers.line.biz/en/reference/messaging-api/#get-group-summary

summary = line_bot_api.get_group_summary(group_id)
print(summary.group_id)
print(summary.group_name)
print(summary.picture_url)
get_group_member_profile(self, group_id, user_id, timeout=None)
Gets the user profile of a member of a group that the bot is in. This can be the user ID of a user who has not added the bot as a friend or has blocked the bot.

https://developers.line.biz/en/reference/messaging-api/#get-group-member-profile

profile = line_bot_api.get_group_member_profile(group_id, user_id)

print(profile.display_name)
print(profile.user_id)
print(profile.picture_url)
get_room_member_profile(self, room_id, user_id, timeout=None)
Gets the user profile of a member of a room that the bot is in. This can be the user ID of a user who has not added the bot as a friend or has blocked the bot.

https://developers.line.biz/en/reference/messaging-api/#get-room-member-profile

profile = line_bot_api.get_room_member_profile(room_id, user_id)

print(profile.display_name)
print(profile.user_id)
print(profile.picture_url)
get_group_member_ids(self, group_id, start=None, timeout=None)
Gets the user IDs of the members of a group that the bot is in. This includes the user IDs of users who have not added the bot as a friend or has blocked the bot.

https://developers.line.biz/en/reference/messaging-api/#get-group-member-user-ids

member_ids_res = line_bot_api.get_group_member_ids(group_id)

print(member_ids_res.member_ids)
print(member_ids_res.next)
get_room_member_ids(self, room_id, start=None, timeout=None)
Gets the user IDs of the members of a room that the bot is in. This includes the user IDs of users who have not added the bot as a friend or has blocked the bot.

https://developers.line.biz/en/reference/messaging-api/#get-room-member-user-ids

member_ids_res = line_bot_api.get_room_member_ids(room_id)

print(member_ids_res.member_ids)
print(member_ids_res.next)
get_group_members_count(self, group_id, timeout=None)
Gets the count of members in a group.

https://developers.line.biz/en/reference/messaging-api/#get-members-group-count

group_count = line_bot_api.get_group_members_count(group_id)
print(group_count)
get_room_members_count(self, room_id, timeout=None)
Gets the count of members in a room.

https://developers.line.biz/en/reference/messaging-api/#get-members-room-count

room_count = line_bot_api.get_room_members_count(room_id)
print(room_count)
get_message_content(self, message_id, timeout=None)
Retrieve image, video, and audio data sent by users.

https://developers.line.biz/en/reference/messaging-api/#get-content

message_content = line_bot_api.get_message_content(message_id)

with open(file_path, 'wb') as fd:
    for chunk in message_content.iter_content():
        fd.write(chunk)
leave_group(self, group_id, timeout=None)
Leave a group.

https://developers.line.biz/en/reference/messaging-api/#leave-group

line_bot_api.leave_group(group_id)
leave_room(self, room_id, timeout=None)
Leave a room.

https://developers.line.biz/en/reference/messaging-api/#leave-room

line_bot_api.leave_room(room_id)
get_rich_menu(self, rich_menu_id, timeout=None)
Gets a rich menu via a rich menu ID.

https://developers.line.biz/en/reference/messaging-api/#get-rich-menu

rich_menu = line_bot_api.get_rich_menu(rich_menu_id)
print(rich_menu.rich_menu_id)
create_rich_menu(self, rich_menu, timeout=None)
Creates a rich menu. You must upload a rich menu image and link the rich menu to a user for the rich menu to be displayed. You can create up to 1000 rich menus for one LINE Official Account with the Messaging API.

https://developers.line.biz/en/reference/messaging-api/#create-rich-menu

rich_menu_to_create = RichMenu(
    size=RichMenuSize(width=2500, height=843),
    selected=False,
    name="Nice richmenu",
    chat_bar_text="Tap here",
    areas=[RichMenuArea(
        bounds=RichMenuBounds(x=0, y=0, width=2500, height=843),
        action=URIAction(label='Go to line.me', uri='https://line.me'))]
)
rich_menu_id = line_bot_api.create_rich_menu(rich_menu=rich_menu_to_create)
print(rich_menu_id)
delete_rich_menu(self, rich_menu_id, timeout=None)
Deletes a rich menu.

https://developers.line.biz/en/reference/messaging-api/#delete-rich-menu

line_bot_api.delete_rich_menu(rich_menu_id)
get_rich_menu_id_of_user(self, user_id, timeout=None)
Gets the ID of the rich menu linked to a user.

https://developers.line.biz/en/reference/messaging-api/#get-rich-menu-id-of-user

rich_menu_id = line_bot_api.get_rich_menu_id_of_user(user_id)
print(rich_menu_id)
link_rich_menu_to_user(self, user_id, rich_menu_id, timeout=None)
Links a rich menu to a user. Only one rich menu can be linked to a user at one time.

https://developers.line.biz/en/reference/messaging-api/#link-rich-menu-to-user

line_bot_api.link_rich_menu_to_user(user_id, rich_menu_id)
link_rich_menu_to_users(self, user_ids, rich_menu_id, timeout=None)
Links a rich menu to multiple users.

https://developers.line.biz/en/reference/messaging-api/#link-rich-menu-to-users

line_bot_api.link_rich_menu_to_users(<user_ids>, <rich_menu_id>)
unlink_rich_menu_from_user(self, user_id, timeout=None)
Unlinks a rich menu from a user.

https://developers.line.biz/en/reference/messaging-api/#unlink-rich-menu-from-user

line_bot_api.unlink_rich_menu_from_user(user_id)
unlink_rich_menu_from_users(self, user_ids, timeout=None)
Unlinks rich menus from multiple users.

https://developers.line.biz/en/reference/messaging-api/#unlink-rich-menu-from-users

line_bot_api.unlink_rich_menu_from_users(<user_ids>)
get_rich_menu_image(self, rich_menu_id, timeout=None)
Downloads an image associated with a rich menu.

https://developers.line.biz/en/reference/messaging-api/#download-rich-menu-image

content = line_bot_api.get_rich_menu_image(rich_menu_id)
with open(file_path, 'wb') as fd:
    for chunk in content.iter_content():
        fd.write(chunk)
set_rich_menu_image(self, rich_menu_id, content_type, content, timeout=None)
Uploads and attaches an image to a rich menu.

https://developers.line.biz/en/reference/messaging-api/#upload-rich-menu-image

with open(file_path, 'rb') as f:
    line_bot_api.set_rich_menu_image(rich_menu_id, content_type, f)
get_rich_menu_list(self, timeout=None)
Gets a list of all uploaded rich menus.

https://developers.line.biz/en/reference/messaging-api/#get-rich-menu-list

rich_menu_list = line_bot_api.get_rich_menu_list()
for rich_menu in rich_menu_list:
    print(rich_menu.rich_menu_id)
set_default_rich_menu(self, rich_menu_id, timeout=None)
Sets the default rich menu.

https://developers.line.biz/en/reference/messaging-api/#set-default-rich-menu

line_bot_api.set_default_rich_menu(<rich_menu_id>)
get_default_rich_menu(self, timeout=None)
Gets the ID of the default rich menu set with the Messaging API.

https://developers.line.biz/en/reference/messaging-api/#get-default-rich-menu-id

line_bot_api.get_default_rich_menu()
cancel_default_rich_menu(self, timeout=None)
Cancels the default rich menu set with the Messaging API.

https://developers.line.biz/en/reference/messaging-api/#cancel-default-rich-menu

line_bot_api.cancel_default_rich_menu()
issue_link_token(self, user_id, timeout=None)
Issues a link token used for the account link feature.

https://developers.line.biz/en/reference/messaging-api/#issue-link-token

link_token_response = line_bot_api.issue_link_token(<user_id>)
print(link_token_response)
issue_channel_token(self, client_id, client_secret, grant_type='client_credentials', timeout=None)
Issues a short-lived channel access token.

https://developers.line.biz/en/reference/messaging-api/#issue-channel-access-token

channel_token_response = line_bot_api.issue_channel_token(<client_id>, <client_secret>)
print(access_token_response)
revoke_channel_token(self, access_token, timeout=None)
Revokes a channel access token.

https://developers.line.biz/en/reference/messaging-api/#revoke-channel-access-token

line_bot_api.revoke_channel_token(<access_token>)
get_insight_message_delivery(self, date, timeout=None)
Get the number of messages sent on a specified day.

https://developers.line.biz/en/reference/messaging-api/#get-number-of-delivery-messages

insight = line_bot_api.get_insight_message_delivery('20191231')
print(insight.api_broadcast)
get_insight_followers(self, date, timeout=None)
Get the number of users who have added the bot on or before a specified date.

https://developers.line.biz/en/reference/messaging-api/#get-number-of-followers

insight = line_bot_api.get_insight_followers('20191231')
print(insight.followers)
get_insight_demographic(self, timeout=None)
Retrieve the demographic attributes for a bot's friends.

https://developers.line.biz/en/reference/messaging-api/#get-demographic

insight = line_bot_api.get_insight_demographic()
print(insight.genders)
get_insight_message_event(self, request_id, timeout=None)
Return statistics about how users interact with broadcast messages.

https://developers.line.biz/en/reference/messaging-api/#get-message-event

broadcast_response = line_bot_api.broadcast(TextSendMessage(text='Hello World!'))
insight = line_bot_api.get_insight_message_event(broadcast_response.request_id)
print(insight.overview)
※ Error handling
If the LINE API server returns an error, LineBotApi raises LineBotApiError.

https://developers.line.biz/en/reference/messaging-api/#error-responses

try:
    line_bot_api.push_message('to', TextSendMessage(text='Hello World!'))
except linebot.exceptions.LineBotApiError as e:
    print(e.status_code)
    print(e.request_id)
    print(e.error.message)
    print(e.error.details)
Message objects
https://developers.line.biz/en/reference/messaging-api/#message-objects

The following classes are found in the linebot.models package.

TextSendMessage
text_message = TextSendMessage(text='Hello, world')
ImageSendMessage
image_message = ImageSendMessage(
    original_content_url='https://example.com/original.jpg',
    preview_image_url='https://example.com/preview.jpg'
)
VideoSendMessage
video_message = VideoSendMessage(
    original_content_url='https://example.com/original.mp4',
    preview_image_url='https://example.com/preview.jpg'
)
AudioSendMessage
audio_message = AudioSendMessage(
    original_content_url='https://example.com/original.m4a',
    duration=240000
)
LocationSendMessage
location_message = LocationSendMessage(
    title='my location',
    address='Tokyo',
    latitude=35.65910807942215,
    longitude=139.70372892916203
)
StickerSendMessage
sticker_message = StickerSendMessage(
    package_id='1',
    sticker_id='1'
)
ImagemapSendMessage
imagemap_message = ImagemapSendMessage(
    base_url='https://example.com/base',
    alt_text='this is an imagemap',
    base_size=BaseSize(height=1040, width=1040),
    video=Video(
        original_content_url='https://example.com/video.mp4',
        preview_image_url='https://example.com/video_preview.jpg',
        area=ImagemapArea(
            x=0, y=0, width=1040, height=585
        ),
        external_link=ExternalLink(
            link_uri='https://example.com/see_more.html',
            label='See More',
        ),
    ),
    actions=[
        URIImagemapAction(
            link_uri='https://example.com/',
            area=ImagemapArea(
                x=0, y=0, width=520, height=1040
            )
        ),
        MessageImagemapAction(
            text='hello',
            area=ImagemapArea(
                x=520, y=0, width=520, height=1040
            )
        )
    ]
)
TemplateSendMessage - ButtonsTemplate
buttons_template_message = TemplateSendMessage(
    alt_text='Buttons template',
    template=ButtonsTemplate(
        thumbnail_image_url='https://example.com/image.jpg',
        title='Menu',
        text='Please select',
        actions=[
            PostbackAction(
                label='postback',
                display_text='postback text',
                data='action=buy&itemid=1'
            ),
            MessageAction(
                label='message',
                text='message text'
            ),
            URIAction(
                label='uri',
                uri='http://example.com/'
            )
        ]
    )
)
TemplateSendMessage - ConfirmTemplate
confirm_template_message = TemplateSendMessage(
    alt_text='Confirm template',
    template=ConfirmTemplate(
        text='Are you sure?',
        actions=[
            PostbackAction(
                label='postback',
                display_text='postback text',
                data='action=buy&itemid=1'
            ),
            MessageAction(
                label='message',
                text='message text'
            )
        ]
    )
)
TemplateSendMessage - CarouselTemplate
carousel_template_message = TemplateSendMessage(
    alt_text='Carousel template',
    template=CarouselTemplate(
        columns=[
            CarouselColumn(
                thumbnail_image_url='https://example.com/item1.jpg',
                title='this is menu1',
                text='description1',
                actions=[
                    PostbackAction(
                        label='postback1',
                        display_text='postback text1',
                        data='action=buy&itemid=1'
                    ),
                    MessageAction(
                        label='message1',
                        text='message text1'
                    ),
                    URIAction(
                        label='uri1',
                        uri='http://example.com/1'
                    )
                ]
            ),
            CarouselColumn(
                thumbnail_image_url='https://example.com/item2.jpg',
                title='this is menu2',
                text='description2',
                actions=[
                    PostbackAction(
                        label='postback2',
                        display_text='postback text2',
                        data='action=buy&itemid=2'
                    ),
                    MessageAction(
                        label='message2',
                        text='message text2'
                    ),
                    URIAction(
                        label='uri2',
                        uri='http://example.com/2'
                    )
                ]
            )
        ]
    )
)
TemplateSendMessage - ImageCarouselTemplate
image_carousel_template_message = TemplateSendMessage(
    alt_text='ImageCarousel template',
    template=ImageCarouselTemplate(
        columns=[
            ImageCarouselColumn(
                image_url='https://example.com/item1.jpg',
                action=PostbackAction(
                    label='postback1',
                    display_text='postback text1',
                    data='action=buy&itemid=1'
                )
            ),
            ImageCarouselColumn(
                image_url='https://example.com/item2.jpg',
                action=PostbackAction(
                    label='postback2',
                    display_text='postback text2',
                    data='action=buy&itemid=2'
                )
            )
        ]
    )
)
FlexSendMessage
flex_message = FlexSendMessage(
    alt_text='hello',
    contents=BubbleContainer(
        direction='ltr',
        hero=ImageComponent(
            url='https://example.com/cafe.jpg',
            size='full',
            aspect_ratio='20:13',
            aspect_mode='cover',
            action=URIAction(uri='http://example.com', label='label')
        )
    )
)
※ You can pass a dict to FlexSendMessage#contents as follows:

flex_message = FlexSendMessage(
    alt_text='hello',
    contents={
        'type': 'bubble',
        'direction': 'ltr',
        'hero': {
            'type': 'image',
            'url': 'https://example.com/cafe.jpg',
            'size': 'full',
            'aspectRatio': '20:13',
            'aspectMode': 'cover',
            'action': { 'type': 'uri', 'uri': 'http://example.com', 'label': 'label' }
        }
    }
)
Thus, You can send a JSON designed with Flex Message Simulator.

With QuickReply
text_message = TextSendMessage(text='Hello, world',
                               quick_reply=QuickReply(items=[
                                   QuickReplyButton(action=MessageAction(label="label", text="text"))
                               ]))
Webhook
WebhookParser
※ You can use WebhookParser

__init__(self, channel_secret)
parser = linebot.WebhookParser('YOUR_CHANNEL_SECRET')
parse(self, body, signature, as_payload=False)
Parses the webhook body, and returns a list of Event objects or a WebhookPayload object (depending on as_payload). If the signature does NOT match, InvalidSignatureError is raised.

events = parser.parse(body, signature)

for event in events:
    do_something(event)
payload = parser.parse(body, signature, as_payload=True)

for event in payload.events:
    do_something(payload.event, payload.destination)
WebhookHandler
※ You can use WebhookHandler

__init__(self, channel_secret)
handler = linebot.WebhookHandler('YOUR_CHANNEL_SECRET')
handle(self, body, signature)
Handles webhooks with handlers added by the decorators add and default. If the signature does NOT match, InvalidSignatureError is raised.

handler.handle(body, signature)
add(self, event, message=None)
Add a handler method by using this decorator.

@handler.add(MessageEvent, message=TextMessage)
def handle_message(event):
    line_bot_api.reply_message(
        event.reply_token,
        TextSendMessage(text=event.message.text))
When the event is an instance of MessageEvent and event.message is an instance of TextMessage, this handler method is called.

@handler.add(MessageEvent)
def handle_message(event, destination):
    # do something
If the arity of the handler method is more than one, a destination property in a webhook request is passed to it as the second argument.

@handler.add(FollowEvent)
def handle_follow():
    # do something
If the arity of the handler method is zero, the handler method is called with no arguments.

default(self)
Set the default handler method by using this decorator.

@handler.default()
def default(event):
    print(event)
If there is no handler for an event, this default handler method is called.

WebhookPayload
https://developers.line.biz/en/reference/messaging-api/#request-body

WebhookPayload
destination
events: list[Event]
Webhook event object
https://developers.line.biz/en/reference/messaging-api/#webhook-event-objects

The following classes are found in the linebot.models package.

[https://github.com/Juvla/jaguar5.git]

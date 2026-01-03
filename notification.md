Notifications Configuration
Notifications is a way to notify users about important events. Check events and notifications page for more details about different types of events. Traccar supports various channels for delivering notifications, including email, push notifications and many others. This page focuses on the channel configuration.

By default, Traccar has web and email channels enabled. Note that for the email to work, SMTP server needs to be configured per user or globally in the config file. See the corresponding section below for more details on how to configure each channel.

Email Configuration
Push Configuration
SMS Configuration
Telegram Configuration
Web Configuration
To change the URL in notifications, set or change the web.url configuration parameter.

You can test notification channels from the settings when creating or editing a notification:


Email Configuration
There are two ways to configure email notifications:

Server-wide parameters in the server config file
Per-user configuration via user attributes
All available parameters are listed in the configuration page (see mail.smtp.* parameters).

Tips:

Some SMTP servers require the From field even if authorization is used. Make sure to set the mail.smtp.from parameter.
If you want to use your Gmail email for notifications please check Gmail in Traccar configuration guide.
SSL configuration example:

<entry key='mail.smtp.host'>smtp.gmail.com</entry>
<entry key='mail.smtp.port'>465</entry>
<entry key='mail.smtp.ssl.enable'>true</entry>
<entry key='mail.smtp.from'>traccar@gmail.com</entry>
<entry key='mail.smtp.auth'>true</entry>
<entry key='mail.smtp.username'>traccar@gmail.com</entry>
<entry key='mail.smtp.password'>password</entry>
STARTTLS configuration example:

<entry key='mail.smtp.host'>smtp.gmail.com</entry>
<entry key='mail.smtp.port'>587</entry>
<entry key='mail.smtp.starttls.enable'>true</entry>
<entry key='mail.smtp.from'>traccar@gmail.com</entry>
<entry key='mail.smtp.auth'>true</entry>
<entry key='mail.smtp.username'>traccar@gmail.com</entry>
<entry key='mail.smtp.password'>password</entry>
Push Configuration
Push ntifications are sent through the Firebase platform. There are two options for push notifications in Traccar:

Traccar notifications to the official Traccar Manager apps
Direct Firebase notifications to your own mobile app
To receive notifications to the official Traccar Manager app from Google Play all you need to do is configure Traccar with the following parameters:

<entry key='notificator.types'>traccar,...</entry>
<entry key='notificator.traccar.key'>TRACCAR_KEY</entry>
You can find your personal API key on the Account page. You need to be registered on the website.

To receive Firebase push notifications directly, you need a custom mobile app that is built with your Firebase keys. Server configuration for direct Firebase notifications should look like this:

<entry key='notificator.types'>firebase,...</entry>
<entry key='notificator.firebase.serviceAccount'>
  {
    "type": "service_account",
    ...
  }
</entry>
You can create a service account from your Firebase console and download the JSON from there as well.

Note that if you're using Traccar version 5.1 and older, you have to use the legacy API key with notificator.firebase.key parameter instead of the service account.

SMS Configuration
Text notifications are delivered to the phone number in the user account settings. The user must have correctly configured the Phone field according to your SMS API provider requirements.

Traccar supports a flexible format to work with many SMS API providers. Check the HTTP SMS API documentation for more details and examples.

We also provide a mobile app Traccar SMS Gateway for Android. You can use it to send SMS directly from your own mobile phone without using an external service.

An example configuration to work with Traccar SMS Gateway app:

<entry key='notificator.types'>sms,...</entry>
<entry key='sms.http.url'>https://www.traccar.org/sms/</entry>
<entry key='sms.http.authorization'>TOKEN</entry>
<entry key='sms.http.template'>
    {
        "to": "{phone}",
        "message": "{message}"
    }
</entry>
Telegram Configuration
Telegram is a popular mobile messaging app. Traccar supports Telegram as one option for delivering notifications.

You need to create a bot first. You can do it using BotFather contact in Telegram. It will provide you with a key.

Then you need to create a chat group with your bot and get the chat id. You can do it using the instructions here.

Traccar configuration example for Telegram:

<entry key='notificator.types'>telegram,...</entry>
<entry key='notificator.telegram.key'>BOT_KEY</entry>
<entry key='notificator.telegram.chatId'>CHAT_ID</entry>
Chat id can also be configured per user. See user attributes in settings.

Web Configuration
Web notifications are delivered directly to the web or mobile app when it's open and connected to the server. It does not require any additional configuration and it's enabled by default.

Web notifications are displayed as toasts and are also available in the events panel on the main screen:


Web notifications are delivered instantly without any delay. It is also possible to enable sound for some critical notifications.

Templates
Traccar uses Velocity Engine for notification templates. Emails use the content and most other channels use the digest string from templates.

Templates can be easily adjusted to your needs or translated. Template *.vm files must be in the UTF-8 encoding.

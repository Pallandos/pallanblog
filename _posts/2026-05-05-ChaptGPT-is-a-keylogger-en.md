---
title: ChatGPT is a keylogger
description: Why you should think twice before pasting something into LLMs
date: 2026-05-05 17:30:00
categories: [Privacy]
tags: [ai]
lang: en
---

Do you sometimes paste big chunks of text into ChatGPT and then remove sensitive data like API keys or coding secrets? Well, you should not, because OpenAI is collecting your keystrokes even before you hit enter.

## ChaptGPT sends everything way before you hit enter

First, we open a fresh new tab at `https://chatgpt.com/`, without being logged in and start typing some text in the text field. Without hitting the enter key, as soon as we finish typing, our text input is already sent to OpenAI servers. 

![Network activity of ChatGPT](/assets/img/chatgpt-1.png)
_Network activity of ChatGPT while writing_

### The `prepare` request

Indeed, with just the network inspector from our browser (`F12`), we can see a `prepare` request. This is a `POST` request to the following URL: 

- `https://chatgpt.com/backend-anon/f/conversation/prepare` if we are not logged in
- `https://chatgpt.com/backend-api/f/conversation/prepare` if we are using a ChatGPT account

The full payload of the request is something like this :

```json
{
    "action":"next",
    "fork_from_shared_post":false,
    "parent_message_id":"client-created-root",
    "model":"auto",
    "client_prepare_state":"success",
    "timezone_offset_min":-120,
    "timezone":"Europe/Paris",
    "history_and_training_disabled":true,
    "conversation_mode":{"kind":"primary_assistant"},
    "system_hints":[],
    "partial_query":{
        "id":"XXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "author":{"role":"user"},
        "content":{
            "content_type":"text",
            "parts":["hey there!"]
            }
        },
    "supports_buffering":true,
    "supported_encodings":["v1"],
    "client_contextual_info":{"app_name":"chatgpt.com"}
}
```

And we can read our message in the middle of the payload in `partial_query.content.parts`, proving that ChatGPT is sending data even before we validate the input. This behavior proves that you need to think twice before pasting text to ChatGPT, especially if you want to edit the text afterward.

### The possible reason

This behavior likely isn't just a data grab (really?). As the prepare endpoint suggests, OpenAI is probably pre-processing our input to deliver faster responses.

### Environment  

You can find below what i used for the experiments. 

- Browser : `Brave 1.88.138 (Official Build) (64-bit)` based on `Chromium 146.0.7680.178`
- ChatGPT : Accessed from France between April 30 and May 5, 2026
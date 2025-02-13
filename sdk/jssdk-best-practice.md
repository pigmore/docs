---
title: "Streaming Avatar SDK Best Practice"
description: "Learn how implement Streaming Avatar SDK step by step"
---

## Overview

When implementing a JavaScript SDK, especially one that interacts with sensitive resources or APIs, it is critical to ensure the security of private keys, tokens, and other sensitive credentials. Exposing such sensitive information in a client-side environment (e.g., browsers) can lead to vulnerabilities, including unauthorized access, token theft, and API abuse. This document outlines best practices for securing private keys and tokens in your Streaming Avatar SDK implementation while exposing only the necessary session data to the client.

- Never Expose Private Keys in the Client-Side Code
- Use Short-Lived Session as token
- Delegate Authentication to a Backend Server
- Handling avatar interactions and responses
- Managing audio streams and events

The integration uses Agora's Real-Time Communication (RTC) SDK for reliable, low-latency streaming and our avatar service for generating responsive avatar behaviors.

## Prerequisites

- Get your [Akool API Token](https://www.akool.com) from [Akool Authentication API](/authentication/usage#get-the-token)

- Basic knowledge of backend services and internet security.

- Basic knowledge of JavaScript and Http Request

## Getting Started

1.To get started with the Streaming Avatar SDK, you just need one html page with lots of elements like below:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Streaming Avatar SDK</title>
</head>
<body>
  <div id="app">
    <h1>Streaming Avatar Demo</h1>
    <div id="yourAvatarContainer" class="avatarContainer">
      <div id="videoWrap" class="videoWrap" style="width: fit-content;">
        <video id="yourStreamingVideoDom" autoplay="" playsinline="" loop=""></video>
        <div id="messageWrap"></div>
      </div>
      <div id="controls">
        <div>
          <button id="toggleSession">Start Session</button>
        </div>
        <div style="display: flex; gap: 10px;">
          <input type="text" id="userInput" disabled="" placeholder="Type anything to communicate with the avatar...">
          <button id="sendButton" disabled="">Send</button>
          <button id="voiceButton" disabled="">Turn Voice on</button>
        </div>
      </div>
    </div>
  </div>
</body>
</html>
```
2.importing Streaming Avatar SDK:

```html
  <script src="https://cdn.jsdelivr.net/gh/pigmore/docs/streamingAvatar-min.js"></script>
```

3.Instantiation StreamingAvatar class and get session params form your backend:

```js
  var stream = new StreamingAvatar();
  // info: start your stream session with Credentials.
  // Best Practice: get from Akool Session_ID and Credentials from your backend service.
  const paramsWithCredentials = await YOUR_BACK_END_API_FOR_START_SESSION()
```
<Tip> YOUR_BACK_END_API_FOR_START_SESSION may like below:</Tip>

```js
  async function fetchAccessToken() {
    const id = YOUR_CLIENT_ID;
    const apiKey = AKOOL_SECRET_KEY;
    const response = await fetch(
      "https://openapi.akool.com/api/open/v3/getToken",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          "clientId": id
          "clientSecret": apiKey
        }),
        redirect: "follow",
      }
    );

    const { data } = await response.json();
    return data.token;
  }

  async function createSession(token,avatar_id,duration) {
    const id = YOUR_CLIENT_ID;
    const apiKey = AKOOL_SECRET_KEY;
    const response = await fetch(
      "https://openapi.akool.com/api/open/v3/getToken",
      {
        method: "POST",
        headers: {
          "Authorization": `Bearer ${token}`,
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          avatar_id;
          duration;
        }),
        redirect: "follow",
      }
    );

    const { data } = await response.json();
    return data;
  }

  const token = await fetchAccessToken()
        avatar_id = "dvp_Tristan_cloth2_1080P"
        duration = 300;
  const paramsWithCredentials = await createSession(token,avatar_id,duration)
```
<Note> Make sure the YOUR_BACK_END_API_FOR_START_SESSION return the paramsWithCredentials from your backend </Note>

4. Sign up functions for steaming events and button click events:

```js
  function handleStreamReady(event:any) {
    console.log('Stream is ready:',event.detail)
  }
  function handleMessageReceive(event:any) {
    console.log('Message:', event.detail)
  }
  function handleWillExpire(event:any) {
    console.log('Warning:',event.detail.msg)
  }
  function handleExpired(event:any) {
    console.log('Warning:',event.detail.msg)
  }
  function handleERROR(event:any) {
    console.error('ERROR has occurred:',event.detail.msg)
  }
  function handleStreamClose(event:any) {
    console.log('Stream is close:',event.detail)
    // when you leave the page you'd better off the eventhandler
    stream.off(StreamEvents.READY,handleStreamReady)
    stream.off(StreamEvents.ONMESSAGE,handleMessageReceive)
    stream.off(StreamEvents.WILLEXPIRE,handleWillExpire)
    stream.off(StreamEvents.EXPIRED,handleExpired)
    stream.off(StreamEvents.ERROR,handleERROR)
    stream.off(StreamEvents.CLOSED,handleStreamClose)
  }

  stream.on(StreamEvents.READY,handleStreamReady)
  stream.on(StreamEvents.ONMESSAGE,handleMessageReceive)
  stream.on(StreamEvents.WILLEXPIRE,handleWillExpire)
  stream.on(StreamEvents.EXPIRED,handleExpired)
  stream.on(StreamEvents.ERROR,handleERROR)
  stream.on(StreamEvents.CLOSED,handleStreamClose)

  async function handleToggleSession() {
    if (window.toggleSession.innerHTML == "&nbsp;&nbsp;&nbsp;...&nbsp;&nbsp;&nbsp;") return
    if (window.toggleSession.innerHTML == "Start Session") {
      window.toggleSession.innerHTML = "&nbsp;&nbsp;&nbsp;...&nbsp;&nbsp;&nbsp;"
      await stream.startSessionWithCredentials('yourStreamingVideoDom',paramsWithCredentials)
      window.toggleSession.innerHTML = "End Session"
      window.userInput.disabled = false;
      window.sendButton.disabled = false;
      window.voiceButton.disabled = false;
    }else{
      // info: close your stream session
      stream.closeStreaming()
      window.messageWrap.innerHTML = ''
      window.toggleSession.innerHTML = "Start Session"
      window.userInput.disabled = true;
      window.sendButton.disabled = true;
      window.voiceButton.disabled = true;
    }
  }

  async function handleSendMessage() {
    await stream.sendMessage(window.userInput.value ?? '')
  }

  async function handleToggleMic() {
    await stream.toggleMic()
    if (stream.micStatus) {
      window.voiceButton.innerHTML = "Turn mic off"
    }else{
      window.voiceButton.innerHTML = "Turn mic on"
    }
  }

  window.toggleSession.addEventListener("click", handleToggleSession);
  window.sendButton.addEventListener("click", handleSendMessage);
  window.voiceButton.addEventListener("click", handleToggleMic);
```

## Additional Resources
- [Streaming Avatar SDK API Interface](/implementation-guide/jssdk-api)
- [AKool OpenAPI Error Codes](/ai-tools-suite/live-avatar#response-code-description)

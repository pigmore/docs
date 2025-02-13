<think>
Okay, I need to figure out how to create an API documentation for the StreamingAvatar class based on the given context. Let's start by looking at each of the provided documents to extract relevant information.

From doc id='0', I see that the StreamingAvatar class has several properties like openapiHost, openapiToken, voiceId, etc. The constructor takes a token parameter and initializes other properties with default values. So, I should include these properties in the documentation, noting their types and optional parameters where applicable.

Doc id='2' shows methods emit, off, and on which deal with custom events. These seem important for handling events emitted by the class, so I'll document each method, explaining their parameters and usage examples.

The initDom method in doc id='2' initializes session parameters. It takes an optional avatarId and other parameters, using defaults if not provided. This is crucial for setting up the configuration, so I'll detail the parameters and how they affect class behavior.

Docs id='1' and '3' are more about DOM manipulation but don't directly relate to the API methods of StreamingAvatar. However, understanding the setup might help in explaining how the class integrates into a web page.

Putting it all together, the documentation should cover the class structure, its properties, key methods like emit, off, on, and initDom, along with code examples demonstrating their usage. I'll organize this information clearly, making sure each section is concise and informative.
</think>

# StreamingAvatar Class API Documentation

## Overview
The `StreamingAvatar` class provides a framework for integrating a streaming avatar into a web application. It handles low-level communication and DOM manipulation, allowing developers to create interactive avatar experiences.

## Class Structure

### Properties
- **openapiHost**: The host URL for OpenAPI operations.
- **openapiToken**: Authentication token for OpenAPI requests.
- **voiceId**: Identifier for the avatar's voice.
- **videoContainer**: ID of the DOM element containing the video.
- **sessionId**: Unique identifier for the current session.
- **language**: Language used by the avatar (default: 'en').
- **modeType**: Operation mode (1: Repeat, 2: Dialogue).
- **isJoined**: Indicates if the avatar has joined the session.
- **connected**: Status of the WebSocket connection.
- **micEnable**: Whether microphone input is enabled.
- **sendingMessage**: Flag for ongoing message transmission.

### Constructor
```typescript
constructor(params?: { token: string });
```
Initializes the class with optional authentication token.

## Methods

### `emit`
```typescript
emit(_event: StreamEvents, _params: any = {});
```
Emits a custom event to the window object. Used for communication between components.

**Parameters:**
- `_event`: The event name (e.g., 'messageSent').
- `_params`: Event-specific data.

### `off`
```typescript
off(_event: StreamEvents, _handler: void);
```
Removes an event listener from the window object.

**Parameters:**
- `_event`: The event name.
- `_handler`: The function to remove as a listener.

### `on`
```typescript
on(_event: StreamEvents, _handler: void);
```
Adds an event listener to the window object.

**Parameters:**
- `_event`: The event name.
- `_handler`: The function to execute when the event occurs.

### `initDom`
```typescript
initDom(_id: string = 'avatarContainer', _params?: InitParams);
```
Initializes the DOM elements required for the avatar's display and interaction.

**Parameters:**
- `_id`: ID of the main container element.
- `_params`: Configuration parameters including avatar ID, voice, language, etc.

## Example Usage
```typescript
// Initialize with token
const avatar = new StreamingAvatar({ token: 'your_api_token' });

// Set up event handlers
avatar.on('messageSent', (event) => {
    console.log('Message sent:', event.detail);
});

// Emit a custom event
avatar.emit('toggleSession', { status: 'start' });
```

## Events
- `messageSent`: Triggered when a message is successfully sent.
- `sessionToggled`: Fired when the session is started or stopped.

## Configuration Parameters (`InitParams`)
```typescript
{
    avatarId?: string;
    voiceId?: string;
    language?: string;
    modeType?: number;
    background?: string;
    duration?: number;
    voice_interaction_on?: boolean;
}
```

This documentation provides a comprehensive overview of the `StreamingAvatar` class, enabling developers to integrate and customize avatars in their applications effectively.

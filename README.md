# WebRTC Core

WebRTC Core is developed based on ([webrtc](https://webrtc.org/getting-started/overview)).
WebRTC Core allows users communicate in real time.

## Integrated

- Git clone: `$ git clone https://gitlab.saver.jp/devmat/VSWebRTCCore_Js.git`
- Embed script to project: `<script type="text/javascript" src="../../dist/vswebrtc_client.min.js"></script>`

## Initialization

Get a singleton instance:

```
var vsWebRTCClient = VSWebRTCClient.getInstance();
```

### 1) Initialize listeners

```
	vsWebRTCClient.setRoomListener(onJoinRoomSuccess, onJoinRoomFail, onLeaveRoomSuccess, onLeaveRoomFail, onRoomInfoChanged, onMemberJoin, onMemberLeave, onOnline, onOffline);
    vsWebRTCClient.setCallListener(onCallSuccess, onCallFail, onHangupSuccess, onHangupFail, onBeCalled, onBeAccepted, onBeRejected, onHungup, onResponseSuccess, onResponseFail);
    vsWebRTCClient.setEventListener(onEventError);
    vsWebRTCClient.setConnectionListener(onConnectSuccess, onConnectFail, onDisconnected);
    vsWebRTCClient.setMessageListener(onError, onMessage, onOpen, onClose);
    vsWebRTCClient.setStreamListener(onRemoteStreamChanged, onPlayStreamFail);
    vsWebRTCClient.setMediaRTCListener(onMediaCommunicationError, onMediaCommunicationOpened);
```

- `vsWebRTCClient.setRoomListener(onJoinRoomSuccess, onJoinRoomFail, onLeaveRoomSuccess, onLeaveRoomFail, onRoomInfoChanged, onMemberJoin, onMemberLeave, onOnline, onOffline)`: Set room event
  - `onJoinRoomSuccess (room)` Callback when join room success
  - `onJoinRoomFail (roomName, msg)` Callback when join room fail
  - `onLeaveRoomSuccess (room)` Callback when leave room success
  - `onLeaveRoomFail (roomName, msg)` Callback when leave room fail
  - `onRoomInfoChanged (room)` Callback when room info changed
  - `onMemberJoin (room, user)` Callback when any member joins room
  - `onMemberLeave (room, user)` Callback when any member leaves room
  - `onOnline (room, user)` Callback when any member is online
  - `onOffline (room, user)` Callback when any member is offline
- `vsWebRTCClient.setCallListener(onCallSuccess, onCallFail, onHangupSuccess, onHangupFail, onBeCalled, onBeAccepted, onBeRejected, onHungup, onResponseSuccess, onResponseFail)`: Set call events
  - `onCallSuccess(room, partner)` Callback when call success
  - `onCallFail(room, partner, errMsg)` Callback when call fail
  - `onHangupSuccess(room, partner)` Callback when hangup success
  - `onHangupFail(room, partner)` Callback when hangup fail
  - `onBeCalled(room, caller)` Callback when be called
  - `onBeAccepted(room, acceptor)` Callback when be accepted
  - `onHungup(room, acceptor)` Callback when hung up
  - `onResponseSuccess(room, partner, stream)` Callback when response success
  - `onResponseFail(room, partner, msg)` Callback when response fail
- `vsWebRTCClient.setEventListener(onEventError)`: Set events
  - `onEventError(errorMsg)` Callback when event error
- `vsWebRTCClient.setConnectionListener(onConnectSuccess, onConnectFail, onDisconnected)`: Set connection events
  - `onConnectSuccess()` Callback when connect success
  - `onConnectFail(msg)` Callback when connect fail
  - `onDisconnected()` Callback when disconnect fail
- `vsWebRTCClient.setMessageListener(onError, onMessage, onOpen, onClose)`: Set message events
  - `onError(room, partner, errorMsg)` Callback when message error
  - `onMessage(room, partner, msg)` Callback when receive message
  - `onOpen(room, partner)` Callback when data channel open
    - `onClose(room, partner)` Callback when data channel close
- `vsWebRTCClient.setStreamListener(onRemoteStreamChanged, onPlayStreamFail)`: Set stream events
  - `onRemoteStreamChanged(room, partner, stream)` Callback when remote stream changed
  - `onPlayStreamFail(error, element, stream)`Callback when play remote stream failed
- `vsWebRTCClient.setMediaRTCListener(onMediaCommunicationError, onMediaCommunicationOpened)`: Set media communication events
  - `onMediaCommunicationError(room, partner, errorMsg)` Callback when start media communication fail
  - `onMediaCommunicationOpened(room, partner)` Callback when start media communication success

### 2) Configure

a. Set uid for all sessions, need to set before of calling connect. (Required)

```
	vsWebRTCClient.identify(uid);
```

- uid is unique and defined by user.

b. Configure Signaling Url and Ice Servers. (Required)

```
    vsWebRTCClient.configure(signaling_url, [new IceServer("stun:host"), new IceServer("turn:host", "username", "password")]);
```

- `signaling_url`: Signaling is used to send signals via web socket.
- `[new IceServer("stun"), new IceServer("turn", "username", "password")]`: Array of ICE Servers.

c. Configure local media stream. (Optional)

```
    vsWebRTCClient.configStream(StreamHandler.TRACK_KIND_CAMERA, {
        fps: 30,
        bitrate_max: -1, //no limit
        width: 640,
        height: 320,
        codec: 'H264' | 'VP8' | 'VP9',
        video_source_id: 1
    });

    vsWebRTCClient.configStream(StreamHandler.TRACK_KIND_MIC, {
        bitrate_max: -1, //no limit
        codec: 'opus' | 'ISAC',
        audio_source_id: 1,
        echo_cancellation: true,
        auto_gain_control: true,
        noise_suppression: true,
        highpass_filter: true,
        typing_noise_detection: true,
        enable_constant_bitrate: false
    });

    vsWebRTCClient.configStream(StreamHandler.TRACK_KIND_SCREEN, {
        cursor: 'always' | 'motion' | 'never',
        display_surface: 'monitor' | 'application' | 'browser' | 'window'
    });
```

d. Set transport type. (Optional)

```
	vsWebRTCClient.configIceTransportPolicy(StreamConfiguration.CONNECT_TYPE_BOTH | StreamConfiguration.CONNECT_TYPE_STUN | StreamConfiguration.CONNECT_TYPE_TURN);
```

e. Set log level. (bitwise OR) (Optional)

```
	vsWebRTCClient.setLogReporting(LOG_INFO | LOG_WARN | LOG_ERROR | LOG_DEBUG);
```

### 3) Local stream

a. Make local media stream:

```
	vsWebRTCClient.makeStream([kinds], onMakeStreamSuccess, onMakeStreamFail, method, isCleanStream);
```

- `kinds` array of expected tracks for local stream. (StreamHandler.TRACK_KIND_CAMERA | StreamHandler.TRACK_KIND_MIC | StreamHandler.TRACK_KIND_SCREEN)

- `onMakeStreamSuccess(stream)` receive when makeStream success.

- `onMakeStreamFail(errorMsg, type)` receive when makeStream failed.

- `method`: TRACK_CREATE_NEW | TRACK_CREATE_NEW_IF_DONT_EXIST | TRACK_DONT_CREATE

- `isCleanStream`: clean tracks of old stream or not

b. ShowStream

```
	vsWebRTCClient.showStream(renderProxy, stream);
```

- `renderProxy` (object) Local video element
- `stream` (MediaStream) Media stream

b. Pause stream:

```
	vsWebRTCClient.pauseStream([kinds]);
```

- `kinds` array of expected tracks for stream. (StreamHandler.TRACK_KIND_CAMERA | StreamHandler.TRACK_KIND_MIC | StreamHandler.TRACK_KIND_SCREEN)

c. Resume stream:

```
	vsWebRTCClient.resumeStream([kinds])
```

- `kinds` array of expected tracks for stream. (StreamHandler.TRACK_KIND_CAMERA | StreamHandler.TRACK_KIND_MIC | StreamHandler.TRACK_KIND_SCREEN)

d. Stop stream:

```
	vsWebRTCClient.stopStream([kinds]);
```

- `kinds` array of expected tracks for stream. (StreamHandler.TRACK_KIND_CAMERA | StreamHandler.TRACK_KIND_MIC | StreamHandler.TRACK_KIND_SCREEN)

## Action

### 1) Connect signaling

Connect to signaling server.

```
	vsWebRTCClient.connect();
```

### 2) Disconnect signaling

Hang up all connections, clear all rooms, disconnect to signaling server and close all peer connections.

```
	vsWebRTCClient.disconnect();
```

### 3) Check signaling connected

Check if VSWebRTCClient connected to signaling server

```
	vsWebRTCClient.isConnected()
```

### 4) Join room

Join room.

```
	vsWebRTCClient.joinRoom(roomName , nickName, extend_info);
```

- `roomName` (string) Name of room
- `nickName` (string) Nick name
- `extend_info` (Json object) More information data about user

### 5) Leave room

Leave room and hang up all connections.

```
	vsWebRTCClient.leaveRoom(room);
```

`room` (Room object) The room you want to leave

### 6) Call

Call to a specified partner on a specified room.

```
	vsWebRTCClient.call(room, callToUser);
```

- `room` (Room object) The room where you and your partner is in.
- `callToUser` (User object) The partner you want to make a call.

### 7) Response: accept/reject a calling request

When you got a calling request from an partner, invoke this function if you want to accept/reject that calling request.

```
	vsWebRTCClient.response(room, caller, deny=false);
```

- `room` (Room object) The room where you and your partner is in.
- `caller` (User object) The partner you want to accept/reject the calling request.
- `deny` (boolean) 1: reject, 2: accept

### 8) Show remote stream

When remote stream changed, callback `onRemoteStreamChanged(room, partner, stream)` is called.
Function `showstream` will call into `onRemoteStreamChanged` and use `stream` to show remote stream

```
	vsWebRTCClient.showStream(renderProxy, stream);
```

- `renderProxy` (object) Remote video element
- `stream` (MediaStream) Media stream

### 9) Get remote stream

Get the media stream of your partner

```
	vsWebRTCClient.getRemoteStream(room, targetUser);
```

- `room` (Room object) The room where you and your partner is in
- `targetUser` (User object) Your partner

### 10) Get local stream

```
	vsWebRTCClient.getLocalStream()
```

### 11) Notify to partner when local stream changed

```
	vsWebRTCClient.notifyStreamChanged(room, partner);
```

- `room` (Room object room The room where you and your partner is in
- `partner` (User object) The partner you want to notify

### 12) Hang up connection and hange up all connections

a. Hang up connection to the specific partner

```
	vsWebRTCClient.hangup(room, partner);
```

- `room` (Room object) The room where you and your partner is in.
- `partner` (User object) The partner you want to hang up.

b. Hange up all connections

Hang up all connections, clear all rooms, disconnect to signaling server and close all peer connections.

```
	vsWebRTCClient.hangupAll();
```

### 13) Create peer connection, open media stream and sharing

This function will create a peer connection, open media stream communication.

```
	vsWebRTCClient.startMediaCommunication(room, partner);
```

- `room` (Room object) The room where you and your partner is in
- `partner` (User object) The partner you want to sharing media stream

### 14) Get stats of a peer session

Get statistic of a peer session.

```
	vsWebRTCClient.getMediaCommunicationStats(room, partner);
```

- `room` (Room object) The room where you and your partner is in
- `partner` (User object) The partner you're comunnicating.

### 15) Send message

You can send message via signaling or webrtc.

a. Send message via signaling
Send message via signaling server

```
	vsWebRTCClient.sendMessage(room, targetUser, msg, viaSignaling);
```

- `room` (Room object) The room where you and your partner is in
- `targetUser` (User object) Your partner
- `msg` (String) Message content
- `viaSignaling` (boolean) The message will be sent via signaling (viaSignaling=true)

b. Send message via webrtc
Send message via data channel of webrtc.

```
	vsWebRTCClient.sendMessage(room, targetUser, msg, viaSignaling);
```

- `room` (Room object) The room where you and your partner is in
- `targetUser` (User object) Your partner
- `msg` (String) Message content
- `viaSignaling` (boolean) The message will be sent via data channel (viaSignaling=false)

c. Send message to all partners in room
If partner user is undefined, message will send to all partners via signaling.

```
    vsWebRTCClient.broadcastMessage(room, msg);
```

- `room` (Room object) The room where you and your partner is in
- `msg` (String) Message content

## Bugs and Feedback

## Authors

This project is developed by VinaSaver team

## LICENSE

This project is licensed under the Saver corp
Contact to Saver corp for details

# Lunar Websocket docs
Documentation for Lunar Client's websocket connection

## Protocol
Please read about the protocol here: https://github.com/Solar-Tweaks/LunarSocket/blob/main/doc/protocol.md

## Connecting to the websocket server
### Lunar's websocket server's address: `wss://assetserver.lunarclientprod.com/connect`

Create a websocket client connected to the address, with the following handshake arguments:

|          Key          | Type    | Comment                                                                | Example(s)                                 |
| :-------------------: | ------- | ---------------------------------------------------------------------- | :----------------------------------------- |
|      accountType      | string  | Game profile type                                                      | "XBOX", "MOJANG"                           |
|         arch          | string  | CPU architecture                                                       | "amd64"                                    |
|     Authorization     | JWT     | JWT token, received when logging into the Mojang / Microsoft account   | "xx.xxx.xx"                                |
|        branch         | string  | Branch of the Lunar Client instance                                    | "master"                                   |
|      clothCloak       | boolean | User has cloth cloaks                                                  | "false"                                    |
|       gitCommit       | string  | Commit id, not sure if it can be requested from somewhere              | "9f9eccb18fdf008b833e381bb12457599de2e61f" |
|    hatHeightOffset    | JSON    | JSON of the hat height offset, for each of the hats?                   | "[{\\"id\\":2528,\\"height\\": 0.0}, ...]" |
|         hwid          | string  | Hardware ID from the machine connecting, just set it to "not supplied" | "not supplied"                             |
|    launcherVersion    | string  | Version on Lunar Client's launcher, just set it to "not supplied"      | "not supplied"                             |
|    lunarPlusColor     | int     | Color code? of Lunar Plus                                              | "-1"                                       |
|          os           | string  | Operating system                                                       | "Linux"                                    |
|       playerId        | UUID    | UUID of the player                                                     | "b876ec32-e396-476b-a115-8438d83c67d4"     |
|    protocolVersion    | int     | Version of the websocket's protocol                                    | "7"                                        |
|        server         | string  | IP of the initial server, usually an empty string                      | ""                                         |
|  showHatsOverHelmet   | boolean | Show hats over helmet                                                  | "true"                                     |
| showHatsOverSkinLayer | boolean | Show hats over skin layer                                              | "true"                                     |
|       username        | string  | Name of the user                                                       | "Technoblade"                              |
|        version        | string  | Version of Minecraft launched, format: `v1_X` where X is 1.X           | "v1_8"                                     |
**Please note, that all values are supplied as strings, for example a boolean is *"true"*, not *true***

## Warning
**You need to have a basic understanding about Minecraft packets, before continuing!**

**All the examples, key names are taken from the [LunarSocket protocol docs](https://github.com/Solar-Tweaks/LunarSocket/blob/main/doc/protocol.md)**.

---

## Connecting to a Minecraft server


If you want other players to see that you're on Lunar Client send a [JoinServerPacket](https://github.com/Solar-Tweaks/LunarSocket/blob/main/doc/protocol.md#joinserver-serverbound---6) (id: `6`) every time you join, or leave a server to the websocket

Packet data sent when joining the server: `{"uuid":"","server":"hypixel.net"}`, the uuid will always be an empty string, server is the address of the selected server.

Packet data sent when **leaving** the server: `{"uuid":"","server":""}`, both fields need to be empty.

## Getting Lunar users from the world

If you've done everything so far, the players should see you as a Lunar Client user, yayyyy 🎉. Though you might want to see who uses Lunar Client (or at least is connected to the websocket, haha). First you need to request data about the players, and then you will [receive the data later](#receiving-lunar-client-user-informations).

### Joining a world

You only need to send a [PlayerInfoRequest](https://github.com/Solar-Tweaks/LunarSocket/blob/main/doc/protocol.md#playerinforequest---48) (id: `48`) with the players' UUIDs.

Example of PlayerInfoRequest: `{"uuids":["069a79f4-44e9-4726-a5be-fca90e38aaf5","853c80ef-3c37-49fd-aa49-938b674adae6"]}`

### Updating players in the current world
Players might join / leave your lobby. Around every 150ms, send an [UpdateVisiblePlayers](https://github.com/Solar-Tweaks/LunarSocket/blob/main/doc/protocol.md#updatevisibleplayers---50) (id `50`) with the **difference** of players' UUIDs, in a list.

Example of UpdateVisiblePlayers, updating players: `{"players":["069a79f4-44e9-4726-a5be-fca90e38aaf5"]}`

### Leaving a world
After leaving the world, send an [UpdateVisiblePlayers](https://github.com/Solar-Tweaks/LunarSocket/blob/main/doc/protocol.md#updatevisibleplayers---50) (id `50`) of all the players, that you're seeing. Lunar Client send this packet after the JoinServerPacket if you're leaving the whole server.

Example of UpdateVisiblePlayers, leaving the world: `{"players":["069a79f4-44e9-4726-a5be-fca90e38aaf5","853c80ef-3c37-49fd-aa49-938b674adae6"]}`

## Receiving Lunar Client user informations

After you sent the requests, you'll receive [PlayerInfo](https://github.com/Solar-Tweaks/LunarSocket/blob/main/doc/protocol.md#playerinfo---8) (id: `8`) packets about the requested players **who are Lunar Client users**. Lunar won't send back any packets about the other players, only about their users.

Example of PlayerInfo: `{"uuid":"30b935b8-5b5e-408f-b9dc-1398c98f31b3","cosmetics":[{"id":2377,"equipped":true}],"color":16733695,"unknownBooleanA":false,"premium":true,"clothCloak":false,"showHatAboveHelmet":true,"scaleHatWithHeadwear":true,"adjustableHeightCosmetics":{"2424":0,"2490":0,"2491":0,"2492":0,"2493":0,"2494":0,"2526":0,"2527":0,"2528":0,"2540":0,"2541":0,"2542":0,"2543":0,"2544":0,"2545":0,"2558":0,"2559":0},"plusColor":5636095,"unknownBooleanB":false}`

Note: *this example is from a Lunar+ subscriber's packet data*

## Credits

Huge shoutout to the [Solar Tweaks](https://github.com/Solar-Tweaks) team, for making the protocol available for everyone, and for creating an awesome websocket proxy, [Lunar Socket](https://github.com/Solar-Tweaks/LunarSocket) for Lunar Client!
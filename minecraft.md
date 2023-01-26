# Minecraft

## Terms

- Java: PC version
  - Hardcore and Spectator modes are only available in the Java Edition
- Bedrock: Console, iOS etc version
  - Better for cross-platform play
  - While the Bedrock Edition does have add-ons, it features more paid content to add to the game, whereas the Java version lets you install mods (such as texture packs) for free

## Docker Server

https://github.com/itzg/docker-minecraft-bedrock-server

```sh
docker run -d -it -e EULA=TRUE -p 19132:19132/udp itzg/minecraft-bedrock-server
```

Need to ensure VM has 19132/UDP open.

## Add-on Install

Mods are called Add-ons for Bedrock version.  See below for install instructions:
https://github.com/itzg/docker-minecraft-bedrock-server#mods-addons


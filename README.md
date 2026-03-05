# TransmogBridge

WoW 12.x addon that patches a client serializer bug in the outfit-based transmog system.

## The Problem

The 12.x WoW client omits **head, mainhand, offhand, and enchant illusion** slots from `CMSG_TRANSMOG_OUTFIT_UPDATE_SLOTS`. This causes the server to receive incomplete outfit data, resulting in missing transmogs on those slots.

## The Solution

TransmogBridge intercepts the outfit save/apply flow on the client side, reads the full pending outfit state via `C_TransmogOutfitInfo.GetViewedOutfitSlotInfo()`, and sends the complete slot data to the server via an addon message. The server merges this with the incomplete packet data before applying.

## How It Works

1. Client sends `CMSG_TRANSMOG_OUTFIT_UPDATE_SLOTS` (missing 4+ slots)
2. TransmogBridge sends a `TRANSMOGBRIDGE` addon message with all 17 slot entries
3. Server's `HandleTransmogOutfitUpdateSlots` defers finalization
4. Server's `ChatHandler` receives the addon message and merges missing IMA IDs
5. Server finalizes and applies the complete outfit

## Installation

Copy `TransmogBridge.lua` and `TransmogBridge.toc` to:
```
WoW/_retail_/Interface/AddOns/TransmogBridge/
```

## Requirements

- WoW 12.0.1 (Midnight) client
- TrinityCore server with outfit-based transmog handling (PR #760 or equivalent)

## License

GPL-2.0 — Same as TrinityCore

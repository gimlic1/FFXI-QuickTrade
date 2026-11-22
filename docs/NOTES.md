# FFXI Addon — QuickTrade

> [!NOTE]
> **Quick Reference**
> - **Path:** `C:\Windower\addons\QuickTrade\`
> - **Author / Version:** Valok@Asura, edited by Gimlic / v1.8.1
> - **Commands:** `//qtr [all] [loop [<count>]]`
> - **Load Status:** `scripts/init.txt`
> - **Origin:** Custom / Modified (Gimlic v1.8.1 edit)

Automates bulk NPC turn-ins and storage trades by scanning inventory (and optionally Mog Sack/Case via Itemizer) for items accepted by the targeted NPC and dispatching `tradenpc` commands.

## What the Code Does
- Inspects the currently targeted mob (`'t'`), or runs `/targetnpc` if none is selected, and matches `target.name` against `npcTable` (covering crystals/clusters to Ephemeral Moogles/Waypoints, Shami seals/crests, Moat Carp, Copper Vouchers, **Silver Vouchers**, Rem's Tales, Mellidopt Wings, Salvage Plans, Alexandrite, **Special Gobbiedial Keys + Dial Key #Ab**, Zinc Ore, Yagudo Necklaces, Mandragora-Mad items, Only the Best items, Soul Plates, JSE Capes, Ancient Beastcoins, Reisenjima Stones, Befouled Water, and Geas Fete pop items for Affi/Dremi/Shiftrix).
- When `all` is passed, scans `sack` and `case` and issues Itemizer `get "<item>" <count>` commands if inventory space permits.
- Calculates total remaining trades (`numTrades`) and builds a `tradenpc <qty> "<item>" ...` command respecting per-NPC trade rules (e.g., 8 slots max, 1-at-a-time for Gobbiedial keys/capes/Soul Plates, 4 for Zinc Ore/Yagudo Necklaces, 3 matching JSE capes for Makel-Pakel, key-item deduplication for Geas Fete via `windower.ffxi.get_key_items()`).
- In `loop` mode, sleeps `npc.loopWait` seconds between trades and aborts if the target NPC changes or inventory fills up (for Gobbiedial keys).
- Registers `incoming text` to strip NPC continuation bytes (`0x7F, 0x31`) on modes `150`/`151` for up to 10 seconds after a trade (disabled for `JSE Capes x3` and Geas Fete trades).

## Commands & Usage
- `//qtr` — Perform one trade batch with the targeted (or nearest) supported NPC.
- `//qtr all` — Pull matching items from Mog Sack and Mog Case using Itemizer first, then trade.
- `//qtr loop [<max_count>]` — Repeat trades automatically (up to `<max_count>`, or all available trades for `loopable = true` NPCs).
- Requires **TradeNPC** loaded to execute trades, and **Itemizer** for `//qtr all`.

## Events, Packets & Technical Hooks
- **Windower Events:** `addon command`, `incoming text`
- **Packets:** None directly (delegates packet trading to `TradeNPC` via `windower.send_command('tradenpc ...')`)
- **Dependencies / Libraries:** `tables`, `coroutine`, `resources` (runtime dependency on `TradeNPC` and optionally `Itemizer`)

## Local Settings & Customizations
- **Gimlic v1.8.1 Edit (highlighted in `QuickTrade.lua` lines 29–30, 135–137, 166–169, 334):**
  - `_addon.author = 'Valok@Asura edit Gimlic'`, `_addon.version = '1.8.1' -- added Silver Vouchers and dial key #Ab`.
  - Added `silverVoucherIDs` (`{id = 9277, name = 'silver voucher', stacksize = 99}`) and mapped NPC `'Greyson'` (`loopable = false`) in `npcTable`.
  - Added `{id = 9217, name = 'dial key #ab', stacksize = 99}` into `spGobbieKeyIDs` alongside `special gobbiedial key` (`8973`), allowing all 11 Gobbie box NPCs (`Mystrix` through `Winrix`) to trade `#Ab` keys as well.

## Related Notes
- `FFXI Windower Overview`
- `FFXI Windower Addons Catalog`

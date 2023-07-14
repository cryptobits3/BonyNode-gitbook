---
coverY: 0
---

# 🏝 Governance Proposals Bots

## Telegram Proposal Bot <a href="#telegram-proposal-bot" id="telegram-proposal-bot"></a>

### Intro <a href="#intro" id="intro"></a>

Kjnodes team have built bots to keep validators notified when there is a new proposal on the chain. To distinguish mainnet from testnet we have created two separate bots that can be found by following links provided below.

|                                                                         | Supported chains | Users | Active subscriptions |
| ----------------------------------------------------------------------- | ---------------- | ----- | -------------------- |
| [🤖 Mainnet Proposal Bot](https://t.me/kjnodes\_proposal\_bot)          | 17               | 140   | 226                  |
| [🤖 Testnet Proposal Bot](https://t.me/kjnodes\_testnet\_proposal\_bot) | 27               | 421   | 1331                 |

### How to use <a href="#how-to-use" id="how-to-use"></a>

To start using the bot type `/start` command and choose the networks in which you want to get notified. 🥝 icon will tell you which network notifications are turned on.

<figure><img src="https://services.kjnodes.com/assets/images/bots/telegram-proposal/menu.png" alt=""><figcaption></figcaption></figure>

Currently bots support chains where we operate our own RPC endpoints. List of supported chains can be found in the bot menu.

That’s it! You are all set! Here is some example of the notification from the bot.

<figure><img src="https://services.kjnodes.com/assets/images/bots/telegram-proposal/notification.png" alt=""><figcaption></figcaption></figure>

From kjnodes with ❤

## Discord Proposal Bot <a href="#discord-proposal-bot" id="discord-proposal-bot"></a>

### Intro <a href="#intro" id="intro"></a>

As a contribution to Cosmos ecosystem our team has built this bot to keep chain governance participants notified when there is a new proposal on chain.

### How it works <a href="#how-it-works" id="how-it-works"></a>

Bot checks the proposals on chain every minute using our RPC node, if new proposal is found - bot post it in a Discord using a Webhook.

#### Workflows <a href="#workflows" id="workflows"></a>

Our bot supports three workflows giving the projects oportunity to choose workflow they like the most.

1.  **Simple proposal notifications** - this workflow is very simple. Bot connects to dedicated channel using WebHook. Proposal cards are being sent to dedicated channel notifying the relevant user groups.

    ![](https://services.kjnodes.com/assets/images/bots/discord-proposal/simple.gif)_Simple Proposals example_
2.  **Proposal notifications with threads** - more advanced worflow where bot is invited to the server with set of permissions. This workflow is similar to first one, but it also adds reactions ✅ and 🧵 threads to the posts where validators can do discussions.

    ![](https://services.kjnodes.com/assets/images/bots/discord-proposal/threaded.gif)_Threaded Proposals example_
3.  **Forum proposals** - more advanced worflow where bot is invited to the server with set of permissions. Bot creates forum posts with proposal cards and sends out notification in announcement channel (this feature can be enabled/disabled based on client needs) This workflow is very similar to governance solution used in [Osmosis Discord Server](https://discord.gg/osmosis).

    ![](https://services.kjnodes.com/assets/images/bots/discord-proposal/forum.gif)_Forum Proposals example_

### How to use <a href="#how-to-use" id="how-to-use"></a>

If you are interested and want us to implement bot into your Discord server please reach out at [admin@kjnodes.com](mailto:admin@kjnodes.com)

As we fully manage current service our bot support chains where we operate our own RPC endpoints.

From kjnodes with ❤

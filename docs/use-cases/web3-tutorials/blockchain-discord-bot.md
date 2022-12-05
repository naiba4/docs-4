---
title: "Blockchain Discord Bot"
slug: "blockchain-discord-bot"
hidden: false
createdAt: "2022-11-24T12:32:10.556Z"
updatedAt: "2022-11-27T13:11:12.142Z"
---
## Introduction

This tutorial teaches you how to create a web3 discord bot that listens to blockchain events and sends real-time notifications.

We will use discord.js, express and Moralis Streams API.

### YouTube Tutorial

https://www.youtube.com/watch?v=GiDXKT_AAIs


## Project Setup

This section is split in multiple steps that you can follow. Let's dive in.

You can find the repository with the final code here: [Github](https://github.com/MoralisWeb3/youtube-tutorials/tree/main/Discord-Blockchain-Bot).

1. ### Create a folder and initiate a project using the package manager of your choice.

```Text npm
npm init -y
```
```Text yarn
yarn init -y
```
```Text pnpm
pnpm init
```



This will initiate the project and create a `package.json` file containing the following:

```json package.json
{
    "name": "blockchainbot",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "MIT"
}

```



2. ### Install the required dependencies.

```shell npm
npm install moralis express discord.js dotenv nodemon
```
```shell yarn
yarn add moralis express discord.js dotenv nodemon
```
```shell pnpm
pnpm add moralis express discord.js dotenv nodemon
```



3. ### Create an `index.js` file containing our express endpoint.

```javascript index.js
const express = require("express");
const app = express();
const port = 3000;

app.use(express.json());

app.post("/webhook", async (req, res) => {
  const { body, headers } = req;

	console.log(body);

	res.status(200).json();
});

app.listen(port, () => {
	console.log(`Listening to streams`);
});

```



4. ### Setup an `ngrok` tunnel to our express app that will be running locally

- Install `ngrok`.

```shell Shell
npm i -g ngrok
```



- In a new terminal window run `ngrok http` to create a tunnel to our express app using the same port that we specified inside `index.js`

```shell Shell
ngrok http 3000
```



This will give us an address where out express app will be running and where we can setup webhooks for streams api.

![](https://files.readme.io/c2b72e9-Screenshot_2022-11-24_at_15.24.44.jpg)

5. ### Start our express server.

Back inside `package.json` add a start script that will start our express app and reload when we make changes.

```json package.json
{
    "name": "blockchainbot",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "start": "nodemon index.js",
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
        "discord.js": "^14.6.0",
        "dotenv": "^16.0.3",
        "express": "^4.18.2",
        "moralis": "^2.6.7",
        "nodemon": "^2.0.20"
    }
}

```



Run the start command.

```Text npm
npm run start
```
```Text yarn
yarn run start
```
```Text pnpm
pnpm start
```



## Streams Setup

We will use Moralis Streams API to get real-time events and send notifications

 Inside our admin panel access [streams section](https://admin.moralis.io/streams) and create a new stream.

![](https://files.readme.io/1a3f153-Screenshot_2022-11-24_at_15.54.14.jpg)

Select **Create From Scratch**

![](https://files.readme.io/af70faf-Screenshot_2022-11-24_at_15.55.22.jpg)

For this example we will use this simple donation contract, but you can use any smart contract of your choice:

- Contract Address : `OxbD317B46A004CCF0e1773DBFcEE89E77e32D2dB9`
- Abi: 
  ```json Abi
  [{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":false,"internalType":"address","name":"from","type":"address"},{"indexed":false,"internalType":"uint256","name":"amount","type":"uint256"}],"name":"Donate","type":"event"},{"inputs":[],"name":"newDonation","outputs":[],"stateMutability":"payable","type":"function"}]
  ```

For the webhook url have to use the url provided previously by ngrok, followed by our `/webhook` endpoint from our express app

![](https://files.readme.io/3083fdd-Screenshot_2022-11-24_at_16.05.02.jpg)

Our contract is deployed on mumbai testnet, so here you will select the network that your smart contrat is deployed on: 

![](https://files.readme.io/89031d2-Screenshot_2022-11-24_at_16.07.14.jpg)

Because our donations are sending native currency to a smart contract, here we will select native transactions and check Event Emittance.

![](https://files.readme.io/d2fc65d-Screenshot_2022-11-24_at_16.11.00.jpg)

Next we have to add the ABI and select the topic we with to stream.

![](https://files.readme.io/7a495e0-image.png)

After creating the stream Moralis will send an empty POST request to confirm that the stream was created successfully.

![](https://files.readme.io/3ce0485-image.png)

## Verify Stream Sender

Right now everybody what knows our webhook endpoint is able to make a POST request to our express server.

To stop this form causing any errors, what we have to do know is to verify that the request are sent by Moralis using `Moralis.Streams.verifySignature`.

Back to our express endpoint, we can add logic that allows only request coming from Moralis to go through.

1. ### Go to [Moralis Admin Panel](https://admin.moralis.io/web3apis) and copy your web3 api key.

![](https://files.readme.io/a254a4d-Screenshot_2022-11-24_at_16.29.26.jpg)

2. ### Create an `.env` file.

```shell .env
APIKEY = 'YOUR_API_KEY'
```



3. ### Verify the request.

```javascript index.js
const express = require("express");
const Moralis = require("moralis").default;
require("dotenv").config();
const app = express();
const port = 3000;

app.use(express.json());

app.post("/webhook", async (req, res) => {
  const { body, headers } = req;

  try {
    Moralis.Streams.verifySignature({
      body,
      signature: headers["x-signature"],
    });
    
    return res.status(200).json();
  } catch (e) {
    console.log("Not Moralis");
    return res.status(400).json();
  }
});

Moralis.start({
  apiKey: process.env.APIKEY,
}).then(() => {
  app.listen(port, () => {
    console.log(`Listening to streams`);
  });
});
```



## Discord Bot Setup

We will now begin to setup our bot to send notifications to our discord server.

1. Access [Discord Developer Portal](https://discord.com/developers) and create a new application.

![](https://files.readme.io/a10c89c-Screenshot_2022-11-24_at_16.41.34.jpg)

2. Add a new bot to your application.

![](https://files.readme.io/f180c62-Screenshot_2022-11-24_at_16.44.35.jpg)

3. Add the scope and the permissions to your bot.

![](https://files.readme.io/77f8469-image.png)

![](https://files.readme.io/590011d-image.png)

3. Open the generated url in a new browser tab and invite the bot to your server.

![](https://files.readme.io/385ae12-image.png)

5. Back in the Bot section, Reset and copy the new token.

![](https://files.readme.io/47b8e98-Screenshot_2022-11-24_at_16.51.35.jpg)

6. Add the token and the channel id you wish to send the message to inside your `.env` file.

```shell .env
APIKEY = 'YOUR_API_KEY'
PASS = 'YOUR_BOT_TOKEN'
CHANNEL = 'CHANNEL_ID'
```



7. Add the discord client to your `index.js` and setup the login to send messages.

```javascript index.js
const express = require("express");
const Moralis = require("moralis").default;
const discord = require("discord.js");
require("dotenv").config();
const app = express();
const port = 3000;

const client = new discord.Client({
    intents: [],
});

client.login(process.env.PASS);

app.use(express.json());

app.post("/webhook", async (req, res) => {
  const { body, headers } = req;

  try {
    Moralis.Streams.verifySignature({
      body,
      signature: headers["x-signature"],
    });


    let from = body.txs[0].fromAddress;
    let amount = Number(body.txs[0].value / 1E18);

    const channel = await client.channels.fetch(process.env.CHANNEL);
    channel.send(`New Donation submitted by ${from}, for ${amount.toFixed(2)} MATIC!!!!`);

    return res.status(200).json();
  } catch (e) {
    console.log("Not Moralis");
    return res.status(400).json();
  }
});

Moralis.start({
  apiKey: process.env.APIKEY,
}).then(() => {
  app.listen(port, () => {
    console.log(`Listening to streams`);
  });
});
```



## Testing the bot

1. Run your bot.

```shell Shell
node index.js
```



2. To test our bot, We can now call the donation function from our contract.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/604fd4b-image.png",
        null,
        ""
      ],
      "border": true
    }
  ]
}
[/block]



3. We should see a new notification sent by our bot.

![](https://files.readme.io/e8aac1e-image.png)
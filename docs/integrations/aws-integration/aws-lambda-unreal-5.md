---
title: "Unreal Engine 5"
slug: "aws-lambda-unreal-5"
excerpt: "This tutorial shows you how to connect an **Unreal game** to a **Moralis backend**, in this case hosted on **AWS Lambda**. We will retrieve a wallet's **native balance** and its **NFTs**.\nBelow we provide a sample game to set everything up in a few minutes."
hidden: false
createdAt: "2022-11-30T07:35:42.834Z"
updatedAt: "2022-12-03T11:54:51.949Z"
---
# Prerequisites

- Complete [AWS Lambda integration](https://docs.moralis.io/docs/using-aws-lambda).
- Install [Unreal Engine 5.1 ](https://docs.unrealengine.com/5.1/en-US/installing-unreal-engine/).
- Install [VaRest plugin](https://www.unrealengine.com/marketplace/en-US/product/varest-plugin) to Unreal Engine 5.1.

# Open sample game

> 📘 Download sample game
> 
> Go to [GitHub](https://github.com/MoralisWeb3/demo-unreal-aws-lambda) and clone/download the repository.

After downloading the project, launch Unreal Engine 5.1 and under _RECENT PROJECTS_, choose _**Browse**_:

![](https://files.readme.io/4353feb-image.png)

Search for the **`.uproject`** file and open it:

![](https://files.readme.io/ff45723-image.png)

The _Plugins_ window should automatically open but if not, go to the top toolbar and choose **_Edit --> Plugins_**:

![](https://files.readme.io/2e240ee-image.png)

Search for _VaRest_, **enable the checkbox** and close the _Plugins_ window:

![](https://files.readme.io/e3e2255-image.png)

> 📘 
> 
> As stated in the [prerequisites](https://docs.moralis.io/docs/aws-lambda-unreal-5#prerequisites), you need to have this plugin previously installed. We will use it to call our **Moralis API endpoints** hosted in **AWS Lambda**.

# Setting up

On the editor, click the blueprint icon and choose _**Open Level Blueprint**_:

![](https://files.readme.io/d39c0e4-image.png)

This is the blueprint holding most of the logic of this sample game.

Zoom out to visualize and locate **4 important areas**:

1. [**Manage Input**](https://docs.moralis.io/docs/aws-lambda-unreal-5#manage-input)
2. [**Construct Request Body**](https://docs.moralis.io/docs/aws-lambda-unreal-5#construct-request-body)
3. [**Get Native Balance**](https://docs.moralis.io/docs/aws-lambda-unreal-5#get-native-balance)
4. [**Get Wallet NFTs**](https://docs.moralis.io/docs/aws-lambda-unreal-5#get-wallet-nfts)

![](https://files.readme.io/0d0bd99-image.png)

## Manage input

> 📘 
> 
> No need to do any action here. Just informative.

Apart from enabling and disabling the player's input, here we get the `wallet address` and the `chain ID` from the text input fields when the _START_ button is clicked and we save them as variables:

![](https://files.readme.io/d81ba07-image.png)

## Construct request body

> 📘 
> 
> No need to do any action here. Just informative.

What interests us in this tutorial is to know **how do we call an API endpoint**. In this case we want to call the _getNativeBalance_ and _getWalletNfts_ endpoints hosted in our [Express AWS Lambda App](https://docs.moralis.io/docs/using-aws-lambda#lambda-nodejs-express-api). These endpoints need the _address_ and the _chain_ as request body parameters so here we construct the request and we **set it as a variable**:

![](https://files.readme.io/e9fdaae-image.png)

## Get Native Balance

Add the _getNativeBalance_ endpoint URL to the _**URL field**_. You can get it [following the instructions here](https://docs.moralis.io/docs/using-aws-lambda#testing-express-api-function):

![](https://files.readme.io/4b02975-image.png)

## Get Wallet NFTs

Add the _getWalletNfts_ endpoint URL to the **_URL field_**. You can get it [following the instructions here](https://docs.moralis.io/docs/using-aws-lambda#testing-express-api-function):

![](https://files.readme.io/0662f30-image.png)

## Saving changes

Click _**Compile**_ and then the _**Save icon**_: 

![](https://files.readme.io/c03b362-image.png)

> 👍 
> 
> Everything is set up correctly.

# Play the game

On the editor, choose **_Play_**:

![](https://files.readme.io/64b1f25-image.png)

Enter a _wallet address_ and a _chain ID_ and choose _**START**_:

![](https://files.readme.io/98ccd15-image.png)

> 📘 
> 
> In this tutorial, we enter **`80001`** as chain ID but here you can check [all the chains that Moralis supports](https://docs.moralis.io/reference/supported-chains).

> 📘 
> 
> Also make sure to enter an address that holds some NFTs to see them on the game.

On the left side we can see our **NFTs** displayed and our **Native balance** on the right side:

![](https://files.readme.io/d5835b1-image.png)

> 👍 Congratulations!
> 
> You've completed the **Moralis & Unreal Engine 5** integration tutorial.
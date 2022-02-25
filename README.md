# Eclectic-Token-Sniper
Eclectic Token Sniper (ETSniper) is a versatile token sniper for the blockchain. At the moment it only offers full support for the BSC and PancakeSwap (although some functionalities could work on other chains or DEXs), but work is in progress to support other EVM compatible chains. Stay tunned!

## Main features

- Multiple methods for sniping tokens:
	- From a list of token addresses to watch
	- From PairCreated events
	- From AddLiquidityETH transactions
	- From Telegram channels
- Tokens auditing, including:
	- Contract source verification and filtering by a configurable list of expressions
	- Minimum liquidity amount and percentage
	- Very quick and efficient in-house and in-chain honeypot check, that checks if a token can be approved and sold, with configurable limits for buy and sell taxes and gas usage
- Whitelisting of token addresses
- Blacklisting of addresses, symbols and names
- Stops buying tokens when the account balance is below a configurable threshold
- Monitoring of bought tokens that supports:
	- Taking profit after reaching a certain value
	- Setting a fixed stop loss
	- Setting a trailing stop
	- Disabling the sell of a token for a certain time after being bought
	- Selling a token after a certain amount of time

***

For bug reports, feedback or help, use the Issues functionality here on Github, send an email to [devareus@protonmail.com](devareus@protonmail.com) or join the
Telegram channel at [http://t.me/EclecticTokenSniper](http://t.me/EclecticTokenSniper).
 
A 7.5% developer fee is paid on all profits above 0.01 BNB.

If you want to support further development of this sniper you can send a donation on Ethereum or BSC to: 

0xa9f5B4Fd93ddA4eb247CC1cC726a6602a2Ce7F00


## How to run it
Download the corresponding executable for your platform from the Releases section and place it in an empty folder. At the moment there are binaries available for Windows x64, Linux x64, Linux ARM (32 and 64 bits) and MacOS. If you need binaries for another platform, get in touch and I'll try to build one.

Download the appsettings-template.json file, copy it as appsettings.json and put in the same folder as the executable.
Edit it following the instructions below.

Double click on the executable, or better, open a terminal/console and run it from it.

You can quit the sniper by pressing Ctrl+C. The status of the sniper is saved each time a token is bought, sold or the trailing stops are changed, so if you launch it again from the same folder, it will carry on monitoring them.

## appsettings.json

NOTE: The JSON standard doesn't allow comments, but the library I use does, so I used them to add clarity to the appsettings template. Feel free to remove them if they bother you.

### Accounts
  - WalletAddress: Your account address
  - WalletPrivateKey: Your account private key. It's a string of numbers and letters that you can export, from example, from your Metamask. *It's not the list of recovery words that gives access to all the accounts in your Metamask, but a separate key for each account in your wallet*. It's recommended to use a separate account so the sniper doesn't interfere with your normal activity or is affected by it. Don't hold in this account more funds than you need
  - BscScanApiKey: If you want to check if a token's contract is verified, you'll need to get an API key from BSCScan and put it here

### Node
  - ChainId: The blockchain Id. 56 for the BSC
  - NodeAddressHttp: The RPC endpoint of the blockchain node that you'll connect to. You can use one of the official nodes, get a node from moralis.io or deploy your own node
  - NodeAddressWss: For some snipping options you'll also need a websocket node endpoint. You can use the nariox node, get one from moralis.io or use your own one
  - GetContractSourceUrl: The URL for downloading the tokens' contracts' source codes. Don't change it unless you know what you're doing
### DEX
  - PancakeswapFactoryAddress: Pancake Swap's Factory contract address. Don't change it unless you know what you're doing. In principle other DEXs should work too (except for the honeypot checker; I'll eventually add support for others to it)
  - PancakeswapRouterAddress: Same, but the Router contract address
  - LiquidityPairAddress: The token that will be paired for liquidity in the DEX. At the moment only BNB is supported, but support for more will be added
### Global settings
  - TransactionRevertTimeSeconds: Maximum time for a transaction to be confirmed before it's automatically cancelled
  - MonitorPricesEverySeconds: When 0, it disables the monitoring of the prices of the tokens the sniper has bought. If set to a number, the sniper will check the prices regularly at the interval set (in seconds)
### Disable/enable Snipers
  - WatchedTokensSniper: If enabled, the sniper will audit once and again the tokens in the *WatchedTokens* list and will try to buy it once they pass the audit. This, with *AuditTokens* and *HoneypotCheckEnabled* enabled, is probably the most reliable method to snip known token addresses
  - PairCreatedEventSniper: If enabled, the sniper will listen for PairCreated events for new tokens to snipe
  - AddLiquiditySniper: If enabled, the sniper will listen for all AddLiquidityETHs transactions on the DEX for tokens to snipe. This method is not recommended unless you limit buys to the white list, or you'll end buying lots of old tokens
  - MempoolSniper: Ignore for now, this is still work in progress
  - TelegramSniper: If enabled, the sniper will listed on the configured Telegram channels, filtering by keywords and sniping token addresses for it. Use only with reliable sources
### Gas settings
  - BuyGasPrice: Gas price of buy transactions
  - ApproveGasPrice: Gas price for approval transactions
  - SellGasPrice: Gas price for sell transactions
  - BuyGasAmount: Maximum gas amount for buy transactions
  - ApproveGasAmount: Maximum gas amount for approval transactions
  - SellGasAmount: Maximum gas amount for sell transactions
### Taxes
  - MaxBuyTax: Used for the token audits. Maximum Buy Tax for sniping a token
  - MaxSellTax: Similarly, maximum Sell Tax for sniping a token
  - MaxTotalTax: Maximum total of taxes (buy+sell) for snipping a token

**Mind that the tokens owners can change the taxes at any time! That a token passes an audit is not guarantee that you'll be able to sell the token later or you won't be charged higher taxes. And obviously, it doesn't guarantee that the owner won't do a rug pull and remove all the liquidity from the DEX at any some point**

### Buy settings
-    BuyEnabled: Enables or disables the buy of tokens. This can be toggled while running by pressing the B key, but it will revert back to the default configured here if the sniper is restarted
-    MinWalletBalance: Minimum balance in your account for buying tokens. The sniper won't buy any more tokens if your balance is below this figure. If the balance increases (for example, you send more funds, or a token is sold) it will start buying again. I recommend to set this to slightly more than the *AmountToSnipe*, to guarantee that some funds are left to pay for the networks fees when selling.
-    AmountToSnipe: The default amount to buy of each snipped token (in BNB) 
-    MinLiquidityAmount: Minimum liquidity in BNB in the DEX to allow buying a token (if *CheckMinLiquidity* is enabled)
-    MinLiquidityPercentage: Minimum liquidity, as a percentage of the total supply, in the DEX to allow buying a token (if *CheckMinLiquidity* is enabled)
-    AuditTokens: Enables the audit of tokens before buying. You can enable or disable the followings checks separately:
		-    CheckContract: Only buys if the token's contract is verified on BSCScan and it doesn't contain any of the expressions in the red flags list
		-    CheckMinLiquidity: Doesn't buy if the available liquidity doesn't fulfil the conditions above
        -    HoneypotCheckEnabled: Using an in-house and in-chain solution checks if a token can be bought, approved and sold before buying. It can also check for maximum taxes and gas, using the limits set above
-    OnlyBuyWhitelisted: Will only buy tokens included in the white list
-    BuyDelaySeconds: Waits the indicated amount of seconds before buying a token. This is usually used to avoid antibots measures, but the Honeypot Check method is more reliable
### Sell settings
-    SellEnabled: Enables or disables selling the tokens previously bought. This can be toggled while running by pressing the S key, but it will revert back to the default configured here if the sniper is restarted
-    SellSlippage: Maximum slippage set during selling
-    TakeProfit: Sells a token if the current profit is above this figure. It's indicated as a percentage above the buy price, for example, 50 means "sell when the profit is above 50%", that is, the current price is about 150% of the buy price
-    StopLoss: Sets a stop loss. A token will be sold if its price goes below this percentage in relation to the buy price. For example, -25 means "sell when the prices has lost a 25% of its value"
-    EnableTrailingStop: Enables or disables the trailing stop. If the price of a token increases above a threshold, the stop loss will be adjusted at a certain distance from the current price
-    TrailingStopStrategy: Can be 0 or 1. If 0, the *TrailingStop* and *TrailingStopThreshold* will be used for the trailing stop as percentages in relation to the buy price. If 1, *TrailingStop1* and *TrailingStopThreshold1* will be used instead, as percentages in relation to the current price
-    TrailingStop: Distance at which the stop loss will be set, as a percentage in relation to the buy price
-    TrailingStopThreshold: How much the price has to increase above the previous stop loss for this to change, as a percentage of the buy price
-    TrailingStop1: Distance at which the stop loss will be set, as a percentage in relation to current price
-    TrailingStopThreshold1: How much the price has to increase above the previous stop loss for this to change, as a percentage of the current price
-    DontSellBeforeMinutes: Prevents selling a token, even if  stop loss or take profit limits are touched for the first few minutes after buying a token. This can be used to prevent being taken out of the market by the initial volatility after a token is launched. Use at your own risk!
-    ForceSellAfterTime: Sells tokens after the amount of minutes indicated in the setting just below, no matter if any of the conditions above is fulfilled or not
-    ForceSellAfterMinutes: Amount of minutes as explained above
### Watched tokens
-    WatchTokensInterval: Indicates the interval, in seconds, at which the tokens in the watched list will be checked for sniping. Each time the interval elapses a new token from the list will be checked, starting again for the beginning once the list is exhausted
-    WatchedTokens: List of token addresses to watch for sniping, if this sniping method is enabled
### White/Black Listing
-    WhitelistedTokens: This list of token addresses can have two different but similar usages. If *OnlyBuyWhitelisted* is enabled, only the tokens in this list can be bought. If it's disabled, it skips any audits for these tokens (use with care!)
-    BlacklistedTokens: A list of token addresses to never buy
-    BlacklistedTokenSymbols: Tokens whose symbols contain these strings won't be bought. This can be use to filter out tokens with symbols that contain, for example, "doge", "elon", "musk", "shiba", etc, as these are usually scams, or "test" which usually are... well... that... tests :)
-    BlacklistedTokenNames: Same as above, but for the token names
-    ContractRedFlagStrings: If contract verification is enabled, this is the list of expressions that prevent buying a token if any of them is present in its source code
### Telegram Sniper
-    TelegramApiId: Telegram Account Id for the Telegram sniping method
-    TelegramApiHash: Telegram Account Hash for the Telegram sniping method
-    TelegramApiPhoneNo: Telegram Phone Number for the Telegram sniping method

*To obtain your Telegram API credentials follow the instructions here: [https://core.telegram.org/api/obtaining_api_id](https://core.telegram.org/api/obtaining_api_id). **Use at your own risk as whether Telegram allows this kind of use is a bit of a grey area and they could ban you forever from their service***

 -   TelegramListenerConfigurations: This a list of rules for sniping tokens from Telegram channels. For each rule you can set:
		- RuleName: A name for this rule, for your reference
		- ChannelName: The name of the channel to listen to for this rule. You must already be a member of the channel
		- RequiredText: All these words or expressions need to be present in a message for it to be parsed in search of a token address
		- ExcludeText: If any of these words or expressions is present in a message, it will be ignored and not used for snipping
		- AmountToSnipe: Telegram sniping doesn't use the default *AmountToSnipe*. You must set a separate amount here for each rule. This can be used, for example, to buy lower or higher amounts of tokens depending on your trust on the source

### Additional Settings
- CheckSeen: If enabled, it will check again a token that has been previously bought or discarded, if the sniper finds it again. It's recommended to leave it disabled
- BuySeen: If enabled, it allows buying a token again, even if it's been previously bought or discarded, if the sniper finds it again. It's recommended to leave it disabled
- DeadWallets: List of dead wallets used for the calculation of the percentage of the total supply as liquidity. It's recommended not to touch this

You can ignore the sections below, they're used to configure the log files

## Plans for the next releases
- Add support for other blockchains
- Mempool snipping
- Wallet mirroring
- Checking the mempool for the removal of liquidity of tokens hold to try to frontrun the operation and sell them before it's processed by the blockchain
- Explore arbitrage strategies (possibly using flash loans, etc)
- Open to suggestions

# Crossover services
Crossover services allows people to convert West tokens to other cryto's or from other crypto's to West Tokens.

RAIDA servers will have a special locker just for the West's conversion locker. Only the Treasure will be able to create and destroy tokens in this locker. The Treasury may also read the amount of coins in it. When the total coins are counted, the coins in the conversion account will need to be counted too. They will also need to be syncronized. While converting, coins will be taken out of this locker and placed into new lockers. Coins may also be taken out of user's lockers and placed in the conversion locker. 

# Commands

Command Code | Command | Link
---|---|---
? | Check Market Account | [Set Market Account](#check_market_account)
? | Set Conversion Fee | [Set Conversion Fee](#set_conversion_fee)
? | Convert From West | [Convert From West](#convert_from_west) 
? | Convert To West | [Convert To West](#convert_to_west)

# Set Conversion Fee
Everytime someone converst coins, a fee will be charged to the account of the West. This
fee is set by the Treasure. In the future, the fee may be either fixed or variable and may have a complicated algorithm. Each currency may have a different fee associated with it. That is way there will be bytes reserved in the request body. For now this command just sets a percentage of the coins conversted. 

Suppose the conversion fee is set to 3%. If 100 West are converted to Bitcoin, the fee will be 3 West. Suppose a person wants to convert 1 Bitcoin to 100 West with the same 3% fee. The fee will be 3 West. The amount of fees is always calculated based on the value of the West tokens involved.  

The request body needs to have a percentage to charge as a fee. This percentage is expressed in two bytes. The first byte is the whole part of the percentage and ranges from 0 to 99. The second byte is the fraction part of the percentage and is valued from 0.99 too. So the least percentage that can be charged is 0.0% and the most is 99.99%

Example Request:
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
$$ $$ // Conversion Fee (percentage)
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS //160 bytes reserved for future expansion. 
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS 
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS
RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS RS 
3E 3E //Not Encrypted
```

Example Response:

Response Status | Code
---|---
Success | 250
Percentage is out of bounds| ?? 
The body is empty| ?? 
```
 //Empty
3E 3E //Not Encrypted
```

# Convert From West
* The user must first put the coins that they want to sell into a locker.
* The client must check with the exchange rate web API and decide when to convert. 
* The client will tell the RAIDA what price it would like to buy at (based on the exchange rate)
* The RAIDA will then check the price with the same API, if the prices are within 1% of each other, the transaction shall be made. 
* The Administrators must have a market maker account with enough currency in it to handle the transaction
* The RAIDA will tell the Crypto Wallet (Multisig) to transfer the money to the account.
* First come first serve with the market account to keep it simple. If the market account runs out conversion stops.
* There must be some way to roll back the transaction if they crypto part fails. 

The user sends:
* The cryptocurrency-code that they want to convert into (See table of crypto currencies)
* The converstion cost they expect to pay
* Their wallet address
* The receipt number (not required )
* The memo (up to 1300 bytes allows for 20KB on 16 RAIDAs)

Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK //Locker key with CloudCoins
CD CD CD //currency code to convert to
$$ $$ $$ //Converstion cost expected
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD //Target cryptocurrency address
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID  //The receipt ID 
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME
...
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME //up to 1300 bytes of memo data. Optional.
3E 3E //Not Encrypted
```


Response Status | Code
---|---
Success | 250
Not enough market coins| ??
Price differnt more than 1% | ??
Address did not fit allowable format | ??
Memo too long | ??
Receipt ID in use| ?? 
Locker not found| ?? 

```
TK TK TK TK TK TK TK TK TK TK TK TK TK TK TK TK // Pickup Ticket

 //Empty 
3E 3E 

```


# Convert To West
* The client must check with the exchange rate web API and decide when to convert. 
* The client will not get to specify the price due to the slowness of crypto transactions.
* The RAIDA will then check the price with the same API, if the prices are within 1% of each other, the transaction shall be made. 
* The Administrators must have a market maker account with enough West tokens in it to handle the transaction.
* First come first serve with the market account to keep it simple. If the market account runs out conversion stops.
* The RAIDA will 

The user sends:
* The cryptocurrency-code they send coins to. 
* The transaction numbers.
* Their crypto wallet address that coins were sent from.
* The receipt number (not required )
* The memo (up to 1300 bytes allows for 20KB on 16 RAIDAs)

Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK //Locker key that they want the coins to be put into.
CD CD CD //currency code to convert to
$$ $$ $$ //Converstion cost expected
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD //Target cryptocurrency address
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID  //The receipt ID 
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME
...
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME //up to 1300 bytes of memo data. Optional.
3E 3E //Not Encrypted
```


Response Status | Code
---|---
Success | 250
Not enough market coins| ??
Price differnt more than 1% | ??
Address did not fit allowable format | ??
Memo too long | ??
Receipt ID in use| ?? 
Locker not found| ?? 

```
TK TK TK TK TK TK TK TK TK TK TK TK TK TK TK TK // Pickup Ticket

 //Empty 
3E 3E 

```


## Check Market Account
This command lets the Treasure see how many WEST Tokens are in the WEST Token's iquidity account. The Treasure must put coins in these accounts so then when people want to sell, they don't have to wait for a buyer or vis versa. Clients cannot sell more tokens then are in the liquidity pool. The admin of the system can set a "fee" for conversion. The fee will need to be set by someone with a treasury key. The client should not try to sell more than is in the liquidity account. Otherwise they will simply get an error "Not enough Coins".

Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
CD CD CD //currency code to convert to
3E 3E //Not Encrypted
```
Response 
Status code "ok" or successful
The amount that is in the account. This amount will show no more than 16.7 Million even if there are more coins then that.
```
$$ $$ $$ 
3E 3E 
```

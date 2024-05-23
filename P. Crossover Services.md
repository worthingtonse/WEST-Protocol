# Crossover services
Crossover services allows people to convert West tokens to other cryto's or from other crypto's to West Tokens.

RAIDA servers will have a special locker just for the West's conversion locker. Only the Treasure will be able to create and destroy tokens in this locker. The Treasury may also read the amount of coins in it. When the total coins are counted, the coins in the conversion account will need to be counted too. They will also need to be syncronized. While converting, coins will be taken out of this locker and placed into new lockers. Coins may also be taken out of user's lockers and placed in the conversion locker. 

## Commands

Command Code | Command | Link
---|---|---
? | Convert From West | [Convert From West](#convert_from_west) 
? | Convert To West | [Convert To West](#convert_to_west)
? | Check Market Account | [Check Market Account](#check_market_account)
? | Set Conversion Fee | [Set Conversion Fee](#set_conversion_fee)

## Convert From West
* The user must first put the coins that they want to sell into a locker.
* The client must check with the price API and decide when they would like purchase. 
* The client will tell the RAIDA what price it would like to buy at.
* The RAIDA will then check the price with the API, it the prices are within 1% of each other, the transaction shall be made. 
* The Administrators must have a market maker account with enough currency in it to handle the transaction
* I think it will become a first come first serve with the market account to keep it simple. If the market account runs out, people will know by using the "Check Market Account" command. 

The user sends:
* The cryptocurrency-code that they want to convert into (See table of crypto currencies)
* The converstion cost they expect to pay
* Their wallet address
* The receipt number (not required)
* The memo (up to 1300 bytes)


Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
CD CD CD //currency code to convert to
$$ $$ $$ //Converstion cost expected
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD //Target address
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID  //The receipt ID 
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME
...
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME //up to 1300 bytes of memo data. Optional. Total of 32K accross the RAIDA. 
3E 3E //Not Encrypted
```

Response 
Status code "ok", "Not enough Market making", Price differnt than 1%, Address did not fit allowable format, memo too long, receipt ID in use. 
```
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

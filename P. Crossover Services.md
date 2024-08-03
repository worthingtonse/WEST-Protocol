![Reserve Locker](zips/convert.png)
# Crossover services
Crossover services allows people to convert West tokens to other cryptos or from other cryptos to West Tokens.

RAIDA servers will have a special locker just for the West's conversion locker. Only the Treasure will be able to create and destroy tokens in this locker. The Treasury may also read the amount of coins in it. When the total coins are counted, the coins in the conversion account will need to be counted too. They will also need to be syncronized. While converting, coins will be taken out of this locker and placed into new lockers. Coins may also be taken out of user's lockers and placed in the conversion locker. 

# Commands

Command Code | Type | Command | Link
---|---|---|---
110 | RAIDAX  | [Reserve Locker For Receiving West](#reserve-locker-for-receiving-west) | Used first when you want to convert Bitcoin to West. Prepares a locker for the user's West Tokens. 
111 | RAIDAX | [Check Depository For Deposit](#check-depository-for-deposit) | User sends Crypto to RAIDA's depository wallet. RAIDA puts West tokens into the clients locker. Calls service #115 on PythonRAIDA
112 | RAIDAX | [Withdraw from Depository](#withdraw-from-depository) | After user puts West into a locker, the user sends the locker code to the RAIDA and the RAIDA sends that user crypto. Calls service #113 on PythonRAIDA
113 | PythonRAIDA | [TriggerTransaction](#trigger-transaction) | Requests a RAIDA server to send a crypto transaction to a remote wallet
114 | PythonRAIDA, RAIDAX | [GetRate](#get-rate) | Gets exchange rate for the client (This has been moved to the RAIDAX Proxy on the Treasurer's Workstation). RAIDAX calls service #114 on PythonRAIDA
115 | PythonRAIDA | [WatchForTransaction](#watch-for-transaction) | Checks if a transaction is confirmed on the Blockchain(This has been moved to the RAIDAX Proxy on the Treasurer's Workstation).

## REST Services Running on an Inforation Server
Some services do not need Data Supremacy and are located on tradition servers. They are acccessed using cutomary REST calls. These REST services are provided by a thrid part. The API can be found at [Postman](https://documenter.getpostman.com/view/16362858/UVXokDS6)

Method | Name | Description
---|---|---
GET | Get supported currencies | Returns a list of currencies that can be swapped
GET | Get rate | Shows the exchange rate the user must take. 
GET | Get transaction status | Tells the user what is happening with their transaction
GET | Get Transactions list | Shows a list of transactions that the user has performed
GET | Validate address | Allows the user to make sure the address is good

![Reserve Locker](zips/lockers.png)
# Reserve Locker For Converting Crypto To West
The client calls this to tell the RAIDA that it will soon receive cryptocurrency so get a locker ready to put their West Tokens in. This service can be called after the crypto currency is sent to the RAIDA's wallet but that is risky. Someone else could claim that they sent the crypto and steal the CloudCoins. Therefor, the Reserve Locker command should be called before the client transfers crypto from their wallet to the RAIDA's wallet.

After receiving the Reserve Locker command the RAIDA will:
1. Create an empty locker with the locker code provided by the cleint.
2. Associates the locker code with the sender's crypto address.
3. Check the web API to see how much time that currencies are taking to complete. 
4. Set a timer based on the currency code or API.
5. If the times goes off before the locker has been used, the locker is deleted.
6. The locker is deleted after it West Tokens have been removed.

Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK //Locker key that they RAIDA should create. 
CD CD CD //currency code to look out for.
$$ $$ $$ $$ $$ $$ $$ $$ // 8 Bytes for the amount of crypto that should
AS // Address size
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD // 32-35 bytes. Sender's cryptocurrency address
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD
AD AD AD
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID  //The receipt ID the client would like to use
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME //Memo should contain identifyable tansaction information 
...
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME //Helps user recover coins if something goes wrong.
3E 3E //Not Encrypted
```
![Convert](zips/exchange.png)









# Check Depository For Deposit
* The client must check with the exchange rate web API and decide when to convert. 
* The client will not get to specify the price due to the slowness of crypto transactions.
* The RAIDA will then check the price with the same API, if the prices are within 1% of each other, the transaction shall be made. 
* The Administrators must have a market maker account with enough West tokens in it to handle the transaction.
* First come first serve with the market account to keep it simple. If the market account runs out conversion stops.
* The RAIDA will make a call to a block explore for the transaction supplied by the client
* RAIDA checks the data of the transactoin. If it is too old it is rejected.
* RAIDA checks the list of recetn transactions and makes sure tokens have not been isssued yet.
* RAIDA puts coins from the market locker into the client's locker. 

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
CD CD CD // Currency Code that was sent
TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR // Transaction ID
TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR 
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID  //The receipt ID 
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME
...
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME //up to 1300 bytes of memo data. Optional.
3E 3E //Not Encrypted
```


Response Status | Code
---|---
Success | 250
Not enough market coins| 182
Price differnt more than 1% | 181
Address did not fit allowable format | 198



```
 //Empty 
3E 3E 
```
























# Withdraw from Depository
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
$$ $$ $$ $$ $$ $$ $$ $$ $$//Converstion cost expected
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
Not enough market coins| 182
Price differnt more than 1% | 181
Address did not fit allowable format | 198

```
TK TK TK TK TK TK TK TK TK TK TK TK TK TK TK TK // Pickup Ticket

 //Empty 
3E 3E 

```

# Trigger Transaction

Asks a RAIDA server to send some crypto coins to a remote wallet
The Server must download the key using the GetKey method from RAIDA servers
This key is used to contact the remote API

```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
CD CD CD // Cryptocurrency ticker
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID // Receipt ID
KV KV KV KV KV KV KV KV KV KV KV KV KV KV KV KV
KV KV KV KV KV KV KV KV KV KV KV KV KV KV KV KV // Key Value
$$ $$ $$ $$ $$ $$ $$ $$ // 8 Bytes for the amount 
AS // Adress size (from 32 to 35)
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD //Target cryptocurrency address
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD
AD AD AD
ME ME ME ... ME // Memo up to 1500 bytes
3E 3E //Not Encrypted
```

Response Status | Code
---|---
Success | 250
Failure | 251


# Get Rate

Requests the exchange rate between cryptocurrency and US dollar
A remote API is used to get this rate. 

```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
CD CD CD // Cryptocurrency ticker
3E 3E //Not Encrypted
```

Response 
```
$$ $$ $$ $$ $$ $$ $$ $$ // 8 bytes exchange rate for 1 USD
3E 3E 
```

Response Status | Code
---|---
Success | 250
Failure | 251
BackendError | 193 // Error in External API
KeyBuild | 192 // Failed to build key



# Watch For Transaction
* Contacts a blockchain explorer to check on the transaction status (whether it is completed)
* If the transaction has at least 1 confirmation the "SUCCESS" code will be returned
  

Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
CD CD CD // Cryptocurrency ticker
$$ $$ $$ $$ $$ $$ $$ $$ $$ // Amount expected
LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK LK // Locker code
CF // Number of confirmations
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID // ReceiptID
TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR // Transaction ID
TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR TR
ME ME ME ... ME // Memo up to 1500 bytes
3E 3E //Not Encrypted
```

Response
```
//Empty
$$ $$ $$ $$ $$ $$ $$ $$ $$ // Amount sent
$$ $$ $$ $$ $$ $$ $$ $$ $$ // Fees
3E 3E 

```

Response Status | Code
---|---
Success | 250 // Transaction confirmed
Seen | 183 // Transaction seen, but not confirmed yet
DoesNotExist | 191 // Transaction doesn't exist
Empty | 190 // Transaction doesn't send anything to our wallet
BackendError | 193 // Error in External API



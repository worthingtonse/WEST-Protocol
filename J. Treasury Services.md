# Treasury Services (Command  Group: 3)

Used by the Treasury to create and destroy tokens. Also used to manage multi-chain liqudity.

Command Code | Service | Description
--- | --- | :---: 
120 | [Get Available SNs](#get-available-sns) | Tells the Treasury what serial numbers it can use to create tokens. 
130 | [Create Tokens](#create-tokens) | Orders that tokens be created
? | [Create Tokens in Market Locker](#create_tokens_in_market_locker) | Puts tokens in locker key: 01 23 45 67 89 AB CD EF 01 23 45 67 89 AB CD EF
140 | [Delete Tokens](#delete-tokens) | Orders tokens to be destroyed. 
150 | [Release Lock](#release-lock)|  Tells RAIDA to release lock on reserved SNs|
160 | [Get All SNs](#get-all-sns)|  Returns all the serial numbers that the RAIDA has minted|
? | [Set Conversion Fee](#set_conversion_fee) | 

## Code meanings
Code | Meaning | Sample
---|---|---
RD | Random | 6
DN | Denomination | This denomination will not have a random first four bits. Hex between 0x00 and 0x0F 
SN | Serial Number | 16777215
AN | Authenticity Number | F5DD153926DA42E8A062A915AD763DF0
AU | Authorization Number | 03C7C0ACE395d80182DB07AE2C30F034
CH | Challenge | A random number that they RAIDA must 
NR | Number of SN ranges | 10
NS | Number of single SN | 5
SI | Session ID | FF01AABB
RS | Range Start | A four byte serial number that is the first available SN in a range
RE | Range End | A four byte serial number that is the last available SN in a range
PG | PAN Generator | The Proposed Authenticy Number Generator is a seed the the RAIDA will use to create authenticy numbers. This is explained below.



# Get Available SNs
This method is the first part of the two-step token creation process. The client sends four-byte SessionID and the RAIDA server locks serial numbers
using the Session ID. After that, the next step (Create Tokens) downloads the tokens using the same Session ID.

This service returns an array of token serial numbers that are available to be used in the creation command. The returned item can be either an individual serial number or a range.

The ranges come first prepended by the number of them. The individual serial numbers come after them.


Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
SI SI SI SI
DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN //One byte for each denomination. Request for 0-255 tokens of that denomination. 
3E 3E //Not Encrypted
```

Response Status | Code
---|---
Success | 250
Failure | 251


Sample Returns for three denominations. The first has three ranges and two singles. The second has 1 range and 1 single. The third has 2 ranges and 3 singles. 
```
DN    //First denomination code (See denomination codes in the Header format)
NR NS  // NR (Number of ranges 0 to 255). NS (Number of single serial numbers 0 to 255). 
RS RS RS RS  RE RE RE RE // RS (starting serial number in range) RE (last serial number in range, inclusive)
RS RS RS RS  RE RE RE RE
RS RS RS RS  RE RE RE RE  
SN SN SN SN // first single serial number
SN SN SN SN
DN     //Second denomination
NR NS 
RS RS RS RS  RE RE RE RE 
SN SN SN SN
DN  // Third denomination
NR NS 
RS RS RS RS  RE RE RE RE
RS RS RS RS  RE RE RE RE  
SN SN SN SN
SN SN SN SN
SN SN SN SN
3E 3E  //Not Encrypted
```

# Release Lock 
- WARNING: This service has been implmented but is being considerd for deletion.

The service deletes coins that were previously reserved by Get Available SNs call.  
Example Request Body with four coins:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
SI SI SI SI
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
3E 3E
```


Response Status | Code
---|---
Success | 250


# Get All SNs
The service returns all serial number for a denomination (or all denominations).
The returned byte array is packed into a bitmap sequence where "1" stands for "minted", "0" is "available"


Example Request Body: 
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  
3E 3E  //Not Encrypted
```


Response Status | Code
---|---
Success | 250


```
11110000 111111000
```
sn 0 (lowest bit of the first byte) available
sn 1 available
sn 2 available
sn 3 available
sn 4 minted
sn 5 minted
sn 6 minted
sn 7 minted
sn 8 (lowest bit of the second byte) available
sn 9 available
sn 10 available
sn 11 minted
sn 12 minted
sn 13 minted
sn 14 minted
sn 15 minted

Response Body 
```
DN
NR NR NR NR
SB SB SB SB SB ...
...
E3 E3
```
DN - denomination (0x99 for all of them)
NR - number of returned bytes for a denomination
SB - serial number byte (one bit per serial number)



# Create tokens

The service creates tokens that were previously reserved by "Get Available SNs" call. Create tokens must be called within a few seconds after
the initial Get Available SNs call before the session expires. The same Session ID must be used for both services.

Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
SI SI SI SI
PG PG PG PG PG PG PG PG PG PG PG PG PG PG PG PG // see instructions below
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
3E 3E //Not Encrypted
```


Response Status | Code
---|---
Success | 250


Response Body Will be empty except for status. 
```
E3 E3 //Not Encrypted
```


The PAN is determined by concatinating the RAIDA ID, Serial Number and the PG. Like this

```diff
- WARNING: Client should send a different PG to each RAIDA otherwise the coin will be stolen by any other RAIDA admin. 
```

1. The Client will generate a 16 byte random seed.
2. The Client will send this seed to the RAIDA. This seed is called a "PAN Generator".
3. The Client and the RAIDA will use an algorithm to figure out what the Authenticity numbers will be for each serial number that fixes. 
4. The Autheticty numbers for each serial number will be calculated by concatenating the RAIDA Number (String), the Coin's Serial Number (string) and the Seed that will be (converted into a 32 character string) together. 

```
1 + 1632435 + 9195C7A60CF0471BBE9B66144F5681C5 and then running an MD5 hash agains that.
```
Then the concatenated string is: 
```
PAN = MD5( "116324359195C7A60CF0471BBE9B66144F5681C5"); 
```
So the AN will be:
```
fd1495be732ab2f6959559f3041c72c0
```



# Create Tokens In Market Locker
This is exactly like the Create Tokens command but with a different commmand number. 

The PG is not needed and will be ignored. Random numbers should be put there instead. 

The difference on the RAIDA is that these coins will be put in a system locker with a fixed Key ID. The key ID is: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
No locker service can call this special locker key and this cannot be used by the public as their locker. 


Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
SI SI SI SI
RD RD RD RD RD RD RD RD RD RD RD RD RD RD RD RD // Random Numbers
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
3E 3E //Not Encrypted
```

Response Status | Code
---|---
Success | 250
Coins Not Available | ??

Response Body Will be empty except for status. 
```
E3 E3 //Not Encrypted
```


# Delete tokens
The sender sends authentic tokens. Those tokens are then deleted from the system and the Months From Start Byte is changed to zero. 

Example Request Body with four tokens: 
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
3E 3E  //Not Encrypted
```

Response Status | Code
---|---
All Pass | 241
All Fail | 242
Mixed | 243

Response Body 
```
E3 E3 //Not Encrypted
```


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


## Show Reserves
This command lets the Treasure see how many WEST Tokens are in the WEST Token's Liquidity Accounts. The Treasure must put coins in these accounts so then when people want to sell, they don't have to wait for a buyer or vis versa. Clients cannot sell more tokens then are in the liquidity pool. The admin of the system can set a "fee" for conversion. The fee will need to be set by someone with a treasury key. The client should not try to swap more than is in the liquidity account. Otherwise they will simply get an error "Not enough Coins".

Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
CD CD CD //currency code for future use. Phase 1 returns all coins
3E 3E //Not Encrypted
```
Response 
Status code "ok" or successful
The amount that is in the account. This amount will show no more than 16.7 Million even if there are more coins then that. Any amount returned that equals 16,777,215 should be shown as "Greater than 16,777,215"
```
$$ $$ $$ // Index 0 shows the amount for coin code 0 (See coin codes. This one is the West Token)
$$ $$ $$ // Index 1 shows the amount for coin code 1 (See coin codes. This one is CloudCoin)
$$ $$ $$ // Index 1 shows the amount for coin code 1 (See coin codes. This one is Bitcoin)
.. .. .. Length of response will vary based on the number of coins
$$ $$ $$
$$ $$ $$ // Index n. 
3E 3E // Unencrypted
```


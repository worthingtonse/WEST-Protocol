# Admin Services (Command  Group: 3)

Used by the RAIDA Authority to create and destroy tokens.

Command Code | Service | Description
--- | --- | :---: 
120 | [Get Available SNs](#get-available-sns) | Tells the Admin what serial numbers it can use to create tokens. 
130 | [Create tokens](#create-tokens) | Orders that tokens be created
140 | [Delete tokens](#delete-tokens) | Orders tokens to be destroyed. 

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



# Get Available SNs
This method is the first part of the two-step token creation process. The client sends four-byte SessionID and the RAIDA server locks serial numbers
using the Session ID. After that, the next step (Create Tokens) downloads the tokens using the same Session ID.

This service returns an array of token serial numbers that are available to be used in the creation command. The returned item can be either an individual serial number or a range.

The ranges come first prepended by the number of them. The individual serial numbers come after them.


Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
SI SI SI SI
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU
DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN //One byte for each denomination. Request for 0-255 tokens of that denomination. 
3E 3E //Not Encrypted
```


Response Status | Code
---|---
Success | 250
Failure | 251


Returns: Three denominations. The first has three ranges and two singles. The second has 1 range and 1 single. The third has 2 ranges and 3 singles. 
```
DN    
NR NS 
RS RS RS RS  RE RE RE RE 
RS RS RS RS  RE RE RE RE
RS RS RS RS  RE RE RE RE  
SN SN SN SN
SN SN SN SN
DN
NR NS 
RS RS RS RS  RE RE RE RE 
SN SN SN SN
DN
NR NS 
RS RS RS RS  RE RE RE RE
RS RS RS RS  RE RE RE RE  
SN SN SN SN
SN SN SN SN
SN SN SN SN
3E 3E  //Not Encrypted
```

# Create tokens

The service creates tokens that were previously reserved by "Get Available SNs" call. Create tokens must be called within a few seconds after
the initial Get Available SNs call before the session expires. The same Session ID must be used for both services.

Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
SI SI SI SI
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
DN SN SN SN SN
3E 3E //Not Encrypted
```


Response Status | Code
---|---
Success | 250


Response Body 
```
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
E3 E3 //Not Encrypted
```


# Delete tokens
The sender sends authentic tokens. Those tokens are then deleted from the system and the Months From Start Byte is changed to zero. 

Example Request Body with four tokens: 
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU
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



![Request Headers](zips/request.png)
# WEST RAIDA Request Headers V.1.0.0
This prodocol allows clients to send requests to the WEST RAIDA. 
All requests made to the WEST RAIDA must have a 32 byte header.
The header is fixed length and will always be 32 bytes. 
If the header does not contain 32 bytes, then the RAIDA will not respond. 
The Request Header is not encrypted. 



Headers are made up of four groups of eight bytes. The byte groups are named "Routing", "Presentation", "Encryption" and "Nounce".
This header is designed to allow WEST RAIDAs to be extensible and perfome other functions besides token authentication. (extensible)[#extensible].

All multibyte fields are considered big-endian. For example, the two "ID" byte located at 0x04 and 0x05, 0x04 is MSB and 0x05 is LSB

## WEST RAIDA Protocol Header Byte Group View (32 Bytes Fixed):
```javascript
VR SP RI SH CG CM ID ID  // 8 Routing Bytes
VR AP AP CP TR AI RE RE  // 8 Presentation Bytes
EN DN SN SN SN SN BL BL  // 8 Encryption Bytes
NO NO NO NO NO NO EC EC  // 8 Nounce Bytes
```



## WEST RAIDA Protocol Header Offset View:
Offset(h) |`0x00` | `0x01` | `0x02` | `0x03` |`0x04` |`0x05`|`0x06`|`0x07`|`0x08`|`0x09`|`0x0A`|`0x0B`|`0x0C`|`0x0D`|`0x0E`|`0x0F`
-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-
`0x00` | VR |SP |RI |SH| CG | CM |ID| ID| VR | AP| AP |CP | TR| AI| RE| RE 
`0x10` | EN | DN |SN |SN| SN | SN |BL| NO| NO| NO | NO| NO |NO | NO| EC| EC 


### Request Header Byte by Byte Code Meanings

Index (Decimal) | Group | Code | Name | Notes
---|---|---|---|---
0 | Routing | VR | Version  | Version of Routing Header Protocol. Should be 1 
1 | Routing | SP | Split ID | For future use in case the token is to be split. Otherwise zero. 
2 | Routing | RI | RAIDA ID  |  Detection Agents called RAIDA Servers. Value must be 0-24. 
3 | Routing | SH | Shard ID  | For future use in case the token is to be sharded. For now, zero. 
4 | Routing | CG | [Command Group](#command-groups) | High order byte for command number
5 | Routing | CM | [Command](#commands)  | Low Number byte for command number
6 | Routing | ID  |  WEST's RAIDA ID 0 | Allways zero for WEST
7 | Routing | ID  |  WEST's RAIDA ID | Always 1 for WEST
8 | Presentation | VR | Version of PLS | Should be 1
9  | Presentation | AP | Application 0| | Applications are generally numbered by the port they use
10 | Presentation | AP  | Application 1 |  0 means generic. 80 = HTTP, 25 =SMTP etc. 
11 | Presentation | CP | Compression | Future Use. 0 means none (Future Use)
12 | Presentation | TR | Translation | Future Use. 0 means none. Can be used to encode text-based protocols. 
13 | Presentation | AI  |AI Translation | Future Use. 0 means none. What type of AI should be used to translate.
14 | Presentation | RE |  Reserved | Future Use.Can be used if bytes are needed for some unforseen purpose
15 | Presentation | RE |  Reserved | Future Use.
16 | Encryption | EN  | [Encryption](#encryption) Type  |  0x00 means no encryption. See encryption codes table.
17 | Encryption | DE | [Denomination](#denominations) | Denomination of the token used to encrypt the request body.
18 | Encryption | SN | Encryption token SN 0| Serial Number of the token used to encrypt the body. HOB
19 | Encryption | SN |  Encryption token SN 1 | 2nd Highest Order Byte
20 | Encryption | SN |  Encryption token SN 2 |  
21 | Encryption | SN |  Encryption token SN 3| Lowest Order Byte
22 | Encryption | BL | Body Length | Length in bytes of the entire body including the last 2 terminating bytes. 
23 | Encryption | BL | Body Length| LOB. if more than 65K bytes are sent, then these two will be FF FF and bytes 24, 25, 26 and 27 will tokentain the length. 
24 | Nonce | NO |  Nonce 0 | The nonce used in the encryption.
25 | Nonce | NO |  Nonce 1 | 
26 | Nonce | NO |  Nonce 2|
27 | Nonce | NO |  Nonce 3| Nouce bytes 0-7 should be random and never used twice. 
28 | Nonce | NO |  Nonce 4 |
29 | Nonce | NO |  Nonce 5 |
30 | Nonce | NO |  Nonce 6 / Echo 0 | Serves two purposes. These bytes are always echoed back to the client.
31 | Nonce | NO |  Nonce 7 / Echo 1 | 

* Nounce can do two jobs. Bytes 30, 31 are used as an Echo also.
* If EN (byte 16) is zero (no encryption) then bytes 17-31 may take any values. In 'no encryption' mode, these values will be ignored except for the two echo bytes. 
* The nounce is also a challenge. The RKE (RAIDA Key Exchange) server must decrypt this and place it in the response if RKE is enabled. 


## COMMAND GROUPS
Code | Name | Description
--- | --- | ---
0 | Status | Status, Errors that have nothing to do with commands but usually with headers
1 | Authentication | Services that authenticate tokens.
2 | Healing | Services that make all RAIDA agree on the authenticity of tokens.
3 | Admin | Services that add or subtrack tokens from the WEST RAIDA.
8 | Locker Services | Services that place and retrieve tokens from "RAIDA Lockers."
9 | Change | Breaks 100s into 10s or joins 10s to make 100s. Works with all denominations.


## COMMAND CODES

### 0. Status Commands
Code (Decimal) | Command | Description
--- | --- | --- 
 00 |  Echo | Sends a request for a response.
 01 | Version | Returns the version of the RAIDA Software
 02 | ShowStats | Shows information about requests to the RAIDA
 03 | Audit | Shows how many coins are in the RAIDA

### 1. Authentication Commands
Code (Decimal) | Command | Description
--- | --- | --- 
 10 |  Detect | Compares authenticity numbers but does not change them.
 11 |  Detect SUM | Compares authenticity numbers by adding them all together. Does not change them.
 20 | P'Own | Password Own. Changes ANs (Authenticity Numbers) with PANs (Proposed Authenticity Numbers). 
 21 | P'Own Check Sum | Checks the sums of all ANs sent at once and changes them if good.
 
 ### 2. Healing Commands
Code (Decimal) | Command | Description
--- | --- | --- 
 40 |Get Ticket | Returns proof that the tokens are good. 
 50 | Validate Ticket | Checks to see if a ticket is valid. 
 60 |Find|  Checks last two ANs to see if there is a match. 
 80 | Fix | Accepts a bunch of tickets to ensure they are good.

### 3. Admin Commands
Code (Decimal) | Command | Description
--- | --- | --- 
 120 | Get Available SNs| Shows available serial numbers. 
 130 | Create tokens| Adds more tokens to the total tokens on the RAIDA.
 140 | Delete tokens| Removes tokens from the RAIDA.
 160 | Get All SNs | Allows RAIDA to syncronize their serial numbers. 

 ### 4. Key Exchange 
 Code (Decimal) | Command | Description
--- | --- | --- 
 44 |Encrypt Ticket | Returns an encrypted key part that can be used as a shared secret. 
 45 | Fix Encryption | Accepts tickets from many RAIDA to created a shared secret from many "key parts". 

 ### 8. Locker Commands
Code (Decimal) | Command | Description
--- | --- | --- 
 82 | Put | Puts token in a locker that can be opened by a key.
 83 | Peek | Just returns information about what is in the locker such as how many tokens are there. 
 84 | Remove | Removes tokens from locker and destroys locker.

 ### 9. Change Commands
Code (Decimal) | Command | Description
--- | --- | --- 
 91 | Get Available SNs | Asks the RAIDA what SNs are available for use. 
 92 | Break | Breaks token into ten smaller tokens. 
 93 | Join | Joins smaller tokens into one larger token. 

## DENOMINATIONS
Except for ID Token and possible future extensions of this table decimal values should be treated as powers of ten.

Hex Code| Decimal Code | Denomination
--- | :---: | ---
`0xF8` | -8 | `.000 000 01` (1 Satoshi)
`0xF9` | -7 | `.000 000 1`
`0xFa` | -6 | `.000 001`
`0xFb` | -5 | `.000 01`
`0xFc` | -4 | `.000 1`
`0xFd` | -3 | `.001`
`0xFe` | -2 | `.01` (1 Cent token)
`0xFf` | -1 | `.1` (1 Dime token)
`0x00` | 0 | `1` (1 Whole token)
`0x01` | 1 | `10`
`0x02` | 2 | `100`
`0x03` | 3 | `1,000`
`0x04` | 4 | `10,000`
`0x05` | 5 | `100,000`
`0x06` | 6 | `1,000,000`
`0x07` | 7 | User Keys
`0x08` | 8 | Treasurer Keys
`0x09` | 9 | Account Admin Keys 
`0x0a` | 10 | RAIDA ID Keys
`0x0b` | 11 | KYC Keys


## ENCRYPTION
Code | Type | Description | Bytes after EN
---|---|-----|---
0 | No encryption | Clear Text | 5 ( All Zeros)
1 | 128 AES CRT | Shared Secret |  5 (1 DN + 4 SN)
2 | 128 AES CRT | Locker Key is used | This is used by the PEEK command if the user doesn not have any coins yet. See PEEK in Locker Services. 

# ENCRYPTION RULES All SERVICES

Rule | Description
---|---
 Request Headers | Not encrypted
 Request Bodies | Encrypted using the same encryption key as the Request header specifies. 
 "E3 E3" |  Terminating bytes at end of a request or a response not encrypted.

# RAIDA Healing Service

These services allow the RAIDA to heal when a token becomes counterfeit on one RAIDA and authentic on others. 


Command Code | Service 
--- | --- 
40 | [Get Ticket](#/get-ticket)
50 | 🔴[Validate Ticket](#validate-ticket)
60 | [Find](#find)
80 | [Fix](OFF_LEDGER.md#fix) 
44 | [Get Encryption Ticket](#post-an-part) 
45 | [Fix Encryption](#post-an-parts) 


# Get Ticket
The GET TICKET service is nearly identical to DETECT accept a Ticket is returned. 
A ticket is a 4-byte random number that the RAIDA gives to the caller as proof that the tokens are authentic. 
This ticket can be used by the client to prove its authenticity to other RAIDA. 
The Ticket can be claimed one time by each RAIDA server. After the ticket grows old it times out.

The client gets a one ticket from a RAIDA and can send that ticket to 24 other RAIDA. 

Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
3E 3E //Not Encryption
```

Response Status | Code
---|---
All Pass | 241
All Fail | 242
Mixed | 243



Example Response if all pass :
```hex
MT MT MT MT  //Master Ticket
3E 3E //Not Encryption
```
Example Response if all mixed :
```hex
🔴MS MS MS MS MS MT MT MT MT  //Master Ticket or 🔴MT MT MT MT MS MS MS MS MS  //Needs verification
3E 3E 
```


# VALIDATE TICKET
The Validate Ticket protocol allows the client to see if a ticket belongs to a serial number.
It is used to prove that a person owns a token without them having to show the token. 
This is useful if you are using WEST Tokens to authenticate a person or thing in your own application. 

EXAMPLE BODY OF REQUEST
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
🔴RI //Always 0
TK TK TK TK 
3E 3E //Not Encryption
```

EXAMPLE REPLY: Shows seven serial numbers returned. The number is divisable by 5 bytes. 
```
DN  SN SN SN SN 
DN  SN SN SN SN 
DN  SN SN SN SN 
DN  SN SN SN SN 
DN  SN SN SN SN 
DN  SN SN SN SN 
DN  SN SN SN SN 
3E 3E 
```


# FIND 

A token is in Limbo when the client makes a request to POWN but there is no response from the RAIDA. 
The client is uncertain if the RAIDA got the message and turned the AN to the PAN or if the RAIDA did not get the message and the AN is unchanged. 

To get the token out of Limbo the client sends a Find rquest and sends the AN and the PAN to the RAIDA and asks which one is it's AN. 
The RAIDA will tell the Client if the AN is authentic, the PAN is authentic or if neither is authentic. 

The Fix Lost service allows the caller to send both AN and PAN to see if they are authentic. 
The service then responds with the correct answer. If neither are correct, it will respond with a status of "Neither".  '0x00'= Neither. "0x01" = AN and "0x02"=PAN. 

Example with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
3E 3E //Not Encryption
```

Response Statuses

Code | Status | Description
---|---|---
208 | Find All Neither | Neither the ANs or the PANs on any of the tokens were authentic 
209 | Find All AN | The ANs of all of the tokens were authentic 
210 | Find All PAN | The PANs of all of the tokens were authentic
211 | Find mixed | The tokens had a mix of ANs good, PANs good and Neither good. 


Mixed Results Codes

Hex Code | Meaning
---|---
0x00 | Nether the AN or the PAN presented by the Client was authentic. 
0x01 | The AN is athentic and the RAIDA has it in its database.  
0x02 | The PAN is authentic and the RAIDA has the PAN as in its database. 

Response Body if Results are Mixed for four tokens: One byte per token
```
01 
01 
00 
02   
//1st & 2nd tokens the ANs matched. 3rd neither matched. 4th the PAN matched. 
```

# Fix

All 25 RAIDA should agree that a token is authentic.  
When all RAIDA are not in agreement about the authenticity of a token, we call the token "Fractured" or "Fract" for short. 
The Fix protocol allows for Fract tokens to become whole. 

The fix procol must look at two config files to determine which other RAIDA to trust and how many of the RAIDA need to be in agreement to switch the AN to the client's request.
All of the file's data are located in the file's name and the file is located in the Config folder

Example config file that tells this RAIDA can accept tickets from all 25 RAIDA and it needs 13 good tickets to turn the AN. 
```
trusted.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.1.13.config
```
This example file name shows that the RAIDA is to trust no other RAIDA except for RAIDA 0 and that it must have one good ticket. 
```
trusted.1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.1.config
```
This example show that it can get tickets from any RAIDA except 10 and that it needs 13 good tickets. 
```
trusted.1.1.1.1.1.1.1.1.1.1.0.1.1.1.1.1.1.1.1.1.1.1.1.1.1.13.config
```
Each RAIDA Trusts the majority of other RAIDA. The client can get 'Tickets' from RAIDA that think the token is authentic and give them to the fract raida. 
The RAIDA requires 13 of the 25 RAIDA to change its AN to the AN that the client specifies. 

The fract RAIDA will always receive 25 tickts although they will all be zeros unless the actaully contain a ticket. So the majority of the tickets are just place holders. 

The Fix protocl may recieve empty tickets with all "00000". In this case there is no ticket. 

### Client Steps
1. Client receves a "fail" from one or more of the RAIDA (up to 12).
2. Client requests tickets from 13 or more RAIDA that are good.
3. Client sends all the tickets to all the RAIDA that are fracked (recieved a fail).
4. RAIDA checks the tickets. If the tickets are good then the fracked RAIDA set the AN that the clients specifies.  


### RAIDA Steps
1. The fracted RAIDA recieves Tickets with the Serial Numbers of the tokens that are fract and the Proposed Authenticity Numbers (or Proposed Authenticty Number Generator)  that client wants to give them.
2. The fracted RAIDA sends the Tickets to all the trused RAIDA in parallel using the VALIDATE TICKE protocol. 
3. Trusted RAIDA respond with the serial numbers associated with they ticket.
7. Fract RAIDA compares the Serial Numbers returned by the Good RAIDA with the Serial Numbers specified by the Client. 
8. If there are 13 Good RAIDAs that agree with what the Serial numbers that the client sent, the Authenticty Numbers on the Fract RAIDA are changed. 
9. All Pass is returned if all the token were fixed. All fail if none of the tokens were fixed. Mixed if some of the tokens were fixed. 

### Fix Algorithm

Fix allows you to fix multiple tokens using tickets that it got from the Get_Ticket service. 
Each ticket can only be used one time per RAIDA. 
Each RAIDA can use the same ticket to fix. 
This allows the client to use all the tickets returned from a Get_Ticket request to fix all the fracked tokens on all the RAIDA without needed to go and get more tickets.  The Fix then calls the "Validate Ticket" service and sees what SNs are returned. 
If 13 or 25 RAIDA return the same SN, that SN's ANs are changed to the PAN supplied by the Client in 🔴Version 0 of the Fix command. 
🔴Version 1 requires the caller to supply a PAN Generator instead of PANs. 


Version 0 Example Request Body with four tokens:
```dif
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN SN SN SN SN 
DN SN SN SN SN 
DN SN SN SN SN 
DN SN SN SN SN 
PG PG PG PG PG PG PG PG PG PG PG PG PG PG PG PG  //PAN Generator that will be used to generate PANs.  
TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  //Tickts for RAIDA 0 through RAIDA 4. 
TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  //Tickts for RAIDA 5 through RAIDA 9. 
TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  //Tickts for RAIDA 10 through RAIDA 14. 
TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  //Tickts for RAIDA 15 through RAIDA 19. 
TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  TK TK TK TK  //Tickts for RAIDA 20 through RAIDA 24. 
3E 3E //Not Encryption
```
The PAN is determined by concatinating the RAIDA ID, Serial Number and the PG. Like this

```diff
- WARNING: Client should send a different PG to each RAIDA otherwise the token will be stolen by any other RAIDA admin. 
```

1. The Client will generate a 16 byte random seed.
2. The Client will send this seed to the RAIDA. This seed is called a "PAN Generator".
3. The Client and the RAIDA will use an algorithm to figure out what the Authenticity numbers will be for each serial number that fixes. 
4. The Autheticty numbers for each serial number will be calculated by putting together the RAIDA Number (one byte), the token's denomination (one byte) and serial Number (four bytes) and the PG (16 bytes) t. 

```
1 + 0xFE + 1632435 + 9195C7A60CF0471BBE9B66144F5681C5 and then running an MD5 hash agains that.
```
Then the concatenated string is: 
```
PAN = MD5( "01FE18E8B39195C7A60CF0471BBE9B66144F5681C5"); 
```
So the AN will be:
```
fd1495be732ab2f6959559f3041c72c0
```

Example With Actual Bytes for an attempt to fix RAIDA 12 for four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
00 00 01  
05 00 23   
05 00 24  
05 00 25  
13 0A B9 AD 18 5E 45 1C 8E F4 11 13 74 95 65 FB
00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  //NO Tickts for RAIDA 0 through RAIDA 4. 
8A F4 55 E7  00 00 00 00  A4 BB 4A BF  B6 5C 74 87  00 00 00 00  //Tickts for RAIDA 5, 7 and 8. RAIDA 6 and 9 are empty. 
00 00 00 00  08 4C 38 8B  00 00 00 00  00 00 00 00  00 00 00 00  //Tickts for RAIDA 11.  RAIDA 10,12, 13 and 14 are empty.  
00 00 00 00  75 E5 6A A4  00 00 00 00  56 3C 57 72  00 00 00 00  //Tickts for RAIDA 16 and 18. RAIDA 15,17 and 19 are empty.  
00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  //NO Tickts for RAIDA 20 through RAIDA 24. 
3E 3E //Not Encryption
```

Reponse body if all fixed | Reponse body if all failed to fix | Response body if mixed with fixed and fails:
---|---|---
No response body | No response body | MS 

## GET ENCRYPTION TICKET

The two services "Get Encryption TIcket" and "Fix Encryption" are used to exchange keys between clients who have shared secrets with RAIDA servers but not a specific
RAIDA server. This will establish a shared secret with a fracked RAIDA so fixes can be done. 

These services are only needed if there are no tokens that can be used for encryption between a RAIDA and client but the same token does work with at least two other RAIDA. 

Then the fix keys process can run. This process uses the two services in this "RAIDA Key Service" that are designed just for this purpose. 

The client uses the "Encrypt RAIDA Key" service and sends key parts to two "helper" RAIDA that are not fracked (They can be successfully encrypted). The helper RAIDA are told which RAIDA the key parts are for so they can encrypt the key parts using a common secret between the Helper RAIDA and the Fracked RAIDA. 

For example, if the key is fracked on RAIDA 5, the client would randomly choose a key 5000 through 5999 and ask the helper RAIDA to use that. If the key was fracked on RAIDA 23, the client would pick a number between 23000 and 23999.

The key parts are actually the AN of the fracked token. 

The client will recieve two encrypted key parts from the two different RAIDA. The client sent an 8 byte key but receives a 16 byte key in the response. 

The the client then uses these two key parts and calls the "Post RAIDA Key" service. The call to the Post RAIDA Keys is unencrypted. But the key parts embedded in the request are encrypted by the other RAIDAs so called "id keys". 

The Fracked RAIDA can then decrypt the key parts using the common secret between them and the helping RAIDA. The key is actually the AN of the key ID that they client provides and so the AN of that token is changed accordinly so now encryption will work with the token provided. 


Client wants to talk to RAIDA 11. However, RAIDA 11 lost all its data and the Client and RAIDA 11 have no shared secret.  RAIDA 5 is "Helper RAIDA 1" and RAIDA 12 is "Helper RAIDA 2". The Client has shared secrets with Helper RAIDA 1 and 2 (tokens). 

Step | Actor | Action | Notes
---|---|---|---
1 | Client | Gets the AN from the token that has failed the validation challenge. This is the AN that the Fracked RAIDA should have but does not. | This is a GUID
2 | Client | Chooses 2 random RAIDA (who do pass the validation challenge) to talk to | In this case Helper RAIDA 1 and Helper RAIDA 2 are chosen.
3 | Client | Client breaks the AN from the fracked token into 2 parts (Each 8 bytes long) | Parts are indexed as part 0 and part 1
4 | Client | Encrypts the request including the DN, SN and AN of the token on Fracked RAIDA to be fixed along with part 0 usings Helper RAIDA 1's key  | This is the AN for the token that we want to fix on Fracked RAIDA.
5 | Client | Encrypts part 1 using Helper RAIDA 2's key | Again, We will use the encrypt DN SN SN SN SN of the token we will fix on Fracked RAIDA.
6 | Client | Calls the [Encrypt Key](RAIDA%20Key%20Tickets.md#post-raida-key) service on Helper RAIDA 1 and Helper RAIDA 2 | Tells them to encrypt using Fracked RAIDA's key
7 | Helper RAIDA 1 and 2 | They unencrypt the request using Client's shared secret | This is the token in the request header. We want to fix this on Fracked RAIDA.  
8 | Helper RAIDA 1 and 2 | Add padding and encrypt the part using Fracked RAIDA's key | Five bytes are from the token in the request header's encryption part. 
9 | Helper RAIDA 1 and 2 | Encrypt the response using Client's key and return to Client | Now Client has unreadable "tickets"
10 | Client | Does not encrypt the body of the [Post Key](RAIDA%20Key%20Services.md#post-raida-key) request to Fracked RAIDA | Denomination and Serial Numbers are clear text
11 | Fracked RAIDA | Fracked RAIDA decrypts the messages using Helper RAIDA 1's and Helper RAIDA 2's Key | Must already have shared secrets with other RAIDA
12 | Fracked RAIDA | Puts the key parts together in the order the were received | First keypart is part 0. Second is part 1
13 | Fracked RAIDA | Fracked RAIDA puts this key into the AN of the token specified | Now Client and the fracked RAIDA have at least one shared secret
14 | Fracked RAIDA | Fracked RAIDA uses the key to encrypt the response | Responds to Client
15 | Client | Client decrypts the response and checks the challenge | 
16 | Client | If the challenge is good, Alice changes the AN on the token she specifies to the key  | Not neccessar if the AN has not changed. Now Client has a common secret with the fracked RAIDA
17 | Client | Client can now make any request she likes to Bob using this token | Key exchange done
18 🔴| Fracked RAIDA | For additional security, the Fracked RAIDA should tell its token Manager to pown the key that was used in the encryption. 


Note: Clients know that the each RAIDA has 🔴 1000 tokens on their 0 network and the client can pick one at random. 
Sample Request for Get Encryption Ticket:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH  //Challenge
DN SN SN SN SN //The ID of who the key is for. // The Denomination, The Serial Number of the Bob's encryption token (1-25,999) depending on Bob's RAIDA ID. 
KY KY KY KY KY KY KY KY //Eight byte key part. 
DN SN SN SN SN //The serial number of the token that will be used to create a shared secret between Alice and Bob (Fracked token on Bob)
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN //The AN of the token used to create a shared secret between Alica and Bob (Fracked token on Bob)
E3 E3  //Not Encrypted

```

The RKE will take 8 byte key parts and then add 5 bytes of the token to be fixed (DN SN SN SN SN), 2 random bytes to the end. 
Then it adds one know byte to the end: 0xFF. 
This creates a 16 byte block that can be encrypted effectivly and padds it as needed. 
Then, the RKE server will use the encryption key that the client specified to encrypt the 16 bytes. 
The RKE returns the encrypted 16 bytes to the user.

The nonce for encryption is the same as the nonce sent in the request header.

Response Status Codes
Code | Status | Details
---|---|---
0 | Fail | Failed but no details provided
1| Success | Key ticket returned successfuly
11 | Offline | Service temporarilty offline 

Sample Response Body
```
D798010F14C64102AC3EB6D728166054
E3 E3 //Not Encrypted
```

## FIX ENCRYPTION
The "Post Key" service recieves a group of tickets encrypted by other RAIDAs. It uses its Authenticity Numbers of its own ID keys to decrypt them. Then it assembles the keys that it is able to encrypt a response to the caller. It checks that each key ends with a "0xFF"and removes the last eight bytes that the client does not know about. It Ignores keys it is unable to encrypt and then respond to the caller with:

* Which key parts it used to create a master key.

* 🔴A SHA-256 hash of the key.

* Seems the Nonce in the header should be the same as the ones used in the "Encrypt Key" requests.

Sample Request for Fix Encryption. Note: This is all unencrypted:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH  //Challenge
DN SN SN SN SN //The serial number of the fracked token. 
CO CO SP DA SH DN SN SN SN SN KY KY KY KY KY KY KY KY KY KY KY KY KY KY KY KY //token to encrypt and key part. 
CO CO SP DA SH DN SN SN SN SN KY KY KY KY KY KY KY KY KY KY KY KY KY KY KY KY //token to encrypt and key part.  
.....
CO CO SP DA SH DN SN SN SN SN KY KY KY KY KY KY KY KY KY KY KY KY KY KY KY KY //token to encrypt and key part. 
E3 E3  //Not Encrypted
```

Meaning of Codes:
Code | Meaning
---|---
CH | Challenge
CO | token ID
SP | Split ID (Zero if no splits)
DA | Detection Agent (RAIDA ID)
SH | Shard ID
DN | Denomination 
SN | Serial Number
KY | Key
E3 | Ending bytes


The service will then decypt the keys using the keys that it owns. 
It looks for the 0xFF at the end to make sure the token is whole and true. 
It removes the token DN and SN SN SN SN bytes and make sure they are the same for both key parts. 

Then it responds to the client with a hash of the key it has created. 

RAIDA Changes the AN in its ANs table for the token specified in the key parts. 

Sample Respons:
```hex
HS HS HS HS HS HS HS HS HS HS HS HS HS HS HS HS //SHA-256 hash of the Master key or is it SHA-128?
KA //Key accepted status. Either 0x00 for not accepted or 0x01 for accepted. 
KA
---
KA //one for every key the client proposed
E3 E3  //Not Encrypted
```

# Authentication Services

Theses services help people to know if tokens are authentic and to allow them to change ownership.

Command Code | Service  
--- | --- 
10 | [Detect](#detect) 
11 | [Detect Sum](#detect-sum)
20 | [Pown](#pown)|
21 | [Pown Sum](pown-sum)| 

## Code meanings
Code | Meaning | Sample in HEX | Description
---|---|---|---
RD | Random | 6 | A random byte
DN | Denomination | 1 | A denomination code (See Request Header Denominations)
SN | Serial Number | 16777215 | Four bytes of serial numbers
AN | Authenticity Number | F5DD153926DA42E8A062A915AD763DF0 | The token's password
PN | Proposed Authenticity Number | 6A3C3E384566483A9EE629D6BB38BCB9 | The new password
CH | Challenge | 84566483A9EE629D6BB38BCB96A3C3E3 | A random number that they RAIDA unencrypt 


# DETECT 

DETECT does not require a PANs (Proposed Authenticty Number) and requires less bandwidth and proccessing power than P'OWN. 
This is used as a fast way of checking to see if tokens are authentic as in a "Check Health" function on a client.  
Use UDP if sending less than 65 notes and TCP if sending more. 
It is recomended that this servie only be used if you have already used the DETECT SUM service and it responded with a All Fail status. 

REQUEST: Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
3E 3E //Not Encrypted
```
Response Status | Code
---|---
All Pass | 241
All Fail | 242
Mixed | 243

Response Body if All Pass or All Faile
```
3E 3E
```

Response body only present if there is mixed content
```
MS //Mixed content bitfield. Maybe many bytes. One byte for 8 tokens. 0 is fail, 1 is pass. Will be padded with zero bits to the end of a byte. 
3E 3E  //Not Encrypted
```

# DETECT SUM
Like DETECT, but does not require ANs. Instead, the ANs are added up and put in one number. 
This service can be much faster than DETECT, especially if there are lots of tokens being detected. 
Use UDP if sending between 65 and 275 notes. 
Use TCP if sending more than 275 notes, you should use TCP and it is probably better to use regular detect in case one of the tokens is bad.  


REQUEST: Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  SN SN SN SN  
DN  SN SN SN SN  
DN  SN SN SN SN 
DN  SN SN SN SN  
SU SU SU SU SU SU SU SU SU SU SU SU SU SU SU SU //Some of all Authenticity Numbers with the higest numbers using ADD and not carrying about the Carrie bit (on the processor flags)
3E 3E //Not Encrypted
```
Response Status | Code | Note
---|---|---
All Pass | 241 | All the tokens were authentic. 
All Fail | 242 //really means sum does not add up. So, the tokens may all be good but just one is bad so the DETECT service should be run to no exactly which tokens are bad. 


Response Body if All Pass or sum does not add up (All Fail). 
```
3E 3E
```

# P'OWN 
P'Own means "Password Own". The client sends the Serial Number, Authenticity Number and the Proposed Authenticity Number. The AN and PAN must be different or an error 
will be returned with a status that the ANs and PANs matched. The server checks to see if the AN that the client sent matches the AN in the table.
If it matches, the AN in the table is replaced with the PAN provided by the client and the "pass" status is returned. 
If multiple tokens are sent then the server may return "all pass" or "all fail" status. 
If some of the tokens are found authentic and some are found counterfeit, then the "mixed" status is returned along with a bitfield. 
The bitfield has one bit per token. A bit set to '1' is a pass and each bit set to '0' is a fail. Zeros are padded to the byte. 
Since the server only returns whole bytes, the client must know when to stop reading the bitfield based on how many tokens were sent.

Like all services, the p'own allows the client to send a 16 byte challenge. 
The server returns the first four bytes of an MD5 hash of this challenge in the response header. 
This allows the client to be certain that the response came from the RAIDA since only the RAIDA would be able to decrypt the challenge.

REQUEST: DN (Denomination) is one byte. SN(Serial Number) is 4 bytes
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN 
DN  SN SN SN SN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
3E 3E  //Not Encrypted
```
Reponse body if all passed | Reponse body if all failed: | Response body if mixed with passes and fails:
---|---|---
no response body | no response body | MT MT MT MT MS  //The MT are just zeros. 0x00 00 00 00. They are for future use in case we want to return master tickets.

MS means Mixed Status. Each bit returned represents the status of one token. If the bit is a zero then that token has failed. If the bit is a 1 then that token is authentic. 

Response Status | Code
---|---
All Pass | 241
All Fail | 242
Mixed | 243


# P'OWN SUM
The client must add up all of the AN numbers to get a sum. Then it includes all the SNs of the tokens that are part of the equation. The PAN
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  SN SN SN SN 
DN  SN SN SN SN  
DN  SN SN SN SN  
DN  SN SN SN SN  
SU SU SU SU SU SU SU SU SU SU SU SU SU SU SU SU //The sum of all the ANs of the tokens added together (no carry)
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD //number to add to the current ANs to derive the PAN 
3E 3E  //Not Encrypted
```
You will need to calculate the new Authenticity Number by adding it to the AD. This is done by summing the AN with the AD and truncating the the number . 
so D4785BED + 48BD4125 = 11D359D12 but we drop the leading 1 so the new AN becomes 1D359D12.  


Status| Response Body Contents
---|---
All Passed | Will not include a response body | Will not include a response body | 
All Failed | Will not include a response body
Mixed | MT MT MT MT MS  //The MT are just zeros. 0x00 00 00 00. They are for future use. The number of MS bytes depends on the number of tokens p'owned. 

MS means Mixed Status. Each bit returned represents the status of one token. If the bit is a zero then that token has failed. If the bit is a 1 then that token is authentic. 

Response Status | Code
---|---
All Pass | 241
All Fail | 242
Mixed | 243
 

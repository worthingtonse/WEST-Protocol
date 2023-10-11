# Locker Service (Command Group 8)
 Locker services allow people to put many tokens into a RAIDA "Locker" and receive a locker key. This locer key can then b given to other
 people who can use the key to download the cions. The locker key is a GUID specified by the client. 
 
 
## Sugested Client Use
1. The client creates a seed code. The seed code consists of 7 characters. The seven characters are randomly picked from 31 different symbols. The symbols are [abcdefghjkmnpqrstuvwxyz23456789] The letters of the alphabet, all numbers. However, the letters oh, and el (O,L,I o,l,i) are excluded to reduce confusion. The numbers zero (0) and one (1) are also excluded. Note that the client can create any code but a seven letter code will be standard.

2. User puts the RAIDA number in front of the code and create a hash from it using MD5. These become the locker numbers and each RAIDA has a different numer. Any messages, emails, attachments etc are also sent.

3. The user sends the seven digit code to the money receiver. Along with any other data the receiver needs. 

4. The receiver puts a number infront of each code and creates the locker numbers. 

5. The receiver uses the keys to withdraw the tokens. 

 
 Command Code | Service
 ---|---|---|---|---
82| [Put](#put) 
83| [Peek](#peek)
84 | [Remove](#remove)


### Client Actions
Here are the steps that the client should do to send tokens to a locker:
1. Calculate if you need to make change (break tokens into smaller denominations).
2. Call "Get Available Change SNs" if you need change.
3. Call "Break" using the SNs if you need change.
4. Fix any known fracks according to the "pown" string on the file name.
5. Call Store Sum on the tokens that will be sent. 
7. Find Any Lost tokens if there is no response. 
8. Fix any Fracked tokens.
9. Tell the receiver the tokens have been sent and give them the code. 
10. Perhaps make change for the next transaction. (Change Management)


Step 5 in detail:
1. Generate a seven character seed. This seed should be random and use only 31 characters. [abcdefghjkmnpqrstuvwxyz23456789] 
2. Put a RAIDA ID in front of each seed and so that there are now 25 seeds. If the seed was YU78HNP then you would have 0YU78HNP, 1YU78HNP, 2YU78HNP..24YU78HNP 
3. Create an MD5 hash of each seed. One for each RAIDA. 
4. Change the last four byte of the MD5 hash so that they are all 0xFF.
5. Add up the sum of all the ANs of the tokens you want to put in the locker. The totals will when they get too big.
6. Pick a token randomly from the bank to use for encryption. 
7. Make the "Store Sum" request. 

### Receiver Actions
10. Get the code from the sender. 
11. Call peek and get all the serial numbers and denominations
12. Calculate the total there. 
13. POWN the tokens
14. Find Lost
15. Fix Fracked. 
16. Give change by calling Store Sum and giving the change to the user. 


## PUT

Store is the exact same thing as POWN SUM but instead of adding a sum, a single PAN is used for all the tokens. This PAN becomes the "Locker Number". However, to make it clear that the PAN is a locker number, the last four bytes must be set to all ones (0xFF) by the client. 


Sample Store Request:
```c
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
DN  SN SN SN SN 
DN  SN SN SN SN  
DN  SN SN SN SN  
DN  SN SN SN SN  
SU SU SU SU SU SU SU SU SU SU SU SU SU SU SU SU //The sum of all the ANs of the SNs. See POWN SUM. 
PN PN PN PN PN PN PN PN PN PN PN PN FF FF FF FF  //This is the PAN for all the tokens. The last four bytes must be set to binary ones (0xFF) 
3E 3E  //Not Encrypted
```


## PEEK
Allows the caller to see all the serial numbers in a RAIDA Locker. 

Sample Call
```c
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH 
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
3E 3E

```

Sample Response
```
DN  SN SN SN SN   
DN  SN SN SN SN   
DN  SN SN SN SN  
...
DN  SN SN SN SN  
3E 3E  //Not Encrypted
```

## REMOVE
This command is just like POWN except there is only one AN. 

To Remove tokens from the locker, the user must first call the Peek, then it will know all the serial numbers and all the ANs for the serial numbers. Note that this allows the user to fix fracked easilty.

Sample Call to Remove tokens
```c
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN
DN  SN SN SN SN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
DN  SN SN SN SN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
DN  SN SN SN SN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN 
DN  SN SN SN SN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
3E 3E  //Not Encrypted
```

Returns all the Serial Numbers that were changed. 

Sample Response
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
MS //Mixed content bitfield. Maybe many bytes. One byte for 8 tokens. 0 is fail, 1 is pass. 
3E 3E  //Not Encrypted
```

## 🔴First Peek (The same command number as Peek)
This works exactly like the Peek except, the command does not use the information in the encryption block of the 
request header in the same way. 

Instead of looking at bytes 17 through 21 as the denomination and the serial number of the encryption token,
these bytes are looked at as the clear-text first part of the AN. 

This allows the RAIDA to quickly identify the key used. 

The RAIDA looks at the first five bytes, then compare these with the first five bytes in the locker index table. 

Sample Call
```c
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH 
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN 
3E 3E

```

Sample Response
```
DN  SN SN SN SN   
DN  SN SN SN SN   
DN  SN SN SN SN  
...
DN  SN SN SN SN  
3E 3E  //Not Encrypted
```





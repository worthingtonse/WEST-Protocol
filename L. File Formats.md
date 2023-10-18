# Binary Files
WEST Tokens are designed to be "files within files" so they can be inserted into any file format. This allows us to embed WEST into PNG, JPG, MP3, DOCX, TXT. PDF and and just about 
any other format. However, the native WEST file format is binary and has a ".bin" file extension. 

Binary files with the ".bin" extention may hold many tokens.
Each file has one file header. Each token inside the file has one token header. 

There are four standard file formats of the .bin files but many more can be added later if needed: 

## File Format Overview

Format Code | Bytes per token | Purpose | Description
---|---|---|---
8 | 807 | Debugging | Software will use this format if they have made a pown request but there was no response from the RAIDA. In this case it is unkonw if the AN or the PAN is in the RAIDA. Files using this format can be created and put in the "Limbo" folder until they are fixed using the "Find" service.  
9 | 416 | Most Secure | This format allows for twentyfive 16-byte Authenticity Numbers that are not related to each other. 
A | 21 | Compressed | This format allows for each token to have a unique seed that is used to generate all the authenticity numbers for that token. 
B | 7 | Ultra Compressed | This format allows all the tokens in the file to use the same seed.

## File Nameing Convention For Single tokens Inside Software:
We are accurate to one satoshi which represents a decimal, seven zeros, and a 1.

File Name Part | Description
---|---
Whole Number | The number infront of the decimal formated with commas between 3 numbers
Fraction | The numbers after the decimal point serperated by underscores. 
Token Symbol | WST (Wyoming Stable token
P'Own String (optional) | 25 (one per RAIDA) letters showing the results of the last pown. p=passed, f=failed, u=untried, e=error, n=no response, k=encrypton error
Serial Number | The serial number of the token in the file
Memo (optional) | Can be used for any purpose a software would like or left empty. Commonly created by the user.
price (optional) | How much in dollars the token should be sold for if for sale. 
Token Group (option)| Allows software to group tokens such as "ForSale", "ForGroceries", "Savings" "invoice_98473233".
Extension | Always ".bin"

Although some items are optional, the dots are not. 

Samples:
```c
0.00_000_001.WEST.ppppfpppuppppppppppupppep.488773920.From Bill.20..bin
0.00_100_000.WEST.ppppupppeppfpppuppppppppp.1122320.GAI.30.ForSale.bin
0.00_000_001.WEST..223920....bin
1.00_000_000.WEST.ppppupppeppfpppuppppppppp.94887720.Bonus.50..bin
1000.00_000_000.WEST.ppppupppeppfpppuppppppppp.3920.extra.1000.All people.bin

```

## File Nameing Convention For Files with Multiple tokens:
If there are more than 1 token in the file, the sum of all the tokens will be in the name. The extension maybe .bin or .png. 

File Name Part | Description
---|---
Whole Number | The number infront of the decimal formated with commas between 3 numbers
Fraction | The numbers after the decimal point serperated by underscores. 
Token Symbol | WST (Wyoming Stable token
Memo (optional) | Can be used for any purpose a software would like or left empty. Commonly created by the user.
Extension | Always ".bin"

Although some items are optional, the dots are not. 

Samples:
```dos
0.08_362_541.For Billy.bin
0.50_167_000.bin
0.00_400_099.Taylor.bin
1.00_000_340.transport.bin
1268.69_894_485.CUBBY.bin
1268.69_894_485.CUBBY.bin

0.08_362_541.For Billy.bin
0.50_167..bin
0.00_400_099.Taylor.bin
1.00_000_34.transport.bin
11,268.69_894_485.CUBBY.bin
2,556.58.For PGR.bin

```


## File Header For All tokens (32 Bytes Fixed)
```
FT CL ID ID SP EN CC HS HS HS HS HS HS HS HS FL
RC RC RC RC RC RC RC RC RC RC RC RC RC RC RC RC 
```
## File Header Codes:
Code | Bytes |  Name | Description
---|---|---|---
FT | 1 |  Format Type | '9', 'A' or 'B'.
CL |  1 | Cloud ID | 1 for theRAIDAX network
ID |  2 |token ID 1 | ID tokens are 0, Cloudtoken is 1, NFTs are 2  10 =  BTC
RE |  1 |Reserved| Reserved
EN |  1 |Encryption Type | 0 for no encryption
CC |  2 |token Count | How many note are in the file. Not the tota value but total count. 
HS |  7 | MD5 Hash of encryption password | First 7 bytes of a hash of the encryption key used to encrypt the token seeds. (if used). Otherwise zeros if no encyption. This is used to tell if the password that the user used was correct. 
FL |  1 |Flags | Represents 8 bitfields. 00000000 means no flags. 00000001 means encoded with email recovery. Custom for applications. 
RC |  16 | Receipt Number | A transaction's GUID to help software track transactions internally. Can be all random or zeros. But can be used by applications for anything. However, if there is no Receipt number, then the following hex should be used "4C 69 76 65 20 46 72 65 65 20 4F 72 20 44 69 65". This transalates to "Live Free or Die" in ASCII.

## DENOMINATIONS

Except for Idtoken and possible future extensions of this table decimal values should be treated as powers of ten

Hex Code| Decimal Code | Denomination
--- | :---: | ---
`0xf8` | F8 | `.000 000 01` (1 Satoshi)
`0xf9` | F9 | `.000 000 1`
`0xfa` | FA | `.000 001`
`0xfb` | FB | `.000 01`
`0xfc` | FC | `.000 1`
`0xfd` | FD | `.001`
`0xfe` | FE | `.01` (1 Cent)
`0xff` | FF | `.1` (1 Dime)
`0x00` | 00 | `1` (1 whole)
`0x01` | 01 | `10`
`0x02` | 02 | `100`
`0x03` | 03 | `1,000`
`0x04` | 04 | `10,000`
`0x05` | 05 | `100,000`
`0x06` | 06 | `1,000,000`
--- | --- | ---
`0x80` | 80 | Idtoken
--- | --- | ---
|  x | XX  | Others (Application keys, chat, etc...) 



# FORMAT '8'
This requires 807 bytes per token. Format 8 should be readable by all software but is not the perfered file format. Format 'A' should be used instead. 
This format is for internal use only. It is used for tokens that are stored in the Limbo folder

Shard 1 byte | Stripe 1 byte | Denomination 1 Byte | Serial Number (4 Bytes) | Authenticity Numbers | PAN Numbers
---|---|---|---|---|---
SP | ST | DN | SN SN SN SN  |  16 byte AN x 25 | 16 byte PAN x 25


## token Body Codes for Format 9
16 byte AN x 25 (The original ANs before the call)
16 byte PAN x 25 (The PANs that were sent to the RAIDA but were not confirmed)

Sample token in format 8
```hex
SP ST DN SN SN SN SN 
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN   
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN
PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN PN  

```



# FORMAT '9'
This requires 407 bytes per token. Format 9 should be readable by all software but is not the perfered file format. Format 'A' should be used instead. 

Shard 1 byte | Stripe 1 byte | Denomination 1 Byte | Serial Number (4 Bytes) | Authenticity Numbers
---|---|---|---|---
Shard | ST | DN | SN SN SN SN  |  16 byte AN x 25


## token Body Codes for Format 9
16 byte AN x 25

Sample token in format 9
```hex
SP ST DN SN SN SN SN 
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN   
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  
AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN AN  

```




# FORMAT 'A'
## token Header Codes for Format 'A'
Every token within a file has its own header. For file format 'A', we just have 20 bytes per token because all the ANs are created by one hash seed. 

Shard | Denomination 1 Byte | Serial Number (4 Bytes) 
---|---|---
SD| ST | DN | SN SN SN SN  

## token Body Codes for Format 'A'

 11 Byte Seed (SE ED) | 3 Bytes Reserved (RE)
---|---
 SE ED SE ED SE ED SE ED SE ED SE | RE RE RE


##  Sample token with Header and Body Format 'A'
This has 7 tokens in it. Each token takes up 20 bytes. 
```
SD ST DN  SN SN SN SN  SE ED SE ED SE ED SE ED SE ED SE  RE RE RE//First token
SD ST DN  SN SN SN SN  SE ED SE ED SE ED SE ED SE ED SE  RE RE RE//Second token
SD ST DN  SN SN SN SN  SE ED SE ED SE ED SE ED SE ED SE  RE RE RE
SD ST DN  SN SN SN SN  SE ED SE ED SE ED SE ED SE ED SE  RE RE RE
SD ST DN  SN SN SN SN  SE ED SE ED SE ED SE ED SE ED SE  RE RE RE
SD ST DN  SN SN SN SN  SE ED SE ED SE ED SE ED SE ED SE  RE RE RE
SD ST DN  SN SN SN SN  SE ED SE ED SE ED SE ED SE ED SE  RE RE RE//7th token
```

To Generate the 25 seeds for each RAIDA:

RAIDA ID | DN |SN | Seed | Concatenated Hex Strings | SHA-384 Hash. Truncated to 16 bytes. 
---|---|---|---|---|---
0x00 | 0xFD | 0x009EF945  | 0xD427AC7B9EF945768E435E | 0x00FD009EF945D427AC7B9EF945768E435E | D9F0CC6D564E8425651BBC6925B0062E
0x01 | 0xFD | 0x009EF945  | 0xD427AC7B9EF945768E435E | 0x01FD009EF945D427AC7B9EF945768E435E | E32D6DC6B7A0A3272E2D8444BF9CDE1C
0x02 | 0xFD | 0x009EF945  | 0xD427AC7B9EF945768E435E | 0x02FD009EF945D427AC7B9EF945768E435E | C6C4D93385C22287C7647F6F2EFA9BE9

1. The software will take all the binary numbers (RAIDA ID, denomination, Serial Number, 11 byte seed ) and turn them into hexidecimal string that uses all uppercase characters. 
2. The resulting number 34 character string will generate a SHA-384 Hash.
3. The hash is trucated to 16 bytes and that becomes the binary AN number. 


# FORMAT 'B'
## token Header Codes for Format 'B'
For this standard, there is one seed for all the tokens. Each token is just the split number, Denomination number and the serial number. This gives us 6 bytes per token. 

After the header, there is an 11 byte seed. THis keeps the same seed size as with format 'A':
 11 Byte Seed (SE ED) 
|---|
| SE ED SE ED SE ED SE ED SE ED SE | 

Then there are six bytes for every token. 

Shard | Denomination 1 Byte | Serial Number (4 Bytes) 
---|---|---
SD | ST | DN | SN SN SN SN  



##  Sample file body in format 'B'
This has 7 tokens in it. Each token takes up 20 bytes. 
```
CC CC CC //token Count (How many tokens are in the file)
SE ED SE ED SE ED SE ED SE ED SE //Seed
SD ST DN  SN SN SN SN  // First token
SD ST DN  SN SN SN SN  
SD ST DN  SN SN SN SN 
SD ST DN  SN SN SN SN  
SD ST DN  SN SN SN SN 
SD ST DN  SN SN SN SN  6th token
```

To Generate the 25 seeds for each RAIDA (Same as with 'A'):

RAIDA ID | DN |SN | Seed | Concatenated Hex Strings | SHA-384 Hash. Truncated to 16 bytes. 
---|---|---|---|---|---
0x00 | 0xFD | 0x009EF945  | 0xD427AC7B9EF945768E435E | 0x00FD009EF945D427AC7B9EF945768E435E | D9F0CC6D564E8425651BBC6925B0062E
0x01 | 0xFD | 0x009EF945  | 0xD427AC7B9EF945768E435E | 0x01FD009EF945D427AC7B9EF945768E435E | E32D6DC6B7A0A3272E2D8444BF9CDE1C
0x02 | 0xFD | 0x009EF945  | 0xD427AC7B9EF945768E435E | 0x02FD009EF945D427AC7B9EF945768E435E | C6C4D93385C22287C7647F6F2EFA9BE9

1. The software will take all the binary numbers (RAIDA ID, denomination, Serial Number, 11 byte seed ) and turn them into hexidecimal string that uses all uppercase characters. 
2. The resulting number 34 character string will generate a SHA-384 Hash.
3. The hash is trucated to 16 bytes and that becomes the binary AN number. 



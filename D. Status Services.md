# Status Services (Command Group 0)

Explains that Status of the RAIDA


Command Code | Service | Description
--- | --- | :---: 
00 | [Echo](OFF_LEDGER.md#echo) | Returns the challenge 
01🔴 | Version  | Returns the version of the protocol
02🔴 | Performance - Status Not Implemented | Returns the usage of services
03🔴 | Audit - CoinsNot Implemented | Returns the number of tokens in the system


# Echo  

Echo tests connection and the encryption to ensure it is working. This echo is more for debugging than production work.
Reponse will have no body. The status code in the response header will be:
```
0xFA  //Decimal code 250
```

Request (18 bytes)
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
3E 3E //Not Encrypted
```

Typically it is enough to send 18 bytes in the body. 
But echo can accept more bytes between CH and 3E if necessary. 
For example if we want to test big packets we may want to send a bigger packet.


# 🔴VERSION
Not documented
<!--
# Not Implemented 
## Show Stats
Sends the data in RAM from the last statistics dump. 
This service must be able to be turned off incase of Denial of service attacks. 
So it will only respond to a 1 request per ten seconds. 

## Show Stats Table in RAIDA RAM

Index | Column Name | Datatype | Notes
0 |  Echo | 4 bytes | Every time echo is called ++
1 |  Audit | 4 bytes | Tracks status calls
2 |  Status | 4 bytes | ++
3 |  Echo | 4 bytes | ++
4 |  Echo | 4 bytes |  ++
5 |  Echo | 4 bytes | ++


Sample Request: 

```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU //Autherization: Only RAIDA can uA password given only to status reporting servers
ST // Status Code requested
AM AM AM //Number of records requested. 

3E 3E //Not encryption
```
Sample Respons:
```

Needs to be decided. 

```
-->

# Audit
Note: This service is almost identical to the Stable Token Service called Get_Available_SNs.
Returns the serial numbers of the coin in that denomination that have been created. It has two sections: The returned item can be either an individual serial number or a range.
The ranges come first prepended by the number of them. The individual serial numbers come after them.

This method is the first part of the two-step FreeID command. The client sends four-byte SessionID and the RAIDA server locks serial numbers
using the Session ID. After that, the next step downloads the coins using the same Session ID.

Example Request Body with four coins:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
00 00 00 00 //These bytes are ignored
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU //Authorization Password. Only RAIDA can use this service and a password is required
DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN DN //These bytes must all be zeros except one: The denomination requested. 
3E 3E
```


Response Status | Code
---|---
Success | 250
Failure | 251


Returns: three ranges (contiguous serial numbers) and three single (incontiguous) serial numbers. It also shows the denomination but the client will probably 
rember what denomination they asked for. 
```
DN   
NR NS 
RR RR RR RR RR RR RR RR //Range: 4 bytes start, 4 bytes end. 
RR RR RR RR RR RR RR RR
RR RR RR RR RR RR RR RR 
SN SN SN SN
SN SN SN SN
SN SN SN SN 
```




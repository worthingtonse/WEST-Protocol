# Status Services (Command Group 0)

Explains that Status of the RAIDA


Command Code | Service | Description
--- | --- | :---: 
00 | [Echo](OFF_LEDGER.md#echo) | Returns the challenge 
01 | Version  | Returns the version of the protocol
02🔴 | Shows - Status Not Implemented | Returns the usage of services
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


# VERSION
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
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU //Authentication A password given only to status reporting servers
ST // Status Code requested
AM AM AM //Number of records requested. 

3E 3E //Not encryption
```
Sample Respons:
```

Needs to be decided. 

```

## Audit Coins
Shows the denominations and the amount of each denomination that is on the RAIDA.

This shows the hex code for the Denomination here

Sample Audit Coins Request: 

```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
3E 3E //Not Encrypted
```

Returns as many records as there are denominations that have coins:
```
DN CT CT CT    //DN comes from the denomination table. CT is the count of how many coins of that denomination there are. 
DN CT CT CT  
DN CT CT CT 
.... The amount will vary
3E 3E //Not Encrypted
```

-->


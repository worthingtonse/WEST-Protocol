![Status Services](zips/status.png)

# Status Services (Command Group 0)

Explains that Status of the RAIDA.


Command Code | Service | Description
--- | --- | :---: 
00 | [Echo](#echo) | Returns the challenge 
01 | [Version](#version)  | Returns the version of the protocol
02 | [Performance](#performance) | Returns the usage of services (Not Implemented)
03 | [Count Coins](#count-coins) | Returns the number of tokens in the system


# Echo  

Echo tests connection and the encryption to ensure it is working. This echo is more for debugging than production work.

Typically it is enough to send 18 bytes in the body. 
But echo can accept more bytes between CH and 3E if necessary. 
For example if we want to test big packets we may want to send a bigger packet.

Request Body (18 bytes):
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
3E 3E //Not Encrypted
```

Reponse will have no body. The status code in the response header will be:

0xFA  //Decimal code 250


# Version

Command Group: 0

Command Number: 1

Request (no parameters)
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
3E 3E
```
Response: Success Status 250

Version (8 bytes)
```
VE VE VE VE VE VE VE VE
3E 3E
```

# Count Coins
Returns the total number of coins the RAIDA has. 

## Example Request Body:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
00 00 00 00 //for future use 
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU //Authorization Password. Only RAIDA can use this service and a password is required
3E 3E
```
Response Status | Code
---|---
Success | 250
Failure | 251

Response:

## Response
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
TO TO TO TO TO TO  //Six Byte total amount of coins
3E 3E
```
<!--
Code | Meaning
--|--
CH |  Challenge (16 byte randome number)
DU |  Days of uptime. How many days the RAIDA has been up since last failure .
NE | No Error Time. How many days since the RAIDA has had to change its SN table to correct errors. 
ER | Errors Last time. How many errors were found the last time it found errors.  
AU |  Authorizatio number (Secret between RAIDA servers like a password)
DN | Denomination of the serial numbers that the RAIDA is to return. 


# Performance (This service is not implemented yet)
The job of the performance service is to allow admins to evaluat the use of their RAIDA. 
The performance returns up to 255 performance indicators. 
The is done by key value pairs These keys and values are based on the Performance Table below. 

Returns: three ranges (contiguous serial numbers) and three single (incontiguous) serial numbers. It also shows the denomination but the client will probably 
rember what denomination they asked for. 
```
DU DU NE ER    //Discolosures
NR NS 
RR RR RR RR RR RR RR RR //Range: 4 bytes start, 4 bytes end. 
RR RR RR RR RR RR RR RR
RR RR RR RR RR RR RR RR 
SN SN SN SN
SN SN SN SN
SN SN SN SN 
```

Example Request Body:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
KY MX MX MX //Key Maxium Records to return
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU //Authorization Password. Only RAIDA can use this service and a password is required
PR PR PR PR PR PR PR PR PR PR PR PR PR PR PR PR //Comand Parameters. Each one may have different meanings 
3E 3E
```

Sample Responses with four key/value pairs:
```
DT DT DT DT TT TT TT TT //Date (a day), Total calls for that day)   //Key Value
3E 3E
```

Number of times a service has been called

Amount handled per request. 
Key | Name | Parameters
---|---|---
0 | Echo | None
10 | Detect | 4 byte starting date. 4 byte ending date (optional)
20 | Pown | 4 byte starting date. 4 byte ending date (optional)
30 | Pown with Check Sum and Add | 4 byte starting date. 4 byte ending date (optional)
40 | Get Ticket | 4 byte starting date. 4 byte ending date (optional)
50 | Validate Ticket | 4 byte starting date. 4 byte ending date (optional)
60 | Find | 4 byte starting date. 4 byte ending date (optional)
80 | Fix | 4 byte starting date. 4 byte ending date (optional)
81 | Put in Locker | 4 byte starting date. 4 byte ending date (optional)
82 | Get From Locker | 4 byte starting date. 4 byte ending date (optional)
83 | Read Meta | 4 byte starting date. 4 byte ending date (optional)
90 | Fix with Remote Key | 4 byte starting date. 4 byte ending date (optional)
100 | Show Stats | 4 byte starting date. 4 byte ending date (optional)
120 | Get Available SNS | 4 byte starting date. 4 byte ending date (optional)
130 | 	Create Coins | 4 byte starting date. 4 byte ending date (optional)
140 | Delete Coins | 4 byte starting date. 4 byte ending date (optional)
150 | Audit Coins | 4 byte starting date. 4 byte ending date (optional)
200 | Put Key | 4 byte starting date. 4 byte ending date (optional)
210 | Get Key | 4 byte starting date. 4 byte ending date (optional)
220 | Exchange Key | 4 byte starting date. 4 byte ending date (optional)
-->



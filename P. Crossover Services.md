#Crossover services
Crossover services allows people to convert West tokens to other cryto's or from other crypto's to West Tokens.

## Commands

[Convert From West](#convert_from_west) 
[Convert To West](#convert_to_west)

## Convert From West
The user must first put the coins that they want to sell into a locker.
The user sends:
* The cryptocurrency-code that they want to convert into (See table of crypto currencies)
* The converstion cost they expect to pay
* Their wallet address
* The receipt number (not required)
* The memo (up to 1300 bytes)


Sample Request
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
CD CD CD //currency code to convert to
$$ $$ $$ //Converstion cost expected
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD
AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD AD
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID  //The receipt ID 
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME
...
ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME ME //up to 1300 bytes of memo data. Optional. Total of 32K accross the RAIDA. 
3E 3E //Not Encrypted
```


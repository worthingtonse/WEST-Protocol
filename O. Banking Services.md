# Banking Services
Allows a user to control their account. The user must already have been given an account but the network administrator.

Command Number | Command
---|---
110 | [Balance](#balance)
111 | [Transfer](#transfer)
112 | [Read Statement](#read_statement)

## Balance
Returns that number of Tokens a user has in their account.
Data Type of the total: DECIMAL(27,18) 12 bytes. 4 bytes for each 9 digits. 


Example Request Body:
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
00 00 00 00 //Account Number
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU //Account Key
3E 3E
```

Response
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
TO TO TO TO TO TO TO TO TO TO TO TO  //12 Byte total amount of coins in the account
3E 3E
```

## Transfer
Moves coins from owner's account to another

Example Request Body:
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
00 00 00 00 //Account Number
AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU AU //Account Key
00 00 00 00 //To account number
TO TO TO TO TO TO TO TO TO TO TO TO  //12 Byte total amount of coins to transfer
ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID ID //GUID of the transaction
YY MM DD HH MM SS //Client's time stamp Must be in UTC
TY //There will be two transactions created. One for the sender and one for the receiver. The sender has 03 and the receiver has 02.
ST ST ST ST.....//Fix length Meta Data Stripe up to 50 bytes.
3E 3E
```

Response
```
// No body, just a status of All Pass or a status of "failed to authenticate"
```


## Statement


## Confirm Payment

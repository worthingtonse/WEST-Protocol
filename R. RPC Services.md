# Remote Procedure Call Services
RPC Services allows clients to make simple calls to get simple information. 

This has not been developed yet. 

![Status Services](zips/gpg.png)

# nslookup (Command Group _)

Explains that Status of the RAIDA.


Command Code | Service | Description
--- | --- | :---: 
120 | [nslookup](#nslookup) | Looks up the IP address of a server
121 | [host](#host)  | Returns the Host file on the RAIDA. 
122 | [Get Exchange Rate](#get_exchange_rate) | Gets the exchange rate between West and another currency.
123 | [Ask AI](#ask_ai) | Asks AI a question
124 | [GET URL](#get-url) | Gets the return data from a URL call. 



## nslookup 
Allow the caller to request the first IP returned from an nslookup called on the RAIDA. The 
fully qualified domain name requested can be up to 64 bytes. 

Request (no parameters)
```
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN //64 Bytes Fixed
FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN
FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN
FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN FQ DN 
3E 3E
```
Response: Success Status 250

Version (8 bytes)
```
IP V4 IP V4 // four byte IPV4 address. 
3E 3E
```


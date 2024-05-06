# Key Manager

Key Manager is a CLI program that creates and destroys keys.
The following keys can be managed:

- Admin Key
- User Key
- KYC Key
- Treasurer Key
- RAIDA Key

You need an Admin Key in order to create the other key types.
If a RAIDA server is run in the 'Init' mode then the admin key is not required

Init mode can be set in the RAIDA configuration file:
```
init_mode = 1
```

Key Manager can also mint and destroy coins. A treasurer key is needed for that purpose.

The full list of possible arguments:

```
# ./wyoming_key_manager -help
Usage of ./dist/wyoming_key_manager:
./dist/wyoming_key_manager [-debug] <operation> <args>
./dist/wyoming_key_manager [-help]
./dist/wyoming_key_manager [-version]
./dist/wyoming_key_manager createkey <type>
./dist/wyoming_key_manager deletekey <type> <key_id>
./dist/wyoming_key_manager mintcoins <denomination> <count>
./dist/wyoming_key_manager freecoins <denomination> <serialnumber>
<args> arguments for operation

<type> Key type. One of: kyc, user, treasurer, raida, admin<key_id> Key ID (serial number)<denomination> Coin Denomination (from -7 to 7)<serialnumber> Coin Serial Number  -cli
        RUN in CLI mode
  -cliport string
        Listen a specific port (works in CLI mode only)
  -debug
        Display Debug Information
  -help
        Show Usage
  -home string
        Home Directory
  -uipath string
        UI Path
  -version
        Display version

Examples

# Create Admin Key

Error:
```
$ ./dist/wyoming_key_manager createkey admin
{"code":4100,"message":"No encryption coin found","details":null}
```

Success:
```
$ ./dist/wyoming_key_manager createkey admin
{"code":0,"message":"Key created"}
```

# Create User Key
```
$ ./dist/wyoming_key_manager createkey user
{"code":0,"message":"Key created"}
```

# Create Treasurer Key
```
$ ./dist/wyoming_key_manager createkey treasurer
{"code":0,"message":"Key created"}
```

# Delete Treasurer Key
```
$ ./dist/wyoming_key_manager deletekey treasurer
{"code":0,"message":"Key deleted"}
```

# Minting coins

In order to mint a coin you will want to specify the denomination and number of coins
The denomination range starts from 0.0000001 (lowest) to 10000000 (highest)

```
$ ./dist/wyoming_key_manager mintcoins 10 12
{"code":0,"message":"Coins Minted"}
```

# Delete (free) a minted coin

To delete a previously minted coin specify its denomination and serial number

```
$ ./dist/wyoming_key_manager freecoins 10 123
{"code":0,"message":"Coins Deleted"}
```






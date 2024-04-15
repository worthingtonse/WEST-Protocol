# User Access Keys
Users are not allowed to use the Wyomging Stable Token unless they have passed KYC and AML requirments. 
Users who meet the KYC and AML requirments will be issued an Access Key by the Access Key Administrator. 

## Access Key Types
There are other types of access keys that can also be issued. Here is a list of all the possible keys and their DEN code:

Key Type | Hex Code | Description | Supported Services
---|---|---|---
Common User | 0x77 | These keys are for everyone who uses the token | Athentication, Locker, Healing, Change
Key Administrator |  0x78 | Can issue keys and lock out users | Status, Stable ( denomination 0x77)
Treasure | 0x79 | Creator and destroyer of tokens  | Stable 
RAIDA Machines |0x80| Keys used to allow RAIDA to talk to each other | All except for Stable

## Services
There are services that are used to manage keys:

### Create & Destroy Keys
These services have already been covered [Stable Token Services](F. Stable Token Services.md)



  


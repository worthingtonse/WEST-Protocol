# Access Keys

There are four access key types implemented in the RAIDA Protocol.

Every packet sent to a RAIDA server is encrypted with a key of a certain type. Besides its primary encryption function the key also provides authorization.

A RAIDA server decrypts a packet and identifies the user and permissions granted to this user.

The keys implemented as coins with higher denominations.

The key types are:

## User Keys. Denomination 7
Users are not allowed to use the Wyomging Stable Token unless they have passed KYC and AML requirments. 
Users who meet the KYC and AML requirments will be issued an Access Key by the Access Key Administrator. 
Almost all services require this key: pown, pown_sum, detect, detect_sum, store_sum, store, remove_locket, fix, get_ticket.

Echo and Version calls do not require any key.

## Treasurer Keys. Denomination 8
These keys are used to mint coins. Whoever posseses these keys can create and destroy coins
(get_available_sns, create_coins, delete_coins)

## Admin Keys. Denomination 9
Admins can create all key types and mint coins. Also admins can query stat services (get_stat, audit)
Admins can call ANY service on the RAIDA.

## RAIDA Keys. Denomination 10
Used for inner-RAIDA communication. Only one service (validate_ticket) checks these keys

## KYC Keys. Denomination 11
Anyone can upload KYC documents to the RAIDA filesystem. (create_folder, delete_folder, upload_file, delete_file)






  


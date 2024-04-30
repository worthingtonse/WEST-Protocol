# RAIDA Filesystem Service (Command Group 10)

These services allow the RAIDA to create and modify folders and data objects. 
It is up to the client to stripe/mirror and sum objects.


Command Code | Service 
--- | --- 
101 | [Create Folder](#create-folder)
102 | [List Folder](#ls-folder)
103 | [Remove Folder](#remove-folder)
104 | [Put Object](#put-object)
105 | [Get Object](#get-object)
106 | [Remove Object](#rm-object)


# Create Folder
The Create Folder service creates a folder on the RAIDA filesystem.
The Root Path for the folder is located in the user's directory Folders/<UserID>. The user ID equals to the ID of the KYC key.

The maximum path length is 255 charaters.

Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT .. PT PT PT
3E 3E //Not Encrypted
```

PT is no more than 255 bytes. The full path of the folder
The path must be NULL-terminated

Response Status | Code
---|---
Success | 250
Filesystem Error | 200
Error Already Exists | 201



# Remove Folder
Recursively deletes a folder and all of its contents.
Requires KYC permission.

```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT .. PT PT PT
3E 3E //Not Encrypted
```

PT is no more than 255 bytes. The full path of the folder
The path must be NULL-terminated


Response Status | Code
---|---
Success | 250
Filesystem Error | 200
Does not exist | 202



# List Folder
Displays content of a folder.
An object in the folder can be either another folder or a data file.
Requires KYC permission.


```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT .. PT PT PT
3E 3E //Not Encrypted
```

PT is no more than 255 bytes. The full path of the folder
The path must be NULL-terminated


Response

```hex
NO NO
TY SZ SZ SZ SZ FZ ON ON ON ON ON ON ON ON ON ON
TY SZ SZ SZ SZ FZ ON ON ON ON ON ON ON ON ON ON
TY SZ SZ SZ SZ FZ ON ON ON ON ON ON ON ON ON ON
3E 3E //Not Encrypted
```

NO - Number of objects

TY - Object Type. 1 - Folder, 2 - Data

SZ - Object size in bytes

FZ - Object name length

ON - Object name


Response Status | Code
---|---
Success | 250
Filesystem Error | 200
Does not exist | 202



# Put Object
The service stores a binary chunk of any size below 4G in a RAIDA folder.
The format of the chunk is up to the Client software.

The Client needs to provdide the chunk size

The maximum path length is 255 charaters.

Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
SZ SZ SZ SZ
PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT .. PT PT PT
PL PL PL ... PL
3E 3E //Not Encrypted
```

SZ - the size of the object in bytes
PT - is no more than 255 bytes. The full path of the folder
The path must be NULL-terminated

PL - chunk palyoad


Response Status | Code
---|---
Success | 250
Filesystem Error | 200
Error Already Exists | 201


# Get Object
The service retreives a binary chunk from the RAIDA

The maximum path length is 255 charaters.

Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT .. PT PT PT
3E 3E //Not Encrypted
```

PT - is no more than 255 bytes. The full path of the folder
The path must be NULL-terminated

Response

```hex
PL PL PL ... PL
3E 3E //Not Encrypted
```

PL - chunk payload

Response Status | Code
---|---
Success | 250
Filesystem Error | 200
Does not Exist | 202


# Remove Object
The service remove a binary chunk from the RAIDA


Example Request Body with four tokens:
```hex
CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH CH
PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT PT .. PT PT PT
3E 3E //Not Encrypted
```

PT - is no more than 255 bytes. The full path of the folder
The path must be NULL-terminated


Response Status | Code
---|---
Success | 250
Filesystem Error | 200
Does not Exist | 202

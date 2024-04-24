# RAIDA Filesystem Service (Command Group 10)

These services allow the RAIDA to create and modify folders and data objects. 
It is up to the client to stripe/mirror and sum objects.


Command Code | Service 
--- | --- 
101 | [Create Folder](#create-folder)
102 | [List Folder](#ls-folder)
103 | [Remove Folder](#remove-folder)


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

Response Status | Code
---|---
Success | 250
Filesystem Error | 200
Does not exist | 202




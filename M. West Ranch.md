# West Domain
NOTE: The name "West Domain" is analogus to "Windows Domain" or "Linux Realm", tools used to administer enterprise networks. 

West Domain is an optional features that allows Wyoming to control every aspect of the token and to see everything that is going on including all actions that each user takes and 
exactly who those users are.

# Components of West Ranch include:
Domain

User Accounts

User Groups

Ranch Resources

User Permissions (Access Control Lists)

User Rights

## Domain

A West Domain is a computer network that uses redundant databases to manage user accounts, user groups and access to 
resources such as authentication services and money lockers.

The West Domain has "Domain Administrators". 

## Accounts
Everytime a user wants to join the West Domain, the Domain Administrator will need to create an account for them. 
Accounts have a DNS name such as "David.Falk@WST.WY.gov" and are associated with a dotted decimal number such as 154.56.214.899. Accounts can be recorded in a DNS server to allow for global lookup. 

When the account is created, the user will receive a key that allows them to be part of the West Domain. Users will need to import this key into their software to use the West Token. 

## Groups
Groups are logical groupings of users so that they can be given rights and permissions to access resources on the Domain. 
There are Built-in groups that are perminent in the Domain and Custom Groups that can be created as needed by administers.  
The Domain Administrators can assign people to be members of custom groups and some built-in groups. 

### Built-in Groups
These groups cannot be deleted. Everyone is automatically a member of the Authenticated Users group and the RAIDA Servers Group cannot be modified.
In Phase I, the built-in groups include:
* Authenticated Users (All accounts are members of this group by default)
* Account Administrators (People who are allowed to create, disable and delete accounts). 
* RAIDA Servers (The actual servers used as nodes)
* Treasurers ( People who are allowed to create, disable and delete tokens) 

NOTE: In Phase I, the Trasueres Group is able to create and delete but not disable. Disable is not needed right now because we can disable accounts instead and that 
will do nearly the same thing. We will add this in disable tokens Phase II. 

### Custom Groups
Custom Groups reduce the administrative effort needed to administrator the Ranch. For example, we can have a custom
group called "Suspect" that is not allowed to user the "Locker" facilities. If we want to stop Bob from using lockers we just 
add his account to the Suspect group. 

In Phase I we will not have custom groups. These will be created in Phase II. To start with we can enable and disable accounts. This is not very granular but its good for a start. 

## Resources
Resources are things on the ranch that users can use. The West Ranch has these resources: 

* Token Authentication
* Token Change Service
* System Status Reports
* Token Healing
* Mint
* Lockers
* Quantum Safe Key Exchange
* Membership

# Permissions / ACL (Access Contol Lists)
Each resource tracks user permissions in its ACL. Each list has a "allow" part on top and a "deny" part on the bottom. To 
have permission to use a resource, the user must pass two tests: They must be on the allow part of the list, and they must not be on the deny part of the list. 

These Resources have the Authenticated Users built-in group in the allow part. This allows everyone to use these services unless specifically denied. 
* Token Authentication
* Token Change Service
* Token Healing
* Lockers
* Quantum Safe Key Exchange

These Resources have deny everyone automatically except members added to specific groups. 
* Mint: Treasurers built-in group
* System Status Reports: Account Administrators and Treasurers built in groups
* Membership Services:  Account Administrators built in group

# Rights
The ranch has a list of Rights. 
* Create user accounts
* Disable user accounts
* Delete user accounts
* Create tokens
* Disable / freeze tokens 
* Delete / Burn tokens
* Grant and deny permission to use services. 

For example, the Realm Administrator can say that user Bill@West.Wy.gov is able to trade up to $75K per day except on Sundays while Jill@West.Wy.gov may only trade $1K per day from 9am to 5pm MT. 







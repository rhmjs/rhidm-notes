# Group Based RBAC

Sometime in the 90s, Microsoft coined the acronym ["AGDLP"](https://en.wikipedia.org/wiki/AGDLP), which refers to ab RBAC model based on Windows NT Domain group structures.

* An **A**ccount is added as a member to a **G**lobal group,
* The **G**lobal group is added as a member to a **D**omain **L**ocal group,
* The **D**omain **L**ocal group is granted **P**ermisions

So, the process of granting access to a user would look like **A**ccount-**G**lobal Group-**D**omain **L**ocal Group-**P**ermission, or AGDLP.

Although the syntax in this model is very specific to Active Directory, there is some real value in this model.  Let's break this down, make it a little more generic, and explore why this is an important model.

**Please note -- throughout this document I will hold to a rather pure application of grou-based RBAC.  I do understand that pragmatic application may require some deviation from this model; however, caution should be applied in deviating too far to avoid reducing the overall effectiveness.**

## Account
The account is rather self-explanatory -- this is simply the object that represents a person or other actor (such as a computer system performing actions on another computer system).

Sometimes this is called a "User", but we will continue referring to this simply as "Account" to include the non-human actors.

## Permissions
Permissions or privileges reflect the access that can be granted to a resource.  For example, access could be granted to read the contents of a web-site, or to create new files on a network share, or to open a specific door in a specific building.  Generally, any given resource could allow for multiple types of access: read and write are two very common access types for files, but much more complex privileges can be created, such as the ability to create new files versus just modifying existing files, the ability to delete files, etc.

Let's simply refer to this as a "Privilege".

## Global Group
The Global group represents a group of accounts.  Groups of accounts should be reflected by something the users have in common, such as:

* Organizational membership ( Financial Analysts )
* Location ( Occupants of Building 113 )
* Common Work ( HR Project Developers )

These groups of users may also have a hierarchical, or nested, relationship.  For example, a group named "Springfield Campus" might contain nested member groups named "Occupants of Building 113", "Occupants of Building 274", and "Springfield Commuters".  Similarly, the group named "All IT Staff" might contain nested member groups named "IT Executive Leadership" and "IT System Administrators" (among others), and "IT System Administrators" might have futher nested member groups named "IT Windows System Administrators" and "IT Linux System Administrators".

The account "msmith" might be a member of both the "Occupants of Building 113" group and the "IT Linux System Administrators" groups; which means that "msmith is also an *effective member* of "Springfield Campus" and "All IT Staff" through the nested memberships.

Note that these groups do not directly reflect permissions/privileges, but rather describe the relationship among these users.

Let's call this type of group an "Account Group".

## Domain Local Group
The Domain Local Group represents a specific privilege that can be granted to a user.  For example, a Domain Local group might be created named "Write Access to HR Project Content Site".  Within the Content Management service, this group object would be granted the ability write to the HR Project's site.

As can be imagined, creating groups to represent every access that could be granted to every resource can be a major undertaking, and major waste of time and effort.  Only the necessary groups should be created.

These groups may also be nested.  For example, a group named "Access Content Management Service" may contain the nested member groups "Write Access to HR Project Content Site" and "Read Access to Parking Services Content Site" (among others).  The "Access Content Management Service" may then be granted access to a gateway/proxy/network-ACL that protects the overall Content Management service.  By granting access to user via one of the more specific groups, that user is also granted the requisite access through the gateway via the group nesting.

Note that these groups do not describe the accounts that will be accessing the resource, but rather describe only the nature of the access and resource.

We will call this type of group a "Privilege Group".

## Model Rules
This model is quite simple and flexible, but there is one operational requirement that must be adhered to for maximal effectiveness:

**There must always be at least one Account Group and at least one Privilege Group in a relationship granting a Privilege to an Account**

Accounts must never be granted privileges directly.  Period.  This maintains the consistency and auditability of the process for those rare activities when an account must be granted access to a resource that was previously assumed to be owned by only a single account, without impacting the original account.

For example, when an employee is terminated, there are often three requirements:

1: The employees account should not be modified, to maintain metrics like "password last changed" or "last logon time"
2: The employee's resources should not be modified, such as the ACLs on their home directory
3: The supervisor should be given access to the employee's resources, such as the home directory.

If a group has been used to grant the employee access to the home directory, then granting this access is as simple as adding the supervisor to the group.

If no group exists, then either the resource's ACLs will need to be modified (violating #2) or the employee's password will be reset and given to the supervisor (violating #1).

## Granting Access
So, to allow everyone that works in Springfield to see the latest collection of parking regulations, one simply has to nest the "Springfield Campus" group into the "Read Access to Parking Services Content Site" group.

If the User "msmith" is a member of the User Group "Occupants of Building 113", which is a member of the User Group "Springfield Campus", and this group has just become a member of the Privilege Group "Read Access to Parking Services Content Site", which is a member of the Privelege Group "Access Content Management Service", then msmith can now pass through the Content Management gateway and read the new parking regulations.

Note that his access was granted without touching the Content Management service, beyond the original assignment of access to Privilege Groups.  The access was granted purely by manipulatng group memberships, within the group management system.

## Why This Is Important
So here is where we discuss the raison d'etre for this RBAC model, which we will refer to as *Account - Account Group - Privilege Group - Privilege*:

**Access management is centralized *completely* within the group management solution**

As critical by-products of this model:

**Audit of access is as simple as reviewing group memberships**

--and--

**Granting of access is consistent and repeatable, regardless of user or type of service, and performed soley within a single group management solution**.


As a corollary, **there must always be at least one Account Group and at least one Privilege Group in a relationship granting a Privilege to an Account**, accounts must never be granted privileges directly.  This maintains the consistency and auditability of the process, and accounts for those rare activities where an account must be granted access to a resource that was previously assumed to be owned by only a single account, without impacting the original account (e.g., granting a supervisor access to a terminated employee's home directory without changing the permissions on the home directory nor the password and login metrics of the employee's account).

## QED
So there we have it - a full disection group-based RBAC, and why it is important.  Please feel free to comment!

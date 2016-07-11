#Integrating iDRAC 6 with Red Hat Identity Management
## For Authentication and Access Control


### Prequisites
1. Download your IdM CA certificate from http://idmserver.example.com/ipa/config/ca.crt
1. Create a POSIX group in IdM for admin users, such as idrac-admins
1. Add a user account to the idrac-admins group

### Access iDRAC 6 Generic LDAP Configuration and Management
1. Log into the iDRAC6 as root
1. Select "iDRAC Settings" from the left menu
1. Pick "Network/Security" from the top menu, then "Directory Service" from the sub-menu
1. Check "Generic LDAP Directory Service, then click the "Apply" button
1. Clich the "COnfigure Generic LDAP" button on the bottom right

### Step 1 of 3
1. Ensure "Enable Certificate Validation" checkbox is checked
1. "Upload Directory Service CA Certificate	" selecting the IdM CA certificate you downloaded above
1. Confirm that you see "Certificate upload was successful" at the bottom, and close the green banner at the top to continue.
1. Click the "Next" button at the bottom

### Step 2 of 3
1. Ensure "Enable Generic LDAP" checkbox is checked
1. Ensure "Use Distinguished Name to Search Group Membership" checkbox is checked
1. Enter the FQDN of your IdM server as the "LDAP Server Address"
1. Ensure the "LDAP Server Port" is 636
1. Bind DN and Bind Password can both be left blank
1. Enter the "Base DN to Search", which will be of the form "cn=accounts,dc=example,dc=com" (without the quotes).
1. "Attribute of User Login" is "uid".
1. "Attribute of Group Membership" is "member"
1. "Search Filter" can be left blank
1. Click the "Next" button to complete this page

### Step 3a of 3
1. Click on "Role Group 1" (or other appropriate Role Group if Role Group 1 is already used)

### Step 3b of 3
1. Enter a "Group DN" of the form "cn=idrac-admins,cn=groups,cn=accounts,dc=example,dc=com", adjusting the group name (cn) and base (dc) as appropriate for your installation
1. Select the appropriate privileges for this role.
1. Click the "Apply" button to continue.

### Step 3a of 3 (again)
1. Click the "Finish" button to continue.

### Generic LDAP Configuration and Management
1. Click the "Test Settings" button
1. Enter the user ID and password for a member of the idrac-admins group
1. Click the "Start Test" button.
1. Confirm all tests pass as expected.

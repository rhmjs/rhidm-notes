#Integrating iDRAC 6 with Red Hat Identity Management

## iDRAC Authentication and Access Control via IdM users and groups

### Prequisites
1. Download your IdM CA certificate from http://idmserver.example.com/ipa/config/ca.crt
1. Create a POSIX group in IdM for admin users, such as idrac-admins
1. Add a user account to the idrac-admins group

### Access iDRAC 6 Generic LDAP Configuration and Management settings
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

## iDRAC HTTPS Certificate signed by IdM CA
### Prequisites
1. Create a host identity in IdM for the iDRAC interface, such as "idrac-ABC12345"

### Access iDRAC 6 SSL settings
1. Log into the iDRAC6 as root
1. Select "iDRAC Settings" from the left menu
1. Pick "Network/Security" from the top menu, then "SSL" from the sub-menu

### GenerateStart the CSR Process
1. Select "Generate Certificate Signing Request (CSR)"
1. Click the "Next" button

### Generate Certificate Signing Request (CSR)
1. Enter the full FQDN such as "idrac-ABC12345.example.com" of the iDRAC interface as the "Common Name"
1. The "Organization Name", "Organization Unit", "Locality", "State", "Country Code", and "Email" can be set to strings as appropriate
1. Click the "Generate" button to download your CSR

### Sign the CSR to create a certificate
1. In the IdM web interface, click into the iDRAC host object created earlier
1. Click the "Action" drop-down button and select "New Certificate"
1. Copy and paste the contents of the CSR downloaded above into the large text box, and click the "Issue" button
1. In the "Host Certificate" pane, click the "Show" button to view the contents of the SSL certificate.
1. Copy those contents and paste into an empy text file.
1. Insert a line containing only "-----BEGIN CERTIFICATE-----" at the beginning of the text file and a line containing only "-----END CERTIFICATE-----" at the end of the text file, such that it looks as follows, and save this file - this is the certificate file for your iDRAC.  **Copy and paste the BEGIN and END lines carefully, as the case and number of spaces is important!** Note this step could more correctly be accomplished by running "ipa getcert" if you have access to the "ipa admin" CLI tool.
```
-----BEGIN CERTIFICATE-----
MIIEIjCCAwqgAwIBAgIBEDANBgkqhkiG9w0BAQ....
-----END CERTIFICATE-----
```

### Install the new certificate
1. Return to the iDRAC SSL interface as described above.
1. Select "Upload Server Certificate" and click the "Next" button.

### Upload Server Certificate
1. Select the certificate you just created, and click the "Apply" button.  If you receive an error, you can use the OpenSSL CLI to fix the formatting of the certificate ```openssl x509 -in idrac.crt -out newidrac.crt```, and upload the 'newidrac.crt' as the corrected certificate.
1. Close the success banner and click the "Go back to the SSL Menu" button.
1. Your certificate is now successfully installed, and your browser can be used to confirm that the certificate used for HTTPS is now signed by your IdM CA.

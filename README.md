# ThinkLab-2257
WebSphere Automation
## Open a browser and visit the console at the following url 
>> cpd-websphere-automation.apps.ocp.ibm.edu

## Login using the openshift authentication with the following username and password 
>> ocadmin
>> ibmocp46

## Go to the application runtime under the hamburger menu on the top left corner
## You should see the console with no data entries

## Next we'll add the liberty and tWAS servers 

## Open 2 terminal session and login as root on both of them.

## Login as root
su root
1bm2021rhjb

## Login to the OCP cluster
oc login --username=ocadmin --password=ibmocp46 --insecure-skip-tls-verify=true --server=https://api.ocp.ibm.edu:6443

## Change the project to websphere-automation
oc project websphere-automation



# Liberty 200012
/opt/IBM/WebSphere/Liberty200012/bin/server create Liberty_200012_server

?? Backup original server.xml

## Copy server_tls.xml to server.xml
cp /home/ibmuser/Desktop/lab_backup/liberty200012/server_tls.xml /opt/IBM/WebSphere/Liberty200012/usr/servers/Liberty_200012_server/server.xml


## Start the server
/opt/IBM/WebSphere/Liberty200012/bin/server start Liberty_200012_server


## Getting the usage metering url
echo https://$(oc get route cpd -n websphere-automation -o jsonpath='{.spec.host}')/websphereauto/meteringapi > /opt/IBM/WebSphere/metering-url.txt

## Getting the api key
oc -n websphere-automation get secret automation-secure-metering-apis-encrypted-tokens -o jsonpath='{.data.automation-secure-metering-apis-sa}' | base64 -d > /opt/IBM/WebSphere/api-key.txt; echo >> /opt/IBM/WebSphere/api-key.txt

## Get server certificate 
oc get secret external-tls-secret -n websphere-automation -o jsonpath='{.data.cert\.crt}' | base64 -d > /opt/IBM/WebSphere/cacert.pem


## Update the keystore
keytool -import -trustcacerts -file /opt/IBM/WebSphere/cacert.pem -keystore /opt/IBM/WebSphere/Liberty200012/usr/servers/Liberty_200012_server/resources/security/key.p12 -storetype PKCS12 -storepass th1nkpassword -noprompt

?? encoded password: {xor}KzduMTQvPiwsKDAtOw==

## Edit the server.xml, gedit will hold your terminal
gedit /opt/IBM/WebSphere/Liberty200012/usr/servers/Liberty_200012_server/server.xml

## Add usage-metering feature like below
<featureManager>
  <feature>usageMetering-1.0</feature>
  <feature>jsp-2.3</feature>
  <feature>transportSecurity-1.0</feature>
</featureManager>

## On your second terminal, run the following cat commands

## Add usageMetering element
<usageMetering url="<url value>" apiKey="<apiKey value>" sslRef="defaultSSL" />

## Get url value and replace it in the above element
cat /opt/IBM/WebSphere/metering-url.txt

## Get api key and replace it in the above element
cat /opt/IBM/WebSphere/api-key.txt

## Save the server.xml file, close the editor and the second terminal.

## Visit the console at the following url and you should see a Liberty Server entry 
>> cpd-websphere-automation.apps.ocp.ibm.edu/websphereauto/secvul/



# LIBERTY 20009

## Liberty
/opt/IBM/WebSphere/Liberty20009/bin/server create Liberty_20009_server

?? Backup original server.xml

## Copy server_tls.xml to server.xml
cp /home/ibmuser/Desktop/lab_backup/liberty20009/server_tls.xml /opt/IBM/WebSphere/Liberty20009/usr/servers/Liberty_20009_server/server.xml

## Start the server
/opt/IBM/WebSphere/Liberty20009/bin/server start Liberty_20009_server

## Update the keystore
keytool -import -trustcacerts -file /opt/IBM/WebSphere/cacert.pem -keystore /opt/IBM/WebSphere/Liberty20009/usr/servers/Liberty_20009_server/resources/security/key.p12 -storetype PKCS12 -storepass th1nkpassword -noprompt

?? encoded password: {xor}KzduMTQvPiwsKDAtOw==

## Edit the server.xml, gedit will hold you terminal.
gedit /opt/IBM/WebSphere/Liberty20009/usr/servers/Liberty_20009_server/server.xml

## Add usage-metering feature like below
<featureManager>
  <feature>usageMetering-1.0</feature>
  <feature>jsp-2.3</feature>
  <feature>transportSecurity-1.0</feature>
</featureManager>

## Add usageMetering element

<usageMetering url="https://cpd-websphere-automation.apps.ocp.ibm.edu/websphereauto/meteringapi" apiKey="5pJi/TEFVB+pnLnyq2GaPAxGtnUiizeM4HBl+eKeb8GOG+DUkUaXYq4kGs176qgR7pYa+FKNgKl6fkIqZxqmC8RY8GnQgombVgbZ7VUvYnSvFEG2Qorf+toEjH9xZFUGGXsvEg4IsNGqqNZgxNC5jn6SiRVXoeT5Ilb0BSpquymRZYefljLYNutIaFZF4A+F+Lmk1PM22fnf47Po2aQhtFt4ZWdnQ1bw37MIfjIrjmTJQhlavZKAG7W4kF9IcBRJtRaz4Pi9CK5TovtivRwUq8Etze89XwgsKjzbCRTmC4kpbu4+GanEPUgLISYhzGYrwETeMDdXPCHMCM6wct3WWCFcmndIVcsQ+UByHzIX8c76RqTbm38PMA7Z3wcVr8OuQbxhX5HvM2NXffcg9HLAWWvhbxJ4xhPA8EmwHHH6mncL+ElFtpIMfEQ5Id+AJSfr+j89sB8grm3AQcNdHO2kt4sumBcdXl2QTKAs+7oNPRy6abwKiE7hHGXJlZPPBPRMIKxPl6wFcykJjtoy3nVYc1ieU29uQdukDFZTOJBwsHsQXESnPFgxglPj0A24s+VQCju2YyHPGpEgrjsXb4e5oklU2uT4dF3pbwP6sQLLFTZ0Hl2bchiQc7OFctamgIRG6D8lJJGtIrGHOTHtXUFOzdKXknwqoSWksRyowgikN3jQXT50WeQ4Id7MVMxSKWB5dHANMlS6aTa4f718+I7uw0DvwAc/Dima/87ABJzhkgKAMjg6CMVI+qo/Bik58AVvP5hWhFII6tLMn2h2wLc3EXOMhiONwEwmh1u2qXpMV2fP8PNTuGl8+BROGd5SEzeg/SI92aE6x6lc+dhRuLm3wUAoYPYpnPyKmn0R5FcYDwJR0Xw5J9er3TucplN6Y5botW9sytZK4Ssk9AodisfF+kfIlN0TsGnxlLSGCRy7Yn86spza1aB1lfW0aMI9UKxwwEq/aXg+Wo5jSwhj+xII36/3/yDXn6o7lNzSRhLNBKo89hV6gb7/aa1yvtNIPzRFsmc+xRArz+uBsu+P6JtH/M31LcVZ6HwPJVpCZGNxD6I6JomT1U/43RFwCNOSzTrpnTKfl88ruSeJtVL43gDMGfa4HuC4iBlL931FzDZYlBQCAhC7VXgWqHV/fYtFEaKMXy/XMS2zhQa2hlBX7bdvO9xEG0S+3cE4f2wi6tRvOs9nPY4Se6OPKnce3GOVJ9xo425mbrpiMigqV1adPVsca89CStwSGCtJkf3OFz11rF9oSVmVosgaLbPLzns5+3zuOmtJQXNE57170q1vaw7FsHAYCkI92UaXxmJoqrqLba53ZMtImhsw1SCtMMGAFxHg5aS1KafLZGTbcxe651CLk+TLE9c2yQXhV2k8X4qG+AdwlFL15MJQxjMUm5wadBCY" sslRef="defaultSSL" />

## Visit the console at the following url and you should see both Liberty Server entries 
>> cpd-websphere-automation.apps.ocp.ibm.edu/websphereauto/secvul/

# tWAS 9056

## Start the server

/opt/IBM/WebSphere/AppServer9056/bin/startServer.sh tWAS_9056_server

## Configure usage metering 
/opt/IBM/WebSphere/AppServer9056/bin/wsadmin.sh -f /api-usagemetering/scripts/configuretWasUsageMetering.py url=$(cat /opt/IBM/WebSphere/metering-url.txt) apiKey=$(cat /opt/IBM/WebSphere/api-key.txt) trustStorePassword=th1nkpassword

## Visit the console at the following url and you should see 2 Liberty Server entries and 1 tWAS entry
>> cpd-websphere-automation.apps.ocp.ibm.edu/websphereauto/secvul/


# tWAS 9057

## Start the server

/opt/IBM/WebSphere/AppServer9057/bin/startServer.sh tWAS_9057_server

## Configure usage metering 
/opt/IBM/WebSphere/AppServer9057/bin/wsadmin.sh -f /api-usagemetering/scripts/configuretWasUsageMetering.py url=$(cat /opt/IBM/WebSphere/metering-url.txt) apiKey=$(cat /opt/IBM/WebSphere/api-key.txt) trustStorePassword=password

## Visit the console at the following url and you should see 2 Liberty Server entries and 2 tWAS entries
>> cpd-websphere-automation.apps.ocp.ibm.edu/websphereauto/secvul/


# Demo (applying fix packs, removing features etc)

# Liberty 20.0.0.9 demo

## Let's add a feature:

## Edit the server.xml file, adding the following feature
gedit /opt/IBM/WebSphere/Liberty20009/usr/servers/Liberty_20009_server/server.xml

<feature>beanValidation-2.0</feature>

## Now check the console for a change. 
## If email notification is set up, you will also receive an email about this CVE.




# Demo for tWAS 9057

## Notice in the console there is 1 CVE (CVE-2021-26296) under the tWAS 9057 server, we are about to fix that.

## First, stop the server, wait until it has stopped.
/opt/IBM/WebSphere/AppServer9057/bin/stopServer.sh tWAS_9057_server

## Run the following script to install the iFix 
/iFix/PH34711/imcl_ifix_install.sh 9057

## Start the server back up once the installation was completed.
/opt/IBM/WebSphere/AppServer9057/bin/startServer.sh tWAS_9057_server

## Go back to the console. Notice that there are no more CVEs under tWAS 9057.
## Also, you can see on the right under "Applied iFixes" there are 4 applied fixes.
cpd-websphere-automation.apps.ocp.ibm.edu/websphereauto/secvul/

## Now, stop the server and uninstall the ifix.
/opt/IBM/WebSphere/AppServer9057/bin/stopServer.sh tWAS_9057_server
/iFix/PH34711/imcl_ifix_uninstall.sh 9057

## Start the server back up
/opt/IBM/WebSphere/AppServer9057/bin/startServer.sh tWAS_9057_server

## Go back to the console. Notice that there is now again 1 CVE under the tWAS 9057 server.
cpd-websphere-automation.apps.ocp.ibm.edu/websphereauto/secvul/











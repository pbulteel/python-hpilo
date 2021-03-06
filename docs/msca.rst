Managing SSL certificates
=========================

When managing large amounts of iLO interfaces, the constant SSL warnings are a
nuisance, so let's make sure we have proper SSL certificates. This script will
make that easy to do for you by taking care of all the work. 

It allows you to use a Microsoft AD Certificate Authority environment to sign 
all the CSRs generated by your iLOs.


First thing to do is configure the MSCA. Add something like the following to your
:file:`~/.ilo.conf`::

  [ca]
  path = ~/.hpilo_ca
  country = NL
  state = Flevoland
  locality = Lelystad
  organization = Kaarsemaker.net
  organizational_unit = Sysadmin
  server = ca.example.com
  ca_login = exampleUser
  ca_password = examplePassword

The path is a location where the CSR is stored before uploading to the MSCA 
and where the signed certificate is stored when it's returned by the CA.

When your CA is set up, you can start signing certificates. :file:`hpilo_msca`
will check several things:

 * Firmware is upgraded if necessary
 * DHCP is disabled if enabled as this doesn't allow for FQDN to be set
 * The hostname is set to the name you use to connect to it, if needed
 * iLO2 is configured to use FQDN's for certificate signing requests

It will then download the certificate signing request, sign it and upload the
signed certificate. Here's an example of it at work::

  $ ./hpilo_ca sign example-server.int.kaarsemaker.net
  (1/6) Checking DHCP settings on example-server.int.kaarsemaker.net
  (2/6) Checking certificate config of example-server.int.kaarsemaker.net
  (3/6) Retrieving certificate signing request
  (4/6) Signing certificate
  (5/6) Uploading certificate
  (6/6) Resetting iLO

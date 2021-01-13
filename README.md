#PingFederate Outbound Provisioning and PingDirectory SCIMv1 and SCIMv2 Lab

###Servers:
* PF 10
* PD 8.2 with 2 separate Backends for provisioning source (dc=provsource,dc=net) and target (dc=example,dc=com) 

PingFederate is setup to provision users from LDAP to both SCIMv1 and SCIMv2 (2 separate SP connections)
SCIMv1 uses PF built-in SCIM plugin
SCIMv2 uses PF dedicated SCIM connector plugin

###LDAP Source:
PD's dc=provsource,dc=net backend (BE)

Both SCIM1 & 2 uses the same source BE but different source settings:

SCIMv1 takes:
Users to provision from group: cn=UsersToProvision,ou=GroupsGroups,dc=provsource,dc=net 
Groups from: cn=GroupsToProvision,ou=GroupsGroups,dc=provsource,dc=net

SCIMv2 takes:
Users from: ou=ProvisioningContainer,ou=staff,dc=provsource,dc=net (using LDAP filter prov. mode)
Groups from: Not implemented due to PD 8.2 limitations.


###Target:
PD's dc=example,dc=com backend

SCIM1 users will be in: ou=provisioned,ou=People,dc=example,dc=com
SCIM1 groups will be in: ou=Groups,dc=example,dc=com

SCIM2 users will be in: ou=provisioned2,ou=People,dc=example,dc=com



##Exposed ports

You are able to access server using "localhost".

E.g.: PF console: https://localhost:9999/pingfederate/app

PingFederate:

Admin Console: 9999
Runtime Port: 9031

PingDirectory:

LDAP: 1389
LDAPS: 1636
HTTP: 18080
HTTPS: 1443

PingDataConsole (PD console):
https://locahost:8443


##Start

To start lab you need Docker and docker-compose.
clone repository:
git clone <url>

cd into repository directory then:

docker-compose up

By default containers uses docker volumes which are not exposed to your machine.

In order to see logs on your Mac and preserver you need to tweak docker file and create directry.

Uncomment the following in docker-compose:



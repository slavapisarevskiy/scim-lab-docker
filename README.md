# PingFederate Outbound Provisioning and PingDirectory SCIMv1 and SCIMv2 Lab

### Servers:
* PF 10
* PD 8.2 with 2 separate Backends for provisioning source (dc=provsource,dc=net) and target (dc=example,dc=com) 

PingFederate is setup to provision users from LDAP to both SCIMv1 and SCIMv2 (2 separate SP connections)
SCIMv1 uses PF built-in SCIM plugin
SCIMv2 uses PF dedicated SCIM connector plugin

### LDAP Source:
PD's dc=provsource,dc=net backend (BE)

Both SCIM1 & 2 uses the same source BE but different source settings:

SCIMv1 takes:
Users to provision from group: cn=UsersToProvision,ou=GroupsGroups,dc=provsource,dc=net 
Groups from: cn=GroupsToProvision,ou=GroupsGroups,dc=provsource,dc=net

SCIMv2 takes:
Users from: ou=ProvisioningContainer,ou=staff,dc=provsource,dc=net (using LDAP filter prov. mode)
Groups from: Not implemented due to PD 8.2 limitation - SCIMv2 does not support Groups provisioning.


### Target:
PD's dc=example,dc=com backend

SCIM1 users will be in: ou=provisioned,ou=People,dc=example,dc=com
SCIM1 groups will be in: ou=Groups,dc=example,dc=com

SCIM2 users will be in: ou=provisioned2,ou=People,dc=example,dc=com

## Credentials:

* PF console: administrator / 2FederateM0re
* PD LDAP: cn=administrator / 2FederateM0re
* PD Console: administrator / 2FederateM0re (!) "Server" in UI prompt: pingdirectory 

## Exposed ports

You are able to access server using "localhost".

E.g.: PF console: https://localhost:9999/pingfederate/app

PingFederate:

* Admin Console: 9999
* Runtime Port: 9031

PingDirectory:

* LDAP: 1389
* LDAPS: 1636
* HTTP: 18080
* HTTPS: 1443

PingDataConsole (PD console):
* https://locahost:8443/console


##Start

To start lab you need Docker and docker-compose.

Clone repository. Recommend ~/projects:

```
mkdir
cd ~/projects
git clone https://github.com/slavapisarevskiy/scim-lab-docker.git
```

cd into repository directory and run:

```
cd ~/projects/scim-lab-docker
docker-compose up -d
```

Temporarily stop (e.g.: at the end of the day):
```docker-compose stop```
Later to start:
```docker-compose start```

Delete container and its volumes (complete reset) !don't forget -v otherwise docker volumes will be kept and next time you spin it it will not be fresh start:
```docker-compose down -v```

###In order to see logs on your Mac and preserver you need to tweak docker file and create directry.

By default containers use Docker's volumes which are not exposed to your Mac/machine.

Create directory (if not already):

```mkdir ~/projects/scim-lab-docker/```

In docker-compose.yaml file:

Under services/pingfederate:

Comment:
```
#- pingfederate-out:/opt/out
```
Uncomment:
```
- ${HOME}/projects/scim-lab-docker/pingfederate-opt-out:/opt/out
```

Under services/pingdirectory:

Comment:
```
#- pingdirectory-out:/opt/out
```
Uncomment:
```
- ${HOME}/projects/scim-lab-docker/pingdirectory-opt-out:/opt/out
```

Obviously you can modify path to any you like but make sure that directory (path - 1) exists. E.g.: "${HOME}/projects/scim-lab-docker" must exist, "pingdirectory-opt-out" will be created. 

###Using only PD (no PF) as SCIM target:
You can just run docker-compose as above and access PD HTTP/HTTPS ports. Though if you like to save Mac resources you can remove PF. In order to do it just comment everything under services/pingfederate in docker-compose and reset.

From:
```
#    pingfederate:
#        image: ${PING_IDENTITY_DEVOPS_REGISTRY}/pingfederate:10.0.2-edge
```
Up to:
```
#    volumes:
#      - pingfederate-out:/opt/out
#      - ${HOME}/projects/scim-lab-docker/pingfederate-opt-out:/opt/out
#      - ${HOME}/projects/scim-lab-docker/profiles/pingfederate:/opt/in
```
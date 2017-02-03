---
Title: Rotating Ops Manager Certificates via (by? with an?) API 
Owner: OpsMan / Security
---


Certificates expire or need to be regenerated.
Security compliance policies may require your certs to be rotated regularly.

Rotate cert on OpsMan and it passes to other places in the deployment. 

Best to use w/ tile, because then you'll know it works correctly--no testing, it just works.

1. Deploy OpsManager1.10-alpha1 + Any Tile
1. Apply Changes
1. 
```POST /api/v0/certificate_authorities/generate with an empty request to create a new certificate authority
GET /api/v0/certificate_authorities
``` 
to see the new CA which should be marked as inactive
1. Apply Changes

1. ```POST /api/v0/certificate_authorities/:certificate_authority_guid/activate
```
 with an empty request to activate the new CA that was created above
1. 
```POST /api/v0/certificate_authorities/active/regenerate 
```
with an empty request to regenerate all non configurable certificates
1. Apply Changes
1. ```DELETE /api/v0/certificate_authorities/:certificate_authority_guid
```
 to delete old inactive CA
1.Apply Changes

Please refer to Ops Manager API docs for examples if needed [1]




[1] http://opsman-dev-api-docs.cfapps.io/#certificate-authorities

---
title: Managing Non-Configurable TLS/SSL Certificates
owner: OpsMan
owner: Security
---

The non-configurable certificates in your deployment expire every two years.
You must regenerate and rotate them so that critical components do not face a complete outage.

Use the procedures in this topic to retrieve certificates and determine when your certificates expire.
When you need to rotate your Certificate Authority (CA), contact [Pivotal Support](http://support.pivotal.io/).

For information about rotating IPsec certificates, see [Rotating IPsec Certificates](https://docs.pivotal.io/addon-ipsec/1-8/credentials.html).

##<a id='list-certs'></a> Retrieve Certificates

To manage and retrieve information about certificates in your deployment, use the API calls in this section.

Perform the steps in the [Using Ops Manager API](../../customizing/ops-man-api.html) topic to target and authenticate with the Ops Manager User Account and Authentication (UAA) server. Record your Ops Manager access token, and use it for `YOUR-UAA-ACCESS-TOKEN`.

To return the Ops Manager root CA certificate as a file, use `curl` to make the following API call:
<pre>
$ curl "https<span>:</span>//OPS-MAN-FQDN/api/v0/download\_root\_ca\_cert \
      -X GET \
      -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
</pre>

To return the Ops Manager root CA certificate as JSON, use `curl` to make the following API call:
<pre>
$ curl "https<span>:</span>//OPS-MAN-FQDN/api/v0/security/root\_ca\_certificate \
      -X GET \
      -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
</pre>

To return metadata from all deployed RSA certificates signed by the root CA, use `curl` to make the following API call:
<pre>
$ curl "https<span>:</span>//OPS-MAN-FQDN/api/v0/deployed/certificates \
      -X GET \
      -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
</pre>

##<a id='cert-expiry'></a> Check Certificate Expiration Dates

The non-configurable certificates in your deployment expire every two years.
Use the following procedure to retrieve information about the expiration dates for RSA and CA certificates in your deployment.

1. Perform the steps in the [Using Ops Manager API](../../customizing/ops-man-api.html) topic to target and authenticate with the Ops Manager User Account and Authentication (UAA) server. Record your Ops Manager access token, and use it for `YOUR-UAA-ACCESS-TOKEN`.

1. To check the system for certificates that expire within a given time interval, use `curl` to make an API call.<br><br>
    Use the `https://OPS-MAN-FQDN/api/v0/deployed/certificates?expires_within=TIME` endpoint, replacing `TIME` with an integer and a letter code.
    Valid letter codes are `d` for days, `w` for weeks, `m` for months, and `y` for years.<br><br>
    For example, run the following command to search for certificates expiring within six months:
    <pre>
    $ curl "https<span>:</span>//OPS-MAN-FQDN/api/v0/deployed/certificates?expires\_within=6m" \
          -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
    </pre>
    Replace `YOUR-UAA-ACCESS-TOKEN` with the <code>access_token</code> value you recorded in the previous step.


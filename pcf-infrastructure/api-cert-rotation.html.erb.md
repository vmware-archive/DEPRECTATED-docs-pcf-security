---
title: Checking Certificate Authority Expiration Dates for Non-Configurable TLS/SSL Certificates 
owner: OpsMan
owner: Security
---

The non-configurable certificates in your deployment expire every two years.
You must regenerate and rotate them so that critical components do not face a complete outage.

Use the procedure in this topic to determine when your certificate authority expires.
When you need to rotate your certificate authority, contact [Pivotal Support](http://support.pivotal.io/).

##<a id='cert-expiry'></a> Check CA Certificate Expiration Dates

The non-configurable certificates in your deployment expire every two years.
Use the following procedure to retrieve information about the expiration dates for RSA and CA certificates in your deployment.

1. Perform the steps in the [Using Ops Manager API](../../customizing/ops-man-api.html) topic to target and authenticate with the Ops Manager User Account and Authentication (UAA) server. Record your Ops Manager access token, and use it for `YOUR-UAA-ACCESS-TOKEN`.

1. Use `curl` to make an API call to check for certificates expiring on the system within 6 months:
<pre>
$ curl "https<span>:</span>//OPS-MAN-FQDN/api/v0/deployed/certificates?expires\_within=6m" \
      -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
</pre>
  Replace `YOUR-UAA-ACCESS-TOKEN` with the <code>access_token</code> value you recorded in the previous step.

1. (Optional) To check for expiring certificates in a different time interval, replace `TIME` with an integer and a letter code. Valid letter codes are `d` for days, `w` for weeks, `m` for months, and `y` for years.<br><br>
    For example, run the following command to search for certificates expiring within one year:
    <pre>
    $ curl "https<span>:</span>//OPS-MAN-FQDN/api/v0/deployed/certificates?expires_within=1y" \
        -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
    </pre>


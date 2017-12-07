---
title: Checking Certificate Authority Expiration Dates for Non-Configurable TLS/SSL Certificates 
owner: OpsMan
owner: Security
---

The non-configurable certificates in your deployment expire every two years.
You must regenerate and rotate them so that critical components do not face a complete outage.

Use the procedure in this topic to determine when your certificate authority expires.
When you need to rotate your certificate authority, contact [Pivotal Support](http://support.pivotal.io/).

##<a id='cert-expiry'></a> Check Certificate Authority Expiration Dates

To retrieve information about the expiration dates for RSA and CA certificates in your deployment, perform the following steps:

1. Target your Ops Manager UAA server:
<pre>
$ uaac target https<span>:</span>//OPS-MAN-FQDN/uaa
</pre>

1. Retrieve your token to authenticate. When prompted for a passcode, retrieve it from `https://OPS-MAN-FQDN/uaa/passcode`.
<pre>
$ uaac token owner get
    Client ID: opsman
    Client secret:
    User name: OPS-MAN-USERNAME
    Password: OPS-MAN-PASSWORD
</pre>
  Leave **Client secret** blank.
  Replace `OPS-MAN-USERNAME` and `OPS-MAN-PASSWORD` with the credentials that you use to log in to the Ops Manager web interface.

1. List your tokens:
<pre>
$ uaac contexts
</pre>

1. Locate the entry for your Ops Manager FQDN. Record the value for <code>access_token</code>.

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


---
title: Regenerating and Rotating Non-Configurable TLS/SSL Certificates 
owner: OpsMan
owner: Security
---

<style>
.note.warning {
    background-color: #fdd;
    border-color: #fbb
}

.note.warning:before {
    color: #f99;
 }
</style>

This topic describes how to regenerate and rotate your Certificate Authority (CA) certificates.

<p class="note warning"><strong>Warning</strong>: You must complete the procedures in this topic in the exact order specified below. Otherwise, you risk doing damage to your deployment.</p>

##<a id='overview'></a> Overview

Depending on the requirements of your deployment, you may need to rotate your CA certificates. Certificates can expire or fall out of currency, or your organization's security compliance policies may require you to rotate certificates periodically.

You can rotate the certificates in your Pivotal Cloud Foundry (PCF) deployment using `curl`. PCF provides different API calls with which to manage certificates and CAs. New certificates generated through this process use SHA-256 encryption.

These API calls allow you to create new CAs, apply them, and delete old CAs. The process of activating a new CA and rotating it in gives new certificates to the Ops Manager Director. The Ops Manager Director then passes the certificates to other components in your PCF deployment.

<p class="note"><strong>Note</strong>: These procedures require you to return to Ops Manager and click <strong>Apply Changes</strong> periodically. Clicking <strong>Apply Changes</strong> redeploys the Ops Manager Director and its tiles. If you apply your changes during each procedure, a successful redeploy verifies that the certificate rotation process is proceeding correctly.</p>

##<a id='add-new-ca'></a> Step 1: Add a New CA

1. Perform the steps in the [Using Ops Manager API](../../customizing/ops-man-api.html) topic to target and authenticate with the Ops Manager User Account and Authentication (UAA) server. Record your Ops Manager access token, and use it for `YOUR-UAA-ACCESS-TOKEN` in the following procedures.

1. Use `curl` to make an API call to create a new CA:
  <pre class="terminal">
  curl "http<span>s</span>://OPS-MAN-FQDN/api/v0/certificate\_authorities/generate" \ 
    -X POST \ 
    -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN" \ 
    -H "Content-Type: application/json" \ 
    -d '{}'
</pre>  
If the command succeeds, the API returns a response that includes the new certificate. <pre class="terminal">HTTP/1.1 200 OK
{
  "guid": "f7bc18f34f2a7a9403c3",
  "issuer": "Pivotal",
  "created\_on": "2017-01-19",
  "expires\_on": "2021-01-19",
  "active": false,
  "cert\_pem": "-----BEGIN EXAMPLE CERTIFICATE-----
	MIIC+zCCAeOgAwIBAgIBADANBgkqhkiG9w0BAQsFADAfMQswCQYDVQQGEwJVUzEQ
	MA4GA1UECgwHUGl2b3RhbDAeFw0xNzAxMTgyMTQyMjVaFw0yMTAxMTkyMTQyMjVa
	MB8xCzAJBgNVBAYTAlVTMRAwDgYDVQQKDAdQaXZvdGFsMIIBIjANBgkqhkiG9w0B
	AQEFAAOCAQ8AMIIBCgKCAQEAyV4OhPIIZTEym9OcdcNVip9Ev0ijPPLo9WPLUMzT
	IrpDx3nG/TgD+DP09mwVXfqwBlJmoj9DqRED1x/6bc0Ki/BAFo/P4MmOKm3QnDCt
	o+4RUvLkQqgA++2HYrNTKWJ5fsXmERs8lK9AXXT7RKXhktyWWU3oNGf7zo0e3YKp
	l07DdIW7h1NwIbNcGT1AurIDsxyOZy1HVzLDPtUR2MxhJmSCLsOw3qUDQjatjXKw
	82RjcrswjG3nv2hvD4/aTOiHuKM3+AGbnmS2MdIOvFOh/7Y79tUp89csK0gs6uOd
	myfdxzDihe4DcKw5CzUTfHKNXgHyeoVOBPcVQTp4lJp1iQIDAQABo0IwQDAdBgNV
	HQ4EFgQUyH4y7VEuImLStXM0CKR8uVqxX/gwDwYDVR0TAQH/BAUwAwEB/zAOBgNV
	HQ8BAf8EBAMCAQYwDQYJKoZIhvcNAQELBQADggEBALmHOPxdyBGnuR0HgR9V4TwJ
	tnKFdFQJGLKVT7am5z6G2Oq5cwACFHWAFfrPG4W9Jm577QtewiY/Rad/PbkY0YSY
	rehLThKdkrfNjxjxI0H2sr7qLBFjJ0wBZHhVmDsO6A9PkfAPu4eJvqRMuL/xGmSQ
	tVkzgYmnCynMNz7FgHyFbd9D9X5YW8fWGSeVBPPikcONdRvjw9aEeAtbGEh8eZCP
	aBQOgsx7b33RuR+CTNqThXY9k8d7/7ba4KVdd4gP8ynFgwvnDQOjcJZ6Go5QY5HA
	R+OgIzs3PFW8pAYcvWrXKR0rE8fL5o9qgTyjmO+5yyyvWIYrKPqqIUIvMCdNr84=
	-----END EXAMPLE CERTIFICATE-----
	"
}</pre>
<br>
If you don't want to use a Pivotal-generated CA, you can provide your own CA with the following API call:
    <pre class="terminal">
    $ curl "http<span>s:</span>//OPS-MAN-FQDN/api/v0/certificate\_authorities" \ 
        -X POST \ 
        -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN" \ 
        -H "Content-Type: application/json" \ 
        -d '{"cert_pem": "-----BEGIN CERTIFICATE-----\nMIIC+zCCAeOgAwI...", "private_key_pem": "-----BEGIN RSA PRIVATE KEY-----\nMIIEowIBAAKCA..."}'
    </pre>

1. Confirm that your new CA has been added by listing all of the root CAs for Ops Manager:
  <pre class="terminal">
  $ curl "http<span>s:</span>//OPS-MAN-FQDN/api/v0/certificate\_authorities" \ 
    -X GET \ 
    -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
  </pre>
  <br>
  The API call returns the following:
  <pre class="terminal">
  HTTP/1.1 200 OK
  {
    "certificate\_authorities": [
      {
        "guid": "f7bc18f34f2a7a9403c3",
        "issuer": "Pivotal",
        "created\_on": "2017-01-09",
        "expires\_on": "2021-01-09",
        "active": true,
        "cert\_pem": "-----BEGIN CERTIFICATE-----\nMIIC+zCCAeOgAwIBAgI....etc"
      }
      {
        "guid": "a8ee01e33e3e3e3303e3",
        "issuer": "Pivotal",
        "created\_on": "2017-04-09",
        "expires\_on": "2021-04-09",
        "active": false,
        "cert\_pem": "-----BEGIN CERTIFICATE-----\zBBBC+eAAAe1gAwAAAeZ....etc"
      }
    ]
  }
  </pre>
  Identify your newly added CA, which has `active` set to `false`. Record its `guid`.

1. Navigate to `https://OPS-MAN-FQDN` in a browser and log in to Ops Manager.
1. Click **Apply Changes**. When the deploy finishes, continue to the next section.

##<a id='activate-new-ca'></a> Step 2: Activate the New CA

1. Use `curl` to make an API call to activate the new CA, replacing `CERT-GUID` with the GUID of your CA that you retrieved in the previous section:
  <pre class="terminal">$ curl "http<span>s</span>://OPS-MAN-FQDN/api/v0/certificate\_authorities/CERT-GUID/activate" \ 
      -X POST \ 
      -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN" \ 
      -H "Content-Type: application/json" \ 
      -d '{}' 
  </pre> 
  The API returns a successful response:
  <pre class="terminal">HTTP/1.1 200 OK</pre>

1. List your root CAs to confirm that the new CA is active:
<pre class="terminal">
  $ curl "http<span>s:</span>//OPS-MAN-FQDN/api/v0/certificate\_authorities" \ 
    -X GET \ 
    -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
  </pre>
  Examine the response to ensure that your new CA has `active` set to `true`.

##<a id='regenerate'></a> Step 3: Regenerate Non-Configurable Certificates to Apply the New CA

1. Use `curl` to make an API call to regenerate all non-configurable certificates and apply the new CA to your existing Ops Manager Director:
<pre class="terminal">$ curl "http<span>s</span>://OPS-MAN-FQDN/api/v0/certificate_authorities/active/regenerate" \ 
    -X POST \ 
    -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN" \ 
    -H "Content-Type: application/json" \ 
    -d '{}'
</pre> 
The API returns a successful response:
<pre class="terminal">HTTP/1.1 200 OK</pre>

1. Navigate to Ops Manager and click **Apply Changes**. When the deploy finishes, continue to the next section.

##<a id='delete'></a> Step 4: Delete the Old CA 

1. List your root CAs to retrieve the GUID of your old, inactive CA:
<pre class="terminal">
  $ curl "http<span>s:</span>//OPS-MAN-FQDN/api/v0/certificate\_authorities" \ 
    -X GET \ 
    -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
  </pre>

1. Use `curl` to make an API call to delete your old CA, replacing `OLD-CERT-GUID` with the GUID of your old, inactive CA:  
<pre class="terminal">curl "http<span>s</span>://OPS-MAN-FQDN/api/v0/certificate_authorities/:OLD-CERT-GUID" \ 
    -X DELETE \ 
    -H "Authorization: Bearer YOUR-UAA-ACCESS-TOKEN"
</pre> 
The API returns a successful response. 
<pre class="terminal">HTTP/1.1 200 OK</pre>

1. Navigate to Ops Manager and click **Apply Changes**. 

---
title: Rotating Non-Configurable TLS/SSL Certificates 
owner: OpsMan / Security
---
Depending on the requirements of your deployment, you may need to rotate your CA certificates. Certificates can expire or fall out of currency, or your organization's security compliance policies may require you to rotate certificates periodically.

In a Pivotal Cloud Foundry (PCF) deployment, you can rotate the certificates using API calls in the command line. This API regenerates the certificate authority (CA) associated with a deployment. New certificates generated through this process will use SHA-256 encryption.

<p class="note"><strong>Note</strong>: In this procedure, you will return to Ops Manager frequently to click <strong>Apply Changes</strong>. Clicking <strong>Apply Changes</strong> redeploys the Ops Manager Director and its tiles. If you apply your changes at each stage of this procedure, a successful redeploy will verify that the certificate rotation process is proceeding correctly.</p>

1. Open the command line terminal.

1. Open a web browser and navigate to Ops Manager.

1. In Ops Manager, click **Apply Changes**. 

1. In the command line terminal, enter the following API call with an empty request:  <pre class="terminal">curl "http<span>s</span>://example.com/api/v0/certificate_authorities/example-cert-guid/activate" \ 
    -X POST \ 
    -H "Authorization: Bearer YOUR\_UAA\_ACCESS\_TOKEN" \ 
    -H "Content-Type: application/json" \ 
    -d '{}'
</pre>  
The API returns a successful response, including a new certificate. <pre class="terminal">HTTP/1.1 200 OK
{
  "guid": "f7bc18f34f2a7a9403c3",
  "issuer": "Pivotal",
  "created\_on": "2017-01-19",
  "expires\_on": "2021-01-19",
  "active": false,
  "cert\_pem": "-----BEGIN EXAMPLE CERTIFICATE-----\nMIIC+zCCAeOgAwIBAgIBADANBgkqhkiG9w0BAQsFADAfMQswCQYDVQQGEwJVUzEQ\nMA4GA1UECgwHUGl2b3RhbDAeFw0xNzAxMTgyMTQyMjVaFw0yMTAxMTkyMTQyMjVa\nMB8xCzAJBgNVBAYTAlVTMRAwDgYDVQQKDAdQaXZvdGFsMIIBIjANBgkqhkiG9w0B\nAQEFAAOCAQ8AMIIBCgKCAQEAyV4OhPIIZTEym9OcdcNVip9Ev0ijPPLo9WPLUMzT\nIrpDx3nG/TgD+DP09mwVXfqwBlJmoj9DqRED1x/6bc0Ki/BAFo/P4MmOKm3QnDCt\no+4RUvLkQqgA++2HYrNTKWJ5fsXmERs8lK9AXXT7RKXhktyWWU3oNGf7zo0e3YKp\nl07DdIW7h1NwIbNcGT1AurIDsxyOZy1HVzLDPtUR2MxhJmSCLsOw3qUDQjatjXKw\n82RjcrswjG3nv2hvD4/aTOiHuKM3+AGbnmS2MdIOvFOh/7Y79tUp89csK0gs6uOd\nmyfdxzDihe4DcKw5CzUTfHKNXgHyeoVOBPcVQTp4lJp1iQIDAQABo0IwQDAdBgNV\nHQ4EFgQUyH4y7VEuImLStXM0CKR8uVqxX/gwDwYDVR0TAQH/BAUwAwEB/zAOBgNV\nHQ8BAf8EBAMCAQYwDQYJKoZIhvcNAQELBQADggEBALmHOPxdyBGnuR0HgR9V4TwJ\ntnKFdFQJGLKVT7am5z6G2Oq5cwACFHWAFfrPG4W9Jm577QtewiY/Rad/PbkY0YSY\nrehLThKdkrfNjxjxI0H2sr7qLBFjJ0wBZHhVmDsO6A9PkfAPu4eJvqRMuL/xGmSQ\ntVkzgYmnCynMNz7FgHyFbd9D9X5YW8fWGSeVBPPikcONdRvjw9aEeAtbGEh8eZCP\naBQOgsx7b33RuR+CTNqThXY9k8d7/7ba4KVdd4gP8ynFgwvnDQOjcJZ6Go5QY5HA\nR+OgIzs3PFW8pAYcvWrXKR0rE8fL5o9qgTyjmO+5yyyvWIYrKPqqIUIvMCdNr84=\n-----END EXAMPLE CERTIFICATE-----\n"
}</pre>
This creates a new CA.

1. In the command line terminal, enter the following API call:  
<pre class="terminal">curl "http<span>s</span>://example.com/api/v0/certificate\_authorities" \ 
    -X GET \ 
    -H "Authorization: Bearer YOUR\_UAA\_ACCESS\_TOKEN"</pre> The new CA will display. It will be marked as inactive.

1. In Ops Manager, click **Apply Changes**.

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">curl "http<span>s</span>://example.com/api/v0/certificate\_authorities/example-cert-guid/activate" \ 
    -X POST \ 
    -H "Authorization: Bearer YOUR\_UAA\_ACCESS\_TOKEN" \ 
    -H "Content-Type: application/json" \ 
    -d '{}' 
</pre> 
The API returns a successful response. 
<pre class="terminal">HTTP/1.1 200 OK</pre>
This activates the new CA.

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">curl "http<span>s</span>://example.com/api/v0/certificate_authorities/active/regenerate" \ 
    -X POST \ 
    -H "Authorization: Bearer YOUR\_UAA\_ACCESS\_TOKEN" \ 
    -H "Content-Type: application/json" \ 
    -d '{}'
</pre> 
The API returns a successful response. 
<pre class="terminal">HTTP/1.1 200 OK</pre>
This regenerates all non-configurable certificates and applies the new CA to your existing Ops Manager Director.

1. In Ops Manager, click **Apply Changes**.

1. In the command line terminal, enter the following API call:  
<pre class="terminal">curl "http<span>s</span>://example.com/api/v0/certificate_authorities/:guid" \ 
    -X DELETE \ 
    -H "Authorization: Bearer YOUR\_UAA\_ACCESS\_TOKEN"
</pre> 
The API returns a successful response. 
<pre class="terminal">HTTP/1.1 200 OK</pre>
This deletes the old, inactive CA.

1. In Ops Manager, click **Apply Changes**.

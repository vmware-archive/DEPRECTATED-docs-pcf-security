---
title: Rotating Ops Manager Certificates 
owner: OpsMan / Security
---
Depending on the requirements of your deployment, you may need to rotate your CA certificates. Certificates can expire or fall out of currency, or your organization's security compliance policies may require you to rotate certificates periodically.

In a Pivotal Cloud Foundry (PCF) deployment, you can rotate the certificates using API calls in the command line. New certificates generated through the command line will use SHA-256 encryption.

Pivotal recommends using these APIs on environments that include at least one tile. The Ops Manager Director will pass new certificates to any component in an environment, so using an environment with at least one tile will make it simple to verify that you rotated the certificates successfully. 

<p class="note"><strong>Note</strong>: In this procedure, you will return to Ops Manager frequently to click <strong>Apply Changes</strong>. Clicking <strong>Apply Changes</strong> redeploys the Ops Manager Director and its tiles. If you apply your changes at each stage of this procedure, a successful redeploy will verify that the certificate rotation process is proceeding correctly.</p>

1. Open the command line terminal.

1. Open a web browser and navigate to Ops Manager.

1. In Ops Manager, click **Apply Changes**. 

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">POST /api/v0/certificate\_authorities/generate</pre> This creates a new certificate.

1. In the command line terminal, enter the following API call:  
<pre class="terminal">GET /api/v0/certificate\_authorities</pre> The new certificate will display. It will be marked as inactive.

1. In Ops Manager, click **Apply Changes**.

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">POST /api/v0/certificate\_authorities/:certificate\_authority\_guid/activate</pre> This activates the new certificate.

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">POST /api/v0/certificate\_authorities/active/regenerate</pre> This regenerates all non-configurable certificates, applying the new certificate to your existing Ops Manager Director.

1. In Ops Manager, click **Apply Changes**.

1. In the command line terminal, enter the following API call:  
<pre class="terminal">DELETE /api/v0/certificate\_authorities/:certificate\_authority\_guid</pre> This deletes the old, inactive certificate.

1. In Ops Manager, click **Apply Changes**.

Refer to the [Ops Manager certificate authority API docs](http://opsman-dev-api-docs.cfapps.io/#certificate-authorities) for more examples of this API.

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

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">POST /api/v0/certificate\_authorities/generate</pre> This creates a new CA.

1. In the command line terminal, enter the following API call:  
<pre class="terminal">GET /api/v0/certificate\_authorities</pre> The new CA will display. It will be marked as inactive.

1. In Ops Manager, click **Apply Changes**.

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">POST /api/v0/certificate\_authorities/:certificate\_authority\_guid/activate</pre> This activates the new CA.

1. In the command line terminal, enter the following API call with an empty request:  
<pre class="terminal">POST /api/v0/certificate\_authorities/active/regenerate</pre> This regenerates all non-configurable certificates and applies the new CA to your existing Ops Manager Director.

1. In Ops Manager, click **Apply Changes**.

1. In the command line terminal, enter the following API call:  
<pre class="terminal">DELETE /api/v0/certificate\_authorities/:certificate\_authority\_guid</pre> This deletes the old, inactive CA.

1. In Ops Manager, click **Apply Changes**.

Refer to the [Ops Manager certificate authority API docs](http://opsman-dev-api-docs.cfapps.io/#certificate-authorities) for more examples of this API.

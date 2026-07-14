# Configuring HTTPS using AWS Certificate Manager

This guide explains how to secure your Defguard deployment with HTTPS by using a public TLS certificate issued by AWS Certificate Manager (ACM). You will request a certificate for the domains used by Defguard Core and Defguard Edge, validate domain ownership via DNS, and attach the certificate to your CloudFormation stack using its ARN.

Once completed, AWS will automatically manage certificate provisioning and renewal, ensuring your Defguard instance is encrypted and trusted without manual certificate handling.

Go to AWS console and open the Certificate Manager service page.

Request a new certificate (if you don’t have one already).

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

A public certificate is enough.

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Specify the domains you will want to use for your Defguard instance (for accessing Defguard Edge and Defguard Core). Those domains should be the same as those you’ll use in `ProxyUrl` and `CoreUrl`.

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Next, you will need to validate your domain ownership by adding appropriate CNAME records in your DNS provider. Use the _CNAME name_ and _CNAME value_ values provided in the AWS console and set them in you domain’s DNS.

After you complete this step, your certificate can be used. Copy the ARN of your certificate and paste it into the `SSLCertificateArn` parameter in the CloudFormation template.

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

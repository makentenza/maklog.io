---
title: "Let's Encrypt ACME v2 API: free wildcards certificates and OpenShift"
date: 2018-01-09
tags: ["let's encrypt", "openshift", "wildcard", "acme", "free certificates", "letsencrypt"]
draft: false
---
Let's Encrypt has finally added (5 January 2018) wildcard certificates support to it's ACME Certificate Authority API, ACME v2. This is running in staging mode yet but is [now public](https://acme-staging-v02.api.letsencrypt.org/directory) to be tested.

This will be 'GA' by February the 27. Until that date, certbot will not be ACME v2 compatible. Wildcards can be requested using the [ACME v2 compatible clients](https://letsencrypt.org/docs/client-options/). For this post, I have used an ACME v2 compatible shell script, [acme.sh](https://github.com/Neilpang/acme.sh).

At this point, staging environment root certificate is not present in browser/client trust stores, so you will need to add the [temporal intermediate certificate](https://letsencrypt.org/certs/fakeleintermediatex1.pem) Let's Encrypt is putting in place in order to be recognised as a valid certificate by your browser.

Another limitation right now (this is a limitation on acme.sh script side) is the ability to request wildcards certificated directly using '*.domain.com'. It must contain both domain.com and *.domain.com at least. Hopefully, this will be solved soon, or you can just use another ACME v2 supported client.

Use the following steps to request a wildcard certificate:

To deploy this template, run the following:

1. Clone the branch called **_2_** from acme.sh repository. This is the actual branch where support to ACME v2 API is being added.

	```shell
		$ git clone https://github.com/Neilpang/acme.sh.git -b 2
  		$ cd acme.sh
	```

2. Add DNS API keys to the corresponding provider on ./dnsapi directory (using CloudFlare on this example).

	```shell
	$ egrep 'CF_Key|CF_Email'  dnsapi/dns_cf.sh | head -2
	  CF_Key="xxxxxxxxxxxxxxxxxxxxxxxxx"
	  CF_Email="xxxxxxxxxxxxxxx"
	```

3. Request the wildcard certificate with the required Subject Alternative Names (on the example I'm requesting 2 DNS Names, one for the OpenShift API and another one for the SSL application endpoints running on my OpenShift Cluster).

	```shell
	$ ./acme.sh --server https://acme-staging-v02.api.letsencrypt.org/directory --test --issue -d console.v37.maklog.xyz  -d *.apps.v37.maklog.xyz --dns  dns_cf
	```
4. Copy generated certs to your preferred location. Certificates will be created under ~/.acme.sh/yourdomain directory

Your wildcard is ready to be used now. Follow the documentation to deploy (https://docs.openshift.com/container-platform/latest/install_config/install/advanced_install.html#advanced-install-custom-certificates) or redeploy (https://docs.openshift.com/container-platform/latest/install_config/redeploying_certificates.html#redeploying-master-certificates) custom named certificates for your OpenShift public API, and custom wildcard certificates for your router (https://docs.openshift.com/container-platform/latest/install_config/router/default_haproxy_router.html#using-wildcard-certificates).

Once you have deployed the certificates on your OpenShift Cluster, and added the intermediate certificate (https://letsencrypt.org/certs/fakeleintermediatex1.pem) to your browser you will see valid certificates for both the public OpenShift API and any application deployed using the router Edge termination.


![Public Master API](/img/ssl01.png "Public Master API")

![Router Certs](/img/ssl02.png "Jenkins Edge Termination")

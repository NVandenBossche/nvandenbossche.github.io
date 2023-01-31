---
title: "Protecting your Credentials"
date: 2023-01-31T20:06:47+01:00
tags: [salesforce, iam, identity, credentials]
thumbnail: "/images/posts/2022/conquering-salesforce-iam/IAM-Architect.png"
summary: "Understand how you can protect Salesforce and external credentials in your org."
series:
    - salesforce-iam
draft: false
---

> This blog post is the third post in a series. To start at the beginning, navigate [here](/posts/2022/conquering-salesforce-iam-first-steps/). You can find a full overview of all blog posts in this series here.

Quite often we hear news stories about hacks involving credentials such as username and password that are leaked. Those credentials give access to key systems of the targeted organisation. The damage is often enormous, from impacting revenue streams to customer data leaks. It's no surprise that these types of vulnerabilities are in the [OWASP top 10](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/).

![Conquering Salesforce IAM - Protecting your Credentials, by Nicolas Vanden Bossche](/images/posts/2023/csi-protect-credentials/header.png)

Leaked credentials can also be a problem in a Salesforce implementation. Lucky for us, Salesforce provides ways to protect credentials and cryptographic keys. We'll look at an example where credentials are stored in the metadata. And how easy it is for attackers to obtain them. Afterwards, we'll run through the actions you can take to protect your org.

## Hypothetical Scenario

Let's imagine a scenario where we have a developer named Alice. She is working on an integration between Salesforce and an external webservice. She needs to authenticate by creating a HTTP request with an authentication header. This header requires her to specify the username and password of a technical user.

She's had some experience with Apex development. Every good developer knows that hardcoding values is never recommended. So instead she copies the username and password over in a Custom Metadata Type (CMT). She then references this CMT in her Apex code and is able to connect to her webservice. A job well done!?

![A screenshot of a custom metadata type where a username and password are stored.](/images/posts/2023/csi-protect-credentials/custom-metadata.jpg)

A day later, another developer named Bob is pulling down all changed metadata into his local IDE. Now we can see that Bob has access to these credentials as well. What if Bob is a bad actor? He can search through his local files for sensitive information. What if the code repository gets hacked? What if Bob gets targeted through social engineering? In all these cases, the credentials would be compromised.

![A screenshot of exposed username and password in the org's metadata.](/images/posts/2023/csi-protect-credentials/credentials-unsecure.jpg)

## The Right Way to Store Secrets in Salesforce

What could Alice have done to avoid these situations? No hardcoding was the right first choice, but it should go further than that. There should be no credentials saved in Salesforce.

Salesforce has a secure mechanisms to store credentials. Alice takes a deeper look at Named Credentials and External Credentials. They allow her to configure the endpoint authentication without exposing credentials. Instead, she can link to the Named Credentials in Apex and External Services.

![A screenshot of an external credential in a Salesforce org. You can see the OAuth 2.0 protocol being used.](/images/posts/2023/csi-protect-credentials/external-credentials.jpg)
![A screenshot of a named credential in a Salesforce org. You can see the link with the external credential, a specified endpoint and a client certificate.](/images/posts/2023/csi-protect-credentials/named-credentials.jpg)

## Prepare for the Worst

We should also take a look in the other direction. Other systems also have secret management solutions such as Azure Key Vault. What if those are not used to secure Salesforce credentials? How can we prevent that an attacker can do no damage, even with leaked credentials?

An obvious action is to enable Multi-Factor Authentication (MFA). This is actually [enforced](https://help.salesforce.com/s/articleView?id=release-notes.rn_security_mfa_auto_enablement_phase2.htm&release=242&type=5) by Salesforce as of the Spring ’23 release, but [not for all users](https://help.salesforce.com/s/articleView?id=000388806&type=1). Most notably, API-only users are excluded. We can further secure API-only users by taking the following steps:

-   Blocking the OAuth Username-Password flow.
-   Blocking the OAuth User Agent (Implicit Grant) flow.
-   High-assurance session for viewing connected app credentials (client ID and client secret).

![A screenshot of exposed username and password in the org's metadata.](/images/posts/2023/csi-protect-credentials/oauth-settings.jpg)

It's also crucial to provide security training and awareness to your users. This is sometimes seen as a nuisance, but it helps in reducing the attack surface. This can be training people on recognising phishing emails or making them aware of the dangers of social engineering.

## Create an Action Plan

Sometimes we cannot avoid bad actors to gain access to the system. Set up proper monitoring of your environment with Event Monitoring. This feature provides detailed logs of everything happening in your Salesforce org.

It's crucial to have a plan and act on the generated logs. Proactively, you can then set up rules to block suspicious activity with [Enhanced Transaction Security](https://help.salesforce.com/s/articleView?id=release-notes.rn_security_mfa_auto_enablement_phase2.htm&release=242&type=5). Reactively, you can set up alerts in Salesforce or integrate the logs with a [Security Information and Event Monitoring (SIEM)](https://en.wikipedia.org/wiki/Security_information_and_event_management) system to handle alerting there. Make sure to have a support organisation in place that can act on those alerts. This way you avoid that the alerts get sent to the Upside Down and are gobbled up by whatever monstrosity is hiding there.

## Conclusion

Saving credentials in easily accessible locations is never a good idea. This article showed you recommended alternatives. First, use security platform features to store them properly. Second, prevent unauthorised access even if bad actors can get a hold of your credentials. Finally, set up monitoring to get alerted about suspicious activity and act quickly.

_Thanks for reading! Let me know if you have any remarks, questions or suggestions. Always happy to discuss security on the Salesforce platform._

---
title: "The Attacker's Mindset"
date: 2022-12-29T19:23:26+01:00
series:
    - salesforce-iam
draft: false
---

Whenever I was studying topics related to Identity & Access Management (IAM), every resource I found was always very theoretical. It was difficult to try and imagine how to put the theory into practice.

After reading many theories, something strange started to happen in my brain. It was like I'd been digging a hole and suddenly I found a long-forgotten memory. This memory took me back to my days in university where our professor kept going on and on about [Alice and Bob](https://en.wikipedia.org/wiki/Alice_and_Bob). You guessed it, it was a course about network security.

The interesting part was that this professor had always been talking about the attacker before diving into the details of network security protocols. So I decided to revisit that approach and apply it to everything I could find related to Salesforce IAM. Theories began to make sense, and I could always relate it to a practical situation in which I could apply that theory.

![Conquering Salesforce IAM - The Attacker's Mindset, by Nicolas Vanden Bossche](/images/posts/2022/csi-attacker-mindset/header.png)

Of course this approach is not new, but it is kind of new to the Salesforce ecosystem. Salesforce admins, developers and architects are usually not trained in web or network security. We work together with security teams, and usually it's only because otherwise we won't go live with our Salesforce app. That's not a healthy situation.

Let's take a deeper dive into the attacker's mindset to understand the security responsibilities of Salesforce professionals.

## The Attacker's Motivation

Salesforce is a system that contains some of our most treasured data. As the platform evolves, we will have more data that will become more important to the business (Genie Lakehouse anyone?). You don't want malicious actors to get access to that data.

Attackers might have different motivations to get access to your systems and data. They can bring client lists to your competition. They can change data in subtle ways to sabotage your business. They can leak data and deal damage to your business' reputation or even open you up to lawsuits. They can shut down your e-commerce site during peak moments.

Lucky for us, a lot of the security aspects on Infrastructure, Platform and Software layers are already taken care of by the Salesforce Platform. These prevent many of these attacks, but there are some that we need to guard ourselves against as Salesforce practitioners and customers.

Looking at Identity & Access Management, what are the attacks that can occur and how can we secure against them?

## Attacker Model

When we are thinking about the types of attacks that can occur in a security context, we're building an "attacker model". Pick your most obscure hoodie, nerdiest glasses, and start reenacting your favourite hacker scene.

{{< youtube 0PxTAn4g20U >}}

Identity Management is all about making sure an actor (person or system) is who they say they are. A bad actor will try to make your Salesforce org believe that they are someone else. In doing so, they'll be authenticated as the impersonated user and get access to their resources. Some attackers will instead try preventing well-intentioned users from authenticating by overloading the Salesforce authentication server (denial-of-service).

Access Management on the other hand is making sure an authenticated user only has access to the resources they should have access to. Attackers want to get their hands on Connect App's access tokens or hijack user's sessions. This would lead them to have access to resources they don't own.

Finally, remember that bad actors could already have access to your Salesforce org! They could seek out weaknesses in your configuration such as violations against [Principle of Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Even without bad actors, users with elevated privileges could always make mistakes.

## Attacks Explained

We've stayed quite abstract up until now. I promised in my [first post](https://nicolasvandenbossche.com/posts/2022/conquering-salesforce-iam-first-steps/) that we'd make things approachable, so let's deliver on that promise! We'll dive into a couple of examples of what such attack patterns could look like.

One type of attacks are pure technology-based attacks that exploit weaknesses in code, infrastructure, network, or configuration. Not sufficiently securing your communications can lead to attackers sniffing your network with tools like [Wireshark](https://www.wireshark.org/). In such a scenario, requests and replies to and from Salesforce can be read, intercepted or even changed in transit.

![A screenshot of a Wireshark session monitoring network traffic](/images/posts/2022/csi-attacker-mindset/wireshark.jpg "Wireshark screenshot - Source: https://www.infosecmatter.com/wp-content/uploads/2020/02/wireshark-80-http4.jpg")

Another type of attacks will combine the technology with social engineering. Attackers could be researching individuals who have access to your Salesforce orgs, carefully crafting scams or phishing attacks that are specific to the individual to get access to their credentials. Multi-factor authentication will help here, but things like MFA fatigue attacks are popping up to break those protections.

Finally there are attacks that will combine social engineering attacks to get access to trusted endpoints, and then leverage elaborate technology schemes using those compromised endpoints to get access to the real target. One example would be to get access to one Authorisation Server to mislead another one in an OAuth flow.

## Conclusion

We've taken a deeper look into the attacker's mind. They can be bad actors from inside or outside of your organisation. By reviewing their attacker model, we've seen that a wide variety of attacks can be launched and some examples of these attacks. This means that protecting our Salesforce org is not something trivial, even if the Salesforce platform already comes with many built-in protections.

Let me know on [LinkedIn](https://www.linkedin.com/in/nicolas-vanden-bossche/) or via [Twitter](https://twitter.com/nvandenbossche/) what you thought about this post. It can be about the content, style, approach, or anything else you'd like to share. In the next posts, we'll dive into specific attacks and how to prevent them.

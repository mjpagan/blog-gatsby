---
layout: blog
title: Follow-up to my Choose Your Own Disaster Presentation
date: 2023-12-05T18:31:08.404Z
img: chose-your-own-adventure-tree.jpg
tags:
  - security
  - presentation
---
I had a lot of fun sharing this presentation at nVision. The audience was invested and participated which made things engaging and fun. Year after year I am thrilled to see new and returning customers come to see my presentations. I know draw it’s the technical content but secretly I hope it’s the jokes :) 

If you wanted to view watch the presentation it will be posted online soon with the slides. We will share the link when it is ready.

When you have the slides, feel free to use them at your company to share what a security tabletop exercise is like. I think they're a lighthearted way to ease into more involved exercises.

I want to cover the last few slides from the presentation in this blog to highlight the key takeaways. 

## Is it legal to pay a ransom?

Everyone hates the "consultant's creed" answer of "it depends" but in this case it really does. Depending on your state, type of business, number of affected users, and group that is believed to have breached your security you may have different laws to navigate.

For example, [North Dakota Century Code Chapter 54-59.1](https://ndlegis.gov/cencode/t54c59-1.html) states:

>  "An entity shall disclose to the department an identified or suspected cybersecurity incident that affects the confidentiality, integrity, or availability of information systems, data, or services." Cybersecurity incidents required to be reported to the department include:
>
> 1. Suspected breaches.
> 2. Malware incidents that cause significant damage
> 3. Denial of service attacks that affect the availability of services; 
> 4. Demands for ransom related to a cybersecurity incident or unauthorized disclosure of     digital records; 
> 5. Identity theft or identity fraud services hosted by entity information technology systems
> 6. Incidents that require response and remediation efforts that will cost more than ten     thousand dollars in equipment, software, and labor
> 7. Other incidents the entity deems worthy of communication to the department."

Currently North Carolina, New York and Florida prohibit state agencies from paying a ransom.

Additionally, the U.S. Department of the Treasury’s Office of Foreign Assets Control (OFAC) has warned against US persons from facilitating ransomware payments to as doing so encourages more ransomware activity and may also risk violating OFAC regulations.

> "OFAC may impose civil penalties for sanctions violations based on strict liability, meaning that a person subject to U.S. jurisdiction may be held civilly liable even if it did not know or have reason to know it was engaging in a transaction with a person that is prohibited under sanctions laws and regulations administered by OFAC."

Source: https://ofac.treasury.gov/recent-actions/20201001

An open-source project has created a map to keep track of various states laws and regulations: https://isitlegaltopay.com/

![Is  It Legal To Pay](static/img/image-20231115163648138.png)

## Be Prepared - Planning

* **Educate your employees** - Your employees are your last and maybe best line of defense against security incidents. Train them what they should and shouldn't be doing on the systems, and most importantly to notify IT if something is amiss. At the same time, do not treat them like children. The quickest way to drive your end users to work around your security controls or hide potentially bad things is to develop a culture of fear and punishment.  
* **Understand your environment/data** - Knowing what data is important and where it lives will be important during recovery. If everything is on one server, that's easy but if your data is spread across multiple servers and applications, knowing where your data lives and its value to the company will help you to be able to correctly prioritize its protection and recovery. 
* **Store incident response plan off-network** - It is hard to open an incident recovery plan if it's on the server that is encrypted or on a cloud drive if you have turned off the internet. Keeping a copy offsite, knowing where it lives and having it available when you need it vital to a successful recovery.
* **Have a communications plan** - If you have been breached, you need to assume that the attackers are monitoring your internal communications. You need to assume that your messaging platform and email server is compromised and use an out-of-band communication plan. Planning to use brand new external email accounts (Gmail, Outlook, Proton Mail, etc.) will keep everyone on the same page and your communications private.
* **Know your cybersecurity insurance and law enforcement contacts** - Don't have the first time you reach out to your cybersecurity insurance company and/or local law enforcement be when your are in the middle of an incident. Reach out ahead of time and make sure you have the correct contact information, and they have your so the initial contact is quick so you can get to work faster.
* **Consider cybersecurity IR service retainer** - Cybersecurity companies are now offering their services on retainer. The benefit for you is that you've secured a company's services, and that company will be better prepared to help you. If you don't have an incident response team on retainer, you will need to contact one, describe the situation, agree to a scope of work, get the contact signed and all of that takes time. Also make sure whomever you use for your incident response work is approved by your cybersecurity insurance company or you may end up paying for that bill yourself. 
* **Complete a risk assessment** - Hire a company to help you assess your cybersecurity risks or complete a self-assessment. Learn where you are weak and strong in your security practices so know what you should be working on improving.

## Be Prepared - Technical

* **Follow good security guidelines: CISA, CIS, NIST CSF, etc.** - You don't have to build your security program from scratch. There are also several security frameworks available that you can borrow from. I'll admit they aren't page turners, but they have actionable steps to improve your security. Also, sometimes it's easier to get policies and projects approved if government agencies names are on the supporting documentation.
* **Implement multi-factor authentication** - You've probably heard this advice, but have you done it? All administrative portals should have multi-factor authentication. Your users Microsoft 365 need it too. We get tickets from our customers each month of compromised email accounts every month. This leads to ACH fraud and in many cases thousands of dollars of losses. Multifactor authentication is included in Microsoft 365 plans, and you need to use it. 
* **Monitor vulnerability lists** - Waiting for a forensics report to tell you that you were hacked by a known exploited vulnerability is too late. One place that you can get more information regarding new vulnerabilities is the Cybersecurity & Infrastructure Security Agency (CISA). CISA is a great source of information, you can sign up for emailed bulletins or RSS feeds so you can keep an eye out for vulnerabilities affecting your hardware and software.
* **Update your systems** - Software and hardware patches and upgrades fix bugs and security vulnerabilities. That said, keeping up with everything that needs to be patched is exhausting. That's why you need a plan. Start with simple rules: 

  * Externally facing devices are patched as soon as is reasonable
  * Remote code execution (RCE) vulnerabilities are patched first (especially if externally facing)
  * Critical vulnerabilities(CVSS score 9.0 - 10.0) get applied within 24 - 48 hours
  * High vulnerabilities (CVSS score 7.0 - 8.9) get applied within 7 days
  * Other vulnerabilities (CVSS score 0.0 - 6.9) are applied during normal product patching
* **Filter internet egress traffic** - This one is easy to overlook. Everyone has firewall rules that only allow certain traffic inbound to your network, but what about outbound? Unfettered egress traffic can make it very easy for a bad actor to download anything they need and *upload* anything they want. Controlling what goes **out** of your network is as important as what comes **in**.
* **Follow 3-2-1-1-0 backup strategy** - Try to follow the modern backup strategy 3-2-1-1-0. **Three** copies of your data, on **two** different pieces of hardware, **one** that is offsite, **one** that is immutable, with **zero** failed backups. This is a lofty goal but one that may save your bacon one day.
* **Test your backups** - Just because your backup jobs are successful, it doesn't guarantee they contain all your data or that you know all the quirks that may be involved in restoring your environment. Practice makes perfect. Backups should be tested annually, preferrable with a full restore test if you have the capabilities.

## Links

* [CISA Tabletop Exercise Packages | CISA](https://www.cisa.gov/resources-tools/services/cisa-tabletop-exercise-packages)
* [Incident Reporting System | CISA](https://www.cisa.gov/forms/report)
* [Advisory on Potential Sanctions Risks for Facilitating Ransomware Payments (treasury.gov)](https://ofac.treasury.gov/media/48301/download)
* [Computer Crime Statutes (ncsl.org)](https://www.ncsl.org/technology-and-communication/computer-crime-statutes)
* [Incident Response Plan Template | ](https://frsecure.com/incident-response-plan-template/)[FRSecure](https://frsecure.com/incident-response-plan-template/)
* [Ransomware Response Playbook | ](https://frsecure.com/ransomware-response-playbook/)[FRSecure](https://frsecure.com/ransomware-response-playbook/)
* [Interactive Map of Countries that Will Allow You to Pay Ransomware or Extortion Demands (isitlegaltopay.com)](https://isitlegaltopay.com/)

## Wrap-up

If you attended the session at nVision, I hope you found it interesting and that this blog gives you some ideas on how to improve your cybersecurity posture. If you missed out on the session and are interested in viewing it, it will be posted on our YouTube channel shortly. I will add links to this blog post when they're available.

As always, I appreciate when anyone comes to any of my sessions and I hope you found it useful and entertaining. If you have any feedback on the session or any ideas for future presentations let me know.

And for anyone who was curious, the wrapped giveaways were actually reprints of the original Choose Your Own Adventure books and not vegan cookbooks. That would be mean :)
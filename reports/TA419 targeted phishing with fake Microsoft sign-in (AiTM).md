# Incident Analysis: TA419 targeted phishing with fake Microsoft sign-in (AiTM)

**Date written:** October 27, 2026  
**Incident type:** Phishing (targeted, adversary-in-the-middle)  
**Sources:** NeoTeo news report of October 1, 2026, which summarizes a cybersecurity analysis (links in section 8)

## 1. Executive summary

A phishing campaign that began on July 8, 2026 targeted AI policy researchers at U.S. think tanks, universities and law firms. The emails pretended to come from well-known former officials and invited people to join a committee or contribute to a report. Anyone who replied received a link that led to a fake OneDrive sign-in page built to steal passwords, MFA codes and session cookies. No successful compromise was reported. The key lesson is that a normal-looking first email can be harmless, and the dangerous link only arrives after the victim replies.

## 2. Timeline

| Date | What happened |
|---|---|
| February 2026 | A separate campaign used an email pretending to be a senior employee of an AI company, sent to an analyst at a think tank. |
| July 8, 2026 | The campaign described in this report began. |
| July 9, 2026 | Two recipients separately contacted one of the impersonated people to ask if the emails were real. She said they were fraudulent and warned colleagues. |
| October 1, 2026 | A cybersecurity analysis linked the activity to a group called TA419 and described the sign-in flow. |

## 3. Attack path (MITRE ATT&CK)

These mappings are my own, based on the behavior described in the report. They are not taken from the source.

| Stage | What happened | Technique ID |
|---|---|---|
| Initial contact | Email impersonating a trusted public figure, inviting the target to join a committee or contribute to a report | T1656 Impersonation |
| Delivery | After a reply, a follow-up email contained a shortened link | T1566.002 Spearphishing Link |
| Credential theft | A fake OneDrive sign-in page relayed the real login to capture the password and MFA code | T1557 Adversary-in-the-Middle, T1111 MFA Interception |
| Session theft | The flow was designed to capture session cookies, which can bypass MFA | T1539 Steal Web Session Cookie |
| Access (if it had worked) | Use of stolen cloud account access | T1078.004 Cloud Accounts |

## 4. Indicators of compromise

The news report I used does not list any domains, IP addresses or file hashes. To fill this section, find the original cybersecurity analysis and copy only the indicators it publishes, written in defanged form (for example 1[.]2[.]3[.]4).

| Indicator | Type | Context |
|---|---|---|
| (add from the original analysis) | | |

## 5. Detection opportunities

These are my own suggestions for a SOC using Microsoft 365 and Entra ID logs.

| Stage | Log source | Detection idea |
|---|---|---|
| Lure and delivery | Email gateway logs | External sender that gets a reply from an internal user, followed by a message containing a shortened URL |
| Redirect and fake page | Proxy and DNS logs | User visits a newly registered domain through a redirect chain, then a page that looks like a Microsoft sign-in but is not on a Microsoft domain |
| Credential and MFA theft | Entra ID sign-in logs | Successful sign-in right after the click from a new IP address, new country, or unusual user agent |
| Session theft | Entra ID sign-in logs | Same session or token used from two different IP addresses or locations within a short time |
| Account takeover | M365 audit logs | New mailbox forwarding rule, new MFA method added, or new app consent soon after a risky sign-in |
| User reporting | Phishing report mailbox | Several users reporting the same sender, a sign that a campaign is active |

## 6. Response actions

- **Containment:** If a user entered credentials, reset the password, revoke all active sessions and refresh tokens, and block the malicious domains at the proxy and email gateway.
- **Eradication:** Check the account for new inbox rules, forwarding, added MFA methods and app consents, and remove anything the user did not create.
- **Recovery:** Move users toward phishing-resistant sign-in such as FIDO2 security keys, and warn targeted groups about this lure.

## 7. Lessons learned and recommendations

1. The first email may contain no link at all, so a clean-looking first message does not mean the sender is safe.
2. One-time MFA codes alone do not stop adversary-in-the-middle phishing, because the fake page relays them in real time. Phishing-resistant MFA and conditional access policies help more.
3. Colleagues and the impersonated person spotted the fraud first. Make reporting easy and fast, and act when several users report the same sender.
4. This report is based on a news article about someone else's analysis, and the link to a specific group is that analyst's assessment. Find and cite the original analysis before relying on it.

## 8. References

- NeoTeo: https://www.neoteo.com/en/a-reported-july-phishing-campaign-used-former-us-officials-identities-to-target-ai-policy-experts
- Original cybersecurity analysis (not named in the NeoTeo article): add the link after you find it

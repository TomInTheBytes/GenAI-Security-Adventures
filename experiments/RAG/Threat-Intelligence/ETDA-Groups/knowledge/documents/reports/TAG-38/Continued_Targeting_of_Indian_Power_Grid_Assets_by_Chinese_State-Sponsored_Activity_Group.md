# Reference for threat actor for "TAG-38"

**Title**: Continued Targeting of Indian Power Grid Assets by Chinese State-Sponsored Activity Group

**Source**: https://www.recordedfuture.com/continued-targeting-of-indian-power-grid-assets/

## Content
Continued Targeting of Indian Power Grid Assets by Chinese State-Sponsored Activity GroupCareersContact UsLoginPlatformSolutionsProductsServicesResearchResourcesCompanyGet a demo
Book a demo
BlogContinued Targeting of Indian Power Grid Assets by Chinese State-Sponsored Activity GroupPosted: 6th April 2022By: INSIKT GROUP
Editor’s Note: The following post is an excerpt of a full report. To read the entire analysis, click here to download the report as a PDF.
This report details a campaign conducted by a likely Chinese state-sponsored threat activity group targeting the Indian power sector. The activity was identified through a combination of large-scale automated network traffic analytics and expert analysis. Data sources include the Recorded Future Platform, SecurityTrails, PolySwarm, Team Cymru’s Pure Signal™, and common open-source tools and techniques. The report will be of most interest to individuals engaged in strategic and operational intelligence relating to Indian and Chinese cyber activity. Recorded Future notified the appropriate Indian government departments prior to publication of the suspected intrusions to support incident response and remediation investigations within affected organizations. With thanks to our colleagues at Dragos for early sharing and collaboration.
Executive Summary
In February 2021, Recorded Future’s Insikt Group reported on intrusion activity targeting operational assets within India’s power grid that we attributed to a likely Chinese state-sponsored threat activity group we track as RedEcho. Following a short lull after the publication of our RedEcho reporting, we have detected ongoing targeting of Indian power grid organizations by China-linked adversaries, frequently using the privately shared modular backdoor ShadowPad. ShadowPad continues to be employed by an ever-increasing number of People’s Liberation Army (PLA) and Ministry of State Security (MSS)-linked groups, with its origins linked to known MSS contractors first using the tool in their own operations and later likely acting as a digital quartermaster. 
In recent months, we observed likely network intrusions targeting at least 7 Indian State Load Despatch Centres (SLDCs) responsible for carrying out real-time operations for grid control and electricity dispatch within these respective states. Notably, this targeting has been geographically concentrated, with the identified SLDCs located in North India, in proximity to the disputed India-China border in Ladakh. One of these SLDCs was also targeted in previous RedEcho activity. This latest set of intrusions, however, is composed of an almost entirely different set of victim organizations. In addition to the targeting of power grid assets, we also identified the compromise of a national emergency response system and the Indian subsidiary of a multinational logistics company by the same threat activity group. To achieve this, the group likely compromised and co-opted internet-facing DVR/IP camera devices for command and control (C2) of Shadowpad malware infections, as well as use of the open source tool FastReverseProxy (FRP).
Despite a partial troop disengagement between India and China from February 2021, the prolonged targeting of Indian critical infrastructure continues to raise concerns over pre-positioning activity being conducted by Chinese adversaries. While this latest activity displays targeting and capability consistencies with previously identified RedEcho activity, there are also some notable distinctions. At this time, we have not identified technical evidence allowing us to attribute it to RedEcho, and we are currently clustering this latest activity under the temporary group name Threat Activity Group 38 (TAG-38).
Key Judgments

Given the continued targeting of State and Regional Load Despatch Centres in India over the past 18 months, first from RedEcho and now in this latest TAG-38 activity, this targeting is likely a long-term strategic priority for select Chinese state-sponsored threat actors active within India.
The prolonged targeting of Indian power grid assets by Chinese state-linked groups offers limited economic espionage or traditional intelligence-gathering opportunities. We believe this targeting is instead likely intended to enable information gathering surrounding critical infrastructure systems or is pre-positioning for future activity.
The objective for intrusions may include gaining an increased understanding into these complex systems in order to facilitate capability development for future use or gaining sufficient access across the system in preparation for future contingency operations.


Figure 1: High-level TAG-38 TTPs and Recorded Future data sourcing graphic (Source: Recorded Future)
Background

Figure 2: Timeline of Insikt research on Chinese state-sponsored groups targeting India versus geopolitical events (Source: Recorded Future)
India continues to be a major target of Chinese cyber espionage activity, as detailed in historical Recorded Future reporting on RedDelta, RedEcho, RedFoxtrot, TAG-28, and additional client-facing research. Although tensions reduced, aided by partial troop disengagement, in February 2021 following prolonged border stand-offs in the Ladakh region, there has been limited progress between the states regarding respective territorial claims. 
Our February 2021 RedEcho report highlighted the compromise of 10 distinct Indian power sector organizations, including 4 of the 5 of the country’s Regional Load Despatch Centres (RLDC), 2 ports, a large generation operator, and other operational assets. These assets offer minimal value as economic espionage or other traditional intelligence targets, which led us to assess a likely goal of pre-positioning network access to support Chinese strategic objectives. Following that February 2021 report, we observed the group abandon the operational infrastructure highlighted and shift its infrastructure modus operandi. Despite this, evidence of targeting of Indian power assets and organizations with links to critical infrastructure from Chinese state-sponsored actors continued. This included the targeting of an Indian managed service provider (MSP) and operational technology (OT) vendor using ShadowPad, which aligns with activity described in recent Dragos reporting. We attribute this particular activity to a separate activity group we track as Threat Activity Group 26 (TAG-26). We have observed TAG-26 targeting multiple high-value organizations in India using ShadowPad, Poison Ivy, and the RoyalRoad RTF weaponized. 
The use of ShadowPad across Chinese activity groups continues to grow over time, with new clusters of activity regularly identified using the backdoor as well as continued adoption by previously tracked clusters. At this time, we track at least 10 distinct activity groups with access to ShadowPad, which is assessed to have likely been originally developed and used by MSS-linked contractors linked to the APT41 (BARIUM) intrusion set.
Editor’s Note: This post is an excerpt of a full report. To read the entire analysis, click here to download the report as a PDF.
About usIntelligence CloudServices & SupportResearchResourcesCompanyHelpful linksCareersContact UsGet a DemoThe Intelligence GraphJoin us onlineWant to learn more?Contact us today
Copyright © 2024 Recorded Future, Inc.Security FAQCookiesPrivacy PolicyTerms & Conditions
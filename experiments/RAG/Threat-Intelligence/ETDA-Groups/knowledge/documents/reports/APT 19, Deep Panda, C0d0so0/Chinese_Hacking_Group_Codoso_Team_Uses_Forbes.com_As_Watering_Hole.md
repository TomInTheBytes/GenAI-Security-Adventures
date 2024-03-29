# Reference for threat actor for "APT 19, Deep Panda, C0d0so0"

**Title**: Chinese Hacking Group Codoso Team Uses Forbes.com As Watering Hole

**Source**: https://www.darkreading.com/attacks-breaches/chinese-hacking-group-codoso-team-uses-forbescom-as-watering-hole-/d/d-id/1319059

## Content
Chinese Hacking Group Codoso Team Uses Forbes.com As Watering HoleDark Reading is part of the Informa Tech Division of Informa PLCInforma PLC|ABOUT US|INVESTOR RELATIONS|TALENTThis site is operated by a business or businesses owned by Informa PLC and all copyright resides with them. Informa PLC's registered office is 5 Howick Place, London SW1P 1WG. Registered in England and Wales and Scotlan. Number 8860726.Black Hat NewsOmdia CybersecurityNewsletter Sign-UpNewsletter Sign-UpCybersecurity TopicsRelated TopicsApplication SecurityCybersecurity CareersCloud SecurityCyber RiskCyberattacks & Data BreachesCybersecurity AnalyticsCybersecurity OperationsData PrivacyEndpoint SecurityICS/OT SecurityIdentity & Access Mgmt SecurityInsider ThreatsIoTMobile SecurityPerimeterPhysical SecurityRemote WorkforceThreat IntelligenceVulnerabilities & ThreatsWorld Related TopicsDR GlobalMiddle East & AfricaSee AllThe EdgeDR TechnologyEventsRelated TopicsUpcoming EventsWebinarsSEE ALLResourcesRelated TopicsLibraryNewslettersReportsVideosWebinarsWhitepapers    Partner Perspectives:> MicrosoftSEE ALLSponsored ByCyberattacks & Data BreachesASLR vulnerability patched today used in tandem with previously patched Flash vuln to carry out drive-by-downloads against political and economic targetsEricka Chickowski, Contributing WriterFebruary 10, 20153 Min ReadAnother day, another cyberespionage campaign attributed to a Chinese hacking group. Today's newly identified hacking push is a watering hole attack against Forbes and other targets last November that's been attributed by iSIGHT Partners and Invincea to likely be the handiwork of a long-running group they call Codoso Team, but which has also been named as Sunshop Group. The campaign was made possible by a zero-day attack that strung together a now-patched Adobe vulnerability with a bypass vulnerability in Microsoft's ASLR technology for Internet Explorer that the company patched today.Research evidence only showed the attack to occur over a couple of days, but in addition to some highly targeted web properties it infected the Thought of the Day widget on Forbes.com with the intent to perform drive-by-download attacks via the Flash vulnerability. In spite of the mainstream appeal via Forbes, which is ranked by Alexa as the 61st most popular website on the Internet, the targets of this attack were fairly narrow. Attackers seemed to be going after defense sector firms, Chinese dissident groups and other political target, as well as certain financial targets and other commercial targets in pharmaceutical and energy sectors that could benefit the Chinese economy."So what’s really interesting about this is it separates a lot of cyber espionage activity from say criminal activity.  These guys don’t typically just put drive-bys anywhere," says John Hultquist, senior manager of cyber espionage threat intelligence for iSIGHT.  "They don’t want anybody’s information.  What they want is information associated with the requirements that they have.  Usually those requirements are gathering intelligence on intellectual property, gathering strategic intelligence, gathering information on say dissidents or security issues that they’re working."First publicly identified as the Sunshop Group by FireEye in 2013, Codoso Team has been on security research radars since 2010 as it perpetrated numerous targeted attacks using zero-day vulnerabilities."You may remember in 2010 the prize was actually awarded to a noted Chinese dissident," says Hultquist.  "Shortly after that these operators went in, popped the website, and used that website to serve up exploits to visitors, again a very targeted concept.  Since then they don’t only operate this way or through this manner, they’re also carrying out targeted spearphishing attacks." It also shares attack techniques with Deep Panda, which like Codoso, leans heavily on the use of the Derusbi malware to carry out attacks. While they may be sharing resources, iSIGHT believes them to be two distinct gangs.According to Anup Ghosh, CEO at Invincea, his team first noticed activity around the Forbes.com site through a defense firm customer. Typically used to tracking broad malvertising campaigns using similar media sites, his team was surprised to see the attack only going after specific customer types, primarily in the defense sector. He also says the attack was unique through the use of chained zero-day exploits. Not only was it attacking a Flash zero-day, but it was also leveraging a zero-day in ASLR to bypass that mitigation technique."Effectively in modern operating systems and browsers there is a layer of technology that Microsoft has added to the mix that really makes it much more difficult for a particular exploit to figure out what address base it’s operating in.  So it makes it more difficult or nearly impossible to execute a buffer overflow," explains Patrick McBride, vice president at iSIGHT. "In this case the team was able to exploit that ASLR, get outside of that box, if you will, and then directly exploit the flash vulnerability. " About the Author(s)Ericka Chickowski, Contributing WriterContributing Writer, Dark ReadingEricka Chickowski specializes in coverage of information technology and business innovation. She has focused on information security for the better part of a decade and regularly writes about the security industry as a contributor to Dark Reading.See more from Ericka Chickowski, Contributing WriterKeep up with the latest cybersecurity threats, newly discovered vulnerabilities, data breach information, and emerging trends. Delivered daily or weekly right to your email inbox.SubscribeYou May Also LikeMore InsightsWebinarsMaking Sense of Security Operations DataFeb 21, 2024Unbiased Testing. Unbeatable ResultsFeb 22, 2024Your Everywhere Security Guide: 4 Steps to Stop CyberattacksFeb 27, 2024API Security: Protecting Your Application's Attack SurfaceFeb 29, 2024Securing the Software Development Life Cycle from Start to FinishMar 06, 2024More WebinarsEventsCybersecurity's Hottest New Technologies - Dark Reading March 21 EventMar 21, 2024Black Hat Asia - April 16-19 - Learn MoreApr 16, 2024Black Hat Spring Trainings - March 12-15 - Learn MoreMar 12, 2024More EventsEditor's ChoiceFemale Cybersecurity Analyst or Manager in large Cyber Security Operations Center SOC handling ThreatsCybersecurity OperationsCISO Corner: DoD Regs, Neurodiverse Talent & Tel Aviv's Light RailCISO Corner: DoD Regs, Neurodiverse Talent & Tel Aviv's Light RailbyTara Seals, Managing Editor, News, Dark ReadingFeb 9, 20249 Min ReadA survey asking is stress good or bad Сloud SecurityIvanti Gets Poor Marks for Cyber Incident ResponseIvanti Gets Poor Marks for Cyber Incident ResponsebyBecky Bracken, Editor, Dark ReadingFeb 13, 20245 Min ReadPadlocks on a black background, indicating information securityCybersecurity OperationsCISO and CIO Convergence: Ready or Not, Here It ComesCISO and CIO Convergence: Ready or Not, Here It ComesbyArthur LozinskiFeb 13, 20244 Min ReadReportsIndustrial Networks in the Age of DigitalizationHow Enterprises Assess Their Cyber-RiskPasswords Are Passe: Next Gen Authentication Addresses Today's ThreatsThe State of Supply Chain ThreatsHow to Deploy Zero Trust for Remote Workforce SecurityMore ReportsWhite PapersUse the 2023 MITRE ATT&CK Evaluation Results for Turla to Inform EDR Buying DecisionsCauses and Consequences of IT and OT ConvergenceStrengthen Microsoft Defender with MDREndpoint Best Practices to Block Ransomware2023 Gartner Magic Quadrant for Single-Vendor SASEMore WhitepapersEventsCybersecurity's Hottest New Technologies - Dark Reading March 21 EventMar 21, 2024Black Hat Asia - April 16-19 - Learn MoreApr 16, 2024Black Hat Spring Trainings - March 12-15 - Learn MoreMar 12, 2024More EventsDiscover More With Informa TechBlack HatOmdiaWorking With UsAbout UsAdvertiseReprintsJoin UsNewsletter Sign-UpFollow UsCopyright © 2024 Informa PLC Informa UK Limited is a company registered in England and Wales with company number 1072954 whose registered office is 5 Howick Place, London, SW1P 1WG.Home|Cookie Policy|Privacy|Terms of Use
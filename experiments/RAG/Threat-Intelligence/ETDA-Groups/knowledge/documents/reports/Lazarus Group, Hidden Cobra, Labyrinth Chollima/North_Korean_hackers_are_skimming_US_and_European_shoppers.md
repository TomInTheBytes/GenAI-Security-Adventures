# Reference for threat actor for "Lazarus Group, Hidden Cobra, Labyrinth Chollima"

**Title**: North Korean hackers are skimming US and European shoppers

**Source**: https://sansec.io/research/north-korea-magecart

## Content
North Korean hackers are skimming US and European shoppersScan your store nowScan yourstore nowProductPricingResourcesResearchPartnersSupportMalwareCompanyContactProductPricingResourcesCompanyContactResearchPartnersSupportScan your store now!North Korean hackers are skimming US and European shoppersby Sansec Forensics TeamPublished in Threat Research − July 06, 2020North Korean state sponsored hackers are implicated in the interception of online payments from American and European shoppers, Sansec research shows. Hackers associated with the APT Lazarus/HIDDEN COBRA group were found to be breaking into online stores of large US retailers and planting payment skimmers as early as May 2019.North Korean state sponsored hackers are implicated in hacking U.S. retailers and intercepting customer paymentsPreviously, North Korean hacking activity was mostly restricted to banks and South Korean crypto markets^cryptohack, covert cyber operations that earned hackers $2 billion, according to a 2019 United Nations report^twobillion. As Sansec's new research shows, they have now extended their portfolio with the profitable crime of digital skimming.Sansec researchers have attributed the activity to HIDDEN COBRA^hiddencobra because infrastructure from previous operations was reused. Furthermore, distinctive patterns in the malware code were identified that linked multiple hacks to the same actor.HIDDEN COBRA & digital skimmingDigital skimming, also known as Magecart^magecart, is the interception of credit cards during online store purchases. This type of fraud has been growing since 2015 and was traditionally dominated by Russian-^russian and Indonesian-speaking^successgan hacker groups. This is no longer the case, as the incumbent criminals now face competition from their North Korean counterparts.In order to intercept transactions, an attacker needs to modify the computer code that runs an online store. HIDDEN COBRA managed to gain access to the store code of large retailers such as international fashion chain Claire's^claires. How HIDDEN COBRA got access is yet unknown, but attackers often use spearphishing attacks (booby-trapped emails) to obtain the passwords of retail staff.Using the unauthorized access, HIDDEN COBRA injects its malicious script into the store checkout page. The skimmer waits for keystrokes of unsuspecting customers. Once a customer completes the transaction, the intercepted data - such as credit card numbers - are sent to a HIDDEN COBRA-controlled collection server.Italian model agency as money muleCuriously, HIDDEN COBRA used the sites of an Italian modeling agency and a vintage music store from Tehran to run its global skimming campaign.To monetize the skimming operations, HIDDEN COBRA developed a global exfiltration network. This network utilizes legitimate sites, that got hijacked and repurposed to serve as disguise for the criminal activity. The network is also used to funnel the stolen assets so they can be sold on dark web markets. Sansec has identified a number of these exfiltration nodes, which include a modeling agency^lux from Milan, a vintage music store^darvish from Tehran and a family run book store^signedbooks from New Jersey.Technical analysisSansec monitors millions of online stores for skimming activity and typically finds 30 to 100 infected stores per day. Many cases have a common modus operandi, such as shared infrastructure or striking features in programming style. These traits can be obvious, such as the debug message "Success bro" that led to the arrest of three Indonesians in December^successgan. However, sometimes they are more subtle, as is the case with HIDDEN COBRA.Sansec research has identified multiple, independent links between recent skimming activity and previously documented North Korean hacking operations. The following diagram shows (a small subset of) victim stores in green and HIDDEN COBRA controlled exfiltration nodes in red. Yellow indicates a uniquely identifying modus operandi (or TTP), which will be discussed next.The first campaign: clienToken=<script src="https://www.luxmodelagency.com/wp-includes/js/customize-gtag.min.js"></script>
On June 23rd, 2019, Sansec discovered a skimmer on a US truck parts store that uses a compromised Italian modeling site^jit to harvest payment data. The injected script customize-gtag.min.js^lux1 is scrambled with a popular Javascript obfuscator^obfuscator. Hidden in the code, the string WTJ4cFpXNTBWRzlyWlc0OQ== is found, which is the double-base64 encoded representation of clientToken=. This particular keyword is later used as HTTP GET parameter to send the stolen payload to the collector exfiltration node. The specific encoding and the attempt to disguise the stolen payload as "clientToken" form a uniquely identifying characteristic.The malware was removed within 24 hours but a week later, the very same malware resurfaced on the same store. This time, it used a New Jersey book store to harvest credit cards^jit2:<script src="https://www.signedbooksandcollectibles.com/js/gmaps.min.js"></script>
During the following months, Sansec discovered the same malware on several dozen stores. Each time, it uses one of these hijacked sites as loader and card collector:stefanoturco.com (between 2019-07-19 and 2019-08-10)
technokain.com (between 2019-07-06 and 2019-07-09)
darvishkhan.net (between 2019-05-30 and 2019-11-26)
areac-agr.com (between 2019-05-30 and 2020-05-01)
luxmodelagency.com (between 2019-06-23 and 2020-04-07)
signedbooksandcollectibles.com (between 2019-07-01 and 2020-05-24)
The second campaign: __preloaderIn February and March 2020, several domain names were registrered that closely resemble popular consumer brands:2020-02-10 PAPERS0URCE.COM
2020-02-26 FOCUSCAMERE.COM
2020-03-21 CLAIRES-ASSETS.COM
Subsequently, Sansec found the web stores of the three corresponding brands compromised with payment skimming malware installed. The anonymously registered domains were used as loader and card collector^focuscamera.The three malware cases not only share infrastructure (domain registrar & DNS service), but they also share a particularly odd code snippet, that Sansec has not observed anywhere else. The three relevant malware segments are displayed below for reference. Common behavior: upon form submission a hidden, dynamic image is added to the page with the deceptive name __preloader. The image address is controlled by the attacker, and the intercepted and encoded payload is sent as argument to this image, along with several random numbers. Immediately, the dynamic image is removed from the page, so the theft is invisible to the customer. Sansec has previously discussed this exfiltration method when it first reported on the Claire's hack on June 15th^claires.(code slightly modified for readability)The common code, behavior, registrar and DNS server are unique traits that link these cases to the same source.The North Korean linkAs the diagram above shows, Sansec has established multiple, independent links to previously documented North Korean hacking activity. We will discuss each link separately.technokain.comSouth Korea based EST Security has published two articles^alyac2416 ^alyac2418 documenting a North Korea-attributed attack where a malicious loader is embedded in Korean office documents. The loader installs remote access software for Windows on the victim's computer, which is downloaded from this address:2019-07-12 https://technokain.com/ads/adshow1.dat
Additionally, US-based security firm Rewterz reported a spearphishing attack targeting attendees of the annual Consumer Electronics Show in Las Vegas^rewterz. This attack uses malware from the same address.These attacks took place on July 11 and 12th, less than a week after the placement of a skimmer on the same site:2019-07-06 https://technokain.com/vendor/jquery.validate.min.js
darvishkhan.netBoth Fortiguard Labs^forti and EST Security^alyac2397 document a DPRK-attributed spearphishing campaign that took place between June 26th and July 2nd 2019. The campaign used malicious Korean office documents containing malware installers, where remote access software was downloaded from:2019-06-27 https://darvishkhan.net/wp-content/uploads/2017/06/update6.dat
Two weeks earlier, multiple digital skimmers were launched from the same site, harvesting credit cards from several US, UK and Australia-based stores:2019-06-12 https://darvishkhan.net/wp-includes/js/hotjar.min.js
2019-06-14 https://darvishkhan.net/wp-includes/js/dist/gtm.min.js
areac-agr.comOn October 25, Beijing-based Netlab360 discovered a novel remote access trojan (RAT) that showed multiple similarities with previously DPRK attributed malware^dacls. Components of the tool were loaded from2019-10-25 http://www.areac-agr.com/cms/wp-content/uploads/2015/12/check.vm
Before and after the presence of this malware, digital skimmers were hosted on the same site, that would intercept payments from multiple American stores:2019-08-16 https://www.areac-agr.com/cms/wp-includes/Requests/Security1.3.min.js
2020-05-01 https://www.areac-agr.com/cms/wp-includes/Requests/Utility/json.min.js
papers0urce.comThe three malware domains from the __preloader campaign use distinct IPs. One of them - papers0urce.com - uses an address from Dutch ISP Leaseweb, 23.81.246.179. This IP is not known to have been used for other domain names since 2015^papers0urcevt, however, it is featured in the same North Korea research as the previously discussed areac-agr.com^dacls. The IP is hardcoded in the RAT, and used as a command and control (C2) server.DiscussionDoes the usage of common loader sites, and the similarity in time frame, prove that the DPRK-attributed operations are run by the same actor as the skimming operations? Theoretically, it is possible that different nefarious actors had simultaneous control over the same set of hijacked sites, but in practice, this would be extremely unlikely. First, thousands of sites get hacked each day^sophos, making an overlap highly coincidental. Secondly, when a site gets hacked, it is common practice for a perpetrator to close the exploited vulnerability after gaining access, in order to shield the new asset from competitors.ConclusionSansec has found proof of global skimming activity that has multiple, independent links to previously documented, North Korea attributed hacking operations. Sansec believes that North Korean state sponsored actors have engaged in large scale digital skimming activity since at least May 2019.Read moreSansec and Europol counter online skimmingMagento wish list exploit bypasses WAF protectionIs your store’s newsletter being used for phishing?Malware Persistence via Telegram and GitHubPostponed Exfiltration Evades DetectionIn this articleHIDDEN COBRA & digital skimmingItalian model agency as money muleTechnical analysisThe first campaign: clienToken=The second campaign: __preloadertechnokain.comThe North Korean linkdarvishkhan.netareac-agr.compapers0urce.comDiscussionConclusionWhat is Magecart?Also known as digital skimming, this crime has surged since 2015. Criminals steal card data during online shopping. Who are behind these notorious hacks, how does it work, and how have Magecart attacks evolved over time?About MagecartScan your store nowfor malware & vulnerabilities$ curl ecomscan.com | sheComscan is the most thorough security scanner for Magento, Adobe Commerce, Shopware, WooCommerce and many more.Learn moreMade with ❤Sansec BVWolvenplein 25 - S.43512 CK UtrechtThe Netherlands[email protected]ProductPricingSupportPartners About Magecart Malware library Media coverage System statusResearchCompanyContact Telegram LoginStay up to date with the latest eCommerce attacksexperts in eCommerce securityTerms & Conditions|Privacy & Cookie Policy|Company Reg 77165187|Tax NL860920306B01
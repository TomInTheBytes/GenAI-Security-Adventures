# Reference for threat actor for "APT 41"

**Title**: ShadowPad: new activity from the Winnti group

**Source**: https://www.ptsecurity.com/ww-en/analytics/pt-esc-threat-intelligence/shadowpad-new-activity-from-the-winnti-group/

## Content











ShadowPad: new activity from the Winnti group


















































Home
AnalyticsPT ESC Threat IntelligenceShadowPad: new activity from the Winnti group 









ShadowPad: new activity from the Winnti group


Published on 29 September 2020






Contents


Introduction
Network infrastructure

Detecting ShadowPad
Links to other groups

TA459
Bisonal


Victims
Activity


Analysis of malware and tools

Analyzing SkinnyD
Analyzing xDII

Dropper
xDll backdoor


ShadowPad

ShadowPad loader and obfuscation
ShadowPad modules
ShadowPad configuration
Network protocol


Python backdoor
Utilities


Conclusion


Introduction
During threat research in March 20201, PT Expert Security Center specialists found a previously unknown backdoor and named it xDll, based on the original name found in the code. As a result of a configuration flaw of the malware's command and control (C2) server, some server directories were externally accessible. The following new samples were found on the server:

ShadowPad
A previously unknown Python backdoor
Utility for progressing the attack
Encrypted xDII backdoor

ShadowPad is used by Winnti (APT41, BARIUM, AXIOM), a group that has been active since at least 2012. This state-sponsored group originates from China2. The key interests of the group are espionage and financial gain. Their core toolkit consists of malware of their own making. Winnti uses complex attack methods, including supply chain and watering hole attacks. The group knows exactly who their victims are. They develop attacks very carefully and deploy their primary tools only after detailed reconnaissance of the infected system. The group attacks countries all over the world: Russia, the United States, Japan, South Korea, Germany, Mongolia, Belarus, India, and Brazil. The group tends to attack the following industries:

Gaming
Software development
Aerospace
Energy
Pharmaceuticals
Finance
Telecom
Construction
Education

The first attack with ShadowPad was recorded in 20173. This backdoor has been often used in supply chain attacks such as the CCleaner4 and ASUS5 hacks. ESET released its most recent report about Winnti activities involving ShadowPad in January 20206. We didn't find any connection with the current infrastructure. However, during research we found that the new ShadowPad infrastructure had commonalities with infrastructures of other groups, which may mean that Winnti was involved in other attacks with previously unknown organizers and perpetrators.
This report contains a detailed analysis of the new network infrastructure related to ShadowPad, new samples of malware from the Winnti group, and also analysis of ties to other attacks possibly associated with the group.
Network infrastructure
Detecting ShadowPad
Initially, when the xDll backdoor was analyzed (see Section 2.2), it could not be clearly tied to any APT group. The sample had a very interesting C2 server, www.g00gle_jp.dynamic-dns[.]net, which potentially could indicate attacks against Japan. When we studied the network infrastructure and searched for similar samples, we found several domains with similar names.


Figure 1. Network infrastructure of the Winnti group at the initial stage of analysis

The domain names give reason to suspect that attacks also target South Korea, Mongolia, Russia, and the United States. When we studied the infrastructure further, we found several simple downloaders unfamiliar to us (see Section 2.1). They contact related C2 servers, and in the response should receive a XORencrypted payload with key 0x37. The downloader we found was named SkinnyD (Skinny Downloader) for its small size and bare-bones functionality. The URL structure and some lines in SkinnyD make it very similar to the xDll backdoor.
At first, we could not obtain the payload for SkinnyD, because all C2 servers were inactive. But after a while, we found new samples of the xDII backdoor. When we analyzed one of the samples, we found some public directories on its С2 server. The file called x.jpg is an xDll backdoor encrypted with XOR with key 0x37. This suggests that xDll is a payload for SkinnyD.


Figure 2. Structure of public directories on the discovered C2 server

The most interesting thing on the server is the contents of the "cache" directory.


Figure 3. Contents of the "cache" directory

It contains data about the victims and the malware downloaded to infected computers. The name of the victim file contains an MD5 hash of the MAC address for the infected computer sent by xDll; the file contents include the time of the last connection to the C2 server. Based on the changes in the second part of the name of the malware file, server time might seem to be indicated in nanoseconds. But that cannot be true, since that would take us back all the way to March 1990. Ultimately, we don't know why this time period was selected.
In the malware files, we found ShadowPad, a previously unknown Python backdoor, and utilities for progressing the attack. Detailed analysis of the malware and utilities is provided in Section 2.
At certain intervals, the attackers request information from infected computers via the xDII backdoor. This information is saved to the file list.gif.
We should note that in the xDII samples we have, the Domain field contains the name of the domain where the infected computer is located. However, in the log that field for almost all computers contains the SID of the user whose name was used to launch xDII. That may be an error in the code of a certain xDII version, because this value does not provide any useful information to the attackers.


Figure 4. Example of lines from the log (for detailed description of parameter values, see xDII analysis)

Going deeper into the network infrastructure, we found that many servers have the same chain of SSL certificates with the following parameters:

Root: C=CN, ST=myprovince, L=mycity, O=myorganization, OU=mygroup, CN=myCA, SHA1=0a71519f5549b21510 410cdf4a85701489676ddb
Base: C=CN, ST=myprovince, L=mycity, O=myorganization, OU=mygroup, CN=myServer, SHA1=2d2d79c478e92a7 de25e661ff1a68de0833b9d9b



Figure 5. Parameters of the SSL certificate

We have encountered this certificate in several publications about ShadowPad attacks.
The first one is an investigation of the 2017 attack on CCleaner. Avast has provided details7 regarding the attack. A screenshot, included there, shows the same certificate.
The second is a talk by FireEye researchers at Code Blue 2019 about cyberattacks against Japanese targets8. In one of the attacks, the researchers found the use of POISONPLUG (the name for ShadowPad used by FireEye). Analysis of the infrastructure revealed the same certificate on ShadowPad C2 servers.


Figure 6. Slide from the FireEye presentation

Searching for servers with this certificate helped us not only detect new ShadowPad samples and C2 servers, but also find connections to other attacks previously not attributed to Winnti (see Section 1.2).
As a result, we found over 150 IP addresses with this certificate, or addresses where it had been installed previously. Of these, 24 addresses were active at the time of writing of this article. There were also 147 domains related to those addresses. For the domains, we found Winnti malware.
During our research, the group's domains relocated from one IP address to another many times, which is indicative of active attack operations.
However, the motive for using the same SSL certificate on almost all ShadowPad C2 servers was not clear. This may be the result of having the same system image installed on the C2 servers, or else simple overconfidence.
We saw the same thing with certificates when researching the activity of the TaskMasters9 group. At some point, the attackers started installing self-signed certificates with identical metadata on their servers, which ultimately helped us in finding their infrastructure.
The following figure shows distribution of detected IP addresses by location:


Figure 7. Geolocation of C2 servers

About half of the group's servers are located in Hong Kong. The IP addresses are distributed between 45 unique providers. More than half of the servers are concentrated on the IP addresses of six providers, five of which are in Asia (Hong Kong, China, and South Korea).
1.2. Links to other groups
1.2.1. TA459
In 2017, Proofpoint issued a report about attacks against targets in Russia and Belarus using ZeroT and PlugX.10 The report mentions the domain yandax[.]net, which was indirectly related to the infrastructure used in that attack. The domain was on the same IP address as one of the PlugX servers. WHOIS data of that domain looks as follows:


Figure 8. Registrant lookup for the domain yandax[.]net

In the past few years, the email address dophfg@yahoo[.]com has been used to register several more domains.


Figure 9. Domains with similar WHOIS data

In our study of ShadowPad infrastructure, we came across active servers linked to two domains from the group: www.ertufg[.]com and www.ncdle[.]net. Those servers also had the SSL certificate typical of ShadowPad. In addition, we found ShadowPad samples connecting to those domains. One of the samples had a rather old compilation date, July 2017. However, this time is probably not accurate, because the C2 server for it was registered in August 2018. It can also disguise itself as a Bluetooth Stack component for Windows by Toshiba named TosBtKbd.dll.


Figure 10. Structure of domains related to ShadowPad

Here we can make another inference. The domain yandax[.]net initially had a different email address in its WHOIS data: fjknge@yahoo[.]com. The same address was also used to register one of the NetTraveler C2 servers, namely, riaru[.]net. That domain was used for attacks targeting the CIS and Europe. These attacks have been described by Proofpoint researchers.11 It is also possible that the infrastructure was used by some other group to disguise its activities. However, the scope, targeted countries, and industries all overlap with those of the Winnti group. The connections are indirect and individual in nature, but still provide reason to believe that all these attacks were carried out by the same group.
1.2.2. Bisonal
On one of the IP addresses on ShadowPad infrastructure, we found domains used in Bisonal RAT attacks in 2015–2020.


Figure 11. ShadowPad and Bisonal domains sharing an IP address

In addition, we found a Bisonal sample with a direct relationship to the new ShadowPad infrastructure.


Figure 12. Bisonal and ShadowPad infrastructure

We came across a presentation12 made at JSAC 2020 by Hajime Takai, a Japanese researcher with NTT Security. The researcher details an attack on Japanese systems, in which the chain included xDII for downloading Bisonal to the infected computer.


Figure 13. Slide from Hajime Takai's research

Takai links the attack to the Bitter Biscuit campaign described by Unit 42.13 Bisonal was used in that attack, too. The attack tools found by Takai are almost completely identical to the ones we found on the ShadowPad server. Even some hash sums are identical (see Section 2).
Researchers believe14 that the Bisonal attacks were performed by Tonto Team. The group concentrates its efforts on three countries: Russia, South Korea, and Japan. Its targets include governmental entities, militaries, finance, and industry. All these fall within the area of interests of the Winnti group. And with the new details about Bisonal used together with xDII, plus overlapping network infrastructures, it stands to reason that the Winnti group is behind the Bisonal attacks.
1.3. Victims
According to the server data, more than 50 computers had been infected. We could not establish the exact location and industry for every infected computer. However, if we match the time of the latest connection of the infected computer to the server and the time we received the file with this timestamp, we can make a map of the timezones.


Figure 14. Map with victims' timezones

Most countries located in the timezones marked on the map are within the area of interest of Winnti.
We were able to identify some of the compromised organizations, including:

A university in the U.S.
An audit firm in the Netherlands
Two construction companies (one in Russia, the other in China)
Five software developers (one in Germany, four in Russia)

All victims, both identified and unidentified, were notified by the national CERTs
We have no details about those attacks. However, since ShadowPad was used in supply chain attacks via software developers, and knowing that at least two software developers have been compromised, we are dealing with either a new distribution attempt or an attack that is already in progress.
1.4. Activity
Activity on the server (such as collection of information from the victims and appearance of new utilities) usually took place outside of the business hours in the victims' timezones. For some, it was evening; for others, early morning. This tactic is typical of Winnti. The group did the same when they compromised CCleaner, as Avast reported.
2. Analysis of malware and tools
Judging by the data we collected, the delivery process in the current campaign looks as follows:


Figure 15. Payload delivery diagram

The compilation time of the malware samples we found corresponds to business hours in UTC+8 timezone (where China and Hong Kong are located).


Figure 16. Malware compilation time in UTC+0



Figure 17. Malware compilation time in UTC+8

2.1. Analyzing SkinnyD
SkinnyD (Skinny Downloader) is a simple downloader: it contains several C2 addresses and goes through them one by one.
The next stage is downloaded with a GET request to the С2 server via a special URL address generated according to a format string hard-coded in the malware code.


Figure 18. URL format string

The malware checks the data received from the C2 as follows:

The data size must be more than 0x2800 bytes.
The data must begin with the bytes "4D 5A" (MZ).

The downloaded binary file is decrypted with XOR and loaded with PE reflective loading. After the binary file loads, control transfers to the exported symbol MyCode.
The malware gains persistence via the key Environment\UserInitMprLogonScript.15


Figure 19. Persistence code

In the SkinnyD samples we studied, we found an interesting artifact linking it to xDII. This was the string "3853ed273b89687". Since the string is not used by the downloader, perhaps it's a builder artifact.
2.2. Analyzing xDII
2.2.1. Dropper
The dropper is an executable file written in C and compiled in Microsoft Visual Studio. Its compilation date (February 11, 2020, 9:54:40 AM) looks plausible.


Figure 20. General information about the dropper

It contains a payload in the form of the xDII backdoor in the data section.


Figure 21. Another executable file in the dropper

The dropper extracts 59,392 bytes of data and attempts to write this to one of two paths:

%windir%\Device.exe
%windir%\system32\browseui.dll

Next, it copies itself to the directory %windir%\DeviceServe.exe and creates a service named VService, thereby ensuring auto-launch as a service.


Figure 22. Installing the service

When the service runs, it creates a separate thread for running the payload.


Figure 23. Running the payload

We should note that there is no option to launch a different payload variant in the form of a DLL library (browseui.dll).
2.2.2. xDll backdoor
The backdoor is a file written in C++ and compiled in Microsoft Visual Studio using the MFC library. It also has a plausible compilation date of February 10, 2020, 6:14:37 PM.


Figure 24. General information about the payload

It creates a separate thread in which all actions take place.
It starts by scouting the system and collects the following information:

Computer name
IP address
OEM code page
MAC address (used later on to calculate the MD5 hash sum for C2 interactions


Figure 25. Obtaining MAC address

OS version


Figure 26. Obtaining OS version

The preset identifier "sssss" (probably characteristic of this particular version of the backdoor)
Whether the user is an admin


Figure 27. Checking privileges

Whether it is in a virtual environment


Figure 28. Checking the environment

Domain and username


Figure 29. Obtaining domain and username

CPU


Figure 30. Obtaining CPU information

RAM


Figure 31. Obtaining information about RAM

System language


Figure 32. Obtaining information about the system language


Next, the backdoor decrypts C2 server addresses. In this case, there are two, but they are identical: www.oseupdate.dns-dns[.]com. The backdoor body contains a third address (127.0.0.1), which is replaced with the decrypted one.


Figure 33. Decrypting C2 address

When the C2 server address is obtained, a GET request will be sent, with its format as follows: hxxp://{host}:{port}/{uri}?type=1&hash={md5}&time={current_time}. Request parameters are:

host (C2 address)
port (port 80)
uri (string "news.php")
md5 (hash sum of the MAC address, which is probably the victim's identifier)
current_time (current system time)

Here's how it all looks:


Figure 34. Sample request to the server

Note that the request uses a preset value for the HTTP User-Agent header:


Figure 35. Embedded User-Agent

The expected server response is the character "1". If that response is received, a POST request is sent with basic system information in JSON format:

Hash sum of the MAC address
Computer name
IP address
OS version
Domain name
Preset identifier "sssss"
OEM code page

Example request:


Figure 36. Sending system information

We should note that the JSON format used is incorrect. In addition, the value of the In_IP field is missing. Perhaps it was expected that both the internal and external IP addresses would be determined. But logic for determining the external address was not yet implemented in this variant of xDII. Another tell-tale detail is the value ("post_info") of the Referer HTTP header. In addition, a different value is selected for the User-Agent HTTP header:

Next comes the loop for processing C2 commands. For that purpose, the backdoor sends a GET request in a format matching the one described earlier. The only difference is that "type" parameter value is now "2" instead of "1":

The expected server response is a lowercase Latin letter (from a to z). The following table shows commands and the corresponding actions:

Successful execution of some commands requires additional data. For instance, downloading a file from the server (the "e" command) requires indicating the file name. In this case, the server provides that name after a comma. For instance, "e,dangerous_file.txt".
This is what a request and the response look like:


Figure 37. An example of a command for downloading a file

Next, the file is requested and its content is returned:


Figure 38. File content sent to the server

Then a report indicating successful download is sent.


Figure 39. Report on successful file download

Notice again the idiosyncratic value of the "Referer: upfile" field, the type of transmitted data (image/ pjpeg), and the name of the transmitted file: {md5}.gif (using the hash sum of the MAC address).
When the command for collecting the directory listing (the "d" command) is processed, the delineator is not a comma. Instead, the path to the catalog is expected to start from the second character, for instance: "d|C:\Users".


Figure 40. Directory listing

The data is transmitted in JSON format, and this time the format is correct.
The following example shows sending information obtained from system analysis (the "o" command).


Figure 41. Sending system information

The data is submitted in JSON format again, but with fewer keys.
The JSON string templates are specified in the backdoor; the string itself is formed by concatenation, without using any special libraries.
However, in some cases, when a brief report is sufficient, the information may be transmitted in plaintext.


Figure 42. Result of command for code execution

2.3. ShadowPad
As mentioned, we found some public directories on one of the xDll servers, and one of those directories contained ShadowPad. We found no significant differences from earlier versions, therefore the following is only a brief analysis of the new version.
2.3.1. ShadowPad loader and obfuscation
The first stage is decryption of the shell code responsible for installing the backdoor on the system. The shellcode is decrypted with an XOR-like algorithm, which modifies the encryption key at each iteration with arithmetic operations with certain constants.


Figure 43. Main module decryption cycle

After decryption, control transfers to the loader, which features a characteristic type of obfuscation.


Figure 44. Obfuscation used in the loader

We already saw this type of obfuscation in previous versions of ShadowPad. Certain bytes are inserted in various sections of the code pre-marked with two opposite conditional jumps pointing to the same address. To do away with this obfuscation, the indicated bytes must be replaced (with the "nop" opcode, for instance).
After the addresses of the API functions are received and the required code is placed in memory, control passes to the backdoor installation stage.
2.3.2. ShadowPad modules
Like the previous versions, this backdoor has a modular architecture. By default, the backdoor includes the following modules:


Figure 45. Calling the functions for decryption and decompression of the modules built into the backdoor





Module
                    name
                
ID
                
Compilation time
                


Root
                
5E6909BA
                
GMT: Wednesday, 11 March 2020 г., 15:54:34
                


Plugins
                
5E69097C
                
GMT: Wednesday, 11 March 2020 г., 15:53:32
                


Online
                
5E690988
                
GMT: Wednesday, 11 March 2020 г., 15:53:44
                


Config
                
5E690982
                
GMT: Wednesday, 11 March 2020 г., 15:53:38
                


Install
                
5E69099F
                
GMT: Wednesday, 11 March 2020 г., 15:54:07
                


DNS
                
5E690909
                
GMT: Wednesday, 11 March 2020 г., 15:51:37
                




The identifiers of these modules remain unchanged from version to version; they, too, are installed and run in a separate thread via the registry. Module compilation times can be found in the auxiliary header that comes before the shellcode.


Figure 46. Location of the compilation time in the shellcode header

A typical feature of any copy of ShadowPad is encryption of the strings in each module. The encryption algorithm is similar to the one used for backdoor decryption. The only difference is in the constants used for key modification.
The method of calling some API functions in ShadowPad modules is somewhat interesting. Some copies of the malware calculate the function address for each time a function is called, as shown in Figure 47. In addition, addresses of the functions to be called can be obtained via a special structure. Loading addresses for libraries are obtained based on the values of the structure members, to which the offsets of the required API functions are then added.


Figure 47. String decryption code in ShadowPad



Figure 48. Example of obfuscation of calling an API function



Figure 49. De-obfuscated calls (illustrated by Install module)

For persistence, the backdoor copies itself to C:\ProgramData\ALGS\ under the name Algs.exe and creates a service with the same name.


Figure 50. Service created for gaining persistence

The malware proceeds to launch a new svchost.exe process, which it injects with its own code and then gives control.


Figure 51. Code for creating process and injecting into it

2.3.3. ShadowPad configuration
In all samples of the backdoor, the configuration is encrypted. The Config module is responsible for operations with it.
Configuration is a sequence of encrypted strings, in which each string follows the previous one without any zero padding or alignment. The configuration is encrypted by the same algorithm as the strings.


Figure 52. Decrypted malware configuration

2.3.4. Network protocol
The format of the packets used in all ShadowPad versions has remained unchanged.16 For the packets sent to the server, the packet body and the packet header are generated separately. After they are concatenated (without any padding), the packet is covered with an encryption algorithm with logic close to that of the algorithms used for decrypting the main module and the strings inside the backdoor. Figure 53 shows the algorithm.


Figure 53. Packet encryption code used in exchanges with the C2 server

The structure of encrypted packets received from the C2 server is fairly simple (as illustrated by the Init packet).


Figure 54. Structure of ShadowPad packets

2.4. Python backdoor
This backdoor we found on the server was in py2exe format. The backdoor is written in Python 2.7 and contains configuration variables in the beginning.
Three commands can be executed remotely:

CMDCMD: execute via cmd.exe
UPFILECMD: upload the file to the server
DOWNFILECMD: download the file from the server

The ONLINECMD command is executed by the backdoor right after launch. This is a command for collecting system information and sending it to the server.


Figure 55. Backdoor configuration



Figure 56. Commands for collecting system information

The backdoor has a function for gaining persistence via the registry:

After gaining persistence and collecting system information, the malware packs the data and uploads it to the C2 server. Interaction with the server is via TCP sockets:

Certain values are added in before the data is sent; then the data is compressed with ZLIB and encoded in Base64.


Figure 57. Data packing algorithm

In the code in Figure 55:

Flag is the value initialized when the backdoor starts.


Figure 58. Initializing the "flag" parameter

Key is the value from configuration changes.
Cmd is an executed config command.
Data is the collected data.
After the data is prepared, its length and the delimiter indicated in the config are added to the beginning, and then the data is sent to the server.



Figure 59. Forming the final data packet



Figure 60. Example of formed data

After the initial system data is sent, the backdoor goes into a loop as it awaits a command from the server.


Figure 61. Main loop

2.5. Utilities
Among our finds on the server were utilities for lateral movement. Most of those are open-source ones available on GitHub. They were initially written in Python but converted to PE. The server had the following utilities:

Utilities17 to check for and exploit vulnerability MS17-010
LaZagne18 for gathering passwords
get_lsass19 for dumping passwords on x64 systems
NBTScan
DomainInfo for collecting domain information

The hackers tweaked the functionality of the MS17-010 utility by adding the ability to check an entire subnet.


Figure 62. Modified utility for checking for MS17-010

Network scanning is performed out of sequence, which may throw defenders off the scent. In addition, the scan will skip addresses with 1 and 2 in the final octets, because such addresses very rarely belong to user computers.
Another utility of note on the server collects information about the domain of the target computer. The information includes the following:

Computer name
Names of computer users, divided into groups
Domain name
Name of the current user's group
Names of the groups on the domain
Names of users in each group

All this information is collected in a legitimate way via the API functions of library Netapi32.dll and saved to the utility directory in XML format.
Interestingly enough, the utility was compiled in 2014 with Microsoft Visual Studio 2005 and has the PDB "e:\Visual Studio 2005\Projects\DomainInfo\Release\Domain05.pdb".
Conclusion
We have analyzed the infrastructure of the Winnti group and conclude that it has been active since early 2019. Currently this infrastructure is growing, which means Winnti is active. According to our information, the group has already compromised over 50 computers, and some of those may serve as a staging ground for subsequent, more serious attacks. The group has added new malware to its arsenal, such as SkinnyD, xDll, and a Python backdoor. We found important connections between the current Winnti infrastructure and other large attacks in which the group may have been directly involved.
The observed spike in the group's activity may be related to the coronavirus pandemic. Many companies have switched employees to working from home and, as shown by our data, 80 percent of employees use their personal computers for work. The result is that many employees are currently not protected by corporate security tools and security policies. This makes them an easy target.




MD5
                
SHA-1
                
SHA-256
                



SkinnyD



ec2377cbd3065b4d751a791a22bd302c
                
cdd78ccd274705f6c94b6640c968e90972597865
                
1d59968304f26651526a27dabd2780006ebd14925c9e00093acfa2443a223675
                


3fff50f9ea582848b8a5db05c88f526e
                
ea11d0d950481676282cee20c5eb24fc71878bcc
                
b5227a12185a6fef8bb99ac87eefba7787bbf75ff9c99bdc855a52539b805d2e
                


55186de70b2d5587625749a12df8b607
                
858d866c5faa965fa9fbe41c8484a88fe0c612eb
                
d81ba465fe59e7d600f7ab0e8161246a5badd8ae2c3084f76442fb49f6585e95
                



Бэкдор xDll



9f01cb61f342f599a013c3e19d359ab4
                
b63bfdfb7f267e9fbf1c19be65093d857696f3b0
                
169c24f0ad3969fe99ff2bf205ead067222781a88d735378f41a9822c620a535
                


a2d552ed07ad15427f36d23da0f3a5d3
                
1858a80c8cff38d7871286a437c502233e027ab0
                
59759bbdfc1a37626d99dd260e298a1285ff006035ab83b7a37561e2884fd471
                


60ddb540da1aefee1e14f12578eafda8
                
8d16bc28cef6760ecf69543a14d29ba041307957
                
87a57f5bb976644fce146e62ee54f3e53096f37f24884d312ab92198eb1e6549
                


7a4c8e876af7d30206b851c01dbda734
                
4cff1af90c69cc123ecafe8081e3c486a890d500
                
06d20fb5894c291fca07021800e7e529371372abff6db310c0cbc100cf9ad9f9
                


3d760b6fc84571c928bed835863fc302
                
adcf9ade7a4dc14b7bf656e86ea15766b843e3b6
                
8ac21275d0db7f3e990551f343e16ac105d6a513810ff71934de4855999cc9c5
                


278eb1f415d67da27b2e35ec35254684
                
7d30043210c8be2f642c449b92fe810a8c81f3f8
                
a77613cbb7e914796433bf344614e0c469e32a1d52fbaf3df174bf521a3fc6b7
                


007f35e233a25877835955bdd5dd3660
                
c1ec5a34b30990d9197c8010441c39d390109c75
                
aa7b1d13a96f90bf539455f25ef138d5e09e27b7da6bf7f0c2e48821d98cf476
                


f2b37be311738a54aa5373f3a45bbde2
                
5e350480787827c19c7bee4833c91d72d0e032a0
                
ece7f411ed1897304ca822b37d6480ff0b9505c8e307ef152fef8ed183b001c5
                



ShadowPad



82118134e674fe403907c9b93c4dc7be
                
5e29d9e4be79b5d1d7e606ba59a910cdd840203b
                
2c2b1d9b34df9364fd91a6551890b0fdc58a7e681713c682221a674d1116089a
                


d5cf8f4c8c908553d57872ab39742c75
                
bc2ef2e2232bce6be5bb0333da6f101f45ca6277
                
319a06a39e5a1394710ec917f281a546d850386e80fdb56238456b68d5207a99
                


eccb14cb5a9f17356ad23aa61d358b11
                
ef8951613ccca06f35b10f87dc11cf5543c727dd
                
3ff1cf65dff231f05bd54df3fecad2545b159094ce59ce4bf4c668c904d2a5d7
                


349382749444e8f63e7f4dc0d8acf75d
                
223f24eadc6e3a48d9cf9799e3e390a4a4015fdb
                
63a74b66685fb94d685cfdfadd10917c805239ea079b9431bb5e9c8a58e0ea4b
                


ed4481a9b50529bfa098c4c530e4198e
                
f6e4d7eb5e3a7ae4c94bb8626f79cc27b776d665
                
79f0e0a0f9c79a9206b9c2af222f026c384d3e0d761b0b42815453991bc05294
                


85b0b8ec05bd6be508b97fd397a9fc20
                
4e60f31e386ec4f478f04b48458e49ef781b04d0
                
831212d40c5120824508a645e54bf1b86f3be0cd19f87b8067e8b2fdea5c844e
                


6e3ce4dc5f739c5ba7878dd4275bb1f5
                
09a3b4823a4d82b72888e185c8b23b13c22885c3
                
85b0ada2836c76cc49b886dfe59d950a073e9d6d761581075bf904238306e8c4
                


05751ea487d99aefea72d96a958140d7
                
2092a0557dcece4b4a32040b1bc09f9606aa1a1c
                
9984d5b554b8dbfeffdb374e1c8eaf74af7109a0e6b924b00ad5b878d0188895
                


b9082bce17059a5789a8a092bbcdbe26
                
a570deda43eb424cc3578ba00b4d42d40044bd00
                
be7b1f7f0b73b77fc8fe4c109ae5a675cc9f3f6c16d3a1d7b2a9c6ba5a52ef9a
                


14d546b1af2329b46c004b5ed37a3bc2
                
07ef26c53b62c4b38c4ff4b6186bda07a2ff40cb
                
bb28528e76649fb72e069b15a76f7c6ef520ae727408b3439856880a4488aa1f
                


988ebf6fec017ec24f24427ac29cc525
                
0eec24a56d093e715047a626b911278a218927d2
                
d7786504a09ae35a75818c686b6299870e91d646bdf20609fbee0d86c94a5ff5
                


e6aa938be4b70c79d297936887a1d9a3
                
8cf60c047ee8d742a7a91626535c64bc6d7b580e
                
ec801e3baa02c7ad36a9b06512ac106d30ab3a2207a7cb1e543fbd076995d43d
                


964be19e477b57d85aceb7648e2c105d
                
6c8ab56853218f28ac11c16b050ad589ea14bafe
                
9843ceaca2b9173d3a1f9b24ba85180a40884dbf78dd7298b0c57008fa36e33d
                


7bb16d5c48eb8179f8dafe306fc7e2c2
                
6bfdee276207d9b738b5e51f72e4852e3bda92d2
                
f7231082241d9e332b45307e180f20e11041f59196715749c6a79a8be17fcdc0
                



Bisonal



5e25dfdf79dfc0542a2db424b1196894
                
3bf3cd0f3817cf9481944536c0c65d8a809e6d4a
                
e114dd78f9acafcf7e93efe1c9e68a29e4fe52c4830431a4aa5457927bef7c5e
                



Python-бэкдор



c86099486519947a53689e1a0ac8326d
                
817a88c07fe6d102961a994681c6674f89e2f28e
                
77e4a1f6eb95b9763cf13803aba0058ac0bcada8ee8b8f746963f2db8ce2e21f
                



get_lsass



802312f75c4e4214eb7a638aecc48741
                
af421b1f5a08499e130d24f448f6d79f7c76af2b
                
8eb40114581fe9dc8d3da71ea407adfb871805902b72040d10f711a1de750bfd
                



DomainInfo



22dfdcddd4f4da04b9ef7d10b27d84bc
                
619d32ea81e64d0af0a3e2a69f803cfe9941884b
                
aad5ca66cfd5f0d1ffd4cccaa199de844b4074d02544521afc757e075739c4b0
                



MS17-010 checker



96c2d3af9e3c2216cd9c9342f82e6cf9
                
397f60d933a3aa030fac5c1255b2eb1944831fb2
                
af3ec84a79dc58d0a449416b4cf8eb5f7fd39c2cf084f6b16ee05abe4a968f12
                



MS17-010 exploiter



2b2ed478cde45a5a1fc23564b72d0dc8
                
a7d6fbbfb2d9d77b8cf079102fb2940bbf968985
                
e3768ad2b2e505453e64fe0f18cb47b2fe62d184ac7925f73e792d374ba630aa
                




Network indicators
SkinnyD
80.245.105.102
xDll
www.yandex2unitedstated.dns05.com
www.oseupdate.dns-dns.com
www.yandex2unitedstated.dynamic-dns.net
g00gle_jp.dynamic-dns.net
hotmail.pop-corps.com
www.yandex2unitedstated.dynamic-dns.net
ShadowPad
www.ncdle.net
www.ertufg.com
info.kavlabonline.com
ttareyice.jkub.com
unaecry.zzux.com
filename.onedumb.com
www.yandex2unitedstated.dns04.com
www.trendupdate.dns05.com
Bisonal
www.g00gleru.wikaba.com
Python backdoor
daum.pop-corps.com
Related domains




agent.my-homeip.net
                
freemusic.zzux.com
                
pop-corps.com
                


alombok.yourtrap.com
                
gaiusjuliuscaesar.dynamicdns.biz
                
microsoft-update.pop-corps.com
                


application.dns04.com
                
ggpage.jetos.com
                
microsoft_update.pop-corps.com
                


arjuna.dynamicdns.biz
                
gkonsultan.mrslove.com
                
rama.longmusic.com
                


arjuna.serveusers.com
                
gmarket.system-ns.org
                
redfish.misecure.com
                


artoriapendragon.itemdb.com
                
googlewizard.ocry.com
                
regulations.vizvaz.com
                


backup.myftp.info
                
hardenvscurry.my-router.de
                
robinhood.longmusic.com
                


billythekid.x24hr.com
                
help.kavlabonline.com
                
server.serveusers.com
                


bluecat.mefound.com
                
hosenw.ns02.info
                
serviceonline.otzo.com
                


bradamante.longmusic.com
                
host.adobe-online.com
                
thebatfixed.zyns.com
                


cindustry.faqserv.com
                
hpcloud.dynserv.org
                
tunnel.itsaol.com
                


cuchulainn.mrbonus.com
                
ibarakidoji.mrbasic.com
                
uacmoscow.com
                


daum.xxuz.com
                
indian.authorizeddns.us
                
update.wmiprvse.com
                


depth.toh.info
                
inthefa.bigmoney.biz
                
videoservice.dnset.com
                


describe.toh.info
                
jaguarman.longmusic.com
                
waswides.isasecret.com
                


developman.ocry.com
                
jeannedarcarcher.zyns.com
                
webhost.2waky.com
                


dnsdhcp.dhcp.biz
                
letstweet.toh.info
                
webmail_gov_mn.pop-corps.com
                


economics.onemore1m.com
                
lezone.jetos.com
                
xindex.ocry.com
                


ecoronavirus.almostmy.com
                
likeme.myddns.com
                
yandex.mrface.com
                


email_gov_mn.pop-corps.com
                
medusa.americanunfinished.com
                
yandex.pop-corps.com
                


ereshkigal.longmusic.com
                
modibest.sytes.net
                
www.alombok.yourtrap.com
                


eshown.itemdb.com
                
movie2016.zzux.com
                
www.arjuna.dynamicdns.biz
                


facegooglebook.mrbasic.com
                
msdn.ezua.com
                
www.asagamifujino.dns05.com
                


fackb00k2us.dynamic-dns.net
                
myflbook.myz.info
                
www.billythekid.x24hr.com
                


fergusmacroich.ddns.info
                
mynews.myftp.biz
                
www.bradamante.longmusic.com
                


fornex.uacmoscow.com
                
nadvocacy.mrbasic.com
                
www.cuchulainn.mrbonus.com
                


frankenstein.compress.to
                
nikolatesla.x24hr.com
                
www.daum.xxuz.com
                


free2015.longmusic.com
                
notepc.ezua.com
                
www.david.got-game.org
                


freedomain.otzo.com
                
npomail.ocry.com
                
www.facebook2us.dynamic-dns.net
                


freemusic.xxuz.com
                
ntripoli.www1.biz
                
www.nthere.ourhobby.com
                


www.facegooglebook.mrbasic.com
                
odanobunaga.dns04.com
                
www.odanobunaga.dns04.com
                


www.fackb00k2us.dynamic-dns.net
                
point.linkpc.net
                
www.officescan_update.mypop3.org
                


www.fergusmacroich.ddns.info
                
www.googlewizard.ocry.com
                
www.program.ddns.info
                


www.frankenstein.compress.to
                
www.hosenw.ns02.info
                
www.robinhood.longmusic.com
                


www.free2015.longmusic.com
                
www.ibarakidoji.mrbasic.com
                
www.siegfried.dynamic-dns.net
                


www.freedomain.otzo.com
                
www.inthefa.bigmoney.biz
                
www.stade653.dns04.com
                


www.g00gle_kr.dns05.com
                
www.jaguarman.longmusic.com
                
www.uacmoscow.com
                


www.g00gle_mn.dynamic-dns.net
                
www.jeannedarcarcher.zyns.com
                
www.webhost.2waky.com
                


www.g0ogle_mn.dynamic-dns.net
                
www.likeme.myddns.com
                
www.xindex.ocry.com
                


www.ggpage.jetos.com
                
www.medusa.americanunfinished.com
                
www.yandex.mrface.com
                


www.gkonsultan.mrslove.com
                
www.microsoft-update.pop-corps.com
                
www.yandex.pop-corps.com
                


www.goog1e_kr.dns04.com
                
www.msdn.ezua.com
                
www.yandex2unitedstated.2waky.com
                


 
                
www.nikolatesla.x24hr.com
                
 
                


 
                
www.nmbthg.com
                
 
                


 
                
www.npomail.ocry.com
                
 




MITRE




ID

Name

Description




Initial Access



T1566.001
                
Spear-phishing Attachment
                
Winnti sent spearphishing emails with malicious attachments
                



Execution



T1204.002
                
User Execution: Malicious File
                
Winnti attempted to get users to launch malicious attachments delivered via spearphishing emails.
                


T1569.002
                
System Services: Service Execution 
                
Winnti created Windows services to execute xDll backdoor
                



Persistence



T1547.001
                
Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder
                
Winnti added Registry Run keys to establish persistence.
                


T1543.003
                
Create or Modify System Process: Windows Service
                
Winnti has created new services to establish persistence
                



Defense evasion



T1140
                
Deobfuscate/Decode Files or Information
                
Winnti used custom cryptographic algorithm to decrypt
                    payload
                


T1055
                
Process Injection
                
Winnti injected ShadowPad into the wmplayer.exe process
                


T1574.002
                
Hijack Execution Flow: DLL Side-Loading
                
Winnti used legitimate executables to perform DLL side-loading of their malware
                


T1564.001
                
Hide Artifacts: Hidden Files and Directories
                
Winnti has created a hidden directory under C:\ProgramData
                    
                


T1027
                
Obfuscated Files or Information 
                
Winnti used VMProtected binaries
                


T027.001
                
Software Packing
                
Winnti used a custom packing algorithm
                



Credential Access



T1555
                
Credentials from Password Stores
                
Winnti used a variety of publicly available tools like LaZagne
                    to gather credentials
                


T1003.001
                
OS Credential Dumping: LSASS Memory
                
Winnti used get_lsass to dump credentials
                



Discovery



T1087.001
                
Credentials from Password Stores 
                
Winnti gathered information of members on the victim’s
                    machine
                


T1087.002
                
Account Discovery: Domain Account
                
Winnti gathered domain user account information
                


T1069.002
                
Permission Groups Discovery: Domain Groups
                
Winnti gathered domain group information
                



Collection



T1056.001
                
Input Capture: Keylogging
                
ShadowPad contains a keylogge
                


T1113
                
Screen Capture
                
ShadowPad contains a screenshot module
                



Command And Control



T1043
                
Commonly Used Port
                
Winnti uses HTTP(s) for C2.
                


T1071.001 
                
Application Layer Protocol: Web Protocols
                
ВПО группы Winnti использует стандартные протоколы для соединения с С2: HTTP и HTTPS
                


T1095
                
Non-Application Layer Protoco
                
Winnti uses TCP and UDP for C2.
                




 

twitter.com/Vishnyak0v/status/1239908264831311872
securelist.com/winnti-more-than-just-a-game/37029/
securelist.com/shadowpad-in-corporate-networks/81432/
blog.avast.com/update-ccleaner-attackers-entered-via-teamviewer
securelist.com/operation-shadowhammer-a-high-profile-supply-chain-attack/90380/
welivesecurity.com/2020/01/31/winnti-group-targeting-universities-hong-kong/
blog.avast.com/update-ccleaner-attackers-entered-via-teamviewer
slideshare.net/codeblue_jp/cb19-cyber-threat-landscape-in-japan-revealing-threat-in-the-shadow-by-chi-en-shen-ashley-olegbondarenko
ptsecurity.com/ww-en/analytics/operation-taskmasters-2019/
proofpoint.com/us/threat-insight/post/APT-targets-russia-belarus-zerot-plugx
proofpoint.com/us/threat-insight/post/nettraveler-apt-targets-russian-european-interests
jsac.jpcert.or.jp/archive/2020/pdf/JSAC2020_3_takai_jp.pdf
unit42.paloaltonetworks.com/unit42-bisonal-malware-used-attacks-russia-south-korea/
blog.talosintelligence.com/2020/03/bisonal-10-years-of-play.html
attack.mitre.org/techniques/T1037/
media.kasperskycontenthub.com/wp-content/uploads/sites/43/2017/08/07172148/ShadowPad_technical_description_PDF.pdf
github.com/worawit/MS17-010/blob/master/checker.py
github.com/AlessandroZ/LaZagne
github.com/3gstudent/Homework-of-C-Language/blob/master/sekurlsa-wdigest.cpp






Download PDF





Related articles




June 19, 2020
The eagle eye is back: old and new backdoors from APT30


November 30, 2023
Hellhounds: operation Lahat


June 16, 2020
Cobalt: tactics and tools update














Share:








Link copied





Related articles





August 4, 2022
Flying in the clouds: APT31 renews its attacks on Russian companies through cloud storage





September 27, 2023
Dark River. You can't see them, but they're there





May 18, 2020
IronPython, darkly: how we uncovered an attack on government entities in Europe




All articles



















Solutions



ICS/SCADA
Vulnerability Management
Financial Services
Protection from targeted attacks (anti-apt)
PT Industrial Cybersecurity Suite 
Utilities
ERP Security
Security Compliance



Products



MaxPatrol 8
MaxPatrol SIEM
PT Application Firewall
PT Application Inspector
PT ISIM
PT Network Attack Discovery
PT Sandbox
XSpider
MaxPatrol VM
MaxPatrol SIEM All-in-One
PT MultiScanner
PT BlackBox



Services



ICS/SCADA Security Assessment
ATM Security Assessments
Web Application Security Services
Mobile Application Security Services
Custom Application Security Services
Penetration Testing
Forensic Services
Advanced Border Control



Analytics



Threatscape
PT ESC Threat Intelligence
Cybersecurity glossary
Knowledge base



Partners




About



Clients
Press
News
Events
Contacts
Documents and Materials









                        Copyright © 2002—2024 Positive Technologies. All Rights Reserved.
                    






Report a vulnerability
Help Portal
Terms of Use
Privacy Notice
Cookie Notice
Positive Coordinated Vulnerability Disclosure Policy
Sitemap


Copyright © 2002—2024 Positive Technologies. All Rights Reserved.




Report a vulnerability
Help Portal
Terms of Use
Privacy Notice
Cookie Notice
Positive Coordinated Vulnerability Disclosure Policy
Sitemap

















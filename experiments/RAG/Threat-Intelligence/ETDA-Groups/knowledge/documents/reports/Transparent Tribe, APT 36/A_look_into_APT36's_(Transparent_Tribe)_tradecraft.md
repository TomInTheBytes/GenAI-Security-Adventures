# Reference for threat actor for "Transparent Tribe, APT 36"

**Title**: A look into APT36's (Transparent Tribe) tradecraft

**Source**: https://cyberstanc.com/blog/a-look-into-apt36-transparent-tribe/

## Content





A look into APT36's (Transparent Tribe) tradecraft




















































Pioneer in Malware Detections & Mitigations








Linkedin





Twitter





Email



 











02 Nov 2020 • 

A look into APT36's (Transparent Tribe) tradecraft


APT36 ( a.k.a Transparent Tribe / Mythic Leopard / PROJECTM/ TEMP ) is a prominent group believed to be operating on behalf of Pakistan state and conducting espionage with great interests in a very specific set of countries specially India, widely since 2013. Most frequent target sectors include:Military organizationsGovernment entities Example honey trap lure templateCyberstanc's very own threat research team have been tracking APT36's activities and we would like to provide you an insight into their tradecraft specially their main malware dubbed "Crimson RAT".Analysis: We won't be laying emphasis on individual samples rather we would be randomly covering samples and variants to provide better insightsPayload Delivery: Transparent Tribe employees multitude of tactics from the old books of espionage 101 for dummies for example honey-trapping army personals however frequent payload delivery methods constitutes of usually the following: Malicious Documents / Excel sheets Compressed archived filesWaterholing attack Basic static analysis consists of examining the sample without viewing the actual instructions. Basic static analysis can confirm whether a file is malicious, provide information about its functionality, and sometimes provide information that will allow you to produce simple network signatures.Filename : Kashmir_conflict_actions.docxFile Type :  MS Word DocumentFile size      300.00 KB (300000 bytes)Stage 1 (Macro enabled document dropper) : Kashmir_conflict_actions.docxKashmir_conflict_actions.docx contains a macro which in turn makes a remote SQL query to C2 server (Datroapp[.]mssql.somee.com) and writes the second stage payload to "\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\Trayicos.exe" and launches the payload1st stage macro payloadStage 2 (Dropper) :Basic static analysis consists of examining the sample without viewing the actual instructions. Basic static analysis can confirm whether a file is malicious, provide information about its functionality, and sometimes provide information that will allow you to produce simple network signatures.Filename : TrayIcos.exeFile Type :  PE32 executable for MS Windows (GUI) Intel 80386 32-bit File size   :  2.4 MB (2519552 bytes)MD5 : 18ACD5EBED316061F885F54F82F00017Signature : Microsoft Visual C++ 8Initial looks at the PE file straight up looks like a payload loader of some sorts specially looking at the resource section of the file we can see a data blob with bigger size than usual and an exceptionally high entropy value. Pestudio resource viewerFurther analysis indicates the same with a import chain of :FindResource -> LoadResource -> LockResource -> SizeofResource -> FreeResource Getting 3rd stage payload from resourceWe can clearly conclude the encrypted data block located in the resource section is the 3rd stage payload.After some dynamic analysis we are able to decrypt the 3rd stage payload. However we are not finished yet ! Once the 3rd stage payload is decrypted which in turn is revealed as a .NET assembly its loaded in the memory space of the same unmanaged process "TrayIcos.exe" .Payload decryptionManaged payload method called from unmanaged parent dropperStage 3 (Third stage dropper):Basic static analysis consists of examining the sample without viewing the actual instructions. Basic static analysis can confirm whether a file is malicious, provide information about its functionality, and sometimes provide information that will allow you to produce simple network signatures.Filename : Random.dllFile Type : C# dynamic link library / .Net AssemblyFile size   :  2.3 MB (2441216  bytes)MD5 : 4A22A43CCAB88B1CA50FA183E6FFB6FASignature : Microsoft Visual C# v7.0 / Basic .NETWe get a unpacked / obfuscated C# assembly which we dumped during the dynamic analysis of the 2nd stage dropper.The functionality of the dropper is pretty straight forward payload from resource and then execute entrypoint of the payload. 3rd stage dropper Stage 4 (Crimson RAT):Final stage includes execution of our crown king Crimson Remote Access Trojan. Basic static analysis consists of examining the sample without viewing the actual instructions. Basic static analysis can confirm whether a file is malicious, provide information about its functionality, and sometimes provide information that will allow you to produce simple network signatures.Filename : TrayIcos.exeFile Type : PE32 executable for MS Windows (GUI) Intel 80386 32-bit Mono/.Net assemblyFile size   :  2.2 MB (2295808  bytes)MD5 : 5A27D092E4A87554206F677B4EADC6F5Signature : Microsoft Visual C# v7.0 / Basic .NETPacker : .Net Reactor Crimson RAT supports basic functionalities a remote access trojan should have like screen capture, screen size enumeration, commands execution, process list, process kill, etc. However the functionalities differ from variant to variant and are stripped in many samples however the complete list of all functionalities supported by the framework are listed below : FunctionalitiesCommand parser and functionalities of crimson ratPersistence mechanism is the least notable and extremely basic in nature HKCU Run key persistenceC2 communication is implemented using simple TCP protocol with no added encryption / encoding even which is highly disappointing. C2 connection using TCPVerdict: Overall Transparent Tribe's tradecraft might seem lackluster but since their inception in 2013 they have been quite successful according to statistics in executing their plans and conducting espionage campaigns on daily basis. However our customers are protected against this threat. Additionally, Scrutiny Anti Malware properly files used by Transparent Tribe as malicious.








© 2024 All rights reserved.
 









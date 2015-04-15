# Sphinx
Sphinx is a highly scalable open source security monitoring tool that offers real-time auditing and analysis of host activities.
It works by having clients forward various types of event logs including process execution with cryptographic signature (MD5 hash), network activity, dll/driver loading, as well as miscellaneous system events to a Sphinx server where each event is recorded and analyzed. 

With Sphinx, you can quickly find an answer to questions like:
1) can we get a list of every event that happened on machine X between date Y and date Z?
2) can we graphically trace what happened on my computer in the last 10 minutes because I feel there's something weird going on?
3) who has run a piece of malware whose existence cannot be detect by our existing Anti-Virus product on our network?
4) give me a list of program executions as well as dll loads whose reputation is questionable or bad.
5) are there Office application making outbound connection to China?
6) are there any dlls injected into explorer.exe whose digital signature does not belong to Microsoft?


You can build both simple and complex queries to search for threats. 
These queries can be run recurringly, and send alerts whenever there's a hit.


Sounds interesting enough? Let's get started :)

#How it works:

Sphinx works by having clients forward various types of event logs including process execution history with program's digital fingerprint (MD5 hash), network activity, dll/driver loading, as well as miscellaneous system events to a Sphinx server where each event is recorded and analyzed. These events are primarily generated through Sysmon, Microsoft's Sysinternal tool, and delivered to the server using nxlog, a robust open source log management tool. 

On the server side, Sphinx receives the incoming data using Logstash, a popular log management tool with horizontal scalability. Logstash loads several plug-ins (including Sphinx's own Logstash plug-in) in order to normalize the data for analysis. The Sphinx plugin is primarily responsible for adding reputation information for events with MD5 hash. Sphinx uses the following sources to build its reputation table:
1) Nastional Software Reference Library (NSRL), a project of the National Institute of Standards and Technology (NIST) which maintains a repository of known software, file profiles and file signatures for use by law enforcement and other organizations involved with computer forensic investigations.
2) VirusTotal, a subsidiary of Google, is a free online service that analyzes files and URLs enabling the identification of viruses, worms, trojans and other kinds of malicious content detected by antivirus engines and website scanners.
3) VirusShare, a repository of malware samples to provide security researchers, incident responders, forensic analysts, and the morbidly curious access to samples of malicious code.

Finally, normalized data is stored in an Elasticsearch server. Elasticsearch is a highly scalable, open-source full-text search engine based on Apache Lucene. Users can use Sphinx's web UI to build/run queries, and detect threats. The web front end is also capable of graphically browsing program execution history or create an alert using saved queries.
For example, you can have an alert set to trigger whenever Sphinx sees a program execution whose reputation is 'Harmful' OR 'Potentially Harmful' OR 'Unknown'.


#What Sphinx is not:
Sphinx is not an Anti-Virus solution. Therefore, it does prevent malware infection in any way. 


#Want to try it yourself?
Trying Sphinx involves 4 steps. 

1. Download and install Sysmon from Microsoft
  a) Download Sysmon 2.0 from https://technet.microsoft.com/en-us/sysinternals/dn798348
  b) Run 'cmd.exe' as Administrator 
  c) Install Sysmon by typing the following:
     sysmon -i -accepteula -n -h md5 -l explorer.exe,lsass.exe,iexplorer.exe,dllhost.exe,smss.exe,winlogon.exe,rundll32.exe

2. Download and install nxlog
  a) Download nxlog Community Edition from http://nxlog.org/products/nxlog-community-edition/download
  b) Install nxlog

3. Configure nxlog to forward logs to Sphinx
  a) Download the following configuration file for nxlog
  b) Overwrite "C:\Program Files (x86)\nxlog\conf\nxlog.conf" with it

4. Restart your computer. (You must restart your PC for Sphinx to work correctly)


#FAQ
1. What platforms do you support?
Currently, Sphinx only works with Windows 7 and above. Linux will be supported in the future release.

2. Sphinx says my computer has malware infection. Can you help?
The short answer is no. I'd just re-install Windows if I were you...

3. What information does Sphinx pull from my computer?
Sphinx works by forwarding your computer's event logs to Sphinx's server. 
By default Sphinx retrieves the following log entries 
- process creation/termination, dll module load, network connection, file creation and driver load (all from Sysmon)
- Security
- AppLocker related events
- EMET related events
- Any Security/System/Setup related events
- Some specific Application related events

Refer to "C:\Program Files (x86)\nxlog\conf\nxlog.conf" for detail.

4. Is the communication between client and server encrypted?
No. However, TLS support is coming very shortly.

5. Can I run Hadoop?
Sure. Since Sphinx uses a RDB for storing reference hashes, it may be a good idea to set up a Hive.




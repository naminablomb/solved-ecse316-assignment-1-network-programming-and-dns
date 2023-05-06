Download Link: https://assignmentchef.com/product/solved-ecse316-assignment-1-network-programming-and-dns
<br>
In this lab you will implement a <em>domain name system </em>(DNS) client using sockets in Java. If you prefer, the system can also be implemented in Python.

At the end of this lab you should know how to:

<ol>

 <li>Understand an existing network protocol specification;</li>

 <li>Design a network application that adheres to the given protocol; and</li>

 <li>Implement and test the network application using sockets.</li>

</ol>

As always, read the instructions carefully!

<h1>2                   Background</h1>

<h2>2.1               The Domain Name System (DNS)</h2>

The internet’s domain name system provides the essential service of mapping between domain names (e.g., <u>mcgill.ca</u> or <u>facebook.com</u>) and IP addresses (e.g<em>., 132.216.177.160 </em>or <em>31.13.65.1</em>). This makes the internet more human-friendly, since we only have to remember domain names and not IP addresses.




When you direct an internet-enabled application to a new site (e.g., you open a new website in your browser, or you send an email to someone at a different domain name), the application needs to resolve the domain name to an IPaddress before it can contact the server. This is accomplished using the DNS request/response mechanism. For more about the DNS system and the protocol it uses, see Section 2.4 of Kurose and Ross (7<sup>th </sup>edition) or Section 2.5 in Kurose and Ross (6<sup>th </sup>edition) and the primer document posted along with these instructions onmyCourses. You may also look at <u>RFC 1035</u> <u>(Sections 3 and 4)</u> which give the official description of the DNS resource record format and message format.

<h2>2.2               Socket programming</h2>

Sockets are the programming mechanism used to implement network applications. If you have previously written an application that reads or writes from a file on disk, then you know you first open a file handle, then read/write using the file handle, and finally close the file handle when you are done. Sockets work in a similar way, but instead of reading/writing to a file on disk, they allow you to read/write to another process (usually running on a different machine).




For more on socket programming read Section 2.7 in Kurose and Ross (6<sup>th </sup>Edition – Java or 7<sup>th </sup>edition – Python) and the <u>Java Networking Tutorial</u> from Oracle. If you choose the Python approach, you can read the material available at https://docs.python.orG/2/howto/sockets.html

<h2>2.3               Summary of background and pre-requisites</h2>

Before proceeding with the rest of this lab document make sure you have accomplished these first steps:

<ol>

 <li>You are familiar with basic Java or Python programming, including how to compile and execute a program from the command line. We also recommend that you are familiar with an <em>integrated development environment (IDE)</em>, such as Eclipse, for debugging.</li>

 <li>You have read the background on the DNS and on java/Python socket programming mentioned above.</li>

</ol>

<h1>3                   Lab requirements</h1>

Write a program using Java/Python sockets that:

<ul>

 <li>Is invoked from the command line (STDIN);</li>

 <li>Sends a query to the server for the given domain name using a UDP socket;</li>

 <li>Waits for the response to be returned from the server;</li>

 <li>Interprets the response and outputs the result to terminal display (STDOUT). Your DNS client should be capable of performing the following actions:</li>

 <li>Send queries for A (IP addresses), MX (mail server), and NS (name server) records; Interpret responses that contain A records (iPad dresses) and CNAME records (Unaliases);</li>

 <li>Retransmit queries that are lost.</li>

</ul>

Your client must also handle errors gracefully. In particular, if the response message does not conform to the DNS specification, if it contains fields or entries that cannot be interpreted, or if the client receives a response that does not match the query it sent, then an appropriate error message should be printed to the screen indicating what was unexpected or what went wrong.




For this lab you must use Java or Python. You must write the code that constructs a DNS request packet and interprets the DNS response packet. You may not use external DNS libraries or classes in your project (such as net.URL or net.InetAddress). Specifically, in Java you may only use the net.InetAddress.getByAddress(byte[] addr) method; you may not use any of the net.InetAddress methods that take an argument of the form String host. (The same requirements apply if you use net.Inet4Address instead of   net.InetAddress.)   Similar instructions   apply   for a Python implementation; please ask me or the TAs if you are not sure about whether you can make use of a library or class.

<h2>3.1               Calling syntax</h2>

Your application should be named DnsClient, and it should be invoked at the command line using the following syntax (for Java; the options should be the same for Python):

java DnsClient [-t timeout] [-r max-retries] [-p port] [-mx|-ns] @server name

where the arguments are defined as follows.

<ul>

 <li>timeout (optional) gives how long to wait, in seconds, before retransmitting an unanswered query. Default value: 5.</li>

 <li>max-retries(optional) is the maximum number of times to retransmit an unanswered query before giving up. Default value: 3.</li>

 <li>port (optional) is the UDP port number of the DNS server. Default value: 53.</li>

 <li>-mx or -ns flags (optional) indicate whether to send a MX (mail server) or NS (name server) query. At most one of these can be given, and if neither is given then the client should send a type A (IP address) query.</li>

 <li>server (required) is the IPv4 address of the DNS server, in a.b.c.d. format</li>

 <li>name (required) is the domain name to query for.</li>

</ul>




For example to query for <a href="http://www.mcgill.ca/">www.mcgill.ca</a> <a href="http://www.mcgill.ca/">I</a>P address using the McGill DNS server, enter java  DnsClient  @132.206.85.18  <a href="http://www.mcgill.ca/">www.mcgill.ca</a>

or toqueryforthemcgill.camail serverusing Google’s public DNSserver with atimeout of10seconds and at most 2 retries, enter




java DnsClient –t 10 –r 2 –mx @8.8.8.8 mcgill.ca




<h2>3.2               Output behavior</h2>

Your program should print output to STDOUT using the following standard format. The first lines should summarize the query that has been sent:




DnsClient sending request for [name]

Server: [server IP address]

Request type: [A | MX | NS]




Where the fields in square brackets are replaced by appropriate values. Then, subsequent lines should summarize the performance and content of the response. When a valid response is received, the first line should read:




Response  received  after  [time]   seconds   ([num-retries]   retries)

If the response contains records in the Answer section then print:

***Answer Section ([num-answers] records)***




Then, if the response contains A (IP address) records, each should be printed on a line of the form:

IP &lt;tab&gt; [ip address] &lt;tab&gt; [seconds can cache] &lt;tab&gt; [auth | nonauth]

Where &lt;tab&gt; is replaced by a tab character. Similarly, if it receives CNAME, MX, or NS records, they should be printed on lines of the form:




CNAME &lt;tab&gt; [alias] &lt;tab&gt; [seconds can cache] &lt;tab&gt; [auth | nonauth] MX &lt;tab&gt; [alias] &lt;tab&gt; [pref] &lt;tab&gt; [seconds can cache]  &lt;tab&gt;  [auth  | nonauth] NS &lt;tab&gt; [alias] &lt;tab&gt; [seconds can cache] &lt;tab&gt; [auth | nonauth]




If the response contains records in the Additional section then also print:




***Additional Section ([num-additional] records)***




along with appropriate lines for each additional record that matches one of the types A, CNAME, MX, or NS. You can ignore any records in the Authority section for this lab.




If no record is found then a line should simply be printed saying

NOTFOUND

If any errors occur during execution then lines should be printed saying

ERROR &lt;tab&gt; [description of error]

Be specific with your error messages. Some example of errors your DNS client may output are:




ERROR &lt;tab&gt; Incorrect input syntax: [description of specific problem] ERROR &lt;tab&gt;

Maximum number of retries [max-retries] exceeded

ERROR &lt;tab&gt; Unexpected response [description of unexpected response content]

<h1>4                   Important dates, deliverables, and evaluation</h1>

<h2>4.1               Report</h2>

The codeandthereport are due at 23:59 on Feb. 22<sup>nd</sup>, and it will count for 7.5% of your final grade.

Your report should include the following items:

<ul>

 <li>The names and McGill ID numbers of both group members</li>

 <li>Design: Describe the design of your application</li>

 <li>Testing: Describe how you tested your application to make sure it behaves as required</li>

 <li>Experiment:

  <ul>

   <li>What are the IP addresses of McGill’s DNS servers? Use the Google public DNS server (8.8.8.8) to perform a NS query for mcgill.ca and any subsequent follow-up queries that may be required. What response do you get? Does this match what you expected?</li>

   <li>Use your client to run DNS queries for 5 different website addresses, of your choice, in addition to <a href="https://www.google.com/">google.com</a> and<u> www.amazon.com,</u> for a total of seven addresses. Query the seven addresses using the Google public DNS server (8.8.8.8). o Briefly explain what a DNS server does and how a query is carried out. Modern web browsers are designed to cache DNS records for a set amount of time. Explain how caching DNS records can speed up the process of resolving an IP address. You can draw a diagram to help clarify your answer.</li>

  </ul></li>

</ul>




To submit your report, upload a PDF file on myCourses. Only one report needs to be uploaded per group.

<h2>4.2               Code</h2>

When submitting your report on myCourses, you should also upload a zip file containing your code. Make sure to include all source (.java) files required to compile and execute your program. Also include a readme file giving any special instructions required to compile your code and mentioning what version of Java or Python you used when writing/testing yourprogram.

<h1>5                    Additional advice</h1>

Java’s support for unsigned integers is somewhatlimitedcompared to otherlanguages. For example, a byte is an 8-bit signed integer (taking values in the range -128 to 127), whereas a char is a 16-bit (unsigned) Unicode character. See the <u>Java Primitive Data Types</u> tutorial for more.




Rather than using character input/output streams, consider using the class <u>java.nio.ByteBuffer</u> when preparing a packet to send. This will give you some control, byte-by-byte, of the data in the packet.
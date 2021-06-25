# Forensic Analysis

Forensic analysis of packet captures to find probably sequence of events during simulated attack

2 sets of packet captures
- baseline traffic
- traffic during attack

Tools for analysis
- Splunk

Since no alerts are in place, we will compare attack traffic to baseline traffic to find discrepancies.

We will start with the attack traffic to find traffic that looks suspicious. We will then look to see if this traffic is in fact anomalous.

### Suspicious Traffic 1

There are 2 events of interest: around 1:30AM on Mar25, there was a large number of "user account locked out" events, and around 9AM on Mar25, there was a large number of "password reset attempts"

![image](https://github.com/dmpeppin/BootCamp_Homework/blob/main/HW19Pic1.PNG)

Global Mitigation: Brute force attacks are generally mitigated by delays in authentication. The traditional response would be to increase the account lockout timeout time to make it time prohibited to fail to lockout. However this makes us susceptible to DoS attack where the account can potentially be forced to lockout indefinitely. Thus we will not go this route. Instead, I will recommend to introduce a 5 second delay to authentication. 5 seconds is not so long that it is time prohibitive to authorized users, but will slow down brute force attacks. We can also look at options to IP ban offenders. For example, if an IP address triggers, say, 5 lockouts, then ban this IP for, say, 30minutes. This will be a nuisance to authorized users to some extent of course, but much more time prohibitive to brute force attacks.

Additional Tasks: For these users, we can deactive these accounts from login and migrate these users to new accounts.



### Suspicious Traffic 2

![image](https://github.com/dmpeppin/BootCamp_Homework/blob/main/HW19Pic4.PNG)

![image](https://github.com/dmpeppin/BootCamp_Homework/blob/main/HW19Pic3.PNG)

If we compare the baseline (top image) to the attack (bottom image) we see an emergance of considerable traffic from Ukraine.

![image](https://github.com/dmpeppin/BootCamp_Homework/blob/main/HW19Pic2.PNG)
  
Closer inspection shows that there was a number of GET methods originating in the US around 6PM and a number of POST methods originating in the Ukraine around 8PM. Further inspection shows that there are 2 files being access in an excessive amounts. Likely the logon.php page is the target of the POST methods and the monolithic.jar logstash log is the target of the GET method. 

- Is the GET method hitting the logstash server from an authorized but misconfigured elasticsearch system? Check the IP if this is one of our own machines

![image](https://github.com/dmpeppin/BootCamp_Homework/blob/main/HW19Pic5.PNG)

Closer inspection of the useragent shows atypical useragents (top image is useragents during attack, bottom image is useragents during baseline)

![image](https://github.com/dmpeppin/BootCamp_Homework/blob/main/HW19Pic6.PNG)

Finally, it looks like there are a couple IP's prime for blocking, since all of the suspicous traffic comes from just 2 IPs

- US GET 209.91.156.11 lostash/monolithic.jar
- Ukraine 79.171.127.34 VSI_Account_login.php

These IPs are probably prime to ban.



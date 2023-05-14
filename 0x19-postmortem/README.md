Any software system will eventually fail, and that failure can come stem from a wide range of possible factors: bugs, traffic spikes, security issues, hardware failures, natural disasters, human error… Failing is normal and failing is actually a great opportunity to learn and improve. Any great Software Engineer must learn from their mistakes to ensure that they won’t happen again. Failing is fine, but failing twice because of the same issue is not.

A postmortem is a tool widely used in the tech industry. After any outage, the team(s) in charge of the system will write a summary that has 2 main goals:

To provide the rest of the company’s employees easy access to information detailing the cause of the outage. Often outages can have a huge impact on a company, so managers and executives have to understand what happened and how it will impact their work.
And to ensure that the root cause(s) of the outage has been discovered and that measures are taken to make sure it will be fixed.
Below is an example of a fictitious incident report.

Internal Server Error

Issue Summary

Start time: 11/3/23 9:00 AM, End time: 11/3/23 10:00 AM.
The WordPress page was returning a 500 status code, so the page was down for every user. Possibly the internal website server was corrupted or broken.
Root cause: typo in a WordPress settings document.
Timeline

11/3/23 9:05 AM — The issue was detected by several users, who contacted the customer support department.
11/3/23 9:10 AM — The issue was escalated to the System Engineering team and the SRE.
11/3/23 9:15 AM — They looked at the running processes on the server using ‘ps aux’ to see if any unwanted child process was running in the background, and keeping the server from responding.
11/3/23 9:20 AM — After seeing the processes looked fine, the team used ‘strace’ on some process ids including the ones of apache2 (the webserver hosting the WordPress page).
11/3/23 9:30 AM — strace on one of the apache2 processes was showing an infinite loop of system calls, so they looked at the second apache2 process, that was calling the system call accept4() and hanging.
11/3/23 9:35 AM — When using curl on the page’s IP while running strace on that second apache2 process, the team realized strace was displaying a lot of errors. One error was that the file index.html didn’t exist, but it was a misleading clue because adding the files in the WordPress folders didn’t seem to make it work.
11/3/23 9:40 AM — After reading carefully all the errors returned by strace, the team saw that one of them mentioned that a file didn’t exist: the file that apache2 was trying to access seemed to be terminating in ‘.phpp’, which is not a common extension for a file.
11/3/23 9:45 AM — When looking at the WordPress settings file, /var/www/html/wp-settings.php, line 134 was trying to require that faulty file. From then, the team just removed the extra ‘p’ at the end of the extension.
11/3/23 9:50 AM — The team only had to restart apache2 using ‘service apache2 restart’. The page went back live as it should.
Root cause and resolution:

Improper spelling in the WordPress settings file was found, causing apache2 to not work properly.
The issue was saved by removing the typo and restarting apache2.
Corrective and preventative measures:

Setting files should not have write permissions for anyone else than the SRE, in order to avoid injection of small typos like the one that was experienced in this incident.
TODO:

Change permissions on /var/www/html/wp-settings.php to read-only for the team.
Read carefully all setting files to look for other typos of that type.

![download](https://github.com/Barakacalvin/alx-system_engineering-devops/assets/30375880/08937bd0-4ec0-413b-9044-0d9dc2d52996)
500 Error Joke

Having blameless postmortems are a tenet of SRE culture. For a postmortem to be truly blameless, it must focus on identifying the contributing causes of the incident without indicting any individual or team for bad or inappropriate behavior. A blamelessly written postmortem assumes that everyone involved in an incident had good intentions and did the right thing with the information they had. If a culture of finger-pointing and shaming individuals or teams for doing the “wrong” thing prevails, people will not bring issues to light for fear of punishment.

Thank you for reaching this point. Kindly leave your reviews in the comment section!

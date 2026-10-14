# Intake CTF 2026: Desert Writeup

**Category:** Full pwn  
**Challenge Name:** Desert  
**Challenge Points:** 500 

![Challenge Description](/images/dessert_challenge.png)

## Initial Reconnaissance

Right off the bat, I wanted to check the user privileges for our current user, **bandit**. 

![Checking sudo permissions](/images/sudo_permisions.png)

By running `sudo -l`, I found that a binary located at `/opt/north_star/run` could be executed without a password as **root**. This is a huge finding; being able to run a binary as root without requiring a password immediately demanded my attention.

## Investigating Permissions

My next step was to check the file permissions within the `/opt/north_star/` directory. 

![Checking file permissions](/images/file_permissions.png)

With a simple `ls -la` command, I discovered that *run* was executable by anyone. I ran it, and the output was simply "sand". However, it was safe to assume that "sand" alone was not the complete flag. 

Thinking back to the challenge description, it mentioned looking "just beneath the sand...". 

## The Exploitation Phase

Upon checking the file permissions more closely, I noticed that while *run* was owned by root, there was a script named *solve.sh* that was fully writable by **bandit**. Also, found that *solve.sh* gets called by the *run* executable, I got to work injecting a simple bash-spawning command. 

![Injecting the payload](/images/injection.png)

Using `echo "/bin/bash" > /opt/north_star/solve.sh`, I overwrote the script. Knowing that *run* could be executed as root via `sudo`, I was eager to see the results of my injection.

![Gaining root access](/images/root.png)

Upon executing `sudo /opt/north_star/run`, a bash shell successfully spawned! Checking my permissions revealed that I was indeed the **root** user.

## Capturing the Flag

I went ahead and read the contents of the *.secret* file located in the directory. 

![Capturing the flag](/images/flag.png)

Sure enough, it revealed the flag: `Intake{th4t5_0n3_B1G_d1pp3r}`.

---

## Lessons Learned

Through this challenge, I was able to better understand the importance of checking user privileges and `sudo` permissions. In addition, thoroughly enumerating file permissions helped to identify a weak configuration where a lower-privileged user had write access to a script executed by root. Methodically performing these steps revealed the vulnerable binary, and the underlying flag that was waiting to be uncovered.

# Crack the Hash TryHackMe Tutorial

This is a tutorial on how to complete the [Crack the Hash](https://tryhackme.com/r/room/crackthehash) box on TryHackMe.

This is a relatively straightforward box that involves cracking a bunch of different hashes. The main tools we'll use are 
* [Crackstation.net](https://crackstation.net/) - a free website for cracking many types of hashes
* [hashcat](https://hashcat.net/hashcat/) - a command line tool for more advanced hash cracking
* [hashid](https://www.kali.org/tools/hashid/) - a command line tool for identifying the type of hash that was used
* Google - for googling things

Let's go!

# Level 1
## Task 1: 48bb6e862e54f2a795ffc4e541caed4d
Pretty simple. Just copy and paste it into Crackstation and it will spit out the result. As you can see, Crackstation tells us it's an MD5 hash.

The answer is listed under "Result."

![hash1](https://github.com/fatmagician/crack-the-hash/assets/51951855/55f4f380-e5e6-4467-ae59-f048e3000b75)


## Task 2: CBFDAC6008F9CAB4083784CBD1874F76618D2A97
Another easy one that can be solved using Crackstation. This time Crackstation tells us it's a SHA-1 hash.

![hash2](https://github.com/fatmagician/crack-the-hash/assets/51951855/4592283e-d9a1-48c7-9e52-bb3cad893cc8)


## Task 3: 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
Maybe you're starting to think this box is too easy, because Crackstation just spit out a third answer in a row for us. This time it was SHA-256.

![hash3](https://github.com/fatmagician/crack-the-hash/assets/51951855/c1d01a47-88f1-4003-9286-b05b5507f275)


## Task 4: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom
Uh oh. Crackstation didn't work this time...

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/ac8414b0-eae0-4f28-ac57-c2276e5d0963)

We're going to have to use a different tool to crack this one. Here's the steps I took:

### Step 1 - Figure out what type of hash algorithm this is
The hint points us in the right direction. Hashcat has a list of example hashes here: [https://hashcat.net/wiki/doku.php?id=example_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes). 
So search through the list and find the one that looks most similar. 

The "$2y$" at the beginning is what will help us find out what type of hash this is. Unfortunately, if you search the page for "$2y$" you won't find anything. So you may have to do a little bit of manual work and sleuthing to figure out which hash it is. Ultimately, the closest thing I could find was this:

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/1d14c4a7-0aa7-4531-8c8d-af5d2f35602a)

The wildcard in "$2*$" tells me that this might be the right one. I went to the [bcrypt Wikipedia page](https://en.wikipedia.org/wiki/Bcrypt) and verified that "$2y$" is, in fact, one of the valid algorithm identifiers for bcrypt.

An alternate (and easier) way to figure this out would have been to use hashid tool, which we'll demonstrate in a later task.

### Step 2 - Filter wordlist for 4-letter words
The hint says that the answer is 4 lowercase characters, and that it is also included in the rockyou.txt wordlist. There are probably multiple ways to do this so that cracking this hash won't take super long. 

What I did was filter rockyou.txt for 4-character strings and output this to a new file called rockyou2.txt. I used the `awk` command to do this:

`awk 'length($0) == 4' rockyou.txt > rockyou2.txt` 

### Step 3 - Use hashcat to crack the hash
Finally, we're going to use a hashcat dictionary attack to crack this. Run the command:

`hashcat -a 0 -m 3200 bcrypt.txt rockyou2.txt`

* -a 0: -a sets the attack mode. 0 is a dictionary attack.
* -m 3200: -m sets the hash type. 3200 is the bcrypt hash-mode we identified earlier.
* bcrypt.txt: this is a text file I created that contains the hash we're trying to crack.
* rockyou2.txt: this is the wordlist we're going to use in our dictionary attack.

Run the command...

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/6b04e1ab-a327-4a2b-8f6d-d12901fe4970)

And after a couple minutes, there's our answer:
![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/b5b4d8e9-b1d9-4fe0-9d5e-c430c9132eec)

If you want more practice with hashcat, another way to solve Task 4 would be to do a hashcat [mask attack](https://hashcat.net/wiki/doku.php?id=mask_attack). 

## Task 5: 279412f945939ba78ce0758d3fd83daa
That last one took a while. Let's paste this one into Crackstation and see if it works.

![hash5](https://github.com/fatmagician/crack-the-hash/assets/51951855/cb91c7da-e754-4a60-aa98-08d065676ef0)

Bingo!

Now it's time for...

# Level 2
## Task 6: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
The intro to Level 2 says it'll be harder, and that we might have to start using hashcat instead of online tools. But, in the interests of saving time, let's try this one out in Crackstation first.

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/28ae37ed-f1a7-4ac6-ae26-809fa96c6d6f)

It worked! Crackstation shows that this was a SHA-256 hash and cracked it for us. 

On to the next one.

## Task 7: 1DFECA0C002AE40B8619ECF94819CC1B

Again, Crackstation is easily able to crack this NTLM hash.

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/b0f2d05d-43ed-48a3-a6ee-96519139c8bc)

## Task 8: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02. ; Salt: aReallyHardSalt

This time we have a hash and salt, which Crackstation can't crack. Instead, we're going to use `hashid` to figure out which type of algorithm was used. First, we copy and paste the hash into a file called `task8.txt`. Then we run the below hashid command to figure out which type of algorithm was used:

`hashid -m task8.txt`

The "-m" tells it to show us the corresponding mode in hashcat that we should use to crack it. This is the output we get:

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/4fefefdf-ce9b-4808-881b-11d1f6e267cc)

The algorithm used is SHA-512 Crypt, and the mode for cracking that in hashcat is 1800. Now we'll try to run hashcat against that same file with 1800 as our mode:

`hashcat -a 0 -m 1800 task8.txt rockyou.txt`

Hashcat tells me that this will take about 12 hours to crack, and I don't feel like waiting that long. Luckily, TryHackMe tells us the answer is 6 characters long. We'll use the `awk` command like we did in Task 4 to filter our wordlist down to 6-character strings:

`awk 'length($0) == 6' rockyou.txt > rockyou6.txt`

Now `rockyou6.txt` contains only strings that are 6 characters long. In real life, you might not know that the password is 6 characters long, and you'll just have to wait. But if we know it's 6 characters long, we might as well take of advantage of the information we have available.

Hashcat still says it'll take an hour and a half to crack this, but that's much better than 12 hours. Go for a walk and come back in an hour. 

⏲️⏲️⏲️⏲️⏲️

After 33 minutes, we've finally cracked it:

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/7593ca11-af05-45a2-a1fe-80c46d74fa7f)


## Task 9: e5d8870e5bdd26602cab8dbe07a942c8669e56d6 ; Salt: tryhackme

Once again we have a hash and a salt, so Crackstation won't work. We'll instead paste it into a file called `task9.txt` in the format hash:salt. Then we'll run the following command with `hashid` to figure out what it is:

`hashid -m task9.txt`

Hashid gives us a few potential hash algorithms:

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/da0c9774-1823-44c1-b672-dd7c806eda33)

The hint TryHackMe gives us is "HMAC-SHA1." HMAC-SHA1 isn't on the the list of algorithms that hashid returned to us. The hashcat [example hashes page](https://hashcat.net/wiki/doku.php?id=example_hashes) lists a few different salted variations of SHA-1. Without that hint, there would probably be some trial and error in figuring out which one to use. But since we have the hint, we know to use mode 160. Run the following command with hashcat:

`hashcat -a 0 -m 160 task9.txt rockyou.txt`

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/8641fc34-4e73-4a1c-8103-f9cac602e767)

And Voila! We've completed this room!

I hope you ennjoyed this tutorial and got comfortable using hashcat, hashid, and Crackstation. Happy hunting.

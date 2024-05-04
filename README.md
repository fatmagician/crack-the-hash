# Crack the Hash TryHackMe Tutorial

This is a tutorial on the [Crack the Hash](https://tryhackme.com/r/room/crackthehash) box on TryHackMe.

This is a relatively straightforward box that involves cracking a bunch of different hashes. The main tools we'll use are [Crackstation.net](https://crackstation.net/) and [hashcat](https://hashcat.net/hashcat/). Let's go!

# Level 1
## Hash #1: 48bb6e862e54f2a795ffc4e541caed4d
Pretty simple. Just copy and paste it into Crackstation and it will spit out the result. As you can see, Crackstation tells us it's an MD5 hash.

The answer is listed under "Result."

![hash1](https://github.com/fatmagician/crack-the-hash/assets/51951855/55f4f380-e5e6-4467-ae59-f048e3000b75)


## Hash #2: CBFDAC6008F9CAB4083784CBD1874F76618D2A97
Another easy one that can be solved using Crackstation. This time Crackstation tells us it's a SHA-1 hash.

![hash2](https://github.com/fatmagician/crack-the-hash/assets/51951855/4592283e-d9a1-48c7-9e52-bb3cad893cc8)


## Hash #3: 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
Maybe you're starting to think this box is too easy, because Crackstation just spit out a third answer in a row for us. This time it was SHA-256.

![hash3](https://github.com/fatmagician/crack-the-hash/assets/51951855/c1d01a47-88f1-4003-9286-b05b5507f275)


## Hash #4: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom
Uh oh. Crackstation didn't work this time...

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/ac8414b0-eae0-4f28-ac57-c2276e5d0963)

So we're going to have to use a different tool to crack this one. Here's the steps I took:

### Step 1 - Figure out what type of hash algorithm this is
The hint points us in the right direction. Hashcat has a list of example hashes here: [https://hashcat.net/wiki/doku.php?id=example_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes). 
So search through the list and find the one that looks most similar. 

The "$2y$" at the beginning is what will help us find out what type of hash this is. Unfortunately, if you search the page for "$2y$" you won't find anything. So you may have to do a little bit of manual work and sleuthing to figure out which hash it is. Ultimately, the closest thing I could find was this:

![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/1d14c4a7-0aa7-4531-8c8d-af5d2f35602a)

The wildcard in "$2*$" tells me that this might be the right one. I went to the [bcrypt Wikipedia page](https://en.wikipedia.org/wiki/Bcrypt) and verified that "$2y$" is, in fact, one of the valid algorithm identifiers for bcrypt.

### Step 2 - Filter wordlist for 4-letter words
The hint tells that the answer is 4 lowercase characters, and that it is also included in the rockyou.txt wordlist. There are probably multiple ways to do this so that cracking this hash won't take super long. 

What I did was filter rockyou.txt for 4-character strings and output this to a new file called rockyou2.txt. I used the "awk" command to do this:

`awk 'length($0) == 4' rockyou.txt > rockyou2.txt`

Another way to do this would be to do a hashcat [mask attack](https://hashcat.net/wiki/doku.php?id=mask_attack). 

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
![image](https://github.com/fatmagician/crack-the-hash/assets/51951855/25c0e72e-4c58-4931-bec8-4b684c35910f)

## Hash #5: 279412f945939ba78ce0758d3fd83daa
That last one took a while. Let's paste this one into Crackstation and see if it works.

![hash5](https://github.com/fatmagician/crack-the-hash/assets/51951855/cb91c7da-e754-4a60-aa98-08d065676ef0)

Bingo!

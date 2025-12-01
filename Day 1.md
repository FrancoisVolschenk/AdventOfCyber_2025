Room to figure out linux terminal. 

Started in home dir of mcskiddy

~/README.txt
- warns of some tom foolery on the system. McSkiddy said that she will rewrite the security guide by the next day, and promises to hide it from plain view.
~/Documents
- Revealed a read-me-please.txt

```
From: mcskidy
To: whoever finds this

I had a short second when no one was watching. I used it.

I've managed to plant a few clues around the account.
If you can get into the user below and look carefully,
those three little "easter eggs" will combine into a passcode
that unlocks a further message that I encrypted in the
/home/eddi_knapp/Documents/ directory.
I didn't want the wrong eyes to see it.

Access the user account:
username: eddi_knapp
password: <redacted>

There are three hidden easter eggs.
They combine to form the passcode to open my encrypted vault.

Clues (one for each egg):

1)
I ride with your session, not with your chest of files.
Open the little bag your shell carries when you arrive.

2)
The tree shows today; the rings remember yesterday.
Read the ledger’s older pages.

3)
When pixels sleep, their tails sometimes whisper plain words.
Listen to the tail.

Find the fragments, join them in order, and use the resulting passcode
to decrypt the message I left. Be careful — I had to be quick,
and I left only enough to get help.

~ McSkidy

```

The above alluded to a secret side quest where we need to find 3 passphrases for unlocking a key. Switching to eddi_knapp user with `su - eddi_knapp` will put you in the home directory of eddi_knapp (alternatively, and the method that is preferred is to gain root access by running `sudo su` from mcskidy's profile. the root user will have all the same access to eddi-knapp's files and more)

Passphrase 1: found in the environment variables 
- run command `env` to print out environment variables 
- Look for PASSFRAG

Passphrase 2: 
- 1. Looked for anything suspicious in the logs by running `journalctl | grep -i passfrag`, but it only showed results pertaining to passfrag1.
- List all directories in eddi's home dir: `ls -a /home/eddi_knapp`
- notice 3 directories of interest: ".secret", ".secret_git", and ".secret_git.bak"
- cd into ".secret_git"
- it is a git repository, so try listing the git history: `git log --oneline`
- Get stopped by a dubious ownership error, but there is a fix in the output logs of the error (run the command in the error that is something along the lines of `git config --global --safe...`)
- retry `git log --oneline`
- see the previous commit where a secret note was added
- revert back to that commit `git checkout <enter the hash of that commit>`
- running `ls -a` now shows a "secret_note.txt" with passfrag2 

Passphrase 3: found in Pictures/.easer_egg = c0M1nG
- the clue pertains to pixels...
- List contents of the /home/eddi_knapp/Pictures directory
- see a hidden file ".easter_egg"
- read the contents for passfrag3

combining these results in ... <redacted>

Decrypting the file
/home/eddi_knapp/Documents/mcskidy_note.txt.gpg 
`gpg --decrypt /home/eddi_knapp/mcskiddy_note.txt.gpg`  and entering the passphrase gives the following

```
Congrats — you found all fragments and reached this file.

Below is the list that should be live on the site. If you replace the contents of
/home/socmas/2025/wishlist.txt with this exact list (one item per line, no numbering),
the site will recognise it and the takeover glitching will stop. Do it — it will save the site.

<list has been redacted, it should appear in your own walkthrough of the exercise>

A final note — I don't know exactly where they have me, but there are *lots* of eggs
and I can smell chocolate in the air. Something big is coming.  — McSkidy

---

When the wishlist is corrected, the site will show a block of ciphertext. This ciphertext can be decrypted with the following unlock key:

UNLOCK_KEY: <redacted>

To decode the ciphertext, use OpenSSL. For instance, if you copied the ciphertext into a file /tmp/website_output.txt you could decode using the following command:

cat > /tmp/website_output.txt
openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 -salt -base64 -in /tmp/website_output.txt -out /tmp/decoded_message.txt -pass pass:'<redacted>'
cat /tmp/decoded_message.txt

Sorry to be so convoluted, I couldn't risk making this easy while King Malhare watches. — McSkidy
```


Fixing the site yielded some more ciphertext


Following the steps in the message to decrypt the ciphertext yields the following

```
Well done — the glitch is fixed. Amazing job going the extra mile and saving the site. Take this flag <redacted>

NEXT STEP:
If you fancy something a little...spicier....use the FLAG you just obtained as the passphrase to unlock:
/home/eddi_knapp/.secret/dir

That hidden directory has been archived and encrypted with the FLAG.
Inside it you'll find the sidequest key.

```

Navigating to /home/eddi_knapp/.secret I ran
`gpg --output dir.tar.gz --decrypt dir.tar.gz.gpg`
then entered my given passphrase, then ran
`gunzip dir.tar.gz`
then 
`tar -xf dir.tar`

inside of the unzipped dir is a file:  sq1.png
with the side quest key.

Carrying on with the main quest

### ~/Guides/.guide.txt

```I think King Malhare from HopSec Island is preparing for an attack.
Not sure what his goal is, but Eggsploits on our servers are not good.
Be ready to protect Christmas by following this Linux guide:

Check /var/log/ and grep inside, let the logs become your guide.
Look for eggs that want to hide, check their shells for what's inside!

P.S. Great job finding the guide. Your flag is:
-----------------------------------------------
<redacted>
-----------------------------------------------

```

After that, read the logs of the auth.log file under /var/log/ directory
There are many failed password login attempts, indicating an attempted breach. 

At the advice of McSkiddy, we need to search for Eggsploits
`find /home/socmas -name *egg*`
Found `/home/socmas/2025/eggstrike.sh`

```
# Eggstrike v0.3
# © 2025, Sir Carrotbane, HopSec
cat wishlist.txt | sort | uniq > /tmp/dump.txt
rm wishlist.txt && echo "Chistmas is fading..."
mv eastmas.txt wishlist.txt && echo "EASTMAS is invading!"

# Your flag is:
# <redacted>

```


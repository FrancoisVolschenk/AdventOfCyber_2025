Today's challenge required access to the attack box as well as a target machine.

On the attack box, navigate to ~/Rooms/AoC2025/Day02
Inside of it, you will find a python web server. Starting this webserver and visiting it takes us to a spoofed TBFC login page that we have control of. 

The goal is to send a spear phishing attack to workers at the factory in wareville. 
We will be using setoolkit for this. 
1. Start setoolkit
2. On first menu, select "Social engineering attacks"
3. On the second menu, select "Mass Mailer attack"
4. Then enter the information provided to answer the prompts:
	- **Send email to**: Let’s begin by targeting `factory@wareville.thm`
	- **How to deliver the email**: We will choose `Use your own server or open relay`
	- **From address**: We know that the guys at the toy factory communicate regularly with Flying Deer, the shipping company, so that we will use `updates@flyingdeer.thm` as the source email address
	- **From name**: Let’s use the name `Flying Deer`
	- **Username for open-relay**: We will leave it blank and just hit the **Enter** key
	- **Password for open-relay**: We will leave it blank and just hit the **Enter** key
	- **SMTP email server address**: We will deliver directly to the TBFC mail server by entering `<target_machine_IP>`.
	- **Port number for the SMTP server**: We leave the default value of `25` and just hit the **Enter** key
	-  **Flag this message as high priority:** The choice is entirely up to you, depending on your knowledge of the circumstances, but we will answer with `no`
	- **Do you want to attach a file:** We will answer with `n`
	- **Do you want to attach an inline file:** Again, let’s answer with `n`
	-  **Email subject:** We need to think of something convincing, for example, “Shipping Schedule Changes”
	- **Send the message as HTML or plain:** We will keep the default choice of plaintext and just hit the **Enter** key
	- **Enter the body of the message, and type END (capitals) when finished:** Create and type any convincing message. Make sure to include the URL `http://<attackbox_ip>:8000` to check if the target will fall for this trick.
The message I entered:
```
Dear Elves,
We regret to inform you that due to the high demand of our services during this season, we are unable to fulfill your order on time. We ask thta you please navigate to http://<attackbox_ip>:8000 to view and approve our newly proposed schedule. Because of tight deadlines, we ask that you please action this by the end of the day today to prevent any further delays.
```

After closing off the message with `END`, setoolkit will send out the attack. Withing a few seconds you should get a request on your running server which shows the login details provided to the login portal.

Navigating to `<target_machine_ip>` in the attack box's browser shows an email client login page. Using the captured password from the attack and the name "factory" as the username, we get access to their emails. One of the emails in their inbox shows the number of toys ordered. 


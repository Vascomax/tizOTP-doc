# tizOTP
TOTP / HOTP app for Galaxy Watch® / Tizen® wearable

Disclaimer: the application is considered as add-on to your normal OTP app. Never should your Galaxy Watch® and tizOTP be the only single place where you keep OTP accounts. tizOTP is desiged as a secondary/sync-slave app. No liabilty for any damages or lost data. Usage at own risk.
The tizOTP development (although highly appreciating the work of the andOTP team) is currently independent from andOTP... Dont blame them for this app.

## Features
- offline read backup from andOTP (encrypted and plain); no data is loaded from the internet
- optional companion mode for andOTP (with minor modifications)
- optimized for 6 digits/ 30 second intervall SHA-1 TOTP
- 5 to 10 digits TOTP and HOTP support (RFC 6238 / RFC 4226)
- SHA-1, SHA-256, SHA-512 algorithm
- flexible TOTP intervalls (from 30 seconds up to 10 years)
- STEAM TOTP algorithm support
- automatic icon assignment built in for many websites
- Comes with a widget where the 4 favorite OTP Accounts are just a bezel turn away

This application reads either plaintext or password encrypted andOTP backup files (current andOTP encryption type since v0.6.3) offline and stores the accounts on the Galaxy Watch®. This is the stand-alone mode. Of course you don't need andOTP to create plaintext files with your otp accounts, as long as you have the secrets (Base32 encoded).
In the andOTP companion mode, tizOTP receives data directly from the andOTP app on the phone (if the galaxy version of andOTP with modifications is used).
An additional widget is provided where 4 accounts and their current TOTP/HOTP code is displayed.
Icons are automatically recognized and displayed based on the correct issuer name. (many issuers are already supported)

The needed privileges are: 
Filesystem.read (to read the andOTP backup file)
Application.launch (so the widget can trigger the new code generation process)

### Limitations so far:
- Only developed and tested for Tizen® 3.0 / 4.0 and 5.5 on Galaxy Watch® (42mm bluetooth with bezel). Maybe others might work, but are not supported so far
- EARLY access
- test version is limited to 3 accounts
- only 6 digit TOTP with 30s interval are officially supported (other formats are implemented as well, but there can be display problems). Also the global time circle and 5s warning colors are desinged for the standard 30s TOTP tokens.
- in companion mode: by design, all data and also counters for HOTP are only synced one direction. andOTP is always the master and can not be modified by tizOTP. That means, HOTP counters can be different on andOTP and tizOTP. As long as no counter change happens on andOTP, the tizOTP counter can increase separately. As soon as the counter changes on the andOTP, tizOTP syncs to that on the next start. This should be no problem since both apps provide the functionality to set the counter to any value any time.  

## Usage:
### Standalone mode / backup load
- name your encrypted andOTP backup file exactly "accounts.json.aes.mp3" or "accounts.json.mp3" if using plain text backup file
- transfer the file as music e.g. via the wearable app ("add music") into the "Music" folder on the Galaxy Watch® (file:///opt/usr/home/owner/media/Music)
- Install/ Start the main application
- At the first start (or until a backup is loaded) some example data is loaded for testing (if you cancel the password input with back key)
- To load the backup data enter the password for the andOTP backup, select the appropriate option (encrypted or plain) and press load (if plain text backup is used, you do not need to enter a password here. For internal data encryption a random key is created)
- After the backup is loaded, the .mp3 file can be deleted from the watch
### Companion mode (with andOTP Galaxy Version)
- set an automatic backup password in the andOTP settings on your phone (at first launch a random starting password will be set)
- enter that password in tizOTP, select the andOTP companion option and press load
- each time the tizOTP main app is started/shown in the future, tizOTP will update automatically (if andOTP is loaded on the phone. Can be in the background though)
### General
- Pressing the home button keeps the app on standby, which opens faster next time
- Pressing the back button will close the app completely. Loading internal account data takes a few seconds at next start up.
- A tap on the code in case of a HOTP will increase the counter and generate a new token.
- Long tap and hold for 2 seconds on the counter of a HOTP account to change the counter to any 16bit integer value (up to 2.147.483.647)

## Widget Usage:
- To use the widget add it to the watch and tab on one of the 4 icons to change the displayed account
- The main app will show all the accounts. Scroll to the account you wish to add and tap the icon to add it to the widget
- Taping on the code while in "Add Widget" screen will cause a HOTP account to generate a new token before adding to the widget. This mechanism is used while updating the HOTP from the widget. If you want to keep the current counter and Token, just tap the icon to add without update. Taping the code of a TOTP is the same as taping on the icon, since the time based tokens are only dependent on the current time
- A tap in the center of the widget will start the main app normal mode
- Widget usage is independent from main app (except for selecting accounts or updating HOTP)
- If you place tokens with more than 6 digits on the widget, up them on the right or left position to avoid display issues

After the initial setup, tizOTP will use the settings as default so there is no need to further interact. Setting a general pin code lock for your watch is highly recommended, since the watch will lock automatically in case you take it off.
To reset the app, delete the app data, or simply uninstall and reinstall the app.

Bug reports help to improve the app. There are many open issues, your feedback is used to prioritize.
Please file your bug reports and issues via GitHub: https://github.com/Vascomax/tizOTP-doc/issues

## Build andOTP To Support Companion Mode With tizOTP:
In order to sync automatically between andOTP and tizOTP using the Samsung Accessory Protocol (SAP), the Accessory SDK needs to be included in the andOTP build. This is already done in the andOTP Galaxy version, which can be downloaded from the Samsung Galaxy Store. 

If you want to make it work by yourself, you should be able to build your own andOTP version with android studio. Maybe if there is a higher demand for Galaxy Watch® support, it will be implemented in the official play store version too (currently only the Galaxy Store version has it and is not compatible with andOTP from the Google Play store)  
You can download the fork/branch on the GitHub repository https://github.com/Vascomax/andOTP/tree/GalaxyVersionMaster and build it for your phone. It will be kept in sync with the main andOTP development.  
Or you make the changes in your own fork. The changes are not big:
- 2 Files for the SAP SDK needs to be included as lib files. Copy them to the lib folder (create if not there). And right click on both files selecting "Add as libary"
- add the accessoryservices.xml to the res/xml folder
- make few additions in the AndroidManifest.xml to include the service and the permissions
- add the new tizen.class
- add a few lines in one existing class (EntryCardAdapter) to implement the syncing 
- add a method in existing settings.java class
-  .....   That's it. The rest of the changes are only cosmetical

In order to make it work you need to set a password for automatic backups in the andOTP settings (no need to activate the auto backup feature itself). This password is used to encrypt the data which is then sent to the Galaxy Watch® through the secureSend method of SAP only via bluetooth (no Internet/Network/Cloud transmissions). The encryption is identical to the normal andOTP password backup encryption (AES/GCM with 256bit key).

<sub>Tizen is a registered trademark of The Linux Foundation.  
Samsung Galaxy Watch® is a registered trademark of the Samsung Electronics Co., Ltd.  
Other names and brands may be claimed as the property of others.</sub>


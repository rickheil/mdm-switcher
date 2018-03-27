# MDM Switcher

I ran into the pain of switching MDMs and made this package. You can deploy this using Munki or whatever your management agent of choice is.

My big problem here was that installing with Munki meant that MDM never picked up on which user to register, meaning I could install device scoped profiles but not user scoped. The solution is to simply pass the current console user to `profiles` when enrolling.

Note: this is geared for 1:1 deployed machines.

## How to use
After cloning the repo, take the following steps:

 1. Place a copy of the new MDM's enrollment profile in payload/var/tmp/switch-mdm and name it "e.mobileconfig" (there's a blank file there now)
 2. Edit scripts/preinstall to have your old MDM's profile identifier in it
 3. Edit build-info.json to customize the receipt as well as add your Developer ID to it in order to have a nicely signed pkg
 4. Run [munkipkg](https://github.com/munki/munki-pkg.git) and it will spit out a lovely signed package for you to import.

## How it works
Preinstall removes the old MDM profile.
The payload simply puts the enrollment profile in /var/tmp/switch-mdm in prep for the postinstall.
The postinstall picks up the profile and passes the current console user to the profiles command. This ensures at least the current user is sent over to receive user scoped profiles.

## How do I find my MDM's profile identifier?
Generally you can find this by running `sudo profiles -P` on an enrolled machine. Look for one with your MDM provider's name in it that's a computer scope. Some examples:
* Meraki: com.meraki.sm.mdm
* SimpleMDM: com.unwiredmdm.mobileconfig.profile-service
* Apple Profile Manager: com.apple.mdm.your-servers-host-name

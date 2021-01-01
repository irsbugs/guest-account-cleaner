# guest-account-cleaner

Ubuntu Desktop distros have a *Users and Groups* GUI utility to create new user accounts. For example a *Guest* account may be created. In the Guest account the user may download or create folders and files. At the end of the users session it may be desirable to remove all these files so the Guest account is in a clean state for the next Guest user.

## Creating a Guest Account

The Ubuntu Mate 20.04 desktop is used in the following example of creating a Guest account. 

* Log into an account that has privilege.
* From the System Menu bar click on **Users and Groups**.
* In the *User Settings* window click on **Add**.
* Provide the password to your privileged account.
* In the *Create New User* window provide the name **Guest** and a Username of **guest**. Click **OK**.
* Provide a password and confirmation, plus check the box for *Don't ask for password on login*. Click **OK**.
* In the *User Settings* window, for the Account Type, click on **Change**. 
* In the *Change User Account Type* click the option for *Desktop user*. Click **OK**.
* On the main *User Settings* window click **Close**.
* Log out of the current account and log into Guest

## Adding the Account Cleaning Capability.

* Log into the Guest account.
* Use the Firefox web-browser to connect to this github repository. I.e. https://github.com/irsbugs/guest-account-cleaner
* Set Firefox so it displays its Menu bar. I.e. Right click near the top of the Firefox window and in drop down menu select *Menu Bar*.
* Click on the python file *guest_account_cleaner*
* Click on the *Raw" icon.
* With the web-browser now displaying teh contents of the file, on the Main Menu bar, click on File --> Save Page As.
* When prompted for a location to save the file select the *home* folder of the guest account.
* Before saving the file, place a dot at the beginning of the file name. E.g. .guest_account_cleaner.
* Click on **Save**.
* Open a terminal session in the home folder of the Guest account.
* Check the file exists as a hidden file...
```
$ ls -lA .guest_account_cleaner
-rw-rw-r-- 1 ian ian 3750 Jan  2 10:41 .guest_account_cleaner
```
* Make the file executable...
```
$ chmod +x .guest_account_cleaner
$ ls -lA .guest_account_cleaner
-rwxrwxr-x 1 ian ian 3750 Jan  2 10:41 .guest_account_cleaner
```
* Review the file so that the last lines of the file can be copied...
```
$ cat .guest_account_cleaner
#!/usr/bin/env python3
#
# .guest_account_cleaner
# Deletes all non-hidden files in the Guest account.

--- missing text ---

The following is added to the end of the file /home/guest/.profile

# Run python script to clean out the Guest directory
# Added by:           date:
if [ -f ~/.guest_account_cleaner ]; then
    python3 ~/.guest_account_cleaner
fi
        
"""
```
* Launch an editor, for example pluma, to edit the .profile file...
```
$ pluma .profile
```
* Paste the following at the end of the .profile file and add your name and the date...
```
# Run python script to clean out the Guest directory
# Added by: Ian - 2021-01-01
if [ -f ~/.guest_account_cleaner ]; then
    python3 ~/.guest_account_cleaner
fi
```
* The Guest account is now be ready to be used.


## Testing

### Removal of folder and files...

* Create *test.txt* file in guest/home folder.
* Create a folder in guest/home called *test*. In this test folder create a file called *test.txt*
* In the Documents folder create a file called *test.txt*
* Now log out of Guest and log back in. The folder test and all files named test.txt should no longer exist.

### Preventing a file from being deleted...

Maybe there is a file you want to always exist in the *guest/Desktop* folder. For example the Firefox launcher can be dragged and dropped onto the desktop. This will provide a file called *guest/Desktop/firefox.desktop*. The desktop icon is renamed *Network*. Thus the file becomes renamed as *guest/Desktop/Network.desktop*.

The *.guest_account_cleaner* python program contains two functions:
```
def backup_files():
def restore_files():
```
These functions current contain sample code that will backup the *guest/Desktop/Network.desktop* file, if it exists, to the /tmp folder. Later, after cleaning up of the guest directory has been completed, then it will restore the file from the /tmp folder and place it in the guest/Desktop folder.

This is the example code that performs the backup...
```
    # If Desktop/Network.desktop exists copy to /tmp
    if os.path.isfile(homedir + "Desktop/Network.desktop"): 
        shutil.copy2(homedir + "Desktop/Network.desktop", "/tmp/Network.desktop")
```

This is the example code that performs the restore...
```
    # If Network.desktop exists in /tmp copy to ~/Desktop 
    if os.path.isfile("/tmp/Network.desktop"):
        shutil.copy2("/tmp/Network.desktop", homedir + "Desktop/Network.desktop")
```

If you have some particular file that should remain in the guest account between logging out and logging back in, then modify these two functions in the .guest_account_cleaner file to meet your requirements.

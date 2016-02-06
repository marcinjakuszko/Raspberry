
#Fed up with pi@raspberry? 
This article is about how I changed a default user on Raspbian.

## Concept and requirements
While logging on to my Raspberry through ssh, I noticed, that I am little bit bored with the stock system account `pi@raspberry`. I decided to personalize my Raspbian, with my own user account to use customized login name. Nevertheless I wanted to preserve:
- Auto-login function to graphical desktop and terminal without need to provide password (for my new account)
- Groups and user permissions
- Stock, out of the box, Rapsbian login, with diferent login name
All furhter actions I done on Raspbian Jessie (Nov. 2015 image).

## Performed steps 
In order to achive goals above described goals, I did:

1. Create new user  
    
    ```
    sudo adduser <username>
    ```
    
2. Check what groups `pi` account have and then assign those groups to the new account 
    
    ```
    sudo usermod -a -G `groups pi | sed -e 's/\s\+/,/g' | sed -r 's/^.{5}//'` <username>
    ```
    
3. Add new account a sudoers rights by adding a line: 
    
    ```
    <username> ALL=(ALL) NOPASSWD: ALL
    ```
    
    to `/etc/sudoers` using `sudo visudo` command
4. Make a new user autologged into graphical system by modifing LightDM config file with `sudo nano /etc/lightdm/lightdm.conf`.
    Replacing a line:

    ```
    autologin-user=pi 
    ```

    with:
    
    ```
    autologin-user=<username>
    ```
    
5. Make a new user autologged into system, while starting it up. In order to  do that I need to modify `systemd` auto login service property (on Raspbian Jessie). 
    Run `sudo nano /etc/systemd/system/autologin@.service` and replaced a line: 

    ```
    ExecStart=-/sbin/agetty --autologin pi --noclear %I $TERM
    ``` 
    with 

    ```
    ExecStart=-/sbin/agetty --autologin <username> --noclear %I $TERM
    ```

6. Restarted Raspberry Pi. It will boot up into newly created user account.

Enjoy!


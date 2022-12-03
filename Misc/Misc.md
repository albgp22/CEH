# Xauthority file

```
The .Xauthority file can be found in each user home directory and is used to store credentials in cookies used by xauth for authentication of X sessions. Once an X session is started, the cookie is used to authenticate connections to that specific display. You can find more info on X authentication and X authority in the xauth man pages (type man xauth in a terminal).

So, if you are not the owner of this file you can't login since you can't store your credentials there.

This situation usually arises when you execute a GUI application (for instance nautilus) with root permissions by typing sudo nautilus. You can avoid it (for 12.10 and older versions) by invoking the app with gksudo nautilus, or in any version using sudo -H nautilus.
```
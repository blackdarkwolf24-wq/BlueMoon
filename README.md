# Bluemoon Report by Zul Arif Danial (52215125986)

- First, I ran `ifconfig` to find my own (attacker) IP address. Then I scanned the network with `nmap -sn 192.168.56.0/24` to see which hosts were alive. Four hosts showed up, and I picked `192.168.56.103` as my target.

  ![Finding the target IP](images/image1.png)

- Next, I ran `gobuster` against the target IP to look for hidden pages or files that could be useful. It found two paths: `server-status` and `hidden_text`.

  ![Gobuster scan results](images/image2.png)

- [http://102.168.56.103/server-status](http://102.168.56.103/server-status) just gives a Not Found error.

  ![server-status Not Found](images/image3.png)

- [http://102.168.56.103/server-status](http://102.168.56.103/server-status) is different. The page says the website is under maintenance and will be back online soon. One thing I noticed is that the "Thank You…" text is highlighted in blue, and clicking on it redirects us to another website.

  ![Maintenance page with highlighted link](images/image4.png)

- We get redirected to [http://102.168.56.103/server-status](http://102.168.56.103/server-status) and the page shows a QR code around the middle right side.

  ![Redirected page with QR code](images/image5.png)

- I downloaded the QR code and saved it in `/home/kali/Documents/`.

  ![QR code saved locally](images/image6.png)

- After decoding it, the QR code gave me an unknown host, a username and a password.

- With that information, I was able to log in as `userftp` using the `ftp` command.

- Once I was in, I ran `ls -la` to list all the files and folders.

- Two files stood out to me, `information.txt` and `p_lists.txt`, so I downloaded both to my machine.

  ![FTP login and file listing](images/image7.png)

- I used the `cat` command to open both of the downloaded files, `information.txt` and `p_lists.txt`.

- `information.txt` is a message to a user named Robin about his/her weak password, and it says a better password will be recommended later.

- `p_lists.txt` is just a list of new passwords.

  ![Contents of information.txt and p_lists.txt](images/image8.png)

- Then I used Hydra to brute force Robin's password, and it came back with `k4rv3ndh4nh4ck3r` as the likely one.

  ![Hydra brute-force result](images/image9.png)

- With that, I logged in through ssh as [robin@193.168.56.103](mailto:robin@193.168.56.103) and it worked.

  ![SSH login as robin](images/image10.png)

- I looked through the files and folders and found `user1.txt`.

- Inside `user1.txt` is the User-1 flag.

  ![User-1 flag](images/image11.png)

- After that, I went to the project directory and checked what was inside `feedback.sh`.

- The script asks for user input and runs the `$feedback` variable without any checking, so it can be abused for command injection.

- I ran `sudo -l` to see what robin is allowed to do with sudo. It turns out robin can run `/home/robin/project/feedback.sh` as user jerry without needing a password.

  ![sudo -l output](images/image12.png)

- So I ran `sudo -u jerry /home/robin/project/feedback.sh` to get a bash shell as jerry.

  ![Shell as jerry](images/image13.png)

- Then I moved to `/home/jerry` and ran `ls -la` to see what was there.

- One of the files, `user2.txt`, holds the second user flag.

- For the last step, I noticed jerry is a member of the docker group, so I abused the Docker socket. I mounted the host's root directory inside a temporary Alpine container using `docker run -v /:/mnt --rm -it alpine chroot /mnt sh`.

- This gave me root access to the host, and I grabbed the final root flag from `/root/root.txt`.

  ![Root flag via Docker socket](images/image14.png)

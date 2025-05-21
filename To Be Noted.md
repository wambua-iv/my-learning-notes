Upon Organization exit consolidate certificates, especially on Certmetrics

Mistakes were made and not by me

#### Fingerprint on Broadcom

Fingerprint reader 0a5c:5843 Broadcom Corp. 58200 Ubuntu / Linux mint driver install

First, we need to checkout the following repo containing the driver:

|   |   |
|---|---|
|1|`git clone https:``//git``.launchpad.net/~oem-solutions-engineers``/libfprint-2-tod1-broadcom/``+git``/libfprint-2-tod1-broadcom/`|

Then go into the cloned folder and issue the following commands:

|            |                                                            |
| ---------- | ---------------------------------------------------------- |
| 1<br><br>2 | `sudo sh install.sh`<br><br>`python3 debian /update-fw.py` |

You may need to restart the laptop in order the changes to be applied.

Finally, enable fingerprint login using the following command:

|   |   |
|---|---|
|1|`sudo` `pam-auth-update`|

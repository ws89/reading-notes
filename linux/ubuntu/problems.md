# No WIFI driver (offline) 

OS: Ubuntu Kylin-17.04

laptop: Lenovo YOGA PRO3



**References**

- [How to install Broadcom wireless drivers offline?](https://askubuntu.com/questions/626642/how-to-install-broadcom-wireless-drivers-offline)

**Steps:**

extract  *.deb from iso file

```
ubuntukylin-17.04-enhanced-amd64.iso:\pool\main\d\dkms\dkms_2.3-3_all.deb
ubuntukylin-17.04-enhanced-amd64.iso:\pool\restricted\b\bcmwl\bcmwl-kernel-source_6.30.223.271+bdcom-0ubuntu2_amd64.deb
```

install debs

```
sudo dpkg -i kms_2.3-3_all.deb
sudo dpkg -i bcmwl-kernel-source_6.30.223.271+bdcom-0ubuntu2_amd64.deb
```

disabled secure boot in BOIS

restart system



-----







---

created at 2017-07-21 00:58
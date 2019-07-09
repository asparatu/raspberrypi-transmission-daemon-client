# How to change the default hostname

We are going to change the default hostname of the Raspberry Pi becauase all new installation have hostname **raspberrypi**.

We are going to edit the **hosts** and **hostname** file in the **etc** directory.

```bash
sudo nano /etc/hosts
```

Look for the **127.0.0.1 raspberrypi** change the **raspberrypi** to what you want your hostname to be.

Save the file by pressing **ctrl + x** and Press **y** and press **Enter**

We need to change the name in hostname file

```bash
sudo nano /etc/hostname
```

Change the **raspberrypi** to what you changed in **hosts** file and save the file.

Reboot the Raspberry Pi so the new hostname can be used. You should be able to connect to the Raspberry Pi using **(yourhostname).local** instead of **raspberrypi.local**.

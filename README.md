# Cracking-WiFi-WPA2-Handshake

Here, I'm going to use ```airmon-ng``` to discover wireless network around me. Then deauthenticate[^1] clients from a specific wireless network, so we can capture the four-way handshake in a ```.cap``` file. Then open up the four-way handshake within **Wireshark** then decrypt or hack the WiFi Password.

[^1]:  A deauth or deauthentication attack disrupts connections between users and Wi-Fi access points. The attackers force devices to lose access and then reconnect to a network they control. Then, perpetrators can track connections, capture login details, or trick users into installing rogue programs.

For running this operation, here the tools I used:
1. [Alfa Network Adapter](https://www.amazon.com/gp/product/B004Y6MIXS/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B004Y6MIXS&linkCode=as2&tag=bombal-20&linkId=c7a2af4cac2d001ba29fa4ed27263e53)
2. Kali Linux
3. [Airmon-ng](https://www.aircrack-ng.org/doku.php?id=airmon-ng)

*Note: Why we can't use our own personal built-in WiFi adapter? Because normally our built-in WiFi adapter might doesn't have monitor and injection mode. They only have the managed mode. If you have it, you can use it.*

## Begining the Process

### Kali Setup
Here, I'm running a Virtual Kali linux on my Windows Machine. For virtualization, I used the VMWare Workstation. 

### Plug in Network Adapter
First thing, is plug in the WiFi network adapter to my computer. After I plug-in, VMWare asked me, where I want to connect this network adapter? In my example, I want to connect to the Kali Virtual Machine. 

![Screenshot (55)](https://user-images.githubusercontent.com/93491482/231320658-4c345144-0dfe-4908-a717-f6c3a7fdb493.png)


### Varify that network adapter is recognized by Kali Linux
Now, after plug-in the network adapter, we need to varify that our Kali Machine recognize this Network Adapter. For this, we need to open up a terminal window, and type the following command to see IP addresses and interfaces on this virtual machine:

```
ip addr
```

![Screenshot_2023-04-11_17_52_37](https://user-images.githubusercontent.com/93491482/231321157-deba7422-16ec-48b6-bb3e-484f71fc4c48.png)

This is what we are wanna see, we've got an wireless LAN adapter, which at this moment is down. But it's important that we can see a WiFi network adapter. 

We can also use the ```iwconfig```, which shows us that we have a WiFi network adapter. At that moment the mode for ```wlan0``` is **managed**.

![Screenshot_2023-04-11_17_55_00](https://user-images.githubusercontent.com/93491482/231324838-e5ad4ff7-7f1e-4ab3-a240-6a187d857620.png)

We are going to change to *Monitor Mode* from *Managed Mode*. But before make sure is that we got a WiFi network adapter that recognize by Kali. 


### A WPA2 Network to Attack or Crack
For this demonstration, I used my personal built Raspbery Pi router. This is the network, which I personally owned. 

***Note: Never ever try on this on actual WiFi router without owner's concern. Just attack on the network that you own or have permission to attack. As a ethical hacker, our job is to follow the legal way.***


### Check your Network Adapter is on Managed Mode.
Once again, we are going to check that, our network adapter is on Managed mode by using the ```iwconfig``` command. 


### Kali Version
Before we start the attack, we need to make sure our kali in up-to-date and current version. To check this we can use those two following command. Either one is fine.

```
cat /etc/os-release
```
or
```
uname -a
```

![Screenshot_2023-04-11_17_57_16](https://user-images.githubusercontent.com/93491482/231328664-d8ee793d-0ed7-4254-9f09-b791a7128588.png)


### Kill Conflicting Processor with airmon-ng
Before we start the process, we need to kill any conflicting processes that might create future issues. To check for any conflicting processes and kill them, we are using the following command:

```
sudo airmon-ng check kill
```

![Screenshot_2023-04-11_17_58_37](https://user-images.githubusercontent.com/93491482/231329207-ed6435f0-80b3-442d-91ac-89b41a4c4598.png)

we can see that ```wpa_supplicant``` was killed. In your example, you may see a whole bunch of other processes that get discovered and get killed and that's fine. Just make sure, you type this command so that you don't have any conflicting processes that interfere with what we're trying to do. 


### Put interface into Monitor Mode from Managed Mode
Our ```iwconfig``` shows us that the wireless network interface is in managed mode, but what we wanna do is put into monitor mode by using this following  command:

```
sudo airmon-ng start wlan0
``` 

![Screenshot_2023-04-11_18_01_04](https://user-images.githubusercontent.com/93491482/231330148-be4c102f-7a91-444a-a424-2ff564a9dc10.png)

Now we see monitor mode is now enabled. ```iwconfig``` shows us that the mode has changed. We can also confirm that by typing the following command:

```
sudo airmon-ng
```

![Screenshot_2023-04-11_18_01_55](https://user-images.githubusercontent.com/93491482/231330412-5576c25a-fbdf-4a31-8dc7-4c8bb865669b.png)

***Note: Now we can notice that the wireless interface is now ```wlan0mon```. Before it was ```wlan0``` but now it's changed to ```wlan0mon```***


### Discover the victim network with airodump-ng
Now, let's discover the victim's access points. To do that we are use the following command:

```
sudo airodump-ng wlan0mon
```

![Screenshot_2023-04-11_18_04_23](https://user-images.githubusercontent.com/93491482/231331427-6ab3ca3e-8622-4922-aa26-7695851062c6.png)

As we can see a whole bunch of a wireless networks are discovered near me. Here are various SSIDs or MAC Addresss of the wireless networks. After my viction network shown, I can break the process by typing ```Command + C```. The network in this demonstration, i'm intereted in **encryptedMuhfat**.

***Notice the channel number. Here I used Channel 1.***

Here MAC address is also important. We need to make a note of that. 

The upper data block shows the access points found:

| Name | Discription |
|------|-------------|
| BSSID |	The MAC address of the AP |
| RXQ |	Quality of the signal, when locked on a channel |
| PWR	| Signal strength. Some drivers don't report it | 
| Beacons |	Number of beacon frames received. If you don't have a signal strength you can estimate it by the number of beacons: the more beacons, the better the signal quality |
| Data |	Number of data frames received |
| CH |	Channel the AP is operating on |
|MB |	Speed or AP Mode. 11 is pure 802.11b, 54 pure 802.11g. Values between are a mixture |
| ENC |	Encryption: OPN: no encryption, WEP: WEP encryption, WPA: WPA or WPA2 encryption, WEP?: WEP or WPA (don't know yet) |
| ESSID	| The network name. Sometimes hidden |


### Use airodump-ng to view only one network
We can use the following command to see only that access point:

```
sudo airodump-ng wlan0mon -d <MAC_address>
```

For my example, my MAC Address is **38:A2:8C:A2:08:B8**

![Screenshot_2023-04-11_18_08_33](https://user-images.githubusercontent.com/93491482/231334236-76612ae2-05ae-43fe-8e93-849bb21a3bf5.png)

So, we can see the BSSID, we can see how many beacons are being sent, ESSID or the name that we as a human would use. 
We can leave it like that for a moment and check if I connect to this network form my phone or any other different device is it actually shows or not?
To do that, I use my personal phone and try to connect to that network. As I connect to this network, in the terminal, we can see a clint has connected to this network. 

![IMG_7262](https://user-images.githubusercontent.com/93491482/231335451-733f04b9-9e4b-4d4b-b130-2ab2277cd04a.PNG)
![Screenshot_2023-04-11_18_08_44](https://user-images.githubusercontent.com/93491482/231335807-e9afc0f1-ef73-44c3-8936-95aba8ee1dca.png)

After confirm that it is successfully work, we can cancle that.


### airodump-ng capture WPA2 four way handshake
Now, we are use the following command, to capture the traffic with four way handshake in a ```.cap``` file. 

```
sudo airodump-ng -w <filename> -c <Channel_Name> --bssid <MAC_address> <network_interface>
```

As our example we use this command:

```
sudo airodump-ng -w hack1 -c 1 --bssid 38:A2:8C:A2:08:B8 wlan0mon
```

Note: hack1 is the name of the file that we're gonna store the captures in. 
-c is the channel is 1.

After enter above command, here we can see in the terminal: 

![Screenshot_2023-04-11_18_10_39](https://user-images.githubusercontent.com/93491482/231337091-7a921308-aaca-4c35-80fe-6d08dd4db3d0.png)

We can leave this terminal for a section and open another terminal.

### Use aireplay-ng to deau thenticate client
In the second window, deauthenticate clients from the network by using the following command:

```
sudo aireplay-ng --deauth 0 -a 38:A2:8C:A2:08:B8 wlan0mon
```

If we notice in our first terminal we can see there is nothing displayed on top, that mean we haven't capture the four-way handshake. 
Notice, there a whole bunch of deauthentication are sending from second terminal window. 

In my phone, it's moved from one access point to the other. 

![IMG_7263](https://user-images.githubusercontent.com/93491482/231345133-4974a238-3053-410c-9b7b-13c15d94ae24.PNG)

So, I will try connect back from my phone to the **encrptedMuhfat** network. as soon as I reconnect to **encryptedMuhfat** from my phone, in the 1st terminal, it displays WPA Handshake. 

![Screenshot_2023-04-11_18_13_09](https://user-images.githubusercontent.com/93491482/231339820-81dd4a48-a0d3-4d8c-ae79-34136ba83e4a.png)


### WPA2 four way handshake was captured
We can see the WPA handshake was captured. Client is not able to connect to the network but we capture the handshake. Then we can stop the running processes by command + C (mac) in both window. 

This is a another type of attack. You can do a denial of service attack against an access point just to stop anyone connecting to it network access. 

Now if use use ```ls``` we can see we capture the file, the file name is ```hack1-01.cap``` 

![Screenshot_2023-04-11_18_15_09](https://user-images.githubusercontent.com/93491482/231340631-2aa593af-05c6-4d80-9968-df5d9731dd13.png)


### Use Wireshark to view WPA2 four way hand shake
Now use can see the file using the wireshark by the following command:

```
wireshark hack1-01.cap
```

![Screenshot_2023-04-11_18_16_21](https://user-images.githubusercontent.com/93491482/231340784-9f7a6cdf-4671-47ab-9700-542d4f95e8c0.png)

There a whole bunch of information has been capture. Now, I'm gonna search for the handshake to use the filter and type ```eapol```.

Notice here, we can see message one, message two, message three, and message four. So we have capture the WPA four way handshake. We had some additional messages, but here's the full four way handshake between the **encryptedMuhfat** and my phone. 

![Screenshot_2023-04-11_18_17_14](https://user-images.githubusercontent.com/93491482/231341425-1d03c90b-6884-4686-b6dd-538aeca0f6d2.png)

If we notice the message two, we can see WPA key data send from the phone to the encryptedMuhfat access point, and that's wjay we wanna crack. That means, our phone is sending authentication information to the accesspoint, we're capturing that and that's what we're going to decrypt. 

![Screenshot_2023-04-11_18_18_47](https://user-images.githubusercontent.com/93491482/231341763-ec46ea85-263a-439c-9b8d-b9eccedf9e4e.png)

Now we can close that wireshark. 


### Put Interface back into managed mode
At the moment, the wifi interface is still in monitor mode. So, what I'll do is stop monitor mode by using the following command:

```
sudo airmon-ng stop wlan0mon
```

![Screenshot_2023-04-11_18_20_39](https://user-images.githubusercontent.com/93491482/231342500-36c18f34-cdae-49e4-93c8-59ff0f702957.png)


***Note: Another reason use the VM is in this example, I've got two network adapter. The Kali Linux machine is actually using the WiFi adapter of Windows to give it internet connectivity. But external ALFA adapter is in monitor mode.***


### Crack WPA2 password with aircrack-ng
Now if we use ```ls``` again, it shows me the captured fil that we wanna use to cracking. And to do that, we are going to use this command:

```
aircrack-ng hack1-01.cap -w /usr/share/wordlists/rockyou.txt
```

![Screenshot_2023-04-11_18_26_14](https://user-images.githubusercontent.com/93491482/231344347-c576fab7-e833-4121-85a7-1ad6a691a86b.png)

Here we are using the wordlist that are store in our built in Kali Machine. We can find those wordlist on ```/usr/share/wordlists```. Normally it stays as zip file.  

![Screenshot_2023-04-11_18_22_42](https://user-images.githubusercontent.com/93491482/231343825-96b19a8b-fd4f-4c0d-a138-e37b3cd271c9.png)

We need to unzip ```rockyou.txt.gz``` file to ```rockyou.txt``` file to use it. To unzip ```rockyou.txt.gz``` file, use the following command:

```
sudo gzip -d rockyou.txt.gz
```

![Screenshot_2023-04-11_18_24_39](https://user-images.githubusercontent.com/93491482/231344080-6df86d07-c710-4214-ab8e-c2a6a75bcb85.png)

Here, we can also see the total number of word list by the following command:

```
wc -l rockyou.txt
```

![Screenshot_2023-04-11_18_24_58](https://user-images.githubusercontent.com/93491482/231344289-7af27aef-6ab6-4155-a2f5-76dbff3e02e9.png)


Now back to the cracking password, if we run above command, it will compare with all the 14 million password in our rockyou.txt file. If it hit the correct one, it will prompt on the screen. 

![Screenshot_2023-04-11_20_28_25](https://user-images.githubusercontent.com/93491482/231344782-8d2d082f-b3ef-493e-b197-8c01e46ec10e.png)


**Note: Try to use the strong and very long password to protect your wifi.**









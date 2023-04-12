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








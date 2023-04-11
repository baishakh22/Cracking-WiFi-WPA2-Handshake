# Cracking-WiFi-WPA2-Handshake

Here, I'm going to use ```airmon-ng``` to discover wireless network around me. Then deauthenticate[^1] clients from a specific wireless network, so we can capture the four-way handshake in a ```.cap``` file. Then open up the four-way handshake within **Wireshark** then decrypt or hack the WiFi Password.

For running this operation, here the tools I used:
1. [Alfa Network Adapter](https://www.amazon.com/gp/product/B004Y6MIXS/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B004Y6MIXS&linkCode=as2&tag=bombal-20&linkId=c7a2af4cac2d001ba29fa4ed27263e53)
2. Kali Linux
3. [Airmon-ng](https://www.aircrack-ng.org/doku.php?id=airmon-ng)

*Note: Why we can't use our own personal built-in WiFi adapter? Because normally our built-in WiFi adapter might doesn't have monitor and injection mode. They only have the managed mode. If you have it, you can use it.*



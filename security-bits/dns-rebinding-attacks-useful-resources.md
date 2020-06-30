# DNS rebinding attacks - Useful resources

During the recent pen testing workshop I attended, I was reminded of the attack that could allow remote access to the devices setup on your local network.

> Picking up the quote from a blog:   
> _Put simply, DNS rebinding allows a remote attacker to bypass a victim’s network firewall and use their web browser as a proxy to communicate directly with devices on their private home network. By following the wrong link, or being served a malicious banner advertisement, you could inadvertently provide an attacker with access to the thermostat that controls the temperature in your home._

In this note, I just plan to share the resources I found very useful to understand and replay the attack.

Blog on Medium showcasing how to attack Private Networks from the Internet with DNS Rebinding : [https://medium.com/@brannondorsey/attacking-private-networks-from-the-internet-with-dns-rebinding-ea7098a2d325](https://medium.com/@brannondorsey/attacking-private-networks-from-the-internet-with-dns-rebinding-ea7098a2d325)

{% hint style="danger" %}
My takeaway: We do not apply authorization on our local REST API thinking our router has it. Protocols like UPnP are built around this idea that devices on the same network can trust each other. This is the problem.
{% endhint %}

Blog on Wired.com detailing how millions of streaming devices are vulnerable to a retro web attack : [https://www.wired.com/story/chromecast-roku-sonos-dns-rebinding-vulnerability/](https://www.wired.com/story/chromecast-roku-sonos-dns-rebinding-vulnerability/)

> DNS rebinding attacks have been brought up many times in the past, but new features in Internet of Things devices including geolocation and collection of personal data make it something people should really be aware of. The problem is exacerbated by IoT devices having APIs intended for communication with other, unauthenticated devices on the network.

Blog on Lifehacker.com on how to prevent DNS rebinding attacks by adjusting your router : [https://lifehacker.com/prevent-dns-rebinding-attacks-by-adjusting-your-router-1827022291](https://lifehacker.com/prevent-dns-rebinding-attacks-by-adjusting-your-router-1827022291)

\_\_




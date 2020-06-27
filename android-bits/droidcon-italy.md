# DROIDCON Italy

The conference about our little green robots was held in Torino Italy in April 2016.

With 800 attendees and four different tracks, one was spoilt for choices. There were 57 speakers, including 3 Developer Advocate at Google and 13 Google Developer Experts from London, Dutch, Denver, Germany, Paris, and working at Intel, Facebook, Trello, Amazon, Spotify, JetBrains, Houston, Bologna etc sharing their experiences.

Bhimsen and I presented a topic on [**Securely developing Android applications**](https://speakerdeck.com/nazneen/who-do-you-think-owns-your-android-application-1). It was my first talk at an international platform. We had a great audience; people were engaged and were really excited to chat afterwards. Yay!

Below is a summary of some sessions:

## _Facebook Infer - Martino Luco \(Facebook\)_

Main Takeaway: What is the **difference between Lint and Infer**?

Infer finds deeper infer-procedural bugs sometimes spanning multiple files. Linters, in contrast, typically implement simple syntactic checks that are local within one procedure. But they are valuable and Infer doesn't try to duplicate what they are good at. At Facebook, they run both Infer and a collection of Android linters to analyze the main Facebook apps for Android and iOS, Facebook Messenger and Instagram, among others. Infer reports bugs which are responsible for app crashes and performance issues, such as accessing null pointers, and leaking resources such as Context instances.

## _Fresco:  Massimo Carli \(Facebook\)_

Another library that takes care of image loading and display so you don’t have to. Fresco’s image pipeline will load images from the network, local storage, or local resources. To save data and CPU, it has three levels of cache; two in memory and another in internal storage. Fresco’s Drawees show a placeholder for you until the image has loaded and automatically show to the image when it arrives. When the image goes off-screen, it automatically releases its memory.

{% hint style="warning" %}
**Now the puzzle in my mind is the comparison between Picasso, Glide and Fresco.**
{% endhint %}

They **claim** that they following are the **advantages**:  
Images aren't stored in the Java heap, but in the ashmem heap. Intermediate byte buffers are also stored in the native heap. This leaves a lot more memory available for applications to use. It reduces the risk of OutOfMemoryErrors. It also reduces the amount of garbage collection apps have to do, leading to better performance.

Progressive JPEG images can be streamed, just like in a web browser.  
Images can be cropped around any point, not just the centre.  
JPEG images can be resized natively.

**Anyone who has used Fresco, it would be great to hear about your experiences. I have used Picasso and it worked like a charm.**

## _To ∞ \(~65K\) and beyond!- Sebastiano Gottardo\(Google Developer Expert - Android Engineer at musixmatch\)_

I have seen a lot of pain of dexing in my current project and wanted to understand a bit more in depth about multi-dexing. This talk explored multiple ways to deal with the situation, and several awesome tools to help you understand and manage your application method count.

[https://speakerdeck.com/dextor/to-65k-and-beyond](https://speakerdeck.com/dextor/to-65k-and-beyond)

## _Engage and retain users in the android world - Matteo Bonifazi \(Google Developer Expert for Android\)_

In this talk, a few examples from Android M were presented like App Invite, App Indexing which can be better utilised with Now on Tap:

[http://www.slideshare.net/MatteoBonifazi/engage-and-retain-users-in-the-android-world-droidcon-italy-2016](http://www.slideshare.net/MatteoBonifazi/engage-and-retain-users-in-the-android-world-droidcon-italy-2016?from_m_app=android)

## _Reverse engineering is not just for hackers! Jon Reeve-\(Wasabi Code Ltd\)_

He talked about tools like apktool, androguard, ClassyShark \(this is second time I heard about this in droidcon, time to read about it\), CodeInspect, IDA Pro, JEB/JEB2, radare2 and some more\(mentioned in the slides\)

He talked about pinpointing bugs that come from closed-source libraries such as those for ad and tracking networks, and working around those bugs, getting them fixed faster, or even patching them if need be!

[https://speakerdeck.com/jonreeve/reverse-engineering-is-not-just-for-hackers](https://speakerdeck.com/jonreeve/reverse-engineering-is-not-just-for-hackers)

## _A realtime infrastructure - Firebase - Alessandro Martellucci\(Open reply\)_

**Firebase - even got mentioned in Google IO 2016.**

[http://www.slideshare.net/AlessandroMartellucc/a-realtime-infrastructure-for-android-apps-firebase-may-be-what-you-needand-even-more](http://www.slideshare.net/AlessandroMartellucc/a-realtime-infrastructure-for-android-apps-firebase-may-be-what-you-needand-even-more)

## _\#PERFMATTERS for Android- Hasan Hosgel\(Google Developer Expert for Android – Co-Lead GDG Berlin Android\)_

He talked about some basics, which we tend to ignore \(mentioned in the slides\)  
Optimising ArrayList  
Optimising Bundle  
Optimising StringBuilder  
Optimising Loops  
Optimising Method Invocations  
Optimising Access Methods

[https://speakerdeck.com/alosdev/perfmatters-for-android-droidcon-turin-2016](https://speakerdeck.com/alosdev/perfmatters-for-android-droidcon-turin-2016)

## _Internal Library Dependency Management-_ Kelly Shuster\(Google Developer Expert – Android Developer @ Thoughtbot\)

She talked about setup and maintenance of internal library dependencies using Git submodules and creating a private maven repository in Artifactory.

[http://www.slideshare.net/KellyShuster/internal-android-library-management-droidcon-sf-2016](http://www.slideshare.net/KellyShuster/internal-android-library-management-droidcon-sf-2016)

I saw a lot of well-known speakers and developers. It was a conference rich of interesting talks in addition to those summarised in this post, ranging between various topics such as TDD, UX, Security and many more.

There are some talks I could not attend but found them interesting. I would urge you all to go through them:  
[Confession of a Digital Finger Painter](https://www.youtube.com/watch?v=21ZB-ULVJSg)  
[Loving Lean Layouts](https://www.youtube.com/watch?v=9bfNUdgoSCU)  
[Let’s Sprinkle some \#PerfMatters on your ViewGroups](https://www.youtube.com/watch?v=fEtyhj74JhU)

You can find the videos of some talks [here](https://www.youtube.com/channel/UC9f8652addezs8ZUuKPB4Ow/videos?sort=dd&flow=grid&view=0).

> Thank you so much droidcon Italy for having me as a speaker!  
> I highly recommend signing up as a speaker\(next is on 7th-8th April 2017\) and sharing your experience.


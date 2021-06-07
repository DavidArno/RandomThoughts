---
layout: post
title: MS Teams On Air Sign - Using the registry to monitor webcam and microphone use
excerpt: ...I wanted a way of notifying [the family]] when I was on a call. Yelling out "On a call!" across the house wasn't popular, so I decided I needed a clear "on a call" sign instead. And I wanted it to automatically turn on and off when the call started and ended. The registry came to the rescue...
---
![On Air sign](/images/on-air.jpg)

I've worked at home 1-2 days a week for many years now, but with the COVID pandemic, I - like so many others - found myself not only working at home full time, but I also was then taking part in many video calls and the rest of the family were at home too. Whilst I don't mind members of my family appearing on my video calls, they were less keen. So I wanted a way of notifying them when I was on a call. Yelling out "On a call!" across the house wasn't popular, so I decided I needed a clear "on a call" sign instead. And I wanted it to automatically turn on and off when the call started and ended.

This article focuses on how the registry can be used to solve the first step of this challenge: telling when MS Teams is accessing the camera and microphone.

As of [Windows 10 1903, Microsoft added additional controls around apps accessing various resources](https://support.microsoft.com/en-us/windows/windows-10-camera-microphone-and-privacy-a83257bc-e990-d54a-d212-b5e41beba857), such as location and - of interest here - webcams and microphones. This appears in Windows as App Permissions within Settings:

![App Permissions in Settings](/images/app-permissions.png)

In addition to being able to toggle whether apps can access a resource, these settings also show which apps have accessed it and when:

![App Permissions in Settings showing Camera access times](/images/camera-access.png)

And it turns out that all of this information is stored in a simple form in the registry under `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\ConsentStore`. For each of the resources that you can grant/deny apps permssion to, there's an entry in the registry with a value of "Allow" or "Deny":

![Consent store info in the Regstry](/images/registry-consentstore.png)

In addition, for each entry, we have a list of applications (those not installed as part of Windows appear below `NonPackaged`) and two keys for each: `LastUsedTimeStart` and `LastUsedTimeStop`, which contain a date/time value (the exact format of which isn't of interest in this instance):

![Camera access times in the registry](/images/camera-access-times.png)

When for example, the camera is accessed, that `LastUsedTimeStop` value is reset to zero. When the camera stops being used, the time it stopped is recorded in that value and so it becomes non-zero. And that is all that's needed to tell whether the camera is in use by a particular application or not. In my case, I'm interested in MS Teams, so I simply monitor the `LastUsedTimeStop` of the `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\`
`ConsentStore\webcam\NonPackaged\C:#Users#david#AppData#Local#Microsoft#Teams#current#Teams.exe`
and the `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\CapabilityAccessManager\`
`ConsentStore\microphone\NonPackaged\C:#Users#david#AppData#Local#Microsoft#Teams#current#Teams.exe`
keys to tell me whether the camera and microphone are in use.

So now all I had to do was write an app that monitored those registry keys and talk to a Raspberry Pi, that in turn could turn the sign on and off and I had my completed "On Air" sign for my Teams calls. How I did that will be the subject of a future blog post.


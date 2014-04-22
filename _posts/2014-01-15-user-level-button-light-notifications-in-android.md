---
layout: post
title: "User level Button Light Notifications in Android"
---

On my phone, the HTC One X, the manufacturer chose to forgo the standard Android device notification LED.
This, combined with a screen that's slow to turn on, makes checking for notifications unnecesarily tedious.

My phone does have hardware buttons, though, which can be controlled using a root shell command.

[Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm) is a versatile Android
automation tool. I use it for, among other things, automatically silencing my phone during lectures.

In this case, a simple profile suffices:

{% highlight bash %}
#On incoming notification:
#Run root shell:
	echo 10 > /sys/devices/platform/msm_ssbi.0/pm8921-core/pm8xxx-led/leds/button-backlight/currents
{% endhighlight %}
(note that you'll neeed to enable Tasker Accessibility service in settings in order to trigger on notifications)

This command sets the brightness of the button backlight (values greater or less than 10 would be brighter
or fainter). Upon change of brightness, the buttons will stay lit until they are explicitly turned off --
which the system takes care of the next time the screen comes on.

While the exact file path may change between devices, I'd hazard it's mostly the same for hardware-buttoned 
devices.



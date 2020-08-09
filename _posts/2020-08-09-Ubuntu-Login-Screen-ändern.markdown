---
layout: post
title:  "Ubuntu Loginscreen ändern"
date:   2020-08-09
categories: jekyll update
---

{% highlight bash %}
sudo update-alternatives --config gdm3-theme.gresource
{% endhighlight %}

![image]({{ site.baseurl }}/assets/auswahl_gnome_login.png)

Hier sollte dann die Auswahl 1. gewählt werden. Dadurch erhält man einen grauen Login Screen
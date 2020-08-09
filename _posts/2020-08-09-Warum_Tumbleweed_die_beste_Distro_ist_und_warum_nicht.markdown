---
layout: post
title:  "Warum Tumbleweed die beste Distro ist, und warum nicht"
date:   2020-08-06
categories: jekyll update
---

## Es gibt zwei Gründe warum Tumbleweed die beste Distro ist und keiner davon heißt Yast

Opensuse hat jahrelang soviele Sachen falsch gemacht und eisern an dem Kurs festgehalt, dass sie heutzutage zu Recht behaupten können, eine der besten
Distributionen da draußen zu sein.

Zwei Gründe bringen sie ganz weit nach vorne und keiner davon ist Yast. Dabei ist Yast so eine tolle Idee: Ein Interface zur kompletten grafischen Verwaltung
des Systems. Jetzt werden bestimmt einige sagen: UI braucht kein Mensch solange er einen Terminal hat. Das sind aber leider auch die Leute, die i3 installiert haben 
und sich freuen, dass ihr System für 20 Tage am Stück gehalten hat, bevor es im i3 Himmel landet ...

UI ist wichtig, da es uns einen "natürlichen" Umgang mit Computern ermöglicht und die Lernkurve für neue User erträglich macht. Zudem können wir dadurch komplexere
Anwendungen nutzen und bauen.

Opensuse bietet hier mit Yast das perfekte Werkzeug für neue User, die sich an Linux ausprobieren wollen und erste tiefergehende Erfahrungen sammeln. Einfach perfekt! 
Zwar nicht immer: Mal als neuer User probiert den Hostnamen über Yast zu ändern? Aber beinahe.

Ok und was sind jetzt diese zwei Punkte, die Tumbleweed aus meiner Sicht zur besten Distribution machen?

## 1. Bleeding Edge

Gut kann arch auch, aber die Opensuse Pakete werden noch revisioniert bevor sie auf die Allgemeinheit losgelassen werden, Dank des Opensuse Build Service (OBS) gegen den Arch's AUR einfach nur abstinkt. Mit OBS werden übrigens auch Pakete für Fedora und Debian gepackt. Aber was macht ein "Rolling Release und Bleeding Edge" soviel besser als ein Ubuntu LTS oder eine XX.10 Version. Ganz einfach es nutzt den aktuellen Stand einer Software und nicht Pakete, welche teilweise ein halbes Jahr alt sind...

## 2. BTRFS Snapshots ins System integriert

Ja kann man auch unter Ubuntu haben, wenn man es auswählt und konfiguriert und Software zur Verwaltung installiert und sich damit auskennt. Bei Tumbleweed ist das von Haus aus vorinstalliert und man kann es ohne Vorkentnisse nutzen: Am System funktioniert was nicht? Yast auf > Snapshot von vor einer Stunde wählen > System zurücksetzen. Mit 4 Klicks.

Dieso Punkte machen Tumbleweed für mich einfach zur besten Distro. Das Problem ist nur ... Custom Software ... Ich möchte eben mal schnell Software XY über die ich gerade soviel gelesen habe installieren .... Nicht in den Quellen? Kein Problem > OBS. Nicht in OBS. Dann kann ichs eigentlich vergessen. Natürlich kann man selbst kompilieren insofern man die Abhängigkeiten hinbekommt, die Chancen dafür stehen aber, auch für erfahrene User eher Schlecht. Es hakt immer was. 

Hat man aber einen Workflow der auf extraordinäre Software verzichtet: Beste Distro.
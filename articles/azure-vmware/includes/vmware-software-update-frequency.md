---
title: Häufigkeit von VMware-Softwareupdates
description: Unterstützte Häufigkeit von VMware-Softwareupdates für Azure VMware Solution.
ms.topic: include
ms.date: 08/24/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 297fc81559adc9bb3f9c22091ca568da36cffb8f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638713"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Einer der Vorteile von privaten Azure VMware Solution-Clouds besteht darin, dass die Plattform für Sie verwaltet wird.  Microsoft ist für die Lebenszyklusverwaltung der VMware-Software (ESXi, vCenter und vSAN) zuständig. Außerdem ist Microsoft für die Lebenszyklusverwaltung von NSX-T-Appliances und das Bootstrapping der Netzwerkkonfiguration zuständig, z. B. die Erstellung des Gateways für Schicht 0 und die Aktivierung des Nord-Süd-Routings. Sie sind für die NSX-T-SDN-Konfiguration verantwortlich, also Netzwerksegmente, Regeln für verteilte Firewalls, Gateways für Schicht 1 und Lastenausgleich. 

Microsoft ist für das Anwenden von Patches, Updates oder Upgrades für ESXi, vCenter, vSAN und NSX-T in Ihrer privaten Cloud verantwortlich. Die Auswirkungen von Patches, Updates und Upgrades auf ESXi, vCenter und NSX-T sind unterschiedlich. 

- **ESXi:** Auf Workloads, die in Ihrer privaten Cloud ausgeführt werden, hat dies keine Auswirkungen. Der Zugriff auf vCenter und NSX-T wird während dieses Zeitraums nicht blockiert.  Es wird empfohlen, während dieses Zeitraums keine weiteren Aktivitäten wie das Hochskalieren der privaten Cloud usw. in Ihrer privaten Cloud zu planen.

- **vCenter:** Auf Workloads, die in Ihrer privaten Cloud ausgeführt werden, hat dies keine Auswirkungen. Während dieses Zeitraums ist vCenter nicht verfügbar, und Sie können keine VMs verwalten (anhalten, starten, erstellen oder löschen). Es wird empfohlen, während dieser Zeit keine weiteren Aktivitäten wie das Hochskalieren der privaten Cloud, das Erstellen neuer Netzwerke usw. in Ihrer privaten Cloud zu planen.

- **NSX-T:** Es gibt Auswirkungen auf die Workload. Wenn ein bestimmter Host aktualisiert wird, geht die Konnektivität für die virtuellen Computer auf diesem Host zwei Sekunden bis maximal eine Minute verloren. Dabei können folgende Symptome auftreten:

   - Pingfehler

   - Paketverlust

   - Fehlermeldungen (etwa *Zielhost nicht erreichbar* und *Net unreachable* (Netz nicht erreichbar))

   Während dieses Upgradefensters ist der gesamte Zugriff auf die NSX-T-Verwaltungsebene blockiert. Sie können während dieser Zeit keine Konfigurationsänderungen an der NSX-T-Umgebung vornehmen.  Ihre Workloads werden jedoch weiterhin normal ausgeführt. Es gelten nur die oben beschriebenen Auswirkungen des Upgrades.
 
   Es wird empfohlen, während dieses Upgradezeitraums keine weiteren Aktivitäten wie das Hochskalieren der privaten Cloud usw. in Ihrer privaten Cloud zu planen. Diese können den Start des Upgrades verhindern oder negative Auswirkungen auf das Upgrade und die Umgebung haben.
 
Sie werden benachrichtigt, bevor Patches und Updates bzw. Upgrades auf Ihre privaten Clouds angewendet werden. Wir arbeiten auch mit Ihnen zusammen, um ein Wartungsfenster zu planen, bevor wir Updates oder Upgrades auf Ihre private Cloud anwenden.


Softwareupdates umfassen:

- **Patches**: Sicherheitspatches oder Fehlerbehebungen, die von VMware freigegeben werden

- **Updates**: Änderungen der Nebenversion einer VMware-Stack-Komponente

- **Upgrades**: Änderungen der Hauptversion einer VMware-Stack-Komponente

>[!NOTE]
>Microsoft testet einen wichtigen Sicherheitspatch, sobald er von VMware zur Verfügung gestellt wird.

Bis die Bereitstellung der nächsten geplanten Updates erfolgt, werden dokumentierte VMware-Problemumgehungen implementiert, statt einen entsprechenden Patch zu installieren.

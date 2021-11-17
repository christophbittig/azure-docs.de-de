---
title: Azure VMware Solution by CloudSimple-Update – September 2020
description: In diesem Artikel erfahren Sie, was während dieses Wartungsvorgangs passiert und welche Änderungen an Ihrer privaten Cloud vorgenommen werden.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: eea32da3da1748fb1049fb32e6af8dd2467b4f60
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339217"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware Solution by CloudSimple-Update – September 2020

Im September wird ein wichtiges Update am Azure VMware Solution-Dienst durchgeführt. In einer E-Mail-Benachrichtigung, die im Rahmen der Wartung gesendet wird, wird der Zeitplan der Wartung angegeben. In diesem Artikel erfahren Sie, was während dieses Wartungsvorgangs passiert und welche Änderungen an Ihrer privaten Cloud vorgenommen werden.

> [!NOTE]
> Dies ist ein unterbrechungsfreies Upgrade. Während des Upgrades wird möglicherweise eine der redundanten Komponenten angehalten bzw. heruntergefahren.

## <a name="vmware-infrastructure-upgrade"></a>Upgrade der VMware-Infrastruktur

Die VMware-Infrastruktur Ihrer privaten Cloud wird auf eine neuere Version aktualisiert. Dies umfasst Updates der vCenter-, ESXi-, NSX-T- und HCX-Komponenten (Hybrid Cloud Extension) Ihrer privaten Cloud (sofern diese Komponenten bereitgestellt wurden).

Bevor ein Knoten zum Durchführen des Upgrades in den Wartungsmodus versetzt wird, wird ein neuer Knoten zu Ihrer privaten Cloud hinzugefügt. Dadurch wird sichergestellt, dass die Kapazität und Verfügbarkeit Ihrer privaten Cloud während des Upgradevorgangs aufrechterhalten wird. Beim Upgrade von VMware-Komponenten werden auf der Webbenutzeroberfläche von vCenter möglicherweise Warnungen angezeigt. Diese Warnungen sind Teil der Wartungsvorgänge, die vom zuständigen Team für Dienstvorgänge durchgeführt werden.

**Komponentenversion**

- ESXi 6.7U3
- vCenter 6.7U3
- vSAN 6.7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Rechenzentrumsupdates

Dieses Update umfasst Updates an der Rechenzentrumsinfrastruktur. Während des Wartungszeitraums werden unterbrechungsfreie Updates ausgeführt. Die Redundanz ist beim Upgrade verringert. Für die VMware-Infrastruktur Ihrer privaten Cloud, Ihre ExpressRoute- oder GlobalReach-Verbindungen sowie für Ihre Site-to-Site-VPN-Geräte, die für die Verfügbarkeit der Verbindungen eingesetzt werden, werden möglicherweise Warnungen generiert. Dies ist ein normales Verhalten während des Updates, da die Komponenten als Teil des Updates neu gestartet werden.

-   Wenn ein Site-to-Site-VPN als Einzelinstanz (keine Hochverfügbarkeit) bereitgestellt wurde, muss die VPN-Verbindung möglicherweise neu hergestellt werden.

-   Wenn Sie eine Point-to-Site-VPN-Verbindung verwenden, muss die VPN-Verbindung neu hergestellt werden.

## <a name="post-update"></a>Nach dem Update

Nachdem die Updates abgeschlossen wurden, sollten neuere Versionen der VMware-Komponenten angezeigt werden. Wenn Probleme auftreten oder Sie Fragen haben, erstellen Sie ein [Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), um unser Supportteam zu kontaktieren.

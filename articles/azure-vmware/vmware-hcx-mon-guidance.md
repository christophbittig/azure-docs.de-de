---
title: Leitfaden zu VMware HCX Mobility Optimized Networking (MON)
description: Erfahren Sie mehr über Azure VMware Solution-spezifische Anwendungsfälle für Mobility Optimized Networking (MON).
ms.topic: reference
ms.date: 10/04/2021
ms.openlocfilehash: 34840fedbf2ae2dbf32711f4dfee307fe3a3a5ca
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373325"
---
# <a name="vmware-hcx-mobility-optimized-networking-mon-guidance"></a>Leitfaden zu VMware HCX Mobility Optimized Networking (MON)

>[!NOTE]
>
>HCX Mobility Optimized Networking wird offiziell von VMware und Azure VMware Solutions ab HCX Version 4.1.0 unterstützt. 

>[!IMPORTANT] 
>
>Bevor Sie HCX MON aktivieren, lesen Sie die folgenden Informationen über Einschränkungen und nicht unterstützte Konfigurationen:
>
>[Für HCX NE nicht unterstützte Quellkonfigurationen](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-DBDB4D1B-60B6-4D16-936B-4AC632606909.html)
> 
>[Einschränkungen für alle HCX-Bereitstellungen einschließlich MON](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-BEC26054-D560-46D0-98B4-7FF09501F801.html)


[HCX Mobility Optimized Networking (MON)](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) ist ein optionales Feature, das bei Verwendung von [HCX Network Extensions (NE)](configure-hcx-network-extension.md) aktiviert werden kann. MON bietet in bestimmten Szenarien ein optimales Routing des Datenverkehrs, um den Posauneneffekt für das Netzwerk zwischen lokalen und cloudbasierten Ressourcen in erweiterten Netzwerken zu verhindern. 

Während des gesamten Migrationszyklus optimiert MON die Anwendungsmobilität für Folgendes:

- Optimieren der Kommunikation zwischen virtuellen Computern (VM) und VM L2 bei Verwendung von Stretchingnetzwerken 

- Optimieren und Vermeiden asymmetrischer Datenverkehrsflüsse zwischen lokalen Standorten, Azure VMware Solution und Azure


In diesem Artikel erfahren Sie mehr über die für Azure VMware Solution spezifischen Anwendungsfälle für MON.


## <a name="optimize-traffic-flows-across-standard-and-stretched-segments-on-the-private-cloud-side"></a>Optimieren von Datenverkehrsflüssen über Standard- und Stretchingsegmente auf der Seite der privaten Cloud 

In diesem Szenario wird VM1 mithilfe der NE in die Cloud migriert, was eine optimale VM-zu-VM-Latenz bietet. Daher benötigt VM1 eine geringe Latenz zu VM3 im lokalen Azure VMware Solution-Segment. Wir migrieren das VM1-Gateway vom lokalen Standort zu Azure VMware Solution (Cloud), um einen optimalen Pfad für den Datenverkehr (blaue Linie) sicherzustellen. Wenn das Gateway am lokalen Standort verbleibt (rote Linie), werden ein Posauneneffekt und eine höhere Latenz beobachtet. 

>[!NOTE]
>Wenn Sie MON aktivieren, ohne das VM-Gateway zur Cloud zu migrieren, wird kein optimaler Pfad für den Datenverkehrsfluss sichergestellt.  Außerdem ist die Auswertung von Richtlinienrouten nicht zulässig.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-1.png" alt-text="Diagramm: Optimierung der Kommunikation zwischen VMs und VMs L2 bei Verwendung von Stretchingnetzwerken" border="false":::



## <a name="optimize-and-avoid-asymmetric-traffic-flows"></a>Optimieren und Vermeiden asymmetrischer Datenverkehrsflüsse 

In diesem Szenario gehen wir davon aus, dass eine VM eines lokalen Standorts zu Azure VMware Solution migriert wurde und an L2 teilnimmt, und dass der L3-Datenverkehr zurück zum lokalen Standort fließt, um auf Dienste zuzugreifen. Wir gehen auch davon aus, dass ein Teil der VM-Kommunikation von Azure (im mit Azure VMware Solution verbundenen vNET) bis in die private Azure VMware Solution-Cloud reichen könnte.

>[!IMPORTANT]
>Hier geht es vor allem darum, asymmetrische Verkehrsdatenflüsse sorgfältig zu planen und zu vermeiden. 

Standardmäßig und ohne Verwendung von MON kann eine VM in Azure VMware Solution in einem Stretchingnetzwerk ohne MON über den bevorzugten ExpressRoute-Pfad wieder mit dem lokalen Standort kommunizieren. Idealerweise und basierend auf dem Anwendungsfall des Kunden sollte ausgewertet werden, wie eine VM auf einem Azure VMware Solution-Stretchingsegment, das mit MON aktiviert ist, entweder über das NE- oder das T0-Gateway über die ExpressRoute wieder zum lokalen Standort geleitet werden sollte, wobei die Datenverkehrsflüsse symmetrisch bleiben sollten.

Wenn Sie z. B. den NE-Pfad auswählen, müssen die MON-Richtlinienrouten speziell das Subnetz auf der lokalen Seite adressieren. Ansonsten wird die 0.0/0-Route verwendet. Richtlinienrouten finden Sie unter dem NE-Segment, wenn Sie „Erweitert“ auswählen. Standardmäßig sind alle RFC1918-IP-Adressen in der MON-Richtlinienroutendefinition enthalten. 

:::image type="content" source="media/tutorial-vmware-hcx/default-hcx-mon-policy-based-routes.png" alt-text="Screenshot: Richtlinienbasierte Standardrouten":::

Richtlinienrouten werden nur ausgewertet, wenn das VM-Gateway in die Cloud migriert wird. Diese Konfiguration bewirkt, dass alle übereinstimmenden Subnetze für das Ziel über die NE-Appliance getunnelt werden.  Wenn keine Übereinstimmung besteht, werden sie über das T0-Gateway weitergeleitet.

>[!NOTE]
>Ein besonderer Aspekt bei der Verwendung von MON in Azure VMware Solution besteht darin, den Peers die /32-Routen, die über BGP angekündigt werden, zu gewähren. Dies schließt lokale und Azure-Verbindungen über die ExpressRoute-Verbindung ein. Beispielsweise lernt eine VM in Azure den Pfad zu einer Azure VMware Solution-VM auf einem für MON-aktiviertem Azure VMware Solution-Segment. Sobald der zurückgegebene Datenverkehr wie erwartet zurück an T0 gesendet wurde, wird der Datenverkehr über das NE anstelle von T0 erzwungen, wenn das Rückgabesubnetz eine RFC1918-Übereinstimmung darstellt.  Dann erfolgt die Ausgabe des Datenverkehrs über ExpressRoute zurück zu Azure auf der lokalen Seite.  Dies kann zu Verwirrung bei zustandsbehafteten Firewalls in der Mitte und zu asymmetrischem Routingverhalten führen. Es ist auch eine gute Idee zu bestimmen, wie VMs in NE MON-Segmenten auf das Internet zugreifen müssen, entweder über T0 in Azure VMware Solution oder nur über die NE zurück zum lokalen Standort.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-3.png" alt-text="Diagramm: RFC1918-Ausgang und ausgehender Datenverkehrsfluss" border="false":::

Wie im obigen Diagramm dargestellt, kommt es darauf an, jedem erforderlichen Subnetz eine Richtlinienroute zuzuordnen. Andernfalls wird der Datenverkehr über T0 und nicht über die NE weitergeleitet.

 
Weitere Informationen zu Richtlinienrouten finden Sie unter [Mobility Optimized Networking-Richtlinienrouten](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-F45B1DB5-C640-4A75-AEC5-45C58B1C9D63.html).


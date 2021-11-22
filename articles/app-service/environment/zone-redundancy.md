---
title: Unterstützung für Verfügbarkeitszonen in App Service-Umgebung v2
description: Erfahren Sie, wie Sie Ihre App Service-Umgebungen so bereitstellen, dass Ihre Apps zonenredundant sind.
author: madsd
ms.topic: article
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 5c60b2496acb03f52da066e56feab1423ac6ae91
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520938"
---
# <a name="availability-zone-support-for-app-service-environment-v2"></a>Unterstützung für Verfügbarkeitszonen in App Service-Umgebung v2

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v2 beschrieben, die mit isolierten App Service-Plänen verwendet wird
> 

App Service-Umgebung v2 kann in Verfügbarkeitszonen (VZ) bereitgestellt werden.  Kunden können eine ILB-ASE (Internal Load Balancer, interner Lastenausgleich) für eine bestimmte VZ in einer Azure-Region bereitstellen. Wenn Sie Ihre ILB-ASE an eine bestimmte VZ anheften, werden die von der ILB-ASE verwendeten Ressourcen entweder an die angegebene VZ angeheftet oder auf zonenredundante Weise bereitgestellt.  

Eine ILB-ASE, die explizit in einer VZ bereitgestellt wird, wird als zonale Ressource betrachtet, da die ILB-ASE an eine bestimmte Zone angeheftet ist. Die folgenden ILB-ASE-Abhängigkeiten werden an die angegebene Zone angeheftet:

- Die IP-Adresse des internen Lastenausgleichs der ASE
- Computeressourcen, die von der ASE zum Verwalten und Ausführen von Webanwendungen genutzt werden

Der Remotedateispeicher für Webanwendungen, die in einer zonalen ILB-ASE bereitgestellt werden, verwendet zonenredundanten Speicher (ZRS).

Wenn Sie die in diesem Artikel beschriebenen Schritte nicht befolgen, werden ILB-ASEs nicht automatisch auf zonale Weise bereitgestellt. Eine externe ASE mit einer öffentlichen IP-Adresse kann nicht an eine bestimmte Verfügbarkeitszone angeheftet werden. 

Zonale ILB-ASEs können in den folgenden Regionen erstellt werden:

- Australien (Osten)
- Kanada, Mitte
- USA (Mitte)
- East US
- USA (Ost) 2
- USA, Osten 2 (EUAP)
- Frankreich, Mitte 
- Japan, Osten
- Nordeuropa
- Europa, Westen
- Asien, Südosten
- UK, Süden
- USA, Westen 2

In einer zonalen ILB-ASE bereitgestellte Anwendungen werden weiterhin in dieser ASE ausgeführt und verarbeiten Datenverkehr in ihr, selbst wenn andere Zonen in derselben Region ausfallen.  Möglicherweise sind Vorgänge außerhalb der Laufzeit wie das Skalieren von Anwendungsdienstplänen oder das Erstellen, Konfigurieren und Veröffentlichen von Anwendungen weiterhin von einem Ausfall in anderen Verfügbarkeitszonen betroffen. Die Bereitstellung einer zonalen ILB-ASE mit Anheften an eine Zone gewährleistet nur eine fortlaufende Betriebszeit für bereits bereitgestellte Anwendungen.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Bereitstellen einer App Service-Umgebung in einer Verfügbarkeitszone

Zonale ILB-ASEs müssen mithilfe von ARM-Vorlagen erstellt werden. Sobald eine zonale ILB-ASE über eine ARM-Vorlage erstellt wurde, kann sie über das Azure-Portal und die Befehlszeilenschnittstelle angezeigt werden und ist für Interaktionen verfügbar.  Eine ARM-Vorlage wird nur für die anfängliche Erstellung einer zonalen ILB-ASE benötigt.

Die einzige erforderliche Änderung in einer ARM-Vorlage zum Angeben einer zonalen ILB-ASE ist die neue **zones** _-Eigenschaft. Die _ *_zones_**-Eigenschaft sollte abhängig von der logischen Verfügbarkeitszone, an die die ILB-ASE angeheftet werden soll, auf einen der Werte „1“, „2“ oder „3“ festgelegt werden.

Der nachstehende Beispielcodeausschnitt für die ARM-Vorlage zeigt die neue ***zones***-Eigenschaft, die angibt, dass die ILB-ASE an Zone 2 angeheftet werden soll.

```json
"resources": [
    {
        "type": "Microsoft.Web/hostingEnvironments",
        "kind": "ASEV2",
        "name": "yourASENameHere",
        "apiVersion": "2015-08-01",
        "location": "your location here",
        "zones": [
            "2"
        ],
        "properties": {
            "name": "yourASENameHere",
            "location": "your location here",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "3",
            "dnsSuffix": "contoso-internal.com",
            "virtualNetwork": {
                "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
                "Subnet": "yourSubnetNameHere"
            }
        }
    }
]
```

Damit Ihre Apps zonenredundant sind, müssen Sie zwei zonale ILB-ASEs bereitstellen. Die beiden zonalen ILB-ASEs müssen sich in getrennten Verfügbarkeitszonen befinden. Anschließend müssen Sie Ihre Apps in beiden ILB-ASEs bereitstellen. Nach dem Erstellen Ihrer Apps müssen Sie eine Lösung für den Lastenausgleich konfigurieren. Die empfohlene Lösung ist das Bereitstellen einer [zonenredundanten Application Gateway-Instanz](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) hinter den zonalen ILB-ASEs. 

## <a name="in-region-data-residency"></a>Data Residency in der Region

In einer Verfügbarkeitszone bereitgestellte ILB-ASEs speichern nur Kundendaten in der Region, in der die zonale ILB-ASE bereitgestellt wurde. Sowohl der Inhalt von Websitedateien als auch die vom Kunden bereitgestellten Einstellungen und Geheimnisse, die in App Service gespeichert sind, verbleiben innerhalb der Region, in der die zonale ILB-ASE bereitgestellt wurde.

Kunden können für Data Residency in einer einzelnen Region sorgen, indem sie die weiter oben im Abschnitt „Bereitstellen einer App Service-Umgebung in einer Verfügbarkeitszone“ beschriebenen Schritte befolgen. Durch Konfigurieren anhand dieser Schritte erfüllt eine in einer Verfügbarkeitszone bereitgestellte App Service-Umgebung die Anforderungen an die Region für Data Residency – auch die in [Azure Trust Center](https://azuredatacentermap.azurewebsites.net/) angegebenen.

Kunden können sich mit den folgenden Schritten vergewissern, dass eine App Service-Umgebung ordnungsgemäß zum Speichern von Daten in einer einzelnen Region konfiguriert ist: 

1. Navigieren Sie im [Ressourcen-Explorer](https://resources.azure.com) zu der ARM-Ressource für die App Service-Umgebung.  ASEs werden unter *providers/Microsoft.Web/hostingEnvironments* aufgelistet.
2. Wenn eine *zones*-Eigenschaft in der Ansicht der ARM-JSON-Syntax vorhanden ist und ein JSON-Array mit einem einzelnen Wert „1“, „2“ oder „3“ enthält, wird die ASE zonal bereitgestellt, und die Kundendaten verbleiben in derselben Region.
2. Wenn keine *zones*-Eigenschaft vorhanden ist oder die Eigenschaft keinen gültigen Zonenwert wie zuvor angegeben aufweist, wird die ASE nicht zonal bereitgestellt, und die Kundendaten werden nicht exklusiv in derselben Region gespeichert.

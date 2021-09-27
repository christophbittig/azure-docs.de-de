---
title: Azure API Management-Rechenplattform
description: Erfahren Sie mehr über die Datenverarbeitungsplattform, die zum Hosten Ihrer API Management-Dienstinstanz verwendet wird
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 15bc0aa0c4a657bbb6f363a9c2786c1d4f9116ee
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544407"
---
# <a name="compute-platform-for-azure-api-management"></a>Datenverarbeitungsplattform für Azure API Management

Als Cloud-Plattform-as-a-Service (PaaS) abstrahiert Azure API Management viele Details der Infrastruktur, die zum Hosten und Ausführen Ihres Dienstes verwendet wird. Die meisten Teile Ihrer API Management-Instanz können erstellt, verwaltet und skaliert werden, ohne etwas über die zugrunde liegenden Ressourcen zu wissen.

Um die Fähigkeiten des Dienstes zu verbessern, aktualisieren wir die Version der API Management Compute Platform - die Azure Compute-Ressourcen, die den Dienst hosten - für Instanzen in mehreren [Dienstleistungsebenen](api-management-features.md). Dieser Artikel gibt Ihnen einen Überblick über das Upgrade und die Hauptversionen der API Management Compute Platform: `stv1` und `stv2`.

Wir haben die Auswirkungen dieses Upgrades auf den Betrieb Ihrer API Management-Instanz minimiert. Wenn Ihre Instanz jedoch mit einem virtuellen Netzwerk von [Azure](virtual-network-concepts.md) verbunden ist, müssen Sie einige Netzwerkkonfigurationseinstellungen ändern, wenn die Instanz auf die Plattformversion `stv2` aktualisiert wird.

## <a name="compute-platform-versions"></a>Versionen der Compute-Plattform

| Version | BESCHREIBUNG | Aufbau | API Management für Drittanbieter |
| -------| ----------| ----------- | ------- |
| `stv2` | Einzelmieter v2 | [Skalierungsgruppen für virtuelle Computer](../virtual-machine-scale-sets/overview.md) | Developer, Basic, Standard und Premium |
| `stv1` |  Einzelmieter v1 | [Clouddienst (klassisch)](../cloud-services/cloud-services-choose-me.md) | Developer, Basic, Standard und Premium |
| `mtv1` | Mehrmieter v1 |  [App Service](../app-service/overview.md) | Nutzung |


## <a name="how-do-i-know-which-platform-hosts-my-api-management-instance"></a>Gewusst wie: Welche Plattform hostet meine API Management-Instanz?

### <a name="developer-basic-standard-and-premium-tiers"></a>Developer-, Basic-, Standard- und Premium-Stufen

* Instanzen mit virtuellen Netzwerkverbindungen, die nach **April 2021** über das Azure-Portal erstellt oder aktualisiert wurden oder die API Management REST API Version **2021-01-01-preview** oder später verwenden, werden auf der `stv2` Plattform gehostet
* Wenn Sie [Zonenredundanz](zone-redundancy.md) in Ihrer Premium Tier Instanz aktiviert haben, wird sie auf der `stv2` Plattform gehostet
* Andernfalls wird die Instanz auf der Plattform `stv1` gehostet

> [!TIP]
> Ab der API-Version `2021-04-01-preview` verfügt die API Management-Instanz über eine schreibgeschützte `PlatformVersion` Eigenschaft, die diese Plattforminformationen anzeigt. 

### <a name="consumption-tier"></a>Verbrauchsebene

* Alle Instanzen werden auf der `mtv1` Plattform gehostet

## <a name="how-do-i-upgrade-to-the-stv2-platform"></a>Gewusst wie: Wie kann ich auf die `stv2`-Plattform wechseln? 

Ein Update ist nur für eine Instanz der Stufe Developer, Basic, Standard oder Premium möglich. 

Erstellen oder aktualisieren Sie die virtuelle Netzwerkverbindung oder die Verfügbarkeitszonenkonfiguration in einer API Management-Instanz mit:

* [Azure portal](https://portal.azure.com)
* Azure REST API oder ARM-Vorlage, mit Angabe der API-Version **2021-01-01-preview** oder höher

> [!IMPORTANT]
> Wenn Sie die Version der Compute-Plattform einer Instanz aktualisieren, die mit einem Azure [virtuellen Netzwerk](virtual-network-concepts.md) verbunden ist:
> * Sie müssen eine Standard SKU [öffentliche IPv4-Adresse](../virtual-network/public-ip-addresses.md#standard) Ressource bereitstellen
> * Die VIP-Adresse(n) Ihrer API Management-Instanz werden sich ändern.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die Verwendung eines [virtuellen Netzwerks](virtual-network-concepts.md) mit API Management.
* Erfahren Sie mehr über [Zonenredundanz](zone-redundancy.md).
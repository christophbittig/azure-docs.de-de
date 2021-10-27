---
title: 'Beispiele für Azure Resource Manager-Vorlagen: Azure Front Door'
description: Hier erfahren Sie mehr über Resource Manager-Vorlagenbeispiele für Azure Front Door, u. a. über Vorlagen zum Erstellen einer grundlegenden Front Door-Instanz und zum Konfigurieren der Front Door-Ratenbegrenzung.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2021
ms.author: duau
ms.openlocfilehash: c76b78b8ed834b1cd3c86cab1bdbf5cb98141956
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073238"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Vorlagen des Azure Resource Manager-Bereitstellungsmodells für Front Door

Die folgende Tabelle enthält Links zu Vorlagen des Azure Resource Manager-Bereitstellungsmodells für Azure Front Door.

| Vorlage | Beschreibung |
| ---| ---|
| [Erstellen einer grundlegenden Front Door-Instanz](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-basic)| Erstellt eine grundlegende Front Door-Konfiguration mit einem einzelnen Back-End. |
| [Erstellen einer Front Door-Instanz mit mehreren Back-Ends und Back-End-Pools sowie URL-basiertem Routing](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-multiple-backends)| Erstellt eine Front Door-Instanz mit Lastenausgleich, die für mehrere Back-Ends in einem Back-End-Pool sowie für mehrere Back-End-Pools basierend auf einem URL-Pfad konfiguriert ist. |
| [Integrieren einer benutzerdefinierten Domäne und eines verwalteten TLS-Zertifikats mit Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain)| Fügen Sie Ihrer Front Door-Instanz eine benutzerdefinierte Domäne hinzu, und verwenden Sie ein von Front Door verwaltetes TLS-Zertifikat. |
| [Integrieren einer benutzerdefinierten Domäne und eines kundenseitig verwalteten TLS-Zertifikats mit Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain-customer-certificate)| Fügen Sie Ihrer Front Door-Instanz eine benutzerdefinierte Domäne hinzu, und verwenden Sie unter Verwendung von Key Vault Ihr eigenes TLS-Zertifikat. |
| [Erstellen einer Front Door-Instanz mit geografischer Filterung](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-geo-filtering)| Erstellt eine Front Door-Instanz, die Datenverkehr aus bestimmten Ländern/Regionen zulässt bzw. blockiert. |
| [Steuern von Integritätstests für Ihre Back-Ends in der Front Door-Instanz](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-health-probes)| Ändert die Einstellungen für Integritätstests in Ihrer Front Door-Instanz, indem der Testpfad sowie die Intervalle, in denen die Tests gesendet werden, aktualisiert werden. |
| [Erstellen einer Front Door-Instanz mit einer Aktiv/Standby-Konfiguration für das Back-End](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-priority-lb)| Erstellt eine Front Door-Instanz, die prioritätsbasiertes Routing für eine Aktiv/Standard-Anwendungstopologie demonstriert. Das bedeutet, dass standardmäßig der gesamte Datenverkehr an das primäre Back-End (das mit der höchsten Priorität) gesendet wird, bis dieses nicht mehr verfügbar ist. |
| [Erstellen einer Front Door-Instanz mit aktiviertem Caching für bestimmte Routen](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-caching)| Erstellt eine Front Door-Instanz mit aktiviertem Caching für die definierte Routingkonfiguration, sodass alle statischen Ressourcen für Ihre Workloads zwischengespeichert werden. |
| [Konfigurieren der Sitzungsaffinität für Ihre Front Door-Hostnamen](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-session-affinity) | Aktualisiert eine Front Door-Instanz und aktiviert die Sitzungsaffinität für Ihren Front-End-Host, sodass nachfolgender Datenverkehr aus der gleichen Benutzersitzung an das gleiche Back-End gesendet wird. |
| [Konfigurieren der Front Door-Instanz für die Aufnahme von Client-IPs in Positivlisten oder Sperrlisten](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-clientip)| Konfiguriert eine Front Door-Instanz, sodass der Datenverkehr von bestimmten Client-IPs mithilfe einer benutzerdefinierten Zugriffssteuerung für Client-IPs eingeschränkt wird. |
| [Konfigurieren der Front Door-Instanz zum Durchführen von Aktionen mit bestimmten HTTP-Parametern](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-http-params)| Konfiguriert eine Front Door-Instanz, sodass bestimmter Datenverkehr basierend auf den http-Parametern in der eingehenden Anforderung zugelassen oder blockiert wird. Hierfür werden benutzerdefinierte Regeln für die Zugriffssteuerung sowie http-Parameter verwendet. |
| [Konfigurieren einer Ratenbegrenzung für die Front Door-Instanz](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-rate-limiting)| Konfiguriert eine Front Door-Instanz, sodass eine Ratenbegrenzung für eingehenden Datenverkehr für einen bestimmten Front-End-Host angewendet wird. |
| | |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).

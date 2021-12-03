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
ms.date: 11/01/2021
ms.author: duau
ms.openlocfilehash: db0bf4783de324af2ab4e5a5ec6bfbaa18ad33cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449771"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Vorlagen des Azure Resource Manager-Bereitstellungsmodells für Front Door

Die folgende Tabelle enthält Links zu Vorlagen des Azure Resource Manager-Bereitstellungsmodells für Azure Front Door.

## <a name="azure-front-door"></a>Azure Front Door

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

## <a name="azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (Vorschau)

| Beispiel | BESCHREIBUNG |
|-|-|
| [Front Door (Schnellerstellung)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium/) | Erstellt ein einfaches Front Door-Profil, einschließlich Endpunkt, Ursprungsgruppe, Ursprung und Route.  |
| [Regelsatz](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set/) | Erstellt ein Front Door-Profil und einen Regelsatz.  |
| [WAF-Richtlinie mit verwaltetem Regelsatz](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-waf-managed/) | Erstellt ein Front Door-Profil und eine WAF mit verwaltetem Regelsatz.  |
| [WAF-Richtlinie mit benutzerdefinierter Regel](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-waf-custom/) | Erstellt ein Front Door-Profil und eine WAF mit benutzerdefinierter Regel.  |
| [WAF-Richtlinie mit Ratenbegrenzung](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rate-limit/) | Erstellt ein Front Door-Profil und eine WAF-Instanz mit einer benutzerdefinierten Regel für die Ratenbegrenzung.  |
| [WAF-Richtlinie mit Geofilterung](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-geo-filtering/) | Erstellt ein Front Door-Profil und eine WAF-Instanz mit einer benutzerdefinierten Regel für die Geofilterung.  |
|**App Service-Ursprünge**| **Beschreibung** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-app-service-public) | Erstellt eine App Service-App mit einem öffentlichen Endpunkt und ein Front Door-Profil.  |
| [App Service mit Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-app-service-private-link) | Erstellt eine App Service-App mit einem privaten Endpunkt und ein Front Door-Profil.  |
|**Azure Functions-Ursprünge**| **Beschreibung** |
| [Azure-Funktionen](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-function-public/) | Erstellt eine App Functions-App mit einem öffentlichen Endpunkt und ein Front Door-Profil.  |
| [Azure Functions mit Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-function-private-link) | Erstellt eine App Functions-App mit einem privaten Endpunkt und ein Front Door-Profil.  |
|**API Management-Ursprünge**| **Beschreibung** |
| [API Management (extern)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-api-management-external) | Erstellt eine API Management-Instanz mit externer VNet-Integration und ein Front Door-Profil.  |
|**Storage-Ursprünge**| **Beschreibung** |
| [Statische Speicherwebsite](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-storage-static-website) | Erstellt ein Azure Storage-Konto und eine statische Website mit einem öffentlichen Endpunkt sowie ein Front Door-Profil.  |
| [Storage-Blobs mit Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-storage-blobs-private-link) | Erstellt ein Azure Storage-Konto und einen Blobcontainer mit einem privaten Endpunkt sowie ein Front Door-Profil.  |
|**Application Gateway-Ursprünge**| **Beschreibung** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-application-gateway-public) | Erstellt eine Application Gateway-Instanz und ein Front Door-Profil. |
|**VM-Ursprünge**| **Beschreibung** |
| [Virtueller Computer mit Private Link-Dienst](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-vm-private-link) | Erstellt einen virtuellen Computer und einen Private Link-Dienst sowie ein Front Door-Profil. |
| | |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).

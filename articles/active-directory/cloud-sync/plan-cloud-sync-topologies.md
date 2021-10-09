---
title: Unterstützte Topologien und Szenarien für die Azure AD Connect-Cloudsynchronisierung
description: Erfahren Sie mehr über verschiedene lokale Topologien und Azure Active Directory-Topologien (Azure AD), in denen die Azure AD Connect-Cloudsynchronisierung verwendet wird.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a480fba6b7923ffb327b22822e4baa3a54acdc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749953"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Unterstützte Topologien und Szenarien für die Azure AD Connect-Cloudsynchronisierung
In diesem Artikel werden verschiedene lokale Topologien und Azure Active Directory-Topologien (Azure AD) beschrieben, in denen die Azure AD Connect-Cloudsynchronisierung verwendet wird. In diesem Artikel werden nur unterstützte Konfigurationen und Szenarien erörtert.

> [!IMPORTANT]
> Microsoft unterstützt die Änderung oder den Einsatz der Azure AD Connect-Cloudsynchronisierung außerhalb dieser formal dokumentierten Konfigurationen oder Aktionen nicht. Diese Konfigurationen oder Aktionen können zu einem inkonsistenten oder nicht unterstützten Status der Azure AD Connect-Cloudsynchronisierung führen. Folglich kann Microsoft auch keinen technischen Support für solche Bereitstellungen leisten.

Weitere Informationen finden Sie im folgenden Video.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWJ8l5]

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Für alle Szenarien und Topologien zu berücksichtigende Aspekte
Im Folgenden finden Sie eine Liste der Informationen, die beim Auswählen einer Lösung zu beachten sind.

- Benutzer und Gruppen müssen in allen Gesamtstrukturen eindeutig identifiziert werden.
- Bei der Cloudsynchronisierung erfolgt kein gesamtstrukturübergreifender Abgleich.
- Ein Benutzer oder eine Gruppe darf nur einmal in allen Gesamtstrukturen dargestellt werden.
- Der Quellanker für Objekte wird automatisch ausgewählt.  Er verwendet das „ms-DS-ConsistencyGuid“-Attribut (sofern vorhanden). Andernfalls wird „ObjectGUID“ verwendet.
- Sie können das für den Quellanker verwendete Attribut nicht ändern.

## <a name="single-forest-single-azure-ad-tenant"></a>Einzelne Gesamtstruktur, einzelner Azure AD-Mandant
![Diagramm der Topologie für eine einzelne Gesamtstruktur und einen einzelnen Mandanten.](media/tutorial-single-forest/diagram-2.png)

Die einfachste Topologie ist eine einzelne lokale Gesamtstruktur mit einer oder mehreren Domänen und einem einzelnen Azure AD-Mandanten.  Ein Beispiel für dieses Szenario finden Sie unter [Tutorial: Eine einzelnen Gesamtstruktur mit einem einzelnen Azure AD-Mandanten](tutorial-single-forest.md).


## <a name="multi-forest-single-azure-ad-tenant"></a>Mehrere Gesamtstrukturen, einzelner Azure AD-Mandant
![Topologie für mehrere Gesamtstrukturen und einen einzelnen Mandanten](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Eine häufig verwendete Topologie umfasst mehrere AD-Gesamtstrukturen mit einer oder mehreren Domänen und einem einzelnen Azure AD-Mandanten.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Vorhandene Gesamtstruktur mit Azure AD Connect, neue Gesamtstruktur mit Cloudbereitstellung
![Diagramm der Topologie für eine vorhandene Gesamtstruktur und eine neue Gesamtstruktur.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Dieses Topologieszenario ist mit dem Szenario mit mehreren Gesamtstrukturen vergleichbar. Hier geht es jedoch um eine vorhandene Azure AD Connect-Umgebung und das Hinzufügen einer neuen Gesamtstruktur mithilfe der Azure AD Connect-Cloudsynchronisierung.  Ein Beispiel für dieses Szenario finden Sie unter [Tutorial: Eine vorhandene Gesamtstruktur mit einem einzelnen Azure AD-Mandanten](tutorial-existing-forest.md).

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Testen der Azure AD Connect-Cloudsynchronisierung in einer vorhandenen Hybrid-AD-Gesamtstruktur
![Topologie für eine einzelne Gesamtstruktur und einen einzelnen Mandanten](media/tutorial-migrate-aadc-aadccp/diagram-2.png) Im Pilottestszenario sind sowohl Azure AD Connect als auch die Azure AD Connect-Cloudsynchronisierung in derselben Gesamtstruktur enthalten, und die Benutzer und Gruppen werden entsprechend festgelegt. HINWEIS:  Ein Objekt darf sich nur in einem der Tools im Gültigkeitsbereich befinden. 

Ein Beispiel für dieses Szenario finden Sie unter [Tutorial: Testen der Azure AD Connect-Cloudsynchronisierung für eine vorhandene synchronisierte AD-Gesamtstruktur](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?](what-is-cloud-sync.md)


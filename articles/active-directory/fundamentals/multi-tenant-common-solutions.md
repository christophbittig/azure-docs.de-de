---
title: Allgemeine Überlegungen zur mehrinstanzenfähigen Benutzerverwaltung in Azure Active Directory
description: Hier erfahren Sie mehr über die gängigen Lösungen, die zum Konfigurieren des Benutzerzugriffs für Azure Active Directory-Mandanten mit Gastkonten verwendet werden.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48412e7ad5c7ff90f1e5b89542f55b57fec4a534
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270663"
---
# <a name="common-solutions-for-multi-tenant-user-management"></a>Allgemeine Lösungen zur mehrinstanzenfähigen Benutzerverwaltung

Es gibt zwei spezifische Herausforderungen, die unsere Kunden mit aktuellen Tools gelöst haben. Ihre Lösungen werden im Folgenden ausführlich beschrieben. Microsoft empfiehlt nach Möglichkeit einen einzelnen Mandanten und arbeitet an Tools, um diese Herausforderungen einfacher zu lösen. Wenn das Einzelmandat für Ihr Szenario nicht funktioniert, haben diese Lösungen heute für Kunden funktioniert. 

## <a name="automatic-user-lifecycle-management-and-resource-allocation-across-tenants"></a>Automatische mandantenübergreifende Benutzerlebenszyklusverwaltung und Ressourcenzuordnung

Ein Kunde fusioniert mit einem Konkurrenten, mit dem er zuvor enge Geschäftsbeziehungen hatte. Die Organisationen behalten ihre Unternehmensidentitäten bei.

### <a name="current-state"></a>Aktueller Status

Derzeit synchronisieren die Organisationen die Benutzer des jeweils anderen Benutzers als Kontakt-E-Mail-Objekte, sodass sie in den Verzeichnissen des jeweils anderen angezeigt werden. 

* Für jeden Ressourcenmandanten ist ein E-Mail-Kontaktobjekt für alle Benutzer im anderen Mandanten aktiviert.

* Mandantenübergreifend ist kein Zugriff auf Anwendungen möglich.

### <a name="goals"></a>Ziele

Dieser Kunde hatte die folgenden Ziele:

* Jeder Benutzer wird weiterhin in der GAL jeder Organisation angezeigt.

   * Änderungen am Lebenszyklus des Benutzerkontos im Stammmandanten werden automatisch in der GAL des Ressourcenmandanten widergespiegelt. 

   * Attributänderungen in Home-Mandanten (z. B. Abteilung, Name, SMTP-Adresse) werden automatisch in der Ressourcenmandanten-GAL und der Home-GAL widergespiegelt.

* Benutzer können auf Anwendungen und Ressourcen im Ressourcenmandanten zugreifen.

* Benutzer können Self-Service-Zugriffsanforderungen an Ressourcen stellen.

### <a name="solution-architecture"></a>Lösungsarchitektur 

Die Organisationen verwenden eine Punkt-zu-Punkt-Architektur mit einer Synchronisierungs-Engine wie MIM.

![Beispiel für eine Point-to-Point-Architektur](media/multi-tenant-common-solutions/point-to-point-sync.png)

Jeder Mandantenadministrator führt folgende Schritte aus, um die Benutzerobjekte zu erstellen:

1. Sicherstellen, dass die Datenbank der Benutzer auf dem neuesten Stand ist.

1. [Bereitstellen und Konfigurieren von MIM](/microsoft-identity-manager/microsoft-identity-manager-deploy)

   1. Adressieren vorhandener Kontaktobjekte.

   1. Erstellenn von B2B External Member-Objekten für die Mitglieder des anderen Mandanten.

   1. Synchronisieren von Benutzerobjektattributen.

1. Zugriffspakete mit [Berechtigungsverwaltung](../governance/entitlement-management-overview.md) bereitstellen und konfigurieren.

   1. Ressourcen, die freigegeben werden sollen

   1. Ablauf- und Zugriffsüberprüfungsrichtlinien

## <a name="sharing-on-premises-apps-across-tenants"></a>Freigeben von lokalen Apps über Mandanten hinweg

Dieser Kunde hat mehrere Peerorganisationen und muss lokale Anwendungen von einem der Mandanten freigeben.

### <a name="current-state"></a>Aktueller Status

Mehrere Peerorganisationen synchronisieren B2B-Gastbenutzer in einer Meshtopologie, wodurch die Ressourcenzuordnung für ihre Cloudanwendungen mandantenübergreifend ermöglicht wird. Sie tun derzeit Folgendes:

* Sie nutzen Anwendungen gemeinsam in Azure AD

* Sie stellen sicher, dass die Benutzerlebenszyklusverwaltung des Ressourcenmandanten basierend auf dem Basismandanten automatisiert ist. Das heißt, Hinzufügen, Ändern, Löschen wird entsprechend berücksichtigt.

* Nur Mitgliedsbenutzer in Unternehmen A greifen auf die lokalen Apps von Unternehmen A zu.

![Mehrmandantenfähige Szenarien](media/multi-tenant-user-management-scenarios/mesh.png)

### <a name="goals"></a>Ziele

In Kombination mit der aktuellen Funktionalität möchten sie Folgendes tun:

* Den externen Gastbenutzern Zugriff auf die lokalen Ressourcen von Unternehmen A gewähren. 

* Apps mit SAML-Authentifizierung

* Apps mit integrierter Windows-Authentifizierung und Kerberos

### <a name="solution-architecture"></a>Lösungsarchitektur

Unternehmen A stellt derzeit SSO für lokale Apps für seine eigenen Mitglieder über Azure-Anwendungsproxy bereit.

![Beispiel für den Anwendungszugriff](media/multi-tenant-common-solutions/app-access-scenario.png)

Um ihren Gastbenutzern den Zugriff auf die gleichen lokalen Anwendungen zu ermöglichen, tun Administratoren bei Mandant A Folgendes:

1. [Zugriff auf SAML-Apps konfigurieren](../external-identities/hybrid-cloud-to-on-premises.md#access-to-saml-apps).

2. [Zugriff auf andere Anwendungen gewähren](../external-identities/hybrid-cloud-to-on-premises.md#access-to-iwa-and-kcd-apps).

3. Lokale Gastbenutzer über [MIM](../external-identities/hybrid-cloud-to-on-premises.md#create-b2b-guest-user-objects-through-mim) oder [PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=51495) erstellen.

Weitere Informationen finden Sie unter B2B-Zusammenarbeit, siehe

[Gewähren des Zugriffs auf lokale Ressourcen für B2B-Benutzer in Azure AD](../external-identities/hybrid-cloud-to-on-premises.md)

[Azure Active Directory B2B-Zusammenarbeit für Hybridorganisationen](../external-identities/hybrid-organizations.md)

## <a name="next-steps"></a>Nächste Schritte
[Einführung in die mehrstufige Benutzerverwaltung](multi-tenant-user-management-introduction.md)

[Szenarien für die mehrinstanzenfähige Endbenutzerverwaltung](multi-tenant-user-management-scenarios.md)

[Allgemeine Überlegungen zu mehreren Mandanten](multi-tenant-common-considerations.md)

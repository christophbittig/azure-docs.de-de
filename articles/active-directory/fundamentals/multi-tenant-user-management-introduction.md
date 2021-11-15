---
title: Konfigurieren der mehrinstanzenfähigen Benutzerverwaltung in Azure Active Directory
description: Hier erfahren Sie mehr über die verschiedenen Muster, die zum Konfigurieren des Benutzerzugriffs für Azure Active Directory-Mandanten mit Gastkonten verwendet werden.
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
ms.openlocfilehash: 379135bc446dbef6a2145a6339b5ddd318a5f579
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270583"
---
# <a name="multi-tenant-user-management"></a>Mehrstufige Benutzerverwaltung 

Die Bereitstellung von Benutzern in einem einzelnen Azure Active Directory-Mandanten (Azure AD) bietet eine einheitliche Ansicht der Ressourcen und einen Richtlinien- bzw. Steuerelementsatz. Dieser Ansatz ermöglicht eine konsistente Benutzerlebenszyklusverwaltung. 

**Von Microsoft wird nach Möglichkeit ein einzelner Mandant empfohlen.** Eine sofortige Konsolidierung in einen einzelnen Azure AD-Mandanten ist jedoch nicht immer möglich. Mehrinstanzenfähige Organisationen können zwei oder mehr Azure AD-Mandanten umfassen. Dies kann zu eindeutigen mandantenübergreifenden Zusammenarbeits- und Verwaltungsanforderungen führen.

Organisationen verfügen möglicherweise über Identitäts- und Zugriffsverwaltungsanforderungen (IAM), die durch Folgendes erschwert werden:

* Fusionen, Übernahmen und Veräußerungen

* Zusammenarbeit in öffentlichen, unabhängigen und/oder regionalen Clouds

* Politische Strukturen oder Organisationsstrukturen, die die Konsolidierung in einem einzelnen Azure AD-Mandanten verhindern

In diesem Leitfaden sind auch Anleitungen enthalten, mit deren Hilfe Sie einen konsistenten Verwaltungszustand des Benutzerlebenszyklus erreichen können. Dies bedeutet, dass Benutzer mithilfe der in Azure verfügbaren Tools mandantenübergreifend bereitgestellt und verwaltet werden können oder die Bereitstellung aufgehoben werden kann. Dies trifft insbesondere bei der Verwendung von [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md) zu.

## <a name="azure-ad-b2b-collaboration"></a>Azure AD B2B-Zusammenarbeit

Mit Azure AD B2B Collaboration können Sie die Anwendungen und Dienste Ihres Unternehmens sicher für externe Gastbenutzer*innen freigeben. Die Benutzer*innen können aus jeder Organisation stammen. Mit Azure AD B2B Collaboration können Sie die Kontrolle über den Zugriff auf Ihre IT-Umgebung und -Daten behalten. Azure AD B2B-Collaboration kann auch verwendet werden, um internen Benutzer*innen Gastzugriff zu gewähren. Üblicherweise wird der B2B-Gastbenutzerzugriff verwendet, um den Zugriff für externe Benutzer*innen zu autorisieren, die nicht von Ihrer eigenen Organisation verwaltet werden. Der Gastbenutzerzugriff kann jedoch auch verwendet werden, um den Zugriff auf mehrere Mandanten zu verwalten, die von Ihrer Organisation verwaltet werden. Obwohl es sich nicht wirklich um eine B2B-Lösung handelt, kann Azure AD B2B Collaboration verwendet werden, um interne Benutzer*innen in Ihrem mehrinstanzenfähigen Szenario zu verwalten.

Die folgenden Links enthalten zusätzliche Informationen, die Sie verwenden können, um mehr über Azure AD B2B Collaboration zu erfahren:

| Artikel| Beschreibung |
| - |-|
| **Konzeptionelle Beiträge**|  |
| [B2B – bewährte Methoden](../external-identities/b2b-fundamentals.md)| Empfehlungen für einen reibungslosen Arbeitsablauf für Ihre Benutzer*innen und Administrator*innen|  
| [B2B und externe Office 365-Freigaben](../external-identities/o365-external-user.md)| Erläutert die Ähnlichkeiten und Unterschiede zwischen der gemeinsamen Nutzung von Ressourcen über B2B, Office 365 und SharePoint/OneDrive|  
| [Eigenschaften eines Azure AD B2B Collaboration-Benutzers](../external-identities/user-properties.md)| Beschreibt die Eigenschaften und Zustände des B2B-Gastbenutzerobjekts in Azure Active Directory (Azure AD). Die Beschreibung enthält Details vor und nach dem Einlösen der Einladung.|  
| [B2B-Benutzertoken](../external-identities/user-token.md)| Enthält Beispiele für Bearertoken für B2B als B2B-Gastbenutzer*in|  
| [Bedingter Zugriff für B2B](../external-identities/conditional-access.md)| Beschreibt, wie der bedingte Zugriff und MFA für Gastbenutzer*innen funktionieren|  
| **Gewusst-wie-Artikel**|  |
| [Masseneinladen von Benutzern für die Azure AD B2B-Zusammenarbeit mithilfe von PowerShell](../external-identities/bulk-invite-powershell.md)| Hier erfahren Sie, wie Sie mit PowerShell Masseneinladungen an externe Benutzer senden.|
| [Erzwingen der mehrstufigen Authentifizierung für B2B-Gastbenutzer](../external-identities/b2b-tutorial-require-mfa.md)|Verwenden Sie Richtlinien für den bedingten Zugriff und MFA, um Authentifizierungsebenen für Mandanten, Apps oder einzelne Gastbenutzer*innen zu erzwingen. |
| [Authentifizierung mit Einmalkennung per E-Mail](../external-identities/one-time-passcode.md)| Mit dem Feature „Einmalkennung per E-Mail“ werden B2B-Gastbenutzer authentifiziert, wenn sie über andere Wege (z. B. über Azure AD, ein Microsoft-Konto (MSA) oder den Verbund mit Google) nicht authentifiziert werden können.|

## <a name="terminology"></a>Begriff

Diese Begriffe werden in diesem Artikel verwendet:

* **Ressourcenmandant:** Der Azure AD-Mandant, der die Ressourcen enthält, die Benutzer*innen für andere Benutzer*innen freigeben möchten

* **Basismandant:** Der Azure AD-Mandant, der Benutzer*innen enthält, die Zugriff auf die Ressourcen im Ressourcenmandanten benötigen

* **Verwaltung des Benutzerlebenszyklus:** Der Prozess der Bereitstellung, Verwaltung und Bereitstellungsaufhebung des Benutzerzugriffs auf Ressourcen.

* **Einheitliche GAL:** Jede*r Benutzer*in in jedem Mandanten kann Benutzer*innen aus jeder Organisation in der globalen Adressliste (Global Address List, GAL) sehen.

## <a name="deciding-how-to-meet-your-requirements"></a>Entscheidung, wie Ihre Anforderungen erfüllt werden

Die individuellen Anforderungen Ihrer Organisation bestimmen Ihre Strategie für die mandantenübergreifende Verwaltung Ihrer Benutzer*innen. Um eine effektive Strategie zu erstellen, müssen Sie Folgendes berücksichtigen:

* Anzahl der Mandanten

* Organisationstyp

* Aktuelle Topologien

* Spezifische Benutzersynchronisierungsanforderungen 

### <a name="common-requirements"></a>Allgemeine Anforderungen

Viele Organisationen konzentrieren sich zunächst auf die Anforderungen, die sie für eine sofortige Zusammenarbeit wünschen. Diese Anforderungen werden manchmal auch als Day-1-Anforderungen bezeichnet und konzentrieren sich darauf, Endbenutzer*innen die reibungslose Zusammenführung zu ermöglichen, ohne ihre Fähigkeit zu unterbrechen, einen Mehrwert für das Unternehmen zu generieren. Wenn Sie Ihre Day-1-Anforderung und Verwaltungsanforderungen definieren, sollten Sie möglicherweise folgende Ziele einschließen: 

| Anforderungskategorie| Allgemeine Anforderungen|
| ------------ | - |
| **Kommunikationsanforderungen**|  |
| Einheitliche globale Adressliste| Jede*r Benutzer*in kann alle anderen Benutzer*innen in der GAL im eigenen Mandanten anzeigen. |
| Informationen zu „Verfügbar/Beschäftigt“| Benutzer*innen ermöglichen, die Verfügbarkeit des jeweils anderen zu ermitteln. Dies ist mit [Organisationsbeziehungen in Exchange Online](/exchange/sharing/organization-relationships/create-an-organization-relationship) möglich.|
| Chat und Präsenz| Benutzer*innen ermöglichen, die Anwesenheit anderer zu bestimmen und Sofortnachrichten zu initiieren. Dies kann über den [externen Zugriff in Microsoft Teams](/microsoftteams/manage-external-access) konfiguriert werden.|
| Buchen von Ressourcen wie Besprechungsräume| Benutzer*innen ermöglichen, Konferenzräume oder andere Ressourcen in der gesamten Organisation zu buchen. Mandantenübergreifende Konferenzraumbuchungen sind derzeit nicht möglich.|
‎Einzelne E-Mail-Domäne| Allen Benutzer*innen ermöglichen, E-Mails aus einer einzelnen E-Mail-Domäne zu senden und zu empfangen, z. B. *users@contoso.com* . Für das Senden ist derzeit eine Lösung zum erneuten Schreiben von Adressen eines Drittanbieters erforderlich.|
| **Erforderliche Zugriffsberechtigungen**|  |
| Dokumentzugriff| Benutzer*innen das Freigeben von Dokumenten aus SharePoint, OneDrive und Teams ermöglichen |
| Verwaltung| Administrator*innen das Verwalten der Konfiguration von Abonnements und Diensten ermöglichen, die über mandantenübergreifend bereitgestellt werden |
| Anwendungszugriff| Endbenutzer*innen den Zugriff auf Anwendungen in der gesamten Organisation gestatten |
| Einmaliges Anmelden| Benutzer*innen ermöglichen, organisationsübergreifend auf Ressourcen zuzugreifen, ohne dass weitere Anmeldeinformationen erforderlich sind|

### <a name="patterns-for-account-creation"></a>Muster für die Kontoerstellung 

Es gibt mehrere Mechanismen zum Erstellen und Verwalten des Lebenszyklus Ihrer Gastbenutzerkonten. Microsoft hat drei gängige Muster entwickelt. Sie können die Muster verwenden, um Ihre Anforderungen zu definieren und zu implementieren. Wählen Sie aus, welches am besten zu Ihrem Szenario passt, und konzentrieren Sie sich dann auf die Details für dieses Muster.

| Mechanismus |  Beschreibung | Beste Eignung wenn |
| - | - | - |
| [Vom Benutzer*innen initiiert](multi-tenant-user-management-scenarios.md#end-user-initiated-scenario) | Administrator*innen für Ressourcenmandanten delegieren die Möglichkeit, Gastbenutzer*innen zum Mandanten oder zu einer App einzuladen oder Benutzer*innen eine Ressource innerhalb des Ressourcenmandanten zuzuweisen. Benutzer*innen aus dem Basismandanten werden einzeln eingeladen oder registriert. |  <li>Benutzer improvisierten Zugriff auf Ressourcen benötigen <li>Keine automatische Synchronisierung von Benutzerattributen erforderlich ist<li>Eine einheitliche GAL nicht erforderlich ist |
|[Skript erstellt](multi-tenant-user-management-scenarios.md#scripted-scenario) | Administrator*innen für Ressourcenmandanten stellen einen skriptbasierten Pullprozess bereit, um die Ermittlung und Bereitstellung von Gastbenutzer*innen für die Unterstützung von Freigabeszenarios zu automatisieren. |  <li>Nicht mehr als zwei Mandanten vorhanden sind<li>Keine automatische Synchronisierung von Benutzerattributen erforderlich ist<li>Benutzer*innen vorkonfigurierten (nicht improvisierten) Zugriff auf Ressourcen benötigen|
|[Automatisiert](multi-tenant-user-management-scenarios.md#automated-scenario)|Administrator*innen für Ressourcenmandanten verwenden ein Identitätsbereitstellungssystem, um die Prozesse für die Bereitstellung und Aufhebung der Bereitstellung zu automatisieren. |  <li>Das vollständige Identity Lifecycle Management mit Bereitstellung und der Aufhebung der Bereitstellung automatisiert werden muss<li>Die Attributsynchronisierung erforderlich ist, um die GAL-Details aufzufüllen und dynamische Berechtigungsszenarios zu unterstützen<li>Benutzer am ersten Tag vorkonfigurierten Zugriff (keinen Ad-hoc-Zugriff) auf Ressourcen an „Day 1“ benötigen|

  
## <a name="next-steps"></a>Nächste Schritte

[Szenarios für die mehrinstanzenfähige Benutzerverwaltung](multi-tenant-user-management-scenarios.md)

[Allgemeine Überlegungen zu mehreren Mandanten](multi-tenant-common-considerations.md)

[Allgemeine Lösungen für mehrere Mandanten](multi-tenant-common-solutions.md)

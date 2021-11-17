---
title: Behandeln von Problemen beim Abrufen von Attributen bei der Personalbereitstellung
description: Hier erfahren Sie, wie Sie Probleme beim Abrufen von Attributen bei der Personalbereitstellung behandeln können.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: f1c0cc4812a3a2770c4b4580d0755bf3766499dd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478737"
---
# <a name="troubleshoot-hr-attribute-retrieval-issues"></a>Behandeln von Problemen beim Abrufen von Personal-Attributen

## <a name="provisioning-app-is-not-fetching-all-workday-attributes"></a>Die Bereitstellungs-App ruft nicht alle Workday-Attribute ab
**Anwendungsbereich:**
* Lokale Benutzerbereitstellung von Workday in Active Directory
* Benutzerbereitstellung von Workday in Azure Active Directory

| | |
|-- | -- |
| **Problem** | Sie haben soeben die Workday-App für eingehende Bereitstellungen eingerichtet und eine Verbindung mit der Workday-Mandanten-URL hergestellt. Sie haben eine Testsynchronisierung ausgeführt und festgestellt, dass die Bereitstellungs-App nicht alle Attribute aus Workday abruft. Nur einige Attribute werden gelesen und für das Ziel bereitgestellt. |
| **Ursache** | Standardmäßig enthält die Workday-App für Bereitstellungen Attributzuordnungen und XPath-Definitionen, die mit Workday Web Services (WWS) der Version 21.1 funktionieren. Wenn Sie beim Konfigurieren der Konnektivität mit Workday in der Bereitstellungs-App explizit die WWS-API-Version angegeben haben (Beispiel: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0`), dann kann dieses Problem aufgrund des Konflikts zwischen der WWS-API-Version und den XPath-Definitionen auftreten.  |
| **Auflösung** | * *Option 1*: Entfernen Sie die WWS-API-Versionsinformationen aus der URL, und verwenden Sie die Standardversion der WWS-API (v21.1). <br> * *Option 2*: Aktualisieren Sie die Ausdrücke der XPath-API manuell, damit Sie mit Ihrer bevorzugten WWS-API-Version kompatibel sind. Aktualisieren Sie die **Ausdrücke der XPath-API** unter **Attributzuordnung -> Erweiterte Optionen -> Edit attribute list for Workday** (Workday-Attributliste bearbeiten) unter Verweis auf den Abschnitt [Workday attribute reference](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) (Workday-Attributreferenz).  |

## <a name="provisioning-app-is-not-fetching-workday-integration-system-attributes--calculated-fields"></a>Die Bereitstellungs-App ruft keine Attribute bzw. berechneten Felder des Workday-Integrationssystems ab
**Anwendungsbereich:**
* Lokale Benutzerbereitstellung von Workday in Active Directory
* Benutzerbereitstellung von Workday in Azure Active Directory

| | |
|-- | -- |
| **Problem** | Sie haben soeben die Workday-App für eingehende Bereitstellungen eingerichtet und eine Verbindung mit der Workday-Mandanten-URL hergestellt. Sie haben ein Integrationssystem in Workday konfiguriert, und Sie haben XPath-Instanzen konfiguriert, die auf Attribute im Workday-Integrationssystem verweisen. Die Azure AD-App für Bereitstellungen ruft jedoch keine Werte ab, die diesen Integrationssystemattributen oder berechneten Feldern zugeordnet sind. |
| **Ursache** | Dies ist eine bekannte Einschränkung. Die Bereitstellungs-App für Workday unterstützt derzeit nicht das Abrufen von berechneten Feldern bzw. Integrationssystemattributen.  |
| **Auflösung** | Es gibt keine Problemumgehung für diese Einschränkung. |


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr zu Integrationsszenarien und Webdienstaufrufen für Azure AD und Workday.](workday-integration-reference.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](check-status-user-account-provisioning.md)


---
title: Behandeln von Rückbuchungsproblemen bei der HR-Bereitstellung
description: Behandeln von Rückbuchungsproblemen bei der HR-Bereitstellung
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 1d40b2738e52866ebc47bbe43b673c52b774619f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324534"
---
# <a name="troubleshoot-hr-write-back-issues"></a>Behandeln von HR Rückbuchungsproblemen

## <a name="null-and-empty-values-not-processed-as-expected"></a>Null- und leere Werte werden nicht wie erwartet verarbeitet
**Anwendungsbereich:**
* Workday Writeback
* SAP SuccessFactors: Rückschreiben

| Problembehandlung | Details |
|-- | -- |
| **Problem** | Sie haben die Rückbuchungs-App erfolgreich konfiguriert. Sie erhalten Null oder einen leeren Wert aus Azure AD. Sie erwarten, dass der Bereitstellungsdienst den entsprechenden E-Mail- oder Telefonnummernwert in der HR-App löscht. Dabei tritt indes ein Fehler auf. |
| **Ursache** | Der Bereitstellungsdienst verfügt über keine Standardlogik für die Verarbeitung von NULL-Werten. Wenn der Bereitstellungsdienst eine leere Zeichenfolge von der Quell-App erhält, versucht er, den Wert "as-is" (wie vorhanden) an die Ziel-App zu übergeben. Wenn Workday oder SuccessFactors keine leeren Werte verarbeiten können, wird ein Fehler ausgegeben. |
| **Auflösung** | Aktualisieren Sie die Attributzuordnung, um Ausdruckszuordnungen wie unten empfohlen zu verwenden. |

**Empfohlene Lösungen**

  Angenommen, das `telephoneNumber`SAP SuccessFactors-Attribut `businessPhoneNumber` ist in Azure AD Null oder leer. 
  * Option 1: Definieren Sie einen Ausdruck, um mithilfe von Funktionen wie [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) oder [IsPresent](functions-for-customizing-application-data.md#ispresent) nach leeren oder Null-Werten zu suchen, und übergeben Sie einen nicht-leeren Literalwert (in diesem Fall 000-000-0000). 
  
     `IIF(IsNullOrEmpty([telephoneNumber]),"000-000-0000",[telephoneNumber])`

  * Option 2: Verwenden Sie die Funktion [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty), um leere oder Null-Attribute in der an SuccessFactors gesendeten Nutzlast zu löschen. 
  
     `IgnoreFlowIfNullOrEmpty([telephoneNumber])` 



## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr zu Integrationsszenarien und Webdienstaufrufen für Azure AD und Workday.](workday-integration-reference.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](check-status-user-account-provisioning.md)

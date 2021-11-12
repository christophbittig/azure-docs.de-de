---
title: Behandeln von Rückbuchungsproblemen bei der HR-Bereitstellung
description: Erfahren Sie, wie Sie Probleme bei der Änderung von Vorgesetzten in der Personalbereitstellung behandeln
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
ms.openlocfilehash: ff6f9d291b52b7028fbccdc001bc10d60c8c1450
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478906"
---
# <a name="troubleshoot-hr-write-back-issues"></a>Behandeln von HR Rückbuchungsproblemen

## <a name="null-and-empty-values-not-processed-as-expected"></a>Null- und leere Werte werden nicht wie erwartet verarbeitet
**Anwendungsbereich:**
* Workday Writeback
* SAP SuccessFactors: Rückschreiben

| | |
|-- | -- |
| **Problem** | Sie haben die Rückbuchungs-App erfolgreich konfiguriert. Sie erhalten Null oder einen leeren Wert aus Azure AD. Sie erwarten, dass der Bereitstellungsdienst den entsprechenden E-Mail- oder Telefonnummernwert in der HR-App löscht. Dabei tritt indes ein Fehler auf. |
| **Ursache** | Der Bereitstellungsdienst verfügt nicht über eine Standardlogik für die Verarbeitung von Null-Werten. Wenn der Bereitstellungsdienst eine leere Zeichenfolge von der Quell-App erhält, versucht er, den Wert "as-is" (wie vorhanden) an die Ziel-App zu übergeben. Wenn Workday oder SuccessFactors keine leeren Werte verarbeiten können, wird ein Fehler ausgegeben. |
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

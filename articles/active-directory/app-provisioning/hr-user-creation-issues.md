---
title: Problembehebung bei der Benutzererstellung mit der Personalbereitstellung
description: Hier erfahren Sie, wie Sie Probleme bei der Benutzererstellung mit der Personalbereitstellung beheben.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 0c660221c230f884b43252cef25626e3d2b1d700
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324572"
---
# <a name="troubleshoot-hr-user-creation-issues"></a>Problembehebung bei der HR-Benutzererstellung

## <a name="creation-fails-due-to-null--empty-values"></a>Fehler bei der Erstellung aufgrund von NULL-Werten/leeren Werten 

**Anwendungsbereich:**
* Lokale Benutzerbereitstellung von Workday in Active Directory
* Benutzerbereitstellung von Workday in Azure Active Directory
* SAP SuccessFactors zur Benutzerbereitstellung in lokalem Active Directory
* SAP SuccessFactors zur Benutzerbereitstellung in Azure Active Directory

| Problembehandlung | Details |
|-- | -- |
| **Problem** | Sie haben die App für die eingehende Bereitstellung erfolgreich konfiguriert. Sie erhalten NULL- oder leere Werte von der HR-App. Der Erstellungsvorgang schlägt mit der folgenden Fehlermeldung fehl: `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839` |
| **Ursache** | Der Bereitstellungsdienst verfügt über keine Standardlogik für die Verarbeitung von NULL-Werten. Wenn der Bereitstellungsdienst eine leere Zeichenfolge von der Quell-App erhält, versucht er den Wert „as-is“ (wie vorhanden) an die Ziel-App zu übergeben. In diesem Fall unterstützt ein lokales Active Directory das Festlegen leerer Zeichenfolgenwerte nicht. Daher wird der oben genannte Fehler angezeigt. |
| **Auflösung** | Überprüfen Sie die Bereitstellungsprotokolle. Identifizieren Sie Attribute im Ziel-Active Directory, die NULL- oder leere Zeichenfolgenwerte empfangen. Aktualisieren Sie die Attributzuordnung für solche Attribute, um eine Ausdruckszuordnung zu verwenden. Weitere Informationen finden Sie weiter unten in den empfohlenen Lösungen. |

**Empfohlene Lösungen**

  Angenommen, das Attribut `BusinessTitle`, das dem AD-Attribut `jobTitle` zugeordnet ist, ist in Workday NULL oder leer. 
  * Option 1: Definieren Sie einen Ausdruck, um mithilfe von Funktionen wie [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) oder [IsPresent](functions-for-customizing-application-data.md#ispresent) nach leeren oder NULL-Werten zu suchen, und übergeben Sie einen nicht leeren Literalwert. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * Option 2: Verwenden Sie die Funktion [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty), um leere oder NULL-Attribute von der an das lokale Active Directory gesendeten Payload zu trennen. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr zu Integrationsszenarien und Webdienstaufrufen für Azure AD und Workday.](workday-integration-reference.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](check-status-user-account-provisioning.md)


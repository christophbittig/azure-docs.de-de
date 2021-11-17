---
title: Behandeln von Problemen bei der Benutzeraktualisierung bei der Personalbereitstellung
description: Hier erfahren Sie, wie Sie Probleme bei der Benutzeraktualisierung bei der Personalbereitstellung behandeln.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: c983429508fa55f14bb8450c58a7a106df00278f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342526"
---
# <a name="troubleshoot-hr-user-update-issues"></a>Behandeln von Problemen bei der Benutzeraktualisierung bei der Personalbereitstellung

## <a name="null-and-empty-values-not-processed-as-expected"></a>NULL- und leere Werte werden nicht wie erwartet verarbeitet.
**Anwendungsbereich:**
* Lokale Benutzerbereitstellung von Workday in Active Directory
* Benutzerbereitstellung von Workday in Azure Active Directory
* SAP SuccessFactors zur Benutzerbereitstellung in lokalem Active Directory
* SAP SuccessFactors zur Benutzerbereitstellung in Azure Active Directory

| Problembehandlung | Details |
|-- | -- |
| **Problem** | Sie haben die App für die eingehende Bereitstellung erfolgreich konfiguriert. Sie erhalten einen NULL- oder leeren Wert von der HR-App. Sie erwarten, dass der Bereitstellungsdienst den entsprechenden Zielattributwert in lokalem Active Directory/Azure AD löscht. Der Vorgang schlägt jedoch mit dieser Fehlermeldung fehl: `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839` |
| **Ursache** | Der Bereitstellungsdienst verfügt über keine Standardlogik für die Verarbeitung von NULL-Werten. Wenn der Bereitstellungsdienst eine leere Zeichenfolge von der Quell-App erhält, versucht er den Wert „as-is“ (wie vorhanden) an die Ziel-App zu übergeben. In diesem Fall unterstützt ein lokales Active Directory das Festlegen leerer Zeichenfolgenwerte nicht. Daher wird der oben genannte Fehler angezeigt. |
| **Auflösung** | Überprüfen Sie die Bereitstellungsprotokolle. Identifizieren Sie Attribute im Ziel-Active Directory, die NULL- oder leere Zeichenfolgenwerte empfangen. Aktualisieren Sie die Attributzuordnung für solche Attribute, um eine Ausdruckszuordnung zu verwenden. Weitere Informationen finden Sie weiter unten in den empfohlenen Lösungen. |

**Empfohlene Lösungen**

  Angenommen, das Attribut `BusinessTitle`, das dem AD-Attribut `jobTitle` zugeordnet ist, ist in Workday NULL oder leer. 
  * Option 1: Definieren Sie einen Ausdruck, um mithilfe von Funktionen wie [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) oder [IsPresent](functions-for-customizing-application-data.md#ispresent) nach leeren oder NULL-Werten zu suchen, und übergeben Sie einen nicht leeren Literalwert. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * Option 2: Verwenden Sie die Funktion [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty), um leere oder NULL-Attribute von der an das lokale Active Directory gesendeten Payload zu trennen. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 

## <a name="some-workday-attribute-updates-are-missing"></a>Einige Aktualisierungen von Workday-Attributen fehlen
**Anwendungsbereich:**
* Lokale Benutzerbereitstellung von Workday in Active Directory
* Benutzerbereitstellung von Workday in Azure Active Directory

| Problembehandlung | Details |
|-- | -- |
| **Problem** | Sie haben die Workday-App für eingehende Bereitstellungen erfolgreich konfiguriert und erfolgreich eine Verbindung mit der Workday-Mandanten-URL hergestellt. Sie beobachten, dass der Fluss bestimmter Attributaktualisierungen von Workday verzögert wird oder dass die Attributänderungen von Workday in manchen Fällen während der inkrementellen Synchronisierung nicht wie erwartet ablaufen. |
| **Ursache** | Während der inkrementellen Synchronisierung fragt die Bereitstellungs-App das Workday-Transaktionsprotokoll nach Änderungen an der primären Workerentität ab und bearbeitet nur Änderungen, die vom Transaktionsprotokoll von Workday nachverfolgt werden. <br> Wenn Änderungen an einem Workday-Attribut in Ihrem Setup nicht vom Transaktionsprotokoll von Workday nachverfolgt werden, kann Azure AD diese Änderung nicht abrufen. Beispiel: Das *LocalReference*-Workday-Attribut ist Teil der Standardattributzuordnung und verfügt über XPath `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Local_Reference/wd:ID[@wd:type='Locale_ID']/text()`. Beachten Sie, dass dieses Attribut Teil der Entität *Business_Site_Summary_Data* ist. Eine Änderung des Werts dieses Attributs in Workday wird nicht im Workday-Transaktionsprotokoll angezeigt. Daher wird während der inkrementellen Synchronisierung der neue Wert dieses Attributs nur angezeigt, wenn sich ein Attribut, das der primären Workerentität zugeordnet ist, auch während des Synchronisierungsintervalls ändert. |
| **Auflösung** | Wenn Sie dieses Verhalten häufig beobachten, wenn Änderungen an bestimmten Workday-Attributen nicht fließen, wird empfohlen, regelmäßig eine wöchentliche oder monatliche vollständige Synchronisierung vorzunehmen. |

## <a name="user-match-with-extensionattribute-not-working"></a>Benutzerabgleich mit extensionAttribute funktioniert nicht
**Anwendungsbereich:**
* Benutzerbereitstellung von Workday in Azure Active Directory
* SAP SuccessFactors zur Benutzerbereitstellung in Azure Active Directory

| Problembehandlung | Details |
|-- | -- |
| **Problem** | Angenommen, Sie verwenden *extensionAttribute3* in Azure AD, um die Mitarbeiter-ID zu speichern, und Sie haben sie der Workday-*WorkerID* oder dem SuccessFactors *personIdExternal*-Attribut für den Benutzerabgleich zugeordnet. Bei dieser Konfiguration tritt beim Abgleich des Bereitstellungsprozess ein Fehler auf. Dieses Problem wirkt sich sowohl auf die Benutzererstellung als auch auf Updates aus. |
| **Ursache** | Die Azure AD *OnPremisesExtensionAttributes* (`extensionAttributes1-15`) können nicht als übereinstimmende Attribute verwendet werden, da der `$filter`-Parameter der **Azure AD-Graph-API** nicht das [Filtern nach extensionAttributes unterstützt](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#filter). |
| **Auflösung** | Verwenden Sie nicht Azure AD *OnPremisesExtensionAttributes* (`extensionAttributes1-15`) im übereinstimmenden Attributpaar. Verwenden Sie die employeeID. |


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr zu Integrationsszenarien und Webdienstaufrufen für Azure AD und Workday.](workday-integration-reference.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](check-status-user-account-provisioning.md)

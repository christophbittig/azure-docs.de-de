---
title: Herunterladen von Protokollen in Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Aktivitätsprotokolle in Azure Active Directory herunterladen können.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/14/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bbf95fe288bb81ac8a1580e56244bea11e52821
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995065"
---
# <a name="how-to-download-logs-in-azure-active-directory"></a>Vorgehensweise: Herunterladen von Protokollen in Azure Active Directory

Das Azure Active Directory-Portal (Azure AD) bietet Ihnen Zugriff auf drei Arten von Aktivitätsprotokollen:

- **[Anmeldungen](concept-sign-ins.md)** : Informationen zu Anmeldungen und zur Verwendung Ihrer Ressourcen durch Ihre Benutzer.
- **[Überwachung](concept-audit-logs.md)** : Informationen zu Änderungen, die auf Ihren Mandanten angewendet wurden, z. B. Benutzer- und Gruppenverwaltung oder Updates, die auf die Ressourcen Ihres Mandanten angewendet wurden.
- **[Bereitstellung](concept-provisioning-logs.md)** : Vom Bereitstellungsdienst ausgeführte Aktivitäten, z. B. die Erstellung einer Gruppe in ServiceNow oder der Import eines Benutzers aus Workday.

Azure AD speichert die Daten für einen begrenzten Zeitraum in diesen Protokollen. Als IT-Administrator können Sie Ihre Aktivitätsprotokolle herunterladen, um über eine langfristige Sicherung zu verfügen.

In diesem Artikel wird erläutert, wie Sie Aktivitätsprotokolle in Azure AD herunterladen können.  

## <a name="what-you-should-know"></a>Wichtige Informationen

- Im Azure AD-Portal finden Sie mehrere Einstiegspunkte zu den Aktivitätsprotokollen. Beispielsweise der Abschnitt **Aktivität** auf der Seite [Benutzer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) oder [Gruppen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups). Es gibt jedoch nur einen Ort, der Ihnen eine anfänglich ungefilterte Ansicht der Protokolle bietet: der Abschnitt **Überwachung** auf der Seite [Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview).    

- Azure AD speichert Aktivitätsprotokolle nur für einen bestimmten Zeitraum. Weitere Informationen finden Sie unter [Wie lange speichert Azure AD die Berichtsdaten?](reference-reports-data-retention.md) 

- Durch Herunterladen der Protokolle können Sie steuern, wie lange Protokolle gespeichert werden. 

- Sie können bis zu 250 000 Datensätze herunterladen. Wenn Sie weitere Daten herunterladen möchten, verwenden Sie die Berichterstellungs-API.

- Ihr Download basiert auf dem Filter, den Sie eingestellt haben. 

- Azure AD unterstützt die folgenden Formate für Ihren Download:

    - **CSV** 

    - **JSON** 

- Die Zeitstempel in den heruntergeladenen Dateien basieren immer auf UTC.



## <a name="what-license-do-you-need"></a>Welche Lizenz benötigen Sie?

Die Option zum Herunterladen der Daten eines Aktivitätsprotokolls ist in allen Editionen von Azure AD verfügbar.

Sie können Aktivitätsprotokolle auch mit Microsoft Graph herunterladen. Das grammatische Herunterladen von Protokollen erfordert jedoch eine Premium-Lizenz.


## <a name="who-can-do-it"></a>Wer kann es durchführen?

Obwohl es mit dem globalen Administratorkonto funktioniert, sollten Sie ein Konto mit geringeren Berechtigungen verwenden, um diese Aufgabe auszuführen. Die folgenden Rollen funktionieren für den Zugriff auf die Überwachungsprotokolle:

- Report Reader (Leseberechtigter für Berichte)
- Globaler Leser
- Sicherheitsadministrator
- Sicherheitsleseberechtigter


## <a name="how-to-do-it"></a>Vorgehensweise


**So laden Sie ein Aktivitätsprotokoll herunter**

1. Navigieren Sie zur relevanten Aktivitätsprotokollansicht:
 
    - [Anmeldeprotokoll](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)
    
    - [Überwachungsprotokoll](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)    
       
    - [Bereitstellungsprotokoll](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)    
   

2.  **Fügen** Sie den erforderlichen Filter hinzu.  

    ![Filter hinzufügen](./media/\howto-download-logs/add-filter.png)    

3. **Laden** Sie die Daten herunter.

    ![Herunterladen des Protokolls](./media/\howto-download-logs/download-log.png)

## <a name="next-steps"></a>Nächste Schritte

- [Anmeldeprotokolle in Azure AD](concept-sign-ins.md)
- [Überwachungsprotokolle in Azure AD](concept-audit-logs.md)
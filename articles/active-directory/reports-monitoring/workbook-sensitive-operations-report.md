---
title: Arbeitsmappe „Berichte zu sicherheitsrelevanten Vorgängen“ in Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Arbeitsmappe „Berichte zu sicherheitsrelevanten Vorgängen“ verwenden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: a44aa07539be94aa645c95a6cee544bf92468bd7
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990544"
---
# <a name="sensitive-operations-report-workbook"></a>Arbeitsmappe „Berichte zu sicherheitsrelevanten Vorgängen“

Als IT-Administrator müssen Sie in der Lage sein, Kompromittierungen in Ihrer Umgebung zu identifizieren, um sicherzustellen, dass sie sich in einem ordnungsgemäßen Zustand befindet. 

Mit der Arbeitsmappe „Berichte zu sicherheitsrelevanten Vorgängen“ lassen sich verdächtige Anwendungs- und Dienstprinzipalaktivitäten identifizieren, die auf eine Kompromittierung in Ihrer Umgebung hinweisen können.


Dieser Artikel enthält eine Übersicht über diese Arbeitsmappe.


## <a name="description"></a>Beschreibung

![Arbeitsmappenkategorie](./media/workbook-sensitive-operations-report/workbook-category.png)

In dieser Arbeitsmappe werden die kürzlich erfolgten sicherheitsrelevanten Vorgänge identifiziert, die in Ihrem Mandanten ausgeführt wurden und möglicherweise den Dienstprinzipal kompromittiert haben.

Wenn Ihre Organisation noch nicht mit Azure Monitor-Arbeitsmappen gearbeitet hat, müssen Sie Ihre Azure AD-Anmelde- und Überwachungsprotokolle in Azure Monitor integrieren, bevor Sie auf die Arbeitsmappe zugreifen. Dann können Sie mithilfe von Arbeitsmappen Ihre Protokolle bis zu zwei Jahre lang speichern, abfragen und visualisieren. Es werden nur Anmelde- und Überwachungsereignisse, die nach der Integration in Azure Monitor erstellt wurden, gespeichert. Daher enthält die Arbeitsmappe keine Erkenntnisse zu Vorgängen vor diesem Zeitpunkt. Erfahren Sie mehr über die Voraussetzungen für Azure Monitor-Arbeitsmappen für Azure Active Directory. Wenn Sie Ihre Azure AD-Anmelde- und Überwachungsprotokolle schon in Azure Monitor integriert haben, können Sie mithilfe der Arbeitsmappe frühere Informationen auswerten. 
 
 

## <a name="sections"></a>Abschnitte

Diese Arbeitsmappe ist in vier Abschnitte unterteilt:

![Arbeitsmappenabschnitte](./media/workbook-sensitive-operations-report/workbook-sections.png)


- **Geänderte Anmeldeinformationen/Authentifizierungsmethoden für Anwendung und Dienstprinzipal**: Dieser Bericht kennzeichnet Akteure, die kürzlich viele Dienstprinzipalanmeldeinformationen geändert haben,und gibt an, wie viele der unterschiedlichen Anmeldeinformationstypen für Dienstprinzipale geändert wurden.

- **Neue Berechtigungen für Dienstprinzipale**: Diese Arbeitsmappe hebt auch die kürzlich Dienstprinzipalen gewährten OAuth 2.0-Berechtigungen hervor. 

- **Aktualisierungen der Verzeichnisrollen- und Gruppenmitgliedschaft für Dienstprinzipale**



- **Geänderte Verbundeinstellungen**: Dieser Bericht hebt hervor, wenn ein Benutzer oder eine Anwendung Verbundeinstellungen für eine Domäne ändert. Gemeldet wird beispielsweise, wenn der Domäne ein neues TrustedRealm-Objekt des Active Directory-Verbunddiensts (Active Directory Federated Service, ADFS) hinzugefügt wird (z. B. ein Signaturzertifikat). Änderungen an Domänenverbundeinstellungen sollten selten erfolgen. 




### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>Geänderte Anmeldeinformationen/Authentifizierungsmethoden für Anwendungen und Dienstprinzipale

Eine der häufigsten Methoden für Angreifer, dauerhaft Zugriff auf die Umgebung zu erhalten, ist das Hinzufügen neuer Anmeldeinformationen zu vorhandenen Anwendungen und Dienstprinzipalen. Mithilfe der Anmeldeinformationen kann der Angreifer sich als Zielanwendung oder Dienstprinzipal authentifizieren und Zugriff auf alle Ressourcen erhalten, für die die Anwendung oder der Dienstprinzipal Berechtigungen hat.

Dieser Abschnitt enthält die folgenden Daten, die Sie bei der Erkennung unterstützen:

- Alle neuen Anmeldeinformationen, die Apps und Dienstprinzipalen hinzugefügt wurden, einschließlich des Anmeldeinformationstyps

- Die wichtigsten Akteure und die Menge der von ihnen vorgenommenen Änderungen an Anmeldeinformationen

- Eine Zeitachse für alle Änderungen der Anmeldeinformationen



### <a name="new-permissions-granted-to-service-principals"></a>Neue Berechtigungen für Dienstprinzipale

Wenn der Angreifer keinen Dienstprinzipal oder keine Anwendung mit hohen Berechtigungen findet, mit denen er Zugriff erhält, versucht er häufig, die Berechtigungen einem anderen Dienstprinzipal oder einer anderen App hinzuzufügen.

Dieser Abschnitt enthält eine Aufschlüsselung der AppOnly-Berechtigungen, die vorhandenen Dienstprinzipalen gewährt werden. Administratoren sollten alle Vorkommen übermäßig hoher Berechtigungen untersuchen, die gewährt werden, einschließlich, aber nicht beschränkt auf Exchange Online, Microsoft Graph und Azure AD Graph.


### <a name="directory-role-and-group-membership-updates-for-service-principals"></a>Aktualisierungen der Verzeichnisrollen- und Gruppenmitgliedschaft für Dienstprinzipale 

Der Logik des Angreifers, vorhandenen Dienstprinzipalen und Anwendungen neue Berechtigungen hinzuzufügen, entspricht auch ein weiterer Ansatz, nämlich die Berechtigungen vorhandenen Verzeichnisrollen oder Gruppen hinzuzufügen.

Dieser Abschnitt enthält eine Übersicht über alle Änderungen, die an Mitgliedschaften des Dienstprinzipals vorgenommen wurden, und sollte auf mögliche Ergänzungen von Rollen und Gruppen mit hohen Rechten überprüft werden.



### <a name="modified-federation-settings"></a>Geänderte Verbundeinstellungen

Auch folgender Ansatz wird häufig verwendet, um in der Umgebung langfristig Fuß zu fassen:

- Ändern der Domänenverbundvertrauensstellungen des Mandanten.
- Hinzufügen eines zusätzlichen SAML-IDP hinzu, der vom Angreifer als vertrauenswürdige Authentifizierungsquelle gesteuert wird. 

Dieser Abschnitt enthält die folgenden Daten:

- An vorhandenen Domänenverbundvertrauensstellungen vorgenommene Änderungen

- Hinzufügungen neuer Domänen und Vertrauensstellungen


  


## <a name="filters"></a>Filter

In diesem Absatz werden die unterstützten Filter für jeden Abschnitt aufgeführt.


### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>Geänderte Anmeldeinformationen/Authentifizierungsmethoden für Anwendungen und Dienstprinzipale

- Uhrzeitbereich
- Vorgangsname
- Anmeldeinformationen
- Akteur
- Akteur ausschließen


### <a name="new-permissions-granted-to-service-principals"></a>Neue Berechtigungen für Dienstprinzipale

- Uhrzeitbereich
- Client-App
- Resource

### <a name="directory-role-and-group-membership-updates-to-service-principals"></a>Aktualisierungen der Verzeichnisrollen- und Gruppenmitgliedschaft für Dienstprinzipale

- Uhrzeitbereich
- Vorgang
- Initiieren eines Benutzers oder einer App

### <a name="modified-federation-settings"></a>Geänderte Verbundeinstellungen

- Uhrzeitbereich
- Vorgang
- Initiieren eines Benutzers oder einer App




## <a name="best-practices"></a>Bewährte Methoden


**Verwendung:**
 
- **Geänderte Anmeldeinformationen für Anwendungen und Dienstprinzipale**, um nach Anmeldeinformationen zu suchen, die Dienstprinzipalen hinzugefügt werden, die in Ihrer Organisation nicht häufig verwendet werden. Verwenden Sie die in diesem Abschnitt enthaltenen Filter, um verdächtige Akteure oder Dienstprinzipale, die geändert wurden, genauer zu untersuchen.


- **Neue Berechtigungen für Dienstprinzipale**, um nach umfassenden oder übermäßigen Berechtigungen zu suchen, die Dienstprinzipalen von Akteuren hinzugefügt werden, die möglicherweise kompromittiert sind.  

- Im Abschnitt **Geänderte Verbundeinstellungen** können Sie bestätigen, dass die hinzugefügte oder geänderte Zieldomäne/URL einem legitimen Administratorverhalten entspricht. Aktionen, durch die Domänenverbundvertrauensstellungen geändert oder hinzugefügt werden, sind selten und sollten mit großer Sorgfalt und baldmöglichst untersucht werden.





## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure AD-Arbeitsmappen](howto-use-azure-monitor-workbooks.md)

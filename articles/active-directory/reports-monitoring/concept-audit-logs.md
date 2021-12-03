---
title: Überwachungsprotokolle in Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die Überwachungsprotokolle in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: d551124491c33cd74f3fbdf1f4b401ada99399d4
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997170"
---
# <a name="audit-logs-in-azure-active-directory"></a>Überwachungsprotokolle in Azure Active Directory 

Als IT-Administrator müssen Sie wissen, wie Ihre IT-Umgebung funktioniert. Anhand der Informationen zur Integrität Ihres Systems können Sie bewerten, ob und wie Sie auf potenzielle Probleme reagieren müssen. 

Um Sie bei diesem Ziel zu unterstützen, bietet Ihnen das Azure Active Directory-Portal Zugriff auf drei Aktivitätsprotokolle:

- **[Anmeldungen](concept-sign-ins.md)** : Informationen zu Anmeldungen und zur Verwendung Ihrer Ressourcen durch Ihre Benutzer.
- **[Überwachung](concept-audit-logs.md)** : Informationen zu Änderungen, die auf Ihren Mandanten angewendet wurden, z. B. Benutzer- und Gruppenverwaltung oder Updates, die auf die Ressourcen Ihres Mandanten angewendet wurden.
- **[Bereitstellung](concept-provisioning-logs.md)** : Vom Bereitstellungsdienst ausgeführte Aktivitäten, z. B. die Erstellung einer Gruppe in ServiceNow oder der Import eines Benutzers aus Workday.

Dieser Artikel bietet eine Übersicht über die Überwachungsprotokolle.


## <a name="what-is-it"></a>Was ist das?

Durch die Überwachungsprotokolle in Azure AD erhalten Sie Zugriff auf Datensätze mit Systemaktivitäten zu Compliancezwecken.
Die am häufigsten verwendeten Ansichten dieses Protokolls basieren auf den folgenden Kategorien:

- Benutzerverwaltung

- Gruppenverwaltung
 
- Anwendungsverwaltung  


Mit einer benutzerorientierten Ansicht können Sie Antworten auf beispielsweise folgende Fragen erhalten:

- Welche Typen von Updates wurden auf Benutzer angewendet?

- Wie viele Benutzer wurden geändert?

- Wie viele Kennwörter wurden geändert?

- Welche Schritte hat ein Administrator in einem Verzeichnis ausgeführt?


Mit einer gruppenorientierten Ansicht können Sie Antworten auf beispielsweise folgende Fragen erhalten:

- Welche Gruppen wurden hinzugefügt?

- Sind Gruppen mit Änderungen der Mitgliedschaft vorhanden?

- Haben sich die Besitzer der Gruppe geändert?

- Welche Lizenzen wurden einer Gruppe oder einem Benutzer zugewiesen?

Mit einer anwendungsorientierten Ansicht können Sie Antworten auf beispielsweise folgende Fragen erhalten:

- Welche Anwendungen wurden hinzugefügt oder aktualisiert?

- Welche Anwendungen wurden entfernt?

- Hat sich ein Dienstprinzipal für eine Anwendung geändert?

- Haben sich die Namen von Anwendungen geändert?

- Wer hat die Zustimmung zu einer Anwendung erteilt?

 
## <a name="what-license-do-i-need"></a>Welche Lizenz benötige ich?

Der Überwachungsaktivitätsbericht ist in allen Editionen von Azure AD verfügbar.

## <a name="who-can-access-it"></a>Wer kann auf sie zugreifen?

Um auf die Überwachungsprotokolle zugreifen zu können, muss Ihnen eine der folgenden Rollen zugewiesen sein: 

- Sicherheitsadministrator
- Sicherheitsleseberechtigter
- Report Reader (Leseberechtigter für Berichte)
- Globaler Leser
- Globaler Administrator

## <a name="where-can-i-find-it"></a>Wo finde ich es?

Das Azure-Portal bietet Ihnen mehrere Optionen für den Zugriff auf das Protokoll. Im Azure Active Directory-Menü können Sie beispielsweise im Abschnitt **Überwachung** öffnen.  

![Öffnen von Überwachungsprotokollen](./media/concept-audit-logs/audit-logs-menu.png)

Darüber hinaus können Sie über [diesen Link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents) direkt zu den Überwachungsprotokollen gelangen.


Sie können auch über die Microsoft Graph-API auf das Überwachungsprotokoll zugreifen.


## <a name="what-is-the-default-view"></a>Was ist die Standansicht?

Ein Überwachungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Datum und Uhrzeit des Auftretens
- Dienst, der das Vorkommen protokolliert hat
- Kategorie und Name der Aktivität (*Was*) 
- Status der Aktivität (Erfolg oder Fehler)
- Ziel
- Initiator/Akteur einer Aktivität (Wer)

![Überwachungsprotokolle](./media/concept-audit-logs/listview.png "Überwachungsprotokolle")

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Überwachungsspalten](./media/concept-audit-logs/columns.png "Überwachungsspalten")

Sie können dann weitere Felder anzeigen oder Felder entfernen, die bereits angezeigt werden.

![Felder entfernen](./media/concept-audit-logs/columnselect.png "Felder entfernen")

Wählen Sie in der Listenansicht ein Element aus, um ausführlichere Informationen zu erhalten.

![Element auswählen](./media/concept-audit-logs/details.png "Element auswählen")

## <a name="filtering-audit-logs"></a>Filtern von Überwachungsprotokollen

Sie können die Überwachungsdaten in den folgenden Feldern filtern:

- Dienst
- Category
- Aktivität
- Status
- Ziel
- Initiiert von (Akteur)
- Datumsbereich

![Filterobjekt](./media/concept-audit-logs/filter.png "Filter-Objekt")

Bei Verwendung des Filters **Dienst** können Sie in einer Dropdownliste die folgenden Dienste auswählen:

- All
- AAD Management UX
- Zugriffsüberprüfungen
- Kontobereitstellung
- Anwendungsproxy
- Authentifizierungsmethoden
- B2C
- Bedingter Zugriff
- Kernverzeichnis
- Berechtigungsverwaltung
- Hybridauthentifizierung
- Schutz der Identität (Identity Protection)
- Invited Users (Eingeladene Benutzer)
- MIM Service (MIM-Dienst)
- MyApps
- PIM
- Self-Service-Gruppenverwaltung
- Self-Service-Kennwortverwaltung
- Terms of Use (Nutzungsbedingungen)

Bei Verwendung des Filters **Kategorie** können Sie eine der folgenden Filteroptionen auswählen:

- All
- AdministrativeUnit
- ApplicationManagement
- Authentifizierung
- Authorization
- Kontakt
- Sicherungsmedium
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Bezeichnung
- Andere
- PermissionGrantPolicy
- Richtlinie
- ResourceManagement
- RoleManagement
- UserManagement

Der Filter **Aktivität** basiert auf der getroffenen Auswahl für die Kategorie und den und Aktivitätsressourcentyp. Sie können entweder eine bestimmte Aktivität verwenden oder alle auswählen. 

Sie können die Liste aller Überwachungsaktivitäten mithilfe der Graph-API abrufen: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Mit dem Filter **Status** können Sie eine Filterung basierend auf dem Status eines Überprüfungsvorgangs durchführen. Folgende Statuswerte sind möglich:

- All
- Erfolg
- Fehler

Bei Verwendung des Filters **Ziel** können Sie anhand des Beginns des Namens oder des Benutzerprinzipalnamens (User Principal Name, UPN) ein bestimmtes Ziel suchen. Bei dem Zielnamen und dem UPN wird die Groß- und Kleinschreibung berücksichtigt. 

Bei Verwendung des Filters **Initiiert von** können Sie definieren, womit der Name eines Akteurs oder ein Benutzerprinzipalname (UPN) beginnt. Bei dem Namen und dem UPN wird die Groß- und Kleinschreibung berücksichtigt.

Mit dem Filter **Datumsbereich** können Sie einen Zeitrahmen für die zurückgegebenen Daten festlegen.  
Mögliche Werte:

- 7 Tage
- 24 Stunden
- Benutzerdefiniert

Beim Auswählen eines benutzerdefinierten Zeitraums können Sie eine Startzeit und eine Endzeit konfigurieren.

Sie können die gefilterten Daten (bis zu 250.000 Datensätze) auch herunterladen, indem Sie die Schaltfläche **Herunterladen** auswählen. Sie können die Protokolle im CSV- oder JSON-Format herunterladen. Die Anzahl von Datensätzen, die Sie herunterladen können, ist durch die [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md) eingeschränkt.

![Herunterladen von Daten](./media/concept-audit-logs/download.png "Herunterladen von Daten")



## <a name="microsoft-365-activity-logs"></a>Microsoft 365-Aktivitätsprotokolle

Sie können Microsoft 365-Aktivitätsprotokolle im [Microsoft 365 Admin Center](/office365/admin/admin-overview/about-the-admin-center) anzeigen. Obwohl Microsoft 365- und Azure AD-Aktivitätsprotokolle einen Großteil der Verzeichnisressourcen gemeinsam nutzen, bietet nur das Microsoft 365 Admin Center eine vollständige Ansicht der Microsoft 365-Aktivitätsprotokolle. 

Mithilfe der [Office 365-Verwaltungs-APIs](/office/office-365-management-api/office-365-management-apis-overview) können Sie auch programmgesteuert auf die Microsoft 365-Aktivitätsprotokolle zugreifen.

> [!NOTE]
> Die meisten eigenständigen oder kombinierten Microsoft 365-Abonnements weisen Back-End-Abhängigkeiten von einigen Subsystemen innerhalb der Microsoft 365-Rechenzentrumsgrenze auf. Aufgrund dieser Abhängigkeiten ist das Rückschreiben einiger Informationen erforderlich, um die Verzeichnisse synchron zu halten und ein problemloses Onboarding in einem Abonnement-Opt-In für Exchange Online zu ermöglichen. Für dieses Rückschreiben werden in Überwachungsprotokolleinträgen Aktionen angezeigt, die von „Microsoft Substrate Management“ durchgeführt wurden. Diese Überwachungsprotokolleinträge beziehen sich auf Vorgänge zum Erstellen/Aktualisieren/Löschen, die von Exchange Online für Azure AD ausgeführt wurden. Die Einträge dienen lediglich zur Information und erfordern keine Aktion.

## <a name="next-steps"></a>Nächste Schritte

- [Referenz zu Überwachungsaktivitäten von Azure AD](reference-audit-activities.md)
- [Referenz zur Aufbewahrung von Azure AD-Protokollen](reference-reports-data-retention.md)
- [Latenzen bei Azure Active Directory-Berichten](reference-reports-latencies.md)
- [Unbekannte Akteure in Überwachungsberichten](/troubleshoot/azure/active-directory/unknown-actors-in-audit-reports)

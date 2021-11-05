---
title: Durchführen einer Zugriffsüberprüfung von Gruppen und Anwendungen – Azure AD
description: Erfahren Sie, wie Sie eine Gruppenmitglieder oder den Anwendungszugriff betreffende Zugriffsüberprüfung in Azure Active Directory-Zugriffsüberprüfungen durchführen.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29dc8e93af91f81a3d9b9426138e8f301c9528f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052253"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Durchführen einer Zugriffsüberprüfung für Gruppen und Anwendungen in Azure AD-Zugriffsüberprüfungen
 
Als Administrator [erstellen Sie eine Zugriffsüberprüfung für Gruppen oder Anwendungen](create-access-review.md), und Prüfer [führen dann die Zugriffsüberprüfung durch](perform-access-review.md). In diesem Artikel wird beschrieben, wie Sie die Ergebnisse der Zugriffsüberprüfung anzeigen und anwenden.
 
[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]
 
## <a name="prerequisites"></a>Voraussetzungen
 
- Azure AD Premium P2
- Die Rolle des globalen Administrators, Benutzeradministrators oder Identity Governance-Administrators wird benötigt, um den Zugriff auf Überprüfungen für Gruppen und Anwendungen zu verwalten. Globale Administratoren und Administratoren für privilegierte Rollen können Überprüfungen von Gruppen verwalten, denen Rollen zugewiesen werden können. Weitere Informationen finden Sie unter [Verwenden von Azure AD-Gruppen zum Verwalten von Rollenzuweisungen](../roles/groups-concept.md).
- Sicherheitsleseberechtigte haben Lesezugriff.
 
Weitere Informationen finden Sie unter [Lizenzanforderungen](access-reviews-overview.md#license-requirements).

 
## <a name="view-the-status-of-an-access-review"></a>Anzeigen des Status der Zugriffsüberprüfung
 
Sie können den Fortschritt von Zugriffsüberprüfungen nachverfolgen, sobald diese abgeschlossen sind.
 
1. Melden Sie sich beim Azure-Portal an, und öffnen Sie die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
1. Klicken Sie im linken Menü auf **Zugriffsüberprüfungen**.
 
1. Klicken Sie in der Liste auf eine Zugriffsüberprüfung.
 
 
    Auf der Seite **Übersicht** können Sie den Status der **aktuellen** Instanz der Überprüfung verfolgen. Wenn zu diesem Zeitpunkt keine aktive Instanz geöffnet ist, werden Informationen zur vorherigen Instanz angezeigt. Zugriffsrechte werden im Verzeichnis erst geändert, wenn die Überprüfung abgeschlossen ist.
 
     ![Überprüfen der Gruppe „All company“](./media/complete-access-review/all-company-group.png)
 
    Alle Blätter unter **Aktuell** sind nur während der Dauer der jeweiligen Überprüfungsinstanz einsehbar. 
    > [!NOTE]
    > Während die **aktuelle** Zugriffsüberprüfung nur Informationen zur aktiven Überprüfungsinstanz enthält, können Sie **im Abschnitt Geplante Überprüfung** Informationen zu Überprüfungen erhalten, die noch in der **Reihe** stattfinden werden.
 
    Die Seite „Ergebnisse“ bietet weitere Informationen zu allen Benutzer*innen, die in der Instanz überprüft werden, einschließlich der Möglichkeit, Ergebnisse zu beenden, zurückzusetzen und herunterzuladen.
 
    ![Überprüfen des Gastzugriffs für Microsoft 365-Gruppen](./media/complete-access-review/all-company-group-results.png)
 
    Wenn Sie eine Zugriffsüberprüfung für den Gastzugriff für Microsoft 365-Gruppen anzeigen, werden auf dem Blatt „Übersicht“ alle Gruppen in der Überprüfung aufgelistet. 
   
    ![Überprüfen des Gastzugriffs für Microsoft 365-Gruppen](./media/complete-access-review/review-guest-access-across-365-groups.png)
 
    Klicken Sie auf eine Gruppe, um den Fortschritt der Überprüfung für diese Gruppe anzuzeigen. Es gibt auch die Möglichkeit zum Beenden, Zurücksetzen, Anwenden und Löschen.
 
   ![Details zur Überprüfung des Gastzugriffs für Microsoft 365-Gruppen](./media/complete-access-review/progress-group-review.png)
 
1. Klicken Sie auf die Schaltfläche **Beenden**, wenn Sie eine Zugriffsüberprüfung beenden möchten, bevor dafür das geplante Enddatum erreicht wurde.
 
    Nachdem Sie eine Überprüfung beendet haben, können Prüfer dafür keine Antworten mehr abgeben. Eine Überprüfung kann nicht neu gestartet werden, nachdem sie beendet wurde.
 
1. Wenn Sie die Zugriffsüberprüfung nicht mehr benötigen, können Sie sie löschen, indem Sie auf die Schaltfläche **Löschen** klicken.
 
## <a name="retrieve-the-results"></a>Abrufen der Ergebnisse
 
Klicken Sie auf die Seite **Ergebnisse**, um die Ergebnisse für eine Überprüfung anzuzeigen. Geben Sie im Suchfeld den Anzeigenamen oder Benutzerprinzipalnamen eines Benutzers ein, dessen Zugriff überprüft wurde, um nur den Zugriff dieses Benutzers anzuzeigen.
 
![Abrufen der Ergebnisse für eine Zugriffsüberprüfung](./media/complete-access-review/retrieve-results.png) 
 
 
Klicken Sie zum Anzeigen der Ergebnisse einer abgeschlossenen Instanz einer Serienzugriffsüberprüfung auf **Ausführungsverlauf**. Wählen Sie anschließend basierend auf dem Start- und Enddatum der Instanz die spezifische Instanz in der Liste mit den abgeschlossenen Zugriffsüberprüfungsinstanzen aus. Die Ergebnisse dieser Instanz können auf der Seite **Ergebnisse** abgerufen werden. Durch wiederholte Zugriffsüberprüfungen erhalten Sie einen konstanten Überblick über den Zugriff auf Ressourcen, die möglicherweise häufiger als einmalige Zugriffsüberprüfungen aktualisiert werden müssen.
 
Klicken Sie auf die Schaltfläche **Herunterladen**, um die Ergebnisse einer Zugriffsüberprüfung abrufen möchten, egal ob diese abgeschlossen ist oder gerade ausgeführt wird. Die CSV-Datei mit den Ergebnissen kann in Excel oder in anderen Programmen angezeigt werden, die mit UTF-8 codierte CSV-Dateien öffnen können.


 

## <a name="apply-the-changes"></a>Übernehmen von Änderungen
 
Wenn **Ergebnisse automatisch auf Ressource anwenden** basierend auf Ihrer Auswahl unter **Einstellungen nach Abschluss** aktiviert wurde, wird die automatische Anwendung durchgeführt, nachdem eine Überprüfungsinstanz abgeschlossen ist oder wenn Sie die Überprüfung manuell beenden.
 
Wenn **Ergebnisse automatisch auf Ressource anwenden** für die Überprüfung nicht aktiviert wurde, navigieren Sie unter **Serien** zu **Überprüfungsverlauf**, nachdem die Überprüfungsdauer endet oder die Überprüfung vorzeitig beendet wurde. Klicken Sie auf die Instanz der Überprüfung, die Sie anwenden möchten.
 
![Anwenden von Änderungen der Zugriffsüberprüfung](./media/complete-access-review/apply-changes.png)
 
Klicken Sie auf **Übernehmen**, um die Änderungen manuell zu übernehmen. Wenn der Zugriff eines Benutzers in der Überprüfung verweigert wurde, entfernt Azure AD dessen Mitgliedschaft oder Anwendungszuweisung, nachdem Sie auf **Anwenden** geklickt haben.
 
![Schaltfläche zum Übernehmen der Änderungen der Zugriffsüberprüfung](./media/complete-access-review/apply-changes-button.png)
 
Der Status der Überprüfung ändert sich von **Abgeschlossen** über Zwischenzustände wie **Wird angewandt** schließlich in den Status **Ergebnis angewendet**. Erwartungsgemäß sollten abgelehnte Benutzer (sofern vorhanden) innerhalb weniger Minuten aus der Gruppenmitgliedschaft oder Anwendungszuweisung entfernt werden.
 
Das manuelle oder automatische Übernehmen von Ergebnissen hat keine Auswirkung auf eine Gruppe, die aus einem lokalen Verzeichnis stammt. Wenn Sie eine Gruppe ändern möchten, die aus einem lokalen Verzeichnis stammt, laden Sie die Ergebnisse herunter, und wenden Sie diese Änderungen auf die Darstellung der Gruppe im Verzeichnis an.

> [!NOTE]
> Auf einige abgelehnte Benutzer*innen können keine Ergebnisse angewendet werden. Zu den Szenarios, in denen dies passieren kann, gehören:
> - Überprüfen von Mitgliedern einer synchronisierten lokalen Windows-AD-Gruppe: Wenn die Gruppe von einem lokalen Windows-AD synchronisiert wird, kann die Gruppe nicht in Azure AD verwaltet werden, und daher kann die Mitgliedschaft nicht geändert werden.
> - Überprüfen einer Ressource (Rolle, Gruppe, Anwendung) mit zugewiesenen geschachtelten Gruppen: Für Benutzer*innen, die über eine Mitgliedschaft durch eine geschachtelte Gruppe verfügen, wird die Mitgliedschaft in der geschachtelten Gruppe nicht entfernt, und daher behalten sie den Zugriff auf die Ressource, die überprüft wird.
> - „Benutzer nicht gefunden“ oder andere Fehler können auch dazu führen, dass ein Anwenden des Ergebnisses nicht unterstützt wird.
 

## <a name="actions-taken-on-denied-guest-users-in-an-access-review"></a>Aktionen für abgelehnte Gastbenutzer*innen in einer Zugriffsüberprüfung
 
Bei der Erstellung der Überprüfung kann der Ersteller zwischen zwei Optionen für abgelehnte Gastbenutzer*innen in einer Zugriffsüberprüfung auswählen. 
 - Der Zugriff abgelehnter Gastbenutzer*innen auf die Ressource kann entfernt werden. Dies ist die Standardoption.
 - Abgelehnte Gastbenutzer*innen können für 30 Tage an der Anmeldung gehindert und dann von dem Mandanten gelöscht werden. Während des Zeitraums von 30 Tagen kann den Gastbenutzer*innen der Zugriff auf den Mandanten durch Administrator*innen wieder gewährt werden. Wenn den Gastbenutzer*innen nach Abschluss der 30 Tage nicht wieder Zugriff auf die Ressource gewährt wurde, werden sie dauerhaft von dem Mandanten entfernt. Darüber hinaus kann ein globaler Administrator über das Azure Active Directory-Portal explizit [einen kürzlich gelöschten Benutzer endgültig löschen](../fundamentals/active-directory-users-restore.md), bevor dieser Zeitraum abgelaufen ist. Wurden Benutzer*innen endgültig gelöscht, werden deren Daten aus aktiven Zugriffsüberprüfungen entfernt. Überwachungsinformationen zu gelöschten Benutzern verbleiben im Überwachungsprotokoll.


## <a name="next-steps"></a>Nächste Schritte
 
- [Verwalten von Zugriffsüberprüfungen](manage-access-review.md) 
- [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md)
- [Erstellen einer Zugriffsüberprüfung von Benutzern in der Azure AD-Administratorrolle](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)


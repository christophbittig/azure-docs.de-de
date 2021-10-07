---
title: Behandeln von Problemen mit Azure Data Share
description: Hier erfahren Sie, wie Sie beim Erstellen oder Empfangen von Datenfreigaben mit Azure Data Share Probleme mit Einladungen und Fehler behandeln.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 09/10/2021
ms.openlocfilehash: da746d14d891c875c9bed49c94e8b408d956e29e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811249"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Behandeln allgemeiner Probleme in Azure Data Share 

In diesem Artikel erfahren Sie, wie Sie allgemeine Probleme in Azure Data Share behandeln. 

## <a name="azure-data-share-invitations"></a>Azure Data Share-Einladungen 

In einigen Fällen wird möglicherweise eine leere Einladungsliste angezeigt, wenn neue Benutzer in einer E-Mail-Einladung auf **Einladung annehmen** klicken. Das Problem kann eine der folgenden Ursachen haben:

* **Der Azure Data Share-Dienst ist im Azure-Mandanten nicht als Ressourcenanbieter eines Azure-Abonnements registriert.** Dieses Problem tritt auf, wenn Ihr Azure-Mandant keine Data Share-Ressource enthält. 

    Wenn Sie eine Azure Data Share-Ressource erstellen, wird der Ressourcenanbieter in Ihrem Azure-Abonnement automatisch registriert. Sie können den Data Share-Dienst manuell registrieren, indem Sie die folgenden Schritte ausführen. Sie müssen über die [Rolle „Mitwirkender“](../role-based-access-control/built-in-roles.md#contributor) für das Azure-Abonnement verfügen, um diese Schritte ausführen zu können. 

    1. Navigieren Sie im Azure-Portal zu **Abonnements**.
    1. Wählen Sie das Abonnement aus, das Sie zum Erstellen der Azure Data Share-Ressource verwenden möchten.
    1. Er wählt **Ressourcenanbieter** aus.
    1. Suchen Sie nach **Microsoft.DataShare**.
    1. Wählen Sie **Registrieren**.

* **Die Einladung wird nicht an Ihre E-Mail-Adresse für die Anmeldung bei Azure, sondern an Ihren E-Mail-Alias gesendet.** Wenn Sie den Azure Data Share-Dienst bereits registriert oder eine Data Share-Ressource im Azure-Mandanten erstellt haben, aber die Einladung weiterhin nicht angezeigt wird, ist möglicherweise Ihr E-Mail-Alias als Empfänger festgelegt. Wenden Sie sich an Ihren Datenanbieter, und vergewissern Sie sich, dass die Einladung an Ihre E-Mail-Adresse für die Anmeldung bei Azure und nicht an Ihren E-Mail-Alias gesendet wird.

* **Die Einladung wurde bereits akzeptiert.** Der Link in der E-Mail führt Sie zur Seite der **Data Share-Einladungen** im Azure-Portal. Auf dieser Seite werden nur ausstehende Einladungen aufgeführt. Bereits akzeptierte Einladungen werden auf der Seite nicht angezeigt. Wechseln Sie zu der Data Share-Ressource, mit der Sie die Einladung akzeptiert haben. Dort können Sie die empfangenen Freigaben anzeigen und die Einstellung für den Azure Data Explorer-Zielcluster konfigurieren.

* **Sie sind Gastbenutzer*in des Mandanten.** Wenn Sie Gastbenutzer*in des Mandanten sind, müssen Sie Ihre E-Mail-Adresse für den Mandanten bestätigen, bevor Sie die Einladung anzeigen. Nach der Überprüfung ist sie zwölf Monate lang gültig. 

## <a name="creating-and-receiving-shares"></a>Erstellen und Empfangen von Freigaben

Wenn Sie eine neue Freigabe erstellen, Datasets hinzufügen oder Datasets zuordnen, kann es zur Anzeige der folgenden Fehler kommen:

* „Fehler beim Hinzufügen von Datasets.“
* „Fehler beim Zuordnen von Datasets.“
* „Data Share-Ressource ‚x‘ kann kein Zugriff auf ‚y‘ gewährt werden.“
* „Sie besitzen nicht die erforderlichen Berechtigungen für ‚x‘.“
* „Für mindestens eine der ausgewählten Ressourcen konnten keine Schreibberechtigungen für das Azure Data Share-Konto hinzugefügt werden.“

Wenn Sie keine ausreichenden Berechtigungen für den Azure-Datenspeicher besitzen, wird möglicherweise einer dieser Fehler angezeigt. Weitere Informationen finden Sie unter [Rollen und Anforderungen](concepts-roles-permissions.md). 

Sie benötigen Schreibberechtigungen, um Daten aus einem Azure-Datenspeicher freizugeben oder zu empfangen. Diese Berechtigung ist üblicherweise in der Rolle „Mitwirkender“ enthalten. 

Wenn Sie zum ersten Mal Daten aus dem Azure-Datenspeicher freigeben oder aus diesem empfangen, benötigen Sie außerdem die Berechtigung *Microsoft.Authorization/role assignments/write*. Diese Berechtigung ist üblicherweise in der Rolle „Besitzer“ enthalten. Selbst wenn Sie die Azure Data Store-Ressource erstellt haben, sind Sie nicht automatisch deren Besitzer. 

Wenn Sie über die geeigneten Berechtigungen verfügen, gewährt der Azure Data Share-Dienst der verwalteten Identität der Data Share-Ressource automatisch Zugriff auf den Datenspeicher. Dies kann einige Minuten dauern. Wenn aufgrund dieser Verzögerung ein Fehler auftritt, versuchen Sie es nach einigen Minuten noch mal.

Für eine SQL-basierte Freigabe sind zusätzliche Berechtigungen erforderlich. Informationen zu den Voraussetzungen finden Sie im Artikel zur [Freigabe aus SQL-Quellen](how-to-share-from-sql.md).

## <a name="snapshots"></a>Momentaufnahmen
Es kann verschiedene Gründe haben, dass eine Momentaufnahme nicht erfolgreich erstellt werden kann. Eine ausführliche Fehlermeldung können Sie öffnen, indem Sie die Startzeit der Momentaufnahme und dann den Status jedes Datasets auswählen. 

Fehler bei Momentaufnahmen haben häufig diese Ursachen:

* Data Share fehlt die Berechtigung zum Lesen aus dem Quelldatenspeicher oder zum Schreiben in den Zieldatenspeicher. Weitere Informationen finden Sie unter [Rollen und Anforderungen](concepts-roles-permissions.md). Wenn Sie zum ersten Mal eine Momentaufnahme erstellen, benötigt die Data Share-Ressource möglicherweise einige Minuten, um Zugriff auf den Azure-Datenspeicher zu erhalten. Warten Sie einige Minuten, und versuchen Sie es noch mal.
* Die Data Share-Verbindung mit dem Quell- oder Zieldatenspeicher wird durch eine Firewall blockiert.
* Ein freigegebenes Dataset, ein Quell- oder ein Zieldatenspeicher wurde gelöscht.

Für Speicherkonten kann es zu Fehlern bei der Momentaufnahme kommen, weil zum Zeitpunkt der Momentaufnahmeerstellung eine Datei in der Quelle aktualisiert wird. Als Ergebnis wird möglicherweise eine 0-Byte-Datei im Ziel angezeigt. Nach der Aktualisierung in der Quelle sollten die Momentaufnahmen erfolgreich erstellt werden können.

Für SQL-Quellen kann es aus anderen Gründen zu Fehlern bei Momentaufnahmen kommen:

* Das SQL-Quellskript oder das SQL-Zielskript zum Gewähren der Data Share-Berechtigung wurde nicht ausgeführt. Möglicherweise wird das Skript auch für Azure SQL-Datenbank oder Azure Synapse Analytics (vormals Azure SQL Data Warehouse) unter Verwendung der SQL-Authentifizierung anstelle der Azure Active Directory-Authentifizierung ausgeführt. Sie können die folgende Abfrage ausführen, um zu überprüfen, ob das Data Share-Konto über die richtige Berechtigung für die SQL-Datenbank verfügt. Für die SQL-Quelldatenbank sollte das Abfrageergebnis angeben, dass das Data Share-Konto über die Rolle *db_datareader* verfügt. Für die SQL-Zieldatenbank sollte das Abfrageergebnis angeben, dass das Data Share-Konto über die Rollen *db_datareader*, *db_datawriter* und *db_dlladmin* verfügt.

    ```sql
        SELECT DP1.name AS DatabaseRoleName,
        isnull (DP2.name, 'No members') AS DatabaseUserName
        FROM sys.database_role_members AS DRM
        RIGHT OUTER JOIN sys.database_principals AS DP1
        ON DRM.role_principal_id = DP1.principal_id
        LEFT OUTER JOIN sys.database_principals AS DP2
        ON DRM.member_principal_id = DP2.principal_id
        WHERE DP1.type = 'R'
        ORDER BY DP1.name; 
     ``` 

* Der Quelldatenspeicher oder der SQL-Zieldatenspeicher wurde angehalten.
* Der Prozess für Momentaufnahmen oder der Zieldatenspeicher unterstützt keine SQL-Datentypen. Weitere Informationen finden Sie im Artikel zur [Freigabe aus SQL-Quellen](how-to-share-from-sql.md#supported-data-types).
* Der Quelldatenspeicher oder SQL-Zieldatenspeicher ist durch andere Prozesse gesperrt. Durch Azure Data Share erfolgt keine Sperrung dieser Datenspeicher. Vorhandene Sperren für diese Datenspeicher können jedoch zu Fehlern bei der Momentaufnahmeerstellung führen.
* Auf die SQL-Zieltabelle wird mit einer Fremdschlüsseleinschränkung verwiesen. Wenn bei der Momentaufnahmeerstellung eine gleichnamige Zieltabelle vorhanden ist, löscht Azure Data Share die Tabelle und erstellt eine neue Tabelle. Wenn auf die SQL-Zieltabelle mit einer Fremdschlüsseleinschränkung verwiesen wird, kann die Tabelle nicht gelöscht werden.
* Die CSV-Zieldatei wird generiert, aber die Daten können nicht in Excel gelesen werden. Dieses Problem tritt möglicherweise auf, wenn die SQL-Quelltabelle Daten mit Sonderzeichen umfasst. Wählen Sie in Excel die Registerkarte **Daten abrufen** und dann die CSV-Datei aus. Wählen Sie den Dateiursprung **65001: Unicode (UTF-8)** aus, und laden Sie anschließend die Daten.

## <a name="update-snapshot-schedule"></a>Aktualisieren des Momentaufnahmezeitplans
Nachdem der Datenanbieter den Momentaufnahmezeitplan für die gesendete Freigabe aktualisiert hat, muss der Datenconsumer den früheren Momentaufnahmezeitplan deaktivieren und dann den aktualisierten Momentaufnahmezeitplan für die empfangene Freigabe erneut aktivieren. Der Momentaufnahmezeitplan wird in UTC gespeichert und auf der Benutzeroberfläche als lokale Computerzeit angezeigt. Er wird nicht automatisch an die Sommer- bzw. Winterzeit angepasst.  

## <a name="in-place-sharing"></a>Direkte Freigabe
Bei der Datasetzuordnung kann für Azure Data Explorer-Cluster aus folgenden Gründen ein Fehler auftreten:

* Der Benutzer hat keine *Schreibberechtigung* für den Azure Data Explorer-Cluster. Diese Berechtigung ist üblicherweise in der Rolle „Mitwirkender“ enthalten. 
* Der Azure Data Explorer-Quell- oder Zielcluster wurde angehalten.
* Der Azure Data Explorer-Quellcluster ist EngineV2 und der Zielcluster EngineV3 oder umgekehrt. Die Freigabe zwischen Azure Data Explorer-Clustern mit unterschiedlichen Engine-Versionen wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Freigeben von Daten finden Sie im Tutorial zur [Datenfreigabe](share-your-data.md). 

Weitere Informationen zum Datenempfang finden Sie im Tutorial [Akzeptieren und Empfangen von Daten](subscribe-to-data-share.md).
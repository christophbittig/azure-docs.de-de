---
title: Wiederherstellen eines gelöschten Azure Database for MySQL-Servers
description: In diesem Artikel wird beschrieben, wie Sie einen gelöschten Server in Azure Database for MySQL über das Azure-Portal wiederherstellen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 9ddf3885931e527e4fd82037d9e82693fb6afa5f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639684"
---
# <a name="restore-a-deleted-azure-database-for-mysql-server"></a>Wiederherstellen eines gelöschten Azure Database for MySQL-Servers

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Wenn ein Server gelöscht wird, kann die Datenbankserversicherung bis zu fünf Tage im Dienst aufbewahrt werden. Auf die Datenbanksicherung kann nur über das Azure-Abonnement zugegriffen werden, zu dem der Server ursprünglich gehörte. Und nur über dieses Abonnement kann die Datenbanksicherung auch wiederhergestellt werden. Die folgenden empfohlenen Schritte können ausgeführt werden, um eine gelöschte MySQL-Serverressource innerhalb von 5 Tagen ab dem Zeitpunkt wiederherzustellen, zu dem der Server gelöscht wurde. Die empfohlenen Schritte funktionieren nur, wenn die Sicherung für den Server weiterhin verfügbar ist und nicht aus dem System gelöscht wurde. 

## <a name="pre-requisites"></a>Voraussetzungen
Zum Wiederherstellen eines gelöschten Azure Database for MySQL-Servers benötigen Sie Folgendes:
- Name des Azure-Abonnements, das den ursprünglichen Server gehostet hat
- Speicherort, an dem der Server erstellt wurde

## <a name="steps-to-restore"></a>Schritte zum Wiederherstellen

1. Wechseln Sie im Azure-Portal auf dem Blatt „Überwachen“ zum [Aktivitätsprotokoll](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). 

2. Klicken Sie im „Aktivitätsprotokoll“ wie hier gezeigt auf **Filter hinzufügen**, und legen Sie die folgenden Filter fest für 

    - **Abonnement** = Ihr Abonnement, das den gelöschten Server hostet
    - **Ressourcentyp** = Azure Database for MySQL-Server (Microsoft.DBforMySQL/servers) 
    - **Vorgang** = MySQL-Server löschen (Microsoft.DBforMySQL/servers/delete) 
 
     [![Für Löschen des MySQL-Servers gefiltertes Aktivitätsprotokoll](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Doppelklicken Sie auf das „MySQL-Server löschen“-Ereignis, klicken Sie auf die Registerkarte „JSON“, und notieren Sie sich die Attribute „resourceId“ und „submissionTimestamp“ in der JSON-Ausgabe. Die „resourceId“ weist das folgende Format auf: „/Abonnements/ffffffff-ffff-ffff-ffff-ffffffffffff/Ressourcengruppe/Zielressourcengruppe/Anbieter/Microsoft.DBforMySQL/Server/gelöschterServer“.
 
 4. Wechseln Sie zur Seite [Server – Erstellen](/rest/api/mysql/singleserver/servers(2017-12-01)/create), klicken Sie auf die grün hervorgehobene Registerkarte „Ausprobieren“, und melden Sie sich mit Ihrem Azure-Konto an.
 
 5. Geben Sie „resourceGroupName“, „serverName“ (gelöschter Servername) und „subscriptionId“ an, abgeleitet aus dem in Schritt 3 aufgezeichneten resourceId-Attribut, während „api-version“, wie im Bild gezeigt, bereits eingetragen ist.
 
     [![Erstellen eines Servers mit der REST-API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Scrollen Sie im Abschnitt „Anforderungstext“ nach unten, und fügen Sie Folgendes ein:
 
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerId": "resourceId"
            }
    }
    ```
7. Ersetzen Sie im vorstehenden Anforderungstext die folgenden Werte:
   * „Dropped server Location“ (Speicherort des gelöschten Servers) durch die Azure-Region, in der der gelöschte Server ursprünglich erstellt wurde
   * „submissionTimestamp“ und „resourceId“ durch die in Schritt 3 erfassten Werte 
   * Geben Sie für „restorePointinTime“ den Wert „submissionTimestamp“ minus **15 Minuten** an, um sicherzustellen, dass der Befehl nicht fehlerhaft ist.
   
8. Wenn Sie „Antwortcode 201“ oder 202 sehen, wurde die Wiederherstellungsanforderung erfolgreich übermittelt. 

9. Die Servererstellung kann abhängig von der Datenbankgröße und den Computeressourcen, die auf dem ursprünglichen Server bereitgestellt werden, eine Weile dauern. Der Wiederherstellungsstatus kann über das Aktivitätsprotokoll überwacht werden durch filtern nach 
   - **Abonnement** = Ihr Abonnement
   - **Ressourcentyp** = Azure Database for MySQL-Server (Microsoft.DBforMySQL/servers) 
   - **Vorgang**  = Update MySQL Server Create

## <a name="next-steps"></a>Nächste Schritte
- Wenn Sie versuchen, einen Server innerhalb von fünf Tagen wiederherzustellen, und nach dem genauen Ausführen der zuvor beschriebenen Schritte immer noch eine Fehlermeldung angezeigt wird, öffnen Sie einen Supportfall, um Unterstützung zu erhalten. Wenn Sie versuchen, einen gelöschten Server nach fünf Tagen wiederherzustellen, wird ein Fehler erwartet, weil die Sicherungsdatei nicht gefunden werden kann. Eröffnen Sie in diesem Szenario kein Supportticket. Das Supportteam kann keine Unterstützung bieten, wenn die Sicherung aus dem System gelöscht worden ist. 
- Um das versehentliche Löschen von Servern zu verhindern, sollten Sie unbedingt [Ressourcensperren](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222) verwenden.

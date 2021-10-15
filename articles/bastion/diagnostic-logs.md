---
title: Aktivieren und Verwenden von Azure Bastion-Ressourcenprotokollen
description: Erfahren Sie, wie Sie Azure Bastion-Diagnoseprotokolle aktivieren und verwenden.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 4803ddf4c3d570e9bd52832ec4120c42972003eb
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458205"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Aktivieren und Verwenden von Bastion-Ressourcenprotokollen

Wenn Benutzer mithilfe von Azure Bastion eine Verbindung mit Workloads herstellen, können Diagnosedaten der Remotesitzungen von Bastion protokolliert werden. Anschließend können Sie anhand der Diagnose feststellen, welche Benutzer wann und von wo aus eine Verbindung mit welchen Workloads hergestellt haben, und weitere relevante Protokollinformationen einsehen. Um die Diagnose verwenden zu können, müssen Sie Diagnoseprotokolle in Azure Bastion aktivieren. Dieser Artikel unterstützt Sie dabei, Diagnoseprotokolle zu aktivieren und anschließend die Protokolle anzuzeigen.

## <a name="enable-the-resource-log"></a><a name="enable"></a>Aktivieren des Ressourcenprotokolls

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu ihrer Azure Bastion-Ressource, und wählen Sie auf der Azure Bastion-Seite **Diagnoseeinstellungen** aus.

   ![Screenshot: Seite „Diagnoseeinstellungen“](./media/diagnostic-logs/1diagnostics-settings.png)
2. Wählen Sie **Diagnoseeinstellungen** und dann **+ Diagnoseeinstellung hinzufügen** aus, um ein Ziel für die Protokolle hinzuzufügen.

   ![Screenshot: Seite „Diagnoseeinstellungen“ mit der ausgewählten Schaltfläche „Diagnoseeinstellung hinzufügen“](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Wählen Sie auf der Seite **Diagnoseeinstellungen** den Typ des Speicherkontos aus, das zum Speichern der Diagnoseprotokolle verwendet werden soll.

   ![Screenshot: Seite „Diagnoseeinstellungen“, auf der der Abschnitt zum Auswählen eines Speicherorts hervorgehoben ist](./media/diagnostic-logs/3add-storage-account.png)
4. Nachdem Sie die Einstellungen festgelegt haben, sieht das Ergebnis in etwa wie im folgenden Beispiel aus:

   ![Beispieleinstellungen](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Anzeigen des Diagnoseprotokolls

Um auf die Diagnoseprotokolle zuzugreifen, können Sie direkt das Speicherkonto verwenden, das Sie beim Aktivieren der Diagnoseeinstellungen angegeben haben.

1. Navigieren Sie zu ihrer Speicherkontoressource und dann zu **Container**. Sie sehen, dass im Blobcontainer Ihres Speicherkontos das **insights-logs-bastionauditlogs**-Blob erstellt wurde.

   ![Diagnoseeinstellungen](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Wenn Sie innerhalb des Containers navigieren, sehen Sie verschiedene Ordner im Blob. Diese Ordner spiegeln die Ressourcenhierarchie für ihre Azure Bastion-Ressource wider.

   ![Diagnoseeinstellung hinzufügen](./media/diagnostic-logs/2-resource-h.png)
3. Navigieren Sie zur vollständigen Hierarchie der Azure Bastion-Ressource, deren Diagnoseprotokolle Sie aufrufen/anzeigen möchten. „y=“, „m=“, „d=“, „h=“ und „m=“ geben das Jahr, den Monat, den Tag, die Stunde und die Minute der Ressourcenprotokollerstellung an.

   ![Speicherort auswählen](./media/diagnostic-logs/3-resource-location.png)
4. Suchen Sie die von Azure Bastion erstellte JSON-Datei, die die Diagnoseprotokolldaten für den Zeitraum enthält, zu dem Sie navigiert sind.

5. Laden Sie die JSON-Datei aus Ihrem Speicherblobcontainer herunter. Im Folgenden ein Beispieleintrag einer erfolgreichen Anmeldung aus der JSON-Datei zu Referenzzwecken:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "userEmail":"<userAzureAccountEmailAddress>"
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```
   
   Im Folgenden finden Sie ein Beispiel für eine fehlgeschlagene Anmeldung (z. B. aufgrund eines falschen Benutzernamens/Kennworts) aus der JSON-Datei:
   
   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Login Failed",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "userEmail":"<userAzureAccountEmailAddress>"
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```
   
## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).

---
title: Problembehandlung für Synapse Studio
description: Problembehandlung für Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: fd560856ab087727d73317eaef5de01950281db9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399471"
---
# <a name="synapse-studio-troubleshooting"></a>Problembehandlung für Synapse Studio

Dieser Leitfaden zur Problembehandlung enthält Anweisungen dazu, welche Informationen beim Öffnen eines Supporttickets zu Netzwerkkonnektivitätsproblemen bereitgestellt werden müssen. Mit den richtigen Informationen können wir das Problem möglicherweise schneller beheben.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Dienstkonnektivitätsproblem bei serverlosem SQL-Pool

### <a name="symptom-1"></a>Symptom 1

Die Option „Serverloser SQL-Pool“ ist in der Dropdownliste „Verbinden mit“ abgeblendet dargestellt.

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptom 2

Wenn Sie die Abfrage mit „Serverloser SQL-Pool“ ausführen, erhalten Sie die Fehlermeldung „Fehler beim Herstellen einer Verbindung zum Server“.

![Symptom 2](media/troubleshooting-synapse-studio/symptom2.png)
 

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

> [!NOTE] 
>    Die folgenden Schritte zur Problembehandlung sind für Microsoft Edge (Chromium) und Chrome vorgesehen. Sie können andere Browser (z. B. FireFox) mit den gleichen Schritten zur Problembehandlung verwenden, aber das Fenster „Entwicklertool“ kann ein anderes Layout als die hier enthaltenen Screenshots aufweisen. Verwenden Sie nach Möglichkeit NICHT das klassische Microsoft Edge zur Problembehandlung, da es in bestimmten Situationen ungenaue Informationen anzeigen kann.

Öffnen Sie das Fenster „Diagnoseinformationen“, wählen Sie die Schaltfläche „Diagnose herunterladen“ aus. Behalten Sie die heruntergeladenen Informationen für die Fehlerberichterstellung bei. Sie können stattdessen die „Sitzungs-ID“ kopieren und beim Öffnen des Supporttickets anfügen.

![diagnostic-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Wiederholen Sie den Vorgang, den Sie in Synapse Studio ausgeführt haben, um die Problembehandlung zu beginnen.

- Wählen Sie für Symptom 1 die Schaltfläche „Aktualisieren“ rechts neben dem Dropdownmenü „Datenbank verwenden“ auf der Registerkarte „SQL-Skript“ aus, und prüfen Sie, ob „Serverloser SQL-Pool“ angezeigt wird.
- Versuchen Sie bei Symptom 2, die Abfrage erneut auszuführen, um zu prüfen, ob sie erfolgreich ausgeführt wird.

Wenn das Problem weiterhin besteht, drücken Sie F12 in Ihrem Browser, um „Entwicklertools“ (DevTools) zu öffnen.

Wechseln Sie im Fenster „Entwicklertools“ zum Fenster „Netzwerk“. Wählen Sie bei Bedarf auf der Symbolleiste im Fenster „Netzwerk“ die Schaltfläche „Löschen“ aus.
Stellen Sie sicher, dass im Fenster „Netzwerk“ die Option „Cache deaktivieren“ aktiviert ist.

Wiederholen Sie den Vorgang, den Sie in Azure Synapse Studio ausgeführt haben. Möglicherweise werden neue Elemente in der Liste „Netzwerk“ in „Entwicklertools“ angezeigt. Notieren Sie sich die aktuelle Systemzeit, die im Supportticket bereitgestellt werden soll.

![network-panel 1](media/troubleshooting-synapse-studio/network-panel.png)

Suchen Sie das Element, dessen URL-Spalte mit dem folgenden Muster übereinstimmt:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Dabei ist [*A*] Ihr Arbeitsbereichsname und „-ondemand“ könnte „-sqlod“ sein, während [*B*] ein Datenbankname wie „master“ sein sollte. Es sollten höchstens zwei Elemente mit dem gleichen URL-Wert, aber unterschiedlichen Methodenwerten vorhanden sein: OPTIONS und POST. Überprüfen Sie, ob diese beiden Elemente in der Spalte „Status“ den Wert „200“ oder „20x“ aufweisen, wobei „x“ eine beliebige einzelne Ziffer sein könnte.

Wenn eines der Elemente etwas anderes als „20x“ aufweist und:

- Der Status beginnt mit „(failed)“: Verbreitern Sie die Spalte „Status“, oder zeigen Sie mit dem Mauszeiger auf den Statustext, um den vollständigen Text anzuzeigen. Fügen Sie den Text und/oder den Screenshot ein, wenn Sie das Supportticket öffnen.

    ![Statustext](media/troubleshooting-synapse-studio/status-text.png)

    - Wenn ERR_NAME_NOT_RESOLVED angezeigt wird und Sie Ihren Arbeitsbereich innerhalb von 10 Minuten erstellt haben, warten Sie 10 Minuten und versuchen Sie erneut zu prüfen, ob das Problem immer noch besteht.
    - Wenn ERR_INTERNET_DISCONNECTED oder ERR_NETWORK_CHANGED angezeigt wird, deutet dies möglicherweise darauf hin, dass Ihre PC-Netzwerkverbindung Probleme hat. Überprüfen Sie Ihre Netzwerkverbindung, und wiederholen Sie den Vorgang.
    - Wenn ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR oder andere Fehlercodes angezeigt werden, die „SSL“ enthalten, deutet dies möglicherweise darauf hin, dass Ihre lokale SSL-Konfiguration Probleme hat oder dass Ihr Netzwerkadministrator den Zugriff auf den serverlosen SQL-Poolserver blockiert hat. Öffnen Sie ein Supportticket, und fügen Sie den Fehlercode in der Beschreibung ein.
    - Wenn ERR_NETWORK_ACCESS_DENIED angezeigt wird, müssen Sie möglicherweise mit dem Administrator prüfen, ob Ihre lokale Firewallrichtlinie den Zugriff entweder auf die Domäne „*.database.windows.net“ oder den Remoteport 1443 blockiert hat.
    - Versuchen Sie optional, den gleichen Vorgang direkt auf einem anderen Computer und/oder in einer Netzwerkumgebung auszuführen, um ein Problem mit der Netzwerkkonfiguration auf Ihrem PC auszuschließen.

- Status ist „40x“, „50x“ oder weist einen anderen Wert auf. Wählen Sie die Elemente aus, um Details anzuzeigen. Die Details zum Element sollten auf der rechten Seite angezeigt werden. Suchen Sie den Abschnitt „Antwortheader“, und prüfen Sie dann, ob ein Element namens „access-control-allow-origin“ vorhanden ist. Wenn dies der Fall ist, überprüfen Sie, ob es einen der folgenden Werte aufweist:

    - `*` (einzelnes Sternchen)
    - https://web.azuresynapse.net/ (oder ein anderer Wert, mit dem der Text in der Adressleiste des Browsers beginnt)

Wenn der Antwortheader einen der obigen Werte enthält, bedeutet dies, dass die Fehlerinformationen bereits gesammelt worden sein sollten. Sie können bei Bedarf ein Supportticket öffnen und optional den Screenshot der Elementdetails anfügen.

Wenn der Header nicht angezeigt wird oder der Header nicht über einen der oben aufgeführten Werte verfügt, fügen Sie einen Screenshot der Elementdetails an, wenn Sie das Ticket öffnen.

 
![Elementdetails](media/troubleshooting-synapse-studio/item-details.png)
 
Wenn das Problem durch die oben genannten Schritte nicht gelöst werden kann, müssen Sie möglicherweise ein Supportticket öffnen. Wenn Sie Ihr Supportticket übermitteln, geben Sie die „Sitzungs-ID“ oder „Diagnoseinformationen“ an, die am Anfang dieses Leitfadens heruntergeladen wurden.

Wenn Sie das Problem melden, können Sie optional einen Screenshot der Registerkarte „Konsole“ in der „Entwicklertools“ erstellen und an das Supportticket anfügen. Scrollen Sie durch den Inhalt, und erstellen Sie ggf. mehr als einen Screenshot, um die gesamte Nachricht zu erfassen.

![Entwicklertoolkonsole](media/troubleshooting-synapse-studio/developer-tool-console.png)

Wenn Sie Screenshots anfügen, geben Sie die Uhrzeit (oder einen geschätzten Zeitraum) an, zu der Sie die Screenshots erstellt haben. Es wird uns bei der Untersuchung des Problems helfen.

Bestimmte Browser unterstützen die Anzeige von Zeitstempeln auf der Registerkarte „Konsole“. Für Microsoft Edge (Chromium)/Chrome öffnen Sie das Dialogfeld „Einstellungen“ unter „Entwicklertools“, und aktivieren Sie „Zeitstempel anzeigen“ auf der Registerkarte „Einstellungen“.

![Einstellungen der Entwicklertoolkonsole](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Zeitstempel anzeigen](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="notebook-websocket-connection-issue"></a>Problem mit der Notebook-Websocket-Verbindung

### <a name="symptom"></a>Symptom
Fehlermeldung: Ihre Notebookverbindung wurde unerwartet geschlossen. Um die Verbindung wiederherzustellen, führen Sie das Notebook erneut aus. Diagnoseinformationen: websocket_close_error (Korrelations-ID) 

![Problem mit der Notebook-Websocket-Verbindung](media/troubleshooting-synapse-studio/notebook-websocket-connection-issue.png)

### <a name="root-cause"></a>Grundursache: 
Die Notebookausführung hängt vom Herstellen einer WebSocket-Verbindung mit der folgenden URL ab. 
``` 
wss://{workspace}.dev.azuresynapse.net/jupyterApi/versions/1/sparkPools/{spark-pool}/api/kernels/{kernel-id}/channels 
``` 

+ **{workspace}** ist der Name des Synapse-Arbeitsbereichs. 
+ **{spark-pool}** ist der Name des Spark-Pools, an dem Sie gerade arbeiten. 
+ **{kernel-id}** ist eine GUID, die zum Unterscheiden von Notebooksitzungen verwendet wird. 

Beim Einrichten der WebSocket-Verbindung enthält Synapse Studio ein Zugriffstoken (JWT-Bearertoken) im Sec-WebSocket-Protocol-Header der WebSocket-Anforderung. 

In einigen Fällen kann eine WebSocket-Anforderung blockiert werden, oder das JWT-Token im Anforderungsheader wird in Ihrer Netzwerkumgebung ggf. umgeschrieben. Dies führt dazu, dass Synapse Notebook die Verbindung mit unserem Server nicht herstellen und Ihr Notebook ausführen kann. 

### <a name="action"></a>Aktion: 

Versuchen Sie nach Möglichkeit, Ihre Netzwerkumgebung zu wechseln, z. B. innerhalb oder außerhalb des Unternehmensnetzwerks, oder greifen Sie auf einer anderen Arbeitsstation auf Synapse Notebook zu. 

+ Wenn Sie Notebooks auf derselben Arbeitsstation, aber in einer anderen Netzwerkumgebung ausführen können, wenden Sie sich an Ihren Netzwerkadministrator, um herauszufinden, ob die WebSocket-Verbindung blockiert wurde. 

+ Wenn Sie Notebooks auf einer anderen Arbeitsstation, aber in derselben Netzwerkumgebung ausführen können, stellen Sie sicher, dass Sie kein Browser-Plug-In installiert haben, das die WebSocket-Anforderung möglicherweise blockiert. 

Wenden Sie sich andernfalls an Ihren Netzwerkadministrator, und stellen Sie sicher, dass die ausgehenden WebSocket-Anforderungen mit dem folgenden URL-Muster zulässig sind und ihr Anforderungsheader nicht redigiert wird: 

``` 
wss://{workspace}.dev.azuresynapse.net/{path} 
``` 
+ **{workspace}** ist der Name des Synapse-Arbeitsbereichs; 

+ **{path}** gibt einen beliebigen Unterpfad (d. h. Schrägstrichzeichen ist enthalten) im URI an. 

Dieses URL-Muster ist lockerer als das im Abschnitt "Grundursache" gezeigte, da es uns ermöglicht, Synapse neue WebSocket-abhängige Funktionen hinzuzufügen, ohne dass in Zukunft ein potenzielles Konnektivitätsproblem vorliegt. 


## <a name="next-steps"></a>Nächste Schritte
Wenn die vorherigen Schritte nicht zur Lösung Ihres Problems beitragen, [erstellen Sie ein Supportticket](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

---
title: Problembehandlung bei der Bereitstellung der Microsoft Sentinel-Lösung für SAP | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie bestimmte Probleme behandeln, die bei der Bereitstellung der Microsoft Sentinel-Lösung für SAP auftreten können.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: troubleshooting
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: 7eee4c9fdd3de93017c15556646fa9e6ece8b22c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520007"
---
# <a name="troubleshooting-your-microsoft-sentinel-sap-solution-deployment"></a>Problembehandlung bei der Bereitstellung der Microsoft Sentinel-Lösung für SAP

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="useful-docker-commands"></a>Nützliche Docker-Befehle

Bei der Problembehandlung des SAP-Datenconnectors können die folgenden Befehle nützlich sein:

|Funktion  |Befehl  |
|---------|---------|
|**Beenden des Docker-Containers**     |  `docker stop sapcon-[SID]`       |
|**Starten des Docker-Containers**     |`docker start sapcon-[SID]`         |
|**Anzeigen von Docker-Systemprotokollen**     |  `docker logs -f sapcon-[SID]`       |
|**Aufrufen des Docker-Containers**     |   `docker exec -it sapcon-[SID] bash`      |
|     |         |

Weitere Informationen finden Sie in der [Dokumentation zur Docker CLI](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="review-system-logs"></a>Überprüfen von Systemprotokollen

Es wird dringend empfohlen, die Systemprotokolle nach dem Installieren oder [Zurücksetzen des Datenconnectors](#reset-the-sap-data-connector) zu überprüfen.

Führen Sie Folgendes aus:

```bash
docker logs -f sapcon-[SID]
```

## <a name="enable-debug-mode-printing"></a>Aktivieren der Debugmodusausgabe

**So aktivieren Sie die Debugmodusausgabe**

1. Kopieren Sie die folgende Datei in Ihr Verzeichnis **sapcon/[SID]** , und benennen Sie sie dann in `loggingconfig.yaml` um: https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml

1. [Setzen Sie den SAP-Datenconnector zurück](#reset-the-sap-data-connector).

Beispiel für SID `A4H`:

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

**Führen Sie**  aus, um das Drucken im Debugmodus erneut zu deaktivieren:

```bash
mv loggingconfig.yaml loggingconfig.old
ls
docker restart sapcon-[SID]
```

## <a name="view-all-docker-execution-logs"></a>Anzeigen aller Docker-Ausführungsprotokolle

Führen Sie einen der folgenden Befehle aus, um alle Docker-Ausführungsprotokolle für die Bereitstellung des Microsoft Sentinel-Datenconnectors für SAP anzuzeigen:

```bash
docker exec -it sapcon-[SID] bash && cd /sapcon-app/sapcon/logs
```

oder

```bash
docker exec –it sapcon-[SID] cat /sapcon-app/sapcon/logs/[FILE_LOGNAME]
```

Es sollte eine Ausgabe angezeigt werden, die Folgendem ähnelt:

```bash
Logs directory:
root@644c46cd82a9:/sapcon-app# ls sapcon/logs/ -l
total 508
-rwxr-xr-x 1 root root      0 Mar 12 09:22 ' __init__.py'
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPAppLog.log
-rw-r--r-- 1 root root   1056 Mar 12 16:01  ABAPAuditLog.log
-rw-r--r-- 1 root root    465 Mar 12 16:01  ABAPCRLog.log
-rw-r--r-- 1 root root    515 Mar 12 16:01  ABAPChangeDocsLog.log
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPJobLog.log
-rw-r--r-- 1 root root    480 Mar 12 16:01  ABAPSpoolLog.log
-rw-r--r-- 1 root root    525 Mar 12 16:01  ABAPSpoolOutputLog.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  ABAPTableDataLog.log
-rw-r--r-- 1 root root    495 Mar 12 16:01  ABAPWorkflowLog.log
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Microsoft Sentinel
-rw-r--r-- 1 root root      0 Mar 12 15:51  LogsDeltaManager.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  PersistenceManager.log
-rw-r--r-- 1 root root   4830 Mar 12 16:01  RFC.log
-rw-r--r-- 1 root root   5595 Mar 12 16:03  SystemAdmin.log
```

Führen Sie Folgendes aus, um Ihre Protokolle in das Hostbetriebssystem zu kopieren:

```bash
docker cp sapcon-[SID]:/sapcon-app/sapcon/logs /directory
```

Zum Beispiel:

```bash
docker cp sapcon-A4H:/sapcon-app/sapcon/logs /tmp/sapcon-logs-extract
```

## <a name="review-and-update-the-sap-data-connector-configuration"></a>Überprüfen und Aktualisieren der Konfiguration des SAP-Datenconnectors

Wenn Sie die Konfigurationsdatei des SAP-Datenconnectors überprüfen und manuelle Aktualisierungen vornehmen möchten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie auf Ihrer VM im Basisverzeichnis des Benutzers die Datei **~/sapcon/[SID]/systemconfig.ini**.
1. Aktualisieren Sie die Konfiguration bei Bedarf, und starten Sie den Container dann neu:

    ```bash
    docker restart sapcon-[SID]
    ```

## <a name="reset-the-sap-data-connector"></a>Zurücksetzen des SAP-Datenconnectors

Mit den folgenden Schritten werden der Connector zurückgesetzt und die SAP-Protokolle der letzten 24 Stunden erneut erfasst.

1.  Beenden Sie den Connector. Führen Sie Folgendes aus:

    ```bash
    docker stop sapcon-[SID]
    ```

1.  Löschen Sie die Datei **metadata.db** aus dem Verzeichnis **sapcon/[SID]** . Führen Sie Folgendes aus:

    ```bash
    cd ~/sapcon/<SID>
    ls
    mv metadata.db metadata.old
    ```

    > [!NOTE]
    > Die Datei **metadata.db** enthält den letzten Zeitstempel jedes Protokolls und verhindert Duplizierung.

1. Starten Sie den Connector neu. Führen Sie Folgendes aus:

    ```bash
    docker start sapcon-[SID]
    ```

[Überprüfen Sie die Systemprotokolle](#review-system-logs), wenn Sie fertig sind.



## <a name="common-issues"></a>Häufige Probleme

Nachdem Sie sowohl den SAP-Datenconnector als auch Sicherheitsinhalte bereitgestellt haben, können die folgenden Fehler oder Probleme auftreten:

### <a name="corrupt-or-missing-sap-sdk-file"></a>Beschädigte oder fehlende SAP SDK-Datei

Dieser Fehler liegt möglicherweise vor, wenn der Connector nicht mit PyRfc gestartet werden kann oder ZIP-bezogene Fehlermeldungen angezeigt werden.

1. Installieren Sie das SAP SDK neu.
1. Vergewissern Sie sich, dass Sie die richtige 64-Bit-Version von Linux haben. Zum jetzigen Zeitpunkt lautet der Dateiname des Release **nwrfc750P_8-70002752.zip**.

Wenn Sie den Datenconnector manuell installiert haben, stellen Sie sicher, dass Sie die SDK-Datei in den Docker-Container kopiert haben.

Führen Sie Folgendes aus:

```bash
Docker cp SDK by running docker cp nwrfc750P_8-70002752.zip /sapcon-app/inst/
```

### <a name="abap-runtime-errors-appear-on-a-large-system"></a>ABAP-Laufzeitfehler werden in großem System gemeldet

Wenn ABAP Laufzeitfehler in großen Systemen gemeldet werden, versuchen Sie, eine kleinere Blockgröße festzulegen:

1. Bearbeiten Sie die Datei **sapcon/SID/systemconfig.ini**, und legen Sie `timechunk = 5` fest.
2. [Setzen Sie den SAP-Datenconnector zurück](#reset-the-sap-data-connector).

> [!NOTE]
> Die Größe von **timechunk** wird in Minuten angegeben.

### <a name="empty-or-no-audit-log-retrieved-with-no-special-error-messages"></a>Leeres oder nicht abgerufenes Überwachungsprotokoll ohne spezielle Fehlermeldungen

1. Prüfen Sie, ob die Überwachungsprotokollierung in SAP aktiviert ist.
1. Überprüfen Sie die Transaktionen **SM19** oder **RSAU_CONFIG**.
1. Aktivieren Sie nach Bedarf alle Ereignisse.
1. Überprüfen Sie, ob Nachrichten in SAP **SM20** oder **RSAU_READ_LOG** eingehen und vorhanden sind, ohne dass im Connectorprotokoll spezielle Fehler angezeigt werden.


### <a name="incorrect-microsoft-sentinel-workspace-id-or-key"></a>Falsche ID oder falscher Schlüssel für den Microsoft Sentinel-Arbeitsbereich

Wenn Sie feststellen, dass Sie eine falsche Arbeitsbereichs-ID oder einen falschen Schlüssel in Ihr [Bereitstellungsskript](sap-deploy-solution.md#create-a-key-vault-for-your-sap-credentials) eingegeben haben, aktualisieren Sie die in Azure Key Vault gespeicherten Anmeldeinformationen.

Starten Sie den Container neu, nachdem Sie Ihre Anmeldeinformationen in Azure KeyVault überprüft haben:

```bash
docker restart sapcon-[SID]
```

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>Falsche SAP ABAP-Benutzeranmeldeinformationen in einer festen Konfiguration

Eine feste Konfiguration liegt vor, wenn das Kennwort direkt in der Konfigurationsdatei **systemconfig.ini** gespeichert wird.

Wenn Ihre Anmeldeinformationen dort falsch sind, überprüfen Sie Ihre Anmeldeinformationen.

Verwenden Sie die base64-Verschlüsselung, um Benutzer und Kennwort zu verschlüsseln. Sie können Tools für die Onlineverschlüsselung verwenden, um Ihre Anmeldeinformationen zu verschlüsseln, beispielsweise https://www.base64encode.org/.

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>Falsche SAP ABAP-Benutzeranmeldeinformationen in Schlüsseltresor

Überprüfen Sie Ihre Anmeldeinformationen, und korrigieren Sie sie ggf., indem Sie in Azure Key Vault die richtigen Werte für **ABAPUSER** und **ABAPPASS** anwenden.

Starten Sie den Container anschließend neu:

```bash
docker restart sapcon-[SID]
```


### <a name="missing-abap-sap-user-permissions"></a>Fehlende Berechtigungen für ABAP (SAP-Benutzer)

Wenn sie eine Fehlermeldung wie die folgende erhalten: **... Fehlende Back-End-RFC-Autorisierung...** , wurden Ihre SAP-Autorisierungen und -Rolle nicht ordnungsgemäß zugeordnet.

1. Stellen Sie sicher, dass die **Rolle MSFTSEN/SENTINEL_CONNECTOR** im Rahmen eines [Change Request](sap-solution-detailed-requirements.md#required-sap-log-change-requests)-Transports importiert und dem Connectorbenutzer zugeordnet wurde.

1. Führen Sie den Prozess für Rollengenerierung und Benutzervergleich mithilfe der SAP-Transaktion PFCG aus.

### <a name="missing-data-in-your-workbooks-or-alerts"></a>Fehlende Daten in Ihrer Arbeitsmappe oder Ihren Warnungen

Wenn Sie feststellen, dass in Ihren Microsoft Sentinel-Arbeitsmappen oder -Warnungen Daten fehlen, stellen Sie sicher, dass die **Auditlog**-Richtlinie auf SAP-Seite ordnungsgemäß aktiviert ist und die Protokolldatei keine Fehler enthält. 

Verwenden Sie die Transaktion **RSAU_CONFIG_LOG** für diesen Schritt.


### <a name="missing-sap-change-request"></a>Fehlender SAP Change Request

Wenn Fehler angezeigt werden, dass ein erforderlicher [SAP Change Request](sap-solution-detailed-requirements.md#required-sap-log-change-requests) fehlt, stellen Sie sicher, dass Sie den richtigen SAP Change Request für Ihr System importiert haben.

Weitere Informationen finden Sie unter [Konfigurieren Ihres SAP-Systems](sap-deploy-solution.md#configure-your-sap-system).

### <a name="network-connectivity-issues"></a>Probleme mit der Netzwerkkonnektivität

Wenn Probleme mit der Netzwerkkonnektivität mit der SAP-Umgebung oder Microsoft Sentinel auftreten, überprüfen Sie Ihre Netzwerkkonnektivität, um sicherzustellen, dass der Datenfluss den Erwartungen entspricht.

Häufige Probleme sind beispielweise:

- Firewalls zwischen dem Docker-Container und den SAP-Hosts blockieren möglicherweise den Datenverkehr. Der SAP-Host empfängt Kommunikationen über die folgenden TCP-Ports, die offen sein müssen: **32xx**, **5xx13** und **33xx**. **xx** steht hierbei für die SAP-Instanznummer.

- Für die ausgehende Kommunikation Ihres SAP-Hosts mit Microsoft Container Registry oder Azure ist eine Proxykonfiguration erforderlich. Dies wirkt sich in der Regel auf die Installation aus und führt dazu, dass Sie die Umgebungsvariablen `HTTP_PROXY` und `HTTPS_PROXY` konfigurieren müssen. Sie können Umgebungsvariablen auch bei der Erstellung des Docker-Containers in selbigem erfassen, indem Sie dem Docker-Befehl `create` / `run` das Flag `-e` hinzufügen.

### <a name="other-unexpected-issues"></a>Sonstige unerwartete Fehler

Wenn unerwartete Probleme auftreten, die in diesem Artikel nicht aufgeführt sind, versuchen Sie Folgendes:

- [Zurücksetzen des Connectors und erneutes Laden der Protokolle](#reset-the-sap-data-connector)
- [Upgraden Sie den Connector](sap-deploy-solution.md#update-your-sap-data-connector) auf die aktuelle Version.

> [!TIP]
> Das Zurücksetzen Ihres Connectors und Sicherstellen, dass Sie über die neuesten Upgrades verfügen, wird auch nach größeren Konfigurationsänderungen empfohlen.

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>Fehler beim Abrufen eines Überwachungsprotokolls mit Warnungen

Wenn Sie versuchen, ein Überwachungsprotokoll ohne den [erforderlichen Change Request](sap-solution-detailed-requirements.md#required-sap-log-change-requests) oder mit einer älteren/nicht gepatchten Version abzurufen, und der Prozess mit Warnungen fehlschlägt, überprüfen Sie, ob das SAP-Überwachungsprotokoll mithilfe einer der folgenden Methoden abgerufen werden kann:

- Verwenden eines Kompatibilitätsmodus namens *XAL* in älteren Versionen
- Verwenden einer Version, die nicht kürzlich gepatcht wurde
- Ohne Installation des erforderlichen Change Requests

Eigentlich sollte Ihr System bei Bedarf automatisch in den Kompatibilitätsmodus wechseln, aber u. U. müssen Sie die Umstellung manuell vornehmen. So stellen Sie manuell auf den Kompatibilitätsmodus um

1. Bearbeiten Sie im Verzeichnis **sapcon/SID** die Datei **systemconfig.ini**.

1. Legen Sie Folgendes fest: `auditlogforcexal = True`

1. Starten Sie den Docker-Container neu:

    ```bash
    docker restart sapcon-[SID]
    ```

### <a name="sapcontrol-or-java-subsystems-unable-to-connect"></a>SAPCONTROL- oder JAVA-Subsysteme können keine Verbindung herstellen

Prüfen Sie, ob der Betriebssystembenutzer gültig ist und auf dem SAP-Zielsystem den folgenden Befehl ausführen kann:

```bash
sapcontrol -nr <SID> -function GetSystemInstanceList
```

### <a name="sapcontrol-or-java-subsystem-fails-with-timezone-related-error-message"></a>Fehler beim SAPCONTROL- oder JAVA-Subsystem mit zeitzonenbezogener Fehlermeldung

Wenn Ihr SAPCONTROL- oder JAVA-Subsystem mit einer zeitzonenbezogenen Fehlermeldung ausfällt, z. B.: **Überprüfen Sie die Konfiguration und den Netzwerkzugriff auf den SAP-Server – „Etc/NZST“** , stellen Sie sicher, dass Sie Standardcodes für Zeitzonen verwenden.

Verwenden Sie beispielsweise `javatz = GMT+12` oder `abaptz = GMT-3**`.


### <a name="unable-to-import-the-change-request-transports-to-sap"></a>Change Request-Transporte können nicht in SAP importiert werden

Falls Sie die [erforderlichen Change Requests für SAP-Protokolle](sap-solution-detailed-requirements.md#required-sap-log-change-requests) nicht importieren können und eine Fehlermeldung zu einer ungültigen Komponentenversion erhalten, fügen `ignore invalid component version` Sie hinzu, wenn Sie den Change Request importieren.

### <a name="audit-log-data-not-ingested-past-initial-load"></a>Überwachungsprotokolldaten werden nach dem ersten Laden nicht erfasst

Wenn die SAP-Überwachungsprotokolldaten aus den **RSAU_READ_LOAD**- oder **SM200**-Transaktionen nach dem ersten Ladevorgang nicht in Microsoft Sentinel erfasst werden, liegt möglicherweise eine Fehlkonfiguration des SAP-Systems und des Betriebssystems des SAP-Hosts vor.

- Die ersten Ladevorgänge werden nach einer Neuinstallation des SAP-Datenconnectors oder nach dem Löschen der Datei **metadata.db** ausgeführt.
- Eine falsche Konfiguration kann beispielsweise vorliegen, wenn die Zeitzone für Ihr SAP-System in der Transaktion **STZAC** auf **CET** festgelegt ist, aber die Zeitzone für das SAP-Hostbetriebssystem **UTC** lautet.

Führen Sie den Bericht **RSDBTIME** in der Transaktion **SE38** aus, um nach Fehlkonfigurationen zu suchen. Wenn Sie einen Konflikt zwischen dem SAP-System und dem SAP-Hostbetriebssystem feststellen, gehen Sie folgendermaßen vor:

1. Beenden Sie den Docker-Container. Ausführen

    ```bash
    docker stop sapcon-[SID]
    ```

1.  Löschen Sie die Datei **metadata.db** aus dem Verzeichnis **sapcon/[SID]** . Führen Sie Folgendes aus:

    ```bash
    rm ~/sapcon/[SID]/metadata.db
    ```

1. Aktualisieren Sie das SAP-System und das SAP-Hostbetriebssystem so, dass ihre Einstellungen (z. B. die Zeitzone) übereinstimmen. Weitere Informationen finden Sie im [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/Basis/Time+zone+settings%2C+SAP+vs.+OS+level).

1. Starten Sie den Container neu. Führen Sie Folgendes aus:

    ```bash
    docker start sapcon-[SID]
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Bereitstellen der kontinuierlichen Bedrohungsüberwachung in SAP (Public Preview)](sap-deploy-solution.md)
- [Referenz zu Protokollen der Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-log-reference.md)
- [Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md)
- [Konfigurationsoptionen für Experten, lokale Bereitstellung und SAPControl-Protokollquellen](sap-solution-deploy-alternate.md)
- [Microsoft Sentinel-Lösung für SAP: Referenz zu sicherheitsbezogenen Inhalten (Public Preview)](sap-solution-security-content.md)
- [Detaillierte SAP-Anforderungen für die Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-detailed-requirements.md)

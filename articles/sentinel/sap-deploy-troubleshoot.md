---
title: Problembehandlung bei der Bereitstellung der Azure Sentinel-Lösung für SAP | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie bestimmte Probleme behandeln, die bei der Bereitstellung Ihrer Azure Sentinel-Lösung für SAP auftreten können.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: troubleshooting
ms.custom: mvc
ms.date: 07/29/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: e3503b8f7464f66bd404b9b70196a017d9c058b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354749"
---
# <a name="troubleshooting-your-azure-sentinel-sap-solution-deployment"></a>Problembehandlung bei der Bereitstellung der Azure Sentinel-Lösung für SAP

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

Es wird dringend empfohlen, die Systemprotokolle nach dem Installieren oder Zurücksetzen des Datenconnectors zu überprüfen.

Führen Sie Folgendes aus:

```bash
docker logs -f sapcon-[SID]
```
## <a name="enable-debug-mode-printing"></a>Aktivieren der Debugmodusausgabe

So aktivieren Sie die Debugmodusausgabe

1. Kopieren Sie die folgende Datei in Ihr Verzeichnis **sapcon/[SID]** , und benennen Sie sie dann in `loggingconfig.yaml` um: https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml

1. [Setzen Sie den SAP-Datenconnector zurück](#reset-the-sap-data-connector).

Beispiel für SID A4H:

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.y
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

## <a name="view-all-docker-execution-logs"></a>Anzeigen aller Docker-Ausführungsprotokolle

Führen Sie einen der folgenden Befehle aus, um alle Docker-Ausführungsprotokolle für die Bereitstellung des SAP-Datenconnectors von Azure Sentinel anzuzeigen:

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
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Azure Sentinel
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

1.  Löschen Sie die Datei **metadata.db** aus dem Verzeichnis **sapcon/[SID]** .

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

Dies tritt auf, wenn der Connector nicht mit PyRfc gestartet werden kann oder ZIP-bezogene Fehlermeldungen angezeigt werden.

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
1. Überprüfen Sie die Transaktionen **SM19** und **RASU_CONFIG**.
1. Aktivieren Sie nach Bedarf alle Ereignisse.
1. Überprüfen Sie, ob Nachrichten in SAP **SM20** oder **RSAU_READ_LOG** eingehen und vorhanden sind, ohne dass im Connectorprotokoll spezielle Fehler angezeigt werden.


### <a name="incorrect-azure-sentinel-workspace-id-or-key"></a>Falsche ID oder falscher Schlüssel für Azure Sentinel-Arbeitsbereich

Wenn Sie feststellen, dass Sie eine falsche Arbeitsbereichs-ID oder einen falschen Schlüssel in Ihr [Bereitstellungsskript](sap-deploy-solution.md#create-key-vault-for-your-sap-credentials) eingegeben haben, aktualisieren Sie die in Azure Key Vault gespeicherten Anmeldeinformationen.

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>Falsche SAP ABAP-Benutzeranmeldeinformationen in einer festen Konfiguration

Eine feste Konfiguration liegt vor, wenn das Kennwort direkt in der Konfigurationsdatei **systemconfig.ini** gespeichert wird.

Wenn Ihre Anmeldeinformationen dort falsch sind, überprüfen Sie Ihre Anmeldeinformationen.

Verwenden Sie die base64-Verschlüsselung, um Benutzer und Kennwort zu verschlüsseln. Hierfür bieten sich Onlineverschlüsselungstools an, z. B. https://www.base64encode.org/.

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>Falsche SAP ABAP-Benutzeranmeldeinformationen in Schlüsseltresor

Überprüfen Sie Ihre Anmeldeinformationen, und korrigieren Sie sie bei Bedarf.

Versehen Sie die Werte **ABAPUSER** und **ABAPPASS** in Azure Key Vault mit den richtigen Informationen.



### <a name="missing-abap-sap-user-permissions"></a>Fehlende Berechtigungen für ABAP (SAP-Benutzer)

Wenn sie eine Fehlermeldung wie die folgende erhalten: **... Fehlende Back-End-RFC-Autorisierung...** , wurden Ihre SAP-Autorisierungen und -Rolle nicht ordnungsgemäß zugeordnet.

1. Stellen Sie sicher, dass die **Rolle MSFTSEN/SENTINEL_CONNECTOR** im Rahmen eines [Change Request](sap-solution-detailed-requirements.md#required-sap-log-change-requests)-Transports importiert und dem Connectorbenutzer zugeordnet wurde.

1. Führen Sie den Prozess für Rollengenerierung und Benutzervergleich mithilfe der SAP-Transaktion PFCG aus.

### <a name="missing-data-in-your-workbooks-or-alerts"></a>Fehlende Daten in Ihrer Arbeitsmappe oder Ihren Warnungen

Wenn Sie feststellen, dass Daten in Ihren Azure Sentinel-Arbeitsmappen oder -Warnungen fehlen, stellen Sie sicher, dass die **Auditlog**-Richtlinie auf SAP-Seite ordnungsgemäß aktiviert ist und keine Fehler in der Protokolldatei auftreten. 

Verwenden Sie die Transaktion **RSAU_CONFIG_LOG** für diesen Schritt.


### <a name="missing-sap-change-request"></a>Fehlender SAP Change Request

Wenn Fehler angezeigt werden, dass ein erforderlicher [SAP Change Request](sap-solution-detailed-requirements.md#required-sap-log-change-requests) fehlt, stellen Sie sicher, dass Sie den richtigen SAP Change Request für Ihr System importiert haben.

Weitere Informationen finden Sie unter [Konfigurieren Ihres SAP-Systems](sap-deploy-solution.md#configure-your-sap-system).

### <a name="network-connectivity-issues"></a>Probleme mit der Netzwerkkonnektivität

Wenn Probleme mit der Netzwerkkonnektivität mit der SAP-Umgebung oder Azure Sentinel auftreten, überprüfen Sie Ihre Netzwerkkonnektivität, um sicherzustellen, dass Daten wie erwartet fließen.

### <a name="other-unexpected-issues"></a>Sonstige unerwartete Fehler

Wenn unerwartete Probleme auftreten, die in diesem Artikel nicht aufgeführt sind, versuchen Sie Folgendes:

- [Zurücksetzen des Connectors und erneutes Laden der Protokolle](#reset-the-sap-data-connector)
- [Durchführen eines Upgrades des Connectors](sap-deploy-solution.md#update-your-sap-data-connector) auf die aktuelle Version

> [!TIP]
> Das Zurücksetzen Ihres Connectors und Sicherstellen, dass Sie über die neuesten Upgrades verfügen, wird auch nach größeren Konfigurationsänderungen empfohlen.

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>Fehler beim Abrufen eines Überwachungsprotokolls mit Warnungen

Wenn Sie versuchen, ein Überwachungsprotokoll abzurufen, ohne dass der [erforderliche Change Request](sap-solution-detailed-requirements.md#required-sap-log-change-requests) bereitgestellt wurde, oder für eine ältere/nicht gepatchte Version und der Prozess mit Warnungen fehlschlägt, überprüfen Sie, ob das SAP-Überwachungsprotokoll mit einer der folgenden Methoden abgerufen werden kann:

- Verwenden eines Kompatibilitätsmodus namens *XAL* in älteren Versionen
- Verwenden einer Version, die nicht kürzlich gepatcht wurde
- Ohne Installation des erforderlichen Change Requests

Eigentlich sollte Ihr System bei Bedarf automatisch in den Kompatibilitätsmodus wechseln, aber u. U. müssen Sie die Umstellung manuell vornehmen. So stellen Sie manuell auf den Kompatibilitätsmodus um

1. Bearbeiten Sie im Verzeichnis **sapcon/SID** die Datei **systemconfig.ini**.
1. Legen Sie Folgendes fest: `auditlogforcexal = True`

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


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter:

- [Bereitstellen der kontinuierlichen Bedrohungsüberwachung in SAP (Public Preview)](sap-deploy-solution.md)
- [Referenz zu Protokollen der Azure Sentinel-Lösung für SAP (Public Preview)](sap-solution-log-reference.md)
- [Konfigurationsoptionen für Experten, lokale Bereitstellung und SAPControl-Protokollquellen](sap-solution-deploy-alternate.md)
- [Azure Sentinel-Lösung für SAP: Referenz zu sicherheitsbezogenen Inhalten (Public Preview)](sap-solution-security-content.md)
- [Detaillierte SAP-Anforderungen für die Azure Sentinel-Lösung für SAP (öffentliche Vorschau)](sap-solution-detailed-requirements.md)

---
title: Problembehandlung für das Tool für anwendungskonsistente Momentaufnahmen in Azure für Azure NetApp Files | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur Problembehandlung für das Tool für anwendungskonsistente Momentaufnahmen in Azure, das Sie mit Azure NetApp Files verwenden können.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/17/2021
ms.author: phjensen
ms.openlocfilehash: f44ad9257f6fbc6b8ca232964f9bee29cea57384
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256487"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool"></a>Problembehandlung für das Tool für anwendungskonsistente Momentaufnahmen in Azure

Dieser Artikel bietet Informationen zur Problembehandlung für das Tool für anwendungskonsistente Momentaufnahmen in Azure (Azure Application Consistent Snapshot, AzAcSnap), das Sie mit Azure NetApp Files und Azure (große Instanz) verwenden können.

Hier finden Sie einige häufige Probleme, die bei der Ausführung der Befehle auftreten können. Befolgen Sie die angegebenen Anweisungen, um das jeweilige Problem zu beheben. Falls weiterhin Probleme auftreten, eröffnen Sie eine Serviceanfrage im Azure-Portal, und ordnen Sie diese zur Beantwortung durch den Microsoft-Support in die Warteschlange für große SAP HANA-Instanzen ein.

## <a name="log-files"></a>Protokolldateien

Eine der besten Informationsquellen beim Debuggen von Fehlern mit AzAcSnap sind die Protokolldateien.  

### <a name="log-file-location"></a>Speicherort der Protokolldatei

Die Protokolldateien sind einem Verzeichnis gespeichert, das in der Konfigurationsdatei für AzAcSnap mit dem Parameter `logPath` konfiguriert ist.  Der Standardname der Konfigurationsdatei lautet `azacsnap.json`, und der Standardwert für `logPath` ist `"./logs"`. Das bedeutet, dass die Protokolldateien in das `./logs`-Verzeichnis im relativen Pfad zum Ausführungsverzeichnis des Befehls `azacsnap` geschrieben werden.  Indem `logPath` als absoluter Speicherort (z. B. `/home/azacsnap/logs`) festgelegt wird, ist sichergestellt, dass `azacsnap` die Protokolle in `/home/azacsnap/logs` ausgibt, unabhängig davon, wo der Befehl `azacsnap` ausgeführt wurde.

### <a name="log-file-naming"></a>Benennen der Protokolldatei

Der Name der Protokolldatei basiert auf dem Anwendungsnamen (z. B. `azacsnap`), der Befehlsoption (`-c`), die verwendet wurde (z. B. `backup`, `test`, `details` usw.), und dem Namen der Konfigurationsdatei (Standardwert: `azacsnap.json`).  Wenn Sie also den Befehl `-c backup` verwenden, lautet der Name der Protokolldatei standardmäßig `azacsnap-backup-azacsnap.log`, und die Datei wird in das durch `logPath` konfigurierte Verzeichnis geschrieben.  

Diese Namenskonvention wurde eingerichtet, um mehrere Konfigurationsdateien zu ermöglichen (eine pro Datenbank) und das Auffinden der zugehörigen Protokolldateien zu erleichtern.  Daher gilt: Wenn der Name der Konfigurationsdatei `SID.json` lautet und die `azacsnap -c backup --configfile SID.json`-Optionen verwendet werden, ist der Name der Ergebnisdatei `azacsnap-backup-SID.log`.

### <a name="result-file-and-syslog"></a>Ergebnisdatei und Syslog

Bei der Befehlsoption `-c backup` schreibt AzAcSnap in eine `*.result`-Datei und das Systemprotokoll (`/var/log/messages`). Dabei wird der Befehl `logger` verwendet.  Der Dateiname `*.result` weist denselben Basisnamen auf wie die [Protokolldatei](#log-file-naming) und wird im selben [Speicherort gespeichert wie die Protokolldatei](#log-file-location).  Es handelt sich um eine einfache einzeilige Ausgabedatei, wie in den folgenden Beispielen gezeigt.

Beispielausgabe aus der `*.result`-Datei.
```output
Database # 1 (PR1) : completed ok
```

Beispielausgabe aus der `/var/log/messages`-Datei.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Fehler bei der Kommunikation mit Azure NetApp Files

Bei der Validierung der Kommunikation mit Azure NetApp Files kann die Kommunikation fehlschlagen oder eine Zeitüberschreitung auftreten.  Stellen Sie sicher, dass die Firewall-Regeln den ausgehenden Datenverkehr vom System, auf dem AzAcSnap läuft, zu den folgenden Adressen und TCP/IP-Ports nicht blockieren:-

- (https://)management.azure.com:443
- (https://)login.microsoftonline.com:443 

### <a name="testing-communication-using-cloud-shell"></a>Testen der Kommunikation mit Cloud Shell

Sie können testen, ob der Dienstprinzipal ordnungsgemäß konfiguriert ist, indem Sie Cloud Shell über Ihr Azure-Portal verwenden. Dadurch wird unter Umgehung von Netzwerksteuerungen in einem VNeT oder virtuellen Computer die richtige Konfiguration durchgeführt. 

**Lösung:**

1. Öffnen Sie eine [Cloud Shell](../cloud-shell/overview.md)-Sitzung in Ihrem Azure-Portal. 
1. Erstellen Sie ein Testverzeichnis (z. B. `mkdir azacsnap`)
1. Wechseln Sie mit „cd“ zum Verzeichnis „azacsnap“ und laden Sie die neueste Version des azacsnap-Tools herunter.
    
    ```bash
    wget https://aka.ms/azacsnapinstaller
    ```
   
    ```output
    ----<snip>----
    HTTP request sent, awaiting response... 200 OK
    Length: 24402411 (23M) [application/octet-stream]
    Saving to: ‘azacsnapinstaller’

    azacsnapinstaller 100%[=================================================================================>] 23.27M 5.94MB/s in 5.3s

    2021-09-02 23:46:18 (4.40 MB/s) - ‘azacsnapinstaller’ saved [24402411/24402411]
    ```
    
1. Machen Sie das Installationsprogramm zu einer ausführbaren Datei. (z.B. `chmod +x azacsnapinstaller`)
1. Extrahieren Sie die Binärdatei zum Testen.

    ```bash
    ./azacsnapinstaller -X -d .
    ```
    
    ```output
    +-----------------------------------------------------------+
    | Azure Application Consistent Snapshot Tool Installer |
    +-----------------------------------------------------------+
    |-> Installer version '5.0.2_Build_20210827.19086'
    |-> Extracting commands into ..
    |-> Cleaning up .NET extract dir
    ```

1. Laden Sie mit dem Cloud Shell-Symbol Upload/Download die Dienstprinzipaldatei (z. B. `azureauth.json`) und die AzAcSnap-Konfigurationsdatei (z. B. `azacsnap.json`) zum Testen hoch.
1. Führen Sie den Storage-Test über die Azure Cloud Shell aus. 

    > [!NOTE]
    > Der Testbefehl kann etwa 90 Sekunden in Anspruch nehmen.

    ```bash
    ./azacsnap -c test --test storage
    ```

    ```output
    BEGIN : Test process started for 'storage'
    BEGIN : Storage test snapshots on 'data' volumes
    BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
    PASSED: Task#1/1 Storage test successful for Volume
    END : Storage tests complete
    END : Test process complete for 'storage'
    ```

## <a name="problems-with-sap-hana"></a>Probleme mit SAP HANA

### <a name="running-the-test-command-fails"></a>Fehler beim Ausführen des Testbefehls

Beim Überprüfen der Kommunikation mit SAP HANA mithilfe eines Tests mit `azacsnap -c test --test hana` tritt der folgende Fehler auf:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Lösung:**

1. Überprüfen Sie die Konfigurationsdatei (z. B. `azacsnap.json`) für jede HANA-Instanz, um sicherzustellen, dass die Werte für die SAP HANA-Datenbanken richtig sind.
1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Befehl `hdbsql` im Pfad ausgeführt wird und eine Verbindung mit dem SAP HANA-Server herstellen kann. Das folgende Beispiel zeigt die korrekte Ausführung des Befehls und die zugehörige Ausgabe.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    In diesem Beispiel ist der Befehl `hdbsql` nicht in der `$PATH`-Variable des Benutzers enthalten.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    In diesem Beispiel wird der Befehl `hdbsql` der `$PATH`-Variable des Benutzers temporär hinzugefügt, aber bei der Ausführung zeigt sich, dass der Verbindungsschlüssel nicht ordnungsgemäß mit dem Befehl `hdbuserstore Set` eingerichtet wurde (Details finden Sie im Leitfaden für die ersten Schritte):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (AZACSNAP)
    ```

    > [!NOTE]
    > Um der `$PATH`-Variable des Benutzers Befehle dauerhaft hinzuzufügen, aktualisieren Sie die `$HOME/.profile`-Datei des Benutzers.

### <a name="insufficient-privilege"></a>Unzureichende Berechtigungen

Wenn beim Ausführen von `azacsnap` ein Fehler wie `* 258: insufficient privilege` angezeigt wird, überprüfen Sie, ob die geeigneten Berechtigungen dem Datenbankbenutzer „AZACSNAP“ zugewiesen wurden (vorausgesetzt, dies ist der Benutzer, der gemäß [Installationshandbuch](azacsnap-installation.md#enable-communication-with-database) erstellt wurde).  Überprüfen Sie mit dem folgenden Befehl die aktuelle Berechtigung des Benutzers:

```bash
hdbsql -U AZACSNAP "select GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE from sys.granted_privileges "' | grep -i -e GRANTEE -e azacsnap
```

```output
GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE
"AZACSNAP","USER","BACKUP ADMIN","TRUE","FALSE"
"AZACSNAP","USER","CATALOG READ","TRUE","FALSE"
"AZACSNAP","USER","CREATE ANY","TRUE","TRUE"
```

Der Fehler enthält möglicherweise auch weitere Informationen, um die erforderlichen SAP HANA-Berechtigungen zu bestimmen, z. B. die Ausgabe von `Detailed info for this error can be found with guid '99X9999X99X9999X99X99XX999XXX999' SQLSTATE: HY000`.  Befolgen Sie in diesem Fall die Anweisungen von SAP im [SAP-Hilfeportal – GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/9a73c4c017744288b8d6f3b9bc0db043.html), worin empfohlen wird, die folgende SQL-Abfrage zu verwenden, um die Details zu den erforderlichen Berechtigungen zu ermitteln.

```sql
CALL SYS.GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS ('99X9999X99X9999X99X99XX999XXX999', ?)
```

```output
GUID,CREATE_TIME,CONNECTION_ID,SESSION_USER_NAME,CHECKED_USER_NAME,PRIVILEGE,IS_MISSING_ANALYTIC_PRIVILEGE,IS_MISSING_GRANT_OPTION,DATABASE_NAME,SCHEMA_NAME,OBJECT_NAME,OBJECT_TYPE
"99X9999X99X9999X99X99XX999XXX999","2021-01-01 01:00:00.180000000",120212,"AZACSNAP","AZACSNAP","DATABASE ADMIN or DATABASE BACKUP ADMIN","FALSE","FALSE","","","",""
```

Im obigen Beispiel sollte das Hinzufügen der Berechtigung „DATABASE BACKUP ADMIN“ zum AZACSNAP-Benutzer von SYSTEMDB den Fehler wegen unzureichender Berechtigungen beheben.

### <a name="the-hdbuserstore-location"></a>Der Speicherort `hdbuserstore`

Beim Einrichten der Kommunikation mit SAP HANA werden die Einstellungen für eine sichere Kommunikation über das Programm `hdbuserstore` erstellt.  Das Programm `hdbuserstore` befindet sich in der Regel unter `/usr/sap/<SID>/SYS/exe/hdb/` oder `/usr/sap/hdbclient`.  Normalerweise fügt das Installationsprogramm den richtigen Speicherort zur `azacsnap`-Variable des `$PATH`-Benutzers hinzu.

## <a name="failed-test-with-storage"></a>Fehler beim Test mit Speicher

Der Befehl `azacsnap -c test --test storage` wird nicht erfolgreich ausgeführt.

### <a name="azure-large-instance"></a>Azure (große Instanz)

Das folgende Beispiel stammt aus der Ausführung von `azacsnap` auf SAP HANA auf einer großen Azure-Instanz:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Lösung:** Der oben genannte Fehler wird normalerweise angezeigt, wenn ein Speicherbenutzer einer großen Azure-Instanz keine Zugriff auf den zugrunde liegenden Speicher hat. Um den Zugriff auf den Speicher für den bereitgestellten Speicherbenutzer zu überprüfen, führen Sie den Befehl `ssh` aus. Damit prüfen Sie die Kommunikation mit der Speicherplattform.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Ein Beispiel mit der erwarteten Ausgabe:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Lösung:** Wählen Sie nicht „Ja“ aus. Stellen Sie sicher, dass die IP-Adresse Ihres Speichers korrekt ist. Falls das Problem weiterhin besteht, überprüfen Sie die IP-Adresse des Speichers mit dem Microsoft-Betriebsteam.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Das folgende Beispiel entstammt der Ausführung von `azacsnap` auf einer VM mit Azure NetApp Files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Lösung:**

1. Überprüfen Sie, ob die Dienstprinzipaldatei `azureauth.json` vorhanden ist, wie in der Konfigurationsdatei `azacsnap.json` festgelegt.
1. Überprüfen Sie in der Protokolldatei (z. B. `logs/azacsnap-test-azacsnap.log`), ob der Dienstprinzipal (`azureauth.json`) den richtigen Inhalt aufweist.  Beispiel aus dem Protokoll:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Überprüfen Sie in der Protokolldatei (z. B. `logs/azacsnap-test-azacsnap.log`), ob der Dienstprinzipal (`azureauth.json`) abgelaufen ist. Beispiel aus dem Protokoll:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Nächste Schritte

- [Tipps](azacsnap-tips.md)
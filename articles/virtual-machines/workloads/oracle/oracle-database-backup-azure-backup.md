---
title: Sichern und Wiederherstellen einer Oracle Database 19c-Datenbank auf einer Azure Linux-VM mithilfe von Azure Backup
description: Erfahren Sie, wie Sie eine Oracle Database 19c-Datenbank mit dem Dienst Azure Backup sichern und wiederherstellen.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: fd6826370a0292190fc2534585fdb08f7982f447
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698698"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Sichern und Wiederherstellen einer Oracle Database 19c-Datenbank auf einer Azure Linux-VM mithilfe von Azure Backup

**Gilt für:** :heavy_check_mark: Linux-VMs 

In diesem Artikel wird das Erstellen von Momentaufnahmen der VM-Datenträger, einschließlich Datenbankdateien und Fast Recovery Area (Bereich für schnelle Wiederherstellung), mithilfe von Azure Backup demonstriert. Mit Azure Backup können Sie vollständige Momentaufnahmen von Datenträgern erstellen, die sich als Sicherungen eignen und im [Recovery Services-Tresor](../../../backup/backup-azure-recovery-services-vault-overview.md) gespeichert werden.  Azure Backup ermöglicht auch anwendungskonsistente Sicherungen, sodass sichergestellt ist, dass zum Wiederherstellen der Daten keine zusätzlichen Korrekturen erforderlich sind. Durch die Wiederherstellung von anwendungskonsistenten Daten wird die Wiederherstellungsdauer reduziert, sodass Sie schnell zum Zustand der normalen Ausführung zurückkehren können.

> [!div class="checklist"]
>
> * Sichern der Datenbank mit anwendungskonsistenter Sicherung
> * Wiederherstellen der Datenbank mithilfe eines Wiederherstellungspunkts
> * Wiederherstellen der VM mithilfe eines Wiederherstellungspunkts

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Sie müssen für den Sicherungs- und Wiederherstellungsprozess zuerst eine Linux-VM erstellen, auf der eine Instanz von Oracle Database 19c installiert ist. Das Marketplace-Image, das derzeit zum Erstellen der VM verwendet wird, heißt **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Führen Sie die Schritte im Schnellstart [Erstellen einer Oracle-Datenbank auf einem virtuellem Azure-Computer](./oracle-database-quick-create.md) aus, um eine Oracle-Datenbank zu erstellen, mit der Sie dieses Tutorial absolvieren können.


## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung

Führen Sie die folgenden Schritte aus, um die Umgebung vorzubereiten:

1. [Stellen Sie eine Verbindung mit der VM her](#connect-to-the-vm).
1. [Einrichten von Azure Files-Speicher](#setup-azure-files-storage-for-the-oracle-archived-redo-log-files)
1. [Bereiten Sie die Datenbank vor](#prepare-the-databases).

### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

1. Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung (Secure Shell) mit dem virtuellen Computer. Ersetzen Sie die Kombination aus IP-Adresse und Hostname durch den `<publicIpAddress>`-Wert für Ihren virtuellen Computer.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Wechseln Sie zum Benutzer *root*:

   ```bash
   sudo su -
   ```
    
1. Fügen Sie der Datei */etc/sudoers* den Benutzer „oracle“ hinzu:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="setup-azure-files-storage-for-the-oracle-archived-redo-log-files"></a>Einrichten von Azure Files-Speicher für die archivierten Oracle-Wiederholungsprotokolldateien

Die archivierten Oracle-Wiederholungsprotokolldateien spielen eine wichtige Rolle bei der Datenbankwiederherstellung, da die Transaktionen in diesen gespeichert sind, für die ein Rollforward von einer Datenbankmomentaufnahme ausgeführt wurde, die in der Vergangenheit erstellt wurde. Im Archivprotokollmodus archiviert die Datenbank die Inhalte von Online-Wiederholungsprotokolldateien, wenn diese voll sind und ausgetauscht werden. Diese werden zusammen mit einer Sicherung für eine Zeitpunktwiederherstellung benötigt, wenn die Datenbank verloren gegangen ist.  
   
Mit Oracle können Wiederholungsprotokolldateien an verschiedenen Speicherorten archiviert werden. Als Best Practice wird in der Branche empfohlen, dabei mindestens einen Remotespeicherort zu wählen. Dieser ist vom Hostspeicher getrennt und wird mit unabhängigen Momentaufnahmen geschützt. Azure Files eignet sich hervorragend, um diese Anforderungen zu erfüllen.

Eine Azure Files-Dateifreigabe ist ein Speicher, der mithilfe von SMB- oder NFS-Protokollen (Vorschauversion) als reguläre Dateisystemkomponente an eine Linux- oder Windows-VM angefügt werden kann. 
   
Befolgen Sie die Anleitung im [Leitfaden für Azure Files mit Linux](../../../storage/files/storage-how-to-use-files-linux.md), um eine Azure Files-Dateifreigabe mithilfe des SMB 3.0-Protokolls (empfohlen) unter Linux einzurichten und als Archivprotokollspeicher zu verwenden. Wenn Sie die Einrichtung abgeschlossen haben, kehren Sie zu dieser Anleitung zurück und führen alle verbleibenden Schritte aus.

### <a name="prepare-the-databases"></a>Vorbereiten der Datenbanken

Dieser Schritt setzt voraus, dass Sie den [Schnellstart zum Erstellen einer Oracle-Datenbank](./oracle-database-quick-create.md) befolgt haben und eine Oracle-Instanz namens `oratest1` auf einer VM namens `vmoracle19c` verwenden, und dass Sie das Oracle-Standardskript „oraenv“ mit seiner Abhängigkeit von der Oracle-Standardkonfigurationsdatei „/etc/oratab“ verwenden, um Umgebungsvariablen in einer Shell-Sitzung einzurichten.

Führen Sie die folgenden Schritte für jede Datenbank auf der VM durch:

1. Ändern Sie den Benutzer in den Benutzer *oracle*:
 
   ```bash
    sudo su - oracle
    ```
    
1. Bevor Sie eine Verbindung herstellen, müssen Sie die Umgebungsvariable ORACLE_SID festlegen, indem Sie das Skript `oraenv` ausführen, das Sie zur Eingabe des ORACLE_SID-Namens auffordert:
    
    ```bash
    $ . oraenv
    ```

1.   Fügen Sie die Azure Files-Freigabe als zusätzliches Ziel für Datenbankarchiv-Protokolldateien hinzu.
     
     Dieser Schritt setzt voraus, dass Sie eine Azure Files-Freigabe auf der Linux-VM konfiguriert und eingebunden haben, z. B. unter einem Bereitstellungspunktsverzeichnis namens `/backup`.

     Erstellen Sie für jede auf der VM installierte Datenbank ein Unterverzeichnis, das nach Ihrer Datenbank-SID benannt ist, indem Sie z. B. das folgende Beispiel verwenden.
     
     In diesem Beispiel lautet der Name des Bereitstellungspunkts `/backup` und die SID lautet `oratest1`. Sie erstellen also das Unterverzeichnis `/backup/oratest1` und übertragen den Besitz an den Benutzer „oracle“. Ersetzen Sie **/backup/SID** durch den Bereitstellungspunktnamen und die Datenbank-SID. 

     ```bash
     sudo mkdir /backup/oratest1
     sudo chown oracle:oinstall /backup/oratest1
     ```
    
1. Stellen Sie eine Verbindung mit der Datenbank her:
    
   ```bash
   sqlplus / as sysdba
   ```

1.  Starten Sie die Datenbank, sofern sie nicht bereits ausgeführt wird. 
   
    ```bash
    SQL> startup
    ```
   
1. Legen Sie das erste Archivprotokollziel der Datenbank auf das Dateifreigabeverzeichnis fest, das Sie zuvor erstellt haben:

   ```bash
   SQL> alter system set log_archive_dest_1='LOCATION=/backup/oratest1' scope=both;
   ```

1. Definieren Sie die RPO (Recovery Point Objective) für die Datenbank.

    Für eine konsistente RPO muss die Häufigkeit berücksichtigt werden, mit der die Online-Wiederholungsprotokolldateien archiviert werden. Die Häufigkeit der Archivprotokollgenerierung hängt von diesen Faktoren ab:
    - Größe der Online-Wiederholungsprotokolldateien: Wenn eine Onlineprotokolldatei voll ist, wird sie ausgewechselt und archiviert. Je größer die Onlineprotokolldatei ist, desto länger dauert es, bis sie voll ist, wodurch sich die Häufigkeit der Archivgenerierung verringert.
    - Die Einstellung des Parameters ARCHIVE_LAG_TARGET bestimmt, wie viele Sekunden maximal vergehen dürfen, bevor die aktuelle Onlineprotokolldatei ausgewechselt und archiviert werden muss. 

    Online-Wiederholungsprotokolldateien in Oracle sind in der Regel relativ groß (1024 MB, 4096 MB, 8192 MB usw.), damit sie so selten wie möglich ausgewechselt und archiviert werden müssen und auch der zugehörige Prüfpunktvorgang so selten wie möglich ausgeführt werden muss. In einer stark ausgelasteten Datenbank müssen Umgebungsprotokolle wahrscheinlich dennoch alle paar Sekunden oder Minuten ausgewechselt und archiviert werden. In weniger aktiven Datenbanken können jedoch Stunden oder Tage vergehen, bevor die letzten Transaktionen archiviert werden. Dadurch sinkt die Archivierungshäufigkeit enorm. Sie sollten deshalb ARCHIVE_LAG_TARGET festlegen, um eine konsistente RPO sicherzustellen. Eine Einstellung von 5 Minuten (300 Sekunden) ist ein sinnvoller Wert für ARCHIVE_LAG_TARGET. So wird sichergestellt, dass jeder Datenbank-Wiederherstellungsvorgang innerhalb von 5 Minuten oder weniger nach dem Eintreten eines Fehlers ausgeführt werden kann.

    So legen Sie ARCHIVE_LAG_TARGET fest:

    ```bash 
    SQL> alter system set archive_lag_target=300 scope=both;
    ```

    Weitere Informationen zum Bereitstellen hochverfügbarer Oracle-Datenbanken in Azure ohne RPO finden Sie unter [Referenzarchitekturen für Oracle Database](./oracle-reference-architecture.md).

1.  Stellen Sie sicher, dass sich die Datenbank im ARCHIVELOG-Modus befindet, um Onlinesicherungen zu ermöglichen.

     Überprüfen Sie zuerst den Protokollarchivstatus:

     ```bash
     SQL> SELECT log_mode FROM v$database;

     LOG_MODE
     ------------
     NOARCHIVELOG
     ```

     Wenn Sie sich im NOARCHIVELOG-Modus befinden, führen Sie die folgenden Befehle aus:

     ```bash
     SQL> SHUTDOWN IMMEDIATE;
     SQL> STARTUP MOUNT;
     SQL> ALTER DATABASE ARCHIVELOG;
     SQL> ALTER DATABASE OPEN;
     SQL> ALTER SYSTEM SWITCH LOGFILE;
     ```
1. Erstellen Sie eine Tabelle zum Testen der Sicherungs- und Wiederherstellungsvorgänge:
   ```bash
   SQL> create user scott identified by tiger quota 100M on users;
   SQL> grant create session, create table to scott;
   SQL> connect scott/tiger
   SQL> create table scott_table(col1 number, col2 varchar2(50));
   SQL> insert into scott_table VALUES(1,'Line 1');
   SQL> commit;
   SQL> quit
   ```
## <a name="using-azure-backup"></a>Verwenden von Azure Backup 

Der Azure Backup-Dienst bietet einfache, sichere und kostengünstige Lösungen, um Ihre Daten zu sichern und aus der Microsoft Azure-Cloud wiederherzustellen. Azure Backup bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen von Originaldaten. Sicherungen werden in einem Recovery Services-Tresor mit integrierter Verwaltung von Wiederherstellungspunkten gespeichert. Konfiguration und Skalierung sind unkompliziert. Sicherungen werden außerdem optimiert und können bei Bedarf problemlos wiederhergestellt werden.

Der Dienst Azure Backup bietet ein [Framework](../../../backup/backup-azure-linux-app-consistent.md) zum Erreichen von Anwendungskonsistenz bei Sicherungen von Windows- und Linux-VMs für verschiedene Anwendungen wie Oracle und MySQL. Dies umfasst das Aufrufen eines Pre-Skripts (zum Stilllegen der Anwendungen), bevor eine Momentaufnahme der Datenträger erstellt wird, und eines Post-Skripts (zum Reaktivieren der Anwendungen) nach Erstellen der Momentaufnahme. 

Das Framework wurde jetzt dahingehend verbessert, dass verpackte Pre- und Post-Skripts für ausgewählte Anwendungen wie Oracle vom Azure Backup-Dienst bereitgestellt und auf dem Linux-Image vorinstalliert werden, sodass Sie nichts installieren müssen. Benutzer von Azure Backup müssen lediglich die Anwendung angeben, woraufhin bei der Azure-VM-Sicherung automatisch die entsprechenden Pre- und Post-Skripts aufgerufen werden. Für die Pflege der gepackten Pre- und Post-Skripts ist das Azure Backup-Team zuständig, sodass Benutzer sicher sein können, dass diese Skripts unterstützt werden und gültig sind. Derzeit sind die vom verbesserten Framework unterstützten Anwendungen *Oracle* und *MySQL*.

> [!Note]
> Das erweiterte Framework führt bei jeder Sicherung die Pre- und Post-Skripts für alle auf der VM installierten Oracle-Datenbanken aus. 
>
> Der Parameter `configuration_path` in der Datei **workload.conf** verweist auf den Speicherort der Oracle-Datei „/etc/oratab“ (oder eine benutzerdefinierte Datei, die der oratab-Syntax folgt). Weitere Informationen finden Sie unter [Einrichten anwendungskonsistenter Sicherungen](#set-up-application-consistent-backups).
> 
> Azure Backup führt die Skripts vor und nach der Sicherung für jede Datenbank aus, die in der Datei aufgeführt ist, auf die „configuration_path“ verweist, mit Ausnahme der Zeilen, die mit # (wird als Kommentar behandelt) oder +ASM (Oracle Automatic Storage Management-Instanz) beginnen.
> 
> Das erweiterte Azure Backup-Framework führt Onlinesicherungen von Oracle-Datenbanken durch, die im ARCHIVELOG-Modus betrieben werden. Die Pre- und Post-Skripts verwenden die Befehle ALTER DATABASE BEGIN/END BACKUP, um die Konsistenz der Anwendung sicherzustellen. 
>
> Datenbanken im NOARCHIVELOG-Modus müssen vor Beginn der Momentaufnahme fehlerfrei heruntergefahren werden, damit die Sicherung der Datenbank konsistent ist.


In diesem Abschnitt erstellen Sie mit dem Framework von Azure Backup anwendungskonsistente Momentaufnahmen Ihrer laufenden VM und der Oracle-Datenbanken. Die Datenbanken werden in den Sicherungsmodus versetzt, sodass eine transaktionskonsistente Onlinesicherung erfolgen kann, während Azure Backup eine Momentaufnahme der VM-Datenträger erstellt. Bei der Momentaufnahme handelt es sich um eine vollständige Kopie des Speichers und nicht um eine inkrementelle Momentaufnahme oder eine des Typs „Kopie bei Schreibvorgang“, sodass sie ein effektives Medium zur Wiederherstellung Ihrer Datenbank ist. Der Vorteil anwendungskonsistenter Momentaufnahmen von Azure Backup ist, dass sie unabhängig von der Größe Ihrer Datenbank äußerst schnell erstellt werden können. Eine Momentaufnahme kann für Wiederherstellungsvorgänge verwendet werden, sobald sie erstellt wurde. Sie müssen nicht warten, bis sie in den Recovery Services-Tresor übertragen wurde.

Führen Sie die folgenden Schritte aus, um die Datenbank mit Azure Backup zu sichern:

1. [Vorbereiten der Umgebung für eine anwendungskonsistente Sicherung](#prepare-the-environment-for-an-application-consistent-backup).
1. [Einrichten anwendungskonsistenter Sicherungen](#set-up-application-consistent-backups).
1. [Auslösen einer anwendungskonsistenten Sicherung der VM](#trigger-an-application-consistent-backup-of-the-vm).

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Vorbereiten der Umgebung für eine anwendungskonsistente Sicherung

> [!Note] 
> In Oracle-Datenbanken werden Auftragsrollen klar voneinander abgetrennt, um eine Aufgabentrennung nach dem Ansatz der geringsten Rechte umzusetzen. Hierfür werden separate Betriebssystemgruppen separaten Datenbank-Verwaltungsrollen zugeordnet. Betriebssystembenutzer können dann abhängig von ihrer Mitgliedschaft in Betriebssystemgruppen über unterschiedliche Datenbankberechtigungen verfügen. 
>
> Die Datenbankrolle `SYSBACKUP` (generischer Name: OSBACKUPDBA) wird verwendet, um eingeschränkte Berechtigungen zum Ausführen von Sicherungsvorgängen in der Datenbank zu erteilen, und ist für Azure Backup erforderlich.
>
> Während der Oracle-Installation sollte der Name der Betriebssystemgruppe `backupdba` lauten, die der SYSBACKUP-Rolle zugeordnet werden soll. Es kann jedoch jeder Name verwendet werden. Zuerst müssen Sie also den Namen der Betriebssystemgruppe festlegen, die die SYSBACKUP-Rolle von Oracle darstellt.

1. Wechseln Sie zum Benutzer *oracle*:
   ```bash
   sudo su - oracle
   ```

1. Legen Sie die Oracle-Umgebung fest:
   ```bash
   export ORACLE_SID=oratest1
   export ORAENV_ASK=NO
   . oraenv
   ```

1. Legen Sie den Namen der Betriebssystemgruppe fest, die die SYSBACKUP-Rolle von Oracle darstellt:
   ```bash
   grep "define SS_BKP" $ORACLE_HOME/rdbms/lib/config.c
   ```
   Die Ausgabe sieht etwa wie folgt aus: 
   ```output
   #define SS_BKP_GRP "backupdba"
   ```

   In der Ausgabe entspricht der in doppelte Anführungszeichen eingeschlossene Wert (in diesem Beispiel `backupdba`) dem Namen der Linux-Betriebssystemgruppe, für die die SYSBACKUP-Rolle von Oracle extern authentifiziert wird. Notieren Sie sich diesen Wert.

1. Überprüfen Sie mit dem folgenden Befehl, ob die Betriebssystemgruppe vorhanden ist. Ersetzen Sie \<group name\> durch den Wert (ohne Anführungszeichen), der vom vorherigen Befehl zurückgegeben wurde:
   ```bash
   grep <group name> /etc/group
   ```
   Die Ausgabe sieht in etwa wie folgt aus. In diesem Beispiel wird `backupdba` verwendet: 
   ```output
   backupdba:x:54324:oracle
   ```

   > [!IMPORTANT] 
   > Wenn die Ausgabe nicht mit dem in Schritt 3 abgerufenen Wert für die Oracle-Betriebssystemgruppe übereinstimmt, müssen Sie die Betriebssystemgruppe erstellen, die die SYSBACKUP-Rolle von Oracle darstellt. Ersetzen Sie `<group name>` durch den in Schritt 3 abgerufenen Gruppennamen:
   >   ```bash
   >   sudo groupadd <group name>
   >   ```

1. Erstellen Sie in der Betriebssystemgruppe, die Sie in den vorherigen Schritten überprüft oder erstellt haben, den neuen Sicherungsbenutzer `azbackup`. Ersetzen Sie \<group name\> durch den Namen der überprüften Gruppe:

   ```bash
   sudo useradd -g <group name> azbackup
   ```

1. Richten Sie die externe Authentifizierung für den neuen Benutzer für die Sicherung ein. 

   Der Sicherungsbenutzer `azbackup` muss in der Lage sein, über eine externe Authentifizierung auf die Datenbank zuzugreifen, damit kein Kennwort abgefragt wird. Hierzu müssen Sie einen Datenbankbenutzer erstellen, der sich extern über `azbackup` authentifiziert. Die Datenbank verwendet ein Präfix für den Benutzernamen, das Sie ermitteln müssen.

   Führen Sie auf jeder auf der VM installierten Datenbank die folgenden Schritte durch:
 
   Melden Sie sich mit sqlplus bei der Datenbank an, und überprüfen Sie die Standardeinstellungen für die externe Authentifizierung:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   Die Ausgabe sollte diesem Beispiel ähneln, in dem das Präfix für den Datenbank-Benutzernamen `ops$` lautet: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Erstellen Sie den Datenbankbenutzer ***ops$azbackup*** für die externe Authentifizierung des Benutzers `azbackup`, und erteilen Sie diesem die sysbackup-Berechtigungen:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Wenn der Fehler `ORA-46953: The password file is not in the 12.2 format.` beim Ausführen der Anweisung `GRANT` auftritt, können Sie die orapwd-Datei in das 12.2-Format konvertieren, indem Sie diese Schritte befolgen. Dieser Vorgang muss für jede Oracle-Datenbank auf der VM erfolgen:
   >
   > 1. Beenden Sie sqlplus.
   > 1. Geben Sie der Kennwortdatei im alten Format einen neuen Namen.
   > 1. Migrieren Sie die Kennwortdatei.
   > 1. Entfernen Sie die alte Datei.
   > 1. Führen Sie die folgenden Befehle aus:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapworatest1 $ORACLE_HOME/dbs/orapworatest1.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapworatest1 input_file=$ORACLE_HOME/dbs/orapworatest1.tmp
   >    rm $ORACLE_HOME/dbs/orapworatest1.tmp
   >    ```
   >
   > 1. Führen Sie in sqlplus den Vorgang `GRANT` erneut aus.
   >
   
1. Erstellen Sie eine gespeicherte Prozedur zum Protokollieren von Sicherungsmeldungen im Warnungsprotokoll der Datenbank:

   Führen Sie die folgenden Schritte für jede auf der VM installierte Datenbank durch:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```

### <a name="set-up-application-consistent-backups"></a>Einrichten anwendungskonsistenter Sicherungen  

1. Wechseln Sie zunächst zum root-Benutzer:
   ```bash
   sudo su -
   ```

1. Suchen Sie nach dem Ordner „/etc/azure“. Wenn er nicht vorhanden ist, erstellen Sie das Arbeitsverzeichnis für die anwendungskonsistente Sicherung:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      mkdir /etc/azure
   fi
   ```

1. Suchen Sie im Ordner nach „workload.conf“. Wenn er nicht vorhanden ist, erstellen Sie im Verzeichnis */etc/azure* eine Datei mit dem Namen *workload.conf* und mit dem folgenden Inhalt, der mit `[workload]` beginnen muss. Wenn die Datei bereits vorhanden ist, bearbeiten Sie einfach die Felder, sodass sie mit dem folgenden Inhalt übereinstimmen. Andernfalls wird mit dem folgenden Befehl die Datei erstellt und der Inhalt aufgefüllt:

   ```bash
   echo "[workload]
   workload_name = oracle
   configuration_path = /etc/oratab
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

   > [!IMPORTANT]
   > Von „workload.conf“ wird folgendes Format verwendet:
   > * Der Parameter **workload_name** wird von Azure Backup verwendet, um den Workloadtyp der Datenbank zu bestimmen. In diesem Fall ermöglicht die Einstellung auf Oracle, dass Azure Backup die richtigen Befehle für Prä- und Post-Konsistenzprüfungen für Oracle-Datenbanken ausführt.
   > * Der Parameter **timeout** gibt die maximale Zeit in Sekunden an, die jede Datenbank zum Anfertigen von Speichermomentaufnahmen hat.
   > * Der Parameter **linux_user** gibt das Linux-Benutzerkonto an, das von Azure Backup zum Ausführen von Datenbank-Stilllegungsvorgängen verwendet wird. Sie haben diesen Benutzer (`azbackup`) bereits erstellt.
   > * Der Parameter **configuration_path** gibt den absoluten Pfadnamen für eine Textdatei auf der VM an, in der jede Zeile eine Datenbankinstanz enthält, die auf der VM ausgeführt wird. Dabei handelt es sich in der Regel die Datei `/etc/oratab`, die von Oracle während der Datenbankinstallation generiert wird. Auch eine andere Datei mit einem anderen Namen ist jedoch möglich, sofern diese Formatregeln befolgt werden:
   >   * Eine Textdatei, bei der jedes Feld durch das Doppelpunktzeichen `:` getrennt ist
   >   * Das erste Feld in jeder Zeile entspricht dem Namen einer ORACLE_SID.
   >   * Das zweite Feld in jeder Zeile ist der Name des absoluten Pfads für den ORACLE_HOME-Wert für diese ORACLE_SID.
   >   * Sämtlicher Text, der diesen ersten beiden Feldern folgt, wird ignoriert.
   >   * Wenn die Zeile mit dem Hashzeichen `#` beginnt, wird die gesamte Zeile als Kommentar interpretiert und ignoriert.
   >   * Wenn das erste Feld den Wert `+ASM` enthält, der eine automatische Speicherverwaltungsinstanz bezeichnet, wird es ignoriert. 


### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Auslösen einer anwendungskonsistenten Sicherung der VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Wechseln Sie im Azure-Portal zu Ihrer Ressourcengruppe **rg-oracle**, und klicken Sie auf Ihren virtuellen Computer **vmoracle19c**.

1.  Erstellen Sie auf dem Blatt **Sicherung** in der Ressourcengruppe **rg-oracle** einen neuen **Recovery Services-Tresor** mit dem Namen **myVault**.
    Wählen Sie für **Sicherungsrichtlinie auswählen** die Option **(neu) DailyPolicy**. Wenn Sie die Häufigkeit der Sicherung oder den Aufbewahrungszeitraum ändern möchten, wählen Sie stattdessen **Neue Richtlinie erstellen** aus.

    ![Seite zum Hinzufügen von Recovery Services-Tresoren](./media/oracle-backup-recovery/recovery-service-01.png)

1.  Klicken Sie zum Fortfahren auf **Sicherung aktivieren**.

    > [!IMPORTANT]
    > Nachdem Sie auf **Sicherung aktivieren** geklickt haben, wird der Sicherungsprozess erst gestartet, wenn die geplante Zeit abgelaufen ist. Führen Sie den nächsten Schritt aus, um eine sofortige Sicherung einzurichten.

1. Klicken Sie auf der Seite „Ressourcengruppe“ auf den neu erstellten Recovery Services-Tresor **myVault**. Hinweis: Möglicherweise müssen Sie die Seite aktualisieren, damit er angezeigt wird.

1.  Wählen Sie auf dem Blatt **myVault – Sicherungselemente** unter **Anzahl von Sicherungselementen** die Anzahl der Sicherungselemente aus.

    ![Seite mit myVault-Details zu Recovery Services-Tresoren](./media/oracle-backup-recovery/recovery-service-02.png)

1.  Klicken Sie auf dem Blatt **Sicherungselemente (Virtueller Azure-Computer)** rechts auf die Schaltfläche mit den Auslassungspunkten (**...**) und anschließend auf **Jetzt sichern**.

    ![Befehl „Jetzt sichern“ für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-03.png)

1.  Übernehmen Sie den Standardwert von „Sicherung aufbewahren bis“, und klicken Sie auf die Schaltfläche **OK**. Warten Sie, bis der Sicherungsvorgang abgeschlossen ist. 

    Klicken Sie auf **Sicherungsaufträge**, um den Status des Sicherungsauftrags anzuzeigen.

    ![Seite mit Aufträgen zu Recovery Services-Tresoren](./media/oracle-backup-recovery/recovery-service-04.png)

    Der Status des Sicherungsauftrags wird im folgenden Bild dargestellt:

    ![Seite mit Aufträgen zu Recovery Services-Tresoren mit Status](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Beachten Sie, dass die Ausführung der Momentaufnahme zwar nur Sekunden dauert, die Übertragung in den Tresor jedoch einige Zeit in Anspruch nehmen kann und der Sicherungsauftrag erst nach Abschluss der Übertragung beendet ist.

1. Geben Sie für eine anwendungskonsistente Sicherung alle Fehler in der Protokolldatei an. Die Protokolldatei befindet sich in „/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log“.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Erstellen Sie einen Recovery Services-Tresor:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

1. Aktivieren Sie den Sicherungsschutz für die VM:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

1. Lösen Sie jetzt eine Sicherung aus, anstatt zu warten, bis die Sicherung gemäß Standardzeitplan (05:00 Uhr UTC) ausgelöst wird: 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Mit `az backup job list` und `az backup job show` können Sie den Status des Sicherungsauftrags überwachen.

---

## <a name="recovery"></a>Wiederherstellung

Führen Sie die folgenden Schritte aus, um eine Datenbank wiederherzustellen:

1. [Entfernen Sie die Datenbankdateien](#remove-the-database-files).
1. [Generieren eines Wiederherstellungsskripts aus dem Recovery Services-Tresor](#generate-a-restore-script-from-the-recovery-services-vault).
1. [Einbinden des Wiederherstellungspunkts](#mount-the-restore-point).
1. [Durchführen der Wiederherstellung](#perform-recovery).

### <a name="remove-the-database-files"></a>Entfernen der Datenbankdateien 

Weiter unten in diesem Artikel erfahren Sie, wie der Wiederherstellungsprozess getestet werden kann. Bevor Sie den Wiederherstellungsprozess testen können, müssen Sie die Datenbankdateien entfernen.

1.  Wechseln Sie zurück zum Benutzer „oracle“:
    ```bash
    su - oracle
    ```

1. Fahren Sie die Oracle-Instanz herunter:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

1.  Entfernen Sie die Datenbankdatendateien und Steuerungsdateien, um einen Fehler zu simulieren:

    ```bash
    cd /u02/oradata/ORATEST1
    rm -f *.dbf *.ctl
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Generieren eines Wiederherstellungsskripts aus dem Recovery Services-Tresor

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wechseln Sie im Azure-Portal im Recovery Services-Tresor zum Element *myVault*, und wählen Sie es aus.

    ![myVault-Sicherungselemente für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-06.png)

1. Wählen Sie auf dem Blatt **Übersicht** die Option **Sicherungselemente** und anschließend die Option **Virtueller Azure-Computer** aus, die für „Anzahl von Sicherungselementen“ nicht 0 enthalten sollte.

    ![Anzahl von Sicherungselementen auf virtuellem Azure-Computer für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-07.png)

1. Auf der Seite „Sicherungselemente (Azure Virtual Machines)“ ist Ihre VM **vmoracle19c** aufgeführt. Klicken Sie rechts auf die Auslassungspunkte, um das Menü einzublenden, und wählen Sie **Dateiwiederherstellung** aus.

    ![Screenshot der Seite zur Dateiwiederherstellung für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-08.png)

1. Klicken Sie im Bereich **Dateiwiederherstellung (Vorschauversion)** auf **Skript herunterladen**. Speichern Sie dann die heruntergeladene Datei (.py) in einem Ordner auf dem Clientcomputer. Zum Ausführen des Skripts wird ein Kennwort generiert. Kopieren Sie das Kennwort zur späteren Verwendung in eine Datei. 

    ![Speichern von Optionen durch Herunterladen der Skriptdatei](./media/oracle-backup-recovery/recovery-service-09.png)

1. Kopieren Sie die PY-Datei auf die VM.

    Im folgenden Beispiel wird dargestellt, wie Sie die Datei mit einem SCP-Befehl (Secure Copy) auf den virtuellen Computer verschieben. Sie können die Inhalte auch in die Zwischenablage kopieren und sie anschließend in eine neue Datei einfügen, die auf dem virtuellen Computer eingerichtet ist.

    > [!IMPORTANT]
    > Stellen Sie im folgenden Beispiel sicher, dass Sie die IP-Adresse und die Ordnerwerte aktualisiert haben. Die Werte müssen dem Ordner zugeordnet werden können, in dem die Datei gespeichert ist.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Auflisten der Wiederherstellungspunkte für Ihre VM „az backup recovery point list“ aus. In diesem Beispiel wird der neueste Wiederherstellungspunkt für die VM vmoracle19c ausgewählt, die durch den Recovery Services-Tresor myVault geschützt wird:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Um das Skript abzurufen, das eine Verbindung des Wiederherstellungspunkts mit Ihrer VM herstellt bzw. ihn dort einbindet, verwenden Sie az backup restore files mount-rp. Im folgenden Beispiel wird das Skript für die VM vmoracle19c abgerufen, die im Recovery Services-Tresor myVault geschützt wird.

Ersetzen Sie myRecoveryPointName mit dem Namen des Wiederherstellungspunkts, den Sie im vorherigen Befehl abgerufen haben:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Das Skript wird heruntergeladen, und ein Kennwort wird wie im folgenden Beispiel angezeigt:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Kopieren Sie die PY-Datei auf die VM.

Im folgenden Beispiel wird dargestellt, wie Sie die Datei mit einem SCP-Befehl (Secure Copy) auf den virtuellen Computer verschieben. Sie können die Inhalte auch in die Zwischenablage kopieren und sie anschließend in eine neue Datei einfügen, die auf dem virtuellen Computer eingerichtet ist.

> [!IMPORTANT]
> Stellen Sie im folgenden Beispiel sicher, dass Sie die IP-Adresse und die Ordnerwerte aktualisiert haben. Die Werte müssen dem Ordner zugeordnet werden können, in dem die Datei gespeichert ist.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Bereitstellen des Wiederherstellungspunkts

1. Wechseln Sie zum Benutzer root:
   ```bash
   sudo su -
   ``````
1. Erstellen Sie einen Bereitstellungspunkt für die Wiederherstellung, und kopieren Sie das Skript in diesen.

    Im folgenden Beispiel erstellen Sie das Verzeichnis */restore*, in dem die Momentaufnahme bereitgestellt werden soll. Verschieben die Datei in das Verzeichnis, und ändern die Datei so, dass sie dem Benutzer „root“ gehört und ausführbar wird.

    ```bash 
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Führen Sie nun das Skript aus, um die Sicherung wiederherzustellen. Sie werden aufgefordert, das im Azure-Portal generierte Kennwort anzugeben. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    Im folgenden Beispiel wird dargestellt, was Ihnen nach der Ausführung des vorherigen Skripts angezeigt werden sollte. Geben Sie **Y** ein, wenn Sie zum Fortsetzen des Vorgangs aufgefordert werden.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

1. Der Zugriff auf die bereitgestellten Volumes wurde bestätigt.

    Geben Sie zum Beenden **q** ein, und suchen Sie anschließend nach den bereitgestellten Volumes. Geben Sie **df -h** ein, um an einer Eingabeaufforderung eine Liste mit den hinzugefügten Volumes zu erstellen.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Ausführen der Wiederherstellung
Führen Sie die folgenden Schritte für jede Datenbank auf der VM durch:

1. Stellen Sie die fehlenden Datenbankdateien an ihrem Speicherort wieder her:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/oradata/ORATEST1
    cp * /u02/oradata/ORATEST1
    cd /u02/oradata/ORATEST1
    chown -R oracle:oinstall *
    ```
1. Wechseln Sie zurück zum Benutzer „oracle“:
   ```bash
   sudo su - oracle
   ```
1. Starten Sie die Datenbankinstanz, und binden Sie die Steuerdatei zum Lesen ein:
   ```bash
   sqlplus / as sysdba
   SQL> startup mount
   SQL> quit
   ```

1. Stellen Sie mit sysbackup eine Verbindung mit der Datenbank her:
   ```bash
   sqlplus / as sysbackup
   ```
1. Initiieren Sie die automatische Datenbankwiederherstellung:

   ```bash
   SQL> recover automatic database until cancel using backup controlfile;
   ```
   > [!IMPORTANT]
   > Sie müssen die USING BACKUP CONTROLFILE-Syntax verwenden, um dem Befehl RECOVER AUTOMATIC DATABASE mitzuteilen, dass die Wiederherstellung nicht bei der Oracle-Systemänderungsnummer (System Change Number, SCN) angehalten werden soll, die in der wiederhergestellten Datenbank-Steuerungsdatei aufgezeichnet wurde. Die wiederhergestellte Datenbank-Steuerungsdatei und der Rest der Datenbank waren eine Momentaufnahme, und die darin gespeicherte SCN wurde zum Zeitpunkt der Momentaufnahme erfasst. Nach diesem Punkt wurden möglicherweise weitere Transaktionen aufgezeichnet, und die Wiederherstellung soll für den Zeitpunkt erfolgen, zu dem die letzte Transaktion an die Datenbank committet wurde.

   Wenn die Wiederherstellung erfolgreich abgeschlossen wurde, wird die Meldung `Media recovery complete` angezeigt. Wenn Sie jedoch die BACKUP CONTROLFILE-Klausel verwenden, ignoriert der Wiederherstellungsbefehl Onlineprotokolldateien, und möglicherweise sind Änderungen am aktuellen Online-Wiederherstellungsprotokoll erforderlich, um die Zeitpunktwiederherstellung abzuschließen. In dieser Situation werden Meldungen wie die folgenden angezeigt:

   ```output
   SQL> recover automatic database until cancel using backup controlfile;
   ORA-00279: change 2172930 generated at 04/08/2021 12:27:06 needed for thread 1
   ORA-00289: suggestion :
   /u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc
   ORA-00280: change 2172930 for thread 1 is in sequence #13
   ORA-00278: log file
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc' no
   longer needed for this recovery
   ORA-00308: cannot open archived log
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc'
   ORA-27037: unable to obtain file status
   Linux-x86_64 Error: 2: No such file or directory
   Additional information: 7

   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   ```
   
   > [!IMPORTANT]
   > Wenn das aktuelle Online-Wiederherstellungsprotokoll verloren gegangen oder beschädigt ist und nicht verwendet werden kann, können Sie die Wiederherstellung an dieser Stelle abbrechen. 

   Sie können dieses Problem beheben, indem Sie ermitteln, welches das aktuelle Onlineprotokoll ist, das nicht archiviert wurde, und den vollqualifizierten Dateinamen in der Eingabeaufforderung angeben.


   Stellen Sie eine neue SSH-Verbindung her: 
   ```bash
   ssh azureuser@<IP Address>
   ```
   Wechseln Sie zum Benutzer „oracle“, und legen Sie die Oracle-SID fest:
   ```bash
   sudo su - oracle
   export ORACLE_SID=oratest1
   ```
   
   Stellen Sie eine Verbindung mit der Datenbank her, und führen Sie die folgende Abfrage aus, um die Onlineprotokolldatei zu finden: 
   ```bash
   sqlplus / as sysdba
   SQL> column member format a45
   SQL> set linesize 500  
   SQL> select l.SEQUENCE#, to_char(l.FIRST_CHANGE#,'999999999999999') as CHK_CHANGE, l.group#, l.archived, l.status, f.member
   from v$log l, v$logfile f
   where l.group# = f.group#;
   ```

   Die Ausgabe sieht etwa wie folgt aus: 
   ```output
   SEQUENCE#  CHK_CHANGE           GROUP# ARC STATUS            MEMBER
   ---------- ---------------- ---------- --- ---------------- ---------------------------------------------
           13          2172929          1 NO  CURRENT          /u02/oradata/ORATEST1/redo01.log
           12          2151934          3 YES INACTIVE         /u02/oradata/ORATEST1/redo03.log
           11          2071784          2 YES INACTIVE         /u02/oradata/ORATEST1/redo02.log
   ```
   Kopieren Sie den Protokolldateipfad und den Dateinamen für das aktuelle Onlineprotokoll, in diesem Beispiel `/u02/oradata/ORATEST1/redo01.log`. Wechseln Sie zurück zur SSH-Sitzung, in der der Wiederherstellungsbefehl ausgeführt wird, geben Sie die Informationen für die Protokolldatei ein, und drücken Sie die EINGABETASTE:

   ```bash
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   ```

   Es sollte angezeigt werden, dass die Protokolldatei angewendet wird und die Wiederherstellung abgeschlossen ist. Geben Sie CANCEL ein, um den Wiederherstellungsbefehl zu beenden:
   ```output
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   Log applied.
   Media recovery complete.
   ```

1. Öffnen Sie die Datenbank:
   
   > [!IMPORTANT]
   > Die RESETLOGS-Option ist erforderlich, wenn der RECOVER-Befehl die Option USING BACKUP CONTROLFILE verwendet. RESETLOGS erstellt eine neue Inkarnation der Datenbank, indem der Wiederholungsverlauf vollständig zurückgesetzt wird, da nicht bestimmt werden kann, wie viel der vorherigen Datenbankinkarnation bei der Wiederherstellung übersprungen wurde.

   ```bash
   SQL> alter database open resetlogs;
   ```

   
1. Prüfen Sie, ob der Datenbankinhalt vollständig wiederhergestellt wurde:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

1. Heben Sie die Bereitstellung des Wiederherstellungspunkts auf.

   Wenn alle Datenbanken auf der VM erfolgreich wiederhergestellt worden sind, können Sie die Bereitstellung des Wiederherstellungspunkts aufheben. Dies kann auf der VM mithilfe des Befehls `unmount` oder im Azure-Portal über das Blatt zur Dateiwiederherstellung erfolgen. Sie können die Bereitstellung der Wiederherstellungsvolumes auch aufheben, indem Sie das Python-Skript mit der Option **-clean** erneut ausführen.

   Auf der VM mithilfe der Aufhebung der Bereitstellung:
   ```bash
   sudo umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    Klicken Sie im Azure-Portal auf dem Blatt **Dateiwiederherstellung (Vorschauversion)** auf **Bereitstellung der Datenträger aufheben**.

    ![Befehl zum Aufheben der Bereitstellung von Datenträgern](./media/oracle-backup-recovery/recovery-service-10.png)
    


## <a name="restore-the-entire-vm"></a>Wiederherstellen des gesamten virtuellen Computers

Statt die aus den Recovery Services-Tresoren gelöschten Dateien wiederherzustellen, können Sie den gesamten virtuellen Computer wiederherstellen.

Führen Sie diese Schritte aus, um die gesamte VM wiederherzustellen:

1. [Beenden und löschen Sie die VM](#stop-and-delete-the-vm).
1. [Stellen Sie die VM wieder her](#recover-the-vm).
1. [Legen Sie die öffentliche IP-Adresse fest](#set-the-public-ip-address).
1. [Führen Sie die Datenbankwiederherstellung durch](#perform-database-recovery).

### <a name="stop-and-delete-the-vm"></a>Beenden und Löschen der VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wechseln Sie im Azure-Portal zur VM **vmoracle19c**, und wählen Sie dann **Beenden** aus.

1. Wenn die VM nicht mehr läuft, wählen Sie **Löschen** und dann **Ja** aus.

   ![Befehl „Löschen“ für Tresore](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Beenden Sie die VM, und heben Sie die Zuordnung auf:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

1. Löschen Sie den virtuellen Computer. Geben Sie bei Aufforderung „y“ ein:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Wiederherstellen des virtuellen Computers

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Erstellen Sie ein Speicherkonto für den Stagingprozess im Azure-Portal.

   1. Wählen Sie im Azure-Portal **+ Ressource erstellen** aus. Suchen Sie nach **Speicherkonto**, und wählen Sie diese Option aus.
    
      ![Screenshot der Erstellung einer Ressource.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Wählen Sie auf der Seite „Speicherkonto erstellen“ Ihre vorhandene Ressourcengruppe **rg-oracle** aus. Nennen Sie Ihr Speicherkonto **oracrestore**, und wählen Sie **Storage V2 (generalpurpose v2)** als „Kontotyp“. Ändern Sie „Replikation“ in **Lokal redundanter Speicher (LRS)** , und legen Sie „Leistung“ auf **Standard** fest. Stellen Sie sicher, dass der Standort auf die Region aller anderen Ressourcen in der Ressourcengruppe festgelegt ist. 
    
      ![Seite „Speicherkonto hinzufügen“](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Klicken Sie auf Überprüfen + erstellen und dann auf Erstellen.

1. Wechseln Sie im Azure-Portal im Recovery Services-Tresor zum Element *myVault*, und klicken Sie darauf.

    ![myVault-Sicherungselemente für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-06.png)
    
1.  Wählen Sie auf dem Blatt **Übersicht** die Option **Sicherungselemente** und anschließend die Option **Virtueller Azure-Computer** aus, die für „Anzahl von Sicherungselementen“ nicht 0 enthalten sollte.

    ![Anzahl von Sicherungselementen auf virtuellem Azure-Computer für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-07.png)

1.  Auf der Seite „Sicherungselemente (Azure Virtual Machines)“ ist Ihre VM **vmoracle19c** aufgeführt. Klicken Sie auf den Namen der VM.

    ![Seite zur Wiederherstellung des virtuellen Computers](./media/oracle-backup-recovery/recover-vm-02.png)

1.  Wählen Sie auf dem Blatt **vmoracle19c** einen Wiederherstellungspunkt mit dem Konsistenztyp **Anwendungskonsistent**, und klicken Sie auf die Auslassungspunkte ( **...** ) auf der rechten Seite, um das Menü einzublenden.  Klicken Sie im Menü auf **VM wiederherstellen**.

    ![Befehl „Virtuellen Computer wiederherstellen“](./media/oracle-backup-recovery/recover-vm-03.png)

1.  Wählen Sie auf dem Blatt **Virtuellen Computer wiederherstellen** die Optionen **Neu erstellen** und **Neuen virtuellen Computer erstellen**. Geben Sie den VM-Namen **vmoracle19c** ein, und wählen Sie das VNet **vmoracle19cVNET**. Das Subnetz wird anhand Ihrer VNet-Auswahl automatisch für Sie ausgefüllt. Der Prozess der VM-Wiederherstellung erfordert ein Azure-Speicherkonto in derselben Ressourcengruppe und Region. Sie können das Speicherkonto **orarestore** wählen, das Sie zuvor eingerichtet haben.

    ![Werte der Wiederherstellungskonfiguration](./media/oracle-backup-recovery/recover-vm-04.png)

1.  Klicken Sie auf die Schaltfläche **Wiederherstellen**, um die VM wiederherzustellen.

1.  Klicken Sie auf **Aufträge** und dann auf **Sicherungsaufträge**, um den Status des Wiederherstellungsprozesses anzuzeigen.

    ![Befehl „Status des Sicherungsauftrags“](./media/oracle-backup-recovery/recover-vm-05.png)

    Klicken Sie auf den Wiederherstellungsvorgang **In Bearbeitung**, um den Status des Wiederherstellungsprozesses anzuzeigen:

    ![Status des Wiederherstellungsprozesses](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Einrichten von Speicherkonto und Dateifreigabe die folgenden Befehle in der Azure CLI aus.

1. Erstellen Sie das Speicherkonto entsprechend der Ressourcengruppe und Region Ihrer VM:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

1. Rufen Sie die Liste der verfügbaren Wiederherstellungspunkte ab. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

1. Stellen Sie den Wiederherstellungspunkt im Speicherkonto wieder her. Ersetzen Sie `<myRecoveryPointName>` durch einen Wiederherstellungspunkt aus der im vorherigen Schritt generierten Liste:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

1. Rufen Sie die Details des Wiederherstellungsauftrags ab. Der folgende Befehl ruft weitere Details zum ausgelösten Wiederherstellungsauftrag ab, einschließlich des Namens, der zum Abrufen des Vorlagen-URI benötigt wird. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   Die Ausgabe sieht etwa wie `(Note down the name of the restore job)` aus:

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

1. Ruft die Details des URI ab, der für die Neuerstellung der VM verwendet werden soll. Ersetzen Sie den Namen des Wiederherstellungsauftrags aus dem vorherigen Schritt durch `<RestoreJobName>`.

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   Die Ausgabe ist in etwa wie folgt:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Der Name der Vorlage, der sich am Ende des Vorlagenblob-URI befindet und in diesem Beispiel `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` lautet, und der Name des Blobcontainers, der `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` lautet, werden aufgeführt. 

   Weisen Sie mit diesen Werten im folgenden Befehl Variablen als Vorbereitung auf die Erstellung der VM zu. Ein SAS-Schlüssel mit einer Gültigkeitsdauer von 30 Minuten wird für den Speichercontainer generiert.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Stellen Sie nun die Vorlage bereit, um die VM zu erstellen.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Sie werden aufgefordert, einen Namen für die VM anzugeben.

---

### <a name="set-the-public-ip-address"></a>Festlegen der öffentlichen IP-Adresse

Nachdem die VM wiederhergestellt wurde, müssen Sie der neuen VM die ursprüngliche IP-Adresse erneut zuweisen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Wechseln Sie im Azure-Portal zu Ihrer VM **vmoracle19c**. Sie werden feststellen, dass ihr eine neue öffentliche IP-Adresse und NIC zugewiesen wurde, ähnlich wie vmoracle19c-nic-XXXXXXXXXXXX. Aber sie hat keine DNS-Adresse. Wenn die ursprüngliche VM gelöscht wurde, bleiben deren öffentliche IP-Adresse und NIC erhalten und werden in den nächsten Schritten der neuen VM wieder zugewiesen.

    ![Liste der öffentlichen IP-Adressen](./media/oracle-backup-recovery/create-ip-01.png)

1.  Beenden des virtuellen Computers

    ![Erstellen der IP-Adresse](./media/oracle-backup-recovery/create-ip-02.png)

1.  Navigieren Sie zu **Netzwerk**.

    ![Zuordnen einer IP-Adresse](./media/oracle-backup-recovery/create-ip-03.png)

1.  Klicken Sie auf **Netzwerkschnittstelle anfügen**. Wählen Sie die ursprüngliche NIC **vmoracle19cVMNic aus, der die ursprüngliche öffentliche IP-Adresse nach wie vor zugeordnet ist, und klicken Sie auf **OK**.

    ![Auswählen von Ressourcentyp und Netzschnittstellenwerten](./media/oracle-backup-recovery/create-ip-04.png)

1.  Nun müssen Sie die beim Wiederherstellungsvorgang der VM erstellte NIC trennen, da sie als primäre Schnittstelle konfiguriert ist. Klicken Sie auf **Netzwerkschnittstelle trennen**. Wählen Sie die neue NIC ähnlich wie **vmoracle19c-NIC-xxxxxxxxxxxx** aus, und klicken Sie dann auf **OK**.

    ![Screenshot, der zeigt, wo „Netzwerkschnittstelle trennen“ ausgewählt werden soll](./media/oracle-backup-recovery/create-ip-05.png)
    
    Ihre neu erstellte VM verfügt nun über die ursprüngliche NIC, die mit der ursprünglichen IP-Adresse und den Regeln der Netzwerksicherheitsgruppe verknüpft ist.
    
    ![Wert der IP-Adresse](./media/oracle-backup-recovery/create-ip-06.png)
    
1.  Kehren Sie zur **Übersicht** zurück, und klicken Sie auf **Starten**. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Beenden Sie die VM, und heben Sie die Zuordnung auf:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

1. Auflisten der aktuellen bei der Wiederherstellung generierten VM-NIC

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   Die Ausgabe sieht ähnlich aus wie diese, die den bei der Wiederherstellung generierten NIC-Namen als `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf` auflistet.

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

1. Fügen Sie die ursprüngliche NIC an, die einen Namen des Typs `<VMName>VMNic` haben sollte, in diesem Fall `vmoracle19cVMNic`. Die ursprüngliche öffentliche IP-Adresse ist nach wie vor an diese NIC angefügt und wird für die VM wiederhergestellt, sobald die NIC erneut angefügt wird. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

1. Trennen der bei der Wiederherstellung generierten NIC

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```
1. Starten Sie die VM:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="perform-database-recovery"></a>Durchführen der Datenbankwiederherstellung
Stellen Sie zunächst erneut eine Verbindung mit dem virtuellen Computer her:

```azurecli
ssh azureuser@<publicIpAddress>
```

Wenn die gesamte VM wiederhergestellt wurde, ist es wichtig, jede einzelne Datenbank auf der VM wiederherzustellen, indem Sie die folgenden Schritte für jede einzelne Datenbank durchführen:

1. Es kann sein, dass die Instanz läuft, da beim Autostart versucht wurde, die Datenbank beim Hochfahren der VM zu starten. Da die Datenbank jedoch wiederhergestellt werden muss und sich wahrscheinlich nur in der Bereitstellungsphase befindet, erfolgt zunächst ein vorbereitendes Herunterfahren. Daraufhin folgt der Start der Bereitstellungsphase.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    ```
    
1. Durchführen der Datenbankwiederherstellung
   > [!IMPORTANT]
   > Sie müssen die USING BACKUP CONTROLFILE-Syntax verwenden, um dem Befehl RECOVER AUTOMATIC DATABASE mitzuteilen, dass die Wiederherstellung nicht bei der Oracle-Systemänderungsnummer (System Change Number, SCN) angehalten werden soll, die in der wiederhergestellten Datenbank-Steuerungsdatei aufgezeichnet wurde. Die wiederhergestellte Datenbank-Steuerungsdatei und der Rest der Datenbank waren eine Momentaufnahme, und die darin gespeicherte SCN wurde zum Zeitpunkt der Momentaufnahme erfasst. Nach diesem Punkt wurden möglicherweise weitere Transaktionen aufgezeichnet, und die Wiederherstellung soll für den Zeitpunkt erfolgen, zu dem die letzte Transaktion an die Datenbank committet wurde.
    
    ```bash
    SQL> recover automatic database using backup controlfile until cancel;
    ```
   Wenn die letzte verfügbare Archivprotokolldatei angewendet wurde, geben Sie `CANCEL` ein, um die Wiederherstellung zu beenden.

1. Öffnen Sie die Datenbank:
   > [!IMPORTANT]
   > Die RESETLOGS-Option ist erforderlich, wenn der RECOVER-Befehl die Option USING BACKUP CONTROLFILE verwendet. RESETLOGS erstellt eine neue Inkarnation der Datenbank, indem der Wiederholungsverlauf vollständig zurückgesetzt wird, da nicht bestimmt werden kann, wie viel der vorherigen Datenbankinkarnation bei der Wiederherstellung übersprungen wurde.
   
    ```bash 
    SQL> alter database open resetlogs;
    ```
   

1. Prüfen Sie, ob der Datenbankinhalt wiederhergestellt wurde:

    ```bash
    SQL> select * from scott.scott_table;
    ```

Die Sicherung und Wiederherstellung der Oracle Database 19c-Datenbank auf einem virtuellen Azure Linux-Computer ist nun abgeschlossen.

Weitere Informationen zu Oracle-Befehlen und -Konzepten finden Sie in der Oracle-Dokumentation:

   * [Benutzerseitig verwaltete Sicherung der gesamten Oracle-Datenbank](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-database-backups.html#GUID-65C5E03A-E906-47EB-92AF-6DC273DBD0A8)
   * [Vollständige benutzerseitig verwaltete Wiederherstellung](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-66D07694-533F-4E3A-BA83-DD461B68DB56)
   * [Oracle-Befehl STARTUP](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/STARTUP.html#GUID-275013B7-CAE2-4619-9A0F-40DB71B61FE8)
   * [Oracle-Befehl RECOVER](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-54B59888-8683-4CD9-B144-B0BB68887572)
   * [Oracle-Befehl ALTER DATABASE](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-DATABASE.html#GUID-8069872F-E680-4511-ADD8-A4E30AF67986)
   * [Oracle-Parameter LOG_ARCHIVE_DEST_n](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/LOG_ARCHIVE_DEST_n.html#GUID-10BD97BF-6295-4E85-A1A3-854E15E05A44)
   * [Oracle-Parameter ARCHIVE_LAG_TARGET](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ARCHIVE_LAG_TARGET.html#GUID-405D335F-5549-4E02-AFB9-434A24465F0B)


## <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Wenn Sie die VM nicht mehr benötigen, können Sie mit den folgenden Befehlen die Ressourcengruppe, die VM und alle zugehörigen Ressourcen entfernen:

1. Vorläufiges Löschen von Sicherungen im Tresor deaktivieren:

    ```azurecli
    az backup vault backup-properties set --name myVault --resource-group rg-oracle --soft-delete-feature-state disable
    ```

1. VM-Schutz aufheben und Sicherungen löschen:

    ```azurecli
    az backup protection disable --resource-group rg-oracle --vault-name myVault --container-name vmoracle19c --item-name vmoracle19c --delete-backup-data true --yes
    ```

1. Ressourcengruppe einschließlich aller Ressourcen entfernen:

    ```azurecli
    az group delete --name rg-oracle
    ```

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen von hoch verfügbaren virtuellen Computern](../../linux/create-cli-complete.md)

[Erkunden der Azure CLI-Beispiele für die Bereitstellung virtueller Computer](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)

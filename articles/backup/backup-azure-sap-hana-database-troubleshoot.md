---
title: Problembehandlung bei Sicherungsfehlern in SAP HANA-Datenbanken
description: Beschreibt, wie häufige Fehler behoben werden, die auftreten können, wenn Sie SAP HANA-Datenbanken mithilfe von Azure Backup sichern.
ms.topic: troubleshooting
ms.date: 05/31/2021
ms.openlocfilehash: 1af52116a08344d36a9364ac9e3b4f468e0abb83
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451959"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Behandeln von Problemen beim Sichern von SAP HANA-Datenbanken in Azure

Dieser Artikel enthält Informationen zur Problembehandlung beim Sichern von SAP HANA-Datenbanken in Azure Virtual Machines. Weitere Informationen zu den derzeit unterstützten SAP HANA-Sicherungsszenarien finden Sie unter [Unterstützung von Szenarien](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Voraussetzungen und Berechtigungen

Informationen finden Sie in den Abschnitten zu den [Voraussetzungen](tutorial-backup-sap-hana-db.md#prerequisites) und [Aufgaben des Vorregistrierungsskripts](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) vor dem Konfigurieren von Sicherungen.

## <a name="common-user-errors"></a>Häufige Benutzerfehler

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Fehlermeldung**      | `Azure Backup does not have required role  privileges to carry out Backup and Restore operations`    |
| ---------------------- | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Dieser Fehler tritt bei allen Vorgängen auf, wenn dem Backup-Benutzer (AZUREWLBACKUPHANAUSER) die Rolle **SAP_INTERNAL_HANA_SUPPORT** nicht zugewiesen oder möglicherweise überschrieben wurde.                          |
| **Empfohlene Maßnahme** | Laden Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) herunter, und führen Sie es für die SAP HANA-Instanz aus, oder weisen Sie die Rolle **SAP_INTERNAL_HANA_SUPPORT** manuell dem Backup-Benutzer (AZUREWLBACKUPHANAUSER) zu.<br><br>**Hinweis**<br><br>Ab HANA 2.0 SPS04 Rev 46 tritt dieser Fehler nicht mehr auf, da die Verwendung der Rolle **SAP_INTERNAL_HANA_SUPPORT** in diesen HANA-Versionen veraltet ist. |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| **Fehlermeldung**      | `Failed to connect to HANA system`                        |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | <ul><li>Fehler beim Herstellen der Verbindung mit der HANA-Instanz</li><li>Systemdatenbank ist offline</li><li>Mandantendatenbank ist offline</li><li>Der Backup-Benutzer (AZUREWLBACKUPHANAUSER) verfügt nicht über ausreichende Berechtigungen/Rechte.</li></ul> |
| **Empfohlene Maßnahme** | Vergewissern Sie sich, dass das System betriebsbereit und aktiv ist. Wenn die Datenbanken bereit sind, stellen Sie sicher, dass die erforderlichen Berechtigungen festgelegt sind, indem Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) herunterladen und für die SAP HANA-Instanz ausführen. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| **Fehlermeldung**      | `The specified SAP HANA instance is either invalid or can't be found`  |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | <ul><li>Die angegebene SAP HANA-Instanz ist entweder ungültig oder wurde nicht gefunden.</li><li>Es können nicht mehrere SAP HANA-Instanzen auf einem einzelnen virtuellen Azure-Computer gesichert werden.</li></ul> |
| **Empfohlene Maßnahme** | <ul><li>Stellen Sie sicher, dass auf dem virtuellen Azure-Computer nur eine einzelne HANA-Instanz ausgeführt wird.</li><li>Führen Sie das Skript aus dem Bereich für die Datenbankermittlung (auch [hier](https://aka.ms/scriptforpermsonhana) zu finden) mit der richtigen SAP HANA-Instanz aus, um das Problem zu beheben.</li></ul> |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| **Fehlermeldung**      | `Backup log chain is broken`                                    |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Eine HANA-LSN-Protokollkettenunterbrechung kann unter anderem folgende Ursachen haben:<ul><li>Fehler beim Azure Storage-Aufruf zum Committen der Sicherung.</li><li>Die Mandantendatenbank ist offline.</li><li>Durch das Erweiterungsupgrade wurde ein laufender Sicherungsauftrag beendet.</li><li>Bei der Sicherung konnte keine Verbindung mit Azure Storage hergestellt werden.</li><li>Von SAP HANA wurde ein Rollback für eine Transaktion im Sicherungsprozess ausgeführt.</li><li>Eine Sicherung ist abgeschlossen, aber im HANA-System wurde der Katalog noch nicht erfolgreich aktualisiert.</li><li>Die Sicherung war aus Sicht von Azure Backup nicht erfolgreich, aus Sicht von HANA dagegen schon. Möglicherweise wurde das Ziel für die Protokollsicherung/den Katalog von „backint“ auf das Dateisystem aktualisiert, oder die ausführbare backint-Datei wurde geändert.</li></ul> |
| **Empfohlene Maßnahme** | Von Azure Backup wird eine vollständige Sicherung zur automatischen Reparatur ausgelöst, um dieses Problem zu beheben. Während der Ausführung dieser Sicherung zur automatischen Reparatur tritt bei allen von HANA ausgelösten Protokollsicherungen der Fehler **OperationCancelledBecauseConflictingAutohealOperationRunningUserError** auf. Nach Abschluss der vollständigen Sicherung zur automatischen Reparatur funktionieren Protokolle und alle anderen Sicherungen wieder wie erwartet.<br>Wenden Sie sich an den Microsoft-Support, wenn innerhalb von 24 Stunden keine vollständige Sicherung zur automatischen Reparatur oder keinerlei erfolgreiche Sicherung (vollständig/differenziell/inkrementell) durchgeführt wird.</br> |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| **Fehlermeldung**      | `SDC to MDC upgrade detected.`                                   |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Dieser Fehler tritt bei Sicherungen auf, wenn für ein SDC-System ein Upgrade auf MDC durchgeführt wird. |
| **Empfohlene Maßnahme** | Informationen zum Behandeln und Beheben des Problems finden Sie unter [Upgrade der SDC-Version oder Upgrade der MDC-Version auf demselben virtuellen Computer](#sdc-to-mdc-upgrade-with-a-change-in-sid). |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| **Fehlermeldung**      | `Backups will fail with this error when the Backint Configuration is incorrectly updated.`                       |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Die backint-Konfiguration, die im Rahmen des Flows „Schutz konfigurieren“ von Azure Backup aktualisiert wurde, wurde vom Kunden geändert oder aktualisiert. |
| **Empfohlene Maßnahme** | Überprüfen Sie, ob die folgenden backint-Parameter festgelegt sind:<br><ul><li>[catalog_backup_using_backint:true]</li><li>[enable_accumulated_catalog_backup:false]</li><li>[parallel_data_backup_backint_channels:1]</li><li>[log_backup_timeout_s:900)]</li><li>[backint_response_timeout:7200]</li></ul>Sind backint-basierte Parameter auf HOST-Ebene vorhanden, entfernen Sie sie. Wenn keine Parameter auf HOST-Ebene vorhanden sind, aber Parameter manuell auf Datenbankebene geändert wurden, stellen Sie sicher, dass die Werte auf Datenbankebene wie oben angegeben festgelegt sind. Alternativ können Sie über das Azure-Portal den [Schutz beenden und die Sicherungsdaten beibehalten](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) und anschließend die Sicherung fortsetzen. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|**Fehlermeldung**  | `The source and target systems for restore are incompatible.`  |
|---------|---------|
|**Mögliche Ursachen**   | Dieser Fehler tritt für den Wiederherstellungsflow auf, wenn die HANA-Quell- und -Zieldatenbanken sowie die Systeme nicht kompatibel sind. |
|Empfohlene Maßnahme   |   Stellen Sie sicher, dass das Wiederherstellungsszenario nicht in der folgenden Liste möglicher inkompatibler Wiederherstellungen enthalten ist:<br> **Fall 1:** SYSTEMDB kann während der Wiederherstellung nicht umbenannt werden.<br>**Fall 2:** Quelle = SDC und Ziel = MDC: Die Quelldatenbank kann auf dem Ziel nicht als SYSTEMDB oder Mandanten-DB wiederhergestellt werden. <br> **Fall 3:** Quelle = MDC und Ziel = SDC: Die Quelldatenbank (SYSTEMDB oder Mandanten-DB) kann auf dem Ziel nicht wiederhergestellt werden.<br>Weitere Informationen finden Sie im Hinweis **1642148** im [SAP Support Launchpad](https://launchpad.support.sap.com). |

### <a name="usererrorhanapodoesnotexist"></a>UserErrorHANAPODoesNotExist

**Fehlermeldung** | `Database configured for backup does not exist.`
--------- | --------------------------------
**Mögliche Ursachen** | Wenn eine für die Sicherung konfigurierte Datenbank gelöscht wird, tritt bei allen Sicherungen für diese Datenbank ein Fehler auf. Das gilt sowohl für geplante Sicherungen als auch für Ad-hoc-Sicherungen.
**Empfohlene Maßnahme** | Überprüfen Sie, ob die Datenbank gelöscht wurde. Erstellen Sie die Datenbank neu, oder [beenden Sie den Schutz](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) für die Datenbank (mit oder ohne Beibehaltung der Daten).

### <a name="usererrorinsufficientprivilegeofdatabaseuser"></a>UserErrorInsufficientPrivilegeOfDatabaseUser

**Fehlermeldung** |    `Azure Backup does not have enough privileges to carry out Backup and Restore operations.`
---------- | ---------
**Mögliche Ursachen** | Dem durch das Vorregistrierungsskript erstellten Backup-Benutzer (AZUREWLBACKUPHANAUSER) wurde nicht mindestens eine der folgenden Rollen zugewiesen:<ul><li>Für MDC: „DATABASE ADMIN“ und „BACKUP ADMIN“ (ab HANA 2.0 SPS05) zum Erstellen neuer Datenbanken während der Wiederherstellung</li><li>Für SDC: „BACKUP ADMIN“ zum Erstellen neuer Datenbanken während der Wiederherstellung</li><li>„CATALOG READ“ zum Lesen des Sicherungskatalogs</li><li>„SAP_INTERNAL_HANA_SUPPORT“ zum Zugreifen auf einige private Tabellen. Nur erforderlich für SDC- und MDC-Versionen vor HANA 2.0 SPS04 Rev 46. Nicht erforderlich ab HANA 2.0 SPS04 Rev 46, da die benötigten Informationen aus öffentlichen Tabellen nun mit dem Fix vom HANA-Team abgerufen werden.</li></ul>
**Empfohlene Maßnahme** | Fügen Sie dem Backup-Benutzer (AZUREWLBACKUPHANAUSER) manuell die erforderlichen Rollen und Berechtigungen hinzu, oder laden Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) herunter, und führen Sie es für die SAP HANA-Instanz aus, um das Problem zu beheben.

### <a name="usererrordatabaseuserpasswordexpired"></a>UserErrorDatabaseUserPasswordExpired

**Fehlermeldung** | `Database/Backup user's password expired.`
----------- | -----------
**Mögliche Ursachen** | Der durch das Vorregistrierungsskript erstellte Datenbank-/Backup-Benutzer legt keinen Ablauf für das Kennwort fest. Im Falle einer Änderung tritt jedoch unter Umständen dieser Fehler auf.
**Empfohlene Maßnahme** | Laden Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) herunter, und führen Sie es für die SAP HANA-Instanz aus, um das Problem zu beheben.

### <a name="usererrorinconsistentssfs"></a>UserErrorInconsistentSSFS

**Fehlermeldung** | `SAP HANA error`
------------ | ----------
**Mögliche Ursachen** | Von der SAP HANA-Engine wurde ein Fehler aufgrund eines inkonsistenten SSFS (Secure Storage File System) empfangen.
**Empfohlene Maßnahme** | Arbeiten Sie mit dem SAP HANA-Team zusammen, um dieses Problem zu beheben. Weitere Informationen finden Sie im SAP-Hinweis **0002097613**.

### <a name="usererrorcannotconnecttoazureactivedirectoryservice"></a>UserErrorCannotConnectToAzureActiveDirectoryService

**Fehlermeldung** | `Unable to connect to the AAD service from the HANA system.`
--------- | --------
**Mögliche Ursachen** | Firewall- oder Proxyeinstellungen, da die ausgehende Verbindung mit AAD vom Plug-In-Dienstkonto der Backup-Erweiterung nicht zugelassen wird.
**Empfohlene Maßnahme** | Korrigieren Sie die Firewall- oder Proxyeinstellungen, damit die ausgehende Verbindung mit AAD erfolgreich hergestellt werden kann.

### <a name="usererrormisconfiguredsslcastore"></a>UserErrorMisConfiguredSslCaStore

**Fehlermeldung** | `Misconfigured CA store`
-------- | -------
**Mögliche Ursachen** | Vom Plug-In-Hostprozess der Backup-Erweiterung kann nicht auf den Speicher der Stammzertifizierungsstelle zugegriffen werden (in _/var/lib/ca-certificates/ca-bundle.pem_ für SLES).
**Empfohlene Maßnahme** | Stellen Sie mithilfe von `chmod o+r` die ursprüngliche Berechtigung wieder her, um das Problem mit dem Zertifizierungsstellenspeicher zu beheben.  Starten Sie dann den Plug-In-Hostdienst neu, damit Sicherungen und Wiederherstellungen erfolgreich durchgeführt werden können.

### <a name="usererrorbackupfailedasremedialbackupinprogress"></a>UserErrorBackupFailedAsRemedialBackupInProgress

**Fehlermeldung** | `Remedial Backup in progress.`
---------- | -------
**Mögliche Ursachen** | Azure Backup löst als Abhilfemaßnahme eine vollständige Sicherung aus, um Unterbrechungen der LSN-Protokollkette zu behandeln. Während dieser vollständigen Bereinigungssicherung tritt bei vollständigen, differenziellen und inkrementellen Sicherungen, die über das Portal/die CLI ausgelöst werden, der hier angegebene Fehler auf.
**Empfohlene Maßnahme** | Warten Sie, bis die vollständige Bereinigungssicherung erfolgreich abgeschlossen wurde, bevor Sie eine weitere Sicherung auslösen.

### <a name="operationcancelledbecauseconflictingoperationrunningusererror"></a>OperationCancelledBecauseConflictingOperationRunningUserError

**Fehlermeldung** | `Conflicting operation in progress.`
----------- | -------------
**Mögliche Ursachen** | Eine vollständige, differenzielle oder inkrementelle Sicherung wurde über das Portal, die CLI oder native HANA-Clients ausgelöst, während bereits eine weitere vollständige, differenzielle oder inkrementelle Sicherung aktiv war.
**Empfohlene Maßnahme** | Warten Sie, bis der aktive Sicherungsauftrag abgeschlossen ist, bevor Sie eine neue vollständige Sicherung oder Deltasicherung auslösen.

### <a name="operationcancelledbecauseconflictingautohealoperationrunning-usererror"></a>OperationCancelledBecauseConflictingAutohealOperationRunningUserError

**Fehlermeldung** | `Auto-heal Full backup in progress.`
------- | -------
**Mögliche Ursachen** | Von Azure Backup wird eine vollständige Sicherung zur automatischen Reparatur ausgelöst, um **UserErrorHANALSNValidationFailure** zu beheben. Während der Ausführung dieser Sicherung zur automatischen Reparatur tritt bei allen von HANA ausgelösten Protokollsicherungen der Fehler **OperationCancelledBecauseConflictingAutohealOperationRunningUserError** auf.<br>Nach Abschluss der vollständigen Sicherung zur automatischen Reparatur funktionieren Protokolle und alle anderen Sicherungen wieder wie erwartet.</br>
**Empfohlene Maßnahme** | Warten Sie, bis die vollständige Sicherung zur automatischen Reparatur abgeschlossen ist, bevor Sie eine neue vollständige Sicherung oder Deltasicherung auslösen.

### <a name="usererrorhanaprescriptnotrun"></a>UserErrorHanaPreScriptNotRun

**Fehlermeldung** | `Pre-registration script not run.`
--------- | --------
**Mögliche Ursachen** | Das SAP HANA-Vorregistrierungsskript zum Einrichten der Umgebung wurde nicht ausgeführt.
**Empfohlene Maßnahme** | Laden Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) herunter, und führen Sie es für die SAP HANA-Instanz aus.


### <a name="usererrortargetpoexistsoverwritenotspecified"></a>UserErrorTargetPOExistsOverwriteNotSpecified

**Fehlermeldung** | `Target database cannot be overwritten for Restore.`
------- | -------
**Mögliche Ursachen** | Die Zieldatenbank ist vorhanden, kann aber nicht überschrieben werden. Das Erzwingen der Überschreibung wurde im Wiederherstellungsflow im Portal/in der CLI nicht festgelegt.
**Empfohlene Maßnahme** | Stellen Sie die Datenbank mit der Option „Überschreiben erzwingen“ oder in einer anderen Zieldatenbank wieder her.

### <a name="usererrorrecoverysysscriptfailedtotriggerrestore"></a>UserErrorRecoverySysScriptFailedToTriggerRestore

**Fehlermeldung** | `RecoverySys.py could not be run successfully to restore System DB.`
-------- | ---------
**Mögliche Ursachen** | Ein Fehler bei der Wiederherstellung der Systemdatenbank kann folgende Ursachen haben:<ul><li>**Recoverysys.py** wurde von Azure Backup auf dem HANA-Computer nicht gefunden. Dieser Fall kann eintreten, wenn die HANA-Umgebung nicht ordnungsgemäß eingerichtet wurde.</li><li>**Recoverysys.py** ist vorhanden, aber beim Auslösen des Skripts wurde HANA nicht aufgerufen, um die Wiederherstellung durchzuführen.</li><li>HANA wurde durch „Recoverysys.py“ erfolgreich aufgerufen, um die Wiederherstellung durchzuführen, aber die Wiederherstellung konnte nicht erfolgreich durchgeführt werden.</li></ul>
**Empfohlene Maßnahme** | <ul><li>Arbeiten Sie im Falle des ersten Problems mit dem SAP HANA-Team zusammen, um es zu beheben.</li><li>Sehen Sie sich im Falle der Probleme 2 und 3 die Protokollablaufverfolgung an, indem Sie den Befehl „HDSetting.sh“ an der sid-adm-Eingabeaufforderung ausführen. Beispiel: _/usr/sap/SID/HDB00/HDBSetting.sh_.</li></ul>Geben Sie die Ergebnisse an das SAP HANA-Team weiter, um das Problem zu beheben.

### <a name="usererrordbnamenotincorrectformat"></a>UserErrorDBNameNotInCorrectFormat

**Fehlermeldung** | `Restored database name not in correct format.`
--------- | --------
**Mögliche Ursachen** | Der von Ihnen angegebene Name der wiederhergestellten Datenbank entspricht nicht dem akzeptablen/erwarteten Format.
**Empfohlene Maßnahme** | Stellen Sie sicher, dass der Name der wiederhergestellten Datenbank mit einem Buchstaben beginnt und (mit Ausnahme von Ziffern oder einem Unterstrich) keine Symbole enthält.<br>Er darf maximal 127 Zeichen umfassen und nicht mit "\_SYS_\" beginnen.

### <a name="usererrordefaultsidadmdirectorychanged"></a>UserErrorDefaultSidAdmDirectoryChanged

**Fehlermeldung** | `Default sid-adm directory changed.`
------- | -------
**Mögliche Ursachen** | Das Standardverzeichnis **sid-adm** wurde geändert, und **HDBSetting.sh** ist in diesem Standardverzeichnis nicht verfügbar.
**Empfohlene Maßnahme** | Wenn HXE die SID ist, stellen Sie sicher, dass die Umgebungsvariable „HOME“ auf _/usr/sap/HXE/home_ als Benutzer **sid-adm** festgelegt ist.

### <a name="usererrorhdbsettingsscriptnotfound"></a>UserErrorHDBsettingsScriptNotFound

**Fehlermeldung** | `HDBSetting.sh file cannot be found.`
--------- | -------
**Mögliche Ursachen** | Fehler beim Wiederherstellen von Systemdatenbanken, da die Datei **HDBsettings.sh** von der **&lt;sid&gt;adm**-Benutzerumgebung nicht gefunden wurde, um die Wiederherstellung auszulösen.
**Empfohlene Maßnahme** | Arbeiten Sie mit dem SAP HANA-Team zusammen, um dieses Problem zu beheben.<br><br>Wenn HXE die SID ist, stellen Sie sicher, dass die Umgebungsvariable „HOME“ auf _/usr/sap/HXE/home_ als Benutzer **sid-adm** festgelegt ist.

## <a name="restore-checks"></a>Wiederherstellungsprüfungen

### <a name="single-container-database-sdc-restore"></a>Wiederherstellung einer Datenbank mit einem einzelnen Container (SDC)

Achten Sie auf Eingaben während der Wiederherstellung einer Datenbank mit einem einzelnen Container (SDC) für HANA auf einen anderen SDC-Computer. Der Datenbankname sollte in Kleinbuchstaben angegeben werden, wobei „sdc“ in Klammern angefügt wird. Die HANA-Instanz wird in Großbuchstaben angezeigt.

Angenommen, eine SDC HANA-Instanz „H21“ wird gesichert. Auf der Seite mit den Sicherungselementen wird der Sicherungselementname **„h21(sdc)“** angezeigt. Wenn Sie versuchen, diese Datenbank auf einem anderen Ziel-SDC wiederherzustellen (z. B. „H11“), müssen Sie die folgenden Eingaben bereitstellen:

![Name der wiederhergestellten SDC-Datenbank](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Beachten Sie folgende Punkte:

- Standardmäßig wird der Name der wiederhergestellten Datenbank mit dem Namen des Sicherungselements aufgefüllt. In diesem Fall: „h21(sdc)“.
- Wenn Sie als Ziel „H11“ auswählen, wird der Name der wiederhergestellten Datenbank nicht automatisch geändert. **Er sollte in „h11(sdc)“ geändert werden**. Im Fall von SDC ist der Name der wiederhergestellten Datenbank die ID der Zielinstanz in Kleinbuchstaben, an die „sdc“ in Klammern angefügt wird.
- Da SDC nur über eine einzelne Datenbank verfügen kann, müssen Sie auch das Kontrollkästchen aktivieren, um das Überschreiben der vorhandenen Datenbankdaten mit den Daten des Wiederherstellungspunkts zuzulassen.
- Bei Linux muss die Groß-/Kleinschreibung beachtet werden. Verwenden Sie daher unbedingt die korrekte Groß-/Kleinschreibung.

### <a name="multiple-container-database-mdc-restore"></a>Wiederherstellung einer Datenbank mit mehreren Containern (MDC)

In Datenbanken mit mehreren Containern (MDC) für HANA ist die Standardkonfiguration „SYSTEMDB + 1 oder mehr Mandantendatenbanken“. Das Wiederherstellen einer vollständigen SAP HANA-Instanz bedeutet, dass sowohl SYSTEMDB als auch die Mandantendatenbanken wiederhergestellt werden. Zuerst wird SYSTEMDB wiederhergestellt, und dann wird die Wiederherstellung für die Mandantendatenbank fortgesetzt. Systemdatenbank bedeutet im Wesentlichen, dass die Systeminformationen für das ausgewählte Ziel überschrieben werden. Bei dieser Wiederherstellung werden auch die BackInt-bezogenen Informationen in der Zielinstanz überschrieben. Nachdem die Systemdatenbank auf einer Zielinstanz wiederhergestellt wurde, führen Sie das Vorregistrierungsskript erneut aus. Nur dann ist die nachfolgende Wiederherstellung der Mandantendatenbanken erfolgreich.

## <a name="back-up-a-replicated-vm"></a>Sichern eines replizierten virtuellen Computers

### <a name="scenario-1"></a>Szenario 1

Der ursprüngliche virtuelle Computer wurde mithilfe von Azure Site Recovery oder einer Azure-VM-Sicherung repliziert. Der neue virtuelle Computer wurde erstellt, um den alten virtuellen Computer zu simulieren. Das heißt, die Einstellungen sind identisch. (Das liegt daran, dass der ursprüngliche virtuelle Computer gelöscht wurde und die Wiederherstellung aus der VM-Sicherung oder über Azure Site Recovery erfolgte).

Für dieses Szenario gibt es zwei mögliche Anwendungsfälle. Erfahren Sie, wie Sie den replizierten virtuellen Computer in diesen beiden Fällen sichern:

1. Der neu erstellte virtuelle Computer hat den gleichen Namen und befindet sich in der gleichen Ressourcengruppe und im gleichen Abonnement wie der gelöschte virtuelle Computer.

    - Die Erweiterung ist auf dem virtuellen Computer bereits vorhanden, ist aber für keinen der Dienste sichtbar.
    - Führen Sie das Vorregistrierungsskript aus.
    - Registrieren Sie die Erweiterung im Azure-Portal erneut für den gleichen Computer (**Sicherung** -> **Details anzeigen** -> entsprechende Azure-VM auswählen -> erneut registrieren).
    - Die Sicherung der bereits vorhandenen gesicherten Datenbanken (vom gelöschten virtuellen Computer) sollte dann erfolgreich gestartet werden.

2. Der neu erstellte virtuelle Computer hat entweder:

    - einen anderen Name als der gelöschte virtuelle Computer
    - oder den gleichen Namen wie der gelöschte virtuelle Computers, befindet sich jedoch in einer anderen Ressourcengruppe bzw. einem anderen Abonnement als der gelöschte virtuellen Computer

    Ist dies der Fall, führen Sie die folgenden Schritte aus:

    - Die Erweiterung ist auf dem virtuellen Computer bereits vorhanden, ist aber für keinen der Dienste sichtbar.
    - Führen Sie das Vorregistrierungsskript aus.
    - Wenn Sie die neuen Datenbanken ermitteln und schützen, werden im Portal doppelte aktive Datenbanken angezeigt. Um dies zu vermeiden, [beenden Sie den Schutz mit Beibehaltung der Daten](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) für die alten Datenbanken. Fahren Sie dann mit den verbleibenden Schritten fort.
    - Ermitteln Sie die Datenbanken zum Aktivieren der Sicherung.
    - Aktivieren Sie Sicherungen für diese Datenbanken.
    - Die bereits vorhandenen gesicherten Datenbanken (vom gelöschten virtuellen Computer) werden weiterhin im Tresor gespeichert (ihre Sicherungen werden gemäß der Richtlinie aufbewahrt).

### <a name="scenario-2"></a>Szenario 2

Der ursprüngliche virtuelle Computer wurde mithilfe von Azure Site Recovery oder einer Azure-VM-Sicherung repliziert. Der neue virtuelle Computer wurde aus dem Inhalt erstellt, das als Vorlage verwendet werden soll. Dabei handelt es sich um einen neuen virtuellen Computer mit einer neuen SID.

Führen Sie die folgenden Schritte aus, um Sicherungen auf dem neuen virtuellen Computer zu aktivieren:

- Die Erweiterung ist auf dem virtuellen Computer bereits vorhanden, ist aber für keinen der Dienste sichtbar.
- Führen Sie das Vorregistrierungsskript aus. Basierend auf der SID des neuen virtuellen Computers sind zwei Szenarien denkbar:
  - Der ursprüngliche virtuelle Computer und der neue virtuelle Computer haben dieselbe SID. Das Vorregistrierungsskript wird erfolgreich ausgeführt.
  - Der ursprüngliche virtuelle Computer und der neue virtuelle Computer haben unterschiedliche SIDs. Das Vorregistrierungsskript schlägt fehl. Kontaktieren Sie den Support, um Hilfe in diesem Szenario zu erhalten.
- Ermitteln der Datenbanken, die Sie sichern möchten
- Aktivieren der Sicherungen für diese Datenbanken

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Upgrade der SDC-Version oder Upgrade der MDC-Version auf demselben virtuellen Computer

Upgrades des Betriebssystems oder Änderungen der SDC- oder MDC-Version, die keine SID-Änderung auslösen, können wie folgt behandelt werden:

- Stellen Sie sicher, dass die neue Betriebssystem-, SDC- oder MDC-Version aktuell von [Azure Backup unterstützt wird](sap-hana-backup-support-matrix.md#scenario-support).
- [Beenden Sie den Schutz](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) mit Beibehaltung der Daten für die Datenbank.
- Führen Sie das Upgrade oder Update durch.
- Führen Sie das Vorregistrierungsskript erneut aus. Beim Upgradeprozess werden häufig die [erforderlichen Rollen](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) entfernt. Durch Ausführen des Vorregistrierungsskripts werden alle erforderlichen Rollen überprüft.
- Setzen Sie den Schutz für die Datenbank fort.

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Upgrade von SDC zu MDC ohne SID-Änderung

Upgrades von SDC auf MDC, die keine SID-Änderung verursachen, können wie folgt behandelt werden:

- Stellen Sie sicher, dass die neue MDC-Version aktuell von [Azure Backup unterstützt wird](sap-hana-backup-support-matrix.md#scenario-support).
- [Beenden Sie den Schutz mit Beibehaltung der Daten](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) für die alte SDC-Datenbank.
- Führen Sie das Upgrade durch. Nach Abschluss des Vorgangs ist die HANA-System nun eine MDC-Instanz mit einer Systemdatenbank und einer Mandantendatenbank.
- Führen Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) erneut aus.
- Registrieren Sie die Erweiterung im Azure-Portal erneut für den gleichen Computer (**Sicherung** -> **Details anzeigen** -> entsprechende Azure-VM auswählen -> erneut registrieren).
- Wählen Sie **Datenbanken neu ermitteln** für denselben virtuellen Computer aus. Durch diese Aktion sollten die neuen Datenbanken in Schritt 3 als SYSTEMDB und Mandantendatenbank (nicht SDC) angezeigt werden.
- Die ältere SDC-Datenbank ist weiterhin im Tresor vorhanden, und die alten gesicherten Daten werden gemäß der Richtlinie aufbewahrt.
- Konfigurieren der Sicherung für diese Datenbanken

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Upgrade von SDC zu MDC mit SID-Änderung

Upgrades von SDC auf MDC, die eine SID-Änderung verursachen, können wie folgt behandelt werden:

- Stellen Sie sicher, dass die neue MDC-Version aktuell von [Azure Backup unterstützt wird](sap-hana-backup-support-matrix.md#scenario-support).
- **Beenden Sie den Schutz mit Beibehaltung der Daten** für die alte SDC-Datenbank.
- Führen Sie das Upgrade durch. Nach Abschluss des Vorgangs ist die HANA-System nun eine MDC-Instanz mit einer Systemdatenbank und einer Mandantendatenbank.
- Führen Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) mit den richtigen Details (neue SID und MDC) erneut aus. Aufgrund einer Änderung der SID treten möglicherweise Probleme bei der Ausführung des Skripts auf. Wenden Sie sich an Azure Backup-Support, wenn Probleme auftreten.
- Registrieren Sie die Erweiterung im Azure-Portal erneut für den gleichen Computer (**Sicherung** -> **Details anzeigen** -> entsprechende Azure-VM auswählen -> erneut registrieren).
- Wählen Sie **Datenbanken neu ermitteln** für denselben virtuellen Computer aus. Durch diese Aktion sollten die neuen Datenbanken in Schritt 3 als SYSTEMDB und Mandantendatenbank (nicht SDC) angezeigt werden.
- Die ältere SDC-Datenbank ist weiterhin im Tresor vorhanden, und die alten gesicherten Daten werden gemäß der Richtlinie aufbewahrt.
- Konfigurieren der Sicherung für diese Datenbanken

## <a name="re-registration-failures"></a>Fehler bei der erneuten Registrierung

Überprüfen Sie vor dem Auslösen der erneuten Registrierung, ob ein oder mehrere der folgenden Symptome vorhanden sind:

- Für alle Vorgänge (z.B. Sicherung, Wiederherstellung und Sicherungskonfiguration) tritt auf der VM ein Fehler mit einem der folgenden Fehlercodes auf: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Wenn im Bereich **Sicherungsstatus** für das Sicherungselement der Status **Nicht erreichbar** angezeigt wird, schließen Sie alle anderen Gründe aus, die zum gleichen Status führen könnten:

  - Fehlende Berechtigung zur Durchführung von sicherungsbezogenen Vorgängen auf der VM
  - Herunterfahren des virtuellen Computers, sodass die Sicherungen nicht durchgeführt werden können
  - Netzwerkfehler

Zu diesen Symptomen kann es aufgrund von einer oder mehreren der folgenden Ursachen kommen:

- Eine Erweiterung wurde gelöscht oder im Portal deinstalliert.
- Die VM wurde über eine direkte Datenträgerwiederherstellung rechtzeitig wiederhergestellt.
- Die VM wurde für längere Zeit heruntergefahren, sodass die Erweiterungskonfiguration dafür abgelaufen ist.
- Die VM wurde gelöscht, und eine andere VM wurde mit dem gleichen Namen und in derselben Ressourcengruppe wie die gelöschte VM erstellt.

In den vorhergehenden Szenarien sollten Sie einen Neuregistrierungsvorgang auf der VM auszulösen.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [häufig gestellten Fragen](./sap-hana-faq-backup-azure-vm.yml) zum Sichern von SAP HANA-Datenbanken auf Azure-VMs an.

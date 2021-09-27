---
title: Seed-Offlinesicherung mithilfe des Azure Import/Export-Diensts
description: Erfahren Sie, wie Sie Azure Backup verwenden können, um mithilfe des Azure Import/Export-Diensts Daten aus dem Netzwerk zu senden. In diesem Artikel wird das Offlineseeding der ersten Sicherungsdaten mit dem Azure Import/Export-Dienst erläutert.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 36646c148fb42503b8bdc78cbcd11a1f497127b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644066"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Workflow zur Offlinesicherung in Azure Backup

Azure Backup verfügt über mehrere integrierte effizienzsteigernde Funktionen, die die Netzwerk- und Speicherkosten bei den ersten vollständigen Datensicherungen in Azure reduzieren. Bei den ersten vollständigen Sicherungen werden meist große Datenmengen übertragen, sodass eine höhere Netzwerkbandbreite als bei den nachfolgenden Sicherungen erforderlich ist, bei denen nur die Deltamengen bzw. Inkremente übertragen werden. Durch den Prozess des Offlineseedings kann Azure Backup Datenträger verwenden, um die Daten der Offlinesicherung in Azure hochzuladen.

Der Azure Backup-Offlineseedingvorgang ist eng in den [Azure Import/Export-Dienst](../import-export/storage-import-export-service.md) integriert. Sie können diesen Dienst verwenden, um die anfänglichen Sicherungsdaten mithilfe von Datenträgern an Azure zu übertragen. Wenn Sie bei der Erstsicherung mehrere Terabytes an Daten über ein Netzwerk mit hoher Wartezeit und geringer Bandbreite übertragen müssen, können Sie die erste Sicherungskopie mithilfe des Offlineseeding-Workflows auf einer oder mehreren Festplatten speichern und an ein Azure-Datencenter senden. Die folgende Abbildung bietet eine Übersicht über die Workflowschritte:

  ![Übersicht über den Offlineimport-Workflowprozess](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Der Offlinesicherungsprozess umfasst diese Schritte:

1. Senden Sie die Sicherungsdaten nicht über das Netzwerk, sondern schreiben Sie sie an einen Stagingspeicherort.
1. Schreiben Sie die Daten am Stagingspeicherort mithilfe des Hilfsprogramms *AzureOfflineBackupDiskPrep* auf einen oder mehrere SATA-Datenträger.
1. Das Hilfsprogramm *AzureOfflineBackupDiskPrep* erstellt zur Vorbereitung einen Azure-Importauftrag. Senden Sie die SATA-Laufwerke an das nächstgelegene Azure-Datencenter, und geben Sie den Importauftrag an, um die Aktivitäten zu verknüpfen.
1. Im Azure-Datencenter werden die Daten auf den Datenträgern in ein Azure-Speicherkonto kopiert.
1. Azure Backup kopiert die Sicherungsdaten aus dem Speicherkonto in den Recovery Services-Tresor, und inkrementelle Sicherungen werden geplant.

## <a name="supported-configurations"></a>Unterstützte Konfigurationen

Diese Azure Backup-Features oder -Workloads unterstützen die Verwendung der Offlinesicherung für Folgendes:

> [!div class="checklist"]
>
> * Sichern von Dateien und Ordnern mit dem Microsoft Azure Recovery Services-Agent (MARS; auch Azure Backup-Agent genannt).
> * Sichern aller Workloads und Dateien mit dem System Center Data Protection Manager (DPM).
> * Sichern aller Workloads und Dateien mit dem Microsoft Azure Backup Server.

   > [!NOTE]
   > Die Offlinesicherung wird für Sicherungen des Systemstatus mithilfe des Azure Backup-Agents nicht unterstützt.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Voraussetzungen

  > [!NOTE]
  > Die folgenden Voraussetzungen und der folgende Workflow gelten nur für Offlinesicherungen von Dateien und Ordnern mit dem [neuesten Azure Recovery Services-Agent](https://aka.ms/azurebackup_agent). Informationen zum Durchführen von Offlinesicherungen für Workloads mit System Center DPM oder Azure Backup Server finden Sie unter [Offlinesicherungsworkflow für DPM und Azure Backup Server](backup-azure-backup-server-import-export.md).

Führen Sie vor dem Starten des Workflows zur Offlinesicherung die folgenden Schritte aus:

* Erstellen Sie einen [Recovery Services-Tresor](backup-azure-recovery-services-vault-overview.md). Führen Sie zum Erstellen eines Tresors die folgenden Schritte in [Erstellen eines Recovery Services-Tresor](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) aus.
* Vergewissern Sie sich, dass auf dem Windows-Server oder auf dem Windows-Client die [neueste Version des Azure Backup-Agents](https://aka.ms/azurebackup_agent) installiert und der Computer beim Recovery Services-Tresor registriert ist.
* Der Computer, auf dem der Azure Backup-Agent ausgeführt wird, muss über Azure PowerShell 3.7.0 verfügen. [Laden Sie die Azure PowerShell, Version 3.7.0, herunter, und installieren Sie sie](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Auf dem Computer mit dem Azure Backup-Agent müssen Microsoft Edge oder Internet Explorer 11 installiert und JavaScript aktiviert sein.
* Erstellen Sie ein Azure Storage-Konto in dem Abonnement, in dem sich auch der Recovery Services-Tresor befindet.
* Vergewissern Sie sich, dass Sie über die [erforderlichen Berechtigungen](../active-directory/develop/howto-create-service-principal-portal.md) für die Erstellung der Azure Active Directory-Anwendung verfügen. Der Workflow zur Offlinesicherung erstellt eine Azure Active Directory-Anwendung in dem Abonnement, das dem Azure Storage-Konto zugeordnet ist. Die Anwendung dient dazu, Azure Backup sicheren und bereichsbezogenen Zugriff auf den Azure-Import/Export-Dienst zu ermöglichen, der für den Workflow zur Offlinesicherung erforderlich ist.
* Registrieren Sie den Ressourcenanbieter *Microsoft.ImportExport* bei dem Abonnement, das das Azure Storage-Konto umfasst. So registrieren Sie den Ressourcenanbieter:
    1. Wählen Sie im Hauptmenü die Option **Abonnements** aus.
    1. Wählen Sie bei Verwendung mehrerer Abonnements das Abonnement aus, das Sie für die Offlinesicherung verwenden möchten. Falls Sie nur ein einzelnes Abonnement verwenden, wird Ihr Abonnement angezeigt.
    1. Wählen Sie im Abonnementmenü die Option **Ressourcenanbieter** aus, um die Anbieterliste anzuzeigen.
    1. Scrollen Sie in der Anbieterliste nach unten zu *Microsoft.ImportExport*. Falls der **Status** **NotRegistered** lautet, wählen Sie **Registrieren** aus.

        ![Registrieren des Ressourcenanbieters](./media/backup-azure-backup-import-export/registerimportexport.png)

* Es wurde ein Stagingspeicherort, bei dem es sich um eine Netzwerkfreigabe oder ein zusätzliches Laufwerk auf dem Computer (intern oder extern) handeln kann, mit genügend Speicherplatz zum Speichern der Erstkopie erstellt. Wenn Sie beispielsweise einen 500-GB-Dateiserver sichern möchten, muss der Stagingbereich mindestens 500 GB groß sein. (Aufgrund der Komprimierung wird weniger Speicherplatz genutzt.)
* Senden Sie nur interne Datenträger vom Typ SSD (2,5 Zoll) oder SATA II/III (2,5 Zoll oder 3,5 Zoll) an Azure. Sie können Festplatten mit bis zu 10 TB verwenden. Schlagen Sie in der [Dokumentation zum Azure Import/Export-Dienst](../import-export/storage-import-export-requirements.md#supported-hardware) die aktuell vom Dienst unterstützten Laufwerke nach.
* Die SATA-Laufwerke müssen mit einem Computer verbunden sein (als *Kopiercomputer* bezeichnet), auf dem die Sicherungsdaten vom Stagingspeicherort auf die SATA Laufwerke kopiert werden. Stellen Sie sicher, dass BitLocker auf dem Kopiercomputer aktiviert ist.

## <a name="workflow"></a>Workflow

In diesem Abschnitt wird erläutert, wie Sie den Workflow zur Offlinesicherung durchführen, damit Ihre Daten an ein Azure-Datencenter gesendet und in Azure Storage hochgeladen werden können. Weitere Informationen zum Import-Dienst sowie zu anderen Aspekten des Prozesses finden Sie in der [Übersicht über den Azure Import/Export-Dienst](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Initiieren der Offlinesicherung

1. Wenn Sie eine Sicherung mit dem Recovery Services-Agent planen, wird die folgende Seite angezeigt.

    ![Seite „Importieren“](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Wählen Sie die Option **Übertragung über eigenen Datenträger** aus.

    > [!NOTE]
    > Verwenden Sie Azure Data Box, um die Daten der Erstsicherung offline zu übertragen. Diese Option spart den Aufwand der Beschaffung eigener Azure-kompatibler Datenträger, da Ihnen von Microsoft proprietäre, sichere und manipulationsgeschützte Azure Data Box-Geräte bereitgestellt werden, auf die die Sicherungsdaten vom Recovery Services-Agent direkt geschrieben werden können.

1. Wählen Sie **Weiter** aus, und füllen Sie die Felder sorgfältig aus.

    ![Eingeben von Datenträgerdetails](./media/backup-azure-backup-import-export/your-disk-details.png)

   Folgende Felder sind auszufüllen:

    * **Stagingspeicherort**: Der temporäre Speicherort, an den die erste Sicherungskopie geschrieben wird. Beim Stagingspeicherort kann es sich um eine Netzwerkfreigabe oder einen lokalen Computer handeln. Wenn der Kopiercomputer und der Quellcomputer nicht identisch sind, geben Sie den vollständigen Netzwerkpfad des Stagingspeicherorts an.
    * **Azure Resource Manager-Speicherkonto**: Der Name des Resource Manager-Speicherkontos (Universell v1 oder Universell v2) in allen Azure-Abonnements.
    * **Azure Storage-Container**: Der Name des Blob Storage-Zielcontainers im Azure-Speicherkonto, in das die Sicherungsdaten vor dem Kopieren in den Recovery Services-Tresor importiert werden.
    * **Azure-Abonnement-ID**: Die ID für das Azure-Abonnement, in dem das Azure-Speicherkonto erstellt wird.
    * **Name des Azure Importauftrags**: Der eindeutige Name, anhand dessen der Azure Import/Export-Dienst und Azure Backup Datenübertragungen nachverfolgen, die auf Datenträgern an Azure gesendet werden.
  
   Nachdem Sie die Felder ausgefüllt haben, wählen Sie **Weiter** aus. Speichern Sie den **Stagingspeicherort** und den **Namen des Azure-Importauftrags**. Diese Informationen werden zur Vorbereitung der Datenträger benötigt.

1. Melden Sie sich bei Ihrem Azure-Abonnement an, wenn Sie dazu aufgefordert werden. Die Anmeldung ist erforderlich, damit Azure Backup die Azure Active Directory-Anwendung erstellen kann. Geben Sie die erforderlichen Berechtigungen für den Zugriff auf den Azure-Import-/Export-Dienst ein.

    ![Anmeldeseite für das Azure-Abonnement](./media/backup-azure-backup-import-export/azure-login.png)

1. Schließen Sie den Workflow ab. Wählen Sie in der Konsole des Azure Backup-Agents die Option **Jetzt sichern** aus.

    ![Jetzt sichern](./media/backup-azure-backup-import-export/backupnow.png)

1. Wählen Sie auf der **Bestätigungsseite** des Assistenten die Option **Sichern** aus. Im Rahmen der Einrichtung wird die Erstsicherung in den Stagingbereich geschrieben.

   ![Bestätigen der Sicherungsbereitschaft](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Nach Abschluss des Vorgangs kann der Stagingspeicherort für die Datenträgervorbereitung verwendet werden.

   ![Seite des Assistenten für die sofortige Sicherung](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Vorbereiten der SATA-Laufwerke und Senden an Azure

Das Hilfsprogramm *AzureOfflineBackupDiskPrep* bereitet die SATA-Laufwerke vor, die an das nächstgelegene Azure-Datencenter gesendet werden. Dieses Hilfsprogramm steht im Installationsverzeichnis des Azure Backup-Agents unter folgendem Pfad zur Verfügung:

`*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Navigieren Sie zu diesem Verzeichnis, und kopieren Sie das Verzeichnis *AzureOfflineBackupDiskPrep* auf einen anderen Computer, mit dem die SATA-Laufwerke verbunden sind. Vergewissern Sie sich, dass auf dem Computer mit den verbundenen SATA-Laufwerken Folgendes erfüllt ist:

   * Der Kopiercomputer kann über denselben Netzwerkpfad, der während des Workflows im Abschnitt „Initiieren der Offlinesicherung“ angegeben wurde, auf den Stagingspeicherort für den Offlineseeding-Workflow zugreifen.
   * BitLocker ist auf dem Kopiercomputer aktiviert.
   * Azure PowerShell 3.7.0 wurde installiert.
   * Die neuesten kompatiblen Browser (Microsoft Edge oder Internet Explorer 11) sind installiert, und JavaScript ist aktiviert.
   * Der Kopiercomputer kann auf das Azure-Portal zugreifen. Sofern erforderlich, kann der Kopiercomputer mit dem Quellcomputer identisch sein.

     > [!IMPORTANT]
     > Wenn der Quellcomputer ein virtueller Computer ist, muss der Kopiercomputer ein anderer physischer Server- oder Clientcomputer sein.

1. Öffnen Sie auf dem Kopiercomputer eine Eingabeaufforderung mit erhöhten Rechten und dem Verzeichnis des Hilfsprogramms *AzureOfflineBackupDiskPrep* als aktuelles Verzeichnis. Führen Sie den folgenden Befehl aus:

    `.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>`

    | Parameter | Beschreibung |
    | --- | --- |
    | s:&lt;*Pfad zum Stagingspeicherort*&gt; |Mit dieser obligatorischen Eingabe wird der Pfad zum Stagingspeicherort angegeben, den Sie im Workflow im Abschnitt „Initiieren der Offlinesicherung“ eingegeben haben. |
    | p:&lt;*Pfad zu PublishSettingsFile*&gt; |Mit dieser optionalen Eingabe wird der Pfad zur Datei mit den Azure-Veröffentlichungseinstellungen bereitgestellt.  |

    Beim Ausführen des Befehls fordert das Hilfsprogramm die Auswahl des Azure-Importauftrags an, der den vorzubereitenden Datenträgern entspricht. Falls dem angegebenen Stagingspeicherort nur ein einzelner Importauftrag zugeordnet ist, wird eine Seite wie diese angezeigt.

    ![Azure-Tool für die Datenträgervorbereitung – Eingabe](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Geben Sie den Laufwerkbuchstaben ohne nachgestellten Doppelpunkt für den bereitgestellten Datenträger an, den Sie für die Übertragung an Azure vorbereiten möchten.
1. Bestätigen Sie die Formatierung des Laufwerks, wenn Sie dazu aufgefordert werden.
1. Sie werden dazu aufgefordert, sich bei Ihrem Azure-Abonnement anzumelden. Geben Sie Ihre Anmeldeinformationen ein.

    ![Azure-Abonnementanmeldung](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Das Tool beginnt dann, den Datenträger vorzubereiten und die Sicherungsdaten zu kopieren. Falls auf dem bereitgestellten Datenträger nicht genügend Speicherplatz für die Sicherungsdaten verfügbar ist, müssen Sie ggf. weitere Datenträger anfügen, wenn Sie vom Tool dazu aufgefordert werden. <br/>

    Nach erfolgreicher Ausführung des Tools gibt die Befehlszeile drei Informationen zurück:

   1. Die von Ihnen bereitgestellten Datenträger werden für den Versand an Azure vorbereitet.
   1. Sie erhalten eine Erstellungsbestätigung für Ihren Importauftrag. Der Importauftrag hat den von Ihnen angegebenen Namen.
   1. Das Tool zeigt die Lieferadresse für das Azure-Datencenter an.

      ![Azure-Tool für die Datenträgervorbereitung – Abgeschlossen](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Nach Abschluss der Befehlsausführung können Sie die Versandinformationen aktualisieren.

1. Senden Sie die Datenträger an die im Tool angegebene Adresse, und bewahren Sie die Nummer für die Nachverfolgung zur späteren Referenz auf.

   > [!IMPORTANT]
   > Zwei Azure-Importaufträge dürfen nicht dieselbe Nachverfolgungsnummer aufweisen. Stellen Sie sicher, dass die vom Hilfsprogramm unter einem gemeinsamen Azure-Importauftrag vorbereiteten Laufwerke zusammen in einem einzelnen Paket versendet werden und dass das Paket eine einzelne eindeutige Nachverfolgungsnummer besitzt. Fassen Sie Laufwerke, die im Rahmen separater Azure Importaufträge vorbereitet wurden, nicht in einem Paket zusammen.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualisieren der Versanddetails für den Azure-Importauftrag

Das folgende Verfahren dient zum Aktualisieren der Versanddetails für den Azure-Importauftrag. Dazu zählen folgende Informationen:

* Der Name des Transportunternehmens, das die Datenträger an Azure liefert.
* Die Details für den Rückversand Ihrer Datenträger.

1. Melden Sie sich bei Ihrem Azure-Abonnement an.
1. Wählen Sie im Hauptmenü die Option **Alle Dienste** aus. Geben Sie im Dialogfeld **Alle Dienste** **Importieren** ein. Wenn die Option **Import-/Exportaufträge** angezeigt wird, wählen Sie sie aus.

    ![Versandinformationen eingeben](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Das Menü **Import-/Exportaufträge** wird geöffnet, und alle Import-/Exportaufträge im ausgewählten Abonnement werden angezeigt.

1. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das zum Importieren der Sicherungsdaten verwendet wird. Wählen Sie anschließend den neu erstellten Importauftrag aus, um dessen Details anzuzeigen.

    ![Versandinformationen überprüfen](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. Wählen Sie im Menü **Einstellungen** für den Importauftrag die Option **Versandinformationen verwalten** aus, und geben Sie die Details für den Rückversand an.

    ![Versandinformationen speichern](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Wenn Sie die Nachverfolgungsnummer von Ihrem Transportunternehmen erhalten haben, wählen Sie auf der Übersichtsseite des Azure-Importauftrags das Banner aus, und geben Sie folgende Details ein.

   > [!IMPORTANT]
   > Die Angaben zum Transportunternehmen und die Nachverfolgungsnummer müssen innerhalb von zwei Wochen nach Erstellung des Azure-Importauftrags aktualisiert werden. Andernfalls wird der Auftrag unter Umständen gelöscht, und die Datenträger werden nicht verarbeitet.

   ![Aktualisierungswarnung für Nachverfolgungsinformationen](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Angaben zum Transportunternehmen und Nummer für die Nachverfolgung](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Verarbeitungsdauer der Laufwerke

Der Zeitaufwand zum Verarbeiten eines Azure Import/Export-Auftrags variiert. Die Verarbeitungsdauer basiert auf Faktoren wie Versanddauer, Auftragstyp, Typ und Größe der zu kopierenden Daten und Größe der bereitgestellten Datenträger. Der Azure Import/Export-Dienst verfügt über keine Vereinbarung zum Servicelevel (SLA). Nach Erhalt der Datenträger versucht der Dienst jedoch, das Kopieren der Sicherungsdaten in Ihr Azure Storage-Konto innerhalb von sieben bis zehn Tagen fertigzustellen.

### <a name="monitor-azure-import-job-status"></a>Überwachen des Status von Azure-Importaufträgen

Sie können den Status Ihrer Importauftrags im Azure-Portal überwachen. Wechseln Sie zur Seite **Import-/Exportaufträge**, und wählen Sie Ihren Auftrag aus. Weitere Informationen zum Status von Importaufträgen finden Sie unter [Was ist der Azure Import/Export-Dienst?](../import-export/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Workflow abschließen

Nach erfolgreichem Abschluss des Importauftrags sind die Daten der Erstsicherung in Ihrem Speicherkonto verfügbar. Bei der nächsten geplanten Sicherung kopiert Azure Backup den Inhalt der Daten aus dem Speicherkonto in den Recovery Services-Tresor.

   ![Daten in den Recovery Services-Tresor kopieren](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Bei der nächsten geplanten Sicherung führt Azure Backup eine inkrementelle Sicherung durch.

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss der Erstsicherung können die in den Azure Storage-Container importierten Daten und die Sicherungsdaten am Stagingspeicherort gefahrlos gelöscht werden.

## <a name="next-steps"></a>Nächste Schritte

* Falls Sie Fragen zum Workflow des Azure Import/Export-Diensts haben, finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in den Blobspeicher](../import-export/storage-import-export-service.md) weitere Informationen.

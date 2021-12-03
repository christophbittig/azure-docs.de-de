---
title: Versionshinweise zum Azure-Dateisynchronisierungs-Agent | Microsoft-Dokumentation
description: Lesen Sie die Versionshinweise zum Azure-Dateisynchronisierungs-Agent, mit dem Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren können.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 11/9/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 19e1d5f8eab559114f26d10a15c835cf0758b225
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133226"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Versionshinweise zum Azure-Dateisynchronisierungs-Agent
Mit der Azure-Dateisynchronisierung können Sie Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Ihre Windows Server-Installationen werden in einen schnellen Cache Ihrer Azure-Dateifreigabe transformiert. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen (z.B. SMB, NFS und FTPS). Sie können weltweit so viele Caches wie nötig nutzen.

Dieser Artikel enthält die Versionshinweise für die unterstützten Versionen des Azure-Dateisynchronisierungs-Agents.

## <a name="supported-versions"></a>Unterstützte Versionen
Die folgenden Versionen des Agents für die Azure-Dateisynchronisierung werden unterstützt:

| Meilenstein | Agent-Versionsnummer | Veröffentlichungsdatum | Status |
|----|----------------------|--------------|------------------|
| V14-Release [– KB5001872](https://support.microsoft.com/topic/92290aa1-75de-400f-9442-499c44c92a81)| 14.0.0.0 | 29. Oktober 2021 | Unterstützt – Flighting |
| V13 Release – [KB4588753](https://support.microsoft.com/topic/632fb833-42ed-4e4d-8abd-746bd01c1064)| 13.0.0.0 | 12. Juli 2021 | Unterstützt |
| V12.1-Release – [KB4588751](https://support.microsoft.com/topic/497dc33c-d38b-42ca-8015-01c906b96132)| 12.1.0.0 | 20. Mai 2021 | Unterstützt |
| V12 Version - [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26. März 2021 | Unterstützt |
| V11.3-Release: [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 7\. April 2021 | Unterstützt |
| V11.2-Release: [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2\. Februar 2021 | Unterstützt |
| V11.1-Release: [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4\. November 2020 | Unterstützt |

## <a name="unsupported-versions"></a>Nicht unterstützte Versionen
Die folgenden Versionen des Agents für die Azure-Dateisynchronisierung sind abgelaufen und werden nicht mehr unterstützt:

| Meilenstein | Agent-Versionsnummer | Veröffentlichungsdatum | Status |
|----|----------------------|--------------|------------------|
| V10 Release | 10.0.0.0 – 10.1.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 28. Juni 2021 abgelaufen. |
| V9 Release | 9.0.0.0 – 9.1.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 16. Februar 2021 abgelaufen |
| V8-Release | 8.0.0.0 | Nicht zutreffend | Nicht unterstützt – Agent-Versionen sind am 12. Januar 2021 abgelaufen |
| V7 Release | 7.0.0.0 – 7.2.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 1. September 2020 abgelaufen. |
| V6-Release | 6.0.0.0–6.3.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 21. April 2020 abgelaufen. |
| Release V5 | 5.0.2.0–5.2.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 18. März 2020 abgelaufen. |
| Release V4 | 4.0.1.0 – 4.3.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 6. November 2019 abgelaufen. |
| Release V3 | 3.1.0.0 – 3.4.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 19. August 2019 abgelaufen. |
| Pre-GA-Agents | 1.1.0.0 – 3.0.13.0 | – | Nicht unterstützt – Agent-Versionen sind am 1. Oktober 2018 abgelaufen. |

### <a name="azure-file-sync-agent-update-policy"></a>Updaterichtlinie für den Azure-Dateisynchronisierungs-Agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-14000"></a>Agent-Version 14.0.0.0
Die folgenden Versionshinweise gelten für Version 14.0.0.0 des Azure-Dateisynchronisierungs-Agents (Veröffentlichung: 29. Oktober 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme
- Weniger Transaktionen bei der Aufzählung von Cloud-Änderungsenumerationsaufgaben 
    - Azure-Dateisynchronisierung verfügt über einen Cloud-Äänderungsenumerationsauftrag, der alle 24 Stunden ausgeführt wird,um Änderungen zu erkennen, die direkt in der Azure-Dateifreigabe und - synchronisierung vorgenommen wurden, und diese Änderungen mit Servern in Ihren Synchronisierungsgruppen zu synchronisieren. Wir haben Verbesserungen vorgenommen, um die Anzahl der Transaktionen zu reduzieren, wenn dieser Auftrag ausgeführt wird.

- Verbesserte Anleitungen zur Bereitstellung von Serverendpunkten im Portal
    - Wenn Sie einen Serverendpunkt über das Portal entfernen, stellen wir nun eine Schritt-für-Schritt-Anleitung bereit, da nämlich der Serverendpunkt gelöscht werden könnte, sodass Sie Datenverluste vermeiden und sicherstellen können, dass sich Ihre Daten dort befinden, wo sie gespeichert werden müssen (Server- oder Azure-Dateifreigabe). Diese Funktion enthält auch neue PowerShell-Cmdlets (Get-StorageSyncStatus & New-StorageSyncUploadSession), die Sie auf Ihrem lokalen Server verwenden können, um Sie bei dem Aufhebungsprozess der Bereitstellung zu unterstützen.

- Verbesserungen Invoke-AzStorageSyncChangeDetection Cmdlets
    - Wenn Sie Änderungen direkt in der Azure-Dateifreigabe vorgenommen haben, konnten Sie vor der Version 14 das Cmdlet Invoke-AzStorageSyncChangeDetection verwenden, um die Änderungen zu erkennen und mit den Servern in Ihrer Synchronisierungsgruppe zu synchronisieren. Das Cmdlet kann jedoch nicht ausgeführt werden, wenn der angegebene Pfad mehr als 10.000 Elemente enthält. Wir haben das cmdlet Invoke-AzStorageSyncChangeDetection verbessert und das Limit von 10.000 Elementen gilt nicht mehr, wenn die gesamte Freigabe gescannt wird. Weitere Informationen hierzu finden Sie in der Dokumentation von [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).

- Sonstige Verbesserungen
    - Die Azure-Dateisynchronisierung wird jetzt in der Region "USA, Westen 3" unterstützt.
    - Ein Fehler wurde behoben, der dazu führte, dass FileSyncErrorsReport.ps1 Skript nicht die Liste aller Fehler pro Element bereitstellen konnte.
    - Reduzierte Transaktionen, wenn eine Datei aufgrund eines Synchronisierungsfehlers pro Element immer wieder nicht hochgeladen werden kann.
    - Zuverlässigkeits- und Telemetrieverbesserungen für Cloudtiering und Synchronisierung. 

### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](file-sync-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](file-sync-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](file-sync-deployment-guide.md).

- Ein Neustart ist für Server erforderlich, auf denen die Installation eines Azure-Dateisynchronisierungs-Agents vorhanden ist, wenn die Agent-Version niedriger als Version 12.0 ist.
- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 und Windows Server 2022 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden. Weitere Informationen finden Sie unter [Empfohlenen Systemressourcen](file-sync-planning.md#recommended-system-resources).
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](file-sync-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](file-sync-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschoben werden. Wenn der Speichersynchronisierungsdienst oder das Speicherkonto verschoben wurde, müssen Sie der Anwendung „Microsoft.StorageSync“ Zugriff auf das Speicherkonto gewähren (siehe [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Beim Erstellen des Cloudendpunkts müssen sich der Speichersynchronisierungsdienst und das Speicherkonto im selben Azure AD-Mandanten befinden. Nach der Erstellung des Cloudendpunkts können der Speichersynchronisierungsdienst und das Speicherkonto in verschiedene Azure AD Mandanten verschoben werden.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.

## <a name="agent-version-13000"></a>Agent-Version 13.0.0.0
Die folgenden Versionshinweise gelten für Version 13.0.0.0 des Azure-Dateisynchronisierungs-Agents (Veröffentlichung: 12. Juli 2021).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme
- Autoritativer Upload  
    - Autoritativer Upload ist ein neuer Modus, der beim Erstellen des ersten Serverendpunkts in einer Synchronisierungsgruppe verfügbar ist. Dies ist nützlich für das Szenario, in dem die Cloud (Azure-Dateifreigabe) einige bzw. die meisten Daten enthält, aber veraltet ist und mit den neueren Daten auf dem neuen Serverendpunkt auf den neuesten Stand gebracht werden muss. Dies ist beispielsweise in Offlinemigrationsszenarien wie DataBox der Fall. Wenn eine DataBox gefüllt und an Azure gesendet wird, ändern die Benutzer des lokalen Servers weiterhin Dateien auf dem lokalen Server, fügen sie hinzu bzw. löschen sie. Dadurch sind die Daten in der DataBox und somit auf der Azure-Dateifreigabe etwas veraltet. Mit autoritativem Upload können Sie nun dem Server und der Cloud mitteilen, wie sie diesen Fall auflösen sollen, wodurch die Cloud nahtlos mit den neuesten Änderungen auf dem Server aktualisiert wird. 

        Unabhängig davon, wie die Daten in die Cloud gelangt sind, kann dieser Modus die Azure-Dateifreigabe aktualisieren, wenn die Daten vom entsprechenden Speicherort auf dem Server stammen. Achten Sie darauf, umfangreiche Restrukturierungen von Verzeichnissen zwischen der anfänglichen Kopie in der Cloud und dem Aufholen mittels autoritativem Upload zu vermeiden. Dadurch wird sichergestellt, dass Sie nur Updates transportieren. Änderungen an Verzeichnisnamen führen dazu, dass alle Dateien in diesen umbenannten Verzeichnissen erneut hochgeladen werden. Diese Funktionalität ist vergleichbar mit der Semantik von „RoboCopy /MIR“, was dem Spiegeln der Quelle zum Ziel entspricht, einschließlich des Entfernens von Dateien am Ziel, die in der Quelle nicht mehr vorhanden sind. 
        
        Autoritativer Upload ersetzt das Feature „Offlinedatenübertragung“ für die DataBox-Integration durch Azure-Dateisynchronisierung über eine Stagingfreigabe. Für die Verwendung von DataBox ist keine Stagingfreigabe mehr erforderlich. Neue Offlinedatenübertragungsaufträge können nicht mehr mit dem AFS V13-Agent gestartet werden. Vorhandene Aufträge auf einem Server werden weiterhin ausgeführt, selbst nach einem Upgrade auf Agent-Version 13.

- Portalverbesserungen zum Anzeigen der Cloudänderungsenumeration und des Synchronisierungsfortschritts  
    - Wenn eine neue Synchronisierungsgruppe erstellt wird, kann jeder verbundene Serverendpunkt erst mit der Synchronisierung beginnen, wenn die Cloudänderungsenumeration abgeschlossen ist.  Falls Dateien bereits am Cloudendpunkt (Azure-Dateifreigabe) dieser Synchronisierungsgruppe vorhanden sind, kann das Ändern der Enumeration von Inhalten in der Cloud einige Zeit dauern. Je mehr Elemente (Dateien und Ordner) im Namespace vorhanden sind, desto länger kann dieser Vorgang dauern. Administratoren können nun den Fortschritt der Cloudänderungsenumeration im Azure-Portal abrufen, um eine ETA für den Abschluss/den Beginn der Synchronisierung mit den Servern abzuschätzen.

- Unterstützung für Serverumbenennung  
    - Wenn ein registrierter Server umbenannt wird, zeigt die Azure-Dateisynchronisierung jetzt den neuen Servername im Portal an. Wenn der Server vor dem Release v13 umbenannt wurde, wird der Servername im Portal jetzt aktualisiert, damit der richtige Servernamen angezeigt wird.

- Unterstützung für Windows Server 2022  
    - Der Azure-Dateisynchronisierungs-Agent wird jetzt unter Windows Server 2022 unterstützt.

    > [!Note]  
    > Windows Server 2022 fügt Unterstützung für TLS 1.3 hinzu, das derzeit von der Azure-Dateisynchronisierung nicht unterstützt wird. Wenn die [TLS-Einstellungen](/windows-server/security/tls/tls-ssl-schannel-ssp-overview) über eine Gruppenrichtlinie verwaltet werden, muss der Server für die Unterstützung von TLS 1.2 konfiguriert werden. 

- Sonstige Verbesserungen
    - Zuverlässigkeitsverbesserungen für Synchronisierung, Cloud-Tiering und Cloudänderungsenumeration.
    - Wenn eine große Anzahl von Dateien auf dem Server geändert wird, wird der Synchronisierungsupload jetzt von einer VSS-Momentaufnahme aus durchgeführt, was Fehler pro Element und Synchronisierungssitzungsfehler reduziert. 
    - Das Cmdlet „Invoke-StorageSyncFileRecall“ ruft jetzt alle Dateien mit Tiering ab, die einem Serverendpunkt zugeordnet sind, auch wenn die Datei außerhalb des Speicherorts des Serverendpunkts verschoben wurde. 
    - „Explorer.exe“ ist jetzt von der Nachverfolgung der letzten Zugriffszeit beim Cloudtiering ausgeschlossen.
    - Neue Telemetriedaten (Ereignis-ID 6664) zur Überwachung des Bereinigungsfortschritts verwaister Dateien mit Tiering nach dem Entfernen eines Serverendpunkts mit aktiviertem Cloudtiering.


### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](file-sync-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](file-sync-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](file-sync-deployment-guide.md).

- Ein Neustart ist für Server erforderlich, auf denen die Installation eines Azure-Dateisynchronisierungs-Agents vorhanden ist, wenn die Agent-Version niedriger als Version 12.0 ist.
- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 und Windows Server 2022 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden. Weitere Informationen finden Sie unter [Empfohlenen Systemressourcen](file-sync-planning.md#recommended-system-resources).
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](file-sync-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](file-sync-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschoben werden. Wenn der Speichersynchronisierungsdienst oder das Speicherkonto verschoben wurde, müssen Sie der Anwendung „Microsoft.StorageSync“ Zugriff auf das Speicherkonto gewähren (siehe [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Beim Erstellen des Cloudendpunkts müssen sich der Speichersynchronisierungsdienst und das Speicherkonto im selben Azure AD-Mandanten befinden. Nach der Erstellung des Cloudendpunkts können der Speichersynchronisierungsdienst und das Speicherkonto in verschiedene Azure AD Mandanten verschoben werden.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.

## <a name="agent-version-12100"></a>Agent-Version 12.1.0.0
Die folgenden Versionshinweise gelten für Version 12.1.0.0 des Azure-Dateisynchronisierungs-Agents, die am 20. Mai 2021 veröffentlicht wurde. Diese Hinweise gelten zusätzlich zu den Versionshinweisen, die für Version 12.0.0.0 angegeben sind.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme 
Die Agent-Version v12.0 enthielt zwei Fehler, die in dieser Version behoben wurden:
- Beim automatischen Update kann der Agent nicht auf eine höhere Version aktualisiert werden.
- Das Skript „FileSyncErrorsReport.ps1“ stellt nicht die Liste der Fehler pro Element bereit.

## <a name="agent-version-12000"></a>Agent-Version 12.0.0.0
Die folgenden Versionshinweise gelten für Version 12.0.0.0 des Azure-Dateisynchronisierungs-Agent, die am 26. März 2021 veröffentlicht wurde.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme
- Neues Portal zum Konfigurieren von Netzwerk- Zugriffsrichtlinien und privaten Endpunktverbindungen
    - Sie können jetzt über das Portal den Zugriff auf den öffentlichen Endpunkt des Speichersynchronisierungsdienstes deaktivieren und private Endpunktverbindungen genehmigen, ablehnen und entfernen. Öffnen Sie zum Konfigurieren der Richtlinie für den Netzwerkzugriff und der Verbindungen mit privaten Endpunkten das Portal für den Speichersynchronisierungsdienst, wechseln Sie zum Abschnitt „Einstellungen“, und klicken Sie dann auf „Netzwerk“.
 
- Cloudtiering-Unterstützung für Clustergrößen über 64 KiB
    - Cloudtiering unterstützt jetzt Clustergrößen bis zu 2 MiB auf Server 2019. Weitere Informationen erhalten Sie unter [Welche Mindestdateigröße gilt für Dateien, für die ein Tiering durchgeführt werden soll?](./file-sync-choose-cloud-tiering-policies.md#minimum-file-size-for-a-file-to-tier).
 
- Messen der Bandbreite und Latenz für den Azure-Dateisynchronisierungsdienst und das Speicherkonto
    - Das cmdlet „Test-StorageSyncNetworkConnectivity" kann jetzt verwendet werden, um die Latenz und Bandbreite für den Azure-Dateisynchronisierungsdienst und das Speicherkonto zu messen. Die Latenz für den Azure-Dateisynchronisierungsdienst und das Speicherkonto wird standardmäßig beim Ausführen von cmdlet gemessen.  Die Upload und Download-Bandbreite für das Speicherkonto wird bei Verwendung des Parameters "MeasureBandwidth" gemessen.
 
        Führen Sie die folgenden PowerShell-Befehle aus, um die Bandbreite und Latenz für den Azure-Dateisynchronisierungsdienst und das Speicherkonto zu messen:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Verbesserte Fehlermeldungen im Portal, wenn beim Erstellen des Serverendpunkts ein Fehler auftritt
    - Wir haben Ihr Feedback umgesetzt und die Fehlermeldungen und Anleitungen für den Fall verbessert, dass die Erstellung des Serverendpunkt fehlschlägt.
 
- Sonstige Verbesserungen bei Leistung und Zuverlässigkeit
    - Die Leistung bei der Änderungserkennung zum Erkennen von in der Azure-Dateifreigabe geänderten Dateien wurde verbessert.
    - Leistungsverbesserungen zur Abstimmung von Synchronisierungssitzungen. 
    - Synchronisierungsverbesserungen, um Fehler mit ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED und ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED zu verringern.
    - Es wurde der Fehler behoben, der zu Datenbeschädigung führte, wenn Cloudtiering aktiviert war und mehrstufige Dateien mithilfe von Robocopy mit dem /B-Parameter kopiert werden.
    - Es wurde der Fehler behoben, mit dem möglicherweise das Tiering für Dateien auf Server 2019 nicht ausgeführt werden kann, wenn die Datenduplizierung auf dem Datenträger aktiviert wurde.
    - Es wurde der Fehler behoben, der bewirkt, dass afsdiag Dateien nicht komprimiert, wenn eine Datei größer als 2 gib ist.

### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](file-sync-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](file-sync-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](file-sync-deployment-guide.md).

- Für Server mit einer vorhandenen Installation des Azure File Sync-Agenten ist ein Neustart erforderlich.
- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016 und Windows Server 2012 R2 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden. Weitere Informationen finden Sie unter [Empfohlenen Systemressourcen](file-sync-planning.md#recommended-system-resources).
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](file-sync-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](file-sync-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.
- Der Servername im Portal wird bei Umbenennung des Servers nicht aktualisiert.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschoben werden. Wenn der Speichersynchronisierungsdienst oder das Speicherkonto verschoben wurde, müssen Sie der Anwendung „Microsoft.StorageSync“ Zugriff auf das Speicherkonto gewähren (siehe [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Beim Erstellen des Cloudendpunkts müssen sich der Speichersynchronisierungsdienst und das Speicherkonto im selben Azure AD-Mandanten befinden. Nach der Erstellung des Cloudendpunkts können der Speichersynchronisierungsdienst und das Speicherkonto in verschiedene Azure AD Mandanten verschoben werden.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.

## <a name="agent-version-11300"></a>Agent-Version 11.3.0.0
Die folgenden Versionshinweise gelten für die Version 11.3.0.0 des Azure-Dateisynchronisierungs-Agents, die am 7. April 2021 veröffentlicht wurde. Diese Hinweise gelten zusätzlich zu den Versionshinweisen, die für die Version 11.1.0.0 angegeben sind.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme 
Es wurde der Fehler behoben, der zu Datenbeschädigung führte, wenn Cloudtiering aktiviert war und mehrstufige Dateien mithilfe von Robocopy mit dem /B-Parameter kopiert werden.

## <a name="agent-version-11200"></a>Agent-Version 11.2.0.0
Die folgenden Versionshinweise gelten für Version 11.2.0.0 des Azure-Dateisynchronisierungs-Agents, die am 2. Februar 2021 veröffentlicht wurde. Diese Hinweise gelten zusätzlich zu den Versionshinweisen, die für die Version 11.1.0.0 angegeben sind.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme 
- Wenn eine Synchronisierungssitzung aufgrund einer hohen Anzahl von Fehlern pro Element abgebrochen wird, kann die Synchronisierung beim Starten einer neuen Sitzung durch einen Abgleich erfolgen, wenn der Azure-Dateisynchronisierungsdienst feststellt, dass eine benutzerdefinierte Synchronisierungssitzung erforderlich ist, um die Fehler pro Element zu korrigieren.
- Beim Registrieren eines Servers mit dem Cmdlet „Register-AzStorageSyncServer“ tritt möglicherweise ein „Ausnahmefehler“ auf.
- Neues PowerShell-Cmdlet (Add-StorageSyncAllowedServerEndpointPath) zum Konfigurieren zulässiger Serverendpunktpfade auf einem Server. Dieses Cmdlet eignet sich für Szenarien, in denen die Bereitstellung der Azure-Dateisynchronisierung von einem Cloud Solution Provider (CSP) oder Dienstanbieter verwaltet wird und der Kunde zulässige Serverendpunktpfade auf einem Server konfigurieren möchte. Wenn beim Erstellen eines Serverendpunkts der angegebene Pfad nicht in der Zulassungsliste enthalten ist, tritt beim Erstellen dieses Serverendpunkts ein Fehler auf. Beachten Sie, dass es sich hierbei um ein optionales Feature handelt und alle unterstützten Pfade beim Erstellen eines Serverendpunkts standardmäßig zulässig sind.  

    
    - Um einen zulässigen Serverendpunktpfad hinzuzufügen, führen Sie auf dem Server die folgenden PowerShell-Befehle aus:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Um die Liste der unterstützten Pfade abzurufen, führen Sie den folgenden PowerShell-Befehl aus:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Zum Entfernen eines Pfads führen Sie den folgenden PowerShell-Befehl aus:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Agent-Version 11.1.0.0
Die folgenden Versionshinweise gelten für Version 11.1.0.0 des Azure-Dateisynchronisierungs-Agents (Veröffentlichung: 4. November 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme
- Neue Cloudtieringmodi zum Steuern des ersten Downloads und des proaktiven Abrufs
    - Ursprünglicher Downloadmodus: Sie können jetzt auswählen, wie Ihre Dateien anfänglich auf den neuen Serverendpunkt heruntergeladen werden sollen. Möchten Sie Ihre Dateien aufteilen oder so viele Dateien wie möglich gemäß dem Zeitstempel der letzten Änderung auf Ihren Server herunterladen? Das ist kein Problem. Sie können kein Cloudtiering verwenden? Nun können Sie dafür sorgen, dass es auf Ihrem System keine verteilten Dateien mehr gibt. Weitere Informationen finden Sie im Abschnitt [Erstellen eines Serverendpunkts](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) in der Dokumentation zum Bereitstellen der Azure-Dateisynchronisierung.
    - Proaktiver Abrufmodus: Jedes Mal, wenn eine Datei erstellt oder geändert wird, können Sie sie proaktiv auf Servern abrufen, die Sie in derselben Synchronisierungsgruppe angeben. Dadurch wird die Datei auf jedem angegebenen Server für den Verbrauch bereitgestellt. Arbeiten Teams auf der ganzen Welt an denselben Daten? Aktivieren Sie den proaktiven Abruf. Wenn ein Team in einer anderen Zeitzone Dateien aktualisiert, werden diese heruntergeladen und stehen am nächsten Morgen direkt für ein anderes Team bereit. Weitere Informationen finden Sie im Abschnitt [Proaktives Abrufen neuer und geänderter Dateien von einer Azure-Dateifreigabe](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) in der Dokumentation zum Bereitstellen der Azure-Dateisynchronisierung.

- Ausschließen von Anwendungen vom Cloudtiering basierend auf der Nachverfolgung der letzten Zugriffszeit. Sie können nun Anwendungen von der Nachverfolgung der letzten Zugriffszeit ausschließen. Wenn eine Anwendung auf eine Datei zugreift, wird die Uhrzeit des letzten Zugriffs auf die Datei in der Cloudtiering-Datenbank aktualisiert. Anwendungen, die das Dateisystem scannen (z. B. Virenschutz), führen dazu, dass der Zeitpunkt des letzten Zugriffs für alle Dateien gleich ist. Dies wirkt sich auf das Aufteilen von Dateien aus.

    Um Anwendungen aus der Nachverfolgung der letzten Zugriffszeit auszuschließen, fügen Sie der Registrierungseinstellung HeatTrackingProcessNameExclusionList unter „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync“ den Prozessnamen hinzu.

    Beispiel: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Datendeduplizierungs- und FSRM-Prozesse (File Server Resource Manager) werden standardmäßig ausgeschlossen (hartcodiert), und die Prozessausschlussliste wird alle 5 Minuten aktualisiert.

- Sonstige Verbesserungen bei Leistung und Zuverlässigkeit
    - Die Leistung bei der Änderungserkennung zum Erkennen von in der Azure-Dateifreigabe geänderten Dateien wurde verbessert.
    - Die Uploadleistung bei der Synchronisierung wurde verbessert.
    - Der erste Upload wird jetzt von einer VSS-Momentaufnahme ausgeführt. Dadurch werden die Fehler pro Element und Synchronisierungssitzungsfehler verringert.
    - Die Zuverlässigkeit der Synchronisierung für bestimmte E/A-Muster wurde verbessert.
    - Es wurde ein Fehler behoben, der verhinderte, dass die Synchronisierungsdatenbank bei einem Failover in Failoverclustern in der Zeit zurückläuft.
    - Die Rückrufleistung beim Zugriff auf verteilte Dateien wurde verbessert.

### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](../file-sync/file-sync-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](../file-sync/file-sync-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](../file-sync/file-sync-deployment-guide.md).

- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016 und Windows Server 2012 R2 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden. Weitere Informationen finden Sie unter [Empfohlenen Systemressourcen](../file-sync/file-sync-planning.md#recommended-system-resources).
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](../file-sync/file-sync-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.
- Der Servername im Portal wird bei Umbenennung des Servers nicht aktualisiert.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschoben werden. Wenn der Speichersynchronisierungsdienst oder das Speicherkonto verschoben wurde, müssen Sie der Anwendung „Microsoft.StorageSync“ Zugriff auf das Speicherkonto gewähren (siehe [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Beim Erstellen des Cloudendpunkts müssen sich der Speichersynchronisierungsdienst und das Speicherkonto im selben Azure AD-Mandanten befinden. Nach der Erstellung des Cloudendpunkts können der Speichersynchronisierungsdienst und das Speicherkonto in verschiedene Azure AD Mandanten verschoben werden.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.
    > [!Warning]  
    > Der Robocopy /B-Switch wird bei der Azure-Dateisynchronisierung nicht unterstützt. Die Verwendung des Robocopy /B-Switches bei einem Azure-Dateisynchronisierungsserver-Endpunkt als Quelle kann zur Dateibeschädigung führen.

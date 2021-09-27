---
title: Verwalten von Dateien, die von der Azure-Dateisynchronisierung ausgelagert werden | Microsoft-Dokumentation
description: Hier finden Sie Tipps und PowerShell-Cmdlets, die Ihnen bei der Verwaltung ausgelagerter Dateien helfen.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0feb7f6fc9a36e3440001d3a344ada843ea54e0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645700"
---
# <a name="how-to-manage-tiered-files"></a>Verwalten von mehrstufigen Dateien

Dieser Artikel enthält Informationen für Benutzer, die Fragen zur Verwaltung ausgelagerter Dateien haben. Informationen zu grundlegenden Fragen zum Cloudtiering finden Sie unter [Häufig gestellte Fragen (FAQ) zu Azure Files](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Überprüfen, ob Ihre Dateien ausgelagert wurden

Einmal pro Stunde wird ermittelt, ob gemäß den festgelegten Richtlinien ein Tiering von Dateien erforderlich ist. Beim Erstellen eines neuen Serverendpunkts sind zwei Situationen möglich:

Wenn Sie einen neuen Serverendpunkt erstmals hinzufügen, sind an diesem Serverspeicherort häufig Dateien vorhanden. Diese müssen hochgeladen werden, bevor das Cloudtiering erfolgen kann. Die Richtlinie für freien Volumespeicherplatz wird erst wirksam, wenn der erste Upload aller Dateien abgeschlossen ist. Die optionale Datumsrichtlinie wird jedoch auf einzelne Dateien angewendet, sobald eine Datei hochgeladen wurde. Hierbei gilt ebenfalls ein Intervall von einer Stunde.

Wenn Sie einen neuen Serverendpunkt hinzufügen, verbinden Sie möglicherweise einen leeren Serverspeicherort mit einer Azure-Dateifreigabe, auf der sich Ihre Daten befinden. Wenn Sie den Namespace herunterladen und den Inhalt beim ersten Download auf Ihren Server abrufen, werden die Dateien danach auf der Grundlage des Zeitstempels der letzten Änderung abgerufen, bis die Grenzwerte für die Richtlinie für freien Volumespeicherplatz und die optionale Datumsrichtlinie erreicht sind.

Es gibt mehrere Möglichkeiten, um zu überprüfen, ob eine Datei per Tiering auf Ihre Azure-Dateifreigabe ausgelagert wurde:

   - **Überprüfen Sie die Dateiattribute in der Datei.**
     Klicken Sie mit der rechten Maustaste auf eine Datei, navigieren Sie zu **Details**, und scrollen Sie dann nach unten zur Eigenschaft **Attribute**. In einer per Tiering ausgelagerten Datei sind folgende Attribute festgelegt:

        | Attributbuchstabe | attribute | Definition |
        |:----------------:|-----------|------------|
        | Ein | Archivieren | Gibt an, dass die Datei durch Sicherungssoftware gesichert werden sollte. Dieses Attribut ist immer festgelegt, unabhängig davon, ob die Datei per Tiering ausgelagert oder vollständig auf dem Datenträger gespeichert wurde. |
        | P | Sparsedatei | Gibt an, dass die Datei eine „Sparsedatei“ (platzsparende Datei) ist. Eine platzsparende Datei ist ein spezieller Typ von Datei, der von NTFS zur effizienten Verwendung bereitgestellt wird, wenn die Datei im Datenträgerdatenstrom nahezu leer ist. Platzsparende Dateien werden von der Azure-Dateisynchronisierung genutzt, da eine Datei entweder vollständig per Tiering ausgelagert oder teilweise zurückgerufen wurde. Bei einer vollständig per Tiering ausgelagerten Datei wird der Dateidatenstrom in der Cloud gespeichert. Bei einer teilweise zurückgerufenen Datei befindet sich dieser Teil der Datei bereits auf dem Datenträger. Dies kann vorkommen, wenn Dateien von Anwendungen, z.B. Multimedia-Playern oder ZIP-Hilfsprogrammen, teilweise gelesen werden. Wenn eine Datei vollständig auf die Festplatte zurückgerufen wird, konvertiert die Azure-Dateisynchronisierung diese platzsparende Datei in eine normale Datei. Dieses Attribut ist nur unter Windows Server 2016 und früher festgelegt.|
        | M | Abruf beim Datenzugriff | Gibt an, dass die Daten der Datei nicht vollständig im lokalen Speicher vorhanden sind. Das Lesen der Datei bewirkt, dass mindestens ein Teil des Dateiinhalts aus einer Azure-Dateifreigabe abgerufen wird, mit der der Serverendpunkt verbunden ist. Dieses Attribut ist nur unter Windows Server 2019 festgelegt. |
        | L | Analysepunkt | Gibt an, dass die Datei über einen Analysepunkt verfügt. Ein Analysepunkt ist ein spezieller Zeiger für die Verwendung durch einen Dateisystemfilter. Die Azure-Dateisynchronisierung verwendet Analysepunkte, um den Speicherort für den Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) zu definieren, an dem die Datei in der Cloud gespeichert ist. Dies erleichtert den nahtlosen Zugriff. Benutzer müssen nicht wissen, dass die Azure-Dateisynchronisierung verwendet wird oder wie der Zugriff auf die Datei in Ihrer Azure-Dateifreigabe möglich ist. Wenn eine Datei vollständig zurückgerufen wird, entfernt die Azure-Dateisynchronisierung den Analysepunkt aus der Datei. |
        | O | Offline | Gibt an, dass ein Teil des Dateiinhalts oder der gesamte Dateiinhalt nicht auf dem Datenträger gespeichert ist. Wenn eine Datei vollständig zurückgerufen wird, entfernt die Azure-Dateisynchronisierung dieses Attribut. |

        ![Dialogfeld „Eigenschaften“ einer Datei mit ausgewählter Registerkarte „Details“](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)


        > [!NOTE]
        > Sie können die Attribute für alle Dateien in einem Ordner anzeigen, indem das Feld **Attribute** der Tabellenanzeige des Datei-Explorers hinzugefügt wird. Klicken Sie hierzu mit der rechten Maustaste auf eine vorhandene Spalte (z.B. **Größe**). Wählen Sie anschließend **Mehr** und dann in der Dropdownliste **Attribute** aus.

        > [!NOTE]
        > All diese Attribute sind auch für teilweise abgerufene Dateien sichtbar.

   - **Verwenden Sie `fsutil`, um nach Analysepunkten in einer Datei zu suchen.**
       Wie für die vorherige Option beschrieben, verfügt eine per Tiering ausgelagerte Datei immer über einen festgelegten Analysepunkt. Ein Analysepunkt ermöglicht es dem Filtertreiber der Azure-Dateisynchronisierung (StorageSync.sys), Inhalte aus Azure-Dateifreigaben abzurufen, die nicht lokal auf dem Server gespeichert sind.

       Führen Sie das Hilfsprogramm `fsutil` an einer Eingabeaufforderung mit erhöhten Rechten oder in einem PowerShell-Fenster aus, um zu überprüfen, ob eine Datei einen Analysepunkt hat:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

       Wenn die Datei einen Analysepunkt enthält, sollte **Analysentagwert: 0x8000001e** angezeigt werden. Dieser Hexadezimalwert ist der Analysepunktwert, der der Azure-Dateisynchronisierung zuzuordnen ist. Die Ausgabe enthält außerdem die Analysedaten, die für den Pfad zu Ihrer Datei auf der Azure-Dateifreigabe stehen.

        > [!WARNING]
        > Der Hilfsprogrammbefehl `fsutil reparsepoint` bietet außerdem die Möglichkeit, einen Analysepunkt zu löschen. Führen Sie diesen Befehl nur aus, wenn Sie vom Engineering-Team für die Azure-Dateisynchronisierung dazu aufgefordert werden. Bei der Ausführung dieses Befehls kann es zu Datenverlusten kommen.

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Ausschließen von Anwendungen von der Nachverfolgung der letzten Zugriffszeit des Cloudtierings

Wenn eine Anwendung auf eine Datei zugreift, wird die Uhrzeit des letzten Zugriffs auf die Datei in der Cloudtiering-Datenbank aktualisiert. Anwendungen, die das Dateisystem überprüfen (z. B. der Virenschutz), führen dazu, dass der Zeitpunkt des letzten Zugriffs für alle Dateien gleich ist. Dies wirkt sich auf den Zeitpunkt der Auslagerung von Dateien aus.

Um Anwendungen von der Nachverfolgung der letzten Zugriffszeit auszuschließen, fügen Sie der entsprechenden Registrierungseinstellung unter „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync“ den Prozessnamen hinzu.

Fügen Sie die Prozessausschlüsse für die Releases v11 und v12 der Registrierungseinstellung „HeatTrackingProcessNameExclusionList“ hinzu.
Beispiel: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

Fügen Sie die Prozessausschlüsse für das Release v13 und höher der Registrierungseinstellung „HeatTrackingProcessNamesExclusionList“ hinzu.
Beispiel: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNamesExclusionList /t REG_SZ /d "SampleApp.exe,AnotherApp.exe" /f

> [!NOTE]
> Prozesse der Datendeduplizierung und des Ressourcen-Managers für Dateiserver (File Server Resource Manager, FSRM) sind standardmäßig ausgeschlossen. Änderungen an der Prozessausschlussliste werden vom System alle fünf Minuten berücksichtigt.

## <a name="how-to-access-the-heat-store"></a>Zugreifen auf den Wärmespeicher

Beim Cloudtiering wird anhand der Zeit des letzten Zugriffs und der Zugriffshäufigkeit einer Datei bestimmt, welche Dateien ausgelagert werden. Der Cloudtiering-Filtertreiber („storagesync.sys“) verfolgt den Zeitpunkt des letzten Zugriffs und protokolliert die Informationen im Cloudtiering-Wärmespeicher. Sie können den Speicherzustand abrufen und in einer CSV-Datei speichern, indem Sie ein server-local-PowerShell-Cmdlet verwenden.

Für alle Dateien auf demselben Volume ist ein einzelner Wärmespeicher vorhanden. Der Wärmespeicher kann sehr groß werden. Wenn Sie nur die Anzahl der „kältesten“ Elemente abrufen müssen, verwenden Sie „-Limit“ und eine Zahl. Filtern Sie die Ergebnisse ggf. auch nach einem Unterpfad und dem Volumestamm.

- Importieren des PowerShell-Moduls: `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- RICHTLINIE FÜR FREIEN VOLUMESPEICHERPLATZ – Ermitteln der Reihenfolge, in der Dateien mit der Richtlinie für freien Volumespeicherplatz ausgelagert werden: `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- DATUMSRICHTLINIE – Ermitteln der Reihenfolge, in der Dateien mit der Datumsrichtlinie ausgelagert werden: `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Suchen nach den Informationen zum Wärmespeicher für eine bestimmte Datei: `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Anzeigen aller Dateien in absteigender Reihenfolge nach der letzten Zugriffszeit: `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Anzeigen der Reihenfolge, in der ausgelagerte Dateien durch Abrufe im Hintergrund oder bedarfsgesteuerte Abrufe über PowerShell abgerufen werden: `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Erzwingen der Auslagerung einer Datei oder eines Verzeichnisses

> [!NOTE]
> Wenn Sie ein Verzeichnis für das Tiering auswählen, wird es nur auf die Dateien angewendet, die sich derzeit im Verzeichnis befinden. Für alle Dateien, die später erstellt werden, wird kein automatisches Tiering ausgeführt.

Wenn das Cloudtiering-Feature aktiviert ist, wird beim Cloudtiering automatisch basierend auf dem letzten Zugriff und den Änderungszeiten gefiltert, um den auf dem Cloudendpunkt angegebenen freien Volumespeicherplatz zu erzielen. In einigen Fällen kann es auch erforderlich sein, das Tiering für eine Datei manuell zu erzwingen. Dies kann beispielsweise nützlich sein, wenn Sie eine große Datei speichern, die Sie für längere Zeit nicht mehr verwenden werden, und den freien Speicherplatz auf Ihrem Volume nun für andere Dateien und Ordner nutzen möchten. Sie können das Tiering mithilfe der folgenden PowerShell-Befehle erzwingen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Abrufen einer ausgelagerten Datei auf den Datenträger

Der einfachste Weg zum Zurückrufen einer Datei auf den Datenträger besteht darin, sie zu öffnen. Der Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) lädt die Datei nahtlos von Ihrer Azure-Dateifreigabe herunter, ohne dass Sie irgendetwas tun müssen. Bei Dateitypen, die teilweise gelesen oder gestreamt werden können (z. B. Multimedia- oder ZIP-Dateien), wird durch das einfache Öffnen einer Datei nicht sichergestellt, dass die gesamte Datei heruntergeladen wird.

Um sicherzustellen, dass eine Datei vollständig auf den lokalen Datenträger heruntergeladen wird, müssen Sie mithilfe von PowerShell den vollständigen Abruf einer Datei erzwingen. Diese Option kann auch nützlich sein, wenn Sie mehrere Dateien auf einmal abrufen möchten, z. B. alle Dateien in einem Ordner. Öffnen Sie eine PowerShell-Sitzung auf dem Serverknoten, auf dem die Azure-Dateisynchronisierung installiert ist, und führen Sie anschließend die folgenden PowerShell-Befehle aus:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

Optionale Parameter:
- `-Order CloudTieringPolicy` ruft zuerst die Dateien ab, die zuletzt geändert oder auf die zuletzt zugegriffen wurden, wie es die aktuelle Tieringrichtlinie erlaubt. 
    * Wenn die Richtlinie für den freien Volumespeicherplatz konfiguriert ist, werden Dateien abgerufen, bis die Einstellung der Richtlinie für den freien Volumespeicherplatz erreicht ist. Wenn die Einstellung der Richtlinie für den freien Volumespeicherplatz z. B. 20 % beträgt, werden keine Dateien mehr abgerufen, sobald der freie Speicherplatz auf dem Volume 20 % erreicht.  
    * Wenn die Richtlinie für den freien Volumespeicherplatz und die Datumsrichtlinie konfiguriert sind, werden Dateien abgerufen, bis die Einstellung der Richtlinie für den freien Volumespeicherplatz oder der Datumsrichtlinie erreicht ist. Wenn die Einstellung der Richtlinie für den freien Volumenspeicherplatz z. B. 20 % und die Einstellung der Datumsrichtlinie 7 Tage beträgt, werden keine Dateien mehr abgerufen, sobald der freie Speicherplatz auf dem Volume 20 % erreicht wurde oder alle Dateien mit einem Änderungs- oder Zugriffszeitpunkt innerhalb von 7 Tagen lokal gespeichert sind.
- `-ThreadCount` bestimmt, wie viele Dateien parallel abgerufen werden können.
- `-PerFileRetryCount` bestimmt, wie oft ein Abruf einer Datei versucht wird, die derzeit blockiert ist.
- `-PerFileRetryDelaySeconds` bestimmt die Zeit in Sekunden zwischen den Versuchen zum erneuten Abrufen und sollte immer in Kombination mit dem vorherigen Parameter verwendet werden.

Beispiel:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
```

> [!NOTE]  
> - Wenn das lokale Volume, das den Server hostet, nicht genügend freien Speicherplatz aufweist, um alle ausgelagerten Daten abzurufen, schlägt das Cmdlet `Invoke-StorageSyncFileRecall` fehl.

> [!NOTE]
> Um Dateien mit Tiering abzurufen, muss die Netzwerkbandbreite mindestens 1 Mbit/s betragen. Wenn die Netzwerkbandbreite kleiner als 1 Mbit/s ist, misslingt ggf. das Abrufen von Dateien mit einem Timeoutfehler.

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen (FAQ) zu Azure Files](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

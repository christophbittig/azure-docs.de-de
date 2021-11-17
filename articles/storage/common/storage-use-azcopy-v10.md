---
title: Kopieren oder Verschieben von Daten in Azure Storage mit AzCopy v10 | Microsoft-Dokumentation
description: AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Daten in ein oder aus einem Speicherkonto zu kopieren. Dieser Artikel hilft Ihnen, AzCopy herunterzuladen, eine Verbindung mit Ihrem Speicherkonto herzustellen und dann Dateien zu übertragen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/15/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: e2b00c5a0eda3f4679bcbc12a979064cde680e3c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516834"
---
# <a name="get-started-with-azcopy"></a>Erste Schritte mit AzCopy

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel hilft Ihnen, AzCopy herunterzuladen, eine Verbindung mit Ihrem Speicherkonto herzustellen und dann Dateien zu übertragen.

> [!NOTE]
> AzCopy **V10** ist die aktuell unterstützte Version von AzCopy.
>
> Wenn Sie eine vorherige Version von AzCopy verwenden müssen, finden Sie Informationen hierzu im Abschnitt [Verwenden der vorherige Version von AzCopy](#previous-version) in diesem Artikel.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Herunterladen von AzCopy

Laden Sie zunächst die ausführbare Datei für AzCopy V10 in ein beliebiges Verzeichnis auf Ihrem Computer herunter. AzCopy V10 ist nur eine ausführbare Datei, sodass keine Installation erforderlich ist.

- [Windows 64-Bit](https://aka.ms/downloadazcopy-v10-windows) (ZIP)
- [Windows 32-Bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (ZIP)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (ZIP)

Diese Dateien werden als ZIP- (Windows und Mac) oder TAR-Datei (Linux) komprimiert. Informationen zum Herunterladen und Dekomprimieren der TAR-Datei unter Linux finden Sie in der Dokumentation zur Linux-Distribution.

> [!NOTE]
> Wenn Sie Daten in und aus Ihrem [Azure Table Storage](../tables/table-storage-overview.md) kopieren möchten, installieren Sie [AzCopy, Version 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Ausführen von AzCopy

Erwägen Sie aus Gründen der Bedienfreundlichkeit, den Speicherort des Verzeichnisses mit der ausführbaren AzCopy-Datei der Einfachheit halber dem Systempfad hinzuzufügen. Auf diese Weise können Sie in jedem beliebigen Verzeichnis auf Ihrem System `azcopy` eingeben.

Falls Sie das AzCopy-Verzeichnis nicht zum Pfad hinzufügen, müssen Sie in das Verzeichnis der ausführbaren AzCopy-Datei wechseln und in Windows PowerShell-Eingabeaufforderungen `azcopy` oder `.\azcopy` eingeben.

Als Besitzer Ihres Azure Storage-Kontos erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten. Bevor Sie etwas Sinnvolles mit AzCopy machen können, müssen Sie entscheiden, wie Sie dem Speicherdienst die Autorisierungsanmeldeinformationen bereitstellen werden.

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Autorisieren von AzCopy

Sie können die Autorisierungsanmeldeinformationen bereitstellen, indem Sie Azure Active Directory (AD) oder ein SAS-Token (Shared Access Signature) verwenden.

Verwenden Sie diese Tabelle als Richtlinie:

| Speichertyp | Derzeit unterstützte Methode der Autorisierung |
|--|--|
|**Blob Storage** | Azure AD und SAS |
|**Blobspeicher (hierarchischer Namespace)** | Azure AD und SAS |
|**File Storage** | Nur SAS |

#### <a name="option-1-use-azure-active-directory"></a>Option 1: Verwenden von Azure Active Directory

Diese Option ist nur für Blobspeicher verfügbar. Mithilfe von Azure Active Directory können Sie ein einziges Mal Anmeldeinformationen bereitstellen, statt jedem Befehl ein SAS-Token anfügen zu müssen.

> [!NOTE]
> Wenn Sie im aktuellen Release Blobs zwischen Speicherkonten kopieren möchten, müssen Sie an jede Quell-URL ein SAS-Token anfügen. Nur in der Ziel-URL kann das SAS-Token weggelassen werden. Beispiele finden Sie unter [Kopieren von Blobs zwischen Speicherkonten](#transfer-data).

Informationen zum Autorisieren des Zugriffs über Azure AD finden Sie unter [Autorisieren des Zugriffs auf Blobs mit AzCopy und Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Option 2: Verwenden eines SAS-Token

Sie können jeder Quell- oder Ziel-URL, die Sie in Ihren AzCopy-Befehlen verwenden, ein SAS-Token anfügen.

Dieser Beispielbefehl kopiert Daten rekursiv aus einem lokalen Verzeichnis in einen Blob-Container. Am Ende der Container-URL wird ein fiktives SAS-Token angefügt.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Weitere Informationen zu SAS-Tokens und wie Sie eins erhalten, finden Sie unter [Verwenden von SAS (Shared Access Signatures)](./storage-sas-overview.md).

> [!NOTE]
> Über die Einstellung [Sichere Übertragung erforderlich](storage-require-secure-transfer.md) eines Speicherkontos wird festgelegt, ob die Verbindung mit einem Speicherkonto durch TLS (Transport Layer Security) geschützt wird. Die Einstellung ist standardmäßig aktiviert.

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Übertragen von Daten

Nachdem Sie Ihre Identität autorisiert oder ein SAS-Token abgerufen haben, können Sie mit der Übertragung von Daten beginnen.

Beispielbefehle finden Sie in diesen Artikeln.

| Dienst | Artikel |
|--------|-----------|
|Azure Blob Storage|[Hochladen von Dateien in Azure Blob Storage](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Herunterladen von Blobs aus Azure Blob Storage](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|[Kopieren von Blobs zwischen Azure-Speicherkonten](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Synchronisieren mit Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)|
|Amazon S3|[Kopieren von Daten aus Amazon S3 in Azure Storage](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Kopieren von Daten aus Google Cloud Storage in Azure Storage (Vorschau)](storage-use-azcopy-google-cloud.md)|
|Azure Stack-Speicher|[Übertragen von Daten mit AzCopy und Azure Stack-Speicher](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Hilfe zu Befehlen

Um eine Liste der AzCopy-Befehle anzuzeigen, geben Sie `azcopy -h` ein, und drücken Sie dann die EINGABETASTE.

Wenn Sie weitere Informationen zu einem bestimmten Befehl erhalten möchten, fügen Sie einfach den Namen des Befehls ein (etwa `azcopy list -h`).

> [!div class="mx-imgBorder"]
> ![Inlinehilfe](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Befehlsliste

In der folgenden Tabelle sind alle AzCopy v10-Befehle aufgeführt. Jeder Befehl ist mit einem Referenzartikel verknüpft.

|Get-Help|Beschreibung|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Ausführen eines Leistungsvergleichstests, indem Testdaten in einen oder aus einem bestimmten Speicherort hoch- oder heruntergeladen werden|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Kopieren von Quelldaten an einen Zielspeicherort|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Generiert Dokumentation zum Tool im Markdown-Format.|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|Zeigt die Umgebungsvariablen, die das Verhalten von AzCopy konfigurieren können.|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|Unterbefehle im Zusammenhang mit der Verwaltung von Aufträgen.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Entfernen aller Protokoll- und Plandateien für alle Aufträge|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Zeigt Informationen zu allen Aufträgen an.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Entfernt alle Dateien, die der angegebenen Auftrags-ID zugeordnet sind.|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Setzt den vorhandenen Auftrag mit der angegebenen Auftrags-ID fort.|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Zeigt detaillierte Informationen zur angegebenen Auftrags-ID an.|
|[azcopy load](storage-ref-azcopy-load.md)|Unterbefehle im Zusammenhang mit der Übertragung von Daten in bestimmten Formaten|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Überträgt lokale Daten in einen Container und speichert sie im Avere-CLFS-Format (Cloud File System) von Microsoft.|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Listet die Entitäten in einer bestimmten Ressource auf.|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Dient zur Anmeldung bei Azure Active Directory, um den Zugriff auf Azure Storage-Ressourcen zu ermöglichen.|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Meldet den Benutzer ab und beendet den Zugriff auf Azure Storage-Ressourcen.|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Erstellt einen Container oder eine Dateifreigabe.|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Löscht Blobs oder Dateien aus einem Azure Storage-Konto.|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Repliziert den Quellspeicherort an den Zielspeicherort.|

> [!NOTE]
> AzCopy bietet keinen Befehl zum Umbenennen von Dateien.

## <a name="use-in-a-script"></a>Verwenden in einem Skript

#### <a name="obtain-a-static-download-link"></a>Abrufen eines statischen Downloadlinks

Im Laufe der Zeit wird der [Downloadlink](#download-and-install-azcopy) von AzCopy auf neue Versionen von AzCopy verweisen. Wenn Ihr Skript AzCopy herunterlädt, dann kann es sein, dass es nicht mehr funktioniert, wenn eine neuere Version von AzCopy Funktionen verändert, die für Ihr Skript wichtig sind.

Um solche Probleme zu vermeiden, rufen Sie einen statischen (unveränderlichen) Link zur aktuellen Version von AzCopy ab. So lädt Ihr Skript jedes Mal genau dieselbe Version von AzCopy herunter, wenn es ausgeführt wird.

Mit diesem Befehl erhalten Sie den Link:

| Betriebssystem  | Get-Help |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Bei Linux entfernt `--strip-components=1` im `tar`-Befehl den oberen Ordner, der den Versionsnamen enthält, und extrahiert stattdessen die Binärdatei direkt in den aktuellen Ordner. So kann das Skript mit einer neuen Version von `azcopy` aktualisiert werden, indem nur die `wget`-URL aktualisiert wird.

Die URL wird in der Ausgabe dieses Befehls angezeigt. Ihr Skript kann dann AzCopy anhand dieser URL herunterladen.

| Betriebssystem  | Get-Help |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Verwenden von Escapezeichen für Sonderzeichen in SAS-Token

In Batchdateien mit der Erweiterung `.cmd` müssen Sie die `%`-Zeichen, die in SAS-Token vorkommen, mit Escapezeichen versehen. Sie können dazu ein zusätzliches `%`-Zeichen neben vorhandenen `%`-Zeichen in der SAS-Tokenzeichenfolge hinzufügen.

#### <a name="run-scripts-by-using-jenkins"></a>Ausführen von Skripts mithilfe von Jenkins

Wenn Sie beabsichtigen, [Jenkins](https://jenkins.io/) zum Ausführen von Skripts zu verwenden, stellen Sie sicher, dass Sie den folgenden Befehl an den Anfang des Skripts setzen.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Verwenden in Azure Storage-Explorer

Der [Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) verwendet AzCopy zum Ausführen aller seiner Datenübertragungsvorgänge. Sie können [Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) verwenden, wenn Sie die Leistungsvorteile von AzCopy nutzen möchten, aber lieber eine grafische Benutzeroberfläche statt der Befehlszeile verwenden, um mit Ihren Dateien zu interagieren.

Storage-Explorer verwendet Ihren Kontoschlüssel, um Vorgänge auszuführen. Nachdem Sie sich also bei Storage-Explorer anmelden, müssen Sie keine weiteren Autorisierungsdaten angeben.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurieren, Optimieren und Beheben von Fehlern

Sehen Sie sich die folgenden Ressourcen an:

- [AzCopy v10-Konfigurationseinstellungen (Azure Storage)](storage-ref-azcopy-configuration-settings.md)

- [Optimieren der Leistung von AzCopy mit Azure Storage](storage-use-azcopy-optimize.md)

- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Verwenden einer früheren Version

Wenn Sie die vorherige Version von AzCopy verwenden müssen, werden Sie unter den folgenden Links fündig:

- [AzCopy unter Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy unter Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Fragen haben, Probleme melden oder allgemeines Feedback bereitstellen möchten, können Sie dies [auf der GitHub-Seite](https://github.com/Azure/azure-storage-azcopy) tun.

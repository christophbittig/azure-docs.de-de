---
title: Optimieren der Leistung von AzCopy v10 mit Azure Storage | Microsoft-Dokumentation
description: Dieser Artikel hilft Ihnen, die Leistung von AzCopy v10 mit Azure Storage zu optimieren.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f421e2aee0dc6a056cd28cf66a66dda41ce3f9e9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615527"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Optimieren der Leistung von AzCopy mit Azure Storage

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel hilft Ihnen, die Leistung zu optimieren.

> [!NOTE]
> Inhalte, die Sie beim Einstieg in AzCopy unterstützen, finden Sie unter [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md).

Sie können Leistungsvergleichstests durchführen und dann mithilfe von Befehlen und Umgebungsvariablen ein ausgewogenes Verhältnis zwischen Leistung und Ressourcenverbrauch ermitteln.

## <a name="run-benchmark-tests"></a>Ausführen von Vergleichstests

Sie können einen Leistungsvergleichstest für bestimmte Blobcontainer oder Dateifreigaben ausführen, um allgemeine Leistungsstatistiken zu erhalten und Leistungsengpässe zu ermitteln. Sie können den Test ausführen, indem Sie generierte Testdaten hoch- oder herunterladen.

Verwenden Sie den folgenden Befehl, um einen Leistungsvergleichstest auszuführen.

**Syntax**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Beispiel**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> In diesem Beispiel werden Pfadargumente in einfache Anführungszeichen ('') eingeschlossen. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

Mit diesem Befehl wird ein Leistungsvergleichstest ausgeführt, indem Testdaten an ein angegebenes Ziel hochgeladen werden. Die Testdaten werden im Arbeitsspeicher generiert, an das Ziel hochgeladen und dann nach Abschluss des Tests aus dem Ziel gelöscht. Mithilfe optionaler Befehlsparameter können Sie angeben, wie viele Dateien und in welcher Größe generiert werden sollen.

Wenn Sie diesen Test ausführen möchten, indem Sie Daten herunterladen, legen Sie den `mode`-Parameter auf `download` fest. Ausführliche Referenzdokumente finden Sie unter [azcopy benchmark](storage-ref-azcopy-bench.md).

## <a name="optimize-for-large-numbers-of-small-files"></a>Optimieren für eine große Anzahl kleiner Dateien

Der Durchsatz kann bei Übertragung kleiner Dateien abnehmen, insbesondere bei Übertragung einer hohen Anzahl von Dateien. Um die Leistung zu maximieren, verringern Sie die Größe der einzelnen Aufträge. Erhöhen Sie die Parallelität von Download- und Uploadvorgängen, verringern Sie die Protokollaktivität, und deaktivieren Sie Features, die hohe Leistungskosten verursachen.

#### <a name="reduce-the-size-of-each-job"></a>Verringern der Größe der einzelnen Aufträge

Um eine optimale Leistung zu erzielen, stellen Sie sicher, dass bei jedem Auftrag weniger als 10 Millionen Dateien übertragen werden. Aufträge, bei denen mehr als 50 Millionen Dateien übertragen werden, zeigen möglicherweise eine geringe Leistung, weil der AzCopy-Auftragsnachverfolgungsmechanismus zu einem erheblichen Mehraufwand führt. Erwägen Sie die Aufteilung großer Aufträge in kleinere Aufträge, um den Mehraufwand zu reduzieren.

Eine Möglichkeit zum Verkleinern eines Auftrags besteht darin, die Anzahl von Dateien zu begrenzen, die von einem Auftrag betroffen sind. Zu diesem Zweck können Sie Befehlsparameter verwenden. Beispielsweise kann ein Auftrag nur eine Teilmenge von Verzeichnissen kopieren, indem der Parameter `include path` als Teil des Befehls [azcopy copy](storage-ref-azcopy-copy.md) verwendet wird.

Mithilfe des Parameters `include-pattern` können Sie Dateien kopieren, die eine bestimmte Erweiterung aufweisen (z. B. `*.pdf`). Verwenden Sie in einem separaten Auftrag den Parameter `exclude-pattern`, um alle Dateien ohne die Erweiterung `*.pdf` zu kopieren. Beispiele finden Sie unter [Hochladen bestimmter Dateien](storage-use-azcopy-blobs-upload.md#upload-specific-files) und [Herunterladen bestimmter Blobs](storage-use-azcopy-blobs-download.md#download-specific-blobs).

Nachdem Sie entschieden haben, wie große Aufträge in kleinere unterteilt werden, ziehen Sie in Betracht, Aufträge auf mehreren VMs auszuführen.

#### <a name="increase-concurrency"></a>Erhöhen der Parallelität

Verwenden Sie beim Hoch- oder Herunterladen von Dateien die Umgebungsvariable `AZCOPY_CONCURRENCY_VALUE`, um die Anzahl gleichzeitiger Anforderungen auf Ihrem Computer zu erhöhen. Legen Sie diese Variable so hoch wie möglich fest, ohne die Leistung Ihres Computers zu beeinträchtigen. Weitere Informationen zu dieser Variablen finden Sie in diesem Artikel im Abschnitt [Erhöhen der Anzahl gleichzeitiger Anforderungen](#increase-the-number-of-concurrent-requests).

Wenn Sie Blobs zwischen Speicherkonten kopieren, sollten Sie den Wert der Umgebungsvariablen `AZCOPY_CONCURRENCY_VALUE` auf einen höheren Wert als `1000` festlegen. Sie können diese Variable auf einen hohen Wert festlegen, weil AzCopy Server-zu-Server-APIs verwendet, sodass Daten direkt zwischen Speicherservern kopiert werden und die Verarbeitungsleistung Ihres Computers nicht verwendet wird.

#### <a name="decrease-the-number-of-logs-generated"></a>Verringern der Anzahl generierter Protokolle

Sie können die Leistung verbessern, indem Sie die Anzahl der Protokolleinträge reduzieren, die AzCopy bei Abschluss eines Vorgangs erstellt. Standardmäßig protokolliert AzCopy alle Aktivitäten im Zusammenhang mit einem Vorgang. Um eine optimale Leistung zu erzielen, sollten Sie den Parameter `log-level` des Befehls zum Kopieren, Synchronisieren oder Entfernen auf `ERROR` festlegen. Auf diese Weise werden von AzCopy nur Fehler protokolliert. Standardmäßig ist der Protokolliergrad auf `INFO` festgelegt.

#### <a name="turn-off-length-checking"></a>Deaktivieren der Längenüberprüfung

Beim Hoch- oder Herunterladen von Dateien sollten Sie den Wert `--check-length` Ihrer Kopier- oder Synchronisierungsbefehle auf `false` festlegen. Dadurch wird verhindert, dass AzCopy nach einer Übertragung die Länge einer Datei überprüft. Standardmäßig überprüft AzCopy die Länge, um sicherzustellen, dass Quell- und Zieldateien nach Abschluss einer Übertragung übereinstimmen. AzCopy führt diese Überprüfung nach jeder Dateiübertragung durch. Diese Überprüfung kann die Leistung beeinträchtigen, wenn in Aufträgen eine hohe Anzahl kleiner Dateien übertragen wird.

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Aktivieren der gleichzeitigen lokalen Überprüfung (Linux)

Dateiüberprüfungen werden auf einigen Linux-Systemen nicht schnell genug ausgeführt, um alle parallelen Netzwerkverbindungen auszuschöpfen. In diesen Fällen können Sie `AZCOPY_CONCURRENT_SCAN` auf `true` festlegen.

## <a name="increase-the-number-of-concurrent-requests"></a>Erhöhen der Anzahl gleichzeitiger Anforderungen

Sie können den Durchsatz durch Festlegen der Umgebungsvariablen `AZCOPY_CONCURRENCY_VALUE` erhöhen. Diese Variable gibt die zulässige Anzahl gleichzeitiger Anforderungen an.

Wenn Ihr Computer über weniger als 5 CPUs verfügt, wird der Wert dieser Variablen auf `32` festgelegt. Andernfalls ist der Standardwert gleich 16, multipliziert mit der Anzahl der CPUs. Der maximale Standardwert dieser Variablen ist `300`, aber Sie können diesen Wert manuell höher oder niedriger festlegen.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Verwenden Sie `azcopy env`, um den aktuellen Wert dieser Variablen zu überprüfen. Wenn der Wert leer ist, können Sie den verwendeten Wert ermitteln, indem Sie sich den Anfang einer AzCopy-Protokolldatei ansehen. Dort sind der ausgewählte Wert und der Grund aufgeführt, warum er ausgewählt wurde.

Bevor Sie diese Variable festlegen, wird empfohlen, einen Vergleichstest auszuführen. Im Vergleichstest wird der empfohlene Parallelitätswert angegeben. Wenn die Netzwerkbedingungen und Nutzlasten variieren, legen Sie diese Variable alternativ auf den Begriff `AUTO` anstatt auf eine bestimmte Zahl fest. Dies bewirkt, dass von AzCopy immer derselbe automatische Abstimmungsprozess ausgeführt wird, der in Vergleichstests verwendet wird.

## <a name="limit-the-throughput-data-rate"></a>Einschränken der Durchsatzdatenrate

Mithilfe des Flags `cap-mbps` in den Befehlen können Sie eine Obergrenze für die Durchsatzdatenrate festlegen. Mit dem folgenden Befehl wird beispielsweise ein Auftrag fortgesetzt und der Durchsatz auf `10` Megabit (MBit) pro Sekunde begrenzt.

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Optimieren der Arbeitsspeichernutzung

Legen Sie die Umgebungsvariable `AZCOPY_BUFFER_GB` fest, um die maximale Kapazität an Systemarbeitsspeicher festzulegen, die AzCopy zur Pufferung beim Herunterladen und Hochladen von Dateien verwenden soll. Geben Sie diesen Wert in Gigabytes (GB) an.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Die Auftragsverfolgung verursacht immer einen zusätzlichen Overhead in der Speichernutzung. Der Betrag variiert je nach Anzahl der Übertragungen in einem Auftrag. Puffer sind die größte Komponente der Speichernutzung. Mithilfe von `AZCOPY_BUFFER_GB` können Sie den Mehraufwand steuern, um Ihre Anforderungen ungefähr zu erfüllen. Es ist jedoch kein Flag vorhanden, das eine Obergrenze für die gesamte Speicherauslastung festlegt.

## <a name="optimize-file-synchronization"></a>Optimieren der Dateisynchronisierung

Der Befehl [sync](storage-ref-azcopy-sync.md) identifiziert alle Dateien am Ziel und vergleicht dann die Dateinamen und die Zeitstempel der letzten Änderung vor dem Starten des Synchronisierungsvorgangs. Wenn Sie über eine große Anzahl von Dateien verfügen, können Sie die Leistung verbessern, indem Sie diese Vorabverarbeitung vermeiden.

Dazu müssen Sie stattdessen den Befehl [azcopy copy](storage-ref-azcopy-copy.md) verwenden und das Flag `--overwrite` auf `ifSourceNewer` festlegen. AzCopy vergleicht Dateien, während sie kopiert werden, ohne vorab Überprüfungen und Vergleiche auszuführen. In Fällen, in denen sehr viele Dateien verglichen werden müssen, bietet dies einen Leistungsvorteil.

Mit dem Befehl [azcopy copy](storage-ref-azcopy-copy.md) werden keine Dateien aus dem Ziel gelöscht. Wenn Sie also Dateien aus dem Ziel löschen möchten, wenn sie nicht mehr in der Quelle vorhanden sind, verwenden Sie den Befehl [azcopy sync](storage-ref-azcopy-sync.md), und legen Sie dabei das Flag `--delete-destination` auf den Wert `true` oder `prompt` fest.

## <a name="see-also"></a>Weitere Informationen

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)

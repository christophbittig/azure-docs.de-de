---
title: Speicherung und Datenverschiebung für das Rendering
description: Erfahren Sie mehr über die verschiedenen Optionen für die Speicherung und Datenverschiebung zum Rendern von Medienobjekt- und Ausgabedateiworkloads.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 60e2044bc837c986701ec7be048fde04cf6a529a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355828"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Optionen für die Speicherung und Datenverschiebung zum Rendern von Medienobjekt- und Ausgabedateien

Es gibt mehrere Möglichkeiten, wie Sie die Szenen- und Medienobjektdateien für die Renderinganwendungen auf den VMs des Pools bereitstellen können:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md):
  * Szenen- und Medienobjektdateien werden aus einem lokalen Dateisystem in Blobspeicher hochgeladen. Wenn die Anwendung von einer Aufgabe ausgeführt wird, werden die erforderlichen Dateien aus dem Blobspeicher auf die VM kopiert, damit von der Renderinganwendung darauf zugegriffen werden kann. Die Ausgabedateien werden von der Renderinganwendung auf den VM-Datenträger geschrieben und dann in den Blobspeicher kopiert.  Falls erforderlich, können die Ausgabedateien aus dem Blobspeicher in ein lokales Dateisystem heruntergeladen werden.
  * Azure Blob Storage ist eine einfache und kostengünstige Option für kleinere Projekte.  Da auf jeder Pool-VM alle Medienobjektdateien benötigt werden, muss bei steigender Zahl und Größe von Medienobjektdateien sorgfältig sichergestellt werden, dass die Dateiübertragungen so effizient wie möglich durchgeführt werden.  
* Azure-Speicher als Dateisystem mit [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md):
  * Für Linux-VMs kann ein Speicherkonto verfügbar gemacht und als Dateisystem verwendet werden, wenn der virtuelle Dateisystemtreiber „blobfuse“ genutzt wird.
  * Diese Option hat den Vorteil, dass sie sehr kostengünstig ist, weil für das Dateisystem keine VMs benötigt werden. Außerdem werden durch die blobfuse-Zwischenspeicherung auf den VMs mehrfache Downloads derselben Dateien für Aufträge und Aufgaben vermieden.  Auch die Datenverschiebung ist einfach, da die Dateien lediglich Blobs und Standard-APIs und -tools sind. Beispielsweise kann azcopy verwendet werden, um eine Datei zwischen einem lokalen Dateisystem und Azure-Speicher zu kopieren.
* Dateisystem oder Dateifreigabe:
  * Je nach VM-Betriebssystem und den Anforderungen an Leistung und Skalierung gibt es folgende Möglichkeiten: [Azure Files](../storage/files/storage-files-introduction.md), Verwendung einer VM mit angefügten Datenträgern für NFS, Verwendung mehrerer VMs mit angefügten Datenträgern für ein verteiltes Dateisystem, z.B. GlusterFS, oder Verwendung des Angebots eines Drittanbieters.
  * [Avere Systems](https://www.averesystems.com/) ist jetzt Teil von Microsoft und wird in Kürze Lösungen anbieten, die für das Rendering mit großem Umfang und hoher Leistung ideal sind.  Mit der Avere-Lösung wird ein Azure-basierter NFS- oder SMB-Cache erstellt, der zusammen mit Blobspeicher oder mit lokalen NAS-Geräten genutzt werden kann.
  * Mit einem Dateisystem können Dateien gelesen bzw. direkt in das Dateisystem geschrieben oder zwischen dem Dateisystem und den VMs des Pools kopiert werden.
  * Mit einem gemeinsam genutzten Dateisystem kann eine große Zahl von Medienobjekten für mehrere Projekte und Aufträge verwendet werden, und bei den Renderingaufgaben wird nur auf die erforderlichen Komponenten zugegriffen.

## <a name="using-azure-blob-storage"></a>Verwenden von Azure Blob Storage

Es sollte ein Blobspeicherkonto oder ein Speicherkonto vom Typ „Allgemein v2“ verwendet werden.  Diese beiden Typen von Speicherkonten können gegenüber einem Speicherkonto vom Typ „Allgemein v1“ mit deutlich höheren Grenzwerten konfiguriert werden, wie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) beschrieben.  Wenn sie konfiguriert wurden, ermöglichen die höheren Grenzwerte eine viel bessere Leistung und Skalierbarkeit. Dies gilt besonders, wenn viele Pool-VMs auf das Speicherkonto zugreifen.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopieren von Dateien zwischen Client- und Blobspeicher

Zum Kopieren von Dateien in und aus Azure-Speicher können verschiedene Mechanismen verwendet werden, z.B. die Speicherblob-API, die [Azure Storage-Datenverschiebungsbibliothek](https://github.com/Azure/azure-storage-net-data-movement), das Befehlszeilentool azcopy für [Windows](../storage/common/storage-use-azcopy-v10.md) oder [Linux](../storage/common/storage-use-azcopy-v10.md), [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) und [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Bei Verwendung von azcopy können beispielsweise alle Objekte eines Ordners wie folgt übertragen werden:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Der Parameter „/XO“ kann verwendet werden, um nur geänderte Dateien zu kopieren:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopieren von Eingabemedienobjekt-Dateien aus dem Blobspeicher auf Batch-Pool-VMs

Es gibt verschiedene Ansätze zum Kopieren von Dateien, und der beste Ansatz wird anhand der Größe der Medienobjekte eines Auftrags ermittelt.
Der einfachste Ansatz ist das Kopieren aller Medienobjektdateien auf die Pool-VMs für jeden Auftrag:

* Falls Dateien vorhanden sind, die nur für einen Auftrag gelten, aber für alle Aufgaben eines Auftrags erforderlich sind, kann zum Kopieren aller Dateien eine [Auftragsvorbereitungsaufgabe](/rest/api/batchservice/job/add#jobpreparationtask) angegeben werden.  Die Auftragsvorbereitungsaufgabe wird einmal ausgeführt, wenn die erste Auftragsaufgabe auf einer VM ausgeführt wird, aber für nachfolgende Auftragsaufgaben dann nicht mehr.
* Es sollte eine [Auftragsfreigabeaufgabe](/rest/api/batchservice/job/add#jobreleasetask) angegeben werden, um die speziell für einen Auftrag geltenden Dateien zu entfernen, nachdem der Auftrag abgeschlossen ist. So wird verhindert, dass sich der VM-Datenträger mit allen Medienobjektdateien des Auftrags füllt.
* Wenn mehrere Aufträge dieselben Medienobjekte nutzen und für jeden Auftrag an den Medienobjekten nur inkrementelle Änderungen vorgenommen werden, werden auch dann alle Dateien kopiert, wenn nur ein Teil davon aktualisiert wurde.  Dies wäre ineffizient, wenn eine hohe Zahl von großen Medienobjektdateien vorhanden ist.

Falls die Medienobjektdateien zwischen den Aufträgen wiederverwendet werden und es hierbei nur zu inkrementellen Änderungen kommt, besteht ein effizienterer und gleichzeitig etwas aufwändigerer Ansatz darin, Medienobjekte im gemeinsam genutzten Ordner auf der VM zu speichern und geänderte Dateien zu synchronisieren.

* Für die Auftragsvorbereitungsaufgabe wird der Kopiervorgang per azcopy mit dem Parameter „/XO“ für den gemeinsam genutzten VM-Ordner durchgeführt, der über die Umgebungsvariable AZ_BATCH_NODE_SHARED_DIR angegeben wird.  Bei diesem Vorgang werden nur geänderte Dateien auf jede VM kopiert.
* Beim Wählen der Größe für die Medienobjekte muss sorgfältig darauf geachtet werden, dass sie auf das temporäre Laufwerk der Pool-VMs passen.

Azure Batch verfügt über integrierte Unterstützung für das Kopieren von Dateien zwischen einem Speicherkonto und Batch-Pool-VMs.  Mit [Ressourcendateien](/rest/api/batchservice/job/add#resourcefile) für Aufgaben werden Dateien aus dem Speicher auf Pool-VMs kopiert und können für die Auftragsvorbereitungsaufgabe angegeben werden.  Leider besteht bei Hunderten von Dateien auch die Möglichkeit, dass ein Grenzwert erreicht wird und für die Aufgaben ein Fehler auftritt.  Bei einer großen Zahl von Medienobjekten wird empfohlen, das Befehlszeilentool azcopy in der Auftragsvorbereitungsaufgabe zu nutzen, da Platzhalter verwendet werden können und keine Grenzwerte gelten.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopieren von Ausgabedateien in Blobspeicher von Batch-Pool-VMs

[Ausgabedateien](/rest/api/batchservice/task/add#outputfile) können verwendet werden, um Dateien von einer Pool-VM in den Speicher zu kopieren.  Es können eine oder mehrere Dateien von der VM in ein angegebenes Speicherkonto kopiert werden, nachdem die Aufgabe abgeschlossen ist.  Die gerenderte Ausgabe sollte kopiert werden, aber es kann auch ratsam sein, Protokolldateien zu speichern.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Verwenden eines virtuellen blobfuse-Dateisystems für Linux-VM-Pools

„blobfuse“ ist ein virtueller Dateisystemtreiber für Azure Blob Storage, der Ihnen den Zugriff auf Dateien, die als Blobs in einem Storage-Konto gespeichert sind, über das Linux-Dateisystem ermöglicht.

Für Poolknoten kann das Dateisystem beim Starten bereitgestellt werden, oder die Bereitstellung kann im Rahmen einer Auftragsvorbereitungsaufgabe erfolgen. Eine Aufgabe wird nur ausgeführt, wenn die erste Aufgabe eines Auftrags auf einem Knoten ausgeführt wird.  blobfuse kann so konfiguriert werden, dass sowohl ein Ramdisk-Datenträger als auch die lokale SSD der VMs zum Zwischenspeichern von Dateien genutzt werden, um die Leistung erheblich zu steigern, wenn mehrere Aufgaben auf einem Knoten teilweise auf dieselben Dateien zugreifen.

Es sind [Beispielvorlagen](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) verfügbar, mit denen eigenständige V-Ray-Rendervorgänge mit einem blobfuse-Dateisystem ausgeführt werden können und die als Basis für Vorlagen anderer Anwendungen dienen können.

### <a name="accessing-files"></a>Zugreifen auf Dateien

Über Auftragsaufgaben werden Pfade für Eingabe- und Ausgabedateien angegeben, indem das bereitgestellte Dateisystem verwendet wird.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopieren von Eingabemedienobjekt-Dateien aus dem Blobspeicher auf Batch-Pool-VMs

Da es sich bei den Dateien einfach um Blobs in Azure Storage handelt, können standardmäßige Blob-APIs, Tools und Benutzeroberflächen genutzt werden, um Dateien zwischen einem lokalen Dateisystem und Blobspeicher zu kopieren, z.B. azcopy, Storage-Explorer, Batch Explorer usw.

## <a name="using-azure-files-with-windows-vms"></a>Verwenden von Azure Files mit Windows-VMs

[Azure Files](../storage/files/storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das SMB-Protokoll zugegriffen werden kann.  Azure Files basiert auf Azure Blob Storage, ist [kostengünstig](https://azure.microsoft.com/pricing/details/storage/files/) und kann für die Datenreplikation in einer anderen Region konfiguriert werden, um globale Redundanz zu erzielen.  Die [Skalierbarkeitsziele](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) sollten überprüft werden, um zu ermitteln, ob die Verwendung von Azure Files sinnvoll ist. Dies richtet sich nach der prognostizierten Poolgröße und der Anzahl von Medienobjektdateien.

Die Einbindung einer Azure Files-Freigabe wird in [dieser Dokumentation](../storage/files/storage-how-to-use-files-windows.md) beschrieben.

### <a name="mounting-an-azure-files-share"></a>Bereitstellen einer Azure Files-Freigabe

Für die Verwendung in Batch muss bei jeder Ausführung einer Aufgabe ein Bereitstellungsvorgang durchgeführt werden, da es nicht möglich ist, die Verbindung zwischen den Aufgaben beizubehalten.  Die einfachste Möglichkeit ist hierbei die Verwendung von cmdkey, um Anmeldeinformationen beizubehalten, indem die Startaufgabe in der Poolkonfiguration verwendet wird. Anschließend wird die Freigabe vor jeder Aufgabe bereitgestellt.

Beispiel für die Verwendung von cmdkey in einer Poolvorlage (für die Nutzung in einer JSON-Datei mit Escapezeichen versehen): Beachten Sie, dass der Benutzerkontext beim Trennen des cmdkey-Aufrufs vom „net use“-Aufruf für die Startaufgabe dem Benutzerkontext für die Ausführung der Aufgaben entsprechen muss:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Beispiel für Auftragsaufgaben-Befehlszeile:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Zugreifen auf Dateien

Mit Auftragsaufgaben werden Pfade für Eingabe- und Ausgabedateien angegeben, indem das bereitgestellte Dateisystem verwendet wird – entweder mit einem zugeordneten Laufwerk oder über einen UNC-Pfad.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopieren von Eingabemedienobjekt-Dateien aus dem Blobspeicher auf Batch-Pool-VMs

Azure Files wird von allen wichtigen APIs und Tools unterstützt, die über Azure Storage-Unterstützung verfügen, z.B. azcopy, Azure CLI, Storage-Explorer, Azure PowerShell, Batch Explorer usw.

Die [Azure-Dateisynchronisierung](../storage/file-sync/file-sync-planning.md) ist verfügbar, um Dateien zwischen einem lokalen Dateisystem und einer Azure Files-Freigabe automatisch zu synchronisieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Speicheroptionen finden Sie in der ausführlichen Dokumentation:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)

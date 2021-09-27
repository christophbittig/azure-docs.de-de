---
title: Bewährte Methoden für die Notfallwiederherstellung mit der Azure-Dateisynchronisierung
description: Erfahren Sie mehr über bewährte Methoden für die Notfallwiederherstellung mit der Azure-Dateisynchronisierung, insbesondere im Hinblick auf die Hochverfügbarkeit, den Datenschutz und die Datenredundanz.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f5928815b6559c257319eb625587135cbf99df0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645738"
---
# <a name="best-practices-for-disaster-recovery-with-azure-file-sync"></a>Bewährte Methoden für die Notfallwiederherstellung mit der Azure-Dateisynchronisierung

Für die Azure-Dateisynchronisierung sind drei Hauptbereiche für die Notfallwiederherstellung zu berücksichtigen: Hochverfügbarkeit, Datenschutz/Sicherung und Datenredundanz. Dieser Artikel behandelt die einzelnen Bereiche und hilft Ihnen bei der Entscheidung, welche Konfiguration für Ihre eigene Notfallwiederherstellungslösung verwendet werden soll.

Bei der Bereitstellung der Azure-Dateisynchronisierung enthält der Cloudendpunkt immer eine vollständige Kopie Ihrer Daten, während der lokale Server als verwerfbarer Cache Ihrer Daten angesehen werden kann. Bei einem serverseitigen Notfall können Sie die Wiederherstellung durchführen, indem Sie einen neuen Server bereitstellen, den Azure-Dateisynchronisierungs-Agent darauf installieren und ihn als neuen Serverendpunkt einrichten.

Aufgrund der Hybridität funktionieren einige herkömmliche Strategien zur Serversicherung und Notfallwiederherstellung nicht mit der Azure-Dateisynchronisierung. Für registrierte Server unterstützt die Azure-Dateisynchronisierung Folgendes nicht:

> [!WARNING]
> Die Ausführung einer dieser Aktionen kann zu Problemen mit der Synchronisierung oder zu beschädigten mehrstufigen Dateien führen, was schließlich zu einem Datenverlust führt. Wenn Sie eine dieser Aktionen ausgeführt haben, wenden Sie sich an den Azure-Support, um sicherzustellen, dass Ihre Bereitstellung fehlerfrei ist.

- Übertragen von Datenträgerlaufwerken von einem Server auf einen anderen
- Wiederherstellen aus einer Betriebssystemsicherung
- Klonen des Betriebssystems eines Servers auf einen anderen Server
- Zurückkehren zu einem vorherigen Prüfpunkt eines virtuellen Computers
- Wiederherstellen von Dateien aus einer lokalen Sicherung, wenn Cloudtiering aktiviert ist

## <a name="high-availability"></a>Hochverfügbarkeit

Es gibt zwei verschiedene Strategien, die Sie verwenden können, um Hochverfügbarkeit für Ihren lokalen Server zu erreichen. Sie können entweder einen Failovercluster oder einen Standbyserver konfigurieren. Der entscheidende Faktor zwischen beiden Konfigurationen ist, wie viel Sie bereit sind, in Ihr System zu investieren, und ob die Minimierung der Zeitdauer, die Ihr System bei einem Notfall ausfällt, diese zusätzlichen Kosten wert ist.

Für einen Failovercluster müssen Sie keine besonderen Schritte ausführen, um die Azure-Dateisynchronisierung zu verwenden. Für einen Standbyserver sollten Sie die folgenden Konfigurationen vornehmen:

Sie sollten über einen sekundären Server mit unterschiedlichen Serverendpunkten verfügen, die mit derselben Synchronisierungsgruppe wie Ihr primärer Server synchronisiert werden, jedoch keinen Endbenutzerzugriff auf den Server ermöglichen. Dadurch können alle Dateien vom primären Server mit dem Standbyserver synchronisiert werden. Sie können überlegen, ob Sie das rein namespacebasierte Tiering aktivieren, so dass zunächst nur der Namespace heruntergeladen wird. Wenn Ihr primärer Server ausfällt, können Sie DFS-N verwenden, um den Endbenutzerzugriff auf Ihren Standbyserver schnell neu zu konfigurieren.

## <a name="data-protectionbackup"></a>Datenschutz/Sicherung

Der Schutz Ihrer tatsächlichen Daten ist eine wichtige Komponente einer Notfallwiederherstellungslösung. Es gibt zwei Hauptmethoden, dies mit Ihren Azure-Dateifreigaben zu erreichen: Sie können Ihre Daten entweder in der Cloud oder lokal sichern. Es wird dringend empfohlen, Ihre Daten in der Cloud zu sichern, da Ihr Cloudendpunkt eine vollständige Kopie Ihrer Daten enthält, während Serverendpunkte möglicherweise nur eine Teilmenge Ihrer Daten enthalten.

### <a name="back-up-your-data-in-the-cloud"></a>Sichern Ihrer Daten in der Cloud

Sie sollten [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json) als Ihre Cloud-Sicherungslösung verwenden. Azure Backup übernimmt unter anderem die Planung, Aufbewahrung und Wiederherstellung von Sicherungen. Wenn Sie möchten, können Sie manuell [Freigabemomentaufnahmen](../files/storage-snapshots-files.md?toc=/azure/storage/file-sync/toc.json) erstellen und Ihre eigene Planungs- und Aufbewahrungslösung konfigurieren, was jedoch nicht ideal ist. Alternativ können Sie Lösungen von Drittanbietern verwenden, um Ihre Azure-Dateifreigaben direkt zu sichern.

Wenn ein Notfall auftritt, können Sie eine Wiederherstellung aus einer Freigabemomentaufnahme durchführen, bei der es sich um eine schreibgeschützte Zeitpunktkopie Ihrer Dateifreigabe handelt. Da diese Momentaufnahmen schreibgeschützt sind, sind sie nicht von Ransomware betroffen. Bei großen Datasets, bei denen vollständige Freigabewiederherstellungsvorgänge viel Zeit in Anspruch nehmen, können Sie einen direkten Benutzerzugriff auf die Momentaufnahme ermöglichen, so dass Benutzer die benötigten Daten auf ihr lokales Laufwerk kopieren können, während die Wiederherstellung durchgeführt wird.

Momentaufnahmen werden direkt in Ihrer Azure-Dateifreigabe gespeichert, unabhängig davon, ob sie von Ihnen manuell oder von Azure Backup für Sie erstellt werden. Daher sollten Sie [vorläufiges Löschen aktivieren](../files/storage-files-prevent-file-share-deletion.md?toc=/azure/storage/file-sync/toc.json), um Ihre Momentaufnahmen vor versehentlichem Löschen Ihrer Dateifreigabe zu schützen.

Weitere Informationen finden Sie unter [Informationen zum Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md), oder wenden Sie sich an Ihren Sicherungsanbieter, um zu erfahren, ob dieser das Sichern von Azure-Dateifreigaben unterstützt.

### <a name="back-up-your-data-on-premises"></a>Lokales Sichern Ihrer Daten

Wenn Sie Cloudtiering aktivieren, implementieren Sie keine lokale Sicherungslösung. Wenn Cloudtiering aktiviert ist, wird nur eine Teilmenge Ihrer Daten lokal auf Ihrem Server gespeichert. Die restlichen Daten werden in Ihrem Cloudendpunkt gespeichert. Je nachdem, welche Sicherungslösung Sie für eine lokale Sicherung verwenden, werden mehrstufige Dateien entweder übersprungen und nicht gesichert (aufgrund ihres FILE_ATTRIBUTE_RECALL_ONDATA_ACCESS-Attributs), sie werden nur als mehrstufige Datei gesichert und sind bei der Wiederherstellung aufgrund von Änderungen in der Livefreigabe möglicherweise nicht zugänglich oder sie werden auf Ihren Datenträger zurückgerufen, was zu hohen Gebühren für ausgehende Daten führt.

Wenn Sie sich für eine lokale Sicherungslösung entscheiden, sollten die Sicherungen auf einem Server in der Synchronisierungsgruppe ausgeführt werden, wobei das Cloudtiering deaktiviert ist. Wenn Sie eine Wiederherstellung durchführen, verwenden Sie die Wiederherstellungsoptionen auf Volume- oder Dateiebene. Mithilfe der Wiederherstellungsoption auf Dateiebene wiederhergestellte Dateien werden auf allen Endpunkten in der Synchronisierungsgruppe synchronisiert. Dabei werden vorhandene Dateien durch die aus der Sicherung wiederhergestellte Version ersetzt. Bei der Wiederherstellung auf Volumeebene werden die neueren Dateiversionen auf dem Cloudendpunkt oder auf anderen Serverendpunkten nicht ersetzt.

Beim Azure-Dateisynchronisierungs-Agent ab Version 9 werden [VSS-Momentaufnahmen (VSS – Volume Shadow Copy Service (Volumeschattenkopie-Dienst))](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service) (einschließlich der Registerkarte **Vorherige Versionen**) auf Volumes mit aktiviertem Cloudtiering unterstützt. Dadurch können Sie Self-Service-Wiederherstellungen durchführen, anstatt sich auf einen Administrator zu verlassen, der Wiederherstellungen für Sie durchführt. Allerdings müssen Sie die Kompatibilität mit vorherigen Versionen über PowerShell aktivieren, wodurch die Speicherkosten für Momentaufnahmen erhöht werden. VSS-Momentaufnahmen schützen nicht vor Notfällen auf dem Serverendpunkt selbst, daher sollten sie nur zusammen mit cloudseitigen Sicherungen verwendet werden. Einzelheiten dazu finden Sie unter [Self-Service-Wiederherstellung mit „Vorherige Versionen“ und VSS](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-redundancy"></a>Datenredundanz

Um eine stabile Notfallwiederherstellungslösung sicherzustellen, fügen Sie Ihrer Infrastruktur eine Form von Datenredundanz hinzu. Es gibt vier Redundanzangebote für Azure Files: [lokal redundanter Speicher (LRS)](../common/storage-redundancy.md#locally-redundant-storage), [zonenredundanter Speicher (ZRS)](../common/storage-redundancy.md#zone-redundant-storage), [georedundanter Speicher (GRS)](../common/storage-redundancy.md#geo-redundant-storage) und [geozonenredundanter Speicher (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage).

- [Lokal redundanter Speicher (LRS)](../common/storage-redundancy.md#locally-redundant-storage): Bei LRS wird jede Datei in einem Azure-Speichercluster dreimal gespeichert. Dies schützt vor Datenverlusten aufgrund von Hardwarefehlern, z. B. bei einem schadhaften Laufwerk. Bei einem Katastrophenfall in einem Rechenzentrum (Feuer, Überschwemmung usw.) gehen jedoch eventuell alle Replikate in einem Speicherkonto, das LRS verwendet, verloren oder können nicht mehr wiederhergestellt werden.
- [Zonenredundanter Speicher (ZRS)](../common/storage-redundancy.md#zone-redundant-storage) : Bei ZRS werden drei Kopien jeder Datei gespeichert. Diese Kopien werden jedoch physisch in drei verschiedenen Speicherclustern in verschiedenen Azure-*Verfügbarkeitszonen* isoliert. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Ein Schreibvorgang in den Speicher wird erst akzeptiert, wenn die Daten in allen drei Verfügbarkeitszonen in die Speichercluster geschrieben wurden.
- [Georedundanter Speicher (GRS)](../common/storage-redundancy.md#geo-redundant-storage) : Bei GRS verfügen Sie über zwei Regionen, eine primäre und eine sekundäre Region. Dateien werden dreimal in einem Azure-Speichercluster in der primären Region gespeichert. Schreibvorgänge werden asynchron in eine von Microsoft definierte sekundäre Region repliziert. GRS bietet sechs Kopien Ihrer Daten, die auf zwei Azure-Regionen verteilt sind.
- [Geozonenredundanter Speicher (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage): Sie können sich GZRS wie ZRS vorstellen, jedoch mit Georedundanz. Bei GZRS werden Dateien dreimal in drei verschiedenen Speicherclustern in der primären Region gespeichert. Alle Schreibvorgänge werden dann asynchron in eine von Microsoft definierte sekundäre Region repliziert.

Für eine stabile Notfallwiederherstellungslösung sollten die meisten Kunden ZRS in Betracht ziehen. ZRS bringt die geringsten Zusatzkosten für die zusätzlichen Datenredundanzvorteile mit sich und ist auch die nahtloseste Option bei einem Ausfall. Wenn die Richtlinien Ihrer Organisation oder gesetzliche Anforderungen Georedundanz für Ihre Daten erfordern, sollten Sie entweder GRS oder GZRS in Betracht ziehen.

### <a name="geo-redundancy"></a>Georedundanz

Wenn Sie GRS oder GZRS für das Speicherkonto aktivieren, das Ihren Cloudendpunkt enthält, müssen Sie dies auch in Ihrem Speichersynchronisierungsdienst aktivieren. Dadurch wird sichergestellt, dass alle Informationen zu Ihrer Azure-Dateisynchronisierungstopologie und die in Ihrem Cloudendpunkt enthaltenen Daten bei einem Notfall asynchron in die gekoppelte sekundäre Region kopiert werden.

Für Ressourcen, die entweder mit GRS oder GZRS konfiguriert sind, initiiert Microsoft das Failover für Ihren Dienst, wenn eingeschätzt wird, dass die primäre Region dauerhaft nicht wiederhergestellt werden kann oder für einen längeren Zeitraum nicht verfügbar ist. Der Azure-Dateisynchronisierungsdienst führt bei einem Notfall in einer Region automatisch ein Failover auf die gekoppelte Region aus, wenn der Speichersynchronisierungsdienst GRS oder GZRS verwendet. Wenn Sie die mit GRS oder GZRS konfigurierte Azure-Dateisynchronisierung verwenden, ist bei einem Notfall keine Aktion Ihrerseits erforderlich.

Obwohl Sie manuell ein Failover Ihres Speichersynchronisierungsdiensts auf Ihre gekoppelte GRS- oder GZRS-Region anfordern können, wird dies außer bei umfangreichen regionalen Ausfällen nicht empfohlen, da der Prozess nicht nahtlos ist und zusätzliche Kosten verursachen kann. Öffnen Sie zum Initiieren des Prozesses ein Supportticket und fordern Sie an, dass sowohl für Ihre Azure-Speicherkonten, die Ihre Azure-Dateifreigabe enthalten, als auch für Ihren Speichersynchronisierungsdienst ein Failover ausgeführt wird.

> [!WARNING]
> Sie müssen sich an den Support wenden, um ein Failover Ihres Speichersynchronisierungsdiensts anzufordern, wenn Sie diesen Prozess manuell initiieren. Wenn Sie versuchen, einen neuen Speichersynchronisierungsdienst mit denselben Serverendpunkten in der sekundären Region zu erstellen, können zusätzliche Daten in Ihrem Speicherkonto verbleiben, da die vorherige Installation der Azure-Dateisynchronisierung nicht bereinigt wird.

Sobald ein Failover erfolgt, wechseln Serverendpunkte zur automatischen Synchronisierung mit dem Cloudendpunkt in der sekundären Region. Die Serverendpunkte müssen jedoch mit den Cloudendpunkten abgeglichen werden. Dies kann zu Dateikonflikten führen, da die Daten in der sekundären Region möglicherweise noch nicht mit denen in der primären Region auf demselben Stand sind.

## <a name="next-steps"></a>Nächste Schritte

[Learn about Azure file share backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json) (Informationen zur Sicherung von Azure-Dateifreigaben)

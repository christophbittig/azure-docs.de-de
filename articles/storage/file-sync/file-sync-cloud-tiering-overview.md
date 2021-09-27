---
title: Grundlegendes zum Cloudtiering der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Informieren Sie sich über Cloudtiering, ein optionales Feature der Azure-Dateisynchronisierung. Dateien, auf die häufig zugegriffen wird, werden lokal auf dem Server gespeichert. andere werden in Azure Files ausgelagert.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f0ae8b5a4ac2572719340020695a5bcd6b0d8a2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601347"
---
# <a name="cloud-tiering-overview"></a>Übersicht über Cloudtiering

Cloudtiering, ein optionales Feature der Azure-Dateisynchronisierung, verringert die benötigte Menge an lokalem Speicher, ohne die Leistung eines lokalen Dateiservers zu beeinträchtigen.

Wenn dieses Feature aktiviert ist, werden nur Dateien, auf die häufig zugegriffen wird (heiße Dateien), auf dem lokalen Server gespeichert. Dateien, auf die nicht oft zugegriffen wird (kalte Dateien), werden in den Namespace (Datei- und Ordnerstruktur) und den Dateiinhalt aufgeteilt. Der Namespace wird lokal gespeichert, und der Dateiinhalt wird in einer Azure-Dateifreigabe in der Cloud gespeichert.

Wenn ein Benutzer eine ausgelagerte Datei öffnet, ruft die Azure-Dateisynchronisierung die Dateidaten nahtlos aus der Dateifreigabe in Azure ab.

## <a name="how-cloud-tiering-works"></a>Funktionsweise des Cloudtierings

### <a name="cloud-tiering-policies"></a>Cloudtieringrichtlinien

Wenn Sie das Cloudtiering aktivieren, können Sie mithilfe von zwei Richtlinien festlegen, wann die Azure-Dateisynchronisierung kalte Dateien auslagern soll: mit der **Richtlinie für freien Volumespeicherplatz** und mit der **Datumsrichtlinie**.

#### <a name="volume-free-space-policy"></a>Richtlinie für freien Volumespeicherplatz

Die **Richtlinie für freien Volumespeicherplatz** weist die Azure-Dateisynchronisierung an, kalte Dateien in die Cloud auszulagern, wenn auf Ihrem lokalen Datenträger eine bestimmte Menge an Speicherplatz belegt ist.

Wenn die Kapazität des lokalen Datenträgers beispielsweise 200 GiB beträgt und Sie möchten, dass immer mindestens 40 GiB auf dem lokalen Datenträger frei bleiben, legen Sie die Richtlinie für freien Volumespeicherplatz auf 20 % fest. Der freie Volumespeicherplatz gilt auf Volumeebene und nicht auf der Ebene der einzelnen Verzeichnisse oder Serverendpunkte.

#### <a name="date-policy"></a>Datumsrichtlinie

Mit der **Datumsrichtlinie** werden kalte Dateien in die Cloud ausgelagert, auf die für eine bestimmte Anzahl von Tagen nicht zugegriffen wurde (d. h. kein Lese- oder Schreibzugriff). Wenn Sie beispielsweise feststellen, dass es sich bei den Dateien, auf die seit mehr als 15 Tagen nicht zugegriffen wurde, in der Regel um Archivdateien handelt, legen Sie die Datumsrichtlinie auf 15 Tage fest.

Weitere Beispiele dazu, wie die Datumsrichtlinie und die Richtlinie für freien Volumespeicherplatz zusammen verwendet werden können, finden Sie unter [Auswählen von Cloudtieringrichtlinien der Azure-Dateisynchronisierung](file-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>Windows Server-Datendeduplizierung

Die Datendeduplizierung wird für Volumes mit aktiviertem Cloudtiering ab Windows Server 2016 unterstützt. Weitere Informationen finden Sie unter [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](file-sync-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Wärmebild für das Cloudtiering

Die Azure-Dateisynchronisierung überwacht den Dateizugriff (Lese- und Schreibvorgänge) im Laufe der Zeit und weist jeder Datei basierend auf der Häufigkeit und Aktualität des Zugriffs eine Wärmebewertung zu. Diese Bewertungen werden verwendet, um auf jedem Serverendpunkt ein „Wärmebild“ Ihres Namespaces zu erstellen. Bei dem Wärmebild handelt es sich um eine Liste aller synchronisierten Dateien an einem Speicherort mit aktiviertem Cloudtiering (geordnet nach der Wärmebewertung). Dateien, auf die häufig zugegriffen wird und die vor Kurzem geöffnet wurden, gelten als „heiße Dateien“. Dateien, die kaum verwendet wurden und auf die für längere Zeit nicht zugegriffen wurde, werden dagegen als „kalte Dateien“ eingestuft.

Zur Bestimmung der relativen Position einer einzelnen Datei im Wärmebild legt das System den höchsten Zeitstempel in der folgenden Reihenfolge zugrunde: MAX(Zeitpunkt des letzten Zugriffs, Zeitpunkt der letzten Änderung, Erstellungszeitpunkt).

In der Regel wird der Zeitpunkt des letzten Zugriffs nachverfolgt und verfügbar gemacht. Wenn jedoch ein neuer Serverendpunkt mit aktiviertem Cloudtiering erstellt wird, ist nicht genügend Zeit zum Überwachen des Dateizugriffs vergangen. Ist kein gültiger Zeitpunkt des letzten Zugriffs verfügbar, wird stattdessen der Zeitpunkt der letzten Änderung verwendet, um die relative Position im Wärmebild zu ermitteln.

Die Datumsrichtlinie funktioniert auf die gleiche Weise. Wenn der Zeitpunkt des letzten Zugriffs nicht verfügbar ist, zieht die Datumsrichtlinie den Zeitpunkt der letzten Änderung heran. Sollte dieser nicht verfügbar sein, wird auf den Erstellungszeitpunkt einer Datei zurückgegriffen. Das System überwacht im Laufe der Zeit mehr Dateizugriffsanforderungen und verwendet dann automatisch die von ihm selbst nachverfolgte Zeit des letzten Zugriffs.

> [!NOTE]
> Das Cloudtiering ist nicht von der NTFS-Funktion für die Überwachung des Zeitpunkts des letzten Zugriffs abhängig. Diese NTFS-Funktion ist standardmäßig deaktiviert. Um die Leistung nicht zu beeinträchtigen, wird empfohlen, diese Funktion nicht manuell zu aktivieren. Das Cloudtiering verfolgt den Zeitpunkt des letzten Zugriffs separat.

### <a name="proactive-recalling"></a>Proaktiver Abruf

Wenn eine Datei erstellt oder geändert wird, können Sie sie proaktiv auf von Ihnen angegebene Server abrufen. Durch einen proaktiven Aufruf wird die neue oder geänderte Datei auf jedem angegebenen Server zur Nutzung bereitgestellt.

Angenommen, ein globales Unternehmen verfügt über Zweigstellen in den USA und in Indien. Am Morgen (US-Zeit) erstellen Information Worker einen neuen Ordner und neue Dateien für ein ganz neues Projekt, an dem sie den gesamten Tag arbeiten. Die Azure-Dateisynchronisierung synchronisiert Ordner und Dateien auf die Azure-Dateifreigabe (den Cloudendpunkt). Information Worker in Indien setzen die Arbeit an dem Projekt in ihrer Zeitzone fort. Wenn diese am Morgen eintreffen, müssen auf dem lokalen, für Azure-Dateisynchronisierung aktivierten Server in Indien diese neuen Dateien lokal verfügbar sein, damit das Team in Indien effizient aus einem lokalen Cache heraus weiterarbeiten kann. Wenn Sie diesen Modus aktivieren, wird verhindert, dass der anfängliche Dateizugriff aufgrund eines bedarfsgesteuerten Abrufs langsamer ist, und dem Server wird ermöglicht, die Dateien proaktiv abzurufen, sobald sie in der Azure-Dateifreigabe geändert oder erstellt wurden.

Wenn Dateien, die auf den Server abgerufen werden, lokal nicht benötigt werden, kann der unnötige Abruf den ausgehenden Datenverkehr und die Kosten erhöhen. Aktivieren Sie den proaktiven Abruf daher nur, wenn Sie wissen, dass das Vorabauffüllen eines Servercaches mit aktuellen Änderungen aus der Cloud von Vorteil ist für Benutzer oder Anwendungen, die die Dateien auf diesem Server verwenden.

Das Aktivieren des proaktiven Abrufs kann auch zu einer erhöhten Bandbreitennutzung auf dem Server führen und dazu, dass andere relativ neue Inhalte auf dem lokalen Server aufgrund der Zunahme der abgerufenen Dateien aggressiv ausgelagert werden. Ein zu frühes Tiering kann wiederum zu mehr Abrufen führen, wenn die per Tiering ausgelagerten Dateien von Servern als „heiß“ eingestuft werden.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Eine Abbildung, die das Downloadverhalten der Azure-Dateifreigabe für einen derzeit gültigen Serverendpunkt darstellt sowie eine Schaltfläche zum Öffnen eines Menüs, das dessen Änderung gestattet.":::

Weitere Informationen zu proaktiven Abrufen finden Sie unter [Proaktives Abrufen neuer und geänderter Dateien von einer Azure-Dateifreigabe](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Verhalten von ausgelagerten und lokal zwischengespeicherten Dateien

Beim Cloudtiering werden der Namespace (Datei- und Ordnerhierarchie sowie Dateieigenschaften) und der Dateiinhalt voneinander getrennt.

#### <a name="tiered-file"></a>Ausgelagerte Datei

Bei ausgelagerten Dateien ist die Größe auf dem Datenträger Null, da der Dateiinhalt selbst nicht lokal gespeichert wird. Wenn eine Datei ausgelagert wird, ersetzt der Dateisystemfilter der Azure-Dateisynchronisierung (StorageSync.sys) die Datei lokal durch einen Zeiger (Analysepunkt). Der Analysepunkt stellt eine URL zur Datei in der Azure-Dateifreigabe dar. Eine per Tiering ausgelagerte Datei weist sowohl das offline-Attribut als auch das in NTFS festgelegte FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS-Attribut auf, sodass Drittanwendungen Tieringdateien sicher identifizieren können.

![Screenshot der Eigenschaften einer ausgelagerten Datei – nur Namespace](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)

#### <a name="locally-cached-file"></a>Lokal zwischengespeicherte Datei

Wenn eine Datei auf einem lokalen Dateiserver gespeichert wird, entspricht die Größe dieser Datei auf dem Datenträger dagegen in etwa der logischen Größe der Datei, da die gesamte Datei (Dateiattribute und Dateiinhalt) lokal gespeichert wird.

![Screenshot der Eigenschaften einer nicht ausgelagerten Datei – Namespace und Dateiinhalt](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png)

Für eine Datei kann das Tiering auch teilweise durchgeführt werden (bzw. teilweise zurückgerufen werden). Bei einer partiell per Tiering ausgelagerten Datei wird nur ein Teil auf dem Datenträger gespeichert. Sie verfügen möglicherweise über partiell abgerufene Dateien in Ihrem Volume, wenn Dateien partiell von Anwendungen gelesen werden, die den Streamingzugriff auf Dateien unterstützen. Einige Beispiele hierfür sind Multimediaplayer und ZIP-Hilfsprogramme. Die Azure-Dateisynchronisierung ist effizient und ruft nur die angeforderten Informationen aus der verbundenen Azure-Dateifreigabe ab.

> [!NOTE]
> „Größe“ steht für die logische Größe der Datei. „Größe auf Datenträger“ steht für die physische Größe des Dateidatenstroms, der auf dem Datenträger gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

- [Auswählen von Cloudtieringrichtlinien der Azure-Dateisynchronisierung](file-sync-choose-cloud-tiering-policies.md)
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](file-sync-planning.md)

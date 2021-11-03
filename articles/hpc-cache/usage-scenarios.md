---
title: Azure HPC Cache-Szenarien
description: Beschreibt, wie Sie herausfinden, ob sich Ihr Computingauftrag für Azure HPC Cache eignet.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: femila
ms.openlocfilehash: 40434634fde18a9ff9e63f9e2174cd412182b500
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078558"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Eignet sich Ihr Auftrag für Azure HPC Cache?

Der Azure HPC Cache-Dienst kann den Zugriff auf Daten für High Performance Computing (HPC)-Aufträge in einer Vielzahl von Fachrichtungen beschleunigen. Er eignet sich jedoch nicht für alle Arten von Workflows. Dieser Artikel enthält Richtlinien, mit denen Sie herausfinden können, ob HPC Cache eine gute Wahl für Ihre Anforderungen ist.

Unter [Was ist Azure HPC Cache?](hpc-cache-overview.md) finden Sie einen kurzen Überblick über die Verwendungsmöglichkeiten von Azure HPC Cache und einige Beispielanwendungsfälle.

Informationen dazu, wie Sie [in NFS eingebundenen Blobspeicher](../storage/blobs/network-file-system-protocol-support.md) effektiv nutzen, finden Sie in [diesem Artikel](nfs-blob-considerations.md).

## <a name="nfs-version-30-applications"></a>Anwendungen mit NFS-Version 3.0

Azure HPC Cache unterstützt nur NFS 3.0-Clients.

## <a name="high-read-to-write-ratio"></a>Hohes Verhältnis zwischen Lese- und Schreibzugriff

Workloads, bei denen die Computeclients mehr Lese- als Schreibvorgänge durchführen, eignen sich in der Regel gut für einen Cache. Wenn Ihr Verhältnis zwischen Lese- und Schreibzugriff bei 80/20 oder 70/30 liegt, kann Azure HPC Cache beispielsweise hilfreich sein, da häufig angeforderte Dateien über den Cache verarbeitet werden, anstatt sie immer wieder aus Remotespeicher abzurufen.

Beim erstmaligen Abrufen und Speichern einer Datei im Cache ist die Wartezeit geringfügig höher als bei einer normalen Clientanforderung, die direkt an den Speicher gesendet wird. Die Effizienzsteigerung macht sich bemerkbar, wenn die gleiche Datei das nächste Mal von einem Client angefordert wird. Dies gilt insbesondere für große Dateien. Wenn jede Clientanforderung eindeutig ist, ist die mit HPC Cache erzielte Wirkung begrenzt. Aber je größer die Datei ist, desto besser wird die Leistung im Laufe der Zeit nach diesem ersten Zugriff.

## <a name="file-based-analytic-workload"></a>Dateibasierte Analyseworkload

Azure HPC Cache eignet sich ideal für eine Pipeline, die dateibasierte Daten verwendet und für eine große Anzahl von Computeclients ausgeführt wird. Dies gilt insbesondere, wenn es sich bei den Computeclients um Azure-VMs handelt. Der Dienst kann dazu beitragen, Probleme mit langsamer oder inkonsistenter Leistung zu beheben, die durch lange Dateizugriffszeiten verursacht werden.

## <a name="remote-data-access"></a>Remotedatenzugriff

Azure HPC Cache kann die Wartezeit verringern, wenn Ihre Workload auf Remotedaten zugreifen muss, die nicht näher an den Computingressourcen platziert werden können. Ihre Datensätze können sich beispielsweise am Ende einer WAN-Umgebung, in einer anderen Azure-Region oder im Rechenzentrum eines Kunden befinden. (Dies wird auch als „Dateibursting“ bezeichnet.)

## <a name="heavy-request-load"></a>Hohe Anforderungslast

Wenn eine große Anzahl von Clients gleichzeitig Daten aus der Quelle anfordert, kann Azure HPC Cache den Dateizugriff beschleunigen. Bei der Verwendung mit einem High Performance Computing-Cluster bietet Azure HPC Cache beispielsweise über den Cache Skalierbarkeit für eine hohe Anzahl gleichzeitiger Anforderungen.

## <a name="compute-resources-are-located-in-azure"></a>Computeressourcen in Azure

Azure-VMs sind eine skalierbare und kostengünstige Lösung für High Performance Computing-Workloads. Mit Azure HPC Cache können die benötigten Informationen näher an den VMs platziert werden – insbesondere dann, wenn die ursprünglichen Daten auf einem Remotesystem gespeichert sind.

Wenn ein Kunde seine aktuelle Pipeline unverändert auf Azure-VMs ausführen möchte, kann er mit Azure HPC Cache eine skalierbare POSIX-basierte freigegebene Speicherlösung (oder Zwischenspeicherung) bereitstellen.

Wenn Sie Azure HPC Cache nutzen, müssen Sie die Arbeitspipeline nicht neu strukturieren, um native Aufrufe für Azure Blob Storage durchzuführen. Sie können im ursprünglichen System auf Ihre Daten zugreifen oder HPC Cache verwenden, um die Daten in einen neuen Blobcontainer zu verschieben.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Planen und Konfigurieren eines Caches finden Sie in den Artikeln [Übersicht](hpc-cache-overview.md) und [Voraussetzungen](hpc-cache-prerequisites.md).
* Lesen Sie die Hinweise zur Verwendung von [NFS-fähigem Blobspeicher](nfs-blob-considerations.md) mit Azure HPC Cache.

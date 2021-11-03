---
title: Erläuterungen zum aggregierten Azure HPC Cache-Namespace
description: Planen des virtuellen Namespace für Azure HPC Cache
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: femila
ms.openlocfilehash: de93648ab6304f42b2650eb6bba80e7224d2d57f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087759"
---
# <a name="plan-the-aggregated-namespace"></a>Planen des aggregierten Namespace

Azure HPC Cache ermöglicht Clients den Zugriff auf eine Vielzahl von Speichersystemen über einen virtuellen Namespace, der die Details des Back-End-Speichersystems verbirgt.

Nach dem Hinzufügen eines Speicherziels richten Sie mindestens einen clientseitigen Namespacepfad für das Speicherziel ein. Clientcomputer binden diesen Dateipfad ein und können Dateileseanforderungen an den Cache stellen, anstatt das Speichersystem direkt einzubinden.

Da Azure HPC Cache dieses virtuelle Dateisystem verwaltet, können Sie das Speicherziel ändern, ohne den clientseitigen Pfad zu ändern. Beispielsweise können Sie ein Hardwarespeichersystem durch Cloudspeicher ersetzen, ohne die clientseitigen Prozeduren erneut erstellen zu müssen.

## <a name="aggregated-namespace-example"></a>Beispiel zum aggregierten Namespace

Planen Sie Ihren aggregierten Namespace so, dass die Clientcomputer komfortabel an die benötigten Informationen gelangen und Administratoren und Workflowtechniker die Pfade leicht unterscheiden können.

Ziehen Sie beispielsweise ein System in Erwägung, in dem eine Instanz von Azure HPC Cache zum Verarbeiten der im Azure-Blob gespeicherten Daten verwendet wird. Für die Analyse sind Vorlagendateien erforderlich, die in einem lokalen Rechenzentrum gespeichert sind.

Die Vorlagendaten sind in einem Rechenzentrum gespeichert, und die für diesen Auftrag erforderlichen Informationen sind in diesen Unterverzeichnissen gespeichert:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

Das Speichersystem des Rechenzentrums macht diese Exporte verfügbar:

* */*
* */goldline*
* */goldline/templates*

Die zu analysierenden Daten wurden mithilfe des [CLFSLoad-Hilfsprogramms](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload) in einen Azure-Blobspeichercontainer mit dem Namen „sourcecollection“ kopiert.

Um den komfortablen Zugriff über den Cache zu ermöglichen, sollten Sie das Erstellen von Speicherzielen mit diesen virtuellen Namespacepfaden in Erwägung ziehen:

| Back-End-Speichersystem <br/> (NFS-Dateipfad oder Blobcontainer) | Pfad des virtuellen Namespace |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

Ein NFS-Speicherziel kann mehrere virtuelle Namespacepfade aufweisen, sofern jeder von ihnen auf einen eindeutigen Exportpfad verweist. (Unter [NFS-Namespacepfade](add-namespace-paths.md#nfs-namespace-paths) erfahren Sie mehr über die Verwendung mehrerer Namespacepfade mit einem NFS-Speicherziel.)

Da es sich bei den NFS-Quellpfaden um Unterverzeichnisse des gleichen Exports handelt, müssen Sie für das gleiche Speicherziel mehrere Namespacepfade definieren.

| Hostname des Speicherziels  | NFS-Exportpfad     | Unterverzeichnispfad | Namespacepfad    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP-Adresse oder Hostname* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP-Adresse oder Hostname* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Eine Clientanwendung kann den Cache einbinden und komfortabel auf die Dateipfade ``/source``, ``/templates/sku798`` und ``/templates/sku980`` des aggregierten Namespace zugreifen.

Alternativ könnten Sie einen virtuellen Pfad wie `/templates` erstellen, der mit dem übergeordneten Verzeichnis `acme2017` verknüpft ist, und die Clients nach dem Einbinden des Caches zu den einzelnen `sku798`- und `sku980`-Verzeichnissen navigieren lassen. Es ist jedoch nicht möglich, einen Namespacepfad zu erstellen, bei dem es sich um ein Unterverzeichnis eines anderen Namespacepfads handelt. Wenn Sie einen Pfad zum Verzeichnis `acme2017` erstellen, können Sie also nicht zusätzlich Namespacepfade für den direkten Zugriff auf dessen Unterverzeichnisse erstellen.

Auf der Einstellungsseite **Namespace** für Azure HPC Cache wird das clientseitige Dateisystem angezeigt, und Sie können Pfade hinzufügen oder bearbeiten. Weitere Informationen finden Sie unter [Einrichten des aggregierten Namespace](add-namespace-paths.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie entschieden haben, wie Sie das virtuelle Dateisystem einrichten möchten, führen Sie die folgenden Schritte aus, um es zu erstellen:

* [Erstellen Sie Speicherziele](hpc-cache-add-storage.md) zum Hinzufügen Ihrer Back-End-Speichersysteme zu Ihrer Azure HPC Cache-Instanz.
* [Fügen Sie Namespacepfade](add-namespace-paths.md) hinzu, um den aggregierten Namespace zu erstellen, den Clientcomputer für den Zugriff auf Dateien verwenden.

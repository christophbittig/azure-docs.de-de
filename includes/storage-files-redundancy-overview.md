---
title: Datei einfügen
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 400e8de6b8d8481cccd6d2b3bcb25620f956f823
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423368"
---
Um die Daten in Ihren Azure-Dateifreigaben vor Datenverlusten oder Beschädigungen zu schützen, werden für alle Azure-Dateifreigaben beim Schreiben mehrere Kopien der einzelnen Dateien gespeichert. Abhängig von den Anforderungen Ihrer Workload können Sie zusätzliche Redundanzgrade auswählen. Azure Files unterstützt derzeit die folgenden Datenredundanzoptionen:

- **Lokal redundanter Speicher (LRS)** : Bei LRS wird jede Datei in einem Azure-Speichercluster dreimal gespeichert. Dies schützt vor Datenverlusten aufgrund von Hardwarefehlern, z. B. bei einem schadhaften Laufwerk. Bei einem Katastrophenfall in einem Rechenzentrum (Feuer, Überschwemmung usw.) gehen jedoch eventuell alle Replikate in einem Speicherkonto, das LRS verwendet, verloren oder können nicht mehr wiederhergestellt werden.
- **Zonenredundanter Speicher (ZRS)** : Bei ZRS werden drei Kopien jeder Datei gespeichert. Diese Kopien werden jedoch physisch in drei verschiedenen Speicherclustern in verschiedenen Azure-*Verfügbarkeitszonen* isoliert. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Ein Schreibvorgang in den Speicher wird erst akzeptiert, wenn die Daten in allen drei Verfügbarkeitszonen in die Speichercluster geschrieben wurden. 
- **Georedundanter Speicher (GRS)** : Bei GRS verfügen Sie über zwei Regionen, eine primäre und eine sekundäre Region. Dateien werden dreimal in einem Azure-Speichercluster in der primären Region gespeichert. Schreibvorgänge werden asynchron in eine von Microsoft definierte sekundäre Region repliziert. GRS bietet sechs Kopien Ihrer Daten, die auf zwei Azure-Regionen verteilt sind. Bei einem größeren Notfall, z. B. dem permanenten Verlust einer Azure-Region aufgrund einer Naturkatastrophe oder eines ähnlichen Ereignisses, führt Microsoft ein Failover durch, sodass das sekundäre Replikat zum primären Replikat wird, das für alle Vorgänge verwendet wird. Da die Replikation zwischen der primären und der sekundären Region asynchron ist, gehen die Daten, die noch nicht in die sekundäre Region repliziert wurden, bei einem größeren Notfall verloren. Sie können auch ein manuelles Failover eines georedundanten Speicherkontos ausführen.
- **Geozonenredundanter Speicher (GZRS)** : Sie können sich GZRS wie ZRS vorstellen, jedoch mit Georedundanz. Bei GZRS werden Dateien dreimal in drei verschiedenen Speicherclustern in der primären Region gespeichert. Alle Schreibvorgänge werden dann asynchron in eine von Microsoft definierte sekundäre Region repliziert. Der Failoverprozess für GZRS funktioniert genauso wie bei GRS.

Standardmäßige Azure-Dateifreigaben bis zu 5 TiB unterstützen alle vier Redundanztypen. Standarddateifreigaben über 5 TiB unterstützen nur LRS and ZRS. Azure-Premium-Dateifreigaben unterstützen nur LRS und ZRS.

Speicherkonten des Typs Universell v2 bieten zwei zusätzliche Redundanzoptionen, die nicht von Azure Files unterstützt werden: georedundanter Speicher mit Lesezugriff, häufig als RA-GRS bezeichnet, und geozonenredundanter Speicher mit Lesezugriff, häufig als RA-GZRS bezeichnet. Sie können Azure-Dateifreigaben in Speicherkonten mit diesen Optionen bereitstellen, Azure Files unterstützt jedoch nicht das Lesen aus der sekundären Region. Azure-Dateifreigaben, die in georedundanten oder geozonenredundanten Speicherkonten mit Lesezugriff bereitgestellt werden, werden als georedundanter bzw. geozonenredundanter Speicher abgerechnet.
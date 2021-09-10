---
title: Best Practices für direkte E/A von Linux für Azure NetApp Files | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die direkte E/A von Linux und die Best Practices für Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6497d91169c7530251a473e8e06c26ce411e0e6a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233491"
---
# <a name="linux-direct-io-best-practices-for-azure-netapp-files"></a>Best Practices für direkte E/A von Linux für Azure NetApp Files

Dieser Artikel hilft Ihnen dabei, die Best Practices für die direkte E/A für Azure NetApp Files besser zu verstehen.  

## <a name="direct-io"></a>Direkte E/A

 Der gängigste Parameter beim Benchmarking der Speicherleistung ist die direkte E/A. Sie wird von FIO und Vdbench unterstützt. DISKSPD bietet Unterstützung für das ähnliche Konstrukt der E/A mit Arbeitsspeicherzuordnung. Bei der direkten E/A wird der Cache des Dateisystems umgangen, Vorgänge für einen direkten Speicherzugriff werden vermieden, und Speichertests sind schnell und einfach.  

Mit dem Parameter für direkte E/A ist das Testen des Speichers ganz einfach. Es werden keine Daten aus dem Dateisystemcache auf dem Client gelesen. Daher ist der Test ein echter Belastungstest für das Speicherprotokoll und den Dienst selbst, nicht für die Geschwindigkeit des Arbeitsspeicherzugriffs. Ohne die DMA-Arbeitsspeicherkopien sind außerdem Lese- und Schreibvorgänge aus Verarbeitungsperspektive effizient. 

Nehmen Sie als Beispielworkload den Linux-Befehl `dd`. Ohne das optionale `odirect`-Flag werden sämtliche von `dd` generierten E/A-Vorgänge aus dem Linux-Puffercache bedient. Lesevorgänge, deren Blöcke sich bereits im Arbeitsspeicher befinden, werden nicht aus dem Massenspeicher abgerufen. Lesevorgänge, die zu einem Puffercachefehler führen, werden per NFS-Read-Ahead-Vorgang aus dem Massenspeicher gelesen. Dabei kommt es, abhängig von Faktoren wie dem `rsize`-Bereitstellungswert und Client-Read-Ahead-Einstellungen, zu unterschiedlichen Ergebnissen. Wenn Schreibvorgänge über den Puffercache gesendet werden, verwenden sie einen Write-Behind-Mechanismus, der nicht optimiert ist und ein erhebliches Maß an Parallelität benötigt, um die Daten an das Speichergerät zu senden. Möglicherweise kommen Sie in Versuchung, zwei unabhängige E/A-Datenströme auszuführen, einen `dd`-Datenstrom für Lesevorgänge und einen `dd`-Datenstrom für Schreibvorgänge. Tatsächlich zieht das Betriebssystem ohne Optimierung die Schreibvorgänge den Lesevorgängen vor und verwendet mehr Parallelität.

Abgesehen von Datenbanken verwenden sehr wenige Anwendungen direkte E/A-Vorgänge. Stattdessen nutzen sie die Vorteile eines großen Arbeitsspeichercaches für wiederholte Lesevorgänge und einen Write-Behind-Cache für asynchrone Schreibvorgänge. Kurz gesagt: Die Verwendung der direkten E/A verwandelt den Test in einen Mikrobenchmark, *wenn* die Anwendung, die synthetisiert werden soll, den Dateisystemcache verwendet.  

Im Folgenden finden Sie einige Datenbanken, die direkte E/A unterstützen: 

* Oracle 
* SAP HANA
* MySQL (InnoDB-Speicher-Engine)
* RocksDB
* PostgreSQL
* Teradata

## <a name="best-practices"></a>Bewährte Methoden 

Das Testen mit `directio` ist eine hervorragende Möglichkeit, die Grenzwerte von Speicherdienst und Client zu verstehen. Um auch besser zu verstehen, wie die Anwendung selbst sich verhalten wird (falls sie nicht `directio` verwendet), sollten Sie auch Tests über den Dateisystemcache ausführen.

## <a name="next-steps"></a>Nächste Schritte  

* [Bewährte Methoden in Bezug auf den Linux-Dateisystemcache für Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files](performance-linux-mount-options.md)
* [Bewährte Methoden für Linux-Parallelität für Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Bewährte Methoden für NFS-Read-Ahead unter Linux](performance-linux-nfs-read-ahead.md)
* [Bewährte Methoden für SKUs für virtuelle Azure-Computer](performance-virtual-machine-sku.md) 
* [Leistungsbenchmarks für Linux](performance-benchmarks-linux.md) 

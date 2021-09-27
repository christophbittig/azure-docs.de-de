---
title: 'Compute- und Speicheroptionen – Azure Database for PostgreSQL: Flexible Server'
description: 'In diesem Artikel werden die Compute- und Speicheroptionen in Azure Database for PostgreSQL: Flexible Server beschrieben.'
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 3e17806a38cbf060ba40d24a7ebb861e7bf86d50
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555924"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Compute- und Speicheroptionen in Azure Database for PostgreSQL: Flexible Server



Sie können eine Azure Database for PostgreSQL-Serverinstanz basierend auf drei unterschiedlichen Tarifen erstellen: „Burstfähig“, „Allgemeiner Zweck“ und „Arbeitsspeicheroptimiert“. Die Tarife unterscheiden sich anhand der bereitstellbaren Menge an Rechenleistung in V-Kernen, des Arbeitsspeichers pro V-Kern und der zum Speichern der Daten verwendeten Speichertechnologie. Alle Ressourcen werden auf der PostgreSQL-Serverebene bereitgestellt. Ein Server kann über eine oder mehrere Datenbanken verfügen.

| Ressource/Ebene | **Burstfähig** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
|:---|:----------|:--------------------|:---------------------|
| V-Kerne | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Arbeitsspeicher pro V-Kern | Variable | 4 GB | 6,75 bis 8 GB |
| Speichergröße | 32 GB bis 16 TB | 32 GB bis 16 TB | 32 GB bis 16 TB |
| Aufbewahrungszeitraum von Datenbanksicherungen | 7 bis 35 Tage | 7 bis 35 Tage | 7 bis 35 Tage |

Um einen Tarif auszuwählen, verwenden Sie die folgende Tabelle als Ausgangspunkt.

| Tarif | Zielworkloads |
|:-------------|:-----------------|
| Burstfähig | Am besten geeignet für Workloads, die nicht kontinuierlich die vollständige CPU benötigen. |
| Universell | Geeignet für die meisten Unternehmensworkloads mit gängigen Compute- und Arbeitsspeicheranforderungen und skalierbarem E/A-Durchsatz. Hierzu zählen beispielsweise zum Hosten von Web- und mobilen Apps verwendete Server und andere Unternehmensanwendungen.|
| Arbeitsspeicheroptimiert | Geeignet für Hochleistungs-Datenbankworkloads, für die In-Memory-Leistung erforderlich ist, um eine schnellere Transaktionsverarbeitung und höhere Parallelität zu erzielen. Hierzu zählen beispielsweise Server für die Verarbeitung von Echtzeitdaten und leistungsstarke Transaktions- oder Analyse-Apps.|

Nachdem Sie einen Server erstellt haben, können die Computeebene, die Anzahl von virtuellen Kernen und die Speichergröße innerhalb weniger Sekunden erhöht oder verringert werden. Sie können den Aufbewahrungszeitraum für Sicherungen auch unabhängig nach oben oder unten anpassen. Weitere Informationen finden Sie im Abschnitt [Skalieren von Ressourcen](#scale-resources).

## <a name="compute-tiers-vcores-and-server-types"></a>Computeebenen, virtuelle Kerne und Servertypen

Computeressourcen können basierend auf Tarif, virtuellen Kernen und Arbeitsspeichergröße ausgewählt werden. Virtuelle Kerne stellen die logische CPU der zugrunde liegenden Hardware dar.

Die detaillierten Spezifikationen der verfügbaren Servertypen lauten wie folgt:

| SKU-Name             | V-Kerne | Arbeitsspeichergröße | Maximal unterstützte IOPS | Maximal unterstützte E/A-Bandbreite |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Burstfähig**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/s                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB/s                  |
| **Allgemeiner Zweck**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/s                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/s                  |
| D8s_v3               | 8      | 32GiB      | 12800              | 192 MiB/sec                 |
| D16s_v3              | 16     | 64GiB      | 18000              | 384 MiB/sec                 |
| D32s_v3              | 32     | 128 GB     | 18000              | 750 MiB/s                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/s                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/s                 |
| **Arbeitsspeicheroptimiert** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/s                  |
| E4s_v3               | 4      | 32GiB      | 6400               | 96 MiB/s                  |
| E8s_v3               | 8      | 64GiB      | 12800              | 192 MiB/sec                 |
| E16s_v3              | 16     | 128 GB     | 18000              | 384 MiB/sec                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/s                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/s                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/s                 |

## <a name="storage"></a>Storage

Der von Ihnen bereitgestellte Speicher definiert die Speicherkapazität, die für Ihren Azure Database for PostgreSQL-Server zur Verfügung steht. Der Speicher wird für die Datenbankdateien, temporären Dateien, Transaktionsprotokolle und PostgreSQL-Serverprotokolle verwendet. Außerdem wird durch die Gesamtmenge an bereitgestelltem Speicher die E/A-Kapazität Ihres Servers definiert.

Speicher ist in den folgenden festen Größen verfügbar:

| Datenträgergröße | IOPS |
|:---|:---|
| 32GiB | Bereitgestellt 120, max. 3.500 |
| 64GiB | Bereitgestellt 240, max. 3.500 |
| 128 GB | Bereitgestellt 500, max. 3.500 |
| 256 GiB | Bereitgestellt 1100, max. 3.500 |
| 512 GB | Bereitgestellt 2300, max. 3.500 |
| 1 TiB | 5\.000 |
| 2TiB | 7.500 |
| 4 TiB | 7\.500 |
| 8 TiB | 16.000 |
| 16 TiB | 18.000 |

Beachten Sie, dass IOPS auch durch Ihren VM-Typ eingeschränkt sind. Sie können zwar unabhängig vom Servertyp eine beliebige Speichergröße auswählen, dann aber möglicherweise nicht alle IOPS nutzen, die der Speicher zur Verfügung stellt, insbesondere wenn Sie einen Server mit einer kleinen Anzahl von virtuellen Kernen auswählen.

Sie können während und nach der Erstellung des Servers zusätzliche Speicherkapazität hinzufügen.

>[!NOTE]
> Der Speicher kann nur zentral hochskaliert und nicht herunterskaliert werden.

Sie können Ihren E/A-Verbrauch im Azure-Portal oder mit Azure CLI-Befehlen überwachen. Die wichtigen zu überwachenden Metriken sind das [Speicherlimit, der Speicherprozentsatz, der genutzte Speicher und der E/A-Prozentsatz](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>Maximaler IOPS-Wert für Ihre Konfiguration

|SKU-Name            |Speichergröße in GiB                       |32 |64 |128 |256 |512  |1\.024|2\.048|4\.096|8\.192 |16.384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Maximale IOPS-Anzahl                              |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|**Burstfähig**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640*|640* |640* |640* |640* |640*  |640*  |
|B2s                 |1280 IOPS                                 |120|240|500 |1100|1280*|1280*|1280*|1280*|1280* |1280* |
|**Allgemeiner Zweck** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200*|3200*|3200*|3200* |3200* |
|D4s_v3              |6\.400 IOPS                                |120|240|500 |1100|2300 |5.000 |6400*|6400*|6400* |6400* |
|D8s_v3              |12.800 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |12800*|12800*|
|D16s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|D32s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|D48s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|D64s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|**Arbeitsspeicheroptimiert**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200*|3200*|3200*|3200* |3200* |
|E4s_v3              |6\.400 IOPS                                |120|240|500 |1100|2300 |5.000 |6400*|6400*|6400* |6400* |
|E8s_v3              |12.800 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |12800*|12800*|
|E16s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|E32s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|E48s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |
|E64s_v3             |18.000 IOPS                               |120|240|500 |1100|2300 |5.000 |7.500 |7.500 |16000 |18000 |

Wenn sie mit einem \* markiert sind, werden IOPS durch den von Ihnen gewählten VM-Typ begrenzt. Andernfalls werden IOPS durch die ausgewählte Speichergröße beschränkt.

>[!NOTE]
> In den Metriken werden aufgrund des Datenträger-Burstings möglicherweise höhere IOPS-Werte angezeigt. Weitere Informationen finden Sie in der [Dokumentation](../../virtual-machines/disk-bursting.md#disk-level-bursting). 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Maximale E/A-Bandbreite (MiB/s) für Ihre Konfiguration

|SKU-Name            |Speichergröße, GiB                             |32 |64 |128 |256 |512  |1\.024|2\.048|4\.096|8\.192 |16.384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Speicherbandbreite, MiB/s**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Burstfähig**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/s                                    |10*|10*|10* |10* |10*  |10*  |10*  |10*  |10*   |10*   |
|B2s                 |15 MiB/s                                    |15*|15*|15* |15* |15*  |15*  |15*  |15*  |15*   |15*   |
|**Allgemeiner Zweck** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/s                                    |25 |48*|48* |48* |48*  |48*  |48*  |48*  |48*   |48*   |
|D4s_v3              |96 MiB/s                                    |25 |50 |96* |96* |96*  |96*  |96*  |96*  |96*   |96*   |
|D8s_v3              |192 MiB/sec                                   |25 |50 |100 |125 |150  |192* |192* |192* |192*  |192*  |
|D16s_v3             |384 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |384*  |384*  |
|D32s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Arbeitsspeicheroptimiert**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/s                                    |25 |48*|48* |48* |48*  |48*  |48*  |48*  |48*   |48*   |
|E4s_v3              |96 MiB/s                                    |25 |50 |96* |96* |96*  |96*  |96*  |96*  |96*   |96*   |
|E8s_v3              |192 MiB/sec                                   |25 |50 |100 |125 |150  |192* |192* |192* |192*  |192*  |
|E16s_v3             |384 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |384*  |384*  |
|E32s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Wenn sie mit einem \* markiert sind, wird die E/A-Bandbreite durch den von Ihnen gewählten VM-Typ begrenzt. Andernfalls wird die E/A-Bandbreite durch die ausgewählte Speichergröße beschränkt.

### <a name="reaching-the-storage-limit"></a>Erreichen der Speicherbegrenzung

Wird die Speichergrenze erreicht, beginnt der Server, Fehler zurückzugeben und weitere Änderungen zu verhindern. Dies kann auch Probleme bei anderen betrieblichen Aktivitäten wie Sicherungen und der WAL-Archivierung verursachen.

Um diese Situation zu vermeiden, wird der Server automatisch in den **schreibgeschützten Modus** versetzt, wenn die Speicherauslastung 95 % erreicht oder die verfügbare Kapazität unter 5 GiB sinkt.

Es wird empfohlen, den belegten Speicherplatz aktiv zu überwachen und den Speicherplatz auf dem Datenträger zu erhöhen, bevor dieser aufgebraucht ist. Sie können eine Benachrichtigung einrichten, mit der Sie informiert werden, wenn sich der Speicherplatz auf Ihrem Server dem Ende der Festplatte nähert, sodass Sie Probleme durch fehlenden Speicherplatz vermeiden können. Weitere Informationen finden Sie in der Dokumentation zum [Einrichten einer Benachrichtigung](howto-alert-on-metrics.md).


### <a name="storage-auto-grow"></a>Automatische Speichervergrößerung

Die automatische Speichervergrößerung ist für Flexible Server noch nicht verfügbar.

## <a name="backup"></a>Backup

Der Dienst erstellt automatisch Sicherungen Ihres Servers. Sie können als Aufbewahrungszeitraum einen Bereich von 7 bis 35 Tagen auswählen. Im [Artikel zu Konzepten](concepts-backup-restore.md) erfahren Sie mehr über Sicherungen.

## <a name="scale-resources"></a>Skalieren von Ressourcen

Nachdem Sie Ihren Server erstellt haben, können Sie die virtuellen Kerne, die Computeebene, die Speichermenge und den Aufbewahrungszeitraum für Sicherungen einzeln ändern. Die Anzahl virtueller Kerne kann zentral hoch- oder herunterskaliert werden. Die Aufbewahrungsdauer für Sicherungen kann von 7 bis zu 35 Tagen zentral hoch- oder herunterskaliert werden. Die Speichergröße kann nur erhöht werden. Die Skalierung der Ressourcen kann entweder über das Portal oder per Azure CLI durchgeführt werden.

> [!NOTE]
> Die Speichergröße kann nur erhöht werden. Nach der Erhöhung können Sie nicht mehr zu einer kleineren Speichergröße zurückkehren.

Wenn Sie die Anzahl der virtuellen Kerne oder die Computeebene ändern, wird der Server neu gestartet, damit der neue Servertyp wirksam wird. Während des Moments, in dem das System den Wechsel zum neuen Server durchführt, können keine neuen Verbindungen hergestellt werden, und für alle Transaktionen ohne Commit erfolgt ein Rollback. Die Länge dieses Zeitfensters variiert, aber normalerweise dauert der Vorgang nicht länger als eine Minute. Die Skalierung des Speichers funktioniert auf die gleiche Weise und erfordert ebenfalls einen kurzen Neustart.

Die Änderung der Sicherungsaufbewahrungszeit ist ein Onlinevorgang.

## <a name="pricing"></a>Preise

Aktuelle Preisinformationen finden Sie auf der Seite [Azure-Datenbank für MySQL – Preise](https://azure.microsoft.com/pricing/details/PostgreSQL/). Informationen zu den Kosten der gewünschten Konfiguration können Sie im [Azure-Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) anzeigen. Die monatlichen Kosten für die von Ihnen ausgewählten Optionen werden auf der Registerkarte **Tarif** angegeben. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie den Azure-Preisrechner verwenden, um einen geschätzten Preis zu erhalten. Wählen Sie auf der Website des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) die Option **Elemente hinzufügen** aus, erweitern Sie die Kategorie **Datenbanken**, und wählen Sie **Azure Database for PostgreSQL** aus, um die Optionen anzupassen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie [im Portal eine PostgreSQL-Serverinstanz erstellen](how-to-manage-server-portal.md).
- Weitere Informationen zu [Dienstgrenzwerten](concepts-limits.md).
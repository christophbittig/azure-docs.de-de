---
title: Konfigurationsoptionen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Optionen für eine Hyperscale (Citus)-Servergruppe, einschließlich der Compute- und Speicheroptionen für Knoten und Regionen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/03/2021
ms.openlocfilehash: e2dfb0eda9d4e10c97591a6cc534adaa188d87e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602012"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – Konfigurationsoptionen für Hyperscale (Citus)

## <a name="compute-and-storage"></a>Compute und Speicher
 
Sie können die Compute- und Speichereinstellungen für Workerknoten und den Koordinatorknoten in einer Hyperscale (Citus)-Servergruppe unabhängig voneinander auswählen.  Computeressourcen werden in Form von virtuellen Kernen bereitgestellt und repräsentieren die logische CPU der zugrunde liegenden Hardware. Die Speichergröße für die Bereitstellung bezieht sich auf die für die Koordinator- und Workerknoten in Ihrer Hyperscale (Citus)-Servergruppe verfügbare Kapazität. Der Speicher umfasst Datenbankdateien, temporäre Dateien, Transaktionsprotokolle und die Postgres-Serverprotokolle.

### <a name="standard-tier"></a>Standard-Tarif
 
| Resource              | Workerknoten           | Koordinatorknoten      |
|-----------------------|-----------------------|-----------------------|
| Compute, virtuelle Kerne       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Arbeitsspeicher pro V-Kern, GiB | 8                     | 4                     |
| Speichergröße, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Speichertyp          | Allgemein (SSD) | Allgemein (SSD) |
| IOPS                  | Bis zu 3 IOPS/GiB      | Bis zu 3 IOPS/GiB      |

Die RAM-Gesamtmenge in einem einzelnen Hyperscale (Citus)-Knoten basiert auf der ausgewählten Anzahl von virtuellen Kernen.

| V-Kerne | Ein Workerknoten, GiB RAM | Koordinatorknoten, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Außerdem wird durch die Gesamtmenge an bereitgestelltem Speicher die für jeden Worker- und Koordinatorknoten verfügbare E/A-Kapazität definiert.

| Speichergröße, TiB | Maximale IOPS-Anzahl |
|-------------------|--------------|
| 0.5               | 1\.536        |
| 1                 | 3\.072        |
| 2                 | 6\.148        |

Für den gesamten Hyperscale (Citus)-Cluster belaufen sich die aggregierten IOPS auf die folgenden Werte:

| Workerknoten | 0,5 TiB, IOPS gesamt | 1 TiB, IOPS gesamt | 2 TiB, IOPS gesamt |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3\.072               | 6\.144             | 12.296            |
| 3            | 4\.608               | 9\.216             | 18.444            |
| 4            | 6\.144               | 12.288            | 24.592            |
| 5            | 7\.680               | 15.360            | 30.740            |
| 6            | 9\.216               | 18.432            | 36.888            |
| 7            | 10.752              | 21.504            | 43.036            |
| 8            | 12.288              | 24.576            | 49.184            |
| 9            | 13.824              | 27.648            | 55.332            |
| 10           | 15.360              | 30.720            | 61.480            |
| 11           | 16.896              | 33.792            | 67.628            |
| 12           | 18.432              | 36.864            | 73.776            |
| 13           | 19.968              | 39.936            | 79.924            |
| 14           | 21.504              | 43.008            | 86.072            |
| 15           | 23.040              | 46.080            | 92.220            |
| 16           | 24.576              | 49.152            | 98.368            |
| 17           | 26.112              | 52.224            | 104.516           |
| 18           | 27.648              | 55.296            | 110.664           |
| 19           | 29.184              | 58.368            | 116.812           |
| 20           | 30.720              | 61.440            | 122.960           |

### <a name="basic-tier"></a>Basic-Tarif

Beim [Basic-Tarif](concepts-hyperscale-tiers.md) von Hyperscale (Citus) handelt es sich um eine Servergruppe mit nur einem Knoten.  Da nicht zwischen Koordinator- und Workerknoten unterschieden wird, ist die Wahl von Compute- und Speicherressourcen weniger kompliziert.

| Resource              | Verfügbare Optionen     |
|-----------------------|-----------------------|
| Compute, virtuelle Kerne       | 2, 4, 8               |
| Arbeitsspeicher pro V-Kern, GiB | 4                     |
| Speichergröße, GiB     | 128, 256, 512         |
| Speichertyp          | Allgemein (SSD) |
| IOPS                  | Bis zu 3 IOPS/GiB      |

Die RAM-Gesamtmenge in einem einzelnen Hyperscale (Citus)-Knoten basiert auf der ausgewählten Anzahl von virtuellen Kernen.

| V-Kerne | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

Durch die Gesamtmenge an bereitgestelltem Speicher wird auch die verfügbare E/A-Kapazität für den Knoten mit Basic-Tarif definiert.

| Speichergröße, GiB | Maximale IOPS-Anzahl |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1\.536        |

## <a name="regions"></a>Regions
Hyperscale (Citus)-Servergruppen sind in den folgenden Azure-Regionen verfügbar:

* Amerika:
    * Brasilien Süd
    * Kanada, Mitte
    * USA (Mitte)
    * East US
    * USA (Ost) 2
    * USA Nord Mitte
    * USA, Westen 2
* Asien-Pazifik:
    * Australien (Osten)
    * Japan, Osten
    * Japan, Westen
    * Korea, Mitte
    * Asien, Südosten
* Europa:
    * Frankreich, Mitte
    * Deutschland, Westen-Mitte
    * Nordeuropa
    * Schweiz, Norden
    * UK, Süden
    * Europa, Westen

Einige dieser Regionen sind anfangs möglicherweise nicht für alle Azure-Abonnements aktiviert. Öffnen Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), wenn Sie eine Region aus der oben genannten Liste verwenden möchten, sie aber nicht in Ihrem Abonnement angezeigt wird, oder wenn Sie eine nicht in dieser Liste enthaltene Region verwenden möchten.

## <a name="pricing"></a>Preise
Aktuelle Preisinformationen finden Sie auf der Seite [Azure-Datenbank für MySQL – Preise](https://azure.microsoft.com/pricing/details/postgresql/).
Informationen zu den Kosten der gewünschten Konfiguration können Sie im [Azure-Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) auf der Registerkarte **Konfigurieren** anzeigen. Dort werden die monatlichen Kosten basierend auf den von Ihnen ausgewählten Optionen angegeben. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie den Azure-Preisrechner verwenden, um einen geschätzten Preis zu erhalten. Wählen Sie auf der Website des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) die Option **Elemente hinzufügen** aus, erweitern Sie die Kategorie **Datenbanken**, und wählen Sie **Azure Database for PostgreSQL – Hyperscale (Citus)** aus, um die Optionen anzupassen.
 
## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie [eine Hyperscale (Citus)-Servergruppe im Portal erstellen](quickstart-create-hyperscale-portal.md).

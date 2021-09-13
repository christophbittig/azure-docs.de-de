---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: 86b2353109d4005594ee08e27283d6aa9c654120
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078376"
---
In der nachstehenden Tabelle werden die maximalen Grenzwerte für Azure Data Explorer-Cluster beschrieben.

| Resource | Begrenzung |
| --- | --- |
| Cluster pro Region und Abonnement | 20 |
| Instanzen pro Cluster | 1000 | 
| Anzahl der Datenbanken in einem Cluster | 10.000 |
| Anzahl von Follower-Clustern (Datenfreigabe-Consumer) pro Leader-Cluster (Datenfreigabe-Producer) | 100 |

In der nachstehenden Tabelle werden die Grenzwerte für Verwaltungsvorgänge beschrieben, die in Azure Data Explorer-Clustern ausgeführt werden.

| `Scope` | Vorgang | Begrenzung |
| --- | --- | --- |
| Cluster | Lesen (z. B. Abrufen eines Clusters) | 500 pro 5 Minuten |
| Cluster | Schreiben (z. B. Erstellen einer Datenbank) | 1.000 pro Stunde |


---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: f9143f3220ad5f90345436c3c1f97419e4d26fc4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114594087"
---
In der nachstehenden Tabelle werden die maximalen Grenzwerte für Azure Data Explorer-Cluster beschrieben.

| Resource | Begrenzung |
| --- | --- |
| Cluster pro Region und Abonnement | 20 |
| Instanzen pro Cluster | 1000 | 
| Anzahl der Datenbanken in einem Cluster | 10.000 |
| Anzahl von Follower-Clustern (Datenfreigabe-Consumer) pro Leader-Cluster (Datenfreigabe-Producer) | 70 |

In der nachstehenden Tabelle werden die Grenzwerte für Verwaltungsvorgänge beschrieben, die in Azure Data Explorer-Clustern ausgeführt werden.

| `Scope` | Vorgang | Begrenzung |
| --- | --- | --- |
| Cluster | Lesen (z. B. Abrufen eines Clusters) | 500 pro 5 Minuten |
| Cluster | Schreiben (z. B. Erstellen einer Datenbank) | 1.000 pro Stunde |


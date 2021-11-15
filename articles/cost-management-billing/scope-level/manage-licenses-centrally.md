---
title: Anwendung zentral zugewiesener SQL-Lizenzen auf den stündlichen Verbrauch durch Azure
description: Dieser Artikel enthält eine ausführliche Erläuterung über die Anwendung zentral zugewiesener SQL-Lizenzen auf den stündlichen Verbrauch durch Azure mit dem Azure-Hybridvorteil.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 53f8538e6e34c91023bc55e7d47e5f28e9fb3e21
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130243845"
---
# <a name="how-azure-applies-centrally-assigned-sql-licenses-to-hourly-usage"></a>Anwendung zentral zugewiesener SQL-Lizenzen auf den stündlichen Verbrauch durch Azure

Dieser Artikel enthält Details zur zentralen Verwaltung des Azure-Hybridvorteils für SQL Server auf Bereichsebene. Der Prozess beginnt damit, dass ein Administrator dem Abonnement oder einem Abrechnungskontobereich Lizenzen zuweist.

Jede Ressource meldet ihre Nutzung einmal pro Stunde unter Verwendung des entsprechenden vollen Preises oder der Verbrauchseinheiten bei der nutzungsbasierten Bezahlung. Das Usage Application-Modul wertet die verfügbaren normalisierten Core-Lizenzen (Normalized Cores License NCL) intern in Azure aus und wendet sie für diese Stunde an. Für eine bestimmte Stunde des Ressourcenverbrauchs der virtuellen Kerne werden die verbrauchsbasierten Bezahlungen auf die entsprechende Azure-Hybridvorteil Verbrauchsnummer umgestellt. Sie werden mit einem Preis von 0 (0 USD) berechnet, wenn genügend nicht ausgelastete NCLs im ausgewählten Bereich vorhanden sind.

## <a name="license-discount"></a>Lizenzrabatt 

Das folgende Diagramm zeigt die Diskontierung, wenn genügend nicht genutzte NCLs vorhanden sind, um den gesamten Verbrauch virtueller Kerne durch alle SQL-Ressourcen für die Stunde zu reduzieren.

Die in diesem Diagramm gezeigten Preise dienen nur als Beispiel.

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="Ein Diagramm mit vollständig diskontiertem Verbrauch virtueller Kerne" border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::.


Wenn der Verbrauch der virtuellen Kerne durch die SQL-Ressourcen im Bereich die Anzahl der nicht genutzten NCLs überschreitet, wird der überschüssige Verbrauch der virtuellen Kerne mit dem entsprechenden nutzungsbasierten Zähler abgerechnet. Das folgende Diagramm zeigt die Diskontierung, wenn der Verbrauch der virtuellen Kerne die Anzahl der nicht verwendeten NCLs überschreitet.

Die in diesem Diagramm gezeigten Preise dienen nur als Beispiel.

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="Diagramm mit einem zum Teil diskontierten Verbrauch" border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::.

Die Azure SQL-Ressourcen, die von den zugewiesenen Core-Lizenzen abgedeckt werden, können von Stunde zu Stunde variieren. Die Varianz hängt davon ab, welche Ressourcen ausgeführt werden und in welcher Reihenfolge das automatisierte System ihre Nutzung verarbeitet. Das System stellt jedoch die maximale Nutzung der zugewiesenen SQL-Lizenzen im ausgewählten Bereich sicher. Sie können die Nutzung mit Cost Management überwachen. Weitere Informationen finden Sie unter [Anleitung zum Nachverfolgen der zugewiesenen Lizenznutzung](create-sql-license-assignments.md#track-assigned-license-use).

Das folgende Diagramm zeigt, wie die zugewiesenen Microsoft SQL-Server-Lizenzen im Laufe der Zeit angewendet werden, um den maximalen Azure-Hybridvorteil-Rabatt zu erhalten.

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="Ein Diagramm, das die Verwendung der NCL im Zeitverlauf zeigt" border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [häufig gestellten Fragen zum zentral verwalteten Azure-Hybridvorteil](faq-azure-hybrid-benefit-scope.yml).
- Erfahren Sie mehr über den [Übergang von der vorhandenen Azure-Hybridvorteilserfahrung](transition-existing.md).
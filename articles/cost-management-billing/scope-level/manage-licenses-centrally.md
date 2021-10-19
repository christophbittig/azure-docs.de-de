---
title: Die Anwendung zugewiesener SQL-Lizenzen auf die stündliche Nutzung durch Azure
description: Dieser Artikel enthält eine ausführliche Erläuterung über die Anwendung zugewiesener SQL-Lizenzen auf die stündliche Nutzung durch Azure mit dem Azure-Hybridvorteil.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 69517a84fa28a6c1d9cde0cb51df82a296bd653a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710160"
---
# <a name="how-azure-applies-assigned-sql-licenses-to-hourly-usage"></a>Die Anwendung zugewiesener SQL-Lizenzen auf die stündliche Nutzung durch Azure

Dieser Artikel enthält eine ausführliche Erläuterung dazu, wie der Azure-Hybridvorteil auf der Bereichsebene funktioniert, um Sie bei der Verwaltung Ihrer SQL Server-Lizenzen zu unterstützen. Der Prozess beginnt damit, dass ein Administrator dem Abonnement oder einem Abrechnungskontobereich Lizenzen zuweist.

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

- Lesen Sie die [häufig gestellten Fragen zur Verwaltung des Azure-Hybridvorteils auf Bereichsebene](faq-azure-hybrid-benefit-scope.yml).
- Erfahren Sie mehr über den [Übergang von der vorhandenen Azure-Hybridvorteilserfahrung](transition-existing.md).
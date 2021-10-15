---
title: Beheben von Problemen mit der Nutzung von Azure-Reservierungen
description: Dieser Artikel enthält grundlegende Informationen zu Azure-Reservierungen, für die im Azure-Portal keine Nutzung oder eine Nutzung mit dem Wert „0“ angezeigt wird, und Sie erfahren, wie Sie Probleme in diesem Zusammenhang behandeln. Darüber hinaus finden Sie hier auch eine Erläuterung zu Diskrepanzen bei der Nutzung.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 4c758dfdc40e23197128bc08cccc5748f08f7ed4
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534877"
---
# <a name="troubleshoot-reservation-utilization"></a>Beheben von Problemen mit der Reservierungsnutzung

Dieser Artikel enthält grundlegende Informationen zu Azure-Reservierungen, für die im Azure-Portal keine Nutzung oder eine Nutzung mit dem Wert „0“ angezeigt wird, und Sie erfahren, wie Sie Probleme in diesem Zusammenhang behandeln. Darüber hinaus finden Sie hier auch eine Erläuterung zu Diskrepanzen bei der Nutzung.

## <a name="symptoms"></a>Symptome

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Reservierungen**.
1. Sehen Sie sich in der Liste mit den Reservierungen in der Spalte **Nutzung (%)** den Nutzungswert für eine Reservierung an. Dieser Wert kann „0 %“ lauten.
1. Wählen Sie die Reservierung aus.
1. Auf der Übersichtsseite der Reservierung entspricht die im Graphen angegebene Nutzung in Prozent möglicherweise nicht dem Wert, der in der Reservierungsliste angezeigt wird.

## <a name="cause"></a>Ursache

Die Spalte **Nutzung (%)** im Azure-Portal enthält den Wert für den aktuellen Tag. Der Wert wird berechnet, wenn Nutzungsdaten von den Stellen eingehen, an denen Ressourcen ausgeführt werden. Azure verwendet die Nutzung zur Berechnung der Nutzung in Prozent.

Nutzungsdaten werden nicht von allen Ressourcen mit der gleichen Geschwindigkeit gemeldet. Hinzu kommt, dass es bei einigen Produkttypen (etwa bei SQL-Datenbanken) länger dauert, bis Nutzungsdaten gemeldet werden.

Die Wartezeit kann dazu führen, dass die Werte der Nutzungsberechnung niedriger sind als die tatsächliche Nutzung. Der Unterschied ist an der Tagesgrenze erkennbar. In solchen Fällen gilt: Wenn Azure zwischen vier und acht Stunden keine Nutzungsdaten erhält, wird der Wert „0 %“ berechnet. Der Wert „0 %“ wird angezeigt, weil keine Nutzungsdaten eingegangen sind. Dadurch entsteht der Eindruck, dass durch die Reservierung kein Vorteil auf Ressourcen angewendet wird.

Sobald Nutzungsdaten eingehen, wird der Wert in den richtigen Prozentsatz geändert. Wenn alle Nutzungsdaten gesammelt wurden, wird der richtige Wert bestimmt und korrekt im Graphen angezeigt.

## <a name="solution"></a>Lösung

Sollte Ihr Nutzungswert nicht Ihren Erwartungen entsprechen, überprüfen Sie den Graphen, um eine möglichst aktuelle Darstellung Ihrer tatsächlichen Nutzung zu erhalten. Punktwerte, die älter als zwei Tage sind, sollten korrekt sein. Längerfristige Durchschnittswerte zwischen sieben und 30 Tagen sollten korrekt sein.

## <a name="other-common-scenarios"></a>Andere häufige Szenarien
- Möglicherweise haben Sie aufgehört, Ressource A auszuführen und begonnen, Ressource B auszuführen, für die die Reservierung, die Sie gekauft haben, nicht gilt. Um dies zu beheben, müssen Sie möglicherweise die Reservierung austauschen, um sie an die richtige Ressource anzupassen. 
- Möglicherweise haben Sie eine Ressource von einem Abonnement oder einer Ressourcengruppe in eine andere verschoben, wobei der Geltungsbereich der Reservierung anders ist als der, in den die Ressource verschoben worden ist. Um dies zu beheben, müssen Sie möglicherweise den Geltungsbereich der Reservierung ändern.
- Möglicherweise haben Sie eine andere Reservierung erworben, die ebenfalls einen Nutzen für denselben Geltungsbereich anwendet, so dass weniger von einer bereits reservierten Instanz einen Nutzen anwendet. Um dies zu beheben, müssen Sie möglicherweise eine der Reservierungen austauschen/zurückerstatten.
- Möglicherweise haben Sie aufgehört, eine bestimmte Ressource auszuführen, wodurch sie keinen Gebrauch mehr von sich gibt und der Nutzen nicht mehr zur Anwendung kommt. Um dies zu beheben, müssen Sie möglicherweise die Reservierung austauschen, um sie an die richtige Ressource anzupassen. 
- Möglicherweise haben Sie den Geltungsbereich der Reservierung geändert und dies führte dazu, dass sie den Ressourcen keinen Nutzen mehr bringt. Um dies zu beheben, müssen Sie möglicherweise den Bereich der Reservierung erneut ändern, um sicherzustellen, dass die Ressourcen im gleichen Bereich bereitgestellt werden.
- Das Abonnement, auf das die Reservierung ausgerichtet war, wurde gelöscht oder verschoben, so dass die Leistung nicht auf die Ressourcen angewendet wird. Um dies zu beheben, müssen Sie eventuell den Bereich der Reservierung ändern.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwaltung von Reservierungen finden Sie unter [Verwalten von Reservierungen für Azure-Ressourcen](manage-reserved-vm-instance.md).

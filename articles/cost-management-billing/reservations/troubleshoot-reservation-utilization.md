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
ms.openlocfilehash: 3bade2b547db3b76bde02731807d4e6ef3539711
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649604"
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

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwaltung von Reservierungen finden Sie unter [Verwalten von Reservierungen für Azure-Ressourcen](manage-reserved-vm-instance.md).
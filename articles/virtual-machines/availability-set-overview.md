---
title: Übersicht über Verfügbarkeitsgruppen
description: Erfahren Sie mehr über die Verfügbarkeitsgruppen in Azure.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: ac6995a2653ea9f88a25e75f6c1b98e2446a4788
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622434"
---
# <a name="availability-sets-overview"></a>Übersicht über Verfügbarkeitsgruppen

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs

Dieser Artikel enthält eine Übersicht über die Verfügbarkeitsfunktionen von virtuellen Azure-Computern (VMs).

## <a name="what-is-an-availability-set"></a>Was ist eine Verfügbarkeitsgruppe? 

Eine Verfügbarkeitsgruppe ist eine logische Gruppierung von virtuellen Computern, die es Azure ermöglicht zu verstehen, wie Ihre Anwendung erstellt ist, um Redundanz und Verfügbarkeit zu bieten. Es empfiehlt sich, mindestens zwei virtuelle Computer in einer Verfügbarkeitsgruppe zu erstellen, um eine Anwendung mit hoher Verfügbarkeit zu erhalten und die [Azure-SLA von 99,95 Prozent](https://azure.microsoft.com/support/legal/sla/virtual-machines/) zu erfüllen. Für die Verfügbarkeitsgruppe selbst entstehen keine Kosten. Sie bezahlen nur für die einzelnen VM-Instanzen, die Sie erstellen.

## <a name="how-do-availability-sets-work"></a>Funktionsweise von Verfügbarkeitsgruppen
Jeder virtuelle Computer in der Verfügbarkeitsgruppe wird einer **Updatedomäne** (UD) und einer **Fehlerdomäne** (FD) der zugrunde liegenden Azure-Plattform zugewiesen. Jede Verfügbarkeitsgruppe kann mit bis zu drei Fehlerdomänen und 20 Updatedomänen konfiguriert werden. Updatedomänen definieren Gruppen virtueller Computer und zugrunde liegender physischer Hardwarekomponenten, die gleichzeitig neu gestartet werden können. Wenn innerhalb einer Verfügbarkeitsgruppe mehr als fünf virtuelle Computer konfiguriert sind, wird der sechste virtuelle Computer in derselben Updatedomäne wie der erste virtuelle Computer gespeichert, der siebte in derselben Updatedomäne wie der zweite virtuelle Computer usw. Während einer geplanten Wartung werden die Updatedomänen unter Umständen nicht der Reihe nach neu gestartet, sondern es wird jeweils nur eine Updatedomäne neu gestartet. Bei einer neu gestarteten Updatedomäne wird 30 Minuten gewartet, bevor die Wartung für eine andere Updatedomäne initiiert wird.

Mit Fehlerdomänen wird die Gruppe der virtuellen Computer definiert, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen. Die innerhalb der Verfügbarkeitsgruppe konfigurierten virtuellen Computer werden standardmäßig auf bis zu drei Fehlerdomänen verteilt. Auch wenn Verfügbarkeitsgruppen Ihre Anwendung nicht gänzlich vor Fehlern des Betriebssystems oder der Anwendung selbst schützen können, verringern sie doch die Auswirkungen von potenziellen Hardwarefehlern, Netzwerkausfällen oder Stromunterbrechungen.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagramm, das verschiedene Computecluster zeigt, die in Fehlerdomänen aufgeteilt sind und innerhalb dieser Fehlerdomänen über mehrere Updatedomänen verfügen":::

VMs sind auch an Datenträgerfehlerdomänen ausgerichtet. Diese Ausrichtung stellt sicher, dass sich alle verwalteten, an einen virtuellen Computer angefügten verwalteten Datenträger innerhalb der gleichen Fehlerdomäne befinden. 

Nur virtuelle Computer mit verwalteten Datenträgern können in einer verwalteten Verfügbarkeitsgruppe erstellt werden. Die Anzahl der Fehlerdomänen für verwaltete Datenträger variiert je nach Region – pro Region sind entweder zwei oder drei Fehlerdomänen für verwaltete Datenträger vorhanden. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagramm, das zeigt, wie die Fehlerdomänen für Datenträger und VMs ausgerichtet sind.":::

## <a name="next-steps"></a>Nächste Schritte
Sie können diese Verfügbarkeits- und Redundanzfunktionen jetzt nutzen, um Ihre Azure-Umgebung zu erstellen. Weitere Informationen zu bewährten Methoden finden Sie unter [Checkliste für die Verfügbarkeit](/azure/architecture/checklist/resiliency-per-service).


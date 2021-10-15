---
title: Koexistenz von SQL Server HADR und bereichsbezogenem Azure-Hybridvorteil
description: In diesem Artikel wird erläutert, wie der SQL Server HADR-Software Assurance-Vorteil und der bereichsbezogene Azure-Hybridvorteil koexistieren können.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 97c338134430156fefa5d874d44e27c8dc34a496
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547363"
---
# <a name="sql-server-hadr-and-scope-level-azure-hybrid-benefit-coexistence"></a>Koexistenz von SQL Server HADR und bereichsbezogenem Azure-Hybridvorteil

Einer der Vorteile von Software Assurance (SA) besteht darin, dass Azure-Kunden passive SQL Server Instanzen für die Notfallwiederherstellung installieren und ausführen können, um Failoverereignisse abzufangen. Die Verwaltung von Azure-Hybridvorteil auf Bereichsebene unterstützt den SQL Server HADR-Vorteil, indem sichergestellt wird, dass die qualifizierten Replikate für die Notfallwiederherstellung keine zugewiesenen SQL Server-Lizenzen nutzen. Daher müssen Sie die Replikate nicht separat verwalten. 

## <a name="hadr-benefit-selection"></a>HADR-Vorteilsauswahl

Wählen Sie **HADR** im Bereich **Konfigurieren** der Ressource **SQL Virtueller Computer** aus, wie in der folgenden Abbildung dargestellt, um den HADR-Vorteil zu nutzen.

:::image type="content" source="./media/sql-server-hadr-licenses/select-hadr-benefit.png" alt-text="Screenshot: HADR-Option in der Konfiguration der SQL-VM" lightbox="./media/sql-server-hadr-licenses/select-hadr-benefit.png" :::

Wenn die HADR-Eigenschaft ausgewählt ist, werden Lizenzen, die einem Bereich zentral zugewiesen sind, nicht verwendet, um die SQL Softwarekosten dieser Ressource zu diskontieren. Stattdessen wechselt die verbrauchsbasierte Bezahlung automatisch zu einer HADR-Verbrauchsmessung von 0 USD. Diese Methode stellt sicher, dass die zugewiesenen SQL-Lizenzen nur für die Ressourcen verwendet werden, für die eine Lizenzierung erforderlich ist. Sie müssen die SQL-Lizenzzuweisungen nicht erweitern, um die HADR-Vorteile zu berücksichtigen. Das folgende Diagramm veranschaulicht dieses Konzept.

Die in diesem Diagramm gezeigten Preise dienen nur als Beispiel.

:::image type="content" source="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg" alt-text="Ein Diagramm mit vollständig reduziertem Verbrauch virtueller Kerne und HADR-Auswahl" border="false" lightbox="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg":::

> [!NOTE]
> Die HADR-Option spiegelt die spezifische Rolle dieser SQL Server-Instanz in der AlwaysOn-Verfügbarkeitsgruppe wider. Die Auswahl liegt in der Verantwortung des Dienstbesitzers oder DBA und erfordert mindestens die [SQL Server Rolle „Mitwirkender“](../../role-based-access-control/built-in-roles.md#sql-server-contributor). Diese Aufgabe steht in keinem Zusammenhang mit Lizenzzuweisungen auf Gültigkeitsbereichsebene.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [häufig gestellten Fragen zur Verwaltung des Azure-Hybridvorteils auf Bereichsebene](faq-azure-hybrid-benefit-scope.yml).
- Informationen zur Anwendung von Rabatten finden Sie unter [Worum handelt es sich bei der Verwaltung von Azure-Hybridvorteil auf Bereichsebene?](sql-server-hadr-licenses.md).
- Hier erfahren Sie mehr über den [Übergang von der vorhandenen Azure-Hybridvorteilserfahrung](transition-existing.md).
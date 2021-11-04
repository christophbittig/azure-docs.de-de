---
title: Installieren von Visual Studio 2019
description: Installieren von Visual Studio und SQL Server Data Tools (SSDT) für Synapse SQL
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 378c36f1df7c83c6309be44d24ec671b64e34c08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054607"
---
# <a name="getting-started-with-visual-studio-2019"></a>Erste Schritte mit Visual Studio 2019

SQL Server Data Tools (SSDT) von Visual Studio **2019** ist ein einzelnes Tool, mit dem Sie folgende Aufgaben ausführen können:

- Verbinden, Abfragen und Entwickeln von Anwendungen
- Nutzen eines Objekt-Explorers, um alle Objekte im Datenmodell visuell zu untersuchen, einschließlich Tabellen, Ansichten, gespeicherten Prozeduren usw.
- Generieren von T-SQL-DDL-Skripts (Data Definition Language) für die Objekte
- Entwickeln Ihres Data Warehouse mithilfe eines zustandsbasierten Ansatzes für SSDT-Datenbankprojekte
- Integrieren Ihres Datenbankprojekts in Quellcodeverwaltungssysteme wie Git mit Azure Repos
- Einrichten von Pipelines für Continuous Integration und Continuous Deployment mit Automatisierungsservern wie Azure DevOps

## <a name="install-visual-studio-2019"></a>Installieren von Visual Studio 2019

Informationen zum Herunterladen und Installieren von Visual Studio **16.3 und höher** finden Sie unter [Visual Studio 2019 herunterladen](https://visualstudio.microsoft.com/downloads/). Wählen Sie während der Installation die Workload „Datenspeicherung und -verarbeitung“ aus. Eine eigenständige SSDT-Installation ist in Visual Studio 2019 nicht mehr erforderlich.

## <a name="unsupported-features-in-ssdt"></a>Nicht unterstützte Funktionen in SSDT

Gelegentlich bieten für Synapse SQL veröffentliche Featurereleases keine Unterstützung für SSDT. Die folgenden Funktionen werden derzeit nicht unterstützt:


- [Workloadverwaltung](sql-data-warehouse-workload-management.md) – Workloadgruppen und -klassifizierungen
- [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (einschließlich Tabellenwertfunktionen)
  - [Übermitteln Sie ein Supportticket, oder geben Sie Feedback](https://feedback.azure.com/d365community/idea/a9192b4c-0b25-ec11-b6e6-000d3a4f07b8), damit die Funktion unterstützt wird.
  - [Übermitteln Sie ein Supportticket, oder geben Sie Feedback](https://feedback.azure.com/d365community/idea/ab192b4c-0b25-ec11-b6e6-000d3a4f07b8), damit die Funktion unterstützt wird.
- Bestimmte T-SQL-Features wie:
   - *WITHIN GROUP*-Klausel in der [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql)-Zeichenfolgenfunktion.

## <a name="next-steps"></a>Nächste Schritte

Da Sie jetzt die neueste Version von SSDT verwenden, sind Sie bereit für die [Verbindungsherstellung](sql-data-warehouse-query-visual-studio.md) mit Ihrem SQL-Pool.
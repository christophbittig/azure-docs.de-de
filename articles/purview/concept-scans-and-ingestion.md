---
title: Überprüfungen und Erfassung
description: In diesem Artikel werden Überprüfungen und Erfassungen in Azure Purview erläutert.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 140e87f4e03081825fe75ba73b7c5ebd33b20ada
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398275"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Überprüfungen und Erfassung in Azure Purview

Dieser Artikel bietet eine Übersicht über die Features zur Überprüfung und Erfassung von Daten in Azure Purview. Diese Features verbinden Ihr Purview-Konto mit Ihren Quellen, um die Data Map und den Datenkatalog aufzufüllen, sodass Sie beginnen können, Ihre Daten mit Purview zu erkunden und zu verwalten.

## <a name="scanning"></a>Überprüfung

Nachdem Datenquellen in Ihrem Purview-Konto [registriert](manage-data-sources.md) wurden, besteht der nächste Schritt darin, die Datenquellen zu überprüfen. Der Überprüfungsprozess stellt eine Verbindung mit der Datenquelle her und erfasst technische Metadaten wie Namen, Dateigröße, Spalten usw. Bei diesem Prozess wird auch ein Schema für strukturierte Datenquellen extrahiert. Außerdem werden Klassifizierungen auf Schemas angewendet und [Vertraulichkeitsbezeichnungen zugewiesen, wenn Ihr Purview-Konto mit einem Microsoft 365 Security and Compliance Center (SCC)](create-sensitivity-label.md) verbunden ist. Der Überprüfungprozess kann sofort ausgelöst oder für eine regelmäßige Ausführung geplant werden, um Ihr Purview-Konto auf dem neuesten Stand zu halten.

Bei jedem Prüfvorgang können Sie Anpassungen vornehmen, damit Sie Ihre Quellen nur nach den von Ihnen benötigten Informationen durchsuchen.

### <a name="scope-your-scan"></a>Festlegen des Bereichs für Ihre Überprüfung

Beim Überprüfen einer Quelle haben Sie die Wahl, die gesamte Datenquelle zu überprüfen oder nur bestimmte Entitäten (Ordner/Tabellen) auszuwählen, die überprüft werden sollen. Die verfügbaren Optionen hängen von der zu überprüfenden Quelle ab und können sowohl für einmalige als auch für geplante Überprüfungen festgelegt werden.

Wenn Sie zum Beispiel [eine Überprüfung für eine Azure SQL-Datenbank-Instanz erstellen und durchführen](register-scan-azure-sql-database.md#creating-and-running-a-scan), können Sie auswählen, welche Tabellen überprüft werden sollen, oder die gesamte Datenbank auswählen.

### <a name="scan-rule-set"></a>Überprüfungsregelsatz

Ein Prüfungsregelsatz legt fest, nach welchen Arten von Informationen bei einer Überprüfung gesucht wird, wenn sie auf eine Ihrer Quellen angewendet wird. Die verfügbaren Regeln hängen von der Art der Quelle ab, die Sie überprüfen, aber sie umfassen Aspekte wie die [Dateitypen](sources-and-scans.md#file-types-supported-for-scanning), die Sie überprüfen sollten, und die Arten von [Klassifizierungen](supported-classifications.md), die Sie benötigen.

Es gibt bereits [Regelsätze für Systemüberprüfungen](create-a-scan-rule-set.md#system-scan-rule-sets) für viele Datenquellentypen, aber Sie können auch [eigene Regelsätze für Überprüfungen ](create-a-scan-rule-set.md) erstellen, um Ihre Überprüfungen für Ihre Organisation anzupassen.

## <a name="ingestion"></a>Erfassung

Die während des Überprüfungsprozesses erfassten technischen Metadaten oder Klassifizierungen werden dann an die Erfassung weitergeleitet. Der Erfassungsprozess ist für das Auffüllen der Data Map verantwortlich und wird von Purview verwaltet.  Bei der Erfassung werden die Eingaben aus der Überprüfung analysiert, [Ressourcensatzmuster](concept-resource-sets.md#how-azure-purview-detects-resource-sets) angewendet und verfügbare [Herkunftsinformationen](concept-data-lineage.md) aufgefüllt. Dann wird die Data Map automatisch geladen. Ressourcen/Schemas können erst nach Abschluss der Erfassung ermittelt oder zusammengestellt werden. Wenn Ihre Überprüfung abgeschlossen ist, Ihre Ressourcen aber noch nicht in der Data Map oder im Katalog zu sehen sind, müssen Sie warten, bis der Erfassungsprozess abgeschlossen ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen oder spezielle Anweisungen zum Überprüfen von Quellen finden Sie unter den nachstehenden Links.

* Informationen zu Ressourcensätzen finden Sie in unserem [Artikel zu Ressourcensätzen](concept-resource-sets.md).
* [Registrieren und Überprüfen einer Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)

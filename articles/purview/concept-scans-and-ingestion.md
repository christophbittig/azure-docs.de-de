---
title: Überprüfungen und Erfassung
description: In diesem Artikel werden Überprüfungen und Erfassungen in Azure Purview erläutert.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: af12878750c6976a3ce3b459d9cf1ff6b262c112
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024029"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Überprüfungen und Erfassung in Azure Purview

Dieser Artikel bietet eine Übersicht über die Features zur Überprüfung und Erfassung von Daten in Azure Purview. Diese Features verbinden Ihr Purview-Konto mit Ihren Quellen, um die Data Map und den Datenkatalog aufzufüllen, sodass Sie beginnen können, Ihre Daten mit Purview zu erkunden und zu verwalten.

## <a name="scanning"></a>Überprüfung

Nachdem Datenquellen in Ihrem Purview-Konto [registriert](manage-data-sources.md) wurden, besteht der nächste Schritt darin, die Datenquellen zu überprüfen. Der Überprüfungsprozess stellt eine Verbindung mit der Datenquelle her und erfasst technische Metadaten wie Namen, Dateigröße, Spalten usw. Bei diesem Prozess wird auch ein Schema für strukturierte Datenquellen extrahiert. Außerdem werden Klassifizierungen auf Schemas angewendet und [Vertraulichkeitsbezeichnungen zugewiesen, wenn Ihr Purview-Konto mit einem Microsoft 365 Security and Compliance Center (SCC)](create-sensitivity-label.md) verbunden ist. Der Überprüfungprozess kann sofort ausgelöst oder für eine regelmäßige Ausführung geplant werden, um Ihr Purview-Konto auf dem neuesten Stand zu halten.

Bei jedem Prüfvorgang können Sie Anpassungen vornehmen, damit Sie Ihre Quellen nur nach den von Ihnen benötigten Informationen durchsuchen.

### <a name="choose-an-authentication-method-for-your-scans"></a>Auswählen einer Authentifizierungsmethode für die Überprüfungen

Purview ist standardmäßig sicher. Kennwörter oder Geheimnisse werden nicht direkt in Purview gespeichert, sodass Sie eine Authentifizierungsmethode für Ihre Quellen auswählen müssen. Es gibt vier Authentifizierungsmöglichkeiten für Ihr Purview-Konto. Allerdings werden nicht alle Methoden für jede Datenquelle unterstützt.
 - Verwaltete Identität
 - Dienstprinzipal
 - SQL-Authentifizierung
 - Kontoschlüssel- oder Standardauthentifizierung

Nach Möglichkeit ist eine verwaltete Identität die bevorzugte Authentifizierungsmethode, da dadurch Speicherung und Verwaltung von Anmeldeinformationen für einzelne Datenquellen überflüssig werden. Dies kann den Zeitaufwand für Sie und Ihr Team für die Einrichtung und Problembehandlung der Authentifizierung für Überprüfungen erheblich verringern. Wenn Sie eine verwaltete Identität für Ihr Purview-Konto aktivieren, wird in Azure Active Directory eine Identität erstellt und an den Lebenszyklus Ihres Kontos gebunden. 

### <a name="scope-your-scan"></a>Festlegen des Bereichs für Ihre Überprüfung

Beim Überprüfen einer Quelle haben Sie die Wahl, die gesamte Datenquelle zu überprüfen oder nur bestimmte Entitäten (Ordner/Tabellen) auszuwählen, die überprüft werden sollen. Die verfügbaren Optionen hängen von der zu überprüfenden Quelle ab und können sowohl für einmalige als auch für geplante Überprüfungen festgelegt werden.

Wenn Sie zum Beispiel [eine Überprüfung für eine Azure SQL-Datenbank-Instanz erstellen und durchführen](register-scan-azure-sql-database.md#creating-the-scan), können Sie auswählen, welche Tabellen überprüft werden sollen, oder die gesamte Datenbank auswählen.

### <a name="scan-rule-set"></a>Überprüfungsregelsatz

Ein Prüfungsregelsatz legt fest, nach welchen Arten von Informationen bei einer Überprüfung gesucht wird, wenn sie auf eine Ihrer Quellen angewendet wird. Die verfügbaren Regeln hängen von der Art der Quelle ab, die Sie überprüfen, aber sie umfassen Aspekte wie die [Dateitypen](sources-and-scans.md#file-types-supported-for-scanning), die Sie überprüfen sollten, und die Arten von [Klassifizierungen](supported-classifications.md), die Sie benötigen.

Es gibt bereits [Regelsätze für Systemüberprüfungen](create-a-scan-rule-set.md#system-scan-rule-sets) für viele Datenquellentypen, aber Sie können auch [eigene Regelsätze für Überprüfungen ](create-a-scan-rule-set.md) erstellen, um Ihre Überprüfungen für Ihre Organisation anzupassen.

### <a name="schedule-your-scan"></a>Planen der Überprüfung

Purview bietet die Möglichkeit, Überprüfungen wöchentlich oder monatlich zu einem bestimmten, frei wählbaren Zeitpunkt durchzuführen. Wöchentliche Überprüfungen können für Datenquellen mit Strukturen geeignet sein, die sich aktiv in der Entwicklung befinden oder sich häufig ändern. Die monatliche Überprüfung ist besser für Datenquellen geeignet, die sich selten ändern. Es hat sich bewährt, zusammen mit dem Administrator der Quelle, die Sie überprüfen möchten, einen Zeitpunkt zu ermitteln, zu dem die Computeanforderungen an die Quelle gering sind.

### <a name="how-scans-detect-deleted-assets"></a>So werden gelöschte Ressourcen bei Scans erkannt

Ein Azure Purview-Katalog kennt den Status eines Datenspeichers nur, wenn er ihn überprüft. Damit der Katalog weiß, ob eine Datei, eine Tabelle oder ein Container gelöscht wurde, vergleicht er die letzte Scanausgabe mit der aktuellen Scanausgabe. Angenommen, die letzte Überprüfung eines Azure Data Lake Storage Gen2-Kontos umfasste einen Ordner mit dem Namen *folder1*. Bei erneuter Überprüfung desselben Kontos ist *folder1* nicht vorhanden. Daher geht der Katalog davon aus, dass der Ordner gelöscht wurde.

#### <a name="detecting-deleted-files"></a>Erkennen gelöschter Dateien

Die Logik zum Erkennen fehlender Dateien funktioniert sowohl bei mehreren Scans durch denselben Benutzer als auch durch andere Benutzer. Beispiel: Ein Benutzer führt einen einmaligen Scan für einen Data Lake Storage Gen2-Datenspeicher in den Ordnern A, B und C aus. Zu einem späteren Zeitpunkt führt ein anderer Benutzer im gleichen Konto eine andere einmalige Überprüfung der Ordner C, D und E desselben Datenspeichers aus. Da der Ordner C zweimal gescannt wurde, überprüft der Katalog ihn auf mögliche Löschungen. Die Ordner A, B, D und E wurden jedoch nur einmal gescannt, und der Katalog überprüft sie nicht auf gelöschte Ressourcen.

Um gelöschte Dateien aus Ihrem Katalog herauszuhalten, ist es wichtig, reguläre Scans auszuführen. Das Überprüfungsintervall ist wichtig, da der Katalog gelöschte Ressourcen erst erkennen kann, wenn eine andere Überprüfung ausgeführt wird. Wenn Sie also einmal pro Monat in einem bestimmten Speicher Überprüfungen ausführen, kann der Katalog gelöschte Datenressourcen in diesem Speicher erst erkennen, wenn Sie einen Monat später die nächste Überprüfung ausführen.

Wenn Sie große Datenspeicher wie Data Lake Storage Gen2 auflisten, gibt es mehrere Möglichkeiten (einschließlich Enumerationsfehlern und gelöschter Ereignisse), Informationen zu übersehen. Eine bestimmte Überprüfung könnte übersehen, dass eine Datei erstellt oder gelöscht wurde. Solange der Katalog nicht sicher ist, dass eine bestimmte Datei gelöscht wurde, wird sie nicht aus dem Katalog gelöscht. Diese Strategie bedeutet, dass Fehler auftreten können, wenn eine Datei, die im überprüften Datenspeicher nicht vorhanden ist, noch im Katalog vorhanden ist. In einigen Fällen muss ein Datenspeicher möglicherweise zwei oder drei Mal gescannt werden, bevor bestimmte gelöschte Ressourcen abgefangen werden.

> [!NOTE]
> Ressourcen, die zum Löschen markiert sind, werden nach einer erfolgreichen Überprüfung gelöscht. Gelöschte Ressourcen sind möglicherweise noch einige Zeit im Katalog sichtbar, bevor sie verarbeitet und entfernt werden.

## <a name="ingestion"></a>Erfassung

Die während des Überprüfungsprozesses erfassten technischen Metadaten oder Klassifizierungen werden dann an die Erfassung weitergeleitet. Der Erfassungsprozess ist für das Auffüllen der Data Map verantwortlich und wird von Purview verwaltet.  Bei der Erfassung werden die Eingaben aus der Überprüfung analysiert, [Ressourcensatzmuster](concept-resource-sets.md#how-azure-purview-detects-resource-sets) angewendet und verfügbare [Herkunftsinformationen](concept-data-lineage.md) aufgefüllt. Dann wird die Data Map automatisch geladen. Ressourcen/Schemas können erst nach Abschluss der Erfassung ermittelt oder zusammengestellt werden. Wenn Ihre Überprüfung abgeschlossen ist, Ihre Ressourcen aber noch nicht in der Data Map oder im Katalog zu sehen sind, müssen Sie warten, bis der Erfassungsprozess abgeschlossen ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen oder spezielle Anweisungen zum Überprüfen von Quellen finden Sie unter den nachstehenden Links.

* Informationen zu Ressourcensätzen finden Sie in unserem [Artikel zu Ressourcensätzen](concept-resource-sets.md).
* [Registrieren und Überprüfen einer Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database.md#creating-the-scan)
* [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)

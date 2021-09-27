---
title: Grundlegendes zur Migration von Azure Monitor-Warnungen
description: Enthält eine Beschreibung der Funktionsweise der Warnungsmigration sowie Informationen zur Problembehandlung.
ms.topic: conceptual
ms.date: 09/06/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 2167e0ea05206bc9c991353d6518090773934635
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543653"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Grundlegendes zu den Optionen zur Migration zu neueren Warnungen

Klassische Warnungen werden für Benutzer der öffentlichen Cloud [eingestellt](./monitoring-classic-retirement.md). Klassische Warnungen für die Azure Government-Cloud und Azure China 21Vianet werden am **29. Februar 2024** eingestellt.

In diesem Artikel wird erläutert, wie das Tool für die manuelle Migration und die freiwillige Migration verwendet wird, mit dem die verbleibenden Warnungsregeln migriert werden. Außerdem werden Lösungen für einige häufig auftretende Probleme beschrieben.

> [!IMPORTANT]
> Aktivitätsprotokollwarnungen (einschließlich Service Health-Warnungen) und Protokollwarnungen sind von der Migration nicht betroffen. Die Migration gilt nur für klassische Warnungsregeln, die [hier](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform) beschrieben werden.

> [!NOTE]
> Wenn Ihre klassischen Warnungsregeln ungültig sind (d. h. für [veraltete Metriken](#classic-alert-rules-on-deprecated-metrics) oder Ressourcen gelten, die gelöscht wurden), werden sie nicht migriert und sind nach Außerbetriebnahme des Diensts nicht mehr verfügbar.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Manuelles Migrieren klassischer Warnungen zu neueren Warnungen

Kunden, die ihre verbleibenden Warnungen manuell migrieren möchten, können dies anhand der folgenden Abschnitte bereits tun. Außerdem sind Metriken enthalten, die nicht mehr aktuell sind und daher nicht direkt migriert werden können.

### <a name="guest-metrics-on-virtual-machines"></a>Gastmetriken auf virtuellen Computern

Bevor Sie neue Metrikwarnungen für Gastmetriken erstellen können, müssen die Gastmetriken an den Azure Monitor-Protokollspeicher gesendet werden. Befolgen Sie die folgenden Anweisungen, um Warnungen zu erstellen:

- [Datenquellen für den Log Analytics-Agent in Azure Monitor](../agents/agent-data-sources.md)
- [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](./alerts-log.md)

Informieren Sie sich über [weitere Möglichkeiten](../agents/agents-overview.md) zum Erfassen von Gastmetriken und Warnungen für diese Metriken.

### <a name="storage-and-classic-storage-account-metrics"></a>Metriken für Speicherkonten und klassische Speicherkonten

Alle klassischen Warnungen in Speicherkonten können migriert werden, mit Ausnahme von Warnungen in den folgenden Metriken:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Klassische Warnungsregeln für Metriken vom Typ „Percent“ müssen basierend auf der [Zuordnung zwischen alten und neuen Speichermetriken](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics) migriert werden. Schwellenwerte müssen entsprechend geändert werden, da die neue verfügbare Metrik eine absolute Metrik ist.

Die klassischen Warnungsregeln für „AnonymousThrottlingError“, „SASThrottlingError“ und „ThrottlingError“ müssen in zwei neue Warnungen aufgeteilt werden, da es keine kombinierte Metrik gibt, die über die gleiche Funktionalität verfügt. Schwellenwerte müssen entsprechend angepasst werden.

### <a name="cosmos-db-metrics"></a>Cosmos DB-Metriken

Alle klassischen Warnungen für Cosmos DB-Metriken können migriert werden, mit Ausnahme von Warnungen für die folgenden Metriken:

- Mittelwert der Anforderungen pro Sekunde
- Konsistenzebene
- HTTP 2xx
- HTTP 3xx
- Max RUPM Consumed Per Minute (Maximal verwendete U/min)
- Max RUs Per Second (Maximale RUs pro Sekunde)
- Mongo Other Request Charge (Kosten sonstiger Mongo-Anforderungen)
- Mongo Other Request Rate (Rate sonstiger Mongo-Anforderungen)
- Beobachtete Leselatenz
- Beobachtete Schreiblatenz
- Dienstverfügbarkeit
- Speicherkapazität

„Mittelwert der Anforderungen pro Sekunde“, „Konsistenzebene“, „Max RUPM Consumed Per Minute“ (Maximal verwendete U/min), „Max RUs Per Second“ (Maximale RUs pro Sekunde), „Beobachtete Leselatenz“, „Beobachtete Schreiblatenz“ und „Speicherkapazität“ sind im [neuen System](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) derzeit nicht verfügbar.

Warnungen für Anforderungsmetriken wie „Http 2xx“, „Http 3xx“ und „Dienstverfügbarkeit“ werden nicht migriert, da sich die Art, wie Anforderungen bei den klassischen und neuen Metriken gezählt werden, unterscheidet. Warnungen für diese Metriken müssen manuell mit angepassten Schwellenwerten neu erstellt werden.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klassische Warnungsregeln für veraltete Metriken

Bei den folgenden Regeln handelt es sich um klassische Warnungsregeln für Metriken, die früher unterstützt wurden, irgendwann aber veraltet sind. Ein kleiner Prozentsatz der Kunden verfügt möglicherweise über ungültige klassische Warnungsregeln für diese Metriken. Da diese Warnungsregeln ungültig sind, werden sie nicht migriert.

| Ressourcentyp| Veraltete Metrik(en) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Erstellen entsprechender neuer Warnungsregeln und Aktionsgruppen

Das Migrationstool konvertiert klassische Warnungsregeln in entsprechende neue Warnungsregeln und Aktionsgruppen. Bei den meisten klassischen Warnungsregeln gelten die entsprechenden neuen Warnungsregeln für dieselben Metriken mit denselben Eigenschaften (z. B. `windowSize` und `aggregationType`). Es gibt jedoch einige klassische Warnungsregeln, die für Metriken gelten, die im neuen System eine andere Metrikentsprechung haben. Die folgenden Prinzipien gelten für die Migration von klassischen Warnungen, sofern im folgenden Abschnitt nichts anderes angegeben ist:

- **Häufigkeit**: Definiert, wie oft eine klassische oder neue Warnungsregel die Bedingung überprüft. Die `frequency` in klassischen Warnungsregeln konnte nicht vom Benutzer konfiguriert werden und betrug bei allen Ressourcentypen immer fünf Minuten. Die Frequenz äquivalenter Regeln ist ebenfalls auf fünf Minuten festgelegt.
- **Aggregationstyp**: Definiert, wie die Metrik über das interessierende Zeitfenster aggregiert wird. Auch der Aggregationstyp (`aggregationType`) bleibt für klassische und neue Warnungen und die meisten Metriken unverändert. Da bei klassischen und neuen Warnungen manchmal die Metrik unterschiedlich ist, wird das entsprechende, für die Metrik definierte Attribut `aggregationType` oder `primary Aggregation Type` verwendet.
- **Einheiten**: Eigenschaft der Metrik, für die die Warnung erstellt wird. Einige Metrikentsprechungen weisen unterschiedliche Einheiten auf. Der Schwellenwert wird nach Bedarf entsprechend angepasst. Beispiel: Wenn die ursprüngliche Metrik Sekunden, die neue entsprechende Metrik jedoch Millisekunden als Einheit hat, wird der ursprüngliche Schwellenwert mit 1000 multipliziert, um das gleiche Verhalten sicherzustellen.
- **Fenstergröße**: Definiert das Zeitfenster, über das die Metrikdaten aggregiert und mit dem Schwellenwert verglichen werden. An den Standardwerten für die Fenstergröße (`windowSize`) (z. B. 5 Min., 15 Min., 30 Min., 1 Stunde, 3 Stunden, 6 Stunden, 12 Stunden, 1 Tag) wurden bei der entsprechenden neuen Warnungsregel keine Änderung vorgenommen. Bei anderen Werten wird die nächstgelegene `windowSize` verwendet. Bei den meisten Kunden hat diese Änderung keine Auswirkungen. Ein kleiner Prozentsatz der Kunden muss den Schwellenwert möglicherweise optimieren, um genau dasselbe Verhalten zu erzielen.

In den folgenden Abschnitten werden die Metriken im Detail beschrieben, die im neuen System eine andere Metrikentsprechung haben. Alle Metriken, die bei klassischen und neuen Warnungsregeln unverändert bleiben, sind nicht aufgeführt. Eine Liste der im neuen System unterstützten Metriken finden Sie [hier](../essentials/metrics-supported.md).

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts und Microsoft.ClassicStorage/storageAccounts

Bei Speicherkontodiensten wie Blob Storage, Table Storage, Azure Files und Queue Storage werden die folgenden Metriken wie nachstehend gezeigt den entsprechenden Metriken zugeordnet:

| Metrik in klassischen Warnungen | Entsprechende Metrik in neuen Warnungen | Kommentare|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Transaktionsmetrik mit den Dimensionen „ResponseType“=„AuthorizationError“ und „Authentication“ = „Anonymous“| |
| AnonymousClientOtherError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„ClientOtherError“ und „Authentication“ = „Anonymous“ | |
| AnonymousClientTimeOutError| Transaktionsmetrik mit den Dimensionen „ResponseType“=„ClientTimeOutError“ und „Authentication“ = „Anonymous“ | |
| AnonymousNetworkError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„NetworkError“ und „Authentication“ = „Anonymous“ | |
| AnonymousServerOtherError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„ServerOtherError“ und „Authentication“ = „Anonymous“ | |
| AnonymousServerTimeOutError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„ServerTimeOutError“ und „Authentication“ = „Anonymous“ | |
| AnonymousSuccess | Transaktionsmetrik mit den Dimensionen „ResponseType“=„Success“ und „Authentication“ = „Anonymous“ | |
| AuthorizationError | Transaktionsmetrik mit der Dimension „ResponseType“=„AuthorizationError“ | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | Verwenden Sie als `aggregationType` „average“ anstelle von „last“. Die Metrik gilt nur für Blob-Dienste. |
| ClientOtherError | Transaktionsmetrik mit der Dimension „ResponseType“=„ClientOtherError“  | |
| ClientTimeoutError | Transaktionsmetrik mit der Dimension „ResponseType“=„ClientTimeOutError“ | |
| ContainerCount | ContainerCount | Verwenden Sie als `aggregationType` „average“ anstelle von „last“. Die Metrik gilt nur für Blob-Dienste. |
| NetworkError | Transaktionsmetrik mit der Dimension „ResponseType“=„NetworkError“ | |
| ObjectCount | BlobCount| Verwenden Sie als `aggregationType` „average“ anstelle von „last“. Die Metrik gilt nur für Blob-Dienste. |
| SASAuthorizationError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„AuthorizationError“ und „Authentication“ = „SAS“ | |
| SASClientOtherError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„ClientOtherError“ and „Authentication“ = „SAS“ | |
| SASClientTimeOutError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„ClientTimeOutError“ und „Authentication“ = „SAS“ | |
| SASNetworkError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„NetworkError“ und „Authentication“ = „SAS“ | |
| SASServerOtherError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„ServerOtherError“ und „Authentication“ = „SAS“ | |
| SASServerTimeOutError | Transaktionsmetrik mit den Dimensionen „ResponseType“=„ServerTimeOutError“ und „Authentication“ = „SAS“ | |
| SASSuccess | Transaktionsmetrik mit den Dimensionen „ResponseType“=„Success“ und „Authentication“ = „SAS“ | |
| ServerOtherError | Transaktionsmetrik mit der Dimension „ResponseType“=„ServerOtherError“ | |
| ServerTimeOutError | Transaktionsmetrik mit der Dimension „ResponseType“=„ServerTimeOutError“  | |
| Erfolg | Transaktionsmetrik mit der Dimension „ResponseType“=„Success“ | |
| TotalBillableRequests| Transaktionen | |
| TotalEgress | Ausgehende Daten | |
| TotalIngress | Eingehende Daten | |
| TotalRequests | Transaktionen | |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Für Cosmos DB gibt es die folgenden entsprechenden Metriken:

| Metrik in klassischen Warnungen | Entsprechende Metrik in neuen Warnungen | Kommentare|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| Datengröße | DataUsage| |
| Dokumentanzahl | DocumentCount||
| Indexgröße | IndexUsage||
| Dienst nicht verfügbar | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| Gedrosselte Anforderungen | TotalRequests mit Dimension "StatusCode" = "429"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| Interne Serverfehler | TotalRequests mit Dimension "StatusCode" = "500"}| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| HTTP 401 | TotalRequests mit Dimension "StatusCode" = "401"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| HTTP 400 | TotalRequests mit Dimension "StatusCode" = "400"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| Anzahl von Anforderungen | TotalRequests| Aggregationstyp „Max“ wird in „Count“ korrigiert|
| Mongo Count Request Charge (Kosten von Mongo-Zählungsanforderung)| MongoRequestCharge mit Dimension „CommandName“ = „count“||
| Mongo Count Request Rate (Rate von Mongo-Zählungsanforderung) | MongoRequestsCount mit Dimension „CommandName“ = „count“||
| Mongo Delete Request Charge (Kosten von Mongo-Löschanforderung) | MongoRequestCharge mit Dimension „CommandName“ = „delete“||
| Mongo Delete Request Rate (Rate von Mongo-Löschanforderung) | MongoRequestsCount mit Dimension „CommandName“ = „delete“||
| Mongo Insert Request Charge (Kosten von Mongo-Einfügeanforderung) | MongoRequestCharge mit Dimension „CommandName“ = „insert“||
| Mongo Insert Request Rate (Rate von Mongo-Einfügeanforderung) | MongoRequestsCount mit Dimension „CommandName“ = „insert“||
| Mongo Query Request Charge (Kosten von Mongo-Abfrageanforderung) | MongoRequestCharge mit Dimension „CommandName“ = „find“||
| Mongo Query Request Rate (Rate von Mongo-Abfrageanforderung) | MongoRequestsCount mit Dimension „CommandName“ = „find“||
| Mongo Update Request Charge (Kosten von Mongo-Aktualisierungsanforderung) | MongoRequestCharge mit Dimension „CommandName“ = „update“||
| Mongo Insert Failed Requests (Fehlgeschlagene Mongo-Einfügeanforderungen) | MongoRequestCount mit Dimensionen "CommandName" = "insert" und "Status" = "failed"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| Mongo Query Failed Requests (Fehlgeschlagene Mongo-Abfrageanforderungen) | MongoRequestCount mit Dimensionen "CommandName" = "query" und "Status" = "failed"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| Mongo Count Failed Requests (Fehlgeschlagene Mongo-Zählungsanforderungen) | MongoRequestCount mit Dimensionen "CommandName" = "count" und "Status" = "failed"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| Mongo Query Failed Requests (Fehlgeschlagene Mongo-Aktualisierungsanforderungen) | MongoRequestCount mit Dimensionen "CommandName" = "update" und "Status" = "failed"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| Mongo Other Failed Requests (Sonstige fehlgeschlagene Mongo-Anforderungen) | MongoRequestCount mit Dimensionen "CommandName" = "other" und "Status" = "failed"| Aggregationstyp „Average“ wird in „Count“ korrigiert|
| Mongo Delete Failed Requests (Fehlgeschlagene Mongo-Löschanforderungen) | MongoRequestCount mit Dimensionen "CommandName" = "delete" und "Status" = "failed"| Aggregationstyp „Average“ wird in „Count“ korrigiert|

### <a name="how-equivalent-action-groups-are-created"></a>Erstellen entsprechender Aktionsgruppen

Bei klassischen Warnungsregeln waren E-Mail-, Webhook-, Logik-App- und Runbook-Aktionen direkt mit der eigentlichen Warnungsregel verbunden. Die neuen Warnungsregeln verwenden Aktionsgruppen, die warnungsregelübergreifend wiederverwendet werden können. Das Migrationstool erstellt eine einzelne Aktionsgruppe für gleiche Aktionen, und zwar unabhängig davon, wie viele Warnungsregeln die Aktion verwenden. Vom Migrationstool erstellte Aktionsgruppen erhalten das Namensformat „Migrated_AG*“.

> [!NOTE]
> Für klassische Warnungen wurden lokalisierte E-Mails gesendet, die auf dem Gebietsschema des klassischen Administrators basierten, wenn sie zum Benachrichtigen klassischer Administratorrollen verwendet wurden. Neue Warnungs-E-Mails werden über Aktionsgruppen gesendet und sind nur in englischer Sprache verfügbar.

## <a name="rollout-phases"></a>Rolloutphasen

Der Rollout des Migrationstools erfolgt für Kunden, die klassische Warnungsregeln verwenden, in mehreren Phasen. Abonnementbesitzer erhalten eine E-Mail, wenn das Abonnement für die Migration mit dem Tool bereit ist.

> [!NOTE]
> Da der Rollout des Tools in Phasen erfolgt, wird Ihnen in den frühen Phasen möglicherweise angezeigt, dass einige Ihrer Abonnements noch nicht für die Migration bereit sind.

Derzeit sind die meisten Abonnements als migrationsbereit gekennzeichnet. Nur Abonnements mit klassischen Warnungsregeln für die folgenden Ressourcentypen sind noch nicht für die Migration bereit.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Wer kann die Migration auslösen?

Alle Benutzer, die auf Abonnementebene über die integrierte Rolle „Mitwirkender an der Überwachung“ verfügen, können die Migration auslösen. Benutzer, die über eine benutzerdefinierte Rolle mit den folgenden Berechtigungen verfügen, können die Migration ebenfalls auslösen:

- */Lesen
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Ihr Abonnement sollte nicht nur über die obigen Berechtigungen verfügen, sondern außerdem beim Microsoft.AlertsManagement-Ressourcenanbieter registriert werden. Dies ist für eine erfolgreiche Migration von Fehleranomaliewarnungen für Application Insights erforderlich. 

## <a name="common-problems-and-remedies"></a>Häufige Probleme und Abhilfemaßnahmen

Nachdem Sie [die Migration ausgelöst haben](alerts-using-migration-tool.md), erhalten Sie E-Mails an die Adressen, die Sie für die Benachrichtigung über den Abschluss der Migration oder die erforderliche Ausführung einer Aktion angegeben haben. In diesem Abschnitt sind einige häufige Probleme und der Umgang damit beschrieben.

### <a name="validation-failed"></a>Fehler bei der Überprüfung

Aufgrund von einigen kürzlich durchgeführten Änderungen an den klassischen Warnungsregeln in Ihrem Abonnement kann das Abonnement nicht migriert werden. Dies ist ein vorübergehendes Problem. Sie können die Migration neu starten, nachdem der Migrationsstatus nach einigen Tagen wieder **Bereit für die Migration** lautet.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Bereichssperre verhindert die Migration Ihrer Regeln

Im Rahmen der Migration werden neue Metrikwarnungen und neue Aktionsgruppen erstellt und anschließend klassische Warnungsregeln gelöscht. Eine Bereichssperre kann jedoch das Erstellen oder Löschen von Ressourcen verhindern. Je nach Bereichssperre können einige oder alle Regeln nicht migriert werden. Sie können dieses Problem beheben, indem Sie die Bereichssperre für das Abonnement, die Ressourcengruppe oder die Ressource aufheben, die im [Migrationstool](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel) aufgeführt ist, und die Migration erneut auslösen. Die Bereichssperre kann nicht deaktiviert werden und muss während des Migrationsprozesses entfernt werden. [Erfahren Sie mehr über das Verwalten von Bereichssperren](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Richtlinie mit Auswirkung „deny“ verhindert die Migration Ihrer Regeln

Im Rahmen der Migration werden neue Metrikwarnungen und neue Aktionsgruppen erstellt und anschließend klassische Warnungsregeln gelöscht. Eine [Azure Policy](../../governance/policy/index.yml)-Zuweisung kann jedoch das Erstellen von Ressourcen verhindern. Je nach Richtlinienzuweisung können einige oder alle Regeln nicht migriert werden. Die Richtlinienzuweisungen, die den Prozess blockieren, werden im [Migrationstool](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel) aufgeführt. Dieses Problem lässt sich auf eine der folgenden Arten beheben:

- Schließen Sie Abonnements, Ressourcengruppen oder einzelnen Ressourcen während des Migrationsprozesses aus der Richtlinienzuweisung aus. [Erfahren Sie mehr über das Verwalten von Ausschlussbereichen für Richtlinien](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Legen Sie den Erzwingungsmodus in der Richtlinienzuweisung auf **Deaktiviert** fest. [Erfahren Sie mehr über die enforcementMode-Eigenschaft einer Richtlinienzuweisung.](../../governance/policy/concepts/assignment-structure.md#enforcement-mode)
- Legen Sie eine Azure Policy-Ausnahme (Vorschau) für die Abonnements, Ressourcengruppen oder einzelnen Ressource in der Richtlinienzuweisung fest. [Erfahren Sie mehr über die Ausnahmestruktur von Azure Policy.](../../governance/policy/concepts/exemption-structure.md)
- Entfernen Sie die Auswirkung einer Richtlinie, oder ändern Sie die Auswirkung in „disabled“, „audit“, „append“ oder „modify“ (wodurch beispielsweise Probleme im Zusammenhang mit fehlenden Tags gelöst werden können). [Erfahren Sie mehr über das Verwalten von Richtlinienauswirkungen](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Nächste Schritte

- [How to use the migration tool](alerts-using-migration-tool.md) (Verwenden des Migrationstools)
- [Vorbereiten der Migration](alerts-prepare-migration.md)

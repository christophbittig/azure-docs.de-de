---
title: Schema für arbeitsbereichsbasierte Application Insights-Ressourcen in Azure Monitor
description: Erfahren Sie mehr über die neue Tabellenstruktur und das neue Tabellenschema für arbeitsbereichsbasierte Application Insights-Ressourcen in Azure Monitor.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 040f53836466dec446da6fcd312910ca7ad600e4
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615503"
---
# <a name="workspace-based-resource-changes"></a>Änderungen bei arbeitsbereichsbasierten Ressourcen

Vor der Einführung von [arbeitsbereichsbasierten Application Insights-Ressourcen](create-workspace-resource.md) wurden Application Insights-Daten getrennt von anderen Protokolldaten in Azure Monitor gespeichert. Beide basieren auf Azure Data Explorer und verwenden dieselbe Kusto-Abfragesprache (KQL). Dies ist unter [Protokolle in Azure Monitor](../logs/data-platform-logs.md) beschrieben.

Bei arbeitsbereichsbasierten Application Insights-Ressourcen werden Daten in einem Log Analytics-Arbeitsbereich mit anderen Überwachungs- und Anwendungsdaten gespeichert. Dies vereinfacht die Konfiguration, da es Ihnen möglich ist, Daten in mehreren Lösungen einfacher zu analysieren und die Funktionen von Arbeitsbereichen zu nutzen.

## <a name="table-structure"></a>Tabellenstruktur

| Alter Tabellenname | Neuer Tabellenname | BESCHREIBUNG |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Zusammenfassungsdaten aus Verfügbarkeitstests.|
| browserTimings | AppBrowserTimings | Daten über die Leistung des Clients, z. B. die angefallene Zeit zum Verarbeiten der eingehenden Daten.|
| dependencies | AppDependencies | Aufrufe von der Anwendung an andere Komponenten (einschließlich externer Komponenten), die über TrackDependency() aufgezeichnet werden – z. B. Aufrufe der REST-API, der Datenbank oder eines Dateisystems.  |
| customEvents | AppEvents | Benutzerdefinierte Ereignisse, die von Ihrer Anwendung erstellt werden. |
| customMetrics | AppMetrics | Benutzerdefinierte Metriken, die von Ihrer Anwendung erstellt werden. |
| pageViews | AppPageViews| Daten über jede Websiteansicht mit Browserinformationen. |
| performanceCounters | AppPerformanceCounters | Leistungsmessungen der Computeressourcen, die die Anwendung unterstützen, z. B. Windows-Leistungsindikatoren. |
| requests | AppRequests | Von Ihrer Anwendung empfangene Anforderungen. Beispielsweise wird für jede HTTP-Anforderung, die Ihre Web-App empfängt, ein separater Anforderungsdatensatz protokolliert.  |
| Ausnahmen | AppExceptions | Ausnahmen, die von der Anwendungslaufzeit ausgelöst werden, erfassen sowohl server- als auch clientseitige (Browser) Ausnahmen. |
| traces | AppTraces | Ausführliche Protokolle (Ablaufverfolgungen), die über Anwendungscode-/Protokollierungsframeworks ausgegeben und mithilfe von TrackTrace() aufgezeichnet werden. |

## <a name="table-schemas"></a>Tabellenschemas

In den folgenden Abschnitten wird die Zuordnung zwischen den klassischen Eigenschaftsnamen und den neuen arbeitsbereichsbasierten Application Insights-Eigenschaftsnamen gezeigt.  Verwenden Sie diese Informationen zum Konvertieren von Abfragen, die ältere Tabellen verwenden.

Die meisten Spalten haben denselben Namen mit unterschiedlicher Groß- und Kleinschreibung. Da bei KQL die Groß-/Kleinschreibung beachtet wird, müssen Sie in vorhandenen Abfragen die einzelnen Spaltennamen zusammen mit den Tabellennamen ändern. Spalten, die noch weitere Änderungen als die Groß-/Kleinschreibung aufweisen, sind hervorgehoben. Sie können weiterhin Ihre klassischen Application Insights-Abfragen innerhalb des Bereichs **Protokolle** Ihrer Application Insights-Ressource verwenden, auch wenn es sich um eine arbeitsbereichsbasierte Ressource handelt. Die neuen Eigenschaftsnamen werden für den Fall benötigt, dass Abfragen aus dem Kontext des Log Analytics-Arbeitsbereichs heraus ausgeführt werden.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Alte Tabelle: availability

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|customMeasurements|dynamisch|Messungen|Dynamisch|
|duration|real|DurationMs|real|
|`id`|Zeichenfolge|`Id`|Zeichenfolge|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|String|
|location|Zeichenfolge|Standort|Zeichenfolge|
|message|Zeichenfolge|`Message`|Zeichenfolge|
|name|Zeichenfolge|Name|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|performanceBucket|Zeichenfolge|PerformanceBucket|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|size|real|Size|real|
|success|Zeichenfolge|Erfolg|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Alte Tabelle: browserTimings

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|customMeasurements|dynamisch|Messungen|Dynamisch|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|Zeichenfolge|
|name|Zeichenfolge|Name|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|performanceBucket|Zeichenfolge|PerformanceBucket|Zeichenfolge|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|sendDuration|real|SendDurationMs|real|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|timestamp|datetime|TimeGenerated|datetime|
|totalDuration|real|TotalDurationMs|real|
|url|Zeichenfolge|url|Zeichenfolge|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

### <a name="appdependencies"></a>AppDependencies

Alte Tabelle: dependencies

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|customMeasurements|dynamisch|Messungen|Dynamisch|
|data|Zeichenfolge|Daten|Zeichenfolge|
|duration|real|DurationMs|real|
|`id`|Zeichenfolge|`Id`|Zeichenfolge|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|String|
|name|Zeichenfolge|Name|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|performanceBucket|Zeichenfolge|PerformanceBucket|Zeichenfolge|
|resultCode|Zeichenfolge|ResultCode|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|success|Zeichenfolge|Erfolg|Bool|
|target|Zeichenfolge|Ziel|Zeichenfolge|
|timestamp|datetime|TimeGenerated|datetime|
|Type|Zeichenfolge|DependencyType|Zeichenfolge|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

### <a name="appevents"></a>AppEvents

Alte Tabelle: customEvents

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|customMeasurements|dynamisch|Messungen|Dynamisch|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|Zeichenfolge|
|name|Zeichenfolge|Name|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

### <a name="appmetrics"></a>AppMetrics

Alte Tabelle: customMetrics

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|Zeichenfolge|
|name|Zeichenfolge|Name|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|
|value|real|(entfernt)||
|valueCount|INT|ValueCount|INT|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueStdDev|real|ValueStdDev|real|
|valueSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

Alte Tabelle: pageViews

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|customMeasurements|dynamisch|Messungen|Dynamisch|
|duration|real|DurationMs|real|
|`id`|Zeichenfolge|`Id`|Zeichenfolge|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|String|
|name|Zeichenfolge|Name|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|performanceBucket|Zeichenfolge|PerformanceBucket|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|timestamp|datetime|TimeGenerated|datetime|
|url|Zeichenfolge|url|Zeichenfolge|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Alte Tabelle: performanceCounters

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|category|Zeichenfolge|Category|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|Zähler|Zeichenfolge|(entfernt)||
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|instance|Zeichenfolge|Instanz|Zeichenfolge|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|Zeichenfolge|
|name|Zeichenfolge|Name|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|
|value|real|Wert|real|

### <a name="apprequests"></a>AppRequests

Alte Tabelle: requests

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|Dynamisch|
|customMeasurements|dynamisch|Messungen|Dynamisch|
|duration|real|DurationMs|Real|
|`id`|Zeichenfolge|`Id`|String|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|String|
|name|Zeichenfolge|Name|String|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|performanceBucket|Zeichenfolge|PerformanceBucket|String|
|resultCode|Zeichenfolge|ResultCode|String|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|source|Zeichenfolge|`Source`|String|
|success|Zeichenfolge|Erfolg|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|url|Zeichenfolge|url|String|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

### <a name="appsystemevents"></a>AppSystemEvents

Alte Tabelle: exceptions

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|Assembly|Zeichenfolge|Assembly|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|dynamisch|
|customMeasurements|dynamisch|Messungen|dynamisch|
|Details|dynamisch|Details|dynamisch|
|handledAt|Zeichenfolge|HandledAt|Zeichenfolge|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|innermostAssembly|Zeichenfolge|InnermostAssembly|Zeichenfolge|
|innermostMessage|Zeichenfolge|InnermostMessage|Zeichenfolge|
|innermostMethod|Zeichenfolge|InnermostMethod|Zeichenfolge|
|innermostType|Zeichenfolge|InnermostType|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|Zeichenfolge|
|message|Zeichenfolge|`Message`|Zeichenfolge|
|method|Zeichenfolge|Methode|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|outerAssembly|Zeichenfolge|OuterAssembly|Zeichenfolge|
|outerMessage|Zeichenfolge|OuterMessage|Zeichenfolge|
|outerMethod|Zeichenfolge|OuterMethod|Zeichenfolge|
|outerType|Zeichenfolge|OuterType|Zeichenfolge|
|problemId|Zeichenfolge|ProblemId|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|datetime|TimeGenerated|datetime|
|Type|Zeichenfolge|ExceptionType|Zeichenfolge|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

### <a name="apptraces"></a>AppTraces

Alte Tabelle: traces

|ApplicationInsights|Typ|LogAnalytics|Typ|
|:---|:---|:---|:---|
|appId|Zeichenfolge|\_ResourceGUID|Zeichenfolge|
|application_Version|Zeichenfolge|AppVersion|Zeichenfolge|
|appName|Zeichenfolge|\_ResourceId|Zeichenfolge|
|client_Browser|Zeichenfolge|ClientBrowser|Zeichenfolge|
|client_City|Zeichenfolge|ClientCity|Zeichenfolge|
|client_CountryOrRegion|Zeichenfolge|ClientCountryOrRegion|Zeichenfolge|
|client_IP|Zeichenfolge|ClientIP|Zeichenfolge|
|client_Model|Zeichenfolge|ClientModel|Zeichenfolge|
|client_OS|Zeichenfolge|ClientOS|Zeichenfolge|
|client_StateOrProvince|Zeichenfolge|ClientStateOrProvince|Zeichenfolge|
|client_Type|Zeichenfolge|ClientType|Zeichenfolge|
|cloud_RoleInstance|Zeichenfolge|AppRoleInstance|Zeichenfolge|
|cloud_RoleName|Zeichenfolge|AppRoleName|Zeichenfolge|
|customDimensions|dynamisch|Eigenschaften|dynamisch|
|customMeasurements|dynamisch|Messungen|dynamisch|
|iKey|Zeichenfolge|IKey|Zeichenfolge|
|itemCount|INT|ItemCount|INT|
|itemId|Zeichenfolge|\_ItemId|Zeichenfolge|
|itemType|Zeichenfolge|Type|Zeichenfolge|
|message|Zeichenfolge|`Message`|Zeichenfolge|
|operation_Id|Zeichenfolge|OperationId|Zeichenfolge|
|operation_Name|Zeichenfolge|Vorgangsname|Zeichenfolge|
|operation_ParentId|Zeichenfolge|OperationParentId|Zeichenfolge|
|operation_SyntheticSource|Zeichenfolge|OperationSyntheticSource|Zeichenfolge|
|sdkVersion|Zeichenfolge|SdkVersion|Zeichenfolge|
|session_Id|Zeichenfolge|SessionID|Zeichenfolge|
|severityLevel|INT|SeverityLevel|INT|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|Zeichenfolge|UserAccountId|Zeichenfolge|
|user_AuthenticatedId|Zeichenfolge|UserAuthenticatedId|Zeichenfolge|
|user_Id|Zeichenfolge|UserId|Zeichenfolge|

## <a name="next-steps"></a>Nächste Schritte

* [Untersuchen von Metriken](../essentials/metrics-charts.md)
* [Schreiben von Analytics-Abfragen](../logs/log-query-overview.md)


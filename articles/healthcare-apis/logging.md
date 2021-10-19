---
title: Protokollierung für Azure Healthcare-APIs
description: In diesem Artikel wird erläutert, wie die Protokollierung funktioniert und wie Sie die Protokollierung für die Azure Healthcare-APIs aktivieren.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: b71e6088a1a60c17ec16c1a5a265cf20fec53777
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787525"
---
# <a name="logging-for-azure-healthcare-apis-preview"></a>Protokollierung für Azure Healthcare-APIs (Vorschau)

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Die Azure-Plattform bietet drei Arten von Protokollen: Aktivitätsprotokolle, Ressourcenprotokolle und Azure Active Directory Protokolle. Weitere Informationen zu [Aktivitätsprotokollen finden Sie hier.](../azure-monitor/essentials/platform-logs-overview.md) In diesem Artikel erfahren Sie, wie die Protokollierung für die Azure Healthcare-APIs funktioniert.

## <a name="auditlogs"></a>AuditLogs
Aktivitätsprotokolle sind zwar für jede Azure-Ressource aus der Azure-Portal verfügbar, aber die Gesundheits-APIs geben Ressourcenprotokolle aus, die zwei Kategorien von Protokollen enthalten: AuditLogs und DiagnosticLogs.

- AuditLogs stellt einen Überwachungspfad für Gesundheitsdienste zur Verfügung, z. B. die IP-Adresse des Aufrufers und die Ressourcen-URL, wenn ein Benutzer oder eine Anwendung auf den FHIR-Dienst zutritt. Jeder Dienst gibt erforderliche Eigenschaften aus und implementiert optional zusätzliche Eigenschaften.
- DiagnosticLogs bietet Einblicke in den Betrieb des Diensts, z. B. Protokollebene (Informationen, Warnung oder Fehler) und Protokollmeldung.

Derzeit unterstützen ApIs für das Gesundheitswesen nur AuditLogs für die öffentliche Vorschau. DiagnosticLogs ist verfügbar, wenn der Dienst allgemein verfügbar ist.

Im Folgenden finden Sie ein Beispiel für AuditLog.

```
{
    "time": "2021-08-02 16:01:29Z",
    "resourceId": "/SUBSCRIPTIONS/xxx/RESOURCEGROUPS/xxx/PROVIDERS/MICROSOFT.HEALTHCAREAPIS/SERVICES/xxx",
    "operationName": "Microsoft.HealthcareApis/services/fhir-R4/search-type",
    "category": "AuditLogs",
    "resultType": "Started",
    "resultSignature": 0,
    "durationMs": 0,
    "callerIpAddress": "::ffff:73.164.17.31",
    "correlationId": "5d04211aaf172d43b83d9eb500464ec5",
    "identity": {
        "claims": {
            "iss": "https://sts.windows.net/xxx/",
            "oid": "xxx"
        }
    },
    "level": "Informational",
    "location": "South Central US",
    "uri": "https://xxx.azurehealthcareapis.com:443/Patient",
    "properties": {
        "fhirResourceType": "Patient"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Sie finden die neuesten unterstützten Metriken für die Gesundheits-APIs mit Azure Monitor [hier.](../azure-monitor/essentials/metrics-supported.md)

Weitere Informationen zu DICOM-Dienstprotokollen und -Metriken finden Sie [hier.](./dicom/enable-diagnostic-logging.md)

Weitere Informationen zu protokoll- IoT Connector und Metriken finden Sie [hier.](./azure-api-for-fhir/iot-metrics-display.md)
---
title: Senden von Daten aus der Windows Azure-Diagnoseerweiterung an Azure Event Hubs
description: Konfigurieren Sie die Diagnoseerweiterung in Azure Monitor so, dass Daten an Azure Event Hub gesendet werden, damit Sie diese an Speicherorte außerhalb von Azure weiterleiten können.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 6c8353064f96868c99a1e4f1feb270e6cd984bce
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994250"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Senden von Daten aus der Windows Azure-Diagnoseerweiterung an Azure Event Hubs
Die Azure-Diagnoseerweiterung ist ein Agent in Azure Monitor, der Überwachungsdaten vom Gastbetriebssystem und Workloads von virtuellen Azure-Computern und anderen Computeressourcen sammelt. In diesem Artikel wird beschrieben, wie Daten aus der Windows Azure-Diagnoseerweiterung (WAD) an [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) gesendet werden, damit Sie diese an Speicherorte außerhalb von Azure weiterleiten können.

## <a name="supported-data"></a>Unterstützte Daten

Die vom Gastbetriebssystem gesammelten Daten, die an Event Hubs gesendet werden können, umfassen die nachfolgend genannten Datentypen. Andere Datenquellen, die von WAD erfasst werden, einschließlich IIS-Protokollen und Absturzabbildern, können nicht an Event Hubs gesendet werden.

* Ereignisablaufverfolgung für Windows-Ereignisse (ETW)
* Leistungsindikatoren
* Windows-Ereignisprotokolle, einschließlich Anwendungsprotokolle im Windows-Ereignisprotokoll
* Infrastrukturprotokolle der Azure-Diagnose

## <a name="prerequisites"></a>Voraussetzungen

* Windows-Diagnoseerweiterung 1.6 oder höher. Einen Versionsverlauf finden Sie unter [Azure-Diagnoseerweiterung – Versionen und Verlauf des Konfigurationsschemas](diagnostics-extension-versions.md). Unterstützte Ressourcen sind in der [Übersicht zur Azure-Diagnoseerweiterung](diagnostics-extension-overview.md) angegeben.
* Der Event Hubs-Namespace muss immer bereitgestellt werden. Ausführliche Informationen finden Sie unter [Erste Schritte mit Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).


## <a name="configuration-schema"></a>Konfigurationsschema
Verschiedene Optionen zum Aktivieren und Konfigurieren der Diagnoseerweiterung finden Sie unter [Installieren und Konfigurieren der Windows Azure-Diagnoseerweiterung (WAD)](diagnostics-extension-windows-install.md). Eine Referenz zum Konfigurationsschema ist unter [Azure-Diagnosekonfigurationsschema](diagnostics-extension-schema-windows.md) enthalten. Im weiteren Verlauf dieses Artikels wird beschrieben, wie Sie diese Konfiguration zum Senden von Daten an einen Event Hub verwenden. 

Azure-Diagnose sendet Protokolle und Metriken stets an ein Azure Storage-Konto. Sie können eine oder mehrere *Datensenken* konfigurieren, die Daten an zusätzliche Speicherorte senden. Die einzelnen Senken sind im [SinksConfig-Element](diagnostics-extension-schema-windows.md#sinksconfig-element) der öffentlichen Konfiguration mit sensiblen Informationen in der privaten Konfiguration definiert. Diese Konfiguration für Event Hubs verwendet die Werte in der folgenden Tabelle.

| Eigenschaft | BESCHREIBUNG |
|:---|:---|
| Name | Beschreibender Name für die Senke. Wird in der Konfiguration verwendet, um die Datenquellen anzugeben, die an die Senke senden sollen. |
| url  | URL des Event Hubs im Format \<event-hubs-namespace\>.servicebus.windows.net/\<event-hub-name\>.          |
| SharedAccessKeyName | Name einer SAS-Richtlinie für den Event Hub, der mindestens über **Senden**-Autorität verfügt. |
| SharedAccessKey     | Primärer oder sekundär Schlüssel aus der SAS-Richtlinie für den Event Hub. |

Beispiele für die öffentliche und private Konfiguration sind nachfolgend dargestellt. Hierbei handelt es sich um eine minimale Konfiguration mit einem einzelnen Leistungsindikator und Ereignisprotokoll zur Veranschaulichung, wie die Event Hub-Datensenke konfiguriert und verwendet wird. Ein komplexeres Beispiel finden Sie unter [Azure-Diagnosekonfigurationsschema](diagnostics-extension-schema-windows.md).

### <a name="public-configuration"></a>Öffentliche Konfiguration

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Private Konfiguration

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Konfigurationsoptionen
Um Daten an eine Datensenke zu senden, geben Sie das **sinks**-Attribut im Knoten der Datenquelle an. Die jeweilige Platzierung des **sinks**-Attributs bestimmt den Bereich der Zuweisung. Im folgenden Beispiel wird das **sinks**-Attribut für den Knoten **PerformanceCounters** definiert, wodurch alle untergeordneten Leistungsindikatoren an den Event Hub gesendet werden.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


Im folgenden Beispiel wird das **sinks**-Attribut direkt auf drei Leistungsindikatoren angewendet, wodurch nur diese Leistungsindikatoren an den Event Hub gesendet werden. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Überprüfen der Konfiguration
Sie können anhand einer Vielzahl von Methoden überprüfen, ob Daten an den Event Hub gesendet werden. Eine einfache Methode ist die Verwendung von Event Hubs Capture gemäß der Beschreibung unter [Erfassen von Ereignissen über Azure Event Hubs in Azure Blob Storage oder Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Beheben von Problemen mit Event Hubs-Senken

- Sehen Sie sich die Azure Storage-Tabelle **WADDiagnosticInfrastructureLogsTable** an, die Protokolle und Fehler zur Azure-Diagnose selbst enthält. Sie können hierzu beispielsweise mit dem Tool [Azure Storage-Explorer](https://www.storageexplorer.com) eine Verbindung mit diesem Speicherkonto herstellen, diese Tabelle anzeigen und eine Abfrage nach einem TimeStamp innerhalb der letzten 24 Stunden hinzufügen. Sie können das Tool verwenden, um eine CSV-Datei zu exportieren und in einer Anwendung wie Microsoft Excel zu öffnen. Excel erleichtert das Suchen nach Callingcard-Zeichenfolgen wie **EventHubs**, um festzustellen, welcher Fehler gemeldet wird.  

- Überprüfen Sie, ob der Event Hub erfolgreich bereitgestellt wurde. Alle Verbindungsinformationen im Abschnitt **PrivateConfig** der Konfiguration müssen mit den Werten Ihrer Ressourcen übereinstimmen, wie sie im Portal angezeigt werden. Stellen Sie sicher, dass Sie eine SAS-Richtlinie im Portal definiert haben (im Beispiel *SendRule*) und die Berechtigung *Senden* gewährt wurde.  

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Event Hubs](../../event-hubs/event-hubs-about.md)
* [Erstellen eines Event Hubs](../../event-hubs/event-hubs-create.md)
* [Event Hubs – häufig gestellte Fragen](../../event-hubs/event-hubs-faq.yml)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png




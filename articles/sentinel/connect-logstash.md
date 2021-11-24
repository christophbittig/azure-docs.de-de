---
title: Verbinden von Datenquellen über Logstash mit Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Logstash zum Weiterleiten von Protokollen aus externen Datenquellen an Microsoft Sentinel verwenden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 1bb2d8c60fd8962363277a7f530db7d84394498e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521812"
---
# <a name="use-logstash-to-connect-data-sources-to-microsoft-sentinel"></a>Verwenden von Logstash zum Verbinden von Datenquellen mit Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Die Datenerfassung mithilfe des Logstash-Ausgabe-Plug-Ins befindet sich zurzeit in der öffentlichen Vorschauphase. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mithilfe des Ausgabe-Plug-Ins von Microsoft Sentinel für die **Logstash-Datensammlungs-Engine** können Sie nun jeden gewünschten Protokolltyp über Logstash direkt an Ihren Log Analytics-Arbeitsbereich in Microsoft Sentinel senden. Ihre Protokolle werden an eine benutzerdefinierte Tabelle gesendet, die Sie mithilfe des Ausgabe-Plug-Ins definieren.

Weitere Informationen zum Arbeiten mit der Logstash-Datensammlungs-Engine finden Sie unter [Erste Schritte mit Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Übersicht

### <a name="architecture-and-background"></a>Architektur und Hintergrund

![Diagramm der Logstash-Architektur.](./media/connect-logstash/logstash-architecture.png)

Die Logstash-Engine besteht aus drei Komponenten:

- Eingabe-Plug-Ins: Angepasste Sammlung von Daten aus verschiedenen Quellen.
- Filter-Plug-Ins: Bearbeitung und Normalisierung von Daten gemäß angegebener Kriterien.
- Ausgabe-Plug-Ins: Angepasstes Senden gesammelter und verarbeiteter Daten an verschiedene Ziele.

> [!NOTE]
> - Microsoft unterstützt nur das von Microsoft Sentinel bereitgestellte Logstash-Ausgabe-Plug-In, das hier beschrieben wird. Die aktuelle Version dieses Plug-Ins ist v1.0.0, veröffentlicht am 25.08.2020. Für Probleme im Zusammenhang mit dem Ausgabe-Plug-In können Sie [ein Supportticket erstellen](https://ms.portal.azure.com/#create/Microsoft.Support).
>
> - Logstash-Ausgabe-Plug-Ins von Drittanbietern für Microsoft Sentinel, andere Logstash-Plug-Ins oder Komponenten eines anderen Typs werden von Microsoft nicht unterstützt.
>
> - Das Logstash-Ausgabe-Plug-In von Microsoft Sentinel unterstützt nur **Logstash-Versionen von 7.0 bis 7.15**.

Das Microsoft Sentinel-Ausgabe-Plug-In für Logstash sendet JSON-formatierte Daten mithilfe der HTTP-Datensammler-REST-API von Log Analytics an Ihren Log Analytics-Arbeitsbereich. Die Daten werden in benutzerdefinierten Protokollen erfasst.

- Erfahren Sie mehr über die [Log Analytics-REST-API](/rest/api/loganalytics/create-request).
- Erfahren Sie mehr über [benutzerdefinierte Protokolle](../azure-monitor/agents/data-sources-custom-logs.md).

## <a name="deploy-the-microsoft-sentinel-output-plugin-in-logstash"></a>Bereitstellen des Microsoft Sentinel-Ausgabe-Plug-Ins in Logstash

### <a name="step-1-installation"></a>Schritt 1: Installation

Das Microsoft Sentinel-Ausgabe-Plug-In ist in der Logstash-Sammlung verfügbar.

- Befolgen Sie die Anweisungen im Logstash-Dokument zum [Arbeiten mit Plug-Ins](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html), um das Plug-In ***[microsoft-logstash-output-azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)*** zu installieren.
   
- Wenn Ihr Logstash-System keinen Internetzugriff hat, folgen Sie den Anweisungen im Logstash-Dokument zur [Offline-Plug-In-Verwaltung](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html), um ein Offline-Plug-In-Paket vorzubereiten und zu verwenden. (Dazu müssen Sie ein weiteres Logstash-System mit Internetzugriff erstellen.)

### <a name="step-2-configuration"></a>Schritt 2: Konfiguration

Fügen Sie anhand der Informationen im Logstash-Dokument zur [Struktur einer Konfigurationsdatei](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) das Microsoft Sentinel-Ausgabe-Plug-In mit den folgenden Schlüsseln und Werten zur Konfiguration hinzu: (Die ordnungsgemäße Konfigurationsdateisyntax ist unterhalb der Tabelle angegeben.)

| Feldname | Datentyp | Beschreibung |
|----------------|---------------|-----------------|
| `workspace_id` | Zeichenfolge | Geben Sie die ID-GUID Ihres Arbeitsbereichs ein (siehe Tipp). |
| `workspace_key` | Zeichenfolge | Geben Sie die GUID für den Primärschlüssel des Arbeitsbereichs ein (siehe Tipp). |
| `custom_log_table_name` | Zeichenfolge | Legen Sie den Namen der Tabelle fest, in der die Protokolle erfasst werden. Es kann nur ein Tabellenname pro Ausgabe-Plug-In konfiguriert werden. Die Protokolltabelle wird in Microsoft Sentinel im Bereich **Protokolle** unter **Tabellen** in der Kategorie **Benutzerdefinierte Protokolle** mit dem Suffix `_CL` angezeigt. |
| `endpoint` | Zeichenfolge | Optionales Feld. Dies ist standardmäßig der Log Analytics-Endpunkt. Verwenden Sie dieses Feld, um einen alternativen Endpunkt festzulegen. |
| `time_generated_field` | Zeichenfolge | Optionales Feld. Diese Eigenschaft überschreibt das standardmäßige Feld **TimeGenerated** in Log Analytics. Geben Sie den Namen des Zeitstempelfelds in der Datenquelle ein. Die Daten im Feld müssen dem ISO 8601-Format (`YYYY-MM-DDThh:mm:ssZ`) entsprechen. |
| `key_names` | array | Geben Sie eine Liste der Ausgabeschemafelder in Log Analytics ein. Jedes Listenelement muss in einfache Anführungszeichen eingeschlossen, die Elemente müssen durch Kommas getrennt und die gesamte Liste muss in eckige Klammern eingeschlossen werden. Ein Beispiel sehen Sie unten. |
| `plugin_flush_interval` | number | Optionales Feld. Geben Sie einen Wert an, um das maximale Intervall (in Sekunden) zwischen den Nachrichtenübertragungen an Log Analytics zu definieren. Der Standardwert ist 5. |
| `amount_resizing` | boolean | „true“ oder „false“. Aktivieren oder deaktivieren Sie den automatischen Skalierungsmechanismus, der die Größe des Nachrichtenpuffers entsprechend der Menge der empfangenen Protokolldaten anpasst. |
| `max_items` | number | Optionales Feld. Gilt nur, wenn `amount_resizing` auf „false“ festgelegt ist. Hiermit können Sie eine Obergrenze für die Nachrichtenpuffergröße (in Datensätzen) festlegen. Der Standardwert ist „2000“.  |
| `azure_resource_id` | Zeichenfolge | Optionales Feld. Definiert die ID der Azure-Ressource, in der sich die Daten befinden. <br>Der Wert der Ressourcen-ID ist besonders hilfreich, wenn Sie die [rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext](resource-context-rbac.md) verwenden, um nur Zugriff auf bestimmte Daten bereitzustellen. |
| | | |

> [!TIP]
> -  Sie finden die Arbeitsbereichs-ID und den Primärschlüssel in der Arbeitsbereichsressource unter **Agent-Verwaltung**.
> - Da die Speicherung von Anmeldeinformationen und anderen vertraulichen Informationen in Klartext in Konfigurationsdateien jedoch **nicht** den bewährten Sicherheitspraktiken entspricht, wird Ihnen dringend empfohlen, den **Logstash-Schlüsselspeicher** zu verwenden, um Ihre **Arbeitsbereichs-ID** und den **Primärschlüssel des Arbeitsbereichs** sicher in die Konfiguration aufzunehmen. Eine Anleitung hierzu finden Sie in der [Dokumentation zu Elastic](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

#### <a name="sample-configurations"></a>Beispielkonfigurationen

Hier sind einige Beispielkonfigurationen, in denen einige verschiedene Optionen verwendet werden.

- Eine einfache Konfiguration, die eine Filebeat-Eingabepipe verwendet:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Eine einfache Konfiguration, die eine TCP-Eingabepipe verwendet:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Eine erweiterte Konfiguration:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Besuchen Sie das [GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) für Ausgabe-Plug-Ins, um mehr über seine innere Funktionsweise, Konfiguration und Leistungseinstellungen zu erfahren.

### <a name="step-3-restart-logstash"></a>Schritt 3: Neustarten von Logstash

### <a name="step-4-view-incoming-logs-in-microsoft-sentinel"></a>Schritt 4: Anzeigen eingehender Protokolle in Microsoft Sentinel

1. Überprüfen Sie, ob Nachrichten an das Ausgabe-Plug-In gesendet werden.

1. Klicken Sie im Microsoft Sentinel-Navigationsmenü auf **Protokolle**. Erweitern Sie unter der Überschrift **Tabellen** die Kategorie **Benutzerdefinierte Protokolle**. Suchen Sie den Namen der von Ihnen in der Konfiguration angegebenen Tabelle (mit dem Suffix `_CL`), und klicken Sie darauf.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Screenshot von benutzerdefinierten Logstash-Protokollen.":::

1. Zum Anzeigen von Datensätzen in der Tabelle fragen Sie die Tabelle ab. Verwenden Sie dazu den Tabellennamen als Schema.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Screenshot einer benutzerdefinierten Logstash-Protokollabfrage.":::

## <a name="monitor-output-plugin-audit-logs"></a>Überwachen der Ausgabe-Plug-In-Überwachungsprotokolle

Zum Überwachen der Konnektivität und Aktivität des Microsoft Sentinel-Ausgabe-Plug-Ins aktivieren Sie die entsprechende Logstash-Protokolldatei. Informationen zum Speicherort der Protokolldatei finden Sie im Dokument zum [Logstash-Verzeichnislayout](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout).

Wenn in dieser Protokolldatei keine Daten angegeben sind, generieren und senden Sie einige Ereignisse lokal (über die Eingabe- und Filter-Plug-Ins), um sicherzustellen, dass das Ausgabe-Plug-In Daten empfängt. Microsoft Sentinel unterstützt nur Probleme im Zusammenhang mit dem Ausgabe-Plug-In.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Logstash zum Verbinden externer Datenquellen mit Microsoft Sentinel verwenden. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit dem Erkennen von Bedrohungen mit Microsoft Sentinel mithilfe von [integrierten](detect-threats-built-in.md) oder [benutzerdefinierten](detect-threats-custom.md) Regeln.

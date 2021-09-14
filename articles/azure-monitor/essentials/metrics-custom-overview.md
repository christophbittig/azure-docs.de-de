---
title: Benutzerdefinierte Metriken in Azure Monitor (Vorschau)
description: Erfahren Sie mehr zu benutzerdefinierten Metriken in Azure Monitor und wie diese modelliert werden.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a456db938d1607565525aea7ab2e9b43bc368aeb
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122343147"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Benutzerdefinierte Metriken in Azure Monitor (Vorschau)

Wenn Sie Ressourcen und Anwendungen in Azure bereitstellen, sollten Sie mit dem Erfassen von Telemetriedaten beginnen, um Einblicke in deren Leistung und Integrität zu erhalten. Azure stellt Ihnen einige sofort einsetzbare Metriken zur Verfügung. Diese Metriken werden als [Standard- oder Plattformmetriken](./metrics-supported.md) bezeichnet. Allerdings sind sie beschränkt. 

Möglicherweise möchten Sie einige benutzerdefinierte Leistungsindikatoren oder unternehmensspezifische Metriken sammeln, um eingehendere Erkenntnisse bereitzustellen. Diese **benutzerdefinierten** Metriken können über Ihre Anwendungstelemetrie, einen auf Ihren Azure-Ressourcen ausgeführten Agent, oder sogar über ein außerhalb des Unternehmens befindliches Überwachungssystem gesammelt und direkt an Azure Monitor übermittelt werden. Nach der Veröffentlichung in Azure Monitor können Sie nach benutzerdefinierten Metriken für Ihre Azure-Ressourcen und -Anwendungen suchen, diese abfragen und Warnungen ausgeben, und zwar parallel zu den von Azure ausgegebenen Standardmetriken.

Benutzerdefinierte Azure Monitor-Metriken sind aktuell in der öffentlichen Vorschauphase. 

## <a name="methods-to-send-custom-metrics"></a>Methoden zum Senden benutzerdefinierter Metriken

Benutzerdefinierte Metriken können über verschiedene Methoden an Azure Monitor übermittelt werden:
- Instrumentieren Ihrer Anwendung mit dem Azure Application Insights SDK und Senden benutzerdefinierter Telemetriedaten an Azure Monitor 
- Installieren Sie den Azure Monitor-Agent (Vorschau) auf Ihrer [Windows- oder Linux-Azure-VM](../agents/azure-monitor-agent-overview.md), und verwenden Sie eine [Datensammlungsregel](../agents/data-collection-rule-azure-monitor-agent.md), um Leistungsindikatoren an Azure Monitor-Metriken zu senden.
- Installieren der WAD-Erweiterung (Windows Azure-Diagnose) auf Ihrer [Azure-VM](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md), [VM-Skalierungsgruppe](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), [klassischen VM](../essentials/collect-custom-metrics-guestos-vm-classic.md) oder Ihrem [klassischen Clouddienst](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) und Senden der Leistungsindikatoren an Azure Monitor 
- Installieren des [InfluxData Telegraf-Agents](../essentials/collect-custom-metrics-linux-telegraf.md) auf Ihrer Azure-Linux-VM und Senden der Metriken mithilfe des Ausgabe-Plug-Ins von Azure Monitor
- Senden von benutzerdefinierten Metriken [direkt an die Azure Monitor-REST-API](./metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

## <a name="pricing-model-and-retention"></a>Preismodell und Aufbewahrung

Details dazu, wann die Abrechnung für benutzerdefinierte Metriken und Metrikabfragen aktiviert wird, erfahren Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/). Bestimmte Preisdetails für alle Metriken, einschließlich benutzerdefinierter Metriken, und Metrikabfragen finden Sie auf dieser Seite. Zusammenfassend fallen keine Kosten für die Erfassung von Standardmetriken (Plattformmetriken) im Azure Monitor-Metrikspeicher an. Doch für benutzerdefinierte Metriken werden Kosten anfallen, sobald sie die Phase der allgemeinen Verfügbarkeit erreichen. Metrik-API-Abfragen verursachen Kosten.

Benutzerdefinierte Metriken werden [genauso lange wie Plattformmetriken](../essentials/data-platform-metrics.md#retention-of-metrics) aufbewahrt. 

> [!NOTE]  
> Metriken, die über das Application Insights SDK an Azure Monitor gesendet werden, werden als erfasste Protokolldaten in Rechnung gestellt. Sie verursachen nur dann zusätzliche Metrikgebühren, wenn das Application Insights-Feature [Dimensionswarnungen für benutzerdefinierte Metriken aktivieren](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) ausgewählt wurde. Bei Aktivieren dieses Kontrollkästchens werden Daten über die API für benutzerdefinierte Metriken an die Azure Monitor-Metrikdatenbank gesendet, um komplexere Warnungen zu ermöglichen.  Erfahren Sie mehr über das [Application Insights-Preismodell](../app/pricing.md#pricing-model) und [Preise in Ihrer Region](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="how-to-send-custom-metrics"></a>Vorgehensweise beim Senden benutzerdefinierter Metriken

Wenn Sie benutzerdefinierte Metriken an Azure Monitor senden, muss jeder gemeldete Datenpunkt (oder Wert) die folgenden Informationen enthalten.

### <a name="authentication"></a>Authentifizierung
Zum Übermitteln von benutzerdefinierten Metriken an Azure Monitor muss die Entität über ein gültiges Azure Active Directory-Token (Azure AD) im **Bearer**-Header der Anforderung verfügen. Es gibt einige unterstützte Möglichkeiten, einen gültigen Bearertoken zu erhalten:
1. [Verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) Gibt einer Azure-Ressource (z.B. einer VM) eine Identität. Verwaltete Dienstidentität (MSI) gewährt Ressourcen die Berechtigungen zum Durchführen bestimmter Vorgänge. Zum Beispiel erlaubt sie einer Ressource das Ausgeben von Metriken über sich. Einer Ressource oder ihrer MSI kann die Berechtigung **Überwachungsmetriken veröffentlichen** für eine andere Ressource gewährt werden. Mit dieser Berechtigung kann die MSI auch Metriken für andere Ressourcen ausgeben.
2. [Azure AD-Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md) In diesem Szenario können einer AAD-Anwendung oder einem Dienst die Berechtigungen zum Ausgeben von Metriken zu einer Azure-Ressource gewährt werden.
Azure Monitor überprüft das Anwendungstoken mithilfe von öffentlichen Azure AD-Schlüsseln, um die Anforderung zu authentifizieren. Die vorhandene Rolle **Überwachungsmetriken veröffentlichen** verfügt bereits über diese Berechtigung. Sie ist im Azure-Portal verfügbar. Dem Dienstprinzipal kann die Rolle **Überwachungsmetriken veröffentlichen** im erforderlichen Gültigkeitsbereich erteilt werden, je nachdem, für welche Ressourcen er benutzerdefinierte Metriken ausgibt. Dabei kann es sich um ein Abonnement, eine Ressourcengruppe oder eine bestimmte Ressource handeln.

> [!TIP]  
> Wenn Sie ein Azure AD-Token zum Ausgeben von benutzerdefinierten Metriken anfordern, stellen Sie sicher, dass die Zielgruppe oder Ressource, für die das Token angefordert wird, `https://monitoring.azure.com/` ist. Fügen Sie auch unbedingt den nachgestellten Schrägstrich (/) hinzu.

### <a name="subject"></a>Subject
Diese Eigenschaft erfasst, für welche Azure-Ressourcen-ID die benutzerdefinierte Metrik gemeldet wird. Diese Informationen werden in der URL des jeweiligen API-Aufrufs codiert. Jede API kann nur metrische Werte für eine einzelne Azure-Ressource übermitteln.

> [!NOTE]  
> Sie können keine benutzerdefinierten Metriken für die Ressourcen-ID einer Ressourcengruppe oder eines Abonnements ausgeben.


### <a name="region"></a>Region
Diese Eigenschaft erfasst, in welcher Azure-Region die Ressource, für die Sie Metriken senden, bereitgestellt wird. Metriken müssen an denselben regionalen Endpunkt von Azure Monitor ausgegeben werden wie die Region, in der die Ressource bereitgestellt wird. Benutzerdefinierte Metriken für eine VM, die in der Region „USA, Westen“ bereitgestellt wurde, müssen z.B. an den regionalen Endpunkt „WestUS“ von Azure Monitor gesendet werden. Die Regionsinformationen sind auch in der URL des API-Aufrufs codiert.

> [!NOTE]  
> Während der öffentlichen Vorschau sind benutzerdefinierte Metriken nur in einigen Azure-Regionen verfügbar. Eine Liste der unterstützten Regionen ist in einem späteren Abschnitt dieses Artikels dokumentiert.
>
>

### <a name="timestamp"></a>Timestamp
Jeder Datenpunkt, der an Azure Monitor gesendet wird, muss mit einem Zeitstempel gekennzeichnet sein. Dieser Zeitstempel erfasst den Zeitpunkt (Datum/Uhrzeit), zu dem der Metrikwert gemessen oder erfasst wurde. Azure Monitor akzeptiert metrische Daten mit Zeitstempeln, die bis zu 20 Minuten in der Vergangenheit und bis zu 5 Minuten in der Zukunft liegen. Der Zeitstempel muss im ISO 8601-Format vorliegen.

### <a name="namespace"></a>Namespace
Namespaces sind eine Möglichkeit, ähnliche Metriken zu kategorisieren oder zu gruppieren. Namespaces ermöglichen es Ihnen, Gruppen von Metriken zu isolieren, die unterschiedliche Erkenntnisse oder Leistungsindikatoren erfassen. Sie könnten z.B. über den Namespace **contosomemorymetrics** verfügen, der Metriken zur Arbeitsspeichernutzung verfolgt, um ein Profile Ihrer App zu erstellen. Ein anderer Namespace mit dem Namen **contosoapptransaction** könnte alle Metriken über Benutzertransaktionen in Ihrer Anwendung verfolgen.

### <a name="name"></a>Name
**Name** ist der Name der Metrik, die gemeldet wird. Normalerweise ist der Name ausreichend beschreibend, um zu erkennen, was gemessen wird. Ein Beispiel ist eine Metrik, die die Anzahl der Speicherbytes misst, die auf einem bestimmten virtuellen Computer genutzt werden. Der Metrikname könnte beispielsweise **Verwendete Arbeitsspeicherbytes** lauten.

### <a name="dimension-keys"></a>Dimensionsschlüssel
Eine Dimension ist ein Schlüssel-Wert-Paar, das hilft, zusätzliche Merkmale der zu erfassenden Metrik zu beschreiben. Die zusätzlichen Merkmale ermöglichen die Erfassung weiterer Informationen zur Metrik und damit umfangreichere Erkenntnisse. Die Metrik **Verwendete Arbeitsspeicherbytes** könnte z.B. einen Dimensionsschlüssel namens **Prozess** verwenden, der erfasst, wie viele Bytes des Arbeitsspeichers pro Prozess auf einer VM belegt werden. Mithilfe dieses Schlüssels können Sie die Metrik filtern, um zu sehen, wie viele speicherspezifische Prozesse verwendet werden, oder um die fünf Prozesse mit der höchsten Speicherauslastung zu identifizieren.
Dimensionen sind optional, möglicherweise weisen nicht alle Metriken Dimensionen auf. Eine benutzerdefinierte Metrik kann bis zu 10 Dimensionen umfassen.

### <a name="dimension-values"></a>Dimensionswerte
Wenn Sie einen metrischen Datenpunkt melden, gibt es für jeden Dimensionsschlüssel der zu meldenden Metrik einen zugehörigen Dimensionswert. Angenommen, Sie möchten den von der ContosoApp auf Ihrer VM belegten Arbeitsspeicher melden:

* Der Metrikname wäre **Verwendete Arbeitsspeicherbytes**.
* Der Dimensionsschlüssel wäre **Prozess**.
* Der Dimensionswert wäre **ContosoApp.exe**.

Wenn Sie einen Metrikwert veröffentlichen, können Sie nur einen einzigen Dimensionswert pro Dimensionsschlüssel angeben. Wenn Sie dieselbe Speicherauslastung für mehrere Prozesse auf der VM erfassen, können Sie mehrere Metrikwerte für diesen Zeitstempel melden. Jeder Metrikwert würde einen anderen Dimensionswert für den Dimensionsschlüssel **Prozess** angeben.
Dimensionen sind optional, möglicherweise weisen nicht alle Metriken Dimensionen auf. Wenn ein Metrikbeitrag Dimensionsschlüssel definiert, sind die entsprechenden Dimensionswerte erforderlich.

### <a name="metric-values"></a>Metrikwerte
Azure Monitor speichert alle Metriken in Granularitätsintervallen von einer Minute. Uns ist bewusst, dass eine Metrik im Lauf einer Minute möglicherweise mehrere Male abgefragt werden muss. Ein Beispiel ist die CPU-Auslastung. Eventuell muss sie auch für viele separate Ereignisse gemessen werden. Ein Beispiel sind Wartezeiten bei Anmeldetransaktionen. Sie können die Werte vorab lokal aggregieren und ausgeben, um die Anzahl der Rohwerte einzuschränken, die Sie in Azure Monitor ausgeben und bezahlen müssen:

* **Min**: Der beobachtete Mindestwert aus allen Stichproben und Messungen während der Minute.
* **Max**: Der beobachtete Höchstwert aus allen Stichproben und Messungen während der Minute.
* **Sum**: Die Summe aller beobachteten Werte aus allen Stichproben und Messungen während der Minute.
* **Count**: Die Anzahl der Stichproben und Messungen, die während der Minute durchgeführt wurden.

Wenn z. B. innerhalb einer bestimmten Minute vier Anmeldetransaktionen für Ihre App stattfanden, ergeben sich daraus möglicherweise die folgenden gemessenen Wartezeiten für jede dieser Transaktionen:

|Transaktion 1|Transaktion 2|Transaktion 3|Transaktion 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

Als Ergebnis würde die folgende Metrik an Azure Monitor gemeldet werden:
* Min: 4
* Max: 16
* Summe: 40
* Anzahl: 4

Wenn Ihre Anwendung nicht in der Lage ist, vorab die lokale Aggregierung durchzuführen und jede einzelne Stichprobe oder jedes einzelne Ereignis sofort nach der Erfassung ausgeben muss, können Sie die Rohmesswerte ausgeben. So würden Sie z.B. jedes Mal, wenn in Ihrer App eine Anmeldetransaktion stattgefunden hat, eine Metrik mit nur einer einzigen Messung an Azure Monitor übermitteln. Für eine Anmeldetransaktion, die 12 ms dauerte, würden also die folgenden Metriken ausgegeben werden:
* Min: 12
* Max: 12
* Summe: 12
* Anzahl: 1

Dieser Prozess ermöglicht es Ihnen, mehrere Werte für dieselbe Kombination aus Metrik und Dimension während einer bestimmten Minute auszugeben. Azure Monitor aggregiert dann alle für eine bestimmte Minute übermittelten Rohwerte.

### <a name="sample-custom-metric-publication"></a>Beispiel einer benutzerdefinierten metrischen Veröffentlichung
Im folgenden Beispiel erstellen Sie eine benutzerdefinierte Metrik namens **Memory Bytes in Use** unter dem Metriknamespace **Memory Profile** für einen virtuellen Computer. Die Metrik weist eine einzelne Dimension namens **Process** auf. Für den angegebenen Zeitstempel werden Metrikwerte für zwei unterschiedliche Prozesse ausgegeben:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, die Diagnoseerweiterung und der InfluxData Telegraf-Agent sind bereits so konfiguriert, dass sie Metrikwerte für den richtigen regionalen Endpunkt ausgeben und alle oben genannten Eigenschaften in jeder Ausgabe enthalten.
>
>

## <a name="custom-metric-definitions"></a>Benutzerdefinierte Metrikdefinitionen
Es ist nicht erforderlich, eine benutzerdefinierte Metrik in Azure Monitor vorab zu definieren, bevor sie ausgegeben wird. Jeder veröffentlichte Metrikdatenpunkt enthält die Informationen zu Namespace, Name und Dimension. Bei der ersten Ausgabe einer benutzerdefinierten Metrik an Azure Monitor wird also automatisch eine Metrikdefinition erstellt. Diese Metrikdefinition ist dann für jede Ressource sichtbar, für die die Metrik über die Metrikdefinitionen ausgegeben wurde.

> [!NOTE]  
> Das Definieren von **Einheiten** für eine benutzerdefinierte Metrik wird von Azure Monitor noch nicht unterstützt.

## <a name="using-custom-metrics"></a>Verwenden benutzerdefinierter Metriken
Nachdem benutzerdefinierte Metriken an Azure Monitor übermittelt wurden, können Sie sie über das Azure-Portal durchsuchen und über die Azure Monitor-REST-APIs abfragen. Sie können auch Warnungen erstellen, damit Sie benachrichtigt werden, wenn bestimmte Bedingungen erfüllt sind.

> [!NOTE]
> Zum Anzeigen von benutzerdefinierten Metriken wird eine Rolle vom Typ „Leser“ oder „Mitwirkender“ benötigt. Siehe [Überwachungsleser](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Durchsuchen Ihrer benutzerdefinierten Metriken über das Azure-Portal
1.    Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2.    Wählen Sie den Bereich **Überwachen** aus.
3.    Klicken Sie auf **Metriken**.
4.    Wählen Sie eine Ressource aus, für die Sie benutzerdefinierte Metriken übermittelt haben.
5.    Wählen Sie den Namespace Ihrer benutzerdefinierten Metrik aus.
6.    Wählen Sie die benutzerdefinierte Metrik aus.

> [!NOTE]
> Weitere Informationen zum Anzeigen von Metriken im Azure-Portal finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](./metrics-getting-started.md).

## <a name="supported-regions"></a>Unterstützte Regionen
Während der öffentlichen Vorschau ist die Möglichkeit, benutzerdefinierte Metriken zu veröffentlichen, nur in einigen Azure-Regionen verfügbar. Das bedeutet, dass Metriken nur für Ressourcen in einer der unterstützten Regionen veröffentlicht werden können. Weitere Informationen zu Azure-Regionen finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/). Der Azure-Regionscode, der in den folgenden Endpunkten verwendet wird, ist nur der Name der Region mit entfernten Leerzeichen. In der folgenden Tabelle sind die unterstützten Azure-Regionen für benutzerdefinierte Metriken aufgeführt. Außerdem werden die zugehörigen Endpunkte aufgeführt, an denen Metriken für Ressourcen in diesen Regionen veröffentlicht werden sollten:

|Azure-Region |Präfix des regionalen Endpunkts|
|---|---|
| Alle öffentlichen Cloudregionen | https://<Azure-Regionscode>.monitoring.azure.com |


## <a name="latency-and-storage-retention"></a>Latenz und Aufbewahrungsdauer im Speicher

Das Hinzufügen einer ganz neuen Metrik oder einer neuen Dimension, die einer Metrik hinzugefügt wird, kann bis zu 2 bis 3 Minuten dauern. Sobald diese im System enthalten ist, sollten die Daten in 99 % der Fälle nach weniger als 30 Sekunden angezeigt werden. 

Wenn Sie eine Metrik löschen oder eine Dimension entfernen, kann es eine Woche bis zu einem Monat dauern, bis die Löschung aus dem System erfolgt.

## <a name="quotas-and-limits"></a>Kontingente und Grenzwerte
Azure Monitor erzwingt die folgenden Nutzungslimits für benutzerdefinierte Metriken:

|Category|Begrenzung|
|---|---|
|Aktive Zeitreihen/Abonnements/Region|50.000|
|Dimensionsschlüssel pro Metrik|10|
|Zeichenkettenlänge für metrische Namespaces, metrische Namen, Dimensionsschlüssel und Dimensionswerte|256 Zeichen|

Eine aktive Zeitreihe ist definiert als eine beliebige eindeutige Kombination aus Metrik, Dimensionsschlüssel oder Dimensionswert, deren Metrikwerte in den letzten 12 Stunden veröffentlicht wurden.

Um das Zeitreihenlimit von 50.000 zu verstehen, betrachten Sie die folgende Metrik:

*Serverantwortzeit* mit den Dimensionen: *Region*, *Abteilung*, *Kunden-ID*

Bei dieser Metrik haben Sie 10 Regionen, 20 Abteilungen und 100 Kunden, die Ihnen 10 x 20 x 100 = 2 000 Zeitreihen bieten. 

Wenn Sie über 100 Regionen, 200 Abteilungen und 2000 Kunden verfügen, sind das 100 x 200 x 2 000 = 40.000.000 Zeitreihen, was allein für diese Metrik weit über dem Grenzwert liegt. 

Auch hier gilt dieser Grenzwert nicht für eine einzelne Metrik. Dies ist die Summe aller dieser Metriken in einem Abonnement und einer Region.  

## <a name="design-limitations-and-considerations"></a>Entwurfseinschränkungen und -aspekte

**Verwenden Sie Application Insights nicht zum Zweck der Überwachung:** Die Application Insights-Telemetrie-Pipeline ist für die Minimierung der Auswirkungen auf die Leistung und die Beschränkung des Netzwerkdatenverkehrs für die Überwachung Ihrer Anwendung optimiert. Daher wird eine Drosselung oder Stichprobenentnahme vorgenommen (nimmt nur einen Prozentsatz Ihrer Telemetriedaten an und ignoriert den Rest), wenn das anfängliche Dataset zu groß wird. Aufgrund dieses Verhaltens können Sie sie nicht für Überwachungszwecke verwenden, da einige Datensätze wahrscheinlich gelöscht werden. 

**Metriken mit einer Variablen im Namen**: Verwenden Sie keine Variable als Teil des Metriknamens. Verwenden Sie stattdessen eine Konstante. Jedes Mal, wenn die Variable ihren Wert ändert, generiert Azure Monitor eine neue Metrik und erreicht schnell die Grenzwerte für die Anzahl der Metriken. Wenn Entwickler eine Variable in den Metriknamen einschließen möchten, wollen sie im Allgemeinen mehrere Zeitreihen innerhalb einer Metrik nachverfolgen und sollten Dimensionen anstelle von Variablenmetriknamen verwenden. 

**Metrikdimensionen mit hoher Kardinalität**: Metriken mit zu vielen gültigen Werten in einer Dimension (eine „hohe Kardinalität“) werden viel wahrscheinlicher den Grenzwert von 50.000 überschreiten. Im Allgemeinen sollten Sie niemals einen sich ständig ändernden Wert in einer Dimension verwenden. Beispielsweise sollte der Zeitstempel NIEMALS eine Dimension sein. Server, Kunde oder Produkt-ID können verwendet werden, aber nur, wenn Sie über eine kleinere Anzahl dieser Typen verfügen. Fragen Sie sich als Test, ob Sie solche Daten in einem Diagramm anzeigen würden.  Wenn Sie über 10 oder vielleicht sogar 100 Server verfügen, kann es hilfreich sein, sie für den Vergleich alle in einem Diagramm anzuzeigen. Wenn Sie jedoch 1 000 haben, wäre das resultierende Diagramm wahrscheinlich schwierig, oder es wäre sogar unlesbar. Am besten behalten Sie weniger als 100 gültige Werte bei. Bis zu 300 ist eine Grauzone.  Wenn Sie diese Anzahl übergehen müssen, verwenden Sie Azure Monitor benutzerdefinierte Protokolle.   

Wenn Sie eine Variable im Namen oder eine Dimension mit hoher Kardinalität haben, kann Folgendes auftreten:
- Metriken werden aufgrund von Drosselung unzuverlässig
- Der Metrik-Explorer funktioniert nicht.
- Warnungen und Benachrichtigungen werden unvorhersehbar
- Die Kosten können unerwartet steigen. Microsoft erhebt keine Gebühren für benutzerdefinierte Metriken mit entsprechenden Dimensionen, während sich dieses Feature in der Public Preview befindet. Sobald die Gebühren später erhoben werden, entstehen jedoch unerwartete Gebühren. Der Plan ist, die Nutzung von Metriken basierend auf der Anzahl der überwachten Zeitreihen und der Anzahl der erfolgten API-Aufrufe in Rechnung zu stellen.

## <a name="next-steps"></a>Nächste Schritte
Verwenden benutzerdefinierter Metriken von verschiedenen Diensten: 
 - [Virtuelle Computer](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [VM-Skalierungsgruppe](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassisch)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Virtueller Linux-Computer mit Telegraf-Agent](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST-API](./metrics-store-custom-rest-api.md)
 - [Cloud Services (klassisch)](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)

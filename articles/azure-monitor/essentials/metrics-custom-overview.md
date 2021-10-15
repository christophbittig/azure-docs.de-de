---
title: Benutzerdefinierte Metriken in Azure Monitor (Vorschau)
description: Hier erfahren Sie mehr über benutzerdefinierte Metriken in Azure Monitor und wie sie modelliert werden.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a63c690f8b742638b73b5624971f5351ed8c6a2f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455055"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Benutzerdefinierte Metriken in Azure Monitor (Vorschau)

Wenn Sie Ressourcen und Anwendungen in Azure bereitstellen, sollten Sie mit dem Erfassen von Telemetriedaten beginnen, um Einblicke in deren Leistung und Integrität zu erhalten. Azure stellt Ihnen einige sofort einsetzbare Metriken zur Verfügung. Diese Metriken werden als [Standard- oder Plattformmetriken](./metrics-supported.md) bezeichnet. Sie sind jedoch begrenzt. 

Möglicherweise möchten Sie einige benutzerdefinierte Leistungsindikatoren oder unternehmensspezifische Metriken sammeln, um eingehendere Erkenntnisse bereitzustellen. Diese *benutzerdefinierten* Metriken können über Ihre Anwendungstelemetrie, einen Agenten, der auf Ihren Azure-Ressourcen läuft, oder sogar ein externes Überwachungssystem gesammelt werden. Sie können dann direkt an Azure Monitor übermittelt werden. Nachdem benutzerdefinierte Metriken in Azure Monitor veröffentlicht wurden, können Sie benutzerdefinierte Metriken für Ihre Azure-Ressourcen und -Anwendungen neben den standardmäßigen Azure-Metriken durchsuchen, abfragen und Warnungen ausgeben.

Azure Monitor benutzerdefinierte Metriken sind derzeit in der öffentlichen Vorschau. 

## <a name="methods-to-send-custom-metrics"></a>Methoden zum Senden benutzerdefinierter Metriken

Benutzerdefinierte Metriken können über verschiedene Methoden an Azure Monitor übermittelt werden:
- Instrumentieren Ihrer Anwendung mit dem Azure Application Insights SDK und Senden benutzerdefinierter Telemetriedaten an Azure Monitor 
- Installieren Sie den Azure Monitor-Agenten (Vorschau) auf Ihrer [Windows- oder Linux-Azure-VM](../agents/azure-monitor-agent-overview.md), und verwenden Sie eine [Datensammlungsregel](../agents/data-collection-rule-azure-monitor-agent.md), um Leistungszähler an Azure Monitor-Metriken zu senden.
- Installieren Sie die Azure-Diagnose-Erweiterung auf Ihrer [Azure VM](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md), [virtuellen Maschine scale set](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), [classic VM](../essentials/collect-custom-metrics-guestos-vm-classic.md) oder [classic cloud service](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md), und senden Sie Leistungszähler an Azure Monitor. 
- Installieren Sie den [InfluxData Telegraf-Agenten](../essentials/collect-custom-metrics-linux-telegraf.md) auf Ihrer Azure Linux VM und senden Sie Metriken mit Hilfe des Azure Monitor Output Plug-ins.
- Senden von benutzerdefinierten Metriken [direkt an die Azure Monitor-REST-API](./metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

## <a name="pricing-model-and-retention"></a>Preismodell und Aufbewahrung

Details dazu, wann die Abrechnung für benutzerdefinierte Metriken und Metrikabfragen aktiviert wird, finden Sie auf der [Azure Monitor Preisseite](https://azure.microsoft.com/pricing/details/monitor/). Zusammenfassend lässt sich sagen, dass es keine Kosten für die Erfassung von Standardmetriken (Plattformmetriken) in einem Azure Monitor-Metrikspeicher gibt, aber für benutzerdefinierte Metriken fallen Kosten an, sobald sie allgemein verfügbar sind. Abfragen an die Metriken-API sind kostenpflichtig.

Benutzerdefinierte Metriken werden [genauso lange wie Plattformmetriken](../essentials/data-platform-metrics.md#retention-of-metrics) aufbewahrt. 

> [!NOTE]  
> Metriken, die über das Application Insights SDK an Azure Monitor gesendet werden, werden als erfasste Protokolldaten in Rechnung gestellt. Sie verursachen nur dann zusätzliche Kosten für Metriken, wenn die Application Insights-Funktion [Alarmierung bei benutzerdefinierten Metrikdimensionen aktivieren](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) ausgewählt wurde. Dieses Kontrollkästchen sendet Daten an die Azure Monitor Metrikdatenbank unter Verwendung der benutzerdefinierten Metrik-API, um komplexere Warnmeldungen zu ermöglichen.  Erfahren Sie mehr über das [Application Insights-Preismodell](../app/pricing.md#pricing-model) und [Preise in Ihrer Region](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-to-send-custom-metrics"></a>Vorgehensweise beim Senden benutzerdefinierter Metriken

Wenn Sie benutzerdefinierte Metriken an Azure Monitor senden, muss jeder Datenpunkt oder Wert, der in den Metriken gemeldet wird, die folgenden Informationen enthalten.

### <a name="authentication"></a>Authentifizierung
Zum Übermitteln von benutzerdefinierten Metriken an Azure Monitor muss die Entität über ein gültiges Azure Active Directory-Token (Azure AD) im **Bearer**-Header der Anforderung verfügen. Unterstützte Wege zum Erwerb eines gültigen Inhaber-Tokens sind:

- [Verwaltete Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) Sie können eine verwaltete Identität verwenden, um Ressourcen die Berechtigung zu erteilen, bestimmte Vorgänge auszuführen. Zum Beispiel erlaubt sie einer Ressource das Ausgeben von Metriken über sich. Einer Ressource bzw. ihrer verwalteten Identität können **Überwachung von Metriken Publisher** Berechtigungen für eine andere Ressource erhalten. Mit dieser Berechtigung kann die verwaltete Identität auch Metriken für andere Ressourcen ausgeben.
- [Azure AD-Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md). In diesem Szenario können einer AAD-Anwendung oder einem Dienst die Berechtigungen zum Ausgeben von Metriken zu einer Azure-Ressource gewährt werden. Azure Monitor überprüft das Anwendungstoken mithilfe von öffentlichen Azure AD-Schlüsseln, um die Anforderung zu authentifizieren. Die vorhandene Rolle **Überwachungsmetriken veröffentlichen** verfügt bereits über diese Berechtigung. Sie ist im Azure-Portal verfügbar. 

  Dem Dienstprinzipal kann die Rolle **Überwachungsmetriken veröffentlichen** im erforderlichen Gültigkeitsbereich erteilt werden, je nachdem, für welche Ressourcen er benutzerdefinierte Metriken ausgibt. Dabei kann es sich um ein Abonnement, eine Ressourcengruppe oder eine bestimmte Ressource handeln.

> [!TIP]  
> Wenn Sie ein Azure AD-Token zur Ausgabe von benutzerdefinierten Metriken Anforderung, stellen Sie sicher, dass die Zielgruppe oder Ressource, für die das Token angefordert wird, `https://monitoring.azure.com/` ist. Achten Sie darauf, dass der Schrägstrich am Ende enthalten ist.

### <a name="subject"></a>Subject
Die Eigenschaft subject erfasst die Azure-Ressourcen-ID, für die eine benutzerdefinierte Metrik gemeldet wird. Diese Information wird in der URL des API-Aufrufs kodiert. Jede API kann Metrikwerte nur für eine einzige Azure-Ressource übermitteln.

> [!NOTE]  
> Sie können keine benutzerdefinierten Metriken für die Ressourcen-ID einer Ressourcengruppe oder eines Abonnements ausgeben.

### <a name="region"></a>Region
Die Eigenschaft Region erfasst die Azure-Region, in der die Ressource, für die Sie Metriken übermitteln, bereitgestellt ist. Die Metriken müssen an denselben regionalen Endpunkt von Azure Monitor gesendet werden wie die Region, in der die Ressource bereitgestellt ist. Benutzerdefinierte Metriken für eine VM, die in der Region „USA, Westen“ bereitgestellt wurde, müssen z.B. an den regionalen Endpunkt „WestUS“ von Azure Monitor gesendet werden. Die Regionsinformationen sind auch in der URL des API-Aufrufs codiert.

> [!NOTE]  
> In der öffentlichen Vorschau sind benutzerdefinierte Metriken nur in einer Teilmenge der Azure-Regionen verfügbar. Eine Liste der unterstützten Regionen ist in einem [späteren Abschnitt dieses Artikels](#supported-regions) dokumentiert.

### <a name="timestamp"></a>Timestamp
Jeder Datenpunkt, der an Azure Monitor gesendet wird, muss mit einem Zeitstempel gekennzeichnet sein. Dieser Zeitstempel erfasst das Datum und die Uhrzeit, zu der die Metrik gemessen oder erfasst wurde. Azure Monitor akzeptiert metrische Daten mit Zeitstempeln, die bis zu 20 Minuten in der Vergangenheit und bis zu 5 Minuten in der Zukunft liegen. Der Zeitstempel muss im ISO 8601-Format vorliegen.

### <a name="namespace"></a>Namespace
Namespaces sind eine Möglichkeit, ähnliche Metriken zu kategorisieren oder zu gruppieren. Namespaces ermöglichen es Ihnen, Gruppen von Metriken zu isolieren, die unterschiedliche Erkenntnisse oder Leistungsindikatoren erfassen. Sie könnten z.B. über den Namespace **contosomemorymetrics** verfügen, der Metriken zur Arbeitsspeichernutzung verfolgt, um ein Profile Ihrer App zu erstellen. Ein anderer Namespace mit dem Namen **contosoapptransaction** könnte alle Metriken über Benutzertransaktionen in Ihrer Anwendung verfolgen.

### <a name="name"></a>Name
Die Eigenschaft name ist der Name der Metrik, über die berichtet wird. Normalerweise ist der Name ausreichend beschreibend, um zu erkennen, was gemessen wird. Ein Beispiel ist eine Metrik, mit der sich die Anzahl der von einer VM verwendeten Speicherbytes messen lässt. Der Metrikname könnte beispielsweise **Verwendete Arbeitsspeicherbytes** lauten.

### <a name="dimension-keys"></a>Dimensionsschlüssel
Eine Dimension ist ein Schlüssel/Wert-Paar, mit dem zusätzliche Merkmale der erfassten Metrik beschrieben werden. Die zusätzlichen Merkmale ermöglichen die Erfassung weiterer Informationen zur Metrik und damit umfangreichere Erkenntnisse. 

Die Metrik **Verwendete Arbeitsspeicherbytes** könnte z.B. einen Dimensionsschlüssel namens **Prozess** verwenden, der erfasst, wie viele Bytes des Arbeitsspeichers pro Prozess auf einer VM belegt werden. Mithilfe dieses Schlüssels können Sie die Metrik filtern, um zu sehen, wie viele speicherspezifische Prozesse verwendet werden, oder um die fünf Prozesse mit der höchsten Speicherauslastung zu identifizieren.

Dimensionen sind optional, und nicht alle Metriken haben Dimensionen. Eine benutzerdefinierte Metrik kann bis zu 10 Dimensionen umfassen.

### <a name="dimension-values"></a>Dimensionswerte
Wenn Sie einen metrischen Datenpunkt melden, gibt es für jeden Dimensionsschlüssel der gemeldeten Metrik einen entsprechenden Dimensionswert. Sie möchten beispielsweise den von ContosoApp auf Ihrer VM verwendeten Arbeitsspeicher melden:

* Der Metrikname wäre **Verwendete Arbeitsspeicherbytes**.
* Der Dimensionsschlüssel wäre **Prozess**.
* Der Dimensionswert wäre **ContosoApp.exe**.

Wenn Sie einen Metrik-Wert veröffentlichen, können Sie nur einen einzigen Dimensionswert pro Dimensionsschlüssel angeben. Wenn Sie dieselbe Speicherauslastung für mehrere Prozesse auf der VM erfassen, können Sie mehrere Metrikwerte für diesen Zeitstempel melden. Jeder Metrikwert würde einen anderen Dimensionswert für den Dimensionsschlüssel **Prozess** angeben.

Obwohl Bemaßungen optional sind, sind die entsprechenden Bemaßungswerte obligatorisch, wenn ein Metrik-Beitrag Bemaßungsschlüssel definiert.

### <a name="metric-values"></a>Metrikwerte
Azure Monitor speichert alle Metriken mit einer Granularität von 1 Minute. Während einer bestimmten Minute muss eine Metrik möglicherweise mehrmals abgetastet werden. Ein Beispiel ist die CPU-Auslastung. Oder eine Metrik muss für viele einzelne Ereignisse gemessen werden, wie z. B. die Latenzzeit bei Anmeldetransaktionen. 

Um die Anzahl der Rohwerte zu begrenzen, die Sie in Azure Monitor ausgeben und für die Sie bezahlen müssen, können Sie die Werte lokal voraggregieren und ausgeben:

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

Wenn Ihre Anwendung nicht lokal voraggregieren kann und jede diskrete Probe oder jedes Ereignis sofort nach der Erfassung ausgeben muss, können Sie die Rohmesswerte ausgeben. So würden Sie z.B. jedes Mal, wenn in Ihrer App eine Anmeldetransaktion stattgefunden hat, eine Metrik mit nur einer einzigen Messung an Azure Monitor übermitteln. Für eine Anmeldetransaktion, die 12 Millisekunden dauerte, würde die Veröffentlichung der Metrik also wie folgt aussehen:
* Min: 12
* Max: 12
* Summe: 12
* Anzahl: 1

Mit diesem Verfahren können Sie während einer bestimmten Minute mehrere Werte für dieselbe Kombination aus Metrik und Dimension ausgeben. Azure Monitor nimmt dann alle Rohwerte, die für eine bestimmte Minute ausgegeben werden, und aggregiert sie.

### <a name="sample-custom-metric-publication"></a>Beispiel einer benutzerdefinierten metrischen Veröffentlichung
Im folgenden Beispiel erstellen Sie eine benutzerdefinierte Metrik namens **Memory Bytes in Use** unter dem Metriknamespace **Memory Profile** für einen virtuellen Computer. Die Metrik weist eine einzelne Dimension namens **Process** auf. Für den Zeitstempel werden metrische Werte für zwei Prozesse ausgegeben.

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

## <a name="custom-metric-definitions"></a>Benutzerdefinierte Metrikdefinitionen
Es ist nicht erforderlich, eine benutzerdefinierte Metrik in Azure Monitor vorab zu definieren, bevor sie ausgegeben wird. Jeder veröffentlichte Metrikdatenpunkt enthält die Informationen zu Namespace, Name und Dimension. Bei der ersten Ausgabe einer benutzerdefinierten Metrik an Azure Monitor wird also automatisch eine Metrikdefinition erstellt. Diese Metrikdefinition ist dann für jede Ressource auffindbar, für die diese Metrik über die Metrikdefinitionen ausgegeben wird.

> [!NOTE]  
> Das Definieren von **Einheiten** für eine benutzerdefinierte Metrik wird von Azure Monitor noch nicht unterstützt.

## <a name="using-custom-metrics"></a>Verwenden benutzerdefinierter Metriken
Nachdem benutzerdefinierte Metriken an Azure Monitor übermittelt wurden, können Sie diese über das Azure-Portal durchsuchen und über die Azure Monitor REST APIs abfragen. Sie können auch Warnungen erstellen, damit Sie benachrichtigt werden, wenn bestimmte Bedingungen erfüllt sind.

> [!NOTE]
> Um benutzerdefinierte Metriken anzeigen zu können, müssen Sie eine Leser- oder Beitragszahlerrolle haben. Siehe [Überwachungsleser](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Durchsuchen Ihrer benutzerdefinierten Metriken über das Azure-Portal
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Wählen Sie den Bereich **Überwachen** aus.
3. Klicken Sie auf **Metriken**.
4. Wählen Sie eine Ressource aus, für die Sie benutzerdefinierte Metriken erstellt haben.
5. Wählen Sie den Namespace Ihrer benutzerdefinierten Metrik aus.
6. Wählen Sie die benutzerdefinierte Metrik aus.

Weitere Informationen zur Anzeige von Metriken im Azure-Portal finden Sie unter [Einstieg in den Azure Metrics Explorer](./metrics-getting-started.md).

## <a name="supported-regions"></a>Unterstützte Regionen
Während der öffentlichen Vorschau ist die Möglichkeit, benutzerdefinierte Metriken zu veröffentlichen, nur in einigen Azure-Regionen verfügbar. Das bedeutet, dass Metriken nur für Ressourcen in einer der unterstützten Regionen veröffentlicht werden können. Weitere Informationen zu Azure-Regionen finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/). 

In der folgenden Tabelle sind die unterstützten Azure-Regionen für benutzerdefinierte Metriken aufgeführt. Sie listet auch die entsprechenden Endpunkte auf, an die Metriken für Ressourcen in diesen Regionen veröffentlicht werden sollten. Der Code der Azure-Region, der im Endpunktpräfix verwendet wird, ist lediglich der Name der Region, wobei Leerzeichen entfernt wurden. 

|Azure-Region |Präfix des regionalen Endpunkts|
|---|---|
| Alle öffentlichen Cloudregionen | https://<Azure-Regionscode>.monitoring.azure.com |

## <a name="latency-and-storage-retention"></a>Latenz und Aufbewahrungsdauer im Speicher

Es kann bis zu 3 Minuten dauern, bis eine neu hinzugefügte Metrik oder eine neu hinzugefügte Dimension zu einer Metrik erscheint. Nachdem die Daten im System sind, sollten sie in 99 Prozent der Fälle in weniger als 30 Sekunden erscheinen. 

Wenn Sie eine Metrik löschen oder eine Dimension entfernen, kann es eine Woche bis zu einem Monat dauern, bis die Löschung aus dem System erfolgt.

## <a name="quotas-and-limits"></a>Kontingente und Grenzwerte
Azure Monitor erzwingt die folgenden Nutzungslimits für benutzerdefinierte Metriken:

|Category|Begrenzung|
|---|---|
|Gesamtzahl der aktiven Zeitreihen in einem Abonnement für alle Regionen, in denen Sie die Daten bereitgestellt haben|50.000|
|Dimensionsschlüssel pro Metrik|10|
|Zeichenkettenlänge für metrische Namespaces, metrische Namen, Dimensionsschlüssel und Dimensionswerte|256 Zeichen|

Eine aktive Zeitreihe ist definiert als eine beliebige eindeutige Kombination aus Metrik, Dimensionsschlüssel oder Dimensionswert, deren Metrikwerte in den letzten 12 Stunden veröffentlicht wurden.

Um die Grenze von 50.000 bei Zeitreihen zu verstehen, betrachten Sie die folgende Metrik:

> *Serverantwortzeit* mit den Dimensionen: *Region*, *Abteilung*, *Kunden-ID*

Wenn Sie 10 Regionen, 20 Abteilungen und 100 Kunden haben, erhalten Sie mit dieser Metrik 10 x 20 x 100 = 2.000 Zeitreihen. 

Wenn Sie 100 Regionen, 200 Abteilungen und 2.000 Kunden haben, ergibt das 100 x 200 x 2.000 = 40 Millionen Zeitreihen, was allein für diese Metrik weit über dem Limit liegt. 

Auch hier gilt dieser Grenzwert nicht für eine einzelne Metrik. Es handelt sich um die Summe aller derartigen Metriken für ein Abonnement und eine Region.  

## <a name="design-limitations-and-considerations"></a>Entwurfseinschränkungen und -aspekte

**Verwendung von Application Insights zum Zweck des Audits**. Die Telemetrie-Pipeline von Application Insights ist für die Minimierung der Leistungsauswirkungen und die Begrenzung des Netzwerkverkehrs bei der Überwachung Ihrer Anwendung optimiert. Daher wird eine Drosselung oder Stichprobenentnahme vorgenommen (nimmt nur einen Prozentsatz Ihrer Telemetriedaten an und ignoriert den Rest), wenn das anfängliche Dataset zu groß wird. Aufgrund dieses Verhaltens können Sie es nicht für Prüfungszwecke verwenden, da einige Datensätze wahrscheinlich gelöscht werden. 

**Metriken mit einer Variable im Namen**. Verwenden Sie keine Variable als Teil des Metriknamens. Verwenden Sie stattdessen eine Konstante. Jedes Mal, wenn die Variable ihren Wert ändert, wird Azure Monitor eine neue Metrik generieren. Azure Monitor stößt dann schnell an das Limit für die Anzahl der Metriken. Im Allgemeinen, wenn die Entwickler eine Variable in den Metriknamen aufnehmen wollen, wollen sie wirklich mehrere Zeitreihen innerhalb einer Metrik verfolgen und sollten Dimensionen anstelle von variablen Metriknamen verwenden. 

Dimensionen **Metriken mit hoher Kardinalität**. Bei Metriken mit zu vielen gültigen Werten in einer Dimension (*hohe Kardinalität*) ist es sehr viel wahrscheinlicher, dass die 50.000er Grenze erreicht wird. Im Allgemeinen sollten Sie niemals einen sich ständig ändernden Wert in einer Dimension verwenden. Der Zeitstempel zum Beispiel sollte nie eine Dimension sein. Sie können Server-, Kunden- oder Produkt-IDs verwenden, aber nur, wenn Sie eine geringere Anzahl von jedem dieser Typen haben. 

Fragen Sie sich als Test, ob Sie solche Daten in einem Diagramm anzeigen würden. Wenn Sie über 10 oder vielleicht sogar 100 Server verfügen, kann es hilfreich sein, sie für den Vergleich alle in einem Diagramm anzuzeigen. Wenn Sie jedoch 1.000 Werte haben, wäre das resultierende Diagramm wahrscheinlich schwierig oder unmöglich zu lesen. 

Am besten ist es, die Zahl der gültigen Werte auf weniger als 100 zu beschränken. Bis zu 300 ist eine Grauzone. Wenn Sie diese Anzahl übergehen müssen, verwenden Sie Azure Monitor benutzerdefinierte Protokolle.   

Wenn Sie eine Variable im Namen oder eine Dimension mit hoher Kardinalität haben, kann Folgendes passieren:
- Die Metriken werden aufgrund von Drosselung unzuverlässig.
- Der Metrics Explorer funktioniert nicht mehr.
- Alerting und Benachrichtigungen werden unvorhersehbar.
- Die Kosten können unerwartet steigen. Microsoft erhebt keine Gebühren für benutzerdefinierte Metriken mit Dimensionen, solange sich diese Funktion in der öffentlichen Vorschau befindet. Wenn jedoch in Zukunft Gebühren anfallen, werden Sie mit unerwarteten Kosten konfrontiert. Es ist geplant, den Verbrauch von Metriken auf der Grundlage der Anzahl der überwachten Zeitreihen und der Anzahl der API-Aufrufe zu berechnen.

## <a name="next-steps"></a>Nächste Schritte
Verwenden Sie benutzerdefinierte Metriken aus verschiedenen Diensten: 
 - [Virtueller Computer](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [VM-Skalierungsgruppe](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure virtuelle Maschine (klassisch)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuelle Linux-Maschine mit Telegraf-Agent](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST-API](./metrics-store-custom-rest-api.md)
 - [Klassischer Cloud-Dienst](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)

---
title: Parser für das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie KQL-Funktionen als Abfragezeitparser verwendet werden, um das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) zu implementieren.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 43ec5162b0dc1bfd9def8afea6a36c7899483a55
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520292"
---
# <a name="advanced-siem-information-model-asim-parsers-public-preview"></a>Parser für das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) (öffentliche Vorschau)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In Microsoft Sentinel erfolgen das Parsing und die [Normalisierung](normalization.md) zur Abfragezeit. Parser werden als [benutzerdefinierte KQL-Funktionen](/azure/data-explorer/kusto/query/functions/user-defined-functions) erstellt, die Daten in vorhandenen Tabellen (wie etwa **CommonSecurityLog**, benutzerdefinierte Protokolltabellen, Syslog) in das normalisierte Schema transformieren. Sobald der Parser als Arbeitsbereichsfunktion gespeichert ist, kann er wie jede Microsoft Sentinel-Tabelle verwendet werden.

> [!TIP]
> Sehen Sie sich auch das [ausführliche Webinar zu normalisierten Parsern und Inhalten in Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) oder die [Folien](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM) an. Weitere Informationen finden Sie in den [nächsten Schritten](#next-steps).
>

> [!IMPORTANT]
> ASIM befindet sich derzeit in der Vorschauphase. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="source-agnostic-and-source-specific-parsers"></a>Quellenunabhängige und quellenspezifische Parser

ASIM umfasst zwei Ebenen von Parsern: **quellenunabhängige** und **quellenspezifische** Parser:

### <a name="source-agnostic-parsers"></a>Quellenunabhängige Parser

Ein **quellenunabhängiger Parser** kombiniert alle nach demselben Schema normalisierten Quellen und kann zur Abfrage aller Quellen anhand normalisierter Felder verwendet werden. Der Name eines quellenspezifischen Parsers entspricht `im<schema>`, wobei `<schema>` für das spezifische Schema steht, für das er verwendet wird.

In der folgenden Abfrage wird beispielsweise der quellenunabhängige DNS-Parser verwendet, um DNS-Ereignisse anhand der normalisierten Felder `ResponseCodeName`, `SrcIpAddr` und `TimeGenerated` abzufragen:

```kusto
imDns
  | where isnotempty(ResponseCodeName)
  | where ResponseCodeName =~ "NXDOMAIN"
  | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
```

Ein quellenunabhängiger Parser kann mithilfe des KQL-Operators `union` mehrere quellenspezifische normalisierte Parser kombinieren. Der Name eines quellenspezifischen normalisierten Parsers entspricht `vim<schema><vendor><product>`. Daher sieht der Parser `imDns` wie folgt aus:

```kusto
union isfuzzy=true
vimDnsEmpty,
vimDnsCiscoUmbrella,
vimDnsInfobloxNIOS,
vimDnsMicrosoftOMS
```

> [!NOTE]
> Bei Verwendung der quellenagnostischen ASIM-Parser, die auf der Seite **Logs** mit `im` beginnen, ist der Zeitbereichswähler auf `custom` eingestellt. Sie können den Zeitbereich immer noch selbst festlegen. Alternativ können Sie den Zeitbereich mit Parserparametern angeben.
>
> Alternativ dazu können Sie einen `ASim`-Parser verwenden, der keine Parameter unterstützt und auch die Zeitspannenauswahl nicht standardmäßig auf `custom` setzt.
>

### <a name="source-specific-parsers"></a>Quellenlspezifische Parser

Durch Hinzufügen **quellenspezifischer** normalisierter Parser zu dem quellenunabhängigen Parser können Sie benutzerdefinierte Quellen in integrierten Abfragen einschließen, bei denen der quellenunabhängige Parser verwendet wird.

Mit quellenspezifischen Parsern können Sie sofortigen Nutzen aus den integrierten Inhalten ziehen, z. B. Analysen, Arbeitsmappen, Erkenntnisse für Ihre benutzerdefinierten Daten.

Die quellenspezifischen Parser können auch unabhängig voneinander verwendet werden. In einer Infoblox-spezifischen Arbeitsmappe können Sie beispielsweise den Parser `vimDnsInfobloxNIOS` verwenden.

## <a name="optimizing-parsing-using-parameters"></a><a name="optimized-parsers"></a>Optimierung des Parsings mit Hilfe von Parametern

Die Verwendung von Parsern kann die Abfrageleistung beeinträchtigen, vor allem weil die Ergebnisse nach dem Parsen gefiltert werden müssen. Aus diesem Grund verfügen viele Parser über optionale Filterparameter, die es Ihnen ermöglichen, vor dem Parsen zu filtern und die Abfrageleistung zu verbessern. Zusammen mit der Abfrageoptimierung und der Vorfilterung bieten ASIM-Parser oft eine bessere Leistung, als wenn sie überhaupt keine Normalisierung verwenden.

Filterparameter können verwendet werden, indem beim Aufruf des Parsers ein oder mehrere benannte Parameter angegeben werden. Der folgende Abfragestart stellt zum Beispiel sicher, dass nur DNS-Abfragen für nicht existierende Domänen zurückgegeben werden:

```kusto
imDns(responsecodename='NXDOMAIN')
```

Das vorherige Beispiel ähnelt der folgenden Abfrage, ist aber wesentlich effizienter.

```kusto
imDns | where ResponseCodeName == 'NXDOMAIN'
```

Jedes Schema verfügt über einen Standardsatz von Filterparametern, die in der Schemadokumentation dokumentiert sind. Die Filterparameter sind völlig optional und werden derzeit nur für das DNS-Schema vollständig unterstützt. Andere Schemata unterstützen Standard-Filterparameter ohne Optimierung der Vorfilterung.

## <a name="writing-source-specific-parsers"></a>Quellenspezifische Parser

Ein Parser ist eine KQL-Abfrage, die als Arbeitsbereichsfunktion gespeichert wird. Nach dem Speichern kann er wie integrierte Tabellen verwendet werden. Die Parserabfrage umfasst folgende Teile:

**Filterung** > **Analyse** > **Vorbereitung von Feldern**

### <a name="filtering"></a>Filterung

#### <a name="filtering-the-relevant-records"></a>Filterung der relevanten Datensätze

In vielen Fällen enthält eine Tabelle mehrere Ereignistypen. Zum Beispiel:
* Die Syslog-Tabelle enthält Daten aus mehreren Quellen.
* Benutzerdefinierte Tabellen können Informationen aus einer einzelnen Quelle enthalten, die mehrere Ereignistypen umfasst und für verschiedene Schemas geeignet ist.

Daher sollte ein Parser zunächst nur die Datensätze filtern, die für das Zielschema relevant sind.

Die Filterung in KQL erfolgt mit dem Operator `where`. Beispiel: Das **Sysmon-Ereignis 1** meldet die Prozesserstellung und sollte mit dem Schema **ProcessEvent** normalisiert werden. Das **Sysmon-Ereignis 1** ist in der Tabelle `Event` enthalten. Folgender Filter sollte verwendet werden:

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

#### <a name="filtering-based-on-parser-parameters"></a>Filtern anhand von Parser-Parametern

Wenn Sie [parametrisierte Parser](#optimized-parsers) verwenden, stellen Sie sicher, dass Ihr Parser die Filterparameter für das entsprechende Schema akzeptiert, wie im Referenzartikel für dieses Schema dokumentiert.

Die Funktion Artikel ist für jedes Schema identisch. Zum Beispiel für die DNS-Abfrage parametrisierte Parser-Signatur:

```kusto
let DNSQuery_MS=(
    starttime:datetime=datetime(null), 
    endtime:datetime=datetime(null), 
    srcipaddr:string='*', 
    domain_has_any:dynamic=dynamic([]),
    responsecodename:string='*', 
    dnsresponsename:string='*', 
    response_has_any_prefix:dynamic=dynamic([]),
    eventtype:string='lookup'
    )
```

Fügen Sie Ihre Filter auf der Grundlage Ihrer Parameterwerte hinzu. Wenn Sie filtern, stellen Sie sicher, dass Sie:

- **Filtern Sie vor dem Parsen anhand der physischen Felder**. Wenn die gefilterten Ergebnisse nicht genau genug sind, wiederholen Sie den Test nach dem Parsen, um eine Feinabstimmung Ihrer Ergebnisse vorzunehmen. Weitere Informationen finden Sie unter ["Optimierung der Filterung"](#optimization).
 - **Nicht filtern, wenn der Parameter nicht definiert ist und noch den Standardwert hat**. Die folgenden Beispiele zeigen, wie die Filterung für einen String-Parameter, dessen Standardwert normalerweise '\*' ist, und für einen Listenparameter, dessen Standardwert normalerweise eine leere Liste ist, implementiert wird.

``` kusto
srcipaddr=='*' or ClientIP==srcipaddr
array_length(domain_has_any) == 0 or Name has_any (domain_has_any)
```

> [!TIP]
> Ein bereits vorhandener Parser desselben Typs ist ein hervorragender Ausgangspunkt für das Hinzufügen der Parameterfilterung.
>

#### <a name="filtering-optimization"></a><a name="optimization"></a>Filterungsoptimierung


Beachten Sie die folgenden Empfehlungen für die Filterung, um die Leistungsfähigkeit des Parsers zu gewährleisten:

-   Führen Sie die Filterung immer nach integrierten statt nach analysierten Feldern durch. Auch wenn es manchmal einfacher ist, anhand von analysierten Feldern zu filtern, hat dies doch erhebliche Auswirkungen auf die Leistung.
-   Verwenden Sie Operatoren, die optimale Leistung ermöglichen, insbesondere `==`, `has` und `startswith`. Auch die Verwendung von Operatoren wie `contains` oder `matches regex` wirkt sich erheblich auf die Leistung aus.

Die Empfehlungen für die Filterung zur Leistungsoptimierung lassen sich nicht immer einfach umsetzen. Beispielsweise ist die Verwendung von `has` weniger genau als die von `contains`. In anderen Fällen ist der Abgleich des integrierten Felds, z. B. `SyslogMessage`, weniger genau als der Vergleich eines extrahierten Felds, z. B. `DvcAction`. In diesen Fällen empfiehlt es sich, für ein integriertes Feld dennoch eine Vorabfilterung mithilfe eines leistungsoptimierenden Operators durchzuführen und die Filterung nach der Analyse mit genaueren Bedingungen erneut durchzuführen.

Ein Beispiel dafür finden Sie im folgenden [ Infoblox DNS ](https://aka.ms/AzSentinelInfobloxParser) Parser-Schnipsel. Der Parser überprüft zunächst, ob das SyslogMessage-Feld den Begriff `client` enthält (`has`). Der Begriff kann jedoch auch an einer anderen Stelle der Meldung verwendet werden. Daher überprüft der Parser nach dem Analysieren des Felds `Log_Type` erneut, ob der Begriff `client` tatsächlich der Wert des Felds war.

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> Parser sollten nicht nach der Zeit filtern, da die Abfrage, die den Parser verwendet, bereits nach der Zeit filtert.
>

### <a name="parsing"></a>Analyse

Nachdem bei der Abfrage die relevanten Datensätze ausgewählt wurden, müssen sie möglicherweise analysiert werden. Normalerweise ist eine Analyse erforderlich, wenn ein Großteil der Ereignisinformationen in einem einzelnen Textfeld übermittelt wird.

Die KQL-Operatoren, mit denen die Analyse durchgeführt wird, sind nachfolgend nach ihrer Leistungsoptimierung sortiert aufgeführt. Der erste Operator bietet die am besten optimierte Leistung, der letzte die am wenigsten optimierte Leistung.

|Operator  |BESCHREIBUNG  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    Analysiert eine Zeichenfolge von Werten, die durch ein Trennzeichen getrennt sind     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     Analysiert eine Zeichenfolge von Werten, die als CSV-Zeile (mit durch Trennzeichen getrennten Werten) formatiert sind    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    Analysiert mehrere Werte aus einer beliebigen Zeichenfolge anhand eines Musters, das ein vereinfachtes Muster mit besserer Leistung oder ein regulärer Ausdruck sein kann     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | Analysiert einzelne Werte aus einer beliebigen Zeichenfolge anhand eines regulären Ausdrucks. `extract_all` hat eine ähnliche Leistung wie `parse`, wenn bei letzterem ein regulärer Ausdruck verwendet wird.        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    Extrahiert einen einzelnen Wert aus einer beliebigen Zeichenfolge anhand eines regulären Ausdrucks. <br><br>Die Verwendung von `extract` ermöglicht eine bessere Leistung als die von `parse` oder `extract_all`, wenn ein einzelner Wert abgefragt werden soll. Die Verwendung mehrerer Aktivierungen von `extract` für dieselbe Quellenzeichenfolge ist jedoch deutlich weniger effizient als eine einzige Aktivierung von `parse` oder `extract_all` und sollte daher vermieden werden.      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | Analysiert die Werte in einer Zeichenfolge, die als JSON-Code formatiert ist. Wenn nur wenige Werte aus dem JSON-Code benötigt werden, bietet die Verwendung von `parse`, `extract` oder `extract_all` eine bessere Leistung.        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    Analysiert die Werte in einer Zeichenfolge, die als XML-Code formatiert ist. Wenn nur wenige Werte aus dem XML-Code benötigt werden, bietet die Verwendung von `parse`, `extract` oder `extract_all` eine bessere Leistung.     |
| | |

Neben der Analyse der Zeichenfolge kann in der Analysephase eine weitere Verarbeitung der ursprünglichen Werte erforderlich sein, beispielsweise:

- **Formatierung und Typkonvertierung:** Das Quellfeld muss nach der Extraktion möglicherweise so formatiert werden, dass es dem Zielschemafeld entspricht. Beispielsweise müssen Sie möglicherweise eine Zeichenfolge, die Datum und Uhrzeit darstellt, in ein datetime-Feld konvertieren.     In diesen Fällen sind Funktionen wie `todatetime` und `tohex` hilfreich.

- **Wertsuche:** Der Wert des Quellfelds muss nach der Extraktion möglicherweise der für das Zielschemafeld angegebenen Gruppe von Werten zugeordnet werden. Einige Quellen melden z. B. numerische DNS-Antwortcodes, während das Schema die allgemeineren Textantwortcodes vorschreibt. Die Funktionen `iff` und `case` können hilfreich sein, um einige Werte zuzuordnen.

    Beispielsweise weist der Microsoft DNS-Parser das Feld `EventResult` basierend auf der Ereignis-ID und dem Antwortcode mithilfe einer `iff`-Anweisung wie folgt zu:

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    Verwenden Sie für mehrere Werte `datatable` und `lookup`, wie im gleichen DNS-Parser gezeigt:

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName,
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use',
         'Unassigned')
    ```

> [!NOTE]
> Die Transformation erlaubt es nicht, nur `lookup` zu verwenden, da mehrere Werte auf `Reserved for Private Use` oder `Unassigned` abgebildet werden, und daher verwendet die Abfrage sowohl Lookup als auch Fall.
> Dennoch ist die Abfrage immer noch wesentlich effizienter als die Verwendung von `case` für alle Werte.
>

### <a name="prepare-fields-in-the-result-set"></a>Vorbereiten von Feldern im Resultset

Der Parser muss die Felder der Ergebnismenge vorbereiten, um sicherzustellen, dass die normalisierten Felder verwendet werden. Als Leitfaden gilt, dass ursprüngliche Felder, die nicht normalisiert sind, nicht aus dem Resultset entfernt werden sollten, es sei denn, es gibt einen zwingenden Grund dafür, z. B. wenn sie zu Unklarheiten führen.

Zur Vorbereitung von Feldern werden die folgenden KQL-Operatoren verwendet:

|Operator  | BESCHREIBUNG  | Verwendung in einem Parser  |
|---------|---------|---------|
|**extend**     | Erstellt berechnete Felder und fügt sie dem Datensatz hinzu.        |  `Extend` wird verwendet, wenn die normalisierten Felder aus den ursprünglichen Daten analysiert oder transformiert werden. Weitere Informationen finden Sie im Beispiel im Abschnitt [Analyse](#parsing) weiter oben.     |
|**project-rename**     | Benennt Felder um.        |     Wenn ein Feld im aktuellen Ereignis existiert und nur umbenannt werden muss, verwenden Sie `project-rename`. <br><br>Das umbenannte Feld verhält sich weiterhin wie ein integriertes Feld, und Vorgänge für das Feld weisen eine wesentlich bessere Leistung auf.   |
|**project-away**     |      Entfernt Felder.   |Verwenden Sie `project-away` für spezifische Felder, die aus dem Resultset entfernt werden sollen.         |
|**project**     |  Wählt Felder aus, die vorher existierten oder als Teil der Anweisung erstellt wurden. Entfernt alle anderen Felder.       | Wird nicht für die Verwendung in einem Parser empfohlen, da der Parser keine anderen nicht normalisierten Felder entfernen soll. <br><br>Wenn Sie spezifische Felder entfernen möchten, z. B. temporäre bei der Analyse verwendete Werte, verwenden Sie `project-away`, um sie aus den Ergebnissen zu entfernen.      |
| | | |

### <a name="handle-parsing-variants"></a>Verarbeiten von Analysevarianten

In vielen Fällen enthalten Ereignisse in einem Ereignisstream Varianten, die eine andere Analyselogik erfordern.

Es ist oft verlockend, einen Parser aus verschiedenen Subparsern aufzubauen, die jeweils eine andere Variante des Ereignisses behandeln, die eine andere Parsing-Logik erfordert. Diese Unterparser, die jeweils eine Abfrage sind, werden dann mit dem Operator `union` vereinheitlicht. Dieser Ansatz ist zwar praktisch, wird jedoch *nicht* empfohlen, da die Leistung des Parsers erheblich beeinträchtigt wird.

Beachten Sie bei der Verarbeitung von Varianten folgende Leitfäden:

|Szenario  |Verarbeitung  |
|---------|---------|
|Die verschiedenen Varianten stellen *verschiedene* Ereignistypen dar, die häufig verschiedenen Schemas zugeordnet sind.     |  Verwenden Sie separate Parser.       |
|Die verschiedenen Varianten stellen den *gleichen* Ereignistyp dar, sind aber unterschiedlich strukturiert.     |   Wenn die Varianten bekannt sind, z. B. wenn eine Methode zur Unterscheidung der Ereignisse vor der Analyse vorhanden ist, verwenden Sie den Operator `case`, um den richtigen Operator `extract_all` für die Ausführung und Feldzuordnung auszuwählen, wie im Parser [Infoblox DNS](https://aka.ms/AzSentinelInfobloxParser) veranschaulicht.      |
|Wenn `union` unvermeidbar ist     |  Wenn die Verwendung von `union` unvermeidbar ist, sind folgende Leitfäden zu beachten:<br><br>- Führen Sie mithilfe integrierter Felder eine Vorfilterung in jeder der Unterabfragen durch. <br>- Stellen Sie sicher, dass sich die Filter gegenseitig ausschließen. <br>- Analysieren Sie weniger wichtige Informationen ggf. nicht, um so die Anzahl der Unterabfragen zu reduzieren.       |
| | |

## <a name="add-your-parser-to-the-schema-source-agnostic-parser"></a><a name="include"></a>Fügen Sie Ihren Parser dem quellenunabhängigen Schema-Parser hinzu

Durch die Normalisierung können Sie eigene Inhalte und integrierte Inhalte mit Ihren benutzerdefinierten Daten verwenden.

Wenn Sie beispielsweise einen benutzerdefinierten Connector haben, der DNS-Abfrageaktivitätsprotokolle empfängt, können Sie sicherstellen, dass die DNS-Abfrageaktivitätsprotokolle alle normalisierten DNS-Inhalte nutzen.

Ändern Sie dazu den entsprechenden quellenagnostischen Parser so, dass er den von Ihnen erstellten quellenspezifischen Parser enthält. Ändern Sie beispielsweise den quellenagnostischen Parser `imDns` so, dass er Ihren Parser einschließt, indem Sie Ihren Parser in die Liste der Parser in der Anweisung `union` aufnehmen. Wenn Ihr Parser Parameter unterstützt, fügen Sie ihn mit der Parametersignatur ein, wie der vimDnsYyyXxx Parser unten. Falls nicht, fügen Sie ihn ohne Signatur ein, wie den Parser vimDnsWwwZzz unten.

```kusto
let DnsGeneric=(starttime:datetime=datetime(null), endtime:datetime=datetime(null)... ){
  union isfuzzy=true
    vimDnsEmpty, 
    vimDnsCiscoUmbrella (starttime, endtime, srcipaddr...),
    vimDnsInfobloxNIOS (starttime, endtime, srcipaddr...),
    vimDnsMicrosoftOMS (starttime, endtime, srcipaddr...),
    vimDnsYyyXxx (starttime, endtime, srcipaddr...),
    vimDnsWwwZzz
  };
  DnsGeneric( starttime, endtime, srcipaddr...)
```


## <a name="deploy-parsers"></a>Bereitstellen von Parsern

Sie können Parser manuell bereitstellen, indem Sie sie auf die Azure Monitor-Seite Protokolle kopieren und die Änderung speichern. Diese Methode empfiehlt sich bei Tests. Weitere Informationen finden Sie unter [Erstellen einer Funktion](../azure-monitor/logs/functions.md).

Um jedoch eine große Anzahl von Parsern bereitzustellen, wird die Verwendung einer ARM-Vorlage empfohlen. Beispielsweise können Sie eine ARM-Vorlage verwenden, wenn Sie eine vollständige Normalisierungslösung bereitstellen, die einen quellenunabhängigen Parser und mehrere quellenspezifische Parser enthält, oder wenn Sie mehrere Parser für verschiedene Schemas für eine Quelle bereitstellen.

Weitere Informationen finden Sie in der [ARM-Vorlage des generischen Parsers](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery). Verwenden Sie diese Vorlage als Ausgangspunkt, und stellen Sie Ihren Parser bereit, indem Sie ihn während der Bereitstellung der Vorlage an der relevanten Stelle einfügen. Ein Beispiel finden Sie in der [ARM-Vorlage für DNS-Parser](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM).

> [!TIP]
> ARM-Vorlagen können verschiedene Ressourcen enthalten, sodass Ihre Parser zusammen mit Connectors, Analyseregeln oder Watchlists bereitgestellt werden können, um nur einige nützliche Optionen zu nennen. Zum Beispiel kann Ihr Parser auf eine Watchlist verweisen, die parallel bereitgestellt wird.
>


## <a name="next-steps"></a><a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden die Parser für das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) erläutert.

Weitere Informationen finden Sie unter

- Sehen Sie sich das [ausführliche Webinar zu normalisierten Parsern und Inhalten in Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) oder die [Folien](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM) an.
- [Übersicht über das erweiterte SIEM-Informationsmodell](normalization.md)
- [Schemas des erweiterten SIEM-Informationsmodells](normalization-about-schemas.md)
- [Inhalte des erweiterten SIEM-Informationsmodells](normalization-content.md)

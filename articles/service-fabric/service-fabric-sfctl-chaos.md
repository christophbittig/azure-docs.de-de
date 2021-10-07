---
title: 'Azure Service Fabric CLI: sfctl chaos'
description: Erfahren Sie mehr über sfctl, die Azure Service Fabric-Befehlszeilenschnittstelle. Enthält eine Liste der Befehle zum Verwalten von Chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 4d140f5f71413524dc60d0d07bdc6b646ac552a1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351151"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Ermöglicht es, den Chaos-Testdienst zu starten und zu beenden sowie seine Berichte anzuzeigen.

## <a name="subgroups"></a>Untergruppen
|Untergruppe|BESCHREIBUNG|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Ruft den Zeitplan des Chaostests ab und legt diesen fest. |
## <a name="commands"></a>Befehle

|Befehl|BESCHREIBUNG|
| --- | --- |
| events | Ruft das jeweils nächste Segment der Chaostestereignisse basierend auf dem Fortsetzungstoken oder Zeitbereich ab. |
| get | Ruft den Status des Chaostests ab. |
| start | Startet Chaos im Cluster |
| stop | Beendet den Chaostest, wenn er im Cluster ausgeführt wird, und versetzt den Zeitplan des Chaostests in den beendeten Zustand. |

## <a name="sfctl-chaos-events"></a>sfctl chaos events
Ruft das jeweils nächste Segment der Chaostestereignisse basierend auf dem Fortsetzungstoken oder Zeitbereich ab.

Um das jeweils nächste Segment der Chaostestereignisse abzurufen, können Sie das ContinuationToken angeben. Um den Start eines neuen Segments der Chaostestereignisse abzurufen, können Sie mit StartTimeUtc und EndTimeUtc den Zeitbereich angeben. Sie können im selben Aufruf nicht das ContinuationToken sowie den Zeitbereich angeben. Wenn mehr als 100 Chaostestereignisse vorhanden sind, werden die Chaostestereignisse in mehreren Segmenten zurückgegeben, wobei ein Segment nicht mehr als 100 Chaostestereignisse enthält. Um das jeweils nächste Segment abzurufen, führen Sie einen Aufruf dieser API mit dem Fortsetzungstoken durch.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --end-time-utc | Die Windows-Dateizeit, die die Endzeit des Zeitbereichs angibt, für den ein Chaos-Bericht generiert werden soll. Weitere Informationen finden Sie unter [DateTime.ToFileTimeUtc-Methode](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx). |
| --max-results | Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthält die seitenweise Abfrage so viele Ergebnisse, wie in die Rückgabemeldung passen. |
| --start-time-utc | Die Windows-Dateizeit, die die Anfangszeit des Zeitbereichs angibt, für den ein Chaos-Bericht generiert werden soll. Weitere Informationen finden Sie unter [DateTime.ToFileTimeUtc-Methode](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx). |
| --timeout -t | Der Servertimeout für die Ausführung des Vorgangs in Sekunden. Dieser Timeout gibt die Zeitdauer an, die der Client bereit ist, auf den Abschluss des angeforderten Vorgangs zu warten. Der Standardwert für diesen Parameter ist 60 Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen. |
| --help -h | Zeigen Sie diese Hilfemeldung an, und schließen Sie sie. |
| --output -o | Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden. |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
Ruft den Status des Chaostests ab.

Ruft den Status des Chaostests ab, wobei der Ausführungsstatus des Chaostests, die zur Ausführung des Chaostests verwendeten Chaostestparameter und der Status des Chaostestzeitplans angegeben werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --timeout -t | Der Servertimeout für die Ausführung des Vorgangs in Sekunden. Dieser Timeout gibt die Zeitdauer an, die der Client bereit ist, auf den Abschluss des angeforderten Vorgangs zu warten. Der Standardwert für diesen Parameter ist 60 Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen. |
| --help -h | Zeigen Sie diese Hilfemeldung an, und schließen Sie sie. |
| --output -o | Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden. |

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Startet Chaos im Cluster

Startet Chaos mit den übergebenen Chaos-Parametern, wenn Chaos noch nicht im Cluster ausgeführt wird. Wenn Chaos bei der Ausführung dieses Aufrufs bereits ausgeführt wird, führt der Aufruf zu einem Fehler mit dem Fehlercode FABRIC_E_CHAOS_ALREADY_RUNNING. Weitere Informationen finden Sie im Artikel [Auslösen von kontrolliertem Chaos in Service Fabric-Clustern](service-fabric-controlled-chaos.md).

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-type-health-policy-map | Hierbei handelt es sich um ein JSON-codiertes Array mit Wörterbucheinträgen (Schlüssel/Wert) mit einem maximalen Prozentsatz fehlerhafter Anwendungen für bestimmte Anwendungstypen. Jeder Wörterbucheintrag gibt als Schlüssel den Namen des Anwendungstyps und als Wert eine ganze Zahl an, die dem MaxPercentUnhealthyApplications-Prozentsatz entspricht, mit dem die Anwendungen des angegebenen Anwendungstyps ausgewertet werden. <br><br> Definiert eine Zuordnung mit dem maximalem Prozentsatz fehlerhafter Anwendungen für bestimmte Anwendungstypen. Die Zuordnung von Anwendungstyp und Integritätsrichtlinie kann bei der Bewertung der Clusterintegrität für die Beschreibung einzelner Anwendungstypen verwendet werden. Die in der Zuordnung enthaltenen Anwendungstypen werden anhand des Prozentsatzes, der in der Zuordnung angegeben ist, und nicht anhand des in der Clusterintegritätsrichtlinie definierten globalen MaxPercentUnhealthyApplications-Werts ausgewertet. Anwendungen der Anwendungstypen, die in der Zuordnung angegeben sind, werden nicht zum globalen Pool von Anwendungen gezählt. Wenn beispielsweise einige Anwendungen eines Typs wichtig sind, kann der Clusteradministrator einen Eintrag für diesen Anwendungstyp zur Zuordnung hinzufügen und ihm den Wert 0 % zuweisen (der aussagt, dass Fehler nicht toleriert werden). Alle anderen Anwendungen können mit einem MaxPercentUnhealthyApplications-Wert von 20 % ausgewertet werden, bei dem einige Ausfälle bei Tausenden von Anwendungsinstanzen toleriert werden. Die Zuordnung von Anwendungstypen zu Integritätsrichtlinien wird nur angewendet, wenn das Clustermanifest die Auswertung der Integrität von Anwendungstypen mithilfe des Konfigurationseintrags für HealthManager/EnableApplicationTypeHealthEvaluation ermöglicht. <br><br> Beispiel für eine JSON-codierte Zeichenfolge: [{\"key\": \"fabric:/Voting\", \"value\": \"0\"}] |
| --chaos-target-filter | JSON-codiertes Wörterbuch mit zwei Schlüsseln vom Typ string. Die beiden Schlüssel sind NodeTypeInclusionList und ApplicationInclusionList. Die Werte für beide Schlüssel sind Listen von Zeichenfolgen. chaos_target_filter definiert alle Filter für die Chaos-Zielfehler, um z.B. nur bestimmte Knotentypen oder Anwendungen zu stören. <br><br> Wenn chaos_target_filter nicht verwendet wird, stört Chaos alle Clusterentitäten. Wenn chaos_target_filter verwendet wird, stört Chaos nur die Entitäten, die der chaos_target_filter-Spezifikation entsprechen. NodeTypeInclusionList und ApplicationInclusionList gestatten lediglich eine Vereinigungssemantik. Es ist nicht möglich, eine Schnittmenge von NodeTypeInclusionList und ApplicationInclusionList anzugeben. Es ist z. B. nicht möglich, Folgendes anzugeben: „Diese Anwendung nur bemängeln, wenn sie sich auf diesem Knotentyp befindet“. Sobald eine Entität entweder in „NodeTypeInclusionList“ oder „ApplicationInclusionList“ enthalten ist, kann diese Entität nicht mit „ChaosTargetFilter“ ausgeschlossen werden. Selbst wenn „applicationX“ nicht in „ApplicationInclusionList“ enthalten ist, kann „applicationX“ in manchen Chaos-Iterationen fehlerhaft sein, da es sich anscheinend auf einem Knoten von „nodeTypeY“ befindet, der in „NodeTypeInclusionList“ enthalten ist. Wenn sowohl NodeTypeInclusionList als auch ApplicationInclusionList leer sind, wird eine ArgumentException ausgelöst. Für die Knoten dieser Knotentypen sind alle Arten von Fehlern („Knoten neu starten“, „Codepaket neu starten“, „Replikat entfernen“, „Replikat neu starten“, „primäres Replikat verschieben“ und „sekundäres Replikat verschieben“) aktiviert. Wenn ein Knotentyp (z.B. NodeTypeX) nicht in NodeTypeInclusionList enthalten ist, dann werden Fehler auf Knotenebene (z.B. NodeRestart) niemals für die Knoten von NodeTypeX aktiviert, aber Codepaket- und Replikatfehler können für NodeTypeX weiterhin aktiviert werden, wenn sich eine Anwendung in ApplicationInclusionList zufällig auf einem Knoten von NodeTypeX befindet. Es können maximal 100 Knotentypnamen in diese Liste aufgenommen werden. Um diese Zahl zu erhöhen, ist ein Upgrade der MaxNumberOfNodeTypesInChaosEntityFilter-Konfiguration erforderlich. Alle Replikate, die zu den Diensten dieser Anwendungen gehören, sind durch Chaos für Replikatfehler anfällig („Replikat neu starten“, „Replikat entfernen“, „primäres Replikat verschieben“ und „sekundäres Replikat verschieben“). Chaos startet ein Codepaket möglicherweise nur neu, wenn das Codepaket nur Replikate dieser Anwendungen hostet. Wenn eine Anwendung nicht in dieser Liste enthalten ist, kann sie dennoch in einer Chaos-Iteration gestört werden, wenn die Anwendung auf einem Knoten eines Knotentyps landet, der in NodeTypeInclusionList enthalten ist. Wenn „applicationX“ jedoch durch Platzierungseinschränkungen an „nodeTypeY“ gebunden ist und „applicationX“ in „ApplicationInclusionList“ fehlt und „nodeTypeY“ in „NodeTypeInclusionList“ fehlt, dann wird „applicationX“ niemals fehlerhaft sein. Es können maximal 1.000 Anwendungsnamen in diese Liste aufgenommen werden. Um diese Zahl zu erhöhen, ist ein Upgrade der MaxNumberOfApplicationsInChaosEntityFilter-Konfiguration erforderlich. |
| --context | JSON-codierte Zuordnung von Schlüssel-Wert-Paaren des Typs (string, string). Die Zuordnung kann verwendet werden, um Informationen zur Ausführung von Chaos aufzuzeichnen. Es kann nicht mehr als 100 solcher Paare geben, und jede Zeichenfolge (Schlüssel oder Wert) darf höchstens 4.095 Zeichen lang sein. Diese Zuordnung wird beim Start der Chaos-Ausführung festgelegt, um optional den Kontext der jeweiligen Ausführung zu speichern. |
| --disable-move-replica-faults | Deaktiviert das Verschieben von primären und sekundären Fehlern . |
| --max-cluster-stabilization | Die maximale Zeitdauer, die abzuwarten ist, bis alle Clusterentitäten stabil und fehlerfrei sind.  Standardwert\: 60. <br><br> Chaos wird in Iterationen ausgeführt. Am Anfang jeder Iteration wird die Integrität der Clusterentitäten überprüft. Wenn während der Überprüfung eine Clusterentität nicht innerhalb von MaxClusterStabilizationTimeoutInSeconds stabil und fehlerfrei ist, generiert Chaos ein Ereignis für einen Validierungsfehler. |
| --max-concurrent-faults | Die maximale Anzahl von gleichzeitigen Fehlern, die pro Iteration ausgelöst werden. Der Chaostest wird in Iterationen ausgeführt, wobei zwei aufeinanderfolgende Iterationen von einer Überprüfungsphase unterbrochen werden. Je höher die Parallelität, desto aggressiver erfolgt die Einfügung von Fehlern (Fault Injection) – dazu gehört auch eine komplexere Reihe von Zuständen zum Aufdecken von Fehlern. Es wird empfohlen, mit dem Wert 2 oder 3 zu beginnen und diesen vorsichtig zu erhöhen.  Standardwert\: „1“. |
| --max-percent-unhealthy-apps | Der maximal zulässige Prozentsatz fehlerhafter Anwendungen, bevor ein Fehler gemeldet wird, wenn während des Chaostests die Clusterintegrität ausgewertet wird. <br><br> Der maximal zulässige Prozentsatz fehlerhafter Anwendungen, bevor ein Fehler gemeldet wird. Soll es z. B.zulässig sein, dass 10 % der Anwendungen fehlerhaft sind, muss dieser Wert gleich „10“ sein. Der Prozentsatz entspricht dem maximalen tolerierten Prozentsatz an Anwendungen, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Wird der Prozentsatz eingehalten, gibt es aber mindestens eine fehlerhafte Anwendung, wird die Integrität als „Warning“ ausgewertet. Dies wird berechnet, indem die Anzahl von fehlerhaften Anwendungen durch die Gesamtanzahl von Anwendungsinstanzen im Cluster dividiert wird, wobei Anwendungen mit Anwendungstypen ausgeschlossen werden, die in „ApplicationTypeHealthPolicyMap“ enthalten sind. Die Berechnung wird aufgerundet, um einen Fehler bei einer kleinen Anzahl von Anwendungen zu tolerieren. Der Standardprozentsatz ist null. |
| --max-percent-unhealthy-nodes | Der maximal zulässige Prozentsatz fehlerhafter Knoten, bevor ein Fehler gemeldet wird, wenn während des Chaostests die Clusterintegrität ausgewertet wird. <br><br> Der maximal zulässige Prozentsatz fehlerhafter Knoten, bevor ein Fehler gemeldet wird. Soll es z. B.zulässig sein, dass 10 % der Knoten fehlerhaft sind, muss dieser Wert gleich „10“ sein. Der Prozentsatz entspricht dem maximalen tolerierten Prozentsatz an Knoten, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Wird der Prozentsatz eingehalten, gibt es aber mindestens einen fehlerhaften Knoten, wird die Integrität als „Warning“ ausgewertet. Der Prozentsatz wird berechnet, indem die Anzahl von fehlerhaften Knoten durch die Gesamtanzahl von Knoten im Cluster dividiert wird. Die Berechnung wird aufgerundet, um einen Fehler auf einer kleinen Anzahl von Knoten zu tolerieren. Der Standardprozentsatz ist null. Beim Konfigurieren dieses Prozentsatzes muss berücksichtigt werden, dass in großen Clustern immer einige Knoten inaktiv oder aufgrund von Wartungsarbeiten nicht verfügbar sind. |
| --time-to-run | Die Gesamtzeit (in Sekunden), während der der Chaostest ausgeführt wird, bevor er automatisch beendet wird. Der maximal zulässige Wert ist „4.294.967.295“ (System.UInt32.MaxValue).  Standardwert\: „4294967295“. |
| --timeout -t | Standardwert\: 60. |
| --wait-time-between-faults | Wartezeit (in Sekunden) zwischen aufeinander folgenden Fehlern innerhalb einer einzelnen Iteration.  Standardwert\: „20“. <br><br> Je größer der Wert ist, desto geringer ist die Überlappung zwischen Fehlern und desto einfacher ist auch die Sequenz der Zustandswechsel, die der Cluster durchläuft. Es wird empfohlen, mit einem Wert zwischen 1 und 5 zu beginnen und diesen vorsichtig zu erhöhen. |
| --wait-time-between-iterations | Zeitabstand (in Sekunden) zwischen zwei aufeinander folgenden Iterationen von Chaostests. Je höher der Wert ist, desto niedriger ist die durchschnittliche Fault Injection-Rate.  Standardwert\: „30“. |
| --warning-as-error | Gibt an, ob Warnungen mit demselben Schweregrad wie Fehler berücksichtigt werden. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen. |
| --help -h | Zeigen Sie diese Hilfemeldung an, und schließen Sie sie. |
| --output -o | Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Beendet den Chaostest, wenn er im Cluster ausgeführt wird, und versetzt den Zeitplan des Chaostests in den beendeten Zustand.

Verhindert die Ausführung neuer Fehler im Rahmen des Chaostests. In der Ausführung befindliche Fehler werden weiterhin ausgeführt, bis sie abgeschlossen sind. Der aktuelle Chaostestzeitplan wird in den beendeten Zustand versetzt. Nachdem ein Zeitplan beendet wurde, verbleibt er im beendeten Zustand und wird nicht verwendet, um neue Chaostestausführungen zu planen. Es muss ein neuer Chaoszeitplan festgelegt werden, damit die Zeitplanung fortgesetzt werden kann.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --timeout -t | Der Servertimeout für die Ausführung des Vorgangs in Sekunden. Dieser Timeout gibt die Zeitdauer an, die der Client bereit ist, auf den Abschluss des angeforderten Vorgangs zu warten. Der Standardwert für diesen Parameter ist 60 Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen. |
| --help -h | Zeigen Sie diese Hilfemeldung an, und schließen Sie sie. |
| --output -o | Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden. |


## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](./scripts/sfctl-upgrade-application.md)

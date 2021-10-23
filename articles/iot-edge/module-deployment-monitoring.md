---
title: Automatische Bereitstellung für Gerätegruppen – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden Sie automatische Bereitstellungen in Azure IoT Edge, um Gerätegruppen basierend auf freigegebenen Tags zu verwalten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/18/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 349ad022876b2504fe31be6f162255ae4d6d6a65
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179467"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Grundlegendes zu automatischen IoT Edge-Bereitstellungen für einzelne Geräte oder nach Bedarf

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Mithilfe von automatischen Bereitstellungen und einer mehrstufigen Bereitstellung können Sie Module auf einer großen Anzahl von IoT Edge Geräten verwalten und konfigurieren.

Azure IoT Edge bietet zwei Möglichkeiten zum Konfigurieren der Module, die auf IoT Edge Geräten ausgeführt werden sollen. Die erste Methode ist die Bereitstellung von Modulen pro Gerät. Sie erstellen ein Bereitstellungsmanifest und wenden es dann auf ein bestimmtes Gerät nach Name an. Die zweite Methode ist die automatische Bereitstellung auf einem beliebigen registrierten Gerät, das eine Reihe von definierten Bedingungen erfüllt. Sie erstellen ein Bereitstellungsmanifest und definieren dann, auf welche Geräte es angewendet werden soll, basierend auf [Tags](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) im Gerätezwilling.

Der Schwerpunkt dieses Artikels liegt auf dem Konfigurieren und Überwachen von größeren Gerätebeständen, die zusammen als *automatische IoT Edge-Bereitstellungen* bezeichnet werden.  Die grundlegenden Schritte zur Bereitstellung lauten wie folgt:

1. Ein Bediener definiert eine Bereitstellung, in der ein Satz von Modulen und die Zielgeräte beschrieben werden.  Jede Bereitstellung verfügt über ein Bereitstellungsmanifest, das diese Informationen enthält.
2. Der IoT Hub-Dienst kommuniziert mit allen Zielgeräten, um sie mit den deklarierten Modulen zu konfigurieren.
3. Der IoT Hub-Dienst ruft den Status von den IoT Edge-Geräten ab und macht diese für den Operator verfügbar.    Beispielsweise kann ein Bediener sehen, wenn ein Edge-Gerät nicht erfolgreich konfiguriert wurde oder wenn bei einem Modul während der Laufzeit ein Fehler auftritt.
4. Zu jedem Zeitpunkt werden neue IoT Edge-Geräte, die die Zielbedingungen erfüllen, für die Bereitstellung konfiguriert.

In diesem Artikel werden die einzelnen Komponenten beschrieben, die beim Konfigurieren und Überwachen einer Bereitstellung verwendet werden. Eine exemplarische Vorgehensweise zum Erstellen und Aktualisieren einer Bereitstellung finden Sie unter [Bedarfsabhängiges Bereitstellen und Überwachen von IoT Edge-Modulen](how-to-deploy-at-scale.md).

## <a name="deployment"></a>Bereitstellung

Bei einer automatischen IoT Edge-Bereitstellung werden IoT Edge-Modulimages zugewiesen, die als Instanzen auf einem bestimmten Satz von IoT Edge-Geräten ausgeführt werden. Hierfür wird ein IoT Edge-Bereitstellungsmanifest konfiguriert, das eine Liste von Modulen mit den entsprechenden Initialisierungsparametern enthält.  Eine Bereitstellung kann einem einzelnen Gerät (basierend auf der Geräte-ID) oder einer Gruppe von Geräten (basierend auf Tags) zugewiesen werden.  Nachdem ein IoT Edge-Gerät ein Bereitstellungsmanifest erhalten hat, lädt es die Containerimages aus den entsprechenden Containerrepositorys herunter, installiert und konfiguriert sie.  Sobald eine Bereitstellung erstellt wurde, kann der Operator den Bereitstellungsstatus überwachen und so erkennen, ob die Zielgeräte ordnungsgemäß konfiguriert wurden.

Nur IoT Edge-Geräte können mit einer Bereitstellung konfiguriert werden. Auf dem Gerät muss Folgendes vorhanden sein, um die Bereitstellung zu ermöglichen:

* Das Basisbetriebssystem
* Ein Containerverwaltungssystem (beispielsweise Moby oder Docker)
* Bereitstellung der IoT Edge-Laufzeit

### <a name="deployment-manifest"></a>Bereitstellungsmanifest

Ein Bereitstellungsmanifest ist ein JSON-Dokument, in dem die Module beschrieben werden, die auf den IoT Edge-Zielgeräten konfiguriert werden sollen. Es enthält die Konfigurationsmetadaten für alle Module, einschließlich der erforderlichen Systemmodule (insbesondere IoT Edge-Agent und IoT Edge-Hub).  

Die Konfigurationsmetadaten für jedes Modul enthalten Folgendes:

* Version
* type
* Status (z.B. „Wird ausgeführt“ oder „Beendet“)
* Neustartrichtlinie
* Image- und Containerregistrierung
* Routen für Dateneingabe und -ausgabe

Wenn das Modulimage in einer privaten Containerregistrierung gespeichert ist, befinden sich die Anmeldeinformationen für die Registrierung im IoT Edge-Agent.

### <a name="target-condition"></a>Zielbedingung

Die Zielbedingung wird während der Lebensdauer der Bereitstellung kontinuierlich ausgewertet. Es sind alle neuen Geräte enthalten, die die Anforderungen erfüllen. Alle vorhandenen Geräte, die die Anforderungen nicht mehr erfüllen, werden entfernt. Die Bereitstellung wird reaktiviert, wenn der Dienst Änderungen an der Zielbedingung feststellt.

So verfügen Sie beispielsweise über eine Bereitstellung mit der Zielbedingung „tags.environment = 'prod'“. Wenn Sie die Bereitstellung starten, sind 10 Produktionsgeräte vorhanden. Die Module werden erfolgreich auf diesen 10 Geräten installiert. Im Status des IoT Edge-Agents werden insgesamt 10 Geräte, 10 erfolgreiche Antworten, 0 Fehlerantworten und 0 ausstehende Antworten angezeigt. Nun fügen Sie fünf weitere Geräte mit „tags.environment = 'prod'“ hinzu. Der Dienst erkennt die Änderung, und der Status des IoT Edge-Agents wird in insgesamt 15 Geräte, 10 erfolgreiche Antworten, 0 Fehlerantworten und 5 ausstehende Antworten geändert, während der Agent die fünf neuen Geräte bereitstellt.

Verwenden Sie eine beliebige boolesche Bedingung bei Gerätezwillingstags, vom Gerätezwilling gemeldeten Eigenschaften oder für „deviceId“, um die Zielgeräte auszuwählen. Wenn Sie eine Bedingung mit Tags verwenden möchten, müssen Sie auf dem Gerätezwilling einen Abschnitt „"tags":{}“ auf der gleichen Ebene wie die Eigenschaften hinzufügen. [Weitere Informationen über Tags in Gerätezwillingen](../iot-hub/iot-hub-devguide-device-twins.md)

Beispiele für Zielbedingungen:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' UND tags.environment = 'prod' UND NICHT deviceId = 'linuxprod1'
* properties.reported.devicemodel = '4000x'

Beachten Sie diese Einschränkungen, wenn Sie eine Zielbedingung erstellen:

* Sie können auf einem Gerätezwilling eine Zielbedingung nur mithilfe von Tags, gemeldeten Eigenschaften oder von „deviceId“ erstellen.
* Doppelte Anführungszeichen sind in keinem Teil der Zielbedingung zulässig. Verwenden Sie einfache Anführungszeichen.
* Einfache Anführungszeichen werden für die Werte der Zielbedingung verwendet. Daher müssen Sie für ein einfaches Anführungszeichen ein weiteres einfaches Anführungszeichen als Escapezeichen verwenden, wenn es Teil des Gerätenamens ist. Geben Sie für die Zielbedingung des Geräts mit dem Namen `operator'sDevice` beispielsweise `deviceId='operator''sDevice'` an.
* In Werten für Zielbedingungen sind Zahlen, Buchstaben und folgende Zeichen zulässig: `“()<>@,;:\\"/?={} \t\n\r`.
* Die folgenden Zeichen sind in Zielbedingungsschlüsseln nicht zulässig: `/;`.

### <a name="priority"></a>Priority

Eine Priorität definiert, ob eine Bereitstellung auf einem Zielgerät relativ zu anderen Bereitstellungen angewendet werden soll. Bei der Bereitstellungspriorität handelt es sich um eine positive ganze Zahl, wobei höhere Werte eine höhere Priorität angeben. Wenn ein IoT Edge-Gerät Ziel mehrerer Bereitstellungen ist, wird die Bereitstellung mit der höchsten Priorität angewendet.    Bereitstellungen mit niedrigeren Prioritäten werden nicht angewendet, und Bereitstellungen werden nicht zusammengeführt.    Wenn ein Gerät Ziel von mehreren Bereitstellungen mit gleicher Priorität ist, gilt die zuletzt erstellte Bereitstellung (entsprechend dem Erstellungszeitstempel).

### <a name="labels"></a>Bezeichnungen

Bei Bezeichnungen handelt es sich um Schlüssel-Wert-Paare als Zeichenfolgen, mit denen Sie Bereitstellungen filtern und gruppieren können.  Eine Bereitstellung kann mehrere Bezeichnungen aufweisen. Bezeichnungen sind optional und haben keine Auswirkungen auf die tatsächliche Konfiguration von IoT Edge-Geräten.

### <a name="metrics"></a>Metriken

Alle Bereitstellungen melden standardmäßig über vier Metriken:

* **Gezielt** zeigt die IoT Edge-Geräte, die der Zielbedingung für die Bereitstellung entsprechen.
* **Angewendet** zeigt die IoT Edge-Zielgeräte, für die es keine weitere Bereitstellung mit höherer Priorität gibt.
* **Erfolg gemeldet** zeigt die IoT Edge-Geräte, die gemeldet haben, dass die Module erfolgreich bereitgestellt wurden.
* **Fehler gemeldet** zeigt die IoT Edge-Geräte, die gemeldet haben, dass ein oder mehrere Module nicht erfolgreich bereitgestellt wurden. Stellen Sie zum weiteren Untersuchen des Fehlers eine Remoteverbindung mit diesen Geräten her, und zeigen Sie die Protokolldateien an.

Darüber hinaus können Sie Ihre eigenen benutzerdefinierten Metriken zum Überwachen und Verwalten der Bereitstellung definieren.

Metriken bieten zusammenfassende Angaben zu den verschiedenen Zuständen, die Geräte nach dem Anwenden einer Bereitstellungskonfiguration möglicherweise zurückmelden. Metriken können [vom edgeHub-Modulzwilling gemeldete Eigenschaften](module-edgeagent-edgehub.md#edgehub-reported-properties) abfragen, z. B. *lastDesiredStatus* oder *lastConnectTime*. Beispiel:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Das Hinzufügen Ihrer eigenen Metriken ist optional und hat keine Auswirkungen auf die tatsächliche Konfiguration von IoT Edge-Geräten.

## <a name="layered-deployment"></a>Mehrstufige Bereitstellung

Bei mehrstufigen Bereitstellungen handelt es sich um automatische Bereitstellungen, die kombiniert werden können, um die Anzahl der eindeutigen Bereitstellungen, die erstellt werden müssen, zu verringern. Mehrstufige Bereitstellungen sind in Szenarien nützlich, in denen dieselben Module in vielen automatischen Bereitstellungen in verschiedenen Kombinationen wiederverwendet werden.

Mehrstufige Bereitstellungen bestehen aus denselben Grundkomponenten wie jede automatische Bereitstellung. Sie richten sich an Geräte, die auf Tags in den Gerätezwillingen basieren, und bieten dieselbe Funktionalität wie Bezeichnungen, Metriken und Statusberichte. Mehrstufigen Bereitstellungen werden außerdem Prioritäten zugewiesen. Statt aber anhand der Priorität zu bestimmen, welche Bereitstellung auf ein Gerät angewendet wird, bestimmt die Priorität, wie mehrere Bereitstellungen auf einem Gerät eingestuft werden. Wenn beispielsweise zwei mehrstufige Bereitstellungen ein Modul oder eine Route mit demselben Namen enthalten, wird die Bereitstellung mit der höheren Priorität angewendet und die Bereitstellung mit der niedrigeren Priorität überschrieben.

Die Systemlaufzeitmodule, „edgeAgent“ und „edgeHub“, werden nicht als Teil einer mehrstufigen Bereitstellung konfiguriert. Auf IoT Edge-Geräte, die Ziel für eine mehrstufige Bereitstellung sind, muss zunächst eine automatische Standardbereitstellung angewendet werden. Bei der automatischen Bereitstellung wird die Basis bereitgestellt, auf der mehrstufige Bereitstellungen hinzugefügt werden können.

Ein IoT Edge-Gerät kann nur eine einzige automatische Standardbereitstellung (aber mehrere mehrstufige automatische Bereitstellungen) anwenden. Alle mehrstufigen Bereitstellungen, deren Ziel ein Gerät ist, müssen eine höhere Priorität als die automatische Bereitstellung für dieses Gerät haben.

Sehen Sie sich beispielsweise das folgende Szenario eines Unternehmens an, das Gebäude verwaltet. Die Mitarbeiter haben IoT Edge-Module zum Sammeln von Daten aus Sicherheitskameras, Bewegungssensoren und Aufzügen entwickelt. Allerdings können nicht in allen Gebäuden alle drei Module verwendet werden. Bei automatischen Standardbereitstellungen muss das Unternehmen individuelle Bereitstellungen für alle Modulkombinationen erstellen, die von den jeweiligen Gebäuden benötigt werden.

![Standardmäßige automatische Bereitstellungen müssen jede Modulkombination erfüllen.](./media/module-deployment-monitoring/standard-deployment.png)

Sobald das Unternehmen aber zu automatischen mehrstufigen Bereitstellungen wechselt, wird festgestellt, dass man dieselben Modulkombinationen für die Gebäude erstellen kann, wobei weniger Bereitstellungen verwaltet werden müssen. Jedes Modul verfügt über eine eigene mehrstufige Bereitstellung, und die Gerätetags identifizieren, welche Module dem einzelnen Gebäude hinzugefügt werden.

![Mehrstufige automatische Bereitstellungen vereinfachen Szenarien, in denen dieselben Module auf unterschiedliche Weise kombiniert werden.](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfiguration von Modulzwillingen

Wenn Sie mit mehrstufigen Bereitstellungen arbeiten, verfügen Sie – absichtlich oder anderweitig – über zwei Bereitstellungen mit demselben Modul, dessen Ziel ein Gerät ist. In diesen Fällen können Sie entscheiden, ob die Bereitstellung mit höherer Priorität den Modulzwilling überschreiben oder daran angefügt werden soll. Beispielsweise können Sie über eine Bereitstellung verfügen, die das gleiche Modul auf 100 verschiedene Geräte anwendet. 10 dieser Geräte befinden sich allerdings in sicheren Einrichtungen und benötigen eine zusätzliche Konfiguration, um über Proxyserver kommunizieren zu können. Mithilfe einer mehrstufigen Bereitstellung können Sie Eigenschaften für Modulzwillinge hinzufügen, die diesen 10 Geräten eine sichere Kommunikation ermöglichen, ohne dass dadurch die vorhandenen Modulzwillingsinformationen aus der Basisbereitstellung überschrieben werden.

Sie können die gewünschten Eigenschaften des Modulzwillings im Bereitstellungsmanifest anfügen. Dort, wo Sie in einer Standardbereitstellung im Abschnitt **properties.desired** des Modulzwillings Eigenschaften hinzufügen würden, können Sie in einer mehrstufigen Bereitstellung eine neue Teilmenge von gewünschten Eigenschaften deklarieren.

So könnten Sie in einer Standardbereitstellung beispielsweise das simulierte Temperatursensor-Modul mit den folgenden gewünschten Eigenschaften hinzufügen, die es anweisen, Daten in Intervallen von 5 Sekunden zu senden:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

In einer mehrstufigen Bereitstellung, deren Ziel einige oder alle identischen Geräte sind, können Sie eine Eigenschaft hinzufügen, die den simulierten Sensor anweist, 1000 Nachrichten zu senden und dann anzuhalten. Weil Sie die vorhandenen Eigenschaften nicht überschreiben möchten, erstellen Sie innerhalb der gewünschten Eigenschaften den neuen Abschnitt `layeredProperties`, der die neue Eigenschaft enthält:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Ein Gerät, auf das beide Bereitstellungen angewendet werden, zeigt im Modulzwilling für den simulierten Temperatursensor folgende Eigenschaften an:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Wenn Sie das Feld `properties.desired` des Modulzwillings in einer mehrstufigen Bereitstellung festlegen, werden dadurch die gewünschten Eigenschaften für dieses Modul in Bereitstellungen mit niedrigerer Priorität überschrieben.

## <a name="phased-rollout"></a>Schrittweises Rollout

Bei einem schrittweisen Rollout handelt es sich um ein Verfahren, bei dem ein Operator Änderungen für einen größeren Satz von IoT Edge-Geräten bereitstellt. Hierbei werden Änderungen nach und nach vorgenommen, um das Risiko durch umfangreiche wichtige Änderungen zu reduzieren. Automatische Bereitstellungen unterstützen die Verwaltung von stufenweisen Rollouts in einer Gruppe von IoT Edge-Geräten.

Ein schrittweises Rollout wird in den folgenden Phasen und Schritten ausgeführt:

1. Richten Sie eine Testumgebung mit IoT Edge-Geräten durch Bereitstellung und Festlegen eines Gerätezwillingstags wie `tag.environment='test'` ein.  Die Testumgebung sollte die Produktionsumgebung spiegeln, auf die die Bereitstellung später abzielt.
2. Erstellen Sie eine Bereitstellung, einschließlich der gewünschten Module und Konfigurationen. Die Zielbedingung sollte die Testumgebung für IoT Edge-Geräte umfassen.
3. Überprüfen Sie die neue Modulkonfiguration in der Testumgebung.
4. Aktualisieren Sie die Bereitstellung mit einer Teilmenge der IoT Edge-Produktionsgeräte, indem Sie der Zielbedingung ein neues Tag hinzufügen. Stellen Sie außerdem sicher, dass die Priorität für die Bereitstellung höher als bei anderen Bereitstellungen ist, die zurzeit für diese Geräte verwendet werden.
5. Stellen Sie sicher, dass die Bereitstellung auf den IoT-Zielgeräten erfolgreich war, indem Sie den Bereitstellungsstatus anzeigen.
6. Aktualisieren Sie die Bereitstellung so, dass sie auf alle verbleibenden IoT Edge-Produktionsgeräte abzielt.

## <a name="rollback"></a>Rollback

Für Bereitstellungen kann bei Fehlern oder Fehlkonfigurationen ein Rollback ausgeführt werden.  Da bei einer Bereitstellung die absolute Modulkonfiguration für ein IoT Edge-Gerät definiert wird, muss eine zusätzliche Bereitstellung auch auf das gleiche Gerät mit niedrigerer Priorität abzielen, auch wenn alle Module entfernt werden sollen.  

Durch das Löschen einer Bereitstellung werden die Module nicht von den Zielgeräten entfernt. Es muss eine andere Bereitstellung vorhanden sein, die eine neue Konfiguration für die Geräte definiert – selbst wenn es sich um eine leere Bereitstellung handelt.

Führen Sie die Schritte für Rollbacks in der folgenden Reihenfolge aus:

1. Vergewissern Sie sich, dass eine zweite Bereitstellung für die gleiche Gruppe von Geräten vorhanden ist. Wenn bei dem Rollback alle Module entfernt werden sollen, sollte die zweite Bereitstellung keine Module enthalten.
2. Ändern oder entfernen Sie den Ausdruck für die Zielbedingung bei der Bereitstellung, für die ein Rollback ausgeführt werden soll, sodass die Geräte die Zielbedingung nicht mehr erfüllen.
3. Stellen Sie sicher, dass das Rollback erfolgreich war, indem Sie den Bereitstellungsstatus anzeigen.
   * Die Bereitstellung, für die das Rollback ausgeführt wurde, sollte keinen Status für die Geräte mehr anzeigen, auf die sich das Rollback bezogen hat.
   * Die zweite Bereitstellung sollte den Bereitstellungsstatus für die Geräte enthalten, für die das Rollback ausgeführt wurde.

## <a name="next-steps"></a>Nächste Schritte

* Führen Sie die Schritte zum Erstellen, Aktualisieren oder Löschen einer Bereitstellung in [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-at-scale.md) aus.
* Erfahren Sie mehr über andere IoT Edge-Konzepte wie die [IoT Edge-Runtime](iot-edge-runtime.md) und [IoT-Edge-Module](iot-edge-modules.md).

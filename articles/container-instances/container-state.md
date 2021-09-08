---
title: Status von Azure Container Instances
description: Erfahren Sie mehr über die Status von Bereitstellungsvorgängen, Containern und Containergruppen von Azure Container Instances.
ms.author: nickoman
author: nickomang
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 0144b6ebd82c6df4c9ec501897a4315b4554103d
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113016317"
---
# <a name="azure-container-instances-states"></a>Status von Azure Container Instances

Azure Container Instances zeigt mehrere unabhängige Statuswerte an. Dieser Artikel katalogisiert diese Werte, wo sie gefunden werden können und was sie angeben.

## <a name="where-to-find-state-values"></a>Wo befinden sich Statuswerte?

Im Azure-Portal wird der Status an verschiedenen Stellen angezeigt. Auf alle Statuswerte kann über die JSON-Definition der Ressource zugegriffen werden. Diesen Wert finden Sie unter „Grundlagen“ auf dem Blatt „Übersicht“, wie unten dargestellt.

:::image type="content" source="./media/container-state/provisioning-state.png" alt-text="Das Blatt „Übersicht“ im Azure-Portal wird angezeigt. Der Link „JSON-Ansicht“ ist hervorgehoben.":::

Der Status wird auch an anderen Stellen im Azure-Portal angezeigt. In der folgenden Tabelle wird zusammengefasst, wo Statuswerte gefunden werden können:

|Name|JSON-Pfad|Speicherort im Azure-Portal|
|-|-|-|
|Containergruppenzustand|`properties.instanceView.state`|Unter „Grundlagen“ auf dem Blatt „Übersicht“|
|Aktueller Containerzustand|`properties.containers/initContainers[x].instanceView.currentState.state`|Unter der Spalte **Zustand** der Tabelle des Blatts „Container“|
|Vorheriger Containerstatus|`properties.containers/initContainers[x].instanceView.previousState.state`|Über *JSON-Ansicht* unter „Grundlagen“ auf dem Blatt „Übersicht“|
|Bereitstellungsstatus|`properties.provisioningState`|Über *JSON-Ansicht* unter „Grundlagen“ auf dem Blatt „Übersicht“, HTTP-Antworttext|

## <a name="container-groups"></a>Containergruppen

Dieser Wert ist der Status der bereitgestellten Containergruppe auf dem Back-End.

:::image type="content" source="./media/container-state/container-group-state.png" alt-text="Das Übersichtsblatt für die Ressource im Azure-Portal wird in einem Webbrowser angezeigt. Der Text „Status: Wird ausgeführt“ ist hervorgehoben.":::

- **Wird ausgeführt**: Die Containergruppe wird ausgeführt und es wird weiterhin versucht, sie auszuführen, bis eine Benutzeraktion oder ein durch die Neustartrichtlinie verursachter Stopp auftritt.

- **Beendet**: Die Containergruppe wurde beendet und ihre Ausführung kann nicht ohne Benutzeraktion geplant werden.

- **Ausstehend**: Die Containergruppe wartet auf die Initialisierung (Ausführung von Initialisierungscontainern wird abgeschlossen, Azure-Dateivolumes werden ggf. eingebunden). Der Container versucht weiterhin, in den Status **Wird ausgeführt** zu gelangen, es sei denn, es erfolgt eine Benutzeraktion (Beenden/Löschen).

- **Erfolgreich**: Die Containergruppe wurde erfolgreich ausgeführt. Gilt nur für die Neustartrichtlinien *Nie* und *Bei Fehler*.

- **Fehler**: Die Containergruppe konnte nicht bis zum Abschluss ausgeführt werden. Gilt nur für eine *Nie*-Neustartrichtlinie. Dieser Status gibt entweder einen Infrastrukturfehler (Beispiel: falsche Anmeldeinformationen für die Azure-Dateifreigabe) oder einen Fehler bei der Benutzeranwendung an (Beispiel: Anwendung verweist auf eine Umgebungsvariable, die nicht vorhanden ist).

Die folgende Tabelle zeigt, welche Status basierend auf der festgelegten Neustartrichtlinie auf eine Containergruppe anwendbar sind:

|Wert|Nie|Bei Fehler|Always|
|--|--|--|--|
|Wird ausgeführt|Ja|Ja|Ja|
|Beendet|Ja|Ja|Ja|
|Ausstehend|Ja|Ja|Ja|
|Erfolgreich|Ja|Ja|Nein|
|Fehler|Ja|Nein|Nein|

## <a name="containers"></a>Container

Es gibt zwei Statuswerte für Container: einen aktuellen Status und einen vorherigen Status. Im Azure-Portal, wie unten gezeigt, wird nur der aktuelle Status angezeigt. Alle Statuswerte gelten für jeden Container, unabhängig von der Neustartrichtlinie der Containergruppe.

> [!NOTE]
> Die JSON-Werte von `currentState` und `previousState` enthalten zusätzliche Informationen, z. B. einen Exitcode oder einen Grund, der nicht an anderer Stelle im Azure-Portal angezeigt wird.

:::image type="content" source="./media/container-state/container-state.png" alt-text="Das Blatt „Container“ im Azure-Portal wird angezeigt. Eine Tabelle wird angezeigt, und „Wird ausgeführt“ in der Spalte „Status“ ist hervorgehoben. ":::

- **Wird ausgeführt**: Der Container wird ausgeführt.

- **Warten**: Der Container wartet auf die Ausführung. Dieser Status gibt an, dass entweder noch Init-Container ausgeführt werden oder der Container aufgrund einer Absturzschleife ein Backoff durchführt.

- **Beendet**: Der Container wurde mit einem Exitcodewert beendet.

## <a name="provisioning"></a>Bereitstellung

Dieser Wert ist der Status des letzten Vorgangs, der für eine Containergruppe ausgeführt wurde. Im Allgemeinen handelt es sich bei diesem Vorgang um einen PUT-Vorgang (Erstellen), aber es kann auch ein POST-Vorgang (Starten/Neustart/Beenden) oder DELETE (Löschen) sein.

> [!IMPORTANT]
> Darüber hinaus sollten Benutzer keine Abhängigkeiten von Bereitstellungsstatus erstellen, die keine Endzustände sind. Abhängigkeiten von den Status **Erfolgreich** und **Fehler** sind akzeptabel.

Zusätzlich zur JSON-Ansicht befindet sich der Bereitstellungsstatus auch im [Antworttext des HTTP-Aufrufs](/rest/api/container-instances/containergroups/createorupdate#response).

### <a name="create-start-and-restart-operations"></a>Erstellen, Starten und Neustarten von Vorgängen

> [!IMPORTANT]
> PUT-Vorgänge (Erstellen) sind asynchron. Der zurückgegebene Wert aus dem PUT-Antworttext ist nicht der endgültige Status. Das Ausführen nachfolgender GET-Aufrufe für resourceId oder AsyncOperation der Containergruppe (in den PUT-Antwortheadern zurückgegeben) ist die empfohlene Methode zum Überwachen des Status der Bereitstellung.

Diese Status gelten für PUT-Vorgänge (Erstellen) und POST-Vorgänge (Start/Neustart).

- **Ausstehend**: Die Containergruppe wartet auf die Einrichtung der Infrastruktur, z. B. eine Knotenzuweisung, die Bereitstellung virtueller Netzwerke oder Sonstiges, was vor dem Pullen des Benutzerimages erforderlich ist.

- **Erstellen**: Die Infrastruktureinrichtung wurde abgeschlossen. Die Containergruppe wird nun hochgefahren und empfängt die benötigten Ressourcen (Einbinden von Azure-Dateivolumes, Abrufen der Eingangs-IP-Adresse usw.).

- **Erfolgreich**: Die Containergruppe hat ihre Container erfolgreich in den Ausführungszustand gebracht und alle benötigten Ressourcen empfangen.

- **Fehlerhaft**: Die Containergruppe ist fehlerhaft. Für einen unerwarteten Status, wenn z. B. ein Knoten ausgefallen ist, wird automatisch ein Auftrag ausgelöst, um die Containergruppe durch Verschieben zu reparieren.

- **Reparieren**: Die Containergruppe wird verschoben, um einen fehlerhaften Zustand zu reparieren.

- **Fehler**: Die Containergruppe konnte den Bereitstellungsstatus **Erfolgreich** nicht erreichen. Fehler können aus vielen Gründen auftreten (nicht zugängliches Netzwerkprofil, geringe Kapazität in der angegebenen Region, vollständige Nutzung des Benutzerkontingents, Timeout nach 30 Minuten usw.). Weitere Informationen zum Fehler finden Sie unter `events` in der JSON-Ansicht.
    > [!NOTE]
    > Ein fehlerhafter Zustand bedeutet nicht, dass die Ressource entfernt wird oder nicht weiterhin versucht wird, sie erfolgreich zu nutzen. Der Containergruppenstatus gibt den aktuellen Status der Gruppe an. Wenn Sie sicherstellen möchten, dass die Containergruppe nach dem Bereitstellungsstatus **Fehler** nicht ausgeführt wird, müssen Sie sie beenden oder löschen.

### <a name="stop-and-delete-operations"></a>Beenden und Löschen

Diese Werte gelten für POST-Vorgänge (Beenden) und DELETE-Vorgänge (Löschen).

- **Erfolgreich**: Der Vorgang zum Beenden oder Löschen der Containergruppe wurde erfolgreich abgeschlossen.

- **Fehler**: Die Containergruppe konnte den Bereitstellungsstatus **Erfolgreich** nicht erreichen, was bedeutet, dass der Vorgang zum Beenden/Löschen nicht abgeschlossen wurde. Weitere Informationen zum Fehler finden Sie unter `events` in der JSON-Ansicht.
---
title: Erkennen von Arbeitsspeicherverlust – Intelligente Erkennung in Azure Application Insights
description: Überwachen Sie Anwendungen mit Azure Application Insights auf potenzielle Arbeitsspeicherverluste.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: f2b13fc97baac2e8fcfb27475e6e018fdc0c5392
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536617"
---
# <a name="memory-leak-detection-preview"></a>Arbeitsspeicherverlusterkennung (Vorschau)

>[!NOTE]
>Sie können Ihre Application Insight-Ressourcen zu der auf Warnungen basierenden intelligenten Erkennung (Vorschauversion) migrieren. Bei der Migration werden Warnungsregeln für die verschiedenen Module der intelligenten Erkennung erstellt. Nach der Erstellung können Sie diese Regeln wie alle anderen Azure Monitor-Warnungsregeln verwalten und konfigurieren. Sie können auch Aktionsgruppen für diese Regeln konfigurieren, was mehrere Methoden für das Ausführen von Aktionen oder für das Auslösen von Benachrichtigungen bei neuen Erkennungen ermöglicht.
>
> Weitere Informationen finden Sie unter [Migration zu intelligenten Erkennungswarnungen](../alerts/alerts-smart-detections-migration.md).

Die intelligente Erkennung analysiert die Arbeitsspeichernutzung der einzelnen Prozesse in Ihrer Anwendung automatisch und kann Sie vor potenziellen Arbeitsspeicherverlusten oder einer erhöhten Arbeitsspeichernutzung warnen.

Diese Funktion erfordert keine spezielle Einrichtung, abgesehen von der [Konfiguration von Leistungsindikatoren](./performance-counters.md) für Ihre App. Sie ist aktiv, wenn Ihre App genügend Telemetriedaten für Arbeitsspeicher-Leistungsindikatoren (z. B. Private Bytes) generiert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wann erhalte ich diese Art von Benachrichtigung der intelligenten Erkennung?
Eine typische Benachrichtigung folgt auf einen stetigen Anstieg der Arbeitsspeichernutzung über einen längeren Zeitraum eines oder mehrerer Prozesse oder VMs, die Teil Ihrer Anwendung sind. Machine Learning-Algorithmen werden für die Erkennung einer erhöhten Arbeitsspeichernutzung verwendet, die dem Muster eines Speicherverlusts entspricht.

## <a name="does-my-app-really-have-a-problem"></a>Liegt wirklich ein Problem mit meiner App vor?
Eine Benachrichtigung bedeutet nicht zwingend, dass ein Problem mit Ihrer App vorliegt. Obwohl Muster von Arbeitsspeicherverlusten oft auf ein Anwendungsproblem hinweisen, könnten diese Muster typisch für Ihren spezifischen Prozess sein oder einen natürlichen geschäftlichen Grund haben. In solchen Fällen kann die Benachrichtigung ignoriert werden.

## <a name="how-do-i-fix-it"></a>Wie behebe ich das Problem?
Die Benachrichtigungen umfassen Diagnoseinformationen zur Unterstützung des diagnostischen Analyseprozesses:
1. **Selektierung.** Die Benachrichtigung zeigt den Umfang der Arbeitsspeichererhöhung (in GB) und den Zeitraum an, in dem die Größe des Arbeitsspeichers zugenommen hat. Mithilfe dieser Informationen können Sie dem Problem eine Priorität zuweisen.
2. **Umfang.** Wie viele Computer wiesen das Muster des Arbeitsspeicherverlusts auf? Wie viele Ausnahmen wurden während des potenziellen Arbeitsspeicherverlusts ausgelöst? Diese Informationen können Sie der Benachrichtigung entnehmen.
3. **Diagnose.** Die Erkennung enthält das Muster des Arbeitsspeicherverlusts, wobei die Arbeitsspeichernutzung des Prozesses im Laufe der Zeit aufgezeigt wird. Zur weiteren Diagnose des Problems können Sie auch die entsprechenden Elemente und Berichte verwenden, die mit weiterführenden Informationen verknüpft sind.

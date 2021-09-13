---
title: Verschlechterung des Schweregrads der Ablaufverfolgung – Azure Application Insights
description: Überwachen Sie Anwendungsablaufverfolgungen mit Azure Application Insights und der intelligenten Erkennung auf ungewöhnliche Muster in der Ablaufverfolgungstelemetrie.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 776469f5e5cdab7796df1edccb680ab7151bbd18
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951523"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Verschlechterung des Schweregrads der Ablaufverfolgung (Vorschau)

>[!NOTE]
>Sie können Ihre Application Insight-Ressourcen zu der auf Warnungen basierenden intelligenten Erkennung (Vorschauversion) migrieren. Bei der Migration werden Warnungsregeln für die verschiedenen Module der intelligenten Erkennung erstellt. Nach der Erstellung können Sie diese Regeln wie alle anderen Azure Monitor-Warnungsregeln verwalten und konfigurieren. Sie können auch Aktionsgruppen für diese Regeln konfigurieren, was mehrere Methoden für das Ausführen von Aktionen oder für das Auslösen von Benachrichtigungen bei neuen Erkennungen ermöglicht.
>
> Weitere Informationen finden Sie unter [Migration zu intelligenten Erkennungswarnungen](../alerts/alerts-smart-detections-migration.md).
> 

Ablaufverfolgungen werden häufig in Anwendungen verwendet, und sie helfen dabei, die Abläufe im Hintergrund nachzuvollziehen. Wenn etwas schief geht, bieten Ablaufverfolgungen entscheidende Einblicke in die Abfolge der Ereignisse, die zu dem unerwünschten Zustand geführt haben. Obwohl Ablaufverfolgungen größtenteils unstrukturiert sind, kann ihr Schweregrad dennoch wertvolle Informationen liefern. Im stabilen Zustand einer Anwendung erwarten wir, dass das Verhältnis zwischen „guten“ Ablaufverfolgungen (*Info* und *Ausführlich*) und „schlechten“ Ablaufverfolgungen (*Warnung*, *Fehler* und *Kritisch*) stabil bleibt. 

Es ist normal, aufgrund einer Vielzahl von Gründen, z. B. vorübergehenden Netzwerkproblemen, ein gewisses Maß an „schlechten“ Ablaufverfolgungen zu erwarten. Aber wenn ein echtes Problem wächst, manifestiert es sich in der Regel als eine Zunahme des relativen Anteils von „schlechten“ und „guten“ Ablaufverfolgungen. Die intelligente Erkennung analysiert automatisch die von Ihrer Anwendung protokollierte Ablaufverfolgungstelemetrie und kann Sie vor ungewöhnlichen Mustern unter Einbezug ihres Schweregrads warnen.

Dieses Feature erfordert keine spezielle Einrichtung, abgesehen von der Konfiguration von Ablaufverfolgungsprotokollierung für Ihre App. Hier erfahren Sie, wie Sie einen Protokolllistener für die Ablaufverfolgung für [.NET](./asp-net-trace-logs.md) oder [Java](./java-in-process-agent.md) konfigurieren. Er ist aktiv, wenn Ihre App genügend Telemetriedaten zur Ablaufverfolgung generiert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wann erhalte ich diese Art von Benachrichtigung der intelligenten Erkennung?
Sie können diese Art von Benachrichtigung erhalten, wenn das Verhältnis zwischen „guten“ (mit dem Status *Info* oder *Ausführlich* protokollierten) Ablaufverfolgungen und „schlechten“ (mit dem Status *Warnung*, *Fehler* oder *Schwerwiegend* protokollierten) Ablaufverfolgungen an einem bestimmten Tag abnimmt, im Vergleich zu einer Baseline, die über die letzten sieben Tage berechnet wurde.

## <a name="does-my-app-definitely-have-a-problem"></a>Liegt wirklich ein Problem mit meiner App vor?
Eine Benachrichtigung bedeutet nicht zwingend, dass ein Problem mit Ihrer App vorliegt. Obwohl eine Verschlechterung des Verhältnisses zwischen „guten“ und „schlechten“ Ablaufverfolgungen auf ein Anwendungsproblem hindeuten könnte, hat dies möglicherweise keine Auswirkungen. Der Anstieg kann z. B. auf einen neuen Flow in der Anwendung zurückzuführen sein, der mehr „schlechte“ Ablaufverfolgungen ausgibt als bestehende Flows.

## <a name="how-do-i-fix-it"></a>Wie behebe ich das Problem?
Die Benachrichtigungen umfassen Diagnoseinformationen zur Unterstützung des Diagnoseprozesses:
1. **Selektierung.** Die Benachrichtigung zeigt an, wie viele Vorgänge betroffen sind. Mithilfe dieser Informationen können Sie dem Problem eine Priorität zuweisen.
2. **Umfang.** Betrifft das Problem den gesamten Datenverkehr oder nur bestimmte Vorgänge? Diese Informationen können Sie der Benachrichtigung entnehmen.
3. **Diagnose.** Zur weiteren Diagnose des Problems können Sie die entsprechenden Elemente und Berichte verwenden, die mit weiterführenden Informationen verknüpft sind.
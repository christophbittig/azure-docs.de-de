---
title: 'Problembehandlung: Warnungen'
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Probleme mit Azure Digital Twins beheben, indem Sie Warnungen basierend auf Dienstmetriken einrichten.
author: baanders
ms.author: baanders
ms.date: 10/5/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 653a710274abc4da116b0c3f3e06b93ed3fce92a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130128995"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Problembehandlung von Azure Digital Twins: Alerts

In diesem Artikel erfahren Sie, wie Sie Warnungen im [Azure-Portal](https://portal.azure.com) einrichten können. Diese Warnungen benachrichtigen Sie, wenn konfigurierbare Bedingungen erfüllt sind, die Sie basierend auf den Metriken Ihrer Azure Digital Twins-Instanz definiert haben, sodass Sie wichtige Aktionen ausführen können.

Azure Digital Twins sammelt [Metriken](troubleshoot-metrics.md) für Ihre Dienstinstanz, die Informationen zum Zustand Ihrer Ressourcen bereitstellen. Mit diesen Metriken können Sie die allgemeine Integrität des Azure Digital Twins-Diensts und der damit verbundenen Ressourcen bewerten.

**Warnungen** informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Metrikdaten gefunden werden. Sie ermöglichen es Ihnen, Probleme zu identifizieren und zu beheben, bevor die Benutzer Ihres Systems sie bemerken. Weitere Informationen zu Warnungen finden Sie unter [Überblick über Warnungen in Microsoft Azure](../azure-monitor/alerts/alerts-overview.md).

## <a name="turn-on-alerts"></a>Aktivieren von Warnungen

So aktivieren Sie Warnungen für Ihre Azure Digital Twins-Instanz:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz. Sie finden sie, indem Sie ihren Namen auf der Suchleiste des Portals eingeben. 

2. Wählen Sie im Menü die Option **Warnungen** und dann **+ Neue Warnungsregel** aus.

   :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Screenshot von Azure-Portal-mit der Schaltfläche zum Erstellen einer neuen Warnungsregel im Abschnitt „Warnungen“ einer Azure Digital Twin-Instanz." lightbox="media/troubleshoot-alerts/alerts-pre.png":::

3. Auf der Seite *Warnungsregel erstellen*, die als Nächstes angezeigt wird, können Sie die Anweisungen befolgen, um Bedingungen, die auszulösenden Aktionen und die Warnungsdetails zu definieren.     
    * Die Details unter **Bereich** sollten zusammen mit den Details für Ihre Instanz automatisch eingefügt werden.
    * Sie definieren die Details für **Bedingung** und **Aktionsgruppe**, um Warnungstrigger und die zugehörigen Antworten anzupassen.
    * Geben Sie im Abschnitt **Details zur Warnungsregel** einen Namen und optional eine Beschreibung für Ihre Regel ein. 
        - Sie können das Kontrollkästchen _Warnungsregel bei Erstellung aktivieren_ aktivieren, wenn die Warnung aktiviert werden soll, sobald sie erstellt wurde.
        - Sie können das Kontrollkästchen _Warnungen automatisch auflösen_ aktivieren, falls Sie die Warnung auflösen möchten, wenn die Bedingung nicht mehr erfüllt ist.
        - In diesem Abschnitt wählen Sie außerdem ein _Abonnement_, eine _Ressourcengruppe_ und einen _Schweregrad_ aus.

4. Wählen Sie die Schaltfläche _Warnungsregel erstellen_ aus, um die Warnungsregel zu erstellen.

   :::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Screenshot des Azure-Portals mit der Seite „Warnungsregel erstellen“ mit Abschnitten für Bereich, Bedingung, Aktionsgruppe und Details zur Warnungsregel." lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Eine Anleitung zum Ausfüllen dieser Felder finden Sie unter [Überblick über Warnungen in Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Unten sind einige Beispiele dafür angegeben, welche Schritte für Azure Digital Twins ausgeführt werden müssen.

### <a name="select-conditions"></a>Auswählen von Bedingungen

Hier ist ein Auszug aus dem Prozess *Bedingung auswählen*, der veranschaulicht, welche Arten von Warnungssignalen für Azure Digital Twins verfügbar sind. Auf dieser Seite können Sie nach dem Typ des Signals filtern und das gewünschte Signal in einer Liste auswählen.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Screenshot des Azure-Portals mit der ersten Seite „Signallogik konfigurieren“. Es gibt Hervorhebungen um das Feld „Signaltyp“ und die Liste der Metriken herum.":::

Nachdem Sie ein Signal ausgewählt haben, werden Sie aufgefordert, die Logik der Warnung zu konfigurieren. Sie können nach einer Dimension filtern und einen Schwellenwert für Ihre Warnung sowie die Häufigkeit der Überprüfungen für die Bedingung festlegen. Hier ist ein Beispiel für die Einrichtung einer Warnung für den Fall, dass der Durchschnittswert für die Metrik „Routingfehlerrate“ über 5 % steigt.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Screenshot des Azure-Portals mit der zweiten Seite „Signallogik konfigurieren“.":::

### <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

Nach dem Einrichten von Warnungen werden diese auf der Seite *Warnungen* für Ihre Instanz angezeigt.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Screenshot: Azure-Portal mit der Seite „Warnungen“ und einer Schaltfläche zum Hinzufügen. Eine Warnung ist konfiguriert." lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Warnungen mit Azure Monitor finden Sie unter [Überblick über Warnungen in Microsoft Azure](../azure-monitor/alerts/alerts-overview.md).
* Informationen zu den Azure Digital Twins-Metriken finden Sie unter [Problembehandlung: Metriken](troubleshoot-metrics.md).
* Informationen zur Aktivierung der Diagnoseprotokollierung für Ihre Metriken finden Sie unter [Problembehandlung: Diagnoseprotokolle](troubleshoot-diagnostics.md).

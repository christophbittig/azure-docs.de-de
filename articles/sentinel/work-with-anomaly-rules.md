---
title: Arbeiten mit Analyseregeln zur Anomalieerkennung in Microsoft Sentinel | Microsoft Dokumentation
description: In diesem Artikel wird erläutert, wie Sie in Microsoft Sentinel Analyseregeln zur Erkennung von Anomalien anzeigen, erstellen, verwalten, bewerten und feinabstimmen können.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1469f89877c9a20009dd3e1178b77a7f001314ba
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518772"
---
# <a name="work-with-anomaly-detection-analytics-rules-in-microsoft-sentinel"></a>Arbeit mit Analyseregeln für die Anomalieerkennung in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Anomalieregeln sind derzeit als **VORSCHAU** verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="view-soc-ml-anomaly-rule-templates"></a>Anzeigen von Vorlagen für SOC-ML-Anomalieregeln

Microsoft Sentinels [SOC-ML-Anomalie-Funktion](soc-ml-anomalies.md) bietet [eingebaute Anomalievorlagen](detect-threats-built-in.md#anomaly) für sofortigen Nutzen sofort nach der Installation. Diese Anomalievorlagen wurden anhand von Tausenden von Datenquellen und Millionen von Ereignissen entwickelt, sodass sie stabil ausgelegt sind. Mit diesem Feature können Sie jedoch auch problemlos Schwellenwerte und Parameter für Anomalien über die Benutzeroberfläche ändern. Anomalieregeln müssen aktiviert werden, bevor mit ihnen Anomalien generiert werden können. Die Anomalien finden Sie in der Tabelle **Anomalien** im Abschnitt **Protokolle**.

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Analytik**.

1. Wählen Sie auf dem Blatt **Analysen** die Registerkarte **Regelvorlagen** aus.

1. Filtern Sie die Liste nach **Anomalievorlagen**:

    1. Klicken Sie auf den Filter **Regeltyp** und dann auf die unten angezeigte Dropdownliste.

    1. Heben Sie die Markierung **Alle auswählen** auf, und markieren Sie dann die Option **Anomalie**.

    1. Klicken Sie bei Bedarf oben auf die Dropdownliste, um sie zu reduzieren, und klicken Sie dann auf **OK**.

## <a name="activate-anomaly-rules"></a>Aktivieren von Anomalieregeln

Wenn Sie auf eine der Regelvorlagen klicken, werden im Detailbereich die folgenden Informationen zusammen mit der Schaltfläche **Regel erstellen** angezeigt:

- In der **Beschreibung** wird erläutert, wie die Anomalie funktioniert und welche Daten dafür erforderlich sind.

- Mit **Datenquellen** wird der Typ der Protokolle angegeben, die erfasst werden müssen, damit eine Analyse durchgeführt werden kann.

- **Taktik** bezieht sich auf die Taktiken des MITRE ATT&CK-Frameworks, die von der Anomalie abgedeckt werden.

- **Parameter** sind die konfigurierbaren Attribute für die Anomalie.

- **Schwellenwert** ist ein konfigurierbarer Wert, der das Ausmaß angibt, in dem ein Ereignis ungewöhnlich sein muss, bevor eine Anomalie erstellt wird.

- **Regelhäufigkeit** bezeichnet die Zeit zwischen Protokollverarbeitungsaufträgen, mit denen die Anomalien ermittelt werden.

- Unter **Anomaly version** (Anomalieversion) wird die Version der Vorlage angezeigt, die von einer Regel verwendet wird. Wenn Sie die Version ändern möchten, die von einer bereits aktiven Regel verwendet wird, müssen Sie die Regel neu erstellen.

- **Template last updated** (Letzte Aktualisierung der Vorlage) bezieht sich auf das Datum, an dem die Anomalieversion geändert wurde.

Führen Sie die folgenden Schritte aus, um eine Regel zu aktivieren.

1. Wählen Sie eine Regelvorlage aus, die noch nicht mit **WIRD VERWENDET** bezeichnet ist. Klicken Sie auf die Schaltfläche **Regel erstellen**, um den Assistenten zum Erstellen von Regeln zu öffnen.

    Der entsprechende Assistent zum Erstellen einzelner Regelvorlagen sieht jeweils ein bisschen anders aus, verfügt jedoch immer über drei Schritte oder Registerkarten: **Allgemein**, **Konfiguration** und **Überprüfen und erstellen**.

    Sie können keine der Werte im Assistenten ändern. Sie müssen zuerst die Regel erstellen und aktivieren.

1. Gehen Sie die Registerkarten durch, warten Sie auf der Registerkarte **Überprüfen und erstellen** auf die Meldung „Überprüfung erfolgreich“, und klicken Sie auf die Schaltfläche **Erstellen**.

    Sie können mit jeder Vorlage nur jeweils eine aktive Regel erstellen. Sobald Sie die Schritte des Assistenten abgeschlossen haben, wird auf der Registerkarte **Aktive Regeln** eine aktive Anomalieregel erstellt, und die Vorlage (auf der Registerkarte **Regelvorlagen**) wird als **WIRD VERWENDET** markiert.

    > [!NOTE]
    > Wenn die erforderlichen Daten verfügbar sind, kann es bis zu 24 Stunden dauern, bis die neue Regel auf der Registerkarte **Aktive Regeln** angezeigt wird. Wählen Sie zum Anzeigen der neuen Regeln die Registerkarte „Aktive Regeln“ aus, und filtern Sie sie auf dieselbe Weise, wie Sie die Liste „Regelvorlagen“ oben gefiltert haben.

Sobald die Anomalie-Regel aktiviert ist, werden entdeckte Anomalien in der Tabelle **Anomalien** im Abschnitt **Logs** Ihres Microsoft Sentinel-Arbeitsbereichs gespeichert.

Jede Anomalieregel verfügt über einen Trainingszeitraum; Anomalien werden erst nach diesem Trainingszeitraum in der Tabelle angezeigt. Sie finden den Trainingszeitraum in der Beschreibung der jeweiligen Anomalieregel.

## <a name="assess-the-quality-of-anomalies"></a>Bewerten der Qualität von Anomalien

Sie können feststellen, wie gut eine Anomalieregel funktioniert, indem Sie eine Stichprobe der Anomalien überprüfen, die im Zeitraum der letzten 24 Stunden anhand einer Regel erstellt wurden. 

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Analytik**.

1. Überprüfen Sie auf dem Blatt **Analysen**, ob die Registerkarte **Aktive Regeln** ausgewählt ist.

1. Filtern Sie die Liste nach **Anomalieregeln** (wie oben beschrieben).

1. Wählen Sie die Regel aus, die Sie bewerten möchten, und kopieren Sie ihren Namen oben rechts im Detailbereich.

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Protokolle**.

1. Wenn oben ein **Abfragekatalog** angezeigt wird, schließen Sie ihn.

1. Wählen Sie im linken Bereich des Blatts **Protokolle** die Registerkarte **Tabellen** aus.

1. Legen Sie den Filter **Zeitbereich** auf **Letzte 24 Stunden** fest.

1. Kopieren Sie die Kusto-Abfrage unten, und fügen Sie sie in das Abfragefenster ein (dort, wo der Eintrag „Type your query here or...“ [Geben Sie Ihre Abfrage hier ein, oder...] angezeigt wird):

    ```kusto
    Anomalies 
    | where AnomalyTemplateName contains "________________________________"
    ```
    Fügen Sie den oben kopierten Regelnamen anstelle der Unterstriche zwischen den Anführungszeichen ein.

1. Klicken Sie auf **Ausführen**. 

Wenn Sie einige Ergebnisse haben, können Sie damit beginnen, die Qualität der Anomalien zu bewerten. Wenn es keine Ergebnisse gibt, versuchen Sie, den Zeitbereich zu erhöhen.

Erweitern Sie die Ergebnisse für jede Anomalie, und erweitern Sie dann das Feld **AnomalyReasons** (Anomaliegründe). Dadurch erfahren Sie, warum die Anomalie ausgelöst wurde.

Die „Plausibilität“ oder „Nützlichkeit“ einer Anomalie kann von den Bedingungen Ihrer Umgebung abhängen, aber ein häufiger Grund dafür, dass von einer Anomalieregel zu viele Anomalien erzeugt werden, besteht darin, dass der Schwellenwert zu niedrig ist.

## <a name="tune-anomaly-rules"></a>Optimieren von Anomalieregeln

Obwohl Anomalieregeln so konzipiert sind, dass sie bei der Verwendung sofort die maximale Effektivität aufweisen, ist jede Situation einzigartig, und so müssen Anomalieregeln manchmal optimiert werden.

Da Sie eine ursprüngliche aktive Regel nicht bearbeiten können, müssen Sie eine aktive Anomalieregel zunächst duplizieren und dann die Kopie entsprechend anpassen.

Die ursprüngliche Anomalieregel wird weiterhin ausgeführt, bis Sie sie entweder deaktivieren oder löschen.

Dies ist absichtlich so angelegt, damit Sie die Möglichkeit haben, die mit der ursprünglichen Konfiguration generierten Ergebnisse mit den Ergebnissen der neuen Konfiguration zu vergleichen. Doppelte Regeln sind standardmäßig deaktiviert. Sie können nur eine einzige benutzerdefinierte Kopie einer bestimmten Anomalieregel erstellen. Versuche, eine zweite Kopie zu erstellen, schlagen fehl.

1. Wählen Sie zum Ändern der Konfiguration einer Anomalieregel die Anomalieregel auf der Registerkarte **Aktive Regeln** aus.

1. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Zeile der Regel, oder klicken Sie mit der linken Maustaste am Ende der Zeile auf die Auslassungspunkte (...), und klicken Sie dann auf **Duplizieren**.

1. Die neue Kopie der Regel enthält im Regelnamen das Suffix „ – Customized“ (Angepasst). Wählen Sie diese Regel aus, und klicken Sie auf **Bearbeiten**, um diese Regel konkret anzupassen.

1. Die Regel wird im Analyseregel-Assistenten geöffnet. Hier können Sie die Parameter der Regel und ihren Schwellenwert ändern. Die Parameter, die geändert werden können, variieren je nach Anomalietyp und Algorithmus.

    Sie können eine Vorschau der Ergebnisse Ihrer Änderungen im **Bereich der Ergebnisvorschau** anzeigen. Klicken Sie in der Ergebnisvorschau auf eine **Anomalie-ID**, um nachzuvollziehen, warum diese Anomalie vom ML-Modell identifiziert wurde.

1. Aktivieren Sie die benutzerdefinierte Regel, um Ergebnisse zu generieren. Einige Änderungen erfordern möglicherweise, dass die Regel erneut ausgeführt wird. Daher müssen Sie warten, bis die Ausführung abgeschlossen ist, und zurückkehren, um die Ergebnisse auf der Protokollseite zu überprüfen. Die benutzerdefinierte Anomalieregel wird standardmäßig im **Test-Flighting**-Modus (Testmodus) ausgeführt. Die ursprüngliche Regel wird standardmäßig weiterhin im **Produktionsmodus** ausgeführt.

1. Zum Vergleichen der Ergebnisse wechseln Sie zurück zu der unter **Protokolle** verfügbaren Anomalientabelle, um [die neue Regel wie zuvor zu bewerten](#assess-the-quality-of-anomalies). Suchen Sie nur nach Zeilen mit dem ursprünglichen Regelnamen sowie dem Regelnamenduplikat, das in der Spalte **AnomalyTemplateName** (Anomalievorlagenname) mit dem Zusatz „ – Customized“ (Angepasst) aufgeführt ist.

    Wenn Sie mit den Ergebnissen für die benutzerdefinierte Regel zufrieden sind, können Sie zurück zur Registerkarte **Aktive Regeln** wechseln, auf die benutzerdefinierte Regel klicken, auf die Schaltfläche **Bearbeiten** klicken und auf der Registerkarte **Allgemein** von **Test-Flighting** zu **Produktion** wechseln. Die ursprüngliche Regel wird automatisch in **Test-Flighting** geändert, da Sie nicht gleichzeitig zwei Versionen derselben Regel in der Produktion verwenden können. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie mit SOC-ML-Anomalieerkennungsanalyseregeln in Microsoft Sentinel arbeiten können.

- Hier erhalten Sie Hintergrundinformationen zu [SOC-ML](soc-ml-anomalies.md).
- Informieren Sie sich über andere [Typen von Analyseregeln](detect-threats-built-in.md).

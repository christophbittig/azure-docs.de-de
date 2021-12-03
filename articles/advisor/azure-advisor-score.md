---
title: Optimieren von Azure-Workloads mithilfe der Advisor-Bewertung
description: Verwenden Sie die Azure Advisor-Bewertung, um Azure optimal zu nutzen.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: fc9bcee7d19d9e8ed7b550e7298d615d397c6f8b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484721"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Optimieren von Azure-Workloads mithilfe der Advisor-Bewertung

## <a name="introduction-to-advisor-score"></a>Einführung in die Advisor-Bewertung

Azure Advisor bietet Empfehlungen zu bewährten Methoden für Ihre Workloads. Diese Empfehlungen sind personalisiert und umsetzbar, um Ihnen bei Folgendem zu helfen:

* Verbessern des Status Ihrer Workloads und Optimieren Ihrer Azure-Bereitstellungen
* Proaktives Vermeiden der häufigsten Probleme durch Befolgen bewährter Methoden
* Bewerten Ihrer Azure-Workloads anhand der fünf Säulen des [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)

Als ein zentrales Feature von Advisor ist die Advisor-Bewertung entwickelt worden, die Ihnen helfen kann, diese Ziele effektiv und effizient zu erreichen.

Für die optimale Nutzung von Azure ist es entscheidend zu verstehen, an welcher Position Sie sich bei der Optimierung Ihres Workloads befinden. Sie müssen wissen, welche Dienste oder Ressourcen angemessen genutzt werden und welche nicht. Darüber hinaus sollten Sie wissen, wie Sie auf der Grundlage von Empfehlungen Prioritäten setzen können, um das Ergebnis zu maximieren.

Es ist ebenfalls wichtig, den Fortschritt, den Sie auf dieser Optimierungsjourney machen, nachzuverfolgen und zu melden. Mit der Advisor-Bewertung können Sie all diese Punkte mit unserer neuen Gamification-Umgebung ganz einfach erreichen.

Als Ihr persönlicher Cloudberater bewertet Azure Advisor kontinuierlich Ihre Nutzungstelemetrie und Ressourcenkonfiguration, um auf bewährte Methoden der Branche zu prüfen. Der Advisor aggregiert dann seine Ergebnisse in einer einzelnen Bewertung. Mit diesem Ergebnis können Sie auf einen Blick erkennen, ob Sie die erforderlichen Schritte unternehmen, um zuverlässige, sichere und kosteneffiziente Lösungen zu erstellen.

Das Advisor-Ergebnis besteht aus einer Gesamtbewertung, die weiter in fünf Kategorieergebnisse aufgeteilt werden kann. Ein Ergebnis für jede Kategorie von Advisor stellt die fünf Säulen des Well-Architected Framework dar.

Sie können Ihre Fortschritte im zeitlichen Ablauf verfolgen, indem Sie Ihre Gesamtbewertung und Kategoriebewertungen mit täglichen, wöchentlichen und monatlichen Trends anzeigen. Darüber hinaus können Sie Benchmarks festlegen, um Sie beim Erreichen ihrer Ziele zu unterstützen.

 ![Screenshot der Advisor-Bewertungsseite](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Interpretieren einer Advisor-Bewertung

Advisor zeigt Ihre gesamte Advisor-Bewertung und eine Aufschlüsselung nach Advisor-Kategorien in Prozent an. Eine Bewertung von 100 % in einer beliebigen Kategorie bedeutet, dass alle Ihre von Advisor bewerteten Ressourcen die bewährten Methoden befolgen, die von Advisor empfohlen wurden. Am anderen Ende des Spektrums bedeutet ein Wert von 0 %, dass keine Ihrer von Advisor bewerteten Ressourcen den Empfehlungen von Advisor folgt. Mithilfe dieser Bewertungseinheiten können Sie ganz einfach den folgenden Flow erreichen:

* **Advisor-Bewertung** hilft Ihnen dabei, die Grundwerte Ihrer Workload oder Abonnements auf Basis einer Advisor-Bewertung zu ermitteln. Sie können sich auch die historischen Trends ansehen, um zu verstehen, wie Ihr Trend verläuft.
* **Bewertung nach Kategorie** für jede Empfehlung gibt an, durch welche ausstehenden Empfehlungen sich die Bewertung am meisten verbessert. Diese Werte berücksichtigen sowohl die Gewichtung der Empfehlung als auch die vorhergesagte Einfachheit der Implementierung. Mit diesen Faktoren können Sie sicherstellen, dass Sie für ihre Zeit den größten Nutzen erhalten. Sie unterstützen Sie außerdem bei der Priorisierung.
* **Auswirkung der Kategoriebewertung** für die einzelnen Empfehlungen hilft Ihnen bei der Priorisierung Ihrer Aktionen zur Problembehebung für die einzelnen Kategorien.

Der Anteil der einzelnen Empfehlungen an der Kategoriebewertung wird auf der Seite der **Advisor-Bewertung** im Azure-Portal eindeutig angezeigt. Sie können jede Kategoriebewertung um den in der Spalte **Potenzielle Bewertungssteigerung** aufgeführten Prozentpunkt erhöhen. Dieser Wert gibt sowohl die Gewichtung der Empfehlung innerhalb der Kategorie als auch die vorhergesagte einfache Umsetzbarkeit an, um die potenziell am einfachsten zu erledigenden Aufgaben zu ermitteln. Wenn Sie sich auf die Empfehlungen mit der größten Bewertungsauswirkung konzentrieren, können Sie mit der Zeit die größten Fortschritte erzielen.

![Screenshot mit Auswirkungen der Advisor-Bewertung](./media/advisor-score-2.png)

Wenn eine Advisor-Empfehlung für eine einzelne Ressource nicht relevant ist, können Sie die betreffende Empfehlung verschieben oder verwerfen. Sie wird dann bei der nächsten Aktualisierung aus der Berechnung der Bewertung ausgeschlossen. Advisor verwendet diese Eingabe auch als zusätzliches Feedback zur Verbesserung des Modells.

## <a name="how-is-an-advisor-score-calculated"></a>Wie wird eine Advisor-Bewertung berechnet?

Advisor zeigt die Kategoriebewertungen und die Advisor-Gesamtbewertung als Prozentwerte an. Eine Bewertung von 100 % in einer beliebigen Kategorie bedeutet, dass alle Ihre *von Advisor bewerteten* Ressourcen die bewährten Methoden befolgen, die von Advisor empfohlen wurden. Am anderen Ende des Spektrums bedeutet ein Wert von 0 %, dass keine Ihrer von Advisor bewerteten Ressourcen den Empfehlungen von Advisor folgt.

**Jede der fünf Kategorien hat eine höchstmögliche potenzielle Bewertung von 100.** Die Advisor-Gesamtbewertung errechnet sich aus der Summe aller anwendbaren Kategorien, geteilt durch die Summe der höchsten potenziellen Bewertung aus allen anwendbaren Kategorien. Für die meisten Abonnements bedeutet dies, dass Advisor die Bewertung aus den einzelnen Kategorien addiert und durch 500 teilt. *Die Bewertung für die einzelnen Kategorien wird jedoch nur berechnet, wenn Sie Ressourcen verwenden, die von Advisor bewertet werden.*

### <a name="advisor-score-calculation-example"></a>Beispiel für die Berechnung der Advisor-Bewertung

* **Bewertung für ein Einzelabonnement:** Dieses Beispiel ist der einfache Mittelwert aller Advisor-Kategoriebewertungen für Ihr Abonnement. Wenn die Advisor-Kategoriebewertungen wie folgt lauten: **Kosten** = 73, **Zuverlässigkeit** = 85, **optimaler Betrieb** = 77 und **Leistung** = 100, ergibt die Advisor-Bewertung (73 + 85 + 77 + 100) / (4 × 100) = 0,84 oder 84 %.
* **Bewertung mehrerer Abonnements:** Wenn mehrere Abonnements ausgewählt werden, werden die generierten Advisor-Gesamtbewertungen aus den gewichteten aggregierten Kategoriebewertungen errechnet. Hierbei wird jede Advisor-Kategoriebewertung basierend auf den von den Abonnements genutzten Ressourcen aggregiert. Nachdem Advisor über die gewichteten aggregierten Kategoriebewertungen verfügt, führt Advisor eine einfache Mittelwertberechnung durch, um Ihnen eine Gesamtbewertung für Abonnements an die Hand zu geben.

### <a name="scoring-methodology"></a>Bewertungsmethodik

Die Berechnung der Advisor-Bewertung lässt sich in vier Schritten zusammenfassen:

1. Advisor berechnet die *Nettokosten der betroffenen Ressourcen*. Dabei handelt es sich um die Ressourcen in ihren Abonnements, für die mindestens eine Empfehlung in Advisor vorhanden ist.
1. Advisor berechnet den *Stückpreis der bewerteten Ressourcen*. Dabei handelt es sich um die Ressourcen, die von Advisor überwacht werden, unabhängig davon, ob für sie Empfehlungen vorliegen oder nicht.
1. Für jeden Empfehlungstyp berechnet Advisor das *Verhältnis fehlerfreier Ressourcen*. Dieses Verhältnis ist der Stückpreis der betroffenen Ressourcen geteilt durch den Stückpreis der bewerteten Ressourcen.
1. Advisor wendet drei zusätzliche Gewichtungen auf das Verhältnis fehlerfreier Ressourcen in den einzelnen Kategorien an:

   * Empfehlungen mit größerer Auswirkung werden höher gewichtet als Empfehlungen mit geringerer Auswirkung.
   * Ressourcen mit langfristigen Empfehlungen werden bei Ihrer Bewertung höher bewertet.
   * Die in Advisor zurückgestellten oder verworfenen Ressourcen werden vollständig aus der Berechnung Ihrer Bewertung entfernt.

Advisor wendet dieses Modell auf der Kategorieebene des Ratgebers an, um für jede Kategorie eine Advisor-Bewertung anzugeben. **Sicherheit** verwendet ein [Sicherheitsbewertungsmodell](../defender-for-cloud/secure-score-security-controls.md#introduction-to-secure-score). Ein einfacher Mittelwert bildet die endgültige Advisor-Bewertung.

## <a name="advisor-score-faqs"></a>Häufig gestellte Fragen zur Advisor-Bewertung

### <a name="how-often-is-my-score-refreshed"></a>Wie oft wird die Bewertung aktualisiert?

Ihre Bewertung wird mindestens einmal pro Tag aktualisiert.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Warum enthalten einige Empfehlungen einen leeren Wert für „-“ in der Spalte für die Auswirkung der Kategoriebewertung?

Advisor fügt neue Empfehlungen oder Empfehlungen mit aktuellen Änderungen nicht sofort dem Bewertungsmodell hinzu. Nach einer kurzen Evaluierungszeit (in der Regel einige Wochen) werden sie in Bewertungen berücksichtigt.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Warum ist die Auswirkung der Kostenbewertung bei einigen Empfehlungen größer, auch wenn sie geringere potenzielle Einsparungen aufweisen?

Ihre Bewertung **Kosten** gibt sowohl Ihre potenziellen Einsparungen durch nicht ausgelastete Ressourcen als auch die vorhergesagte einfache Umsetzbarkeit dieser Empfehlungen an. Beispielsweise werden betroffene Ressourcen, die längere Zeit nicht genutzt wurden, stärker gewichtet, auch wenn die potenziellen Einsparungen geringer sind.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Warum erhalte ich keine Bewertung für einzelne oder mehrere Kategorien oder Abonnements?

Eine Bewertung wird nur für die Kategorien und Abonnements generiert, die Ressourcen enthalten, die von Advisor bewertet werden.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Was geschieht, wenn eine Empfehlung nicht relevant ist?

Wenn Sie eine Empfehlung von Advisor verwerfen, wird sie bei der Berechnung Ihrer Bewertung nicht berücksichtigt. Das Verwerfen von Empfehlungen hilft Advisor auch, die Qualität der Empfehlungen zu verbessern.

### <a name="why-did-my-score-change"></a>Warum hat sich meine Bewertung geändert?

Ihre Bewertung kann sich ändern, wenn Sie betroffene Ressourcen durch die Anwendung der von Advisor empfohlenen bewährten Methoden korrigieren. Wenn Sie oder Personen mit Berechtigungen für Ihr Abonnement neue Ressourcen geändert oder erstellt haben, ergeben sich möglicherweise auch Schwankungen in ihrem Ergebnis. Ihr Ergebnis basiert auf einem Verhältnis der durch Kosten beeinflussten Ressourcen relativ zu den Gesamtkosten aller Ressourcen.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Wie berechnet Advisor die Einzelhandelskosten für Ressourcen in einem Abonnement?

Advisor legt die Tarife für die nutzungsbasierte Bezahlung zugrunde, die in den [Azure-Preisen](https://azure.microsoft.com/pricing/) veröffentlicht sind. Diese Tarife berücksichtigen keine eventuell anwendbaren Rabatte. Die Tarife werden dann mit der Nutzungsmenge am letzten Tag multipliziert, an dem die Ressource zugewiesen war. Durch das Fortlassen von Rabatten bei der Berechnung der Ressourcenkosten sind Advisor-Bewertungen zwischen Abonnements, Mandanten und Registrierungen vergleichbar, bei denen die Rabatte variieren können.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Muss ich die Empfehlungen in Advisor anzeigen, um Punkte für meine Bewertung zu erhalten?

Nein. Ihre Bewertung gibt an, ob Sie von Advisor empfohlene bewährte Methoden umsetzen, auch wenn Sie die bewährten Methoden proaktiv übernehmen und die Empfehlungen nie in Advisor anzeigen.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>Unterscheidet die Bewertungsmethodik zwischen Produktions- und Dev/Test-Workloads?

Nein, derzeit nicht. Sie können aber Empfehlungen zu einzelnen Ressourcen verwerfen, wenn diese Ressourcen für Entwicklung und Tests verwendet werden und die Empfehlung für sie nicht zutrifft.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Kann ich Bewertungen zwischen einem Abonnement mit 100 Ressourcen und einem Abonnement mit 100.000 Ressourcen vergleichen?

Die Bewertungsmethodik ist so konzipiert, dass die Anzahl der Ressourcen in einem Abonnement und einer Dienstkombination gesteuert wird. Abonnements mit weniger Ressourcen können eine höhere oder niedrigere Bewertung aufweisen als Abonnements mit mehr Ressourcen.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Was bedeutet es, wenn in der Spalte für die Auswirkung der Bewertung „In Kürze verfügbar“ angezeigt wird?

Diese Meldung bedeutet, dass die Empfehlung neu ist und wir daran arbeiten, sie in das Advisor-Bewertungsmodell zu integrieren. Nachdem diese neue Empfehlung bei der Berechnung der Bewertung berücksichtigt wird, wird der Bewertungswert der Auswirkung für die Empfehlung angezeigt.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Hängt meine Bewertung von meinen Ausgaben für Azure ab?

Nein. Ihr Ergebnis spiegelt nicht zwangsläufig die Höhe Ihrer Ausgaben wieder. Unnötige Ausgaben führen zu einer niedrigeren Bewertung bei den **Kosten**.

## <a name="access-advisor-score"></a>Zugang zur Advisor-Bewertung

Die Advisor-Bewertung befindet sich im Azure-Portal in der öffentlichen Vorschau. Im linken Bereich im Abschnitt **Advisor** finden Sie weitere Informationen unter **Advisor-Bewertung**.

![Screenshot vom Einstiegspunkt der Advisor-Bewertung](./media/advisor-score-3.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Advisor-Empfehlungen finden Sie unter:

* [Einführung in Advisor](advisor-overview.md)
* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Reduzieren der Dienstkosten mithilfe des Azure Advisors](advisor-cost-recommendations.md)
* [Verbessern der Leistung von Azure-Anwendungen mit dem Azure Advisor](advisor-performance-recommendations.md)
* [Erhöhen der Sicherheit von Ressourcen mit dem Azure Advisor](advisor-security-recommendations.md)
* [Sicherstellen des optimalen Betriebs mit dem Azure Advisor](advisor-operational-excellence-recommendations.md)

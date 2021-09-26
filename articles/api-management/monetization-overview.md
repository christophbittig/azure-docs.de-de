---
title: Monetarisierung mit Azure API Management
description: Lernen Sie, wie Sie Ihre Monetarisierungsstrategie für Azure API Management in sechs einfachen Stages einrichten.
author: dlepow
ms.author: danlep
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 222a253d35775bd89e6042181937d6c7a7ff624c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594159"
---
# <a name="monetization-with-azure-api-management"></a>Monetarisierung mit Azure API Management

Moderne Web-APIs unterstützen die digitale Wirtschaft. Sie stellen das geistige Eigentum (IP) eines Unternehmens für Dritte bereit und generieren Umsatz durch:

- Verpacken von geistigem Eigentum in Form von Daten, Algorithmen oder Prozessen.
- Ermöglicht es anderen Parteien, nützliches geistiges Eigentum konsistent und reibungslos zu ermitteln und zu nutzen.
- Bietet einen Mechanismus für direkte oder indirekte Zahlungen für diese Nutzung.

Ein gemeinsames Thema der API-Erfolgsgeschichten ist ein *gesundes Geschäftsmodell*. Es wird auf nachhaltige Weise ein Wert zwischen allen Parteien geschaffen und ausgetauscht.

Start-ups, eingerichtete Organisationen und alles dazwischen versuchen in der Regel, eine digitale Transformation zu erreichen und beginnen mit dem Geschäftsmodell. APIs ermöglichen die Umsetzung des Geschäftsmodells, wodurch eine einfachere und kostengünstigere Möglichkeit für Marketing, Einführung, Verbrauch und Skalierung des zugrunde liegenden geistigen Eigentums ermöglicht wird.

Organisationen, die ihre erste API veröffentlichen, stehen vor einem komplexen Satz von Entscheidungen. Während die Azure API Management-Plattform Risiken deeskaliert und wichtige Elemente beschleunigt, müssen Organisationen ihre API weiterhin auf der Basis ihres einzigartigen technischen und geschäftsmäßigen Modells konfigurieren und aufbauen.

## <a name="developing-a-monetization-strategy"></a>Entwicklung einer Monetarisierungsstrategie

*Monetarisierung* ist der Prozess der etwas in Geld umwandelt, in diesem Fall der Wert der API. API-Interaktionen umfassen in der Regel drei unterschiedliche Parteien in der Kette der Werte:

:::image type="content" source="media/monetization-overview/values-overview.png" alt-text="Diagramm der Monetarisierungs-Wertkette":::

Die Kategorien der API-Monetarisierungsstrategie umfassen:

| API-Monetarisierungsstrategie | BESCHREIBUNG |
| ----- | ----- |
| **Free** | Eine API erleichtert die Integration von Unternehmen zu Unternehmen, z. B. das Optimieren einer Lieferkette. Die API wird nicht monetarisiert, bietet aber einen erheblichen Nutzen, da sie die Effizienz von Geschäftsprozessen sowohl für den API-Anbieter als auch für den API-Consumer ermöglicht. |
| **Der Consumer zahlt** | API-Consumer zahlen basierend auf der Anzahl der Interaktionen, die sie mit der API durchführen. Wir konzentrieren uns in diesem Dokument auf diese Vorgehensweise. |
| **Der Consumer wird bezahlt** | Beispielsweise verwendet ein API-Consumer die API, um Werbung in seine Website einzubetten, und erhält einen Anteil von dem generierten Umsatz. |
| **Indirekte Monetarisierung** | Die API-Monetarisierung wird nicht durch die Anzahl der Interaktionen mit der API, sondern durch andere von der API bereitgestellte Umsatzquellen betrieben. |

>[!NOTE]
>Die Monetarisierungsstrategie wird vom API-Anbieter festgelegt und sollte so gestaltet werden, dass sie den Anforderungen des API-Consumers entspricht.

Da eine Vielzahl von Faktoren die Gestaltung beeinflussen, gibt es für die Monetarisierung von APIs keine Einheitslösung, die für alle passt. Mit der Monetarisierungsstrategie unterscheidet sich Ihre API von Ihren Konkurrenten und maximiert Ihren generierten Umsatz.

Die folgenden Schritten erklären, wie Sie eine Monetarisierungsstrategie für Ihre API implementieren.

:::image type="content" source="media/monetization-overview/implementing-strategy.png" alt-text="Diagramm der Schritte zum Implementieren Ihrer Monetarisierungsstrategie":::

### <a name="step-1---understand-your-customer"></a>Schritt 1 – Verstehen Sie Ihre Kunden

1. Entwerfen Sie die Etappen der wahrscheinlichen Reise Ihrer API-Kunden, von der ersten Entdeckung Ihrer API bis zur maximalen Skalierung.

    Eine Reihe von Kundenstages kann beispielsweise folgende sein:

    | Kundenstage | BESCHREIBUNG |
    | ----- | ----- |
    | **Untersuchung** | Ermöglichen Sie es dem API-Consumer, Ihre API kostenlos und reibungslos auszuprobieren. |
    | **Implementierung** | Stellen Sie einen ausreichenden Zugang zur API bereit, um die Entwicklungs- und Testarbeit zu unterstützen, die für die Integration mit der API erforderlich ist. |
    | **Vorschau** | Erlauben Sie dem Kunden, sein Angebot herauszubringen und die anfängliche Nachfrage zu verstehen. |
    | **Verwendung der anfänglichen Produktion** | Unterstützen Sie die frühzeitige Einführung der in Produktion befindlichen API, wenn der Nutzungsgrad noch nicht vollständig verstanden ist und ein risikoadverser Ansatz notwendig sein kann. |
    | **Anfängliches Wachstum** | Ermöglichen Sie es dem API-Consumer, die Nutzung der API in Reaktion auf eine erhöhte Nachfrage von Endbenutzern zu erhöhen. |
    | **Skalieren** | Sie sollten den API-Consumer dazu bringen, sich für ein höheres Volumen von Käufen zu interessieren, sobald die API jeden Monat konsistent ein hohes Nutzungsniveau erreicht. |
    | **Globales Wachstum** | Belohnen Sie die API-Nutzer, die die API in globalem Maßstab nutzen, indem Sie den optimalen Großhandelspreis anbieten. |

1. Analysieren Sie den Wert, den Ihre API für den Kunden in jeder Stage seiner Reise generiert. 
1. Ziehen Sie die Anwendung einer wertorientierten Preisstrategie in Betracht, wenn der direkte Wert der API für den Kunden gut verstanden ist.
1. Berechnen Sie die voraussichtliche Nutzungsdauer der API für einen Kunden und die erwartete Anzahl der Kunden während der Lebensdauer der API.

### <a name="step-2---quantify-the-costs"></a>Schritt 2 – Quantifizieren Sie die Kosten

Berechnen Sie die Gesamtkosten für Ihre API.

| Kosten | BESCHREIBUNG |
| ----- | ----- |
| **Kosten der Kundengewinnung (COCA)** | Die Kosten für Marketing, Vertrieb und Onboarding. Die erfolgreichsten APIs haben in der Regel einen COCA-Wert von Null, wenn die Akzeptanz steigt. APIs sollten beim Onboarding größtenteils -selbstbedienbar sein. Zu den Faktoren gehören Dokumentation und reibungslose Integration in Zahlungssysteme. |
| **Engineeringkosten** | Die Personalressourcen, die für die Erstellung, das Testen, den Betrieb und die Wartung der API während ihrer Lebensdauer notwendig sind. Dies ist in der Regel die wichtigste Kostenkomponente. Nutzen Sie nach Möglichkeit Cloud PaaS und serverlose Technologien, um die Kosten zu minimieren. |
| **Infrastrukturkosten** | Die Kosten für die zugrundeliegenden Plattformen, Rechen-, Netzwerk- und Speicherkapazitäten, die zur Unterstützung der API während ihrer Lebensdauer erforderlich sind. Nutzen Sie Cloudplattformen, um ein Infrastrukturkostenmodell zu erzielen, das proportional entsprechend den API-Nutzungsstufen zentral hochskaliert wird. |

### <a name="step-3---conduct-market-research"></a>Schritt 3: Durchführen von Marktanalysen

1. Erforschen Sie den Markt, um Konkurrenten zu identifizieren. 
1. Analysieren Sie die Monetarisierungsstrategien der Mitbewerber. 
1. Verstehen Sie die spezifischen Funktionen (funktionale und nicht-funktionale), die sie mit ihrer API anbieten.

### <a name="step-4---design-the-revenue-model"></a>Schritt 4 – Entwurf des Ertragsmodells

Entwerfen Sie ein Ertragsmodell auf der Grundlage der Ergebnisse der obigen Schritte. Sie können in zwei Dimensionen arbeiten:

| Dimension | Beschreibung |
| --------- | ----------- |
| **Dienstqualität** | Schränken Sie das von Ihnen angebotene Dienstleistungsniveau ein, indem Sie eine Obergrenze für die API-Nutzung festlegen. Legen Sie ein Kontingent für die API-Aufrufe fest, die über einen bestimmten Zeitraum getätigt werden können (z. B. 50.000 Anrufe pro Monat), und blockieren Sie dann die Anrufe, sobald diese Quote erreicht ist. <br> Sie können auch ein Ratenlimit festlegen, um die Anzahl der Anrufe zu drosseln, die in einem kurzen Zeitraum getätigt werden können (z. B. 100 Anrufe pro Sekunde). <br> Obergrenzen und Ratenbegrenzungen werden zusammen angewendet, um zu verhindern, dass Nutzer ihr monatliches Kontingent in einem kurzen, intensiven Burst von API-Anrufen aufbrauchen. |
| **Preis** | Definieren Sie den Einzelpreis, der für jeden API-Aufruf bezahlt werden soll. |

Maximieren Sie den Lifetime Value (LTV), den Sie von jedem Kunden generieren, indem Sie ein Ertragsmodell entwickeln, das Ihren Kunden in jeder Stage der Customer Journey unterstützt.

1. Machen Sie es Ihren Kunden so einfach wie möglich, zu skalieren und zu wachsen:
    - Schlagen Sie Ihren Kunden vor, die nächste Stufe des Ertragsmodells zu erreichen. 
    - Belohnen Sie zum Beispiel Kunden, die ein höheres Volumen an API-Aufrufen erwerben, mit einem niedrigeren Einheitenpreis.
1. Halten Sie das Ertragsmodell so einfach wie möglich:
    - Sorgen Sie für ein Gleichgewicht zwischen der Notwendigkeit, Wahlmöglichkeiten zu bieten, und dem Risiko, die Kunden mit einem Array von Optionen zu überfordern. 
    - Halten Sie die Anzahl der Dimensionen, die zur Unterscheidung zwischen den verschiedenen Ebenen des Ertragsmodells verwendet werden, gering.
1. Seien Sie transparent:
    - Stellen Sie eine klare Dokumentation über die verschiedenen Optionen bereit.
    - Geben Sie Ihren Kunden Tools an die Hand, mit denen sie das für ihre Bedürfnisse am besten geeignete Ertragsmodell auswählen können.

Ermitteln Sie die Bandbreite der erforderlichen Preismodelle. Ein *Preismodell* beschreibt einen bestimmten Satz von Regeln für den API-Anbieter, um die Nutzung durch den API-Kunden in Ertrag umzuwandeln.

Um beispielsweise die [oben genannten Kundenstages](#step-1---understand-your-customer) zu unterstützen, benötigen wir sechs Arten von Abonnements:

| Abonnementtyp | BESCHREIBUNG |
| ----- | ----- |
| `Free` | Ermöglicht es dem API-Kunden, die API unverbindlich und kostenlos zu testen, um festzustellen, ob sie einen Anwendungsfall erfüllt. Beseitigt alle Zugangsbarrieren. |
| `Freemium` | Ermöglicht dem API-Kunden die kostenlose Nutzung der API, aber den Übergang zu einem kostenpflichtigen Dienst, wenn die Nachfrage steigt. |
| `Metered` | Der API-Kunde kann pro Monat so viele Anrufe tätigen, wie er möchte, und zahlt einen festen Betrag pro Anruf. |
| `Tier` | Der API-Kunde zahlt für eine bestimmte Anzahl von Anrufen pro Monat. Überschreitet er diese Grenze, zahlt er einen Überschreitungsbetrag pro zusätzlichen Anruf. Bei regelmäßiger Überschreitung kann er auf die nächsthöhere Stufe aufsteigen. |
| `Tier + Overage` | Der API-Kunde zahlt für eine bestimmte Anzahl von Anrufen pro Monat. Wenn sie diesen Grenzwert überschreiten, zahlen sie einen festgelegten Betrag pro zusätzlichem Anruf. |
| `Unit` | Der API-Kunde zahlt für eine bestimmte Anzahl von Anrufen pro Monat. Wenn er diese Grenze überschreitet, muss er für eine weitere Einheit von Anrufen bezahlen. |

Ihr Ertragsmodell legt die Menge der API-Produkte fest. Jedes API-Produkt implementiert ein spezifisches Preismodell, das auf eine bestimmte Stage im Lebenszyklus des API-Kunden abzielt.

Obwohl sich die Preismodelle im Allgemeinen nicht ändern sollten, müssen Sie möglicherweise die Konfiguration und Anwendung der Preismodelle für Ihr Ertragsmodell anpassen. Zum Beispiel können Sie Ihre Preise anpassen, um mit denen eines Mitbewerbers gleichzuziehen.

Ausgehend von den obigen Beispielen könnten die Preismodelle wie folgt angewandt werden, um ein Gesamtertragsmodell zu erstellen:

| Stages des Kundenlebenszyklus | Preismodell | Konfiguration des Preismodells | Quality of Service (QoS, Dienstqualität) |
| ------------------------ | ------------------------- | ------------------ | ----------------------------------------------------------------------------------------------------------- |
| Untersuchung | Kostenlos | Nicht implementiert. | Kontingent festgelegt, um den Consumer auf 100 Anrufe/Monat zu beschränken. |
| Implementierung | Freemium | Gestaffelte Ebenen: <ul> <li>Der Pauschalbetrag der ersten Ebene beträgt $0.</li> <li>Die nächsten Ebenen berechnen pro Einheit 0,20 USD/100 Anrufe.</li></ul> | Keine Kontingente festgelegt. Der Verbraucher kann weiterhin Anrufe tätigen und dafür bezahlen, wenn er 100 Anrufe/Minute tätigt. |
| Vorschau | Gezählt | Preis so eingestellt, dass dem Verbraucher 0.15 USD/100 Anrufe berechnet werden. | Keine Kontingente festgelegt. Der Verbraucher kann weiterhin Anrufe tätigen und für diese bezahlen, wobei das Tariflimit bei 200 Anrufen/Minute liegt. |
| Anfängliche Nutzung der Produktion | Tarif | Preis so eingestellt, dass dem Verbraucher 14.95 USD/100 Anrufe berechnet werden. | Das Kontingent wird festgelegt, um den Consumer auf 50.000 Anrufe/Monat mit einem Tariflimit von 100 Anrufen/Minute zu beschränken. |
| Anfängliches Wachstum | Ebene und Überschreitung | Gestaffelte Ebenen: <ul><li>Der Pauschalbetrag der ersten Ebene beträgt 89,95 USD/Monat für die ersten 100.000 Aufrufe.</li><li>Die nächsten Ebenen berechnen pro Einheit 0,10 USD/100 Anrufe.</li></ul> | Keine Kontingente festgelegt. Der Verbraucher kann weiterhin Anrufe tätigen und für diese bezahlen, wobei das Tariflimit bei 100 Anrufen/Minute liegt. |
| Skalieren | Ebene und Überschreitung | Gestaffelte Ebenen:<ul><li>Der Pauschalbetrag der ersten Ebene beträgt 449,95 USD/Monat für die ersten 500.000 Aufrufe.</li><li>Die nächsten Ebenen berechnen pro Einheit 0,06 USD/100 Anrufe.</li></ul> | Keine Kontingente festgelegt. Der Verbraucher kann weiterhin Anrufe tätigen und für diese bezahlen, wobei das Tariflimit bei 1,200 Anrufen/Minute liegt. |
| Globales Wachstum | Einheit | Gestaffelte Ebenen, bei denen jeder Ebenen-Pauschalbetrag 749,95 USD/Monat für 1.500.000 Aufrufe beträgt. | Keine Kontingente festgelegt. Der Verbraucher kann weiterhin Anrufe tätigen und für diese bezahlen, wobei das Tariflimit bei 3,500 Anrufen/Minute liegt. |

**Zwei Beispiele für die Interpretation des Ertragsmodells basierend auf der obigen Tabelle:**

* **Ebenen-Preismodell**  
   Eingesetzt zur Unterstützung von API-Consumern während der **ersten Produktionsphase** des Lebenszyklus. Bei der Konfiguration des Ebenen-Preismodells zahlt der Consumer:  
    * Zahlt 14,95 USD/Monat.  
    * Er kann bis zu 50.000 Anrufe/Monat tätigen. 
    * Der Tarif ist auf 100 Anrufe/Minute begrenzt.

* **Skalieren der Phase des Lebenszyklus** Wird durch Einsatz des Preismodells **Ebene + Überschreitung** implementiert, bei dem die Kunden:
    * 449,95 USD/Monat für die ersten 500.000 Anrufe zahlen. 
    * Nach den ersten 50.000 Anrufen wird ein Aufschlag von 0,06 USD/100 Anrufe berechnet. 
    * Der Tarif ist auf 1,200 Anrufe/Minute begrenzt.

### <a name="step-5---calibrate"></a>Schritt 5: Kalibrieren

Kalibrieren Sie die Preise über das Ertragsmodell hinweg, um:

- Um die Preisgestaltung so festzulegen, dass eine Über- oder Unterbewertung Ihrer API vermieden wird, basierend auf der Marktforschung in Schritt 3 oben.
- Um zu vermeiden, dass Punkte im Ertragsmodell unfair erscheinen oder Kunden dazu verleiten, das Modell zu umgehen, um günstigere Preise zu erzielen.
- Um sich zu vergewissern, dass das Ertragsmodell darauf ausgerichtet ist, einen Gesamtlebenszykluswert (Total Lifetime Value, TLV) zu generieren, der ausreicht, um die Gesamtkosten plus Rand zu decken.
- Um sich zu vergewissern, dass die Qualität Ihres Dienstangebots in jeder Ebene des Ertragsmodells von Ihrer Lösung unterstützt werden kann. 
    - Wenn Sie beispielsweise anbieten, 3.500 Anrufe pro Minute zu unterstützen, stellen Sie sicher, dass Ihre End-to-End-Lösung für diesen Durchsatz ausgelegt ist.

### <a name="step-6---release-and-monitor"></a>Schritt 6 – Release und Monitor

Wählen Sie eine geeignete Lösung, um Zahlungen für die Nutzung Ihrer APIs einzuziehen.  Die Anbieter tendieren dazu, sich in zwei Gruppen aufzuteilen:

- **Zahlungsplattformen, wie [Bereichsstreifen](https://stripe.com/)**
    
    Berechnen Sie die Zahlung auf der Grundlage der rohen API-Nutzungsmetriken, indem Sie das spezifische Ertragsmodell anwenden, das der Kunde gewählt hat. Konfigurieren Sie die Zahlungsplattform so, dass sie Ihre Monetarisierungsstrategie widerspiegelt.
- **Zahlungsanbieter wie [Adyen](https://www.adyen.com/)**

    Kümmern sich nur um die Erleichterung der Transaktion. Sie müssen Ihre Monetarisierungsstrategie anwenden (z. B. API-Nutzungsmetriken in eine Zahlung umwandeln), bevor Sie diesen Dienst aufrufen.

Verwenden Sie Azure API Management, um die Implementierung zu beschleunigen und das Risiko zu verringern, indem Sie die integrierten Funktionen von API Management nutzen. Weitere Informationen zu den spezifischen Funktionen von API Management finden Sie unter [wie API Management die Monetarisierung unterstützt](monetization-support.md).

Implementieren Sie eine Lösung, bei der die Kodierung Ihrer Monetarisierungsstrategie in den zugrundeliegenden Systemen flexibel ist, indem Sie das gleiche Vorgehen wie im Beispielprojekt verwenden. Mit flexibler Kodierung können Sie dynamisch reagieren und das Risiko und die Kosten von Änderungen minimieren.

Folgen Sie der [Dokumentation des GitHub-Repository](https://github.com/microsoft/azure-api-management-monetization), um das Beispielprojekt in Ihrem eigenen Azure-Abonnement zu implementieren.

Überwachen Sie regelmäßig, wie Ihre API genutzt wird, damit Sie beweisbasierte Entscheidungen treffen können. Wenn Sie beispielsweise feststellen, dass Ihnen Kunden abwandern, wiederholen Sie die Schritte 1 bis 5, um die Ursache zu ermitteln und zu beseitigen.

## <a name="ongoing-evolution"></a>Laufende Weiterentwicklung

Überprüfen Sie Ihre Monetarisierungsstrategie regelmäßig, indem Sie alle oben genannten Schritte wiederholen und neu auswerten. Möglicherweise müssen Sie Ihre Monetarisierungsstrategie im Laufe der Zeit weiterentwickeln, wenn Sie mehr über Ihre Kunden erfahren, was die Bereitstellung der API kostet und wie Sie auf den sich verschiebenden Wettbewerb auf dem Markt reagieren.

Denken Sie daran, dass die Monetarisierungsstrategie nur eine Facette einer erfolgreichen API-Implementierung ist. Andere Facetten sind:
* Die Erfahrung der Entwickler
* Die Qualität Ihrer Dokumentation
* Die rechtlichen Bestimmungen
* Ihre Fähigkeit, die API zu skalieren, um die committeten Dienstebenen zu erbringen.

## <a name="next-steps"></a>Nächste Schritte
* [Wie API Management die Monetarisierung unterstützt](monetization-support.md).
* Stellen Sie eine Demo-Integration von Adyen oder Stripe über das zugehörige [Git-Repository](https://github.com/microsoft/azure-api-management-monetization) bereit.
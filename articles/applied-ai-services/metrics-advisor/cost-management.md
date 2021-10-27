---
title: Kostenmanagement mit Azure Metrics Advisor
titleSuffix: Azure Applied AI Services
description: Erfahren Sie mehr über Kostenmanagement und Preise für Azure Metrics Advisor
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: bc376e8bc69845af245c66c045ed345f758e410e
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063923"
---
# <a name="azure-metrics-advisor-cost-management"></a>Kostenmanagement für Azure Metrics Advisor

Azure Metrics Advisor überwacht die Leistung der Wachstumsmotoren Ihrer Organisation, einschließlich Umsatz und Produktionsabläufe. Erkennen und beheben Sie Probleme schnell dank einer leistungsstarken Kombination aus Überwachung in nahezu Echtzeit, Anpassung von Modellen an Ihr Szenario und detaillierter Analyse mit Diagnose und Warnfunktionen. Ihnen werden nur die Zeitreihen in Rechnung gestellt, die vom Dienst analysiert werden. Es gibt keine Vorabmindestabnahme oder Mindestgebühr.

> [!NOTE]
> In diesem Artikel wird erläutert, wie die Kosten von Azure Metrics Advisor berechnet werden, um Sie bei der Planung und beim Kostenmanagement zu unterstützen. Die Preise in diesem Artikel entsprechen nicht den tatsächlichen Preisen und dienen nur zu Beispielzwecken. Die neuesten Preisinformationen finden Sie auf der [offiziellen Preisseite für Metrics Advisor](https://azure.microsoft.com/pricing/details/metrics-advisor/).  

## <a name="key-points-about-cost-management-and-pricing"></a>Wichtige Punkte zu Kostenmanagement und Preisen

- Ihnen wird die Anzahl **unterschiedlicher Zeitreihen** in Rechnung gestellt, die während eines Monats analysiert wurden. Wenn ein Datenpunkt für eine Zeitreihe analysiert wird, wird er auch berechnet.
- Die Anzahl unterschiedlicher Zeitreihen **ist unabhängig** von ihrer Granularität. Eine stündliche und eine tägliche Zeitreihe werden zum gleichen Preis in Rechnung gestellt. 
- Gebühren werden basierend auf der nachstehend aufgeführten Tarifstruktur berechnet. Am ersten Tag des nächsten Monats wird ein neues Statistikfenster initialisiert.  
- Je mehr Zeitreihen Sie für die Analyse in den Dienst integrieren, desto niedriger ist der Preis, den Sie für jede Zeitreihe zahlen. 

**Beachten Sie auch hier, dass die folgenden Preise nur zu Beispielzwecken dienen.** Die neuesten Preisinformationen finden Sie auf der [offiziellen Preisseite für Metrics Advisor](https://azure.microsoft.com/pricing/details/metrics-advisor/).

| Analysierte Zeitreihe/Monat| US-Dollar pro Zeitreihe |
|--------|-----|
| Free-Tarif: erste 25 Zeitreihen | $- |
| 26 Zeitreihen – 1.000 Zeitreihen | 0,75 US-Dollar |
| 1\.000 Zeitreihen – 5.000 Zeitreihen | 0,50 US-Dollar |
| 5000 Zeitreihen – 20.000 Zeitreihen | 0,25 US-Dollar|
| 20.000 Zeitreihen – 50.000 Zeitreihen| 0,10 USD|
| > 50.000 Zeitreihen | 0,05 US-Dollar |


Um Ihnen zu helfen, sich ein grundlegendes Verständnis von Metrics Advisor zu verschaffen und den Dienst zu erkunden, gibt es ein Inklusivvolumen, durch das Sie bis zu 25 Zeitreihen kostenlos analysieren können. 

## <a name="pricing-examples"></a>Preisbeispiele

### <a name="example-1"></a>Beispiel 1
<!-- introduce statistic window-->

Im 1. Monat hat ein Kunde einen Datenfeed mit 25 Zeitreihen für die Analyse der ersten Woche geladen. Anschließend lädt der Kunde in der zweiten Woche einen weiteren Datenfeed mit 30 Zeitreihen. In der dritten Woche werden jedoch 30 Zeitreihen gelöscht, die in der zweiten Woche geladen wurden. Im 1. Monat wurden **55** verschiedene Zeitreihen analysiert, von denen dem Kunden **30** in Rechnung gestellt werden (ohne die 25 Zeitreihen im Free-Tarif) und die unter Tarif 1 fallen. Die monatlichen Kosten betragen 30 x 0,75 USD = **22,50 USD**. 

| Volumentarif | US-Dollar pro Zeitreihe | US-Dollar pro Monat | 
| ------------| ----------------- | ----------- |
| Erste 30 (55-25) Zeitreihen | 0,75 US-Dollar | 22,50 US-Dollar |
| **Gesamt = 30 Zeitreihen** | | **22,50 USD pro Monat** |

Im 2. Monat hat der Kunde keine Zeitreihe geladen oder gelöscht. Im 2. Monat gibt es demnach 25 analysierte Zeitreihen. Es fallen keine Kosten an. 

### <a name="example-2"></a>Beispiel 2
<!-- introduce how time series is calculated-->

Ein Unternehmensplaner muss die Einnahmen des Unternehmens als Indikator für dessen Solidität nachverfolgen. Normalerweise gibt es ein Wochenmuster, bei dem der Kunde die Metriken in Metrics Advisor zur Analyse auf Anomalien überträgt. Metrics Advisor ist in der Lage, das Muster aus bisherigen Daten zu lernen und die Erkennung nachfolgender Datenreihenpunkte durchzuführen. Es kann sein, dass ein plötzlicher Rückgang als Anomalie erkannt wird, was auf ein zugrundeliegendes Problem hinweisen kann, z. B. einen Ausfall des Diensts oder ein Werbeangebot, das nicht wie erwartet funktioniert. Es kann auch eine unerwartete Spitze auftreten, die als Anomalie erkannt wird und auf eine sehr erfolgreiche Marketingkampagne oder den Gewinn eines wichtigen Kunden hinweisen kann. 

Die Metrik wird für **100 Produktkategorien** und **10 Regionen** analysiert, wobei die Anzahl der analysierten Zeitreihen wie folgt berechnet wird: 

```
1(Revenue) * 100 product categories * 10 regions = 1,000 analyzed time series
```

Basierend auf dem oben beschriebenen Tarifmodell werden 1.000 analysierte Zeitreihen pro Monat mit (1.000-25) x 0,75 USD = **731,25 USD** berechnet. 

| Volumentarif | US-Dollar pro Zeitreihe | US-Dollar pro Monat | 
| ------------| ----------------- | ----------- |
| Erste 975 (1.000-25) Zeitreihen | 0,75 US-Dollar | 731,25 US-Dollar |
| **Gesamt = 30 Zeitreihen** | | **731,25 US-Dollar pro Monat** |

### <a name="example-3"></a>Beispiel 3
<!-- introduce cost for multiple metrics and -->

Nach Überprüfung der Erkennungsergebnisse für die Umsatzmetrik möchte der Kunde zwei weitere Metriken in die Analyse einbeziehen. Eine davon sind die Kosten, die andere die täglich aktiven Benutzer seiner Website. Der Kunde möchte auch eine neue Dimension mit **20 Kanälen** hinzufügen. Innerhalb eines Monats werden 10 der 100 Produktkategorien nach der ersten Woche eingestellt und nicht weiter analysiert. Außerdem werden in der 3. Woche des Monats 10 neue Produktkategorien eingeführt, und die entsprechenden Zeitreihen werden für die Hälfte des Monats analysiert. Dann wird die Anzahl der zu analysierenden unterschiedlichen Zeitreihen wie folgt berechnet: 

```    
3(Revenue, cost and DAU) * 110 product categories * 10 regions * 20 channels = 66,000 analyzed time series
```

Basierend auf dem oben beschriebenen Tarifmodell fallen 66.000 analysierte Zeitreihen pro Monat in Tarif 5 und werden mit **10281,25 USD** in Rechnung gestellt. 

| Volumentarif | US-Dollar pro Zeitreihe | US-Dollar pro Monat | 
| ------------| ----------------- | ----------- |
| Erste 975 (1.000-25) Zeitreihen | 0,75 US-Dollar | 731,25 US-Dollar |
| Nächste 4.000 Zeitreihen | 0,50 US-Dollar | 2.000 USD |
| Nächste 15.000 Zeitreihen | 0,25 US-Dollar | 3\.750 USD |
| Nächste 30.000 Zeitreihen | 0,10 USD | 3.000 USD |
| Nächste 16.000 Zeitreihen | 0,05 US-Dollar | 800 USD |
| **Gesamt = 65.975 Zeitreihen** | | **10281,25 USD pro Monat** |

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten Ihrer Datenfeeds](how-tos/manage-data-feeds.md)
- [Konfigurationen für unterschiedliche Datenquellen](data-feeds-from-different-sources.md)
- [Konfigurieren von Metriken und Optimieren der Erkennungskonfiguration](how-tos/configure-metrics.md)



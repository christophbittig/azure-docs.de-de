---
title: 'Formularerkennung: Kontingente und Grenzwerte'
titleSuffix: Azure Applied AI Services
description: Kurzübersicht, ausführliche Beschreibung und bewährte Methoden im Zusammenhang mit Kontingenten und Grenzwerten für den Dienst Azure-Formularerkennung
services: cognitive-services
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: vikurpad
ms.openlocfilehash: 8a1b57571acdbfd8dce29e45218cfd096b0906f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478741"
---
# <a name="form-recognizer-service-quotas-and-limits"></a>Formularerkennung: Dienstkontingente und -grenzwerte

Dieser Artikel enthält eine Kurzübersicht und eine **ausführliche Beschreibung** der Kontingente und Grenzwerte für die Azure-Formularerkennung für alle [Tarife](https://azure.microsoft.com/pricing/details/form-recognizer/). Außerdem finden Sie hier einige bewährte Methoden zur Vermeidung der Anforderungsdrosselung. 

Für die Verwendung mit dem [Formularerkennungs-SDK](quickstarts/try-v3-csharp-sdk.md), [Formularerkennungs-REST-API](quickstarts/try-v3-rest-api.md), [Formularerkennung Studio](quickstarts/try-v3-form-recognizer-studio.md) und [Beispielbeschriftungstool](https://fott-2-1.azurewebsites.net/).

| Kontingent | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Grenzwert für gleichzeitige Anforderungen** | 1 | 15 (Standardwert) |
| Anpassbar | Nein<sup>2</sup> | Ja<sup>2</sup> |
| **Grenzwert beim Zusammenstellen des Modells** | 5 | 100 (Standardwert) |


<sup>1</sup> Sehen Sie sich im Zusammenhang mit dem Tarif **Free (F0)** auch die monatlichen Freibeträge auf der [Preisseite](https://azure.microsoft.com/pricing/details/form-recognizer/) an.
<sup>2</sup> Sehen Sie sich [bewährte Methoden](#example-of-a-workload-pattern-best-practice) und [Anpassungsanweisungen](#create-and-submit-support-request) an.

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Ausführliche Beschreibung, Kontingentanpassung und bewährte Methoden
Überprüfen Sie vor dem Anfordern einer Kontingenterhöhung (sofern zutreffend), dass sie wirklich erforderlich ist. Die Formularerkennung nutzt die automatische Skalierung, um die erforderlichen Rechenressourcen bei Bedarf bereitzustellen und gleichzeitig die Kosten für die Kundschaft niedrig zu halten, indem die Bereitstellung nicht genutzter Ressourcen aufgehoben und keine übermäßige Hardwarekapazität vorgehalten wird. Sollte Ihre Anwendung einen Antwortcode vom Typ 429 (zu viele Anforderungen) erhalten, obwohl sich Ihre Workload innerhalb der definierten Grenzwerte bewegt (siehe [Kurzübersicht über Kontingente und Grenzwerte](#form-recognizer-service-quotas-and-limits)), liegt das wahrscheinlich daran, dass der Dienst hochskaliert wird, um Ihren Bedarf zu decken, und noch nicht die erforderliche Skalierung erreicht hat, weshalb nicht sofort genügend Ressourcen zur Verfügung stehen, um die Anforderung zu bearbeiten. Dieser Zustand ist in der Regel vorübergehend und sollte nicht lange dauern.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Allgemeine bewährte Methoden zur Behandlung der Drosselung während der automatischen Skalierung
Verwenden Sie die folgenden Techniken, um Probleme im Zusammenhang mit der Drosselung (Antwortcode 429) zu vermeiden:
- Implementieren Sie eine Wiederholungslogik in der Anwendung.
- Vermeiden Sie plötzliche Änderungen bei der Arbeitsauslastung. Erhöhen Sie die Arbeitsauslastung nach und nach. <br/>
*Beispiel:* Ihre Anwendung verwendet die Formularerkennung, und die aktuelle Workload liegt bei 10 TPS (Transaktionen pro Sekunde). In der nächsten Sekunde erhöhen Sie die Last auf 40 TPS (also das Vierfache). Der Dienst beginnt sofort mit dem Hochskalieren, um die neue Last bewältigen zu können. Das Hochskalieren dauert jedoch wahrscheinlich länger als eine Sekunde, weshalb für einige Anforderungen der Antwortcode 429 zurückgegeben wird.

In den nächsten Abschnitten werden bestimmte Kontingentanpassungsfälle beschrieben.
Wechseln Sie zu [Formularerkennung: Erhöhen des Grenzwerts für gleichzeitige Anforderungen](#create-and-submit-support-request).

### <a name="increasing-transactions-per-second-request-limit"></a>Erhöhen des Anforderungsgrenzwerts für Transaktionen pro Sekunde
Standardmäßig ist die Anzahl gleichzeitiger Anforderungen für eine Formularerkennungsressource auf 15 Transaktionen pro Sekunde beschränkt. Im Tarif „Standard“ kann diese Anzahl erhöht werden. Machen Sie sich vor dem Übermitteln der Anforderung mit dem Material in [diesem Abschnitt](#detailed-description-quota-adjustment-and-best-practices) und mit [diesen bewährten Methoden](#example-of-a-workload-pattern-best-practice) vertraut.

Eine Erhöhung des Grenzwerts für gleichzeitige Anforderungen wirkt sich **nicht** direkt auf Ihre Kosten aus. Bei der Formularerkennung zahlen Sie nur für Ihre tatsächliche Nutzung. Der Grenzwert gibt an, wie hoch der Dienst skaliert werden kann, bevor Ihre Anforderungen gedrosselt werden.

Der vorhandene Wert des Parameters für den Grenzwert für gleichzeitige Anforderungen wird im Azure-Portal, in Befehlszeilentools und in API-Anforderungen **nicht** angezeigt. Erstellen Sie eine Azure-Supportanfrage, um den vorhandenen Wert zu überprüfen.

#### <a name="have-the-required-information-ready"></a>Halten Sie die erforderlichen Informationen bereit:
- ID der Formularerkennungsressource
- Region

- **Abrufen der Informationen (Basismodell):**
  - Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).
  - Wählen Sie die Formularerkennungsressource aus, für die Sie den Transaktionsgrenzwert erhöhen möchten.
  - Wählen Sie *Eigenschaften* (Gruppe *Ressourcenverwaltung*) aus.
  - Kopieren und speichern Sie die Werte der folgenden Felder:
    - **Ressourcen-ID**
    - **Standort** (Ihre Endpunktregion)

#### <a name="create-and-submit-support-request"></a>Erstellen und Übermitteln der Supportanfrage
Initiieren Sie die Erhöhung des TPS-Grenzwerts (Transaktionen pro Sekunde) für Ihre Ressource, indem Sie eine Supportanfrage übermitteln:

- Vergewissern Sie sich, dass Sie über die [erforderlichen Informationen](#have-the-required-information-ready) verfügen.
- Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).
- Wählen Sie die Formularerkennungsressource aus, für die Sie den TPS-Grenzwert erhöhen möchten.
- Wählen Sie *Neue Supportanfrage* (Gruppe *Support + Problembehandlung*) aus.
- Daraufhin wird ein neues Fenster mit automatisch ausgefüllten Informationen zum Azure-Abonnement und zur Azure-Ressource angezeigt.
- Geben Sie eine *Zusammenfassung* ein (beispielsweise „Increase Form Recognizer TPS limit“ (TPS-Grenzwert für Formularerkennung erhöhen)).
- Wählen Sie unter *Problemtyp* die Option für die Kontingent- oder Verbrauchsprüfung aus.
- Klicken Sie auf *Weiter: Lösungen*.
- Fahren Sie mit der Anforderungserstellung fort.
- Geben Sie auf der Registerkarte *Details* im Feld *Beschreibung* Folgendes ein:
  - Einen Hinweis darauf, dass es bei der Anfrage um ein Kontingent für die **Formularerkennung** geht
  - Geben Sie eine TPS-Erwartung an, auf die Sie skalieren möchten.    
  - [Zuvor ermittelte](#have-the-required-information-ready) Azure-Ressourceninformationen
  - Geben Sie die übrigen erforderlichen Informationen ein, und klicken Sie auf der Registerkarte *Überprüfen + erstellen* auf die Schaltfläche *Erstellen*.
  - Notieren Sie sich die Nummer der Supportanfrage aus den Benachrichtigungen im Azure-Portal. Sie werden in Kürze zur weiteren Bearbeitung kontaktiert.

## <a name="example-of-a-workload-pattern-best-practice"></a>Beispiel für eine bewährte Methode für Workloadmuster
Dieses Beispiel zeigt die empfohlene Vorgehensweise zur Vermeidung einer möglichen Drosselung [im Rahmen der automatischen Skalierung](#detailed-description-quota-adjustment-and-best-practices). Dabei handelt es sich nicht um eine exakte Anleitung, sondern vielmehr um eine empfohlene Vorlage, die Sie nach Bedarf anpassen können.

Angenommen, für eine Formularerkennungsressource wurde der Standardgrenzwert festgelegt. Starten Sie die Workload, um Ihre Analyseanforderungen zu übermitteln. Wenn Sie feststellen, dass eine häufige Drosselung mit dem Antwortcode 429 auftritt, sollten Sie zunächst die Antwortanforderung „GET analyze“ ausführen und den Vorgang mithilfe des Musters 2-3-5-8 wiederholen. Im Allgemeinen wird empfohlen, die „GET analyze“-Antwort nicht häufiger als einmal alle 2 Sekunden für eine entsprechende POST-Anforderung aufzurufen. 

Wenn Sie feststellen, dass die Anzahl der POST-Anforderungen für übermittelte Dokumente gedrosselt wird, sollten Sie eine Verzögerung zwischen den Anforderungen hinzufügen. Wenn Ihre Workload ein höheres Maß an gleichzeitiger Verarbeitung erfordert, müssen Sie eine Supportanfrage erstellen, um Ihre Dienstgrenzwerte für Transaktionen pro Sekunde zu erhöhen.

Es empfiehlt sich allgemein, die Arbeitsauslastung und die Arbeitsauslastungsmuster zu testen, bevor sie in der Produktion verwendet werden.


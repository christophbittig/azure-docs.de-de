---
title: 'Azure-Ressourcen: Fragen und Antworten'
description: In „Fragen und Antworten“ werden verschiedene Azure-Quellen verwendet, von denen jede einen anderen Zweck erfüllt. Wenn Sie wissen, wie sie einzeln verwendet werden, können Sie den richtigen Tarif planen und auswählen oder bestimmen, wann ein Tarifwechsel sinnvoll ist. Das Verständnis dafür, wie sie kombiniert verwendet werden, ermöglicht es Ihnen, auftretende Probleme zu finden und zu beheben.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 10/10/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: ddd5ed0c51c412342b6e8093a69cdef900346be2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476678"
---
# <a name="azure-resources-for-question-answering"></a>Azure-Ressourcen für Fragen und Antworten

In „Fragen und Antworten“ werden verschiedene Azure-Quellen verwendet, von denen jede einen anderen Zweck erfüllt. Wenn Sie wissen, wie sie einzeln verwendet werden, können Sie den richtigen Tarif planen und auswählen oder bestimmen, wann ein Tarifwechsel sinnvoll ist. Das Verständnis dafür, wie Ressourcen _kombiniert_ verwendet werden, ermöglicht es Ihnen, auftretende Probleme zu finden und zu beheben.

## <a name="resource-planning"></a>Ressourcenplanung

Wenn Sie zum ersten Mal eine Wissensdatenbank entwickeln, ist es in der Prototypenphase üblich, sowohl für Tests als auch für die Produktion eine einzelne Ressource zu verwenden.

> [!TIP]
> „Wissensdatenbank“ und „Projekt“ sind äquivalente Begriffe für „Fragen und Antworten“ und können austauschbar verwendet werden.

Beim Einstieg in die Entwicklungsphase des Projekts sollten Sie die folgenden Punkte berücksichtigen:

* Wie viele Sprachen soll Ihr Wissensdatenbank-System enthalten?
* In wie vielen Regionen muss Ihre Wissensdatenbank verfügbar sein, bzw. aus wie vielen Regionen wird sie zur Verfügung gestellt?
* Wie viele Dokumente werden die einzelnen Domänen in Ihrem System enthalten?

## <a name="pricing-tier-considerations"></a>Tarifstufeninformationen

Es gibt in der Regel drei Parameter, die Sie berücksichtigen müssen:

* **Sie benötigen folgenden Durchsatz**:
    * „Fragen und Antworten“ ist ein kostenloses Feature, und der Durchsatz ist derzeit auf 10 Transaktionen pro Sekunde sowohl für Verwaltungs-APIs als auch für Vorhersage-APIs begrenzt.
    * Dies sollte auch Einfluss auf Ihre Wahl der Azure [Cognitive Search](../../../../search/search-sku-tier.md)-SKU haben. Weitere Details dazu finden Sie **hier**. Darüber hinaus müssen Sie unter Umständen die Cognitive Search-[Kapazität](../../../../search/search-capacity-planning.md) mit Replikaten anpassen.

* **Größe und Anzahl von Wissensdatenbanken:** Wählen Sie eine geeignete [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) für Ihr Szenario aus. Normalerweise entscheiden Sie über die Anzahl der erforderlichen Wissensdatenbanken anhand der Anzahl der verschiedenen Themendomänen. Eine Themendomäne (für eine einzelne Sprache) sollte in einer Wissensdatenbank vorliegen.

    Bei „Benutzerdefinierte Fragen und Antworten“ haben Sie die Wahl, ob Sie Ihre Sprachressourcen in einer einzelnen Sprache oder in mehreren Sprachen einrichten. Sie können diese Auswahl treffen, wenn Sie Ihr erstes Projekt in [Language Studio](https://language.azure.com/) erstellen.

    > [!IMPORTANT]
    > Sie können N-1 Wissensdatenbanken einer einzelnen Sprache oder N/2 Wissensdatenbanken verschiedener Sprachen einer bestimmten Ebene veröffentlichen, wobei N die maximal zulässigen Indizes auf der Ebene angibt. Überprüfen Sie außerdem die maximale Größe und Anzahl der in den einzelnen Tarifen zulässigen Dokumente.

    Wenn Ihre Ebene z. B. 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken in derselben Sprache veröffentlichen (ein Index pro veröffentlichter Wissensdatenbank). Der 15. Index wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet. Wenn Sie sich für Wissensdatenbanken in verschiedenen Sprachen entscheiden, können Sie nur sieben Wissensdatenbanken veröffentlichen.

* **Anzahl von Dokumenten als Quellen**: „Fragen und Antworten“ ist ein kostenloses Feature, und es gibt keine Beschränkungen für die Anzahl der Dokumente, die Sie als Quellen hinzufügen können. 

Die folgende Tabelle gibt Ihnen einige allgemeine Richtlinien.

|                            |Azure Cognitive Search | Einschränkungen                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentieren**        |Free-Tarif    | Veröffentlichen von bis zu 2 KB bei einer Größe von 50 MB  |
| **Entwicklungs-/Testumgebung**   |Basic        | Veröffentlichen von bis zu 14 KB bei einer Größe von 2 GB    |
| **Produktionsumgebung** |Standard     | Veröffentlichen von bis zu 49 KB bei einer Größe von 25 GB |

## <a name="recommended-settings"></a>Empfohlene Einstellungen

„Benutzerdefinierte Fragen und Antworten“ ist ein kostenloses Feature, und der Durchsatz ist derzeit auf 10 Transaktionen pro Sekunde sowohl für Verwaltungs-APIs als auch für Vorhersage-APIs begrenzt. Um 10 Transaktionen pro Sekunde für Ihren Dienst zu erreichen, empfehlen wir die SKU S1 (eine Instanz) von Azure Cognitive Search.

## <a name="keys-in-question-answering"></a>Schlüssel in „Fragen und Antworten“

Ihr Feature „Benutzerdefinierte Fragen und Antworten“ befasst sich mit zwei Arten von Schlüsseln: **Erstellungsschlüssel** und **Azure Cognitive Search-Schlüssel**, die für den Zugriff auf den Dienst im Abonnement des Kunden verwendet werden.

Verwenden Sie diese Schlüssel, wenn Sie Anforderungen an den Dienst über APIs senden.

|Name|Standort|Zweck|
|--|--|--|
|Erstellungs-/Abonnementschlüssel|[Azure portal](https://azure.microsoft.com/free/cognitive-services/)|Diese Schlüssel werden für den Zugriff auf die Sprachdienst-APIs verwendet. Mit diesen APIs können Sie die Fragen und Antworten in Ihrer Wissensdatenbank bearbeiten und Ihre Wissensdatenbank veröffentlichen. Diese Schlüssel werden erstellt, wenn Sie eine neue Ressource erstellen.<br><br>Sie finden diese Schlüssel in der Ressource **Cognitive Services** auf der Seite **Schlüssel und Endpunkt**.|
|Administratorschlüssel von Azure Cognitive Search|[Azure portal](../../../../search/search-security-api-keys.md)|Diese Schlüssel werden verwendet, um mit dem Azure Cognitive Search-Dienst zu kommunizieren, der im Azure-Abonnement des Benutzers bereitgestellt wird. Wenn Sie eine Azure Cognitive Search-Ressource mit dem Feature „Benutzerdefinierte Fragen und Antworten“ verknüpfen, wird der Administratorschlüssel automatisch an „Fragen und Antworten“ übergegeben. <br><br>Sie finden diese Schlüssel in der **Azure Cognitive Search**-Ressource auf der Seite **Schlüssel**.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Suchen von Erstellungsschlüsseln im Azure-Portal

Sie können Ihre Dokumenterstellungsschlüssel aus dem Azure-Portal anzeigen und zurücksetzen, wo Sie das benutzerdefinierte Feature „Fragen und Antworten“ in Ihrer Sprachressource hinzugefügt haben.

1. Wechseln Sie im Azure-Portal zur Sprachressource, und wählen Sie die Ressource aus, die den Typ *Cognitive Services* hat:

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Ressourcenliste für „Fragen und Antworten“](../../../qnamaker/media/qnamaker-how-to-setup-service/resources-created-question-answering.png)

2. Wechseln Sie zu **Schlüssel und Endpunkt**:

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Abonnementschlüssel](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)

### <a name="management-service-region"></a>Verwaltungsdienstregion

Bei „Benutzerdefinierte Fragen und Antworten“ befinden sich sowohl die Verwaltungs- als auch die Vorhersage-Dienste in derselben Region.

## <a name="resource-purposes"></a>Zwecke von Ressourcen

Jede Azure-Ressource, die mit dem Feature „Benutzerdefinierte Fragen und Antworten“ erstellt wurde, hat einen bestimmten Zweck:

* Sprachressource (wird je nach Kontext, in dem Sie die Ressource auswerten, auch als Textanalyse bezeichnet).
* Cognitive Search-Ressource

### <a name="language-resource"></a>Sprachressource

Die Sprachressource mit dem Feature „Benutzerdefinierte Fragen und Antworten“ bietet Zugriff auf die Erstellungs- und Veröffentlichungs-APIs, hostet die Rangfolgeruntime und stellt Telemetriedaten bereit.

### <a name="azure-cognitive-search-resource"></a>Azure Cognitive Search-Ressource

Die [Cognitive Search](../../../../search/index.yml)-Ressource wird für diese Zwecke verwendet:

* Speichern der Frage-Antwort-Paare
* Angeben der anfänglichen Rangfolge (Bewerter Nr. 1) der Frage-Antwort-Paare zur Laufzeit

#### <a name="index-usage"></a>Indexnutzung

Sie können N-1 Wissensdatenbanken einer einzelnen Sprache oder N/2 Wissensdatenbanken verschiedener Sprachen einer bestimmten Ebene veröffentlichen, wobei N die maximal zulässige Anzahl von Indizes auf der Azure Cognitive Search-Ebene angibt. Überprüfen Sie außerdem die maximale Größe und Anzahl der in den einzelnen Tarifen zulässigen Dokumente.

Wenn Ihre Ebene z. B. 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken in derselben Sprache veröffentlichen (ein Index pro veröffentlichter Wissensdatenbank). Der 15. Index wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet. Wenn Sie sich für Wissensdatenbanken in verschiedenen Sprachen entscheiden, können Sie nur sieben Wissensdatenbanken veröffentlichen.

#### <a name="language-usage"></a>Sprachverwendung

Mit „Benutzerdefinierte Fragen und Antworten“ haben Sie die Wahl, Ihren Dienst für Wissensdatenbanken in einer einzelnen oder in mehreren Sprachen einzurichten. Sie treffen diese Wahl bei der Erstellung der ersten Wissensdatenbank in Ihrer Sprachressource.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Wissensdatenbanken](../How-To/manage-knowledge-base.md) für Fragen und Antworten.

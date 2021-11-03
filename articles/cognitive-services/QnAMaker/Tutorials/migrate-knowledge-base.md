---
title: 'Migrieren von Wissensdatenbanken: QnA Maker'
description: Die Migration einer Wissensdatenbank erfordert den Export aus einer Wissensdatenbank und den Import in eine andere.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: bdbc77bdb1b0b16085c6df8fb302c5483c36a01f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080686"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrieren einer Knowledge Base durch Exportieren und Importieren

Es kann verschiedene Gründe geben, um eine Kopie Ihrer Wissensdatenbank zu erstellen:

* Kopieren einer Wissensdatenbank aus der allgemein verfügbaren Version von QnA Maker in benutzerdefinierte Fragen und Antworten 
* Implementierung eines Sicherungs- und Wiederherstellungsprozesses 
* Integration in Ihre CI/CD-Pipeline 
* Verschiebung Ihrer Daten in verschiedene Regionen

## <a name="prerequisites"></a>Voraussetzungen

> * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.
> * Eine über das Azure-Portal erstellte [QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker). Merken Sie sich die Azure Active Directory-ID, das Abonnement und den QnA-Ressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.
> * Richten Sie einen neuen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md) ein.

## <a name="export-a-knowledge-base"></a>Exportieren einer Wissensdatenbank
1. Melden Sie sich beim [QnA Maker-Portal](https://qnamaker.ai) an.
1. Wählen Sie die Wissensdatenbank aus, die Sie migrieren möchten.

1. Auf der Seite **Einstellungen** können Sie **QnAs** (Fragen und Antworten), **Synonyms** (Synonyme) oder **Knowledge Base Replica** (Replikat der Wissensdatenbank) exportieren. Die Daten können als TSV- oder XLSX-Datei heruntergeladen werden.

   1. **QnAs** (Fragen und Antworten): Beim Exportieren von QnAs werden alle Frage-Antwort-Paare (mit Fragen, Antworten, Metadaten, Folgeäußerungen und den Datenquellennamen) heruntergeladen. Die QnA-IDs, die mit den Fragen und Antworten exportiert werden, können mithilfe der [Update-API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update) zum Aktualisieren eines bestimmten QnA-Paares verwendet werden. Die QnA-ID für ein bestimmtes QnA-Paar bleibt über mehrere Exportvorgänge hinweg unverändert.
   2. **Synonyms** (Synonyme): Sie können Synonyme exportieren, die der Wissensdatenbank hinzugefügt wurden.
   4. **Knowledge Base Replica** (Replikat der Wissensdatenbank): Wenn Sie die gesamte Wissensdatenbank mit Synonymen und anderen Einstellungen herunterladen möchten, können Sie diese Option auswählen.

## <a name="import-a-knowledge-base"></a>Importieren einer Knowledge Base
1. Klicken Sie im oberen Menü des Portals „qnamaker.ai“ auf **Create a knowledge base** (Neue Wissensdatenbank erstellen), und erstellen Sie dann eine _leere_ Wissensdatenbank, indem Sie keine URLs oder Dateien hinzufügen. Legen Sie für die neue Wissensdatenbank einen Namen Ihrer Wahl fest, und klicken Sie dann auf  **Create your KB** (Wissensdatenbank erstellen). 

1. Öffnen Sie in dieser neuen Wissensdatenbank die Registerkarte **Einstellungen**, und wählen Sie unter _Import knowledge base_ (Wissensdatenbank importieren) eine der folgenden Optionen aus: **QnAs** (Fragen und Antworten), **Synonyms** (Synonyme) oder **Knowledge Base Replica** (Replikat der Wissensdatenbank). 

   1. **QnAs** (Fragen und Antworten): Mit dieser Option werden alle Frage-Antwort-Paare importiert. **Die in der neuen Wissensdatenbank erstellten QnA-Paare müssen dieselbe QnA-ID aufweisen wie in der exportierten Datei**. Beispiele für das Importieren von Fragen und Antworten finden Sie unter [SampleQnAs.xlsx](https://aka.ms/qnamaker-sampleqnas) sowie unter [SampleQnAs.tsv](https://aka.ms/qnamaker-sampleqnastsv).
   2. **Synonyms** (Synonyme): Diese Option kann verwendet werden, um Synonyme in die Wissensdatenbank zu importieren. Beispiele für das Importieren von Synonymen finden Sie unter [SampleSynonyms.xlsx](https://aka.ms/qnamaker-samplesynonyms) sowie unter [SampleSynonyms.tsv](https://aka.ms/qnamaker-samplesynonymstsv).
   3. **Knowledge Base Replica** (Replikat der Wissensdatenbank): Mit dieser Option können Sie ein Replikat der Wissensdatenbank mit Fragen und Antworten, Synonymen und Einstellungen importieren. Ausführlichere Informationen finden Sie unter [KBReplicaSampleExcel](https://aka.ms/qnamaker-samplereplica) sowie unter [KBReplicaSampleTSV](https://aka.ms/qnamaker-samplereplicatsv). Wenn Sie dem Replikat auch unstrukturierten Inhalt hinzufügen möchten, sehen Sie sich [CustomQnAKBReplicaSample](https://aka.ms/qnamaker-samplev2replica) an.

      Zum Importieren von Replikaten benötigen Sie entweder Fragen und Antworten oder unstrukturierten Inhalt. Unstrukturierte Dokumente können nur für benutzerdefinierte Fragen und Antworten verwendet werden.
      Die Synonymdatei ist beim Importieren eines Replikats nicht unbedingt erforderlich.
      Die Einstellungsdatei wird beim Importieren eines Replikats zwingend benötigt.

         |Einstellungen|Aktualisierung beim Importieren in eine QnA Maker-Wissensdatenbank zulässig?|Aktualisierung beim Importieren in eine Wissensdatenbank für benutzerdefinierte Fragen und Antworten zulässig?|
         |:--|--|--|
         |DefaultAnswerForKB|Nein|Ja|
         |EnableActiveLearning (True/False)|Ja|Nein|
         |EnableMultiTurnExtraction (True/False)|Ja|Ja|
         |DefaultAnswerforMultiturn|Ja|Ja|
         |Sprache|Nein|Nein|

1. **Testen** Sie die neue Knowledge Base im Testbereich. Erfahren Sie mehr über das [Testen Ihrer Knowledge Base](../How-To/test-knowledge-base.md).

1. **Veröffentlichen** Sie die Wissensdatenbank, und erstellen Sie einen Chatbot. Erfahren Sie mehr über das [Veröffentlichen Ihrer Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

   > [!div class="mx-imgBorder"]
   > ![Migrieren der Wissensdatenbank](../media/qnamaker-how-to-migrate-kb/import-export-kb.png)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programmgesteuertes Migrieren einer Wissensdatenbank von QnA Maker

Die Migration kann programmgesteuert über die folgenden REST-APIs ausgeführt werden:

**Export**

* [Herunterladen der Wissensdatenbank-API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importieren**

* [Ersetzen der API (erneutes Laden mit der gleichen Wissensdatenbank-ID)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [Erstellen der API (Laden mit der neuen Wissensdatenbank-ID)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs"></a>Chatprotokolle
Die Migration von Chatprotokollen ist nicht möglich, da die neue Wissensdatenbank Application Insights zum Speichern von Chatprotokollen verwendet.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bearbeiten einer Knowledge Base](../How-To/edit-knowledge-base.md)

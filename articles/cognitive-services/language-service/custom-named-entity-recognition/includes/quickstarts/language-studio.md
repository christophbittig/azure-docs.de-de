---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e8fa35bb99a11e51d634219e54296c5be831b3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101421"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>Erstellen einer neuen Azure-Ressource und eines Azure Blob Storage-Kontos

Bevor Sie eine benutzerdefinierte NER verwenden können, müssen Sie eine Azure-Sprachressource erstellen, wodurch Sie die Anmeldeinformationen erhalten, die zum Erstellen eines Projekts und zum Trainieren eines Modells erforderlich sind. Sie benötigen außerdem ein Azure-Speicherkonto, in das Sie Ihr Dataset hochladen können, das zum Erstellen Ihres Modells verwendet wird.

> [!IMPORTANT]
> Für einen schnellen Einstieg empfiehlt es sich, mithilfe der unten angegebenen Schritte eine neue Azure-Sprachressource zu erstellen, mit der Sie die Ressource erstellen und gleichzeitig ein Speicherkonto konfigurieren können. Dies ist in einem Durchgang einfacher zu erledigen als später.
>
> Wenn Sie eine bereits vorhandene Ressource verwenden möchten, müssen Sie sie und ein Speicherkonto separat konfigurieren. Weitere Informationen finden Sie unter [Projekt erstellen](../../how-to/create-project.md#using-a-pre-existing-azure-resource).

1. Wechseln Sie zum [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics), um eine neue Azure-Sprachressource zu erstellen. Wenn Sie aufgefordert werden, zusätzliche Features auszuwählen, wählen Sie **Benutzerdefinierte Textklassifizierung & benutzerdefinierte NER** aus. Stellen Sie beim Erstellen Ihrer Ressource sicher, dass sie die folgenden Parameter aufweist.

    |Azure-Ressourcenanforderung  |Erforderlicher Wert  |
    |---------|---------|
    |Ort | „USA, Westen 2“ oder „Europa, Westen“         |
    |Tarif     | Tarif „Standard“ (**S**)        |

2. Wählen Sie im Abschnitt **Benutzerdefinierte Erkennung benannter Entitäten (NER) & Benutzerdefinierte Klassifizierung (Vorschau)** ein vorhandenes Speicherkonto aus, oder wählen Sie **Neues Speicherkonto erstellen** aus. Beachten Sie, dass diese Werte im Rahmen dieser Schnellstartanleitung gelten und nicht unbedingt die [Speicherkontowerte](/azure/storage/common/storage-account-overview) darstellen, die in Produktionsumgebungen sinnvoll sind.

    |Speicherkontowert  |Empfohlener Wert  |
    |---------|---------|
    | Name | Beliebiger Name |
    | Leistung | Standard |
    | Kontoart| Storage (Universell V1) |
    | Replikation | Lokal redundanter Speicher (LRS)
    |Ort | Ein beliebiger Standort, der möglichst nahe zu Ihnen liegt, um die beste Latenz zu erhalten.        |

## <a name="upload-sample-data-to-blob-container"></a>Hochladen von Beispieldaten in den Blobcontainer

Nachdem Sie ein Azure-Speicherkonto erstellt und es mit Ihrer Sprachressource verknüpft haben, müssen Sie die Beispieldateien für diese Schnellstartanleitung hochladen. Diese Dateien werden später zum Trainieren Ihres Modells verwendet.

1. [Laden Sie die Beispieldaten](https://go.microsoft.com/fwlink/?linkid=2175226) für diesen Schnellstart von GitHub herunter.

2. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com) zu Ihrem Azure-Speicherkonto. Navigieren Sie zu Ihrem Konto, und laden Sie die Beispieldaten in das Konto hoch.

Das bereitgestellte Beispieldataset enthält 20 Kreditverträge. Jede Vereinbarung beinhaltet zwei Parteien: einen Darlehensgeber und einen Darlehensnehmer. Sie können die bereitgestellte Beispieldatei verwenden, um für beide Parteien relevante Informationen zu extrahieren, ein Vertragsdatum, einen Darlehensbetrag und einen Zinssatz.

## <a name="create-a-custom-named-entity-recognition-project"></a>Erstellen eines benutzerdefinierten Projekts zur Erkennung benannter Entitäten

Nachdem Ihre Ressource und der Speichercontainer konfiguriert wurden, erstellen Sie ein neues NER-Projekt für Unterhaltungen. Ein Projekt ist ein Arbeitsbereich zum Erstellen Ihrer benutzerdefinierten KI-Modelle auf der Grundlage Ihrer Daten. Auf Ihr Projekt können nur Sie und andere Personen zugreifen, die Mitwirkendenzugriff auf die verwendete Azure-Ressource haben.

1. Melden Sie sich beim [Language Studio-Portal](https://aka.ms/languageStudio) an. Es wird ein Fenster angezeigt, in dem Sie Ihr Abonnement und Ihre Sprachressource auswählen können. Wählen Sie die Ressource aus, die Sie im Schritt oben erstellt haben.

2. Suchen Sie den Abschnitt **Entitätsextraktion**, und wählen Sie aus den verfügbaren Diensten **Benutzerdefinierte Erkennung benannter Entitäten** aus.

3. Wählen Sie im oberen Menü ihrer Projektseite **Neues Projekt erstellen** aus. Durch das Erstellen eines Projekts können Sie Daten kennzeichnen sowie Ihre Modelle trainieren, auswerten, verbessern und bereitstellen. 

    
    :::image type="content" source="../../media/create-project.png" alt-text="Screenshot der Seite zur Projekterstellung." lightbox="../../media/create-project.png":::


4.  Nachdem Sie auf **Neues Projekt erstellen** geklickt haben,wird ein Bildschirm angezeigt, auf dem Sie eine Verbindung mit Ihrem Speicherkonto herstellen können. Wenn Sie Ihr Speicherkonto nicht finden können, vergewissern Sie sich, dass Sie anhand der oben beschriebenen Schritte eine Ressource erstellt haben. 

    >[!NOTE]
    > * Sie müssen diesen Schritt nur einmal für jede neue Ressource durchführen, die Sie verwenden. 
    > * Dieser Prozess kann nicht rückgängig gemacht werden – wenn Sie ein Speicherkonto mit Ihrer Ressource verbinden, können Sie die Verbindung später nicht trennen.
    > * Sie können Ihre Ressource nur mit einem Speicherkonto verbinden.
    > * Wenn Sie bereits eine Verbindung mit einem Speicherkonto hergestellt haben, wird stattdessen ein Bildschirm **Enter basic information** (Grundlegende Informationen eingeben) angezeigt. Mehr dazu finden Sie im nächsten Schritt.
    
    :::image type="content" source="../../media/connect-storage.png" alt-text="Screenshot: Bildschirm zum Herstellen von Speicherverbindungen." lightbox="../../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. Geben Sie die Projektinformationen ein, einschließlich eines Namens, einer Beschreibung und der Sprache der Dateien in Ihrem Projekt. Sie können den Namen Ihres Projekts später nicht mehr ändern. 

6. Wählen Sie den Container aus, in den Sie Ihre Daten hochgeladen haben. Für diese Schnellstartanleitung verwenden wir die vorhandene Tagdatei, die im Container verfügbar ist. Klicken Sie dann auf **Weiter**.

7. Überprüfen Sie die eingegebenen Daten, und wählen Sie **Projekt erstellen** aus.

## <a name="train-your-model"></a>Trainieren Ihres Modells

In der Regel importieren Sie nach dem Erstellen eines Projekts Ihre Daten und beginnen mit dem [Kennzeichnen der enthaltenen Entitäten](../../how-to/tag-data.md), um das Klassifizierungsmodell zu trainieren. Im Rahmen dieser Schnellstartanleitung verwenden Sie die zuvor heruntergeladene und in Ihrem Azure-Speicherkonto gespeicherte Beispieldatendatei mit Tags.

Ein Modell ist das Machine Learning-Objekt, das trainiert wird, um Text zu klassifizieren. Ihr Modell lernt anhand der Beispieldaten und kann anschließend Tickets für den technischen Support klassifizieren.

So beginnen Sie mit dem Trainieren Ihres Modells:

1. Wählen Sie im Menü links **Trainieren** aus.

2. Wählen Sie **Neues Modell trainieren** aus, und geben Sie den Namen des Modells im Textfeld darunter ein.

    :::image type="content" source="../../media/train-model.png" alt-text="Screenshot der Modellauswahlseite für das Training" lightbox="../../media/train-model.png":::

3. Klicken Sie unten auf der Seite auf die Schaltfläche **Trainieren**.

    > [!NOTE]
    > * Während des Trainings werden die Daten in 2 Mengen aufgeteilt: 80 % für das Training und 20 % für das Testen. Weitere Informationen zur Aufteilung von Daten finden Sie [hier](../../how-to/train-model.md#data-split)
    > * Das Training kann bis zu mehreren Stunden dauern.

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

Im Allgemeinen überprüfen Sie nach dem Trainieren eines Modells seine [Auswertungsdetails](../../how-to/view-model-evaluation.md) und [nehmen bei Bedarf Verbesserungen vor](../../how-to/improve-model.md). Im Rahmen dieser Schnellstartanleitung stellen Sie Ihr Modell einfach bereit, wodurch es Ihnen zum Ausprobieren zur Verfügung steht.

Nachdem Ihr Modell trainiert wurde, können Sie es bereitstellen. Mit der Bereitstellung Ihres Modells können Sie damit beginnen, benannte Entitäten mithilfe der [Analyse-API](https://aka.ms/ct-runtime-swagger) zu extrahieren.

1. Wählen Sie im Menü auf der linken Seite **Modell bereitstellen** aus.

2. Wählen Sie das Modell aus, das Sie bereitstellen möchten, und wählen Sie dann **Modell bereitstellen** aus.

## <a name="test-your-model"></a>Testen des Modells

Nachdem Ihr Modell bereitgestellt wurde, können Sie damit beginnen, es für die Entitätsextraktion zu verwenden. Verwenden Sie die folgenden Schritte, um Ihre erste Anforderung zur Entitätsextraktion zu senden.

1. Wählen Sie im Menü auf der linken Seite **Modell testen** aus.

2. Wählen Sie das Modell aus, das Sie testen möchten.

3. Fügen Sie ihren Text in das Textfeld ein. Alternativ können Sie eine `.txt`-Datei hochladen. 

4. Klicken Sie auf **Test ausführen**.

5. Auf der Registerkarte **Ergebnis** sehen Sie die aus Ihrem Text extrahierten Entitäten und ihren Typ. Auf der Registerkarte **JSON** können Sie außerdem die JSON-Antwort anzeigen.

    :::image type="content" source="../../media/test-model-results.png" alt-text="Anzeigen der Testergebnisse" lightbox="../../media/test-model-results.png":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihr Projekt nicht mehr benötigen, können Sie das Projekt mithilfe von [Language Studio](https://aka.ms/custom-extraction) löschen. Wählen Sie im linken Navigationsmenü **Benutzerdefinierte Erkennung benannter Entitäten (NER)** aus, wählen Sie das zu löschende Projekt aus, und klicken Sie auf **Löschen**.

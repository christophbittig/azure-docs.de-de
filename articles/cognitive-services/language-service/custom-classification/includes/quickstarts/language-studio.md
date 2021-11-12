---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: c7018ad879e5877c131af041685722d30b2906b2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520131"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>Erstellen einer neuen Azure-Ressource und eines Azure Blob Storage-Kontos

Bevor Sie eine benutzerdefinierte Textklassifizierung verwenden können, müssen Sie eine Azure-Sprachressource erstellen, wodurch Sie die Anmeldeinformationen erhalten, die zum Erstellen eines Projekts und zum Trainieren eines Modells erforderlich sind. Sie benötigen außerdem ein Azure-Speicherkonto, in das Sie Ihr Dataset hochladen können, das zum Erstellen Ihres Modells verwendet wird.

> [!IMPORTANT]
> Für einen schnellen Einstieg empfiehlt es sich, mithilfe der unten angegebenen Schritte eine neue Azure-Sprachressource zu erstellen, mit der Sie die Ressource erstellen und gleichzeitig ein Speicherkonto konfigurieren können. Dies ist in einem Durchgang einfacher zu erledigen als später.
>
> Wenn Sie eine bereits vorhandene Ressource verwenden möchten, müssen Sie sie und ein Speicherkonto separat konfigurieren. Weitere Informationen finden Sie in den [**Projektanforderungen**](../../how-to/create-project.md#using-a-pre-existing-azure-resource).

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

1. [Laden Sie die Beispieldaten](https://go.microsoft.com/fwlink/?linkid=2175083) für diesen Schnellstart von GitHub herunter.

2. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com) zu Ihrem Azure-Speicherkonto. Navigieren Sie zu Ihrem Konto, und laden Sie die Beispieldaten in das Konto hoch.

Das bereitgestellte Beispieldataset enthält etwa 200 Filmzusammenfassungen, die zu einer oder mehreren der folgenden Klassen gehören: „Mystery“, „Drama“, „Thriller“, „Comedy“, „Action“.

## <a name="create-a-custom-classification-project"></a>Erstellen eines Projekts zur benutzerdefinierten Klassifizierung

1. Melden Sie sich bei [Language Studio](https://aka.ms/languageStudio) an. Es wird ein Fenster angezeigt, in dem Sie Ihr Abonnement und Ihre Sprachressource auswählen können. Wählen Sie die Ressource aus, die Sie im Schritt oben erstellt haben.

2. Wählen Sie im Abschnitt **Text klassifizieren** von Language Studio in den verfügbaren Diensten die **benutzerdefinierte Textklassifizierung** aus, und wählen Sie sie aus.

3. Wählen Sie im oberen Menü Ihrer Projektseite **Neues Projekt erstellen** aus. Durch das Erstellen eines Projekts können Sie Daten kennzeichnen sowie Ihre Modelle trainieren, auswerten, verbessern und bereitstellen. 

    :::image type="content" source="../../media/create-project.png" alt-text="Screenshot der Seite zur Projekterstellung." lightbox="../../media/create-project.png":::
<!--
4. If you have created your resource using the steps above, the **Connect storage** step will be completed already. You only need to do this step once for each resource you use and it is irreversible. If you connect a storage account to your resource, you cannot disconnect it later.

    :::image type="content" source="../../../custom-named-entity-recognition/media/connect-storage.png" alt-text="A screenshot showing the storage connection screen." lightbox="../../../custom-named-entity-recognition/media/connect-storage.png":::
-->
4. Wenn Sie Ihre Ressource mithilfe der oben genannten Schritten erstellt haben, ist der Schritt **Verbindungsherstellung mit dem Speicher** bereits abgeschlossen. Andernfalls müssen Sie [Rollen für Ihr Speicherkonto](../../how-to/create-project.md#roles-for-your-storage-account) zuweisen, bevor Sie dieses mit der Ressource verbinden.

5. Geben Sie die Projektinformationen ein, darunter einen Namen, eine Beschreibung und die Sprache der Dateien in Ihrem Projekt. Sie können den Namen Ihres Projekts später nicht mehr ändern.
    >[!TIP]
    > Ihr Dataset muss nicht zur Gänze in derselben Sprache vorliegen. Sie können mehrere Dateien verwenden, jede mit jeweils einer anderen unterstützten Sprache. Wenn Ihr Dataset Dateien in verschiedenen Sprachen enthält oder Sie zur Laufzeit mit verschiedenen Sprachen rechnen, wählen Sie **mehrsprachiges Dataset aktivieren** aus, wenn Sie die grundlegenden Informationen für Ihr Projekt eingeben.

6. Wählen Sie Ihren Projekttyp aus. In diesem Schnellstart werden wir ein Projekt zur Klassifizierung mehrerer Beschriftungen erstellen, bei dem Sie derselben Datei mehrere Klassen zuweisen können. Klicken Sie dann auf **Weiter**. Erfahren Sie mehr über die [Projekttypen](../../glossary.md#project-types).

7. Wählen Sie den Container aus, in den Sie Ihre Daten hochgeladen haben. Für diese Schnellstartanleitung verwenden wir die vorhandene Tagdatei, die im Container verfügbar ist. Klicken Sie dann auf **Weiter**.

8. Überprüfen Sie die eingegebenen Daten, und wählen Sie **Projekt erstellen** aus.
    
## <a name="train-your-model"></a>Trainieren Ihres Modells

In der Regel importieren Sie nach dem Erstellen eines Projekts Ihre Daten und beginnen mit dem [Kennzeichnen der enthaltenen Entitäten](../../how-to/tag-data.md), um das Klassifizierungsmodell zu trainieren. Im Rahmen dieser Schnellstartanleitung verwenden Sie die zuvor heruntergeladene und in Ihrem Azure-Speicherkonto gespeicherte Beispieldatendatei mit Tags.

Ein Modell ist das Machine Learning-Objekt, das trainiert wird, um Text zu klassifizieren. Ihr Modell lernt anhand der Beispieldaten und kann anschließend Tickets für den technischen Support klassifizieren.

So beginnen Sie mit dem Trainieren Ihres Modells:

1. Wählen Sie im Menü links **Trainieren** aus.

2. Wählen Sie **Neues Modell trainieren** aus, und geben Sie den Namen des Modells im Textfeld darunter ein.

    :::image type="content" source="../../media/train-model.png" alt-text="Screenshot der Modellauswahlseite für das Training" lightbox="../../media/train-model.png":::

3. Klicken Sie unten auf der Seite auf die Schaltfläche **Trainieren**.

    > [!NOTE]
    > * Während des Trainings werden die Daten in 2 Mengen aufgeteilt: 80 % für das Training und 20 % für das Testen. Weitere Informationen zur Aufteilung von Daten finden Sie [hier](../../how-to/train-model.md#data-splits)
    > * Das Training kann bis zu mehreren Stunden dauern.

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells


Im Allgemeinen überprüfen Sie nach dem Trainieren eines Modells seine [Auswertungsdetails](../../how-to/view-model-evaluation.md) und [nehmen bei Bedarf Verbesserungen vor](../../how-to/improve-model.md). Im Rahmen dieser Schnellstartanleitung stellen Sie Ihr Modell einfach bereit, wodurch es Ihnen zum Ausprobieren zur Verfügung steht.

Nachdem Ihr Modell trainiert wurde, können Sie es bereitstellen. Mit der Bereitstellung Ihres Modells können Sie damit beginnen, mithilfe der [Analyse-API](https://aka.ms/ct-runtime-swagger) Text zu klassifizieren.

1. Wählen Sie im Menü auf der linken Seite **Modell bereitstellen** aus.

2. Wählen Sie das Modell aus, das Sie bereitstellen möchten, und wählen Sie dann **Modell bereitstellen** aus.

## <a name="test-your-model"></a>Testen des Modells

Nachdem Ihr Modell bereitgestellt wurde, können Sie damit beginnen, es für die Textklassifizierung zu verwenden. Verwenden Sie die folgenden Schritte, um Ihre erste Textklassifizierungsanforderung zu senden. 

1. Wählen Sie im Menü auf der linken Seite **Modell testen** aus.

2. Wählen Sie das Modell aus, das Sie testen möchten.

3. Fügen Sie ihren Text in das Textfeld ein. Alternativ können Sie eine `.txt`-Datei hochladen. 

4. Klicken Sie auf **Test ausführen**.

5. Auf der Registerkarte **Ergebnis** werden die vorhergesagten Klassen für Ihren Text angezeigt. Auf der Registerkarte **JSON** können Sie außerdem die JSON-Antwort anzeigen. 

    :::image type="content" source="../../media/test-model-results.png" alt-text="Screenshot: Modelltestergebnisse. Das Beispiel stammt aus CMU Movie Summary, CC BY-SA 3.0, von Microsoft bearbeitet" lightbox="../../media/test-model-results.png":::

## <a name="clean-up-projects"></a>Bereinigen von Projekten

Wenn Sie Ihr Projekt nicht mehr benötigen, können Sie es von Ihrer [Projektseite in Language Studio](https://aka.ms/custom-classification
) löschen. Wählen Sie das zu löschende Projekt aus, und klicken Sie dann auf **Löschen**.

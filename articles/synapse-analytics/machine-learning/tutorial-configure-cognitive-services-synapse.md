---
title: 'Schnellstart: Voraussetzungen für Cognitive Services in Azure Synapse Analytics'
description: Hier erfahren Sie, wie Sie die Voraussetzungen für die Verwendung von Cognitive Services in Azure Synapse konfigurieren.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: d72db64026ff3d4d4cee759b34047662248737a6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845131"
---
# <a name="quickstart-configure-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Schnellstart: Konfigurieren der Voraussetzungen für die Verwendung von Cognitive Services in Azure Synapse Analytics

In dieser Schnellstartanleitung erfahren Sie, wie Sie die Voraussetzungen für die sichere Nutzung von Azure Cognitive Services in Azure Synapse Analytics einrichten. Indem Sie Azure Cognitive Services verknüpfen, können Sie diese aus verschiedenen Funktionen in Synapse verwenden.

In diesem Schnellstart werden folgende Themen behandelt:
> [!div class="checklist"]
> - Erstellen einer Cognitive Services-Ressource wie Textanalyse oder Anomalieerkennung
> - Speichern eines Authentifizierungsschlüssel für Cognitive Services-Ressourcen als Geheimnisse in Azure Key Vault und Konfigurieren des Zugriffs für einen Azure Synapse Analytics-Arbeitsbereich
> - Erstellen eines mit Azure Key Vault verknüpften Diensts in Ihrem Azure Synapse Analytics-Arbeitsbereich
> - Erstellen eines verknüpften Azure Cognitive Services-Diensts in Ihrem Azure Synapse Analytics-Arbeitsbereich

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem als Standardspeicher konfigurierten Azure Data Lake Storage Gen2-Speicherkonto. Für das hier verwendete Azure Data Lake Storage Gen2-Dateisystem müssen Sie über die Rolle *Mitwirkender an Storage-Blobdaten* verfügen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-cognitive-services-resource"></a>Erstellen einer Cognitive Services-Ressource

[Azure Cognitive Services](../../cognitive-services/index.yml) beinhaltet zahlreiche Typen von Diensten. Die folgenden Dienste sind Beispiele, die in den Azure Synapse-Tutorials verwendet werden.

Sie können eine [Textanalyseressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) im Azure-Portal erstellen:

![Screenshot: Textanalyse im Portal mit der Schaltfläche „Erstellen“](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Sie können eine [Anomalieerkennungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) im Azure-Portal erstellen:

![Screenshot: Anomalieerkennung im Portal mit der Schaltfläche „Erstellen“](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

Sie können im Azure-Portal eine Ressource des Typs [Formularerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) erstellen:

![Screenshot: Formularerkennung im Portal mit der Schaltfläche „Erstellen“.](media/tutorial-configure-cognitive-services/tutorial-configure-form-recognizer.png)

Sie können im Azure-Portal eine Ressource des Typs [Textübersetzung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) erstellen:

![Screenshot: Textübersetzung im Portal mit der Schaltfläche „Erstellen“.](media/tutorial-configure-cognitive-services/tutorial-configure-translator.png)

Sie können im Azure-Portal eine Ressource des Typs [Maschinelles Sehen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) erstellen:

![Screenshot: maschinelles Sehen im Portal mit der Schaltfläche „Erstellen“.](media/tutorial-configure-cognitive-services/tutorial-configure-computer-vision.png)


Erstellen Sie im Azure-Portal eine Ressource des Typs [Gesicht](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace):

![Screenshot: „Gesicht“ im Portal mit der Schaltfläche „Erstellen“.](media/tutorial-configure-cognitive-services/tutorial-configure-face.png)


Sie können im Azure-Portal eine Ressource des Typs [Sprache](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) erstellen:

![Screenshot: Sprache im Portal mit der Schaltfläche „Erstellen“.](media/tutorial-configure-cognitive-services/tutorial-configure-speech.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Erstellen eines Schlüsseltresors und Konfigurieren von Geheimnissen und Zugriff

1. Erstellen Sie einen [Schlüsseltresor](https://ms.portal.azure.com/#create/Microsoft.KeyVault) im Azure-Portal.
2. Navigieren Sie zu **Key Vault** > **Zugriffsrichtlinien**, und gewähren Sie der [MSI des Azure Synapse-Arbeitsbereichs](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) Berechtigungen zum Lesen von Geheimnissen aus Azure Key Vault.

   > [!NOTE]
   > Stellen Sie sicher, dass die Richtlinienänderungen gespeichert werden. Dieser Schritt ist leicht zu übersehen.

   ![Screenshot: Auswahl für das Hinzufügen einer Zugriffsrichtlinie](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Navigieren Sie zu Ihrer Cognitive Services-Ressource. Navigieren Sie beispielsweise zu **Anomalieerkennung** > **Schlüssel und Endpunkt**. Kopieren Sie dann einen der beiden Schlüssel in die Zwischenablage.

4. Wechseln Sie zu **Key Vault** > **Geheimnis**, um ein neues Geheimnis zu erstellen. Geben Sie den Namen des Geheimnisses an, und fügen Sie dann den Schlüssel aus dem vorherigen Schritt in das Feld **Wert** ein. Wählen Sie abschließend **Erstellen**.

   ![Screenshot: Auswahl für die Erstellung eines Geheimnisses](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Merken oder notieren Sie sich diesen Geheimnisnamen. Sie benötigen ihn später, wenn Sie den verknüpften Azure Cognitive Services-Dienst erstellen.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Erstellen eines mit Azure Key Vault verknüpften Diensts in Azure Synapse

1. Öffnen Sie Ihren Arbeitsbereich in Azure Synapse Studio. 
2. Navigieren Sie zu **Verwalten** > **Verknüpfte Dienste**. Erstellen Sie einen mit **Azure Key Vault** verknüpften Dienst, indem Sie auf den soeben erstellten Schlüsseltresor verweisen. 
3. Überprüfen Sie die Verbindung, indem Sie die Schaltfläche **Verbindung testen** auswählen. Ist die Verbindung grün, wählen Sie **Erstellen** und dann **Alle veröffentlichen** aus, um die Änderung zu speichern.

![Screenshot: Azure Key Vault als neuer verknüpfter Dienst](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)


## <a name="create-an-azure-cognitive-service-linked-service-in-azure-synapse"></a>Erstellen eines verknüpften Azure Cognitive Services-Dienst in Azure Synapse

1. Öffnen Sie Ihren Arbeitsbereich in Azure Synapse Studio.
2. Navigieren Sie zu **Verwalten** > **Verknüpfte Dienste**. Erstellen Sie einen verknüpften **Azure Cognitive Services**-Dienst, indem Sie auf die gerade erstellte Cognitive Services-Instanz verweisen. 
3. Überprüfen Sie die Verbindung, indem Sie die Schaltfläche **Verbindung testen** auswählen. Ist die Verbindung grün, wählen Sie **Erstellen** und dann **Alle veröffentlichen** aus, um die Änderung zu speichern.

![Screenshot: Azure Cognitive Services-Instanz als neuer verknüpfter Dienst](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-linked-service.png)

Sie können nun mit einem der Tutorials für die Verwendung der Cognitive Services-Benutzeroberfläche in Azure Synapse Studio fortfahren.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Stimmungsanalyse mit Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Anomalieerkennung mit Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Assistent für die Modellbewertung in dedizierten SQL-Pools von Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)

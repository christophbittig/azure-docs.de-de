---
title: 'Schnellstart: Verknüpfen eines Azure Machine Learning-Arbeitsbereichs'
description: Verknüpfen Ihres Synapse-Arbeitsbereichs mit einem Azure Machine Learning-Arbeitsbereich
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 10/01/2021
author: nelgson
ms.author: negust
ms.openlocfilehash: 7d6b9a81f5e5e948704b4597a7283cd976b124c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847197"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Schnellstart: Erstellen eines neuen verknüpften Azure Machine Learning-Diensts in Synapse

In dieser Schnellstartanleitung wird ein Azure Synapse Analytics-Arbeitsbereich mit einem Azure Machine Learning-Arbeitsbereich verknüpft. Durch das Verknüpfen dieser Arbeitsbereiche können Sie Azure Machine Learning aus verschiedenen Erfahrungen in Synapse nutzen.

Beispielsweise ermöglicht diese Verknüpfung mit einem Azure Machine Learning-Arbeitsbereich folgende Erfahrungen:

- Ausführen Ihrer Azure Machine Learning-Pipelines als Schritt in Ihren Synapse-Pipelines. Weitere Informationen finden Sie unter [Ausführen von Azure Machine Learning-Pipelines](../../data-factory/transform-data-machine-learning-service.md).

- Anreichern Ihrer Daten mit Vorhersagen, indem Sie ein Machine Learning-Modell aus der Azure Machine Learning-Modellregistrierung einbinden und das Modell in Synapse SQL-Pools bewerten. Weitere Informationen finden Sie im [Tutorial: Assistent für die Bewertung von Machine Learning-Modellen für Synapse SQL-Pools](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="two-types-of-authentication"></a>Zwei Authentifizierungstypen
Es gibt zwei Typen von Identitäten, die Sie beim Erstellen eines verknüpften Azure Machine Learning-Diensts in Azure Synapse verwenden können.
* Synapse-Arbeitsbereich: verwaltete Identität
* Dienstprinzipal

In den folgenden Abschnitten finden Sie einen Leitfaden zum Erstellen eines mit Azure Machine Learning verknüpften Diensts mithilfe dieser beiden unterschiedlichen Authentifizierungstypen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- Als Standardspeicher konfigurierter [Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem ADLS Gen2-Speicherkonto. Sie müssen **Mitwirkender an Storage-Blobdaten** des ADLS Gen2-Dateisystems sein, mit dem Sie arbeiten.
- [Azure Machine Learning-Arbeitsbereich](../../machine-learning/how-to-manage-workspace.md)
- Wenn Sie die Verwendung eines Dienstprinzipals gewählt haben, benötigen Sie Berechtigungen zum Erstellen eines Dienstprinzipals und von Geheimnisses (oder müssen dies von jemandem mit den benötigten Berechtigungen anfordern), die Sie zum Erstellen des verknüpften Diensts verwenden können. Beachten Sie, dass diesem Dienstprinzipal die Rolle „Mitwirkender“ im Azure Machine Learning-Arbeitsbereich zugewiesen werden muss.
- Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="create-a-linked-service-using-the-synapse-workspace-managed-identity"></a>Erstellen eines verknüpften Diensts mithilfe der verwalteten Identität des Synapse-Arbeitsbereichs

In diesem Abschnitt erfahren Sie, wie Sie einen mit Azure Machine Learning verknüpften Dienst in Azure Synapse mithilfe der [verwalteten Identität des Azure Synapse-Arbeitsbereichs](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) erstellen.

### <a name="give-msi-permission-to-the-azure-ml-workspace"></a>Erteilen der MSI-Berechtigung für den Azure ML-Arbeitsbereich

1. Navigieren Sie zu Ihrer Azure Machine Learning-Arbeitsbereichsressource im Azure-Portal, und wählen Sie **Access Control** aus.

1. Erstellen Sie eine Rollenzuweisung, und fügen Sie Ihre verwaltete Dienstidentität (Managed Service Identity, MSI) für den Synapse-Arbeitsbereich als *Mitwirkender* des Azure Machine Learning-Arbeitsbereichs hinzu. Beachten Sie, dass hierfür erforderlich ist, dass Sie Besitzer der Ressourcengruppe sind, zu der der Azure Machine Learning-Arbeitsbereich gehört. Wenn Sie Probleme beim Suchen Ihrer verwalteten Dienstidentität für den Synapse-Arbeitsbereich haben, suchen Sie nach dem Namen des Synapse-Arbeitsbereichs.

### <a name="create-an-azure-ml-linked-service"></a>Erstellen eines mit Azure ML verknüpften Diensts

1. Wechseln Sie in dem Synapse-Arbeitsbereich, in dem Sie den neuen verknüpften Azure Machine Learning-Dienst erstellen möchten, zu **Verwaltung** > **Verknüpfter Dienst**, und erstellen Sie einen neuen verknüpften Dienst vom Typ „Azure Machine Learning“.

   ![Erstellen eines verknüpften Diensts](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Füllen Sie das Formular aus:

   - Geben Sie Details dazu an, mit welchem Azure Machine Learning-Arbeitsbereich Sie eine Verknüpfung herstellen möchten. Dies umfasst Details zum Abonnement- und Arbeitsbereichsnamen.
   
   - Auswählen der Authentifizierungsmethode: **Verwaltete Identität**
  
3. Klicken Sie auf **Verbindung testen**, um zu überprüfen, ob die Konfiguration korrekt ist. Wenn der Verbindungstest bestanden wird, klicken Sie auf **Speichern**.

   Wenn der Verbindungstest zu einem Fehler führt, stellen Sie sicher, dass die MSI des Azure Synapse-Arbeitsbereichs über Berechtigungen für den Zugriff auf diesen Azure Machine Learning-Arbeitsbereich verfügt, und wiederholen Sie den Vorgang.

## <a name="create-a-linked-service-using-a-service-principal"></a>Erstellen eines verknüpften Diensts mithilfe eines Dienstprinzipals

In diesem Abschnitt erfahren Sie, wie Sie einen verknüpften Azure Machine Learning-Dienst mit einem Dienstprinzipal erstellen.

### <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

In diesem Schritt wird ein neuer Dienstprinzipal erstellt. Wenn Sie einen vorhandenen Dienstprinzipal verwenden möchten, können Sie diesen Schritt überspringen.

1. Öffnen Sie das Azure-Portal. 

1. Wechseln Sie zu **Azure Active Directory** -> **App-Registrierungen**.

1. Klicken Sie auf **Neue Registrierung**. Befolgen Sie dann die Anweisungen, um eine neue Anwendung zu registrieren.

1. Nachdem die Anwendung registriert wurde, generieren Sie ein Geheimnis für die Anwendung. Wechseln Sie zu **Ihre Anwendung** -> **Zertifikat und Geheimnis**. Klicken Sie auf **Geheimen Clientschlüssel hinzufügen**, um ein Geheimnis zu generieren. Bewahren Sie das Geheimnis sicher auf, es wird später verwendet.

   ![Generieren eines Geheimnisses](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Erstellen Sie einen Dienstprinzipal für die Anwendung. Wechseln Sie zu **Ihre Anwendung** -> **Übersicht**, und klicken Sie dann auf **Dienstprinzipal erstellen**. In einigen Fällen wird dieser Dienstprinzipal automatisch erstellt.

   ![Erstellen eines Dienstprinzipals](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Fügen Sie den Dienstprinzipal als „Mitwirkender“ des Azure Machine Learning-Arbeitsbereichs hinzu. Beachten Sie, dass hierfür erforderlich ist, dass Sie Besitzer der Ressourcengruppe sind, zu der der Azure Machine Learning-Arbeitsbereich gehört.

   ![Zuweisen der Rolle „Mitwirkender“](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

### <a name="create-an-azure-ml-linked-service"></a>Erstellen eines mit Azure ML verknüpften Diensts

1. Wechseln Sie in dem Synapse-Arbeitsbereich, in dem Sie den neuen verknüpften Dienst „Azure Machine Learning“ erstellen möchten, zu **Verwaltung** -> **Verknüpfter Dienst**, und erstellen Sie einen neuen verknüpften Dienst mit dem Typ „Azure Machine Learning“.

   ![Erstellen eines verknüpften Diensts](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Füllen Sie das Formular aus:

   - Geben Sie Details dazu an, mit welchem Azure Machine Learning-Arbeitsbereich Sie eine Verknüpfung herstellen möchten. Dies umfasst Details zum Abonnement- und Arbeitsbereichsnamen.

   - Auswählen der Authentifizierungsmethode: **Dienstprinzipal**

   - Dienstprinzipal-ID: Dies ist die **Anwendungs(client)-ID** der Anwendung.

   > [!NOTE]
   > Diese ID ist NICHT der Name der Anwendung. Sie finden diese ID auf der Übersichtsseite der Anwendung. Dabei sollte es sich um eine lange Zeichenfolge handeln, die in etwa wie folgt aussieht: „81707eac-ab38-406u-8f6c-10ce76a568d5“.

   - Dienstprinzipalschlüssel: Das Geheimnis, das Sie im vorherigen Abschnitt generiert haben.

3. Klicken Sie auf **Verbindung testen**, um zu überprüfen, ob die Konfiguration korrekt ist. Wenn der Verbindungstest bestanden wird, klicken Sie auf **Speichern**.

   Wenn der Verbindungstest zu einem Fehler führt, stellen Sie sicher, dass die Dienstprinzipal-ID und das Geheimnis korrekt sind, und versuchen Sie es noch mal.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Assistent für die Bewertung von Machine Learning-Modellen: Dedizierter SQL-Pool](tutorial-sql-pool-model-scoring-wizard.md)
- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)

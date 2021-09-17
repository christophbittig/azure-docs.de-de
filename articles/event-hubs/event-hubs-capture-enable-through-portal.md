---
title: 'Event Hubs: Erfassen von Streamingereignissen über das Azure-Portal'
description: In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal die Erfassung von über Azure Event Hubs gestreamten Ereignissen aktivieren.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: b603af17ca4e46b8004b7b2550cddd87164da157
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253636"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Aktivieren der Erfassung von über Azure Event Hubs gestreamten Ereignissen

Mit Azure [Event Hubs Capture][capture-overview] können Sie die Streamingdaten in Event Hubs automatisch an ein [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)- oder [Azure Data Lake Storage Gen1 oder Gen 2](https://azure.microsoft.com/services/data-lake-store/)-Konto Ihrer Wahl übermitteln.

Sie können Capture über das [Azure-Portal](https://portal.azure.com) zum Zeitpunkt der Event Hub-Erstellung konfigurieren. Die Daten können entweder in einem Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)-Container oder in einem [Azure Data Lake Storage Gen1 oder Gen 2](https://azure.microsoft.com/services/data-lake-store/)-Konto erfasst werden.

Weitere Informationen finden Sie in der [Übersicht über Event Hubs Capture][capture-overview].

> [!IMPORTANT]
> Das Zielspeicherkonto (Azure Storage oder Azure Data Lake Storage) muss sich im gleichen Abonnement wie der Event Hub befinden.

## <a name="capture-data-to-azure-storage"></a>Erfassen von Daten in Azure Storage

Beim Erstellen eines Event Hubs können Sie Capture aktivieren, indem Sie auf dem Portalblatt **Event Hub erstellen** auf die Schaltfläche **Ein** klicken. Anschließend geben Sie ein Speicherkonto und einen Container an, indem Sie im Feld **Capture Provider** (Capture-Anbieter) auf **Azure Storage** klicken. Da Event Hubs Capture die Dienst-zu-Dienst-Authentifizierung mit Speicher verwendet, müssen Sie keine Speicherverbindungszeichenfolge angeben. Die Ressourcenauswahl wählt automatisch den Ressourcen-URI für Ihr Speicherkonto. Wenn Sie Azure Resource Manager verwenden, müssen Sie diesen URI explizit als Zeichenfolge angeben.

Das Standadzeitfenster beträgt fünf Minuten. Der Mindestwert ist 1, der Höchstwert 15. Für das **Größenfenster** gilt ein Bereich von 10 bis 500 MB.

![Zeitfenster für die Erfassung][1]

> [!NOTE]
> Sie können die Ausgabe leerer Dateien, wenn während des Erfassungszeitfensters keine Ereignisse auftreten, aktivieren oder deaktivieren. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Erfassen von Daten in Azure Data Lake Storage Gen 2 

1. Folgen Sie dem Artikel [Speicherkonto erstellen](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account), um ein Azure Storage-Konto zu erstellen. Legen Sie auf der Registerkarte **Erweitert** die Option **Hierarchischer Namespace** auf **Aktiviert** fest, um daraus ein Azure Data Lake Storage Gen 2-Konto zu machen. Das Azure Storage-Konto muss sich in demselben Abonnement wie der Event Hub befinden.
2. Führen Sie die folgenden Schritte aus, wenn Sie einen Event Hub erstellen: 

    1. Wählen Sie für **Capture****Ein** aus. 
    2. Wählen Sie **Azure Storage** als Erfassungsanbieter aus. Die **Azure Data Lake Store**-Option, die für den **Erfassungsanbieter** angezeigt wird, ist für Gen 1 von Azure Data Lake Storage. Um einen Azure Data Lake Storage der Gen 2 zu verwenden, wählen Sie **Azure Storage** aus.
    2. Wählen Sie die Schaltfläche **Container auswählen** aus. 

        ![Aktivieren der Erfassung in Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Wählen Sie das Konto **Azure Data Lake Storage Gen 2** in der Liste aus. 

    ![Auswählen von Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Wählen Sie den **Container** (Dateisystem in Data Lake Storage Gen 2) aus.

    ![Auswählen des Dateisystems im Speicher](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Wählen Sie auf der Seite **Event Hub erstellen** den Befehl **Erstellen** aus. 

    ![Schaltfläche „Erstellen“ auswählen](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Der Container, den Sie in Azure Data Lake Storage Gen 2 mit dieser Benutzeroberfläche erstellen, wird unter **Dateisysteme** im **Storage-Explorer** angezeigt. Ebenso wird das Dateisystem, das Sie in einem Data Lake Storage Gen 2-Konto erstellen, als Container in dieser Benutzeroberfläche angezeigt. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Erfassen von Daten in Azure Data Lake Storage Gen 1 

Wenn Sie Daten in Azure Data Lake Storage Gen 1 erfassen möchten, müssen Sie ein Data Lake Storage Gen 1-Konto und einen Event Hub erstellen:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Erstellen eines Azure Data Lake Storage Gen 1-Kontos und von Ordnern

1. Erstellen Sie gemäß der Anleitung unter [Erste Schritte mit Azure Data Lake Storage Gen 1 über das Azure-Portal](../data-lake-store/data-lake-store-get-started-portal.md) ein Data Lake Storage Gen 1-Konto.
2. Führen Sie die Anleitungsschritte im Abschnitt [Zuweisen von Berechtigungen für Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) aus, um unter dem Data Lake Storage Gen 1-Konto einen Ordner zu erstellen, in dem Sie die Daten aus Event Hubs erfassen möchten. Sie können für Event Hubs auch Berechtigungen zuweisen, damit diese Daten in Ihr Data Lake Storage Gen 1-Konto schreiben können.  


### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

1. Der Event Hub muss demselben Azure-Abonnement angehören wie das Azure Data Lake Store Gen 1-Konto, das Sie erstellt haben. Erstellen Sie den Event Hub, indem Sie auf der Portalseite **Event Hub erstellen** unter **Capture** auf die Schaltfläche **Ein** klicken. 
2. Wählen Sie auf der Portalseite **Event Hub erstellen** im Feld **Capture Provider** (Capture-Anbieter) die Option **Azure Data Lake Store** aus.
3. Geben Sie in **Store auswählen** neben der Dropdownliste **Data Lake Store** das zuvor erstellte Data Lake Storage Gen 1-Konto und im Feld **Data Lake-Pfad** den Pfad zu dem von Ihnen erstellten Datenordner an.

    ![Auswählen des Data Lake Storage-Kontos][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Hinzufügen oder Konfigurieren von Capture für einen bereits vorhandenen Event Hub

Capture kann für vorhandene Event Hubs in Event Hubs-Namespaces konfiguriert werden. Wenn Sie Capture für einen vorhandenen Event Hub aktivieren oder Ihre Capture-Einstellungen ändern möchten, klicken Sie auf den Namespace, um den Bildschirm mit der Übersicht zu laden. Klicken Sie dann auf den Event Hub, den Sie aktivieren bzw. dessen Capture-Einstellung Sie ändern möchten. Klicken Sie abschließend auf der linken Seite der geöffneten Seite auf die Option **Capture**, und bearbeiten Sie die Einstellungen, wie in den folgenden Abbildungen dargestellt:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurieren von Azure Blob Storage][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Konfigurieren von Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Konfigurieren von Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Event Hubs Capture finden Sie in der [Übersicht über Event Hubs Capture][capture-overview].
- Sie können Event Hubs Capture auch über Azure Resource Manager-Vorlagen konfigurieren. Weitere Informationen finden Sie unter [Aktivieren von Capture über eine Azure Resource Manager-Vorlage](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Erstellen eines Azure Event Grid-Abonnements mit einem Event Hubs-Namespace als Quelle](store-captured-data-data-warehouse.md)
- [Erste Schritte mit Azure Data Lake Store über das Azure-Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md

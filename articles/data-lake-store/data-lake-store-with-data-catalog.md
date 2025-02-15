---
title: Integrieren von Data Lake Storage Gen1 in Azure Data Catalog
description: Hier erfahren Sie, wie Sie Daten aus Azure Data Lake Storage Gen1 in Azure Data Catalog integrieren, um Daten in Ihrer Organisation auffindbar zu machen.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 364f7c11e9f15eada8ceb7ac0b73436f71bf38ee
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676882"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registrieren von Daten aus Azure Data Lake Storage Gen1 in Azure Data Catalog
In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Storage Gen1 in Azure Data Catalog integrieren, um Ihre Daten mittels Integration in Data Catalog innerhalb einer Organisation auffindbar zu machen. Weitere Informationen zum Katalogisieren von Daten finden Sie unter [Azure Data Catalog](../data-catalog/overview.md). Informationen zu den Szenarien, in denen Sie Data Catalog verwenden können, finden Sie unter [Häufige Szenarien mit Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Aktiviertes Azure-Abonnement** für Data Lake Storage Gen1. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md).
* **Ein Data Lake Storage Gen1-Konto.** Befolgen Sie die Anweisungen unter [Erste Schritte mit Azure Data Lake Storage Gen1 über das Azure-Portal](data-lake-store-get-started-portal.md). Erstellen Sie für dieses Tutorial ein Data Lake Storage Gen1-Konto namens **datacatalogstore**.

    Nachdem Sie das Konto erstellt haben, laden Sie ein Beispieldataset in das Konto hoch. In diesem Tutorial laden wir alle CSV-Dateien im Ordner **AmbulanceData** in das [Azure Data Lake-Git-Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/) hoch. Sie können verschiedene Clients verwenden, z.B. den [Azure Storage-Explorer](https://storageexplorer.com/), um Daten in einen Blobcontainer hochzuladen.
* **Azure Data Catalog**. In Ihrer Organisation muss bereits ein Azure Data Catalog für Ihre Organisation erstellt worden sein. Nur ein Katalog ist für jede Organisation zulässig.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrieren von Data Lake Storage Gen1 als Quelle für Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Navigieren Sie zu `https://azure.microsoft.com/services/data-catalog`, und klicken Sie auf **Erste Schritte**.
1. Melden Sie sich beim Azure Data Catalog-Portal an, und klicken Sie auf **Daten veröffentlichen**.

    ![Registrieren einer Datenquelle](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrieren einer Datenquelle")
1. Klicken Sie auf der nächsten Seite auf **Anwendung starten**. Dadurch wird die Manifestdatei der Anwendung auf Ihren Computer heruntergeladen. Doppelklicken Sie auf die Manifestdatei, um die Anwendung zu starten.
1. Klicken Sie auf der Seite „Willkommen“ auf **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein.

    ![Bildschirm „Willkommen“](./media/data-lake-store-with-data-catalog/welcome.screen.png "Bildschirm „Willkommen“")
1. Wählen Sie auf der Seite „Datenquelle auswählen“ die Option **Azure Data Lake Store** aus, und klicken Sie dann auf **Weiter**.

    ![Auswählen einer Datenquelle](./media/data-lake-store-with-data-catalog/select-source.png "Auswählen einer Datenquelle")
1. Geben Sie auf der nächsten Seite den Namen des Data Lake Storage Gen1-Kontos an, das Sie in Data Catalog registrieren möchten. Behalten Sie für die anderen Optionen die Standardwerte bei, und klicken Sie dann auf **Verbinden**.

    ![Verbinden mit einer Datenquelle](./media/data-lake-store-with-data-catalog/connect-to-source.png "Verbinden mit einer Datenquelle")
1. Die nächste Seite kann in die folgenden Segmente aufgeteilt werden.

    a. Das Feld **Serverhierarchie** stellt die Ordnerstruktur des Data Lake Storage Gen1-Kontos dar. **$Root** stellt den Stamm des Data Lake Storage Gen1-Kontos dar, und **AmbulanceData** steht für den Ordner, der im Stamm des Data Lake Storage Gen1-Kontos erstellt wurde.

    b. Im Feld **Verfügbare Objekte** werden die Dateien und Ordner im Ordner **AmbulanceData** aufgeführt.

    c. Im Feld **Zu registrierende Objekte** werden die Dateien und Ordner aufgeführt, die Sie in Azure Data Catalog registrieren möchten.

    ![Screenshot des Dialogfelds für das Speicherkonto in Microsoft Azure Data Catalog](./media/data-lake-store-with-data-catalog/view-data-structure.png "Anzeigen der Datenstruktur")
1. Für dieses Tutorial sollten Sie alle Dateien im Verzeichnis registrieren. Klicken Sie auf die Schaltfläche ![Verschieben von Objekten](./media/data-lake-store-with-data-catalog/move-objects.png "Verschieben von Objekten"), um alle Dateien in das Feld **Zu registrierende Objekte** zu verschieben.

    Da die Daten in einem organisationsweiten Datenkatalog registriert werden, empfiehlt es sich, einige Metadaten hinzuzufügen, die Sie später verwenden können, um die Daten schnell zu finden. Sie können z.B. eine E-Mail-Adresse für den Besitzer der Daten (z.B. der, der die Daten hochlädt) oder ein Tag zum Identifizieren der Daten hinzufügen. Der folgende Screenshot zeigt ein Tag, das Sie den Daten hinzufügen.

    ![Screenshot des Dialogfelds für das Speicherkonto in Microsoft Azure Data Catalog mit dem Tag, das den hervorgehobenen Daten hinzugefügt wurde](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Anzeigen der Datenstruktur")

    Klicken Sie auf **Registrieren**.
1. Die folgende Screenshot gibt an, dass die Daten erfolgreich in Data Catalog registriert wurden.

    ![Registrierung abgeschlossen](./media/data-lake-store-with-data-catalog/registration-complete.png "Anzeigen der Datenstruktur")
1. Klicken Sie auf **Portal anzeigen** , um zum Data Catalog-Portal zurückzukehren, und stellen Sie sicher, dass Sie jetzt über das Portal auf die registrierten Daten zugreifen können. Um die Daten zu suchen, können Sie das Tag verwenden, das Sie beim Registrieren der Daten angegeben haben.

     ![Suchen von Daten im Katalog](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Suchen von Daten im Katalog")
1. Sie können nun Vorgänge wie Hinzufügen von Anmerkungen und Dokumenten zu den Daten ausführen. Weitere Informationen finden Sie unter den folgenden Links.

    * [Hinzufügen von Anmerkungen zu Datenquellen in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Hinzufügen von Dokumenten zu Datenquellen in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Weitere Informationen
* [Hinzufügen von Anmerkungen zu Datenquellen in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Hinzufügen von Dokumenten zu Datenquellen in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrieren von Data Lake Storage Gen1 in andere Azure-Dienste](data-lake-store-integrate-with-other-services.md)
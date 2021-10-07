---
title: Freigeben und Empfangen von Daten aus Azure Blob Storage und Azure Data Lake Storage
description: Erfahren Sie, wie Sie Daten aus Azure Blob Storage und Azure Data Lake Storage freigeben und empfangen.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 09/10/2021
ms.openlocfilehash: 61189ba63c072f2c08acc55047d494b076288953
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743254"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Freigeben und Empfangen von Daten aus Azure Blob Storage und Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share unterstützt die auf Momentaufnahmen basierende Freigabe aus einem Speicherkonto. In diesem Artikel wird erläutert, wie Sie Daten aus Azure Blob Storage, Azure Data Lake Storage Gen1 und Azure Data Lake Storage Gen2 freigeben und empfangen können.

Azure Data Share unterstützt die Freigabe von Dateien, Ordnern und Dateisystemen aus Azure Data Lake Gen1 und Azure Data Lake Gen2. Darüber hinaus wird die Freigabe von Blobs, Ordnern und Containern aus Azure Blob Storage unterstützt. Sie können Block-, Anfüge- oder Seitenblobs freigeben und diese als Blockblobs empfangen. Freigegebene Daten aus diesen Quellen können durch Azure Data Lake Gen2 oder Azure Blob Storage empfangen werden.

Wenn Dateisysteme, Container oder Ordner in einer auf Momentaufnahmen basierenden Freigabe freigegeben werden, kann der Datenconsumer wahlweise eine vollständige Kopie der Freigabedaten erstellen. Alternativ kann die Funktion für inkrementelle Momentaufnahmen verwendet werden, um nur neue oder aktualisierte Dateien zu kopieren. Die Funktion für inkrementelle Momentaufnahmen basiert auf dem Zeitpunkt der letzten Änderung der Dateien. 

Vorhandene Dateien mit demselben Namen werden während der Momentaufnahmeerstellung überschrieben. Eine aus der Quelle gelöschte Datei wird nicht im Ziel gelöscht. Leere Unterordner in der Quelle werden nicht in das Ziel kopiert. 

## <a name="share-data"></a>Freigeben von Daten

Verwenden Sie die Informationen in den folgenden Abschnitten, um Daten unter Verwendung von Azure Data Share freizugeben. 
### <a name="prerequisites-to-share-data"></a>Voraussetzungen für das Freigeben von Daten

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ermitteln Sie für den Empfänger die E-Mail-Adresse für die Azure-Anmeldung. Der E-Mail-Alias des Empfängers kann zu diesem Zweck nicht verwendet werden.
* Befindet sich der Azure-Quelldatenspeicher nicht in dem Azure-Abonnement, in dem Sie die Data Share-Ressource erstellen, registrieren Sie den [Microsoft.DataShare-Ressourcenanbieter](concepts-roles-permissions.md#resource-provider-registration) in dem Abonnement, in dem sich der Azure-Datenspeicher befindet. 

### <a name="prerequisites-for-the-source-storage-account"></a>Voraussetzungen für das Quellspeicherkonto

* Azure Storage-Konto Wenn Sie noch kein Konto besitzen, [erstellen Sie ein Konto](../storage/common/storage-account-create.md).
* Berechtigung zum Schreiben im Speicherkonto. Die Schreibberechtigung ist in *Microsoft.Storage/storageAccounts/write* enthalten. Sie ist Bestandteil der Rolle „Mitwirkender“.
* Berechtigung zum Lesen oder Hinzufügen einer Rollenzuweisung zum Speicherkonto. Diese Berechtigung ist in *Microsoft.Authorization/role assignments/write* enthalten. Sie ist Bestandteil der Rolle „Besitzer“. 

### <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

### <a name="create-a-data-share-account"></a>Erstellen eines Data Share-Kontos

Erstellen Sie eine Azure Data Share-Ressource in einer Azure-Ressourcengruppe.

1. Öffnen Sie im Portal links oben das Menü, und klicken Sie dann auf **Ressource erstellen** (+).

1. Suchen Sie nach *Data Share*.

1. Wählen Sie **Data Share** und dann die Option **Erstellen** aus.

1. Geben Sie grundlegende Informationen zu Ihrer Azure Data Share-Ressource an: 

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | Subscription | Ihr Abonnement | Wählen Sie ein Azure-Abonnement für Ihr Data Share-Konto aus.|
    | Ressourcengruppe | *test-resource-group* | Erstellen Sie eine Ressourcengruppe, oder verwenden Sie eine vorhandene Ressourcengruppe. |
    | Standort | *USA (Ost) 2* | Wählen Sie eine Region für Ihr Data Share-Konto aus.
    | Name | *datashareaccount* | Vergeben Sie einen Namen für Ihr Data Share-Konto. |
    | | |

1. Wählen Sie **Überprüfen + erstellen** > **Erstellen** aus, um Ihr Data Share-Konto bereitzustellen. Die Bereitstellung eines neuen Data Share-Kontos dauert üblicherweise ca. 2 Minuten. 

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressource wechseln**.

### <a name="create-a-share"></a>Erstellen einer Freigabe

1. Navigieren Sie zur Seite **Übersicht** für Ihre Data Share-Ressource.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Screenshot: Data Share-Übersicht":::

1. Wählen Sie **Start sharing your data** (Mit Freigabe der Daten beginnen).

1. Klicken Sie auf **Erstellen**.   

1. Geben Sie die Details zu Ihrer Freigabe an. Geben Sie einen Namen, die Art der Freigabe, eine Beschreibung der Freigabeinhalte und Nutzungsbedingungen (optional) an. 

    ![Screenshot: Details zur Datenfreigabe](./media/enter-share-details.png "Eingabe von Details zur Datenfreigabe") 

1. Wählen Sie **Weiter**.

1. Wählen Sie **Datasets hinzufügen** aus, um Ihrer Freigabe Datasets hinzuzufügen. 

    ![Screenshot: Hinzufügen von Datasets zu Ihrer Freigabe](./media/datasets.png "Datasets.")

1. Wählen Sie einen Datasettyp aus, den Sie hinzufügen möchten. Die Liste der verfügbaren Datasettypen richtet sich danach, ob Sie im vorherigen Schritt eine direkte Freigabe oder eine Freigabe ausgewählt haben, die auf Momentaufnahmen basiert. 

    ![Screenshot: Auswahl eines Datasettyps](./media/add-datasets.png "Hinzufügen von Datasets")    

1. Wählen Sie das Objekt aus, das Sie freigaben möchten. Wählen Sie anschließend **Datasets hinzufügen** aus. 

    ![Screenshot: Auswählen eines freizugebenden Objekts](./media/select-datasets.png "Wählen Sie Datasets aus.")    

1. Geben Sie auf der Registerkarte **Empfänger** die E-Mail-Adresse Ihres Datenconsumers ein, indem Sie auf **+ Empfänger hinzufügen** klicken. 

    ![Screenshot: Hinzufügen von Empfänger-E-Mail-Adressen](./media/add-recipient.png "Hinzufügen von Empfängern") 

1. Wählen Sie **Weiter**.

1. Wenn Sie einen auf Momentaufnahmen basierenden Freigabetyp ausgewählt haben, können Sie den Momentaufnahmezeitplan zum Aktualisieren Ihrer Daten für den Datenconsumer einrichten. 

    ![Screenshot: Einstellungen für Momentaufnahmezeitplan](./media/enable-snapshots.png "Aktivieren von Momentaufnahmen") 

1. Wählen Sie eine Startzeit und ein Wiederholungsintervall aus. 

1. Wählen Sie **Weiter**.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die Paketinhalte, Einstellungen, Empfänger und Synchronisierungseinstellungen. Klicken Sie anschließend auf **Erstellen**.

Sie haben soeben Ihre Azure-Datenfreigabe erstellt. Der Empfänger Ihrer Datenfreigabe kann Ihre Einladung annehmen. 

## <a name="receive-data"></a>Empfangen von Daten

In den folgenden Abschnitten wird beschrieben, wie Sie freigegebene Daten empfangen.
### <a name="prerequisites-to-receive-data"></a>Voraussetzungen für den Empfang von Daten
Stellen Sie sicher, dass die folgenden erforderlichen Komponenten vorhanden sind, bevor Sie eine Einladung für eine Datenfreigabe akzeptieren: 

* Ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/).
* Eine Einladung von Azure. Der E-Mail-Betreff sollte „Azure Data Share-Einladung von *\<yourdataprovider\@domain.com>* “ lauten.
* Ein registrierter [Microsoft.DataShare-Ressourcenanbieter](concepts-roles-permissions.md#resource-provider-registration), vorhanden im:
    * Azure-Abonnement, in dem Sie eine Data Share-Ressource erstellen
    * Azure-Abonnement, in dem sich Ihre Azure-Zieldatenspeicher befinden

### <a name="prerequisites-for-a-target-storage-account"></a>Voraussetzungen für ein Zielspeicherkonto

* Azure Storage-Konto Sofern noch nicht geschehen, [erstellen Sie ein Konto](../storage/common/storage-account-create.md). 
* Berechtigung zum Schreiben im Speicherkonto. Diese Berechtigung ist in *Microsoft.Storage/storageAccounts/write* enthalten. Sie ist Bestandteil der Rolle „Mitwirkender“. 
* Berechtigung zum Lesen oder Hinzufügen einer Rollenzuweisung zum Speicherkonto. Diese Zuweisung ist in *Microsoft.Authorization/role assignments/write* enthalten. Sie ist Bestandteil der Rolle „Besitzer“.  

### <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

### <a name="open-an-invitation"></a>Öffnen einer Einladung

Sie können eine Einladung über die E-Mail oder direkt aus dem Azure-Portal öffnen.

1. Wenn Sie die Einladung aus der E-Mail öffnen möchten, überprüfen Sie Ihren Posteingang auf eine Einladung von Ihrem Datenanbieter. Die Einladung stammt von Microsoft Azure und hat die Bezeichnung „Azure Data Share-Einladung von *\<yourdataprovider\@domain.com>* “. Wählen Sie **Einladung anzeigen** aus, um Ihre Einladung in Azure anzuzeigen. 

   Wenn Sie eine Einladung aus dem Azure-Portal öffnen möchten, suchen Sie dort nach *Einladungen zu Data Share*. Es wird eine Liste der Data Share-Einladungen angezeigt.

   Wenn Sie ein/e Gastbenutzer*in von Mandanten sind, werden Sie aufgefordert, Ihre E-Mail-Adresse für die Mandanten zu überprüfen, bevor Sie die Data Share-Einladung zum ersten Mal anzeigen. Nach der Überprüfung ist sie 12 Monate lang gültig.

   ![Screenshot: Liste der Einladungen im Azure-Portal](./media/invitations.png "Liste mit Einladungen") 

1. Wählen Sie die Freigabe aus, die Sie anzeigen möchten. 

### <a name="accept-an-invitation"></a>Akzeptieren einer Einladung
1. Überprüfen Sie alle Felder, einschließlich der **Nutzungsbedingungen**. Wenn Sie den Bedingungen zustimmen, aktivieren Sie das Kontrollkästchen. 

   ![Screenshot: Bereich „Nutzungsbedingungen“](./media/terms-of-use.png "Nutzungsbedingungen") 

1. Wählen Sie unter **Data Share-Zielkonto** das Abonnement und die Ressourcengruppe aus, in der Sie Ihre Datenfreigabe bereitstellen möchten. Füllen Sie dann folgende Felder aus:

   * Wählen Sie im Feld **Data Share-Konto** die Option **Neu erstellen** aus, falls Sie noch kein Data Share-Konto besitzen. Wählen Sie andernfalls ein vorhandenes Data Share-Konto aus, das für Ihre Datenfreigabe geeignet ist. 

   * Im Feld **Name der empfangenen Freigabe** können Sie den vom Datenanbieter angegebenen Standardnamen übernehmen oder einen neuen Namen für die empfangene Freigabe angeben. 

1. Wählen Sie die Option **Akzeptieren und konfigurieren** aus. Das Freigabeabonnement wird erstellt. 

   ![Screenshot: Position zum Akzeptieren der Konfigurationsoptionen](./media/accept-options.png "Optionen zum Akzeptieren") 

    Die empfangene Freigabe wird in Ihrem Data Share-Konto angezeigt. 

    Wählen Sie die Einladung nicht annehmen möchten, klicken Sie auf **Ablehnen**. 

### <a name="configure-a-received-share"></a>Konfigurieren der empfangenen Freigabe
Befolgen Sie die Schritte in diesem Abschnitt, um einen Speicherort zum Empfang von Daten zu konfigurieren.

1. Aktivieren Sie auf der Registerkarte **Datasets** das Kontrollkästchen neben dem Dataset, das Sie als Ziel zuweisen möchten. Wählen Sie **Dem Ziel zuordnen**, um einen Zieldatenspeicher auszuwählen. 

   ![Screenshot: Zuweisen zu einem Ziel](./media/dataset-map-target.png "Zuordnung zum Ziel") 

1. Wählen Sie einen Zieldatenspeicher für die Daten aus. Datendateien im Zieldatenspeicher, die denselben Pfad und Namen aufweisen, werden überschrieben. 

   ![Screenshot: Position zum Auswählen eines Zielspeicherkontos](./media/map-target.png "Zielspeicher") 

1. Wenn der Datenanbieter bei einer Freigabe basierend auf Momentaufnahmen einen Momentaufnahmezeitplan verwendet, um die Daten regelmäßig zu aktualisieren, können Sie den Zeitplan auf der Registerkarte **Momentaufnahmezeitplan** aktivieren. Aktivieren Sie das Kontrollkästchen neben dem Momentaufnahmezeitplan. Wählen Sie anschließend **Aktivieren** aus. Beachten Sie, dass die erste geplante Momentaufnahme innerhalb einer Minute ab der geplanten Zeit beginnt und nachfolgende Momentaufnahmen innerhalb von Sekunden ab der geplanten Zeit gestartet werden.

   ![Screenshot: Aktivieren eines Momentaufnahmezeitplans](./media/enable-snapshot-schedule.png "Aktivieren des Momentaufnahmezeitplans")

### <a name="trigger-a-snapshot"></a>Auslösen einer Momentaufnahme
Die Schritte in diesem Abschnitt gelten nur für die Freigabe basierend auf Momentaufnahmen.

1. Sie können über die Registerkarte **Details** eine Momentaufnahme auslösen. Klicken Sie auf der Registerkarte auf **Momentaufnahme auslösen**. Sie können wahlweise eine vollständige oder eine inkrementelle Momentaufnahme Ihrer Daten erstellen. Falls Sie zum ersten Mal Daten von Ihrem Datenanbieter empfangen, wählen Sie **Vollständige Kopie** aus. Wenn eine Momentaufnahme ausgeführt wird, starten anschließende Momentaufnahmen nur, wenn die vorherige abgeschlossen ist.

   ![Screenshot: Auswahl zum Auslösen einer Momentaufnahme](./media/trigger-snapshot.png "Auslösen einer Momentaufnahme") 

1. Wenn der Status der letzten Ausführung *Erfolgreich* lautet, navigieren Sie zum Zieldatenspeicher, um die empfangenen Daten anzuzeigen. Wählen Sie **Datasets** und anschließend den Zielpfadlink aus. 

   ![Screenshot: Zuordnung eines Consumerdatasets](./media/consumer-datasets.png "Zuordnung des Consumerdatasets") 

### <a name="view-history"></a>Anzeigen des Verlaufs
Sie können den Verlauf Ihrer Momentaufnahmen nur für eine auf Momentaufnahmen basierende Freigabe anzeigen. Öffnen Sie zur Anzeige von Verlaufsdaten die Registerkarte **Verlauf**. Hier wird ein Verlauf aller Momentaufnahmen angezeigt, die in den letzten 30 Tagen generiert wurden. 

## <a name="storage-snapshot-performance"></a>Leistung von Storage-Momentaufnahmen
Die Leistung von Storage-Momentaufnahmen wird von mehren Faktoren beeinflusst. Zusätzlich spielen die Anzahl der Dateien und die Größe der freigegebenen Daten eine Rolle. Es wird immer empfohlen, eigene Leistungstests durchzuführen. Nachfolgend sind einige Beispielfaktoren aufgerührt, die sich auf die Leistung auswirken.

* Gleichzeitiger Zugriff auf Quell- und Zieldatenspeicher.  
* Standort der Quell- und Zieldatenspeicher. 
* Bei inkrementellen Momentaufnahmen kann die Anzahl der Dateien im freigegebenen Dataset beeinflussen, wie lange es dauert, die Liste der Dateien mit dem Zeitpunkt der letzten Bearbeitung nach der letzten erfolgreichen Momentaufnahme zu finden. 


## <a name="next-steps"></a>Nächste Schritte
Sie haben erfahren, wie Sie mithilfe des Azure Data Share-Diensts Daten aus einem Speicherkonto freigeben und empfangen. Informationen zum Freigeben von Daten aus anderen Datenquellen finden Sie unter [Unterstützte Datenspeicher](supported-data-stores.md).

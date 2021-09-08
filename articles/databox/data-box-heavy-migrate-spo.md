---
title: Verwenden von Azure Data Box Heavy zum Verschieben von Dateifreigabeinhalten zu SharePoint Online
description: In diesem Tutorial erfahren Sie, wie Sie mit der Azure Data Box Heavy Inhalte von Dateifreigaben zu SharePoint Online migrieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: 7d0270c08b592742f159ebc7e951dc88731e1773
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122343222"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Verwenden der Azure Data Box Heavy zum Migrieren von Dateifreigabeinhalten zu SharePoint Online

Verwenden Sie die Azure Data Box Heavy und das SharePoint-Migrationstool (SPMT), um Inhalte von Dateifreigaben ganz einfach zu SharePoint Online und OneDrive zu migrieren. Mit der Data Box Heavy sind Sie zum Übertragen von Daten nicht mehr auf eine WAN-Verbindung (Wide Area Network) angewiesen.

Die Microsoft Azure Data Box ist ein Dienst, mit dem Sie ein Gerät aus dem Microsoft Azure-Portal bestellen können. Anschließend können Sie Terabytes an Daten von Ihren Servern auf das Gerät kopieren. Nach dem Zurücksenden an Microsoft werden die Daten in Azure kopiert. Abhängig von der Größe der Daten, die Sie übertragen möchten, können Sie Folgendes wählen:

- [Data Box Disk](./data-box-disk-overview.md) mit einer nutzbaren Kapazität von 35 TB pro Bestellung für Datasets geringer bis mittlerer Größe.
- [Data Box](./data-box-overview.md) mit einer nutzbaren Kapazität von 80 TB pro Gerät für Datasets mittlerer Größe bis große Datasets.
- [Data Box Heavy](./data-box-heavy-overview.md) mit einer nutzbaren Kapazität von 770 TB pro Gerät für große Datasets.

In diesem Artikel wird erläutert, wie Data Box Heavy zum Migrieren von Dateifreigabeinhalten zu SharePoint Online verwendet wird.

## <a name="requirements-and-costs"></a>Anforderungen und Kosten

### <a name="for-data-box-heavy"></a>Für Data Box Heavy

- Die Data Box Heavy ist nur für Kunden mit Enterprise Agreement (EA)-, Cloud Solution Provider (CSP)- oder Azure Sponsorship-Angeboten verfügbar. Falls Ihr Abonnement zu keiner der oben genannten Kategorien gehört, wenden Sie sich an den Microsoft-Support, um ein Upgrade für Ihr Abonnement auszuführen, oder lesen Sie die Informationen zu den [Azure-Preisen](https://azure.microsoft.com/pricing/).
- Die Verwendung von Data Box Heavy ist kostenpflichtig. Lesen Sie die Informationen zu den [Preisen für Data Box Heavy](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Für SharePoint Online

- Lesen Sie die [Mindestanforderungen für das SharePoint-Migrationstool (SPMT)](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Übersicht über Workflow

Für diesen Workflow müssen Sie Schritte für Azure Data Box Heavy sowie in SharePoint Online ausführen.
Die folgenden Schritte beziehen sich auf die Azure Data Box Heavy.

1. Bestellen Sie die Azure Data Box Heavy.
2. Richten Sie das Gerät ein, nachdem Sie es erhalten haben.
3. Kopieren Sie Daten aus der lokalen Dateifreigabe in einen Ordner für Azure Files auf Ihrem Gerät.
4. Senden Sie das Gerät nach Abschluss des Kopiervorgangs gemäß den Anweisungen zurück.
5. Warten Sie, bis die Daten vollständig in Azure hochgeladen wurden.

Die folgenden Schritte beziehen sich auf SharePoint Online.

6. Erstellen Sie einen virtuellen Computer im Azure-Portal, und stellen Sie eine Azure-Dateifreigabe auf ihm bereit.
7. Installieren Sie das SharePoint-Migrationstool auf dem virtuellen Azure-Computer.
8. Führen Sie das SharePoint-Migrationstool aus, mit der Azure-Dateifreigabe als *Quelle*.
9. Führen Sie die letzten Schritte des Tools aus.
10. Überprüfen und bestätigen Sie die Daten.

## <a name="use-data-box-heavy-to-copy-data"></a>Kopieren von Daten mithilfe der Data Box Heavy

Führen Sie die folgenden Schritte aus, um Daten auf die Data Box-Heavy zu kopieren.

1. [Bestellen Sie die Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. [Richten Sie die Data Box Heavy](data-box-heavy-deploy-set-up.md) ein, nachdem Sie sie erhalten haben. Sie verkabeln und konfigurieren beide Knoten auf dem Gerät.
3. [Kopieren Sie Daten auf die Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Stellen Sie während des Kopiervorgangs Folgendes sicher:

    - Verwenden Sie zum Kopieren der Daten nur den Ordner *StorageAccountName_AzFile* in Data Box Heavy. Der Grund dafür ist, dass die Daten in einer Azure-Dateifreigabe und nicht in Block- oder Seitenblobs enthalten sein sollen.
    - Kopieren Sie Dateien in einen Ordner im Ordner *StorageAccountName_AzFile*. Durch einen Unterordner innerhalb des Ordners *StorageAccountName_AzFile* wird eine Dateifreigabe erstellt. Bei direkt in den Ordner *StorageAccountName_AzFile* kopierten Dateien tritt ein Fehler auf, und die Dateien werden als Blockblobs hochgeladen. Dies ist die Dateifreigabe, die Sie im nächsten Schritt auf dem virtuellen Computer bereitstellen.
    - Kopieren Sie die Daten auf beide Knoten in der Data Box Heavy.
3. Führen Sie die [Versandvorbereitung](data-box-heavy-deploy-picked-up.md#prepare-to-ship) für das Gerät durch. Eine erfolgreiche Vorbereitung für den Versand gewährleistet das erfolgreiche Hochladen der Dateien in Azure.
4. [Senden Sie das Gerät zurück](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Überprüfen Sie den Datenupload in Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Migrieren von Daten mithilfe des SharePoint-Migrationstools

Nachdem Sie vom Azure-Datenteam eine Bestätigung erhalten haben, dass das Kopieren der Daten abgeschlossen wurde, können Sie die Daten zu SharePoint Online migrieren.

Für optimale Leistung und Konnektivität empfehlen wir, einen virtuellen Azure-Computer (VM) zu erstellen.

1. Melden Sie sich beim Azure-Portal an, und [erstellen Sie einen virtuellen Computer](../virtual-machines/windows/quick-create-portal.md).
2. [Stellen Sie die Azure-Dateifreigabe auf dem virtuellen Computer bereit](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Laden Sie das SharePoint-Migrationstool herunter](https://spmtreleasescus.blob.core.windows.net/install/default.htm), und installieren Sie es auf dem virtuellen Azure-Computer.
4. Starten Sie die SharePoint-Migrationstool. Klicken Sie auf **Anmelden**, und geben Sie den Benutzernamen und das Kennwort für Ihr Geschäfts-, Schul- oder Unikonto ein.
5. Wenn **Wo sind Ihre Daten?** angezeigt wird, wählen Sie **Dateifreigabe** aus. Geben Sie den Pfad zu der Azure-Dateifreigabe ein, die Ihre Daten enthält.
6. Befolgen Sie die restlichen Eingabeaufforderungen, einschließlich der Angabe des Zielspeicherorts. Weitere Informationen finden Sie unter [Verwenden des SharePoint-Migrationstools](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Die Geschwindigkeit, mit der Daten in SharePoint Online erfasst werden, hängt von mehreren Faktoren ab, unabhängig davon, ob sich die Daten bereits in Azure befinden. Die Kenntnis dieser Faktoren hilft Ihnen bei der Planung der Migration und der Erhöhung ihrer Effizienz.  Weitere Informationen finden Sie unter [Best Practices zum Steigern der Migrationsleistung von SharePoint und OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Beim Migrieren der Daten zu SharePoint Online besteht die Gefahr, dass bestehende Berechtigungen für Dateien verloren gehen. Möglicherweise gehen auch bestimmte Metadaten, z.B. *Erstellt von* und *Änderungsdatum*, verloren.

## <a name="next-steps"></a>Nächste Schritte

[Bestellen der Data Box Heavy](./data-box-heavy-deploy-ordered.md)
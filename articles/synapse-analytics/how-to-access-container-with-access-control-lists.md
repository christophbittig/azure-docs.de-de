---
title: Durchsuchen eines ADLS Gen2-Ordners mit ACLs in Azure Synapse Analytics
description: Erfahren Sie, wie Sie einen ADLS Gen2-Ordner mit ACLs in Azure Synapse Analytics durchsuchen.
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 10/28/2021
ms.openlocfilehash: 2d16c9eae1e48c1471eb7c250bd9cf9e71d16d79
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894922"
---
# <a name="browse-adls-gen2-folders-preview-in-an-azure-synapse-analytics-workspace"></a>Durchsuchen von ADLS Gen2-Ordnern (Vorschau) in einem Azure Synapse Analytics-Arbeitsbereich
Sie können nun einen ADLS Gen2-Container oder -Ordner (Azure Data Lake Storage Gen2) in Ihrem Azure Synapse Analytics-Arbeitsbereich durchsuchen, indem Sie in Data Hub eine Verbindung mit dem Container oder einem bestimmten Ordner herstellen. Wenn Ihre Organisation den Benutzern nicht die Rolle „Mitwirkender an Storage-Blobdaten“ für das Speicherkonto gewährt, aber die Erstellung von POSIX-ähnlichen Zugriffssteuerungslisten (Access Control Lists, ACLs) für den Container oder bestimmte Ordner zulässt, können Sie die in diesem Artikel aufgeführten Schritte ausführen, um Dateien und Ordner in ADLS Gen2 zu durchsuchen.

>[!Note]
>Mit dieser Vorschaufunktion können Benutzer ADLS Gen2-Ordner durchsuchen, für die sie über ACLs verfügen. Es werden jedoch keine Aktionen zum Hochladen, Herunterladen, Erstellen, Bearbeiten, Löschen oder Umbenennen von Dateien oder Ordnern unterstützt. Benutzer mit der Rolle „Mitwirkender an Storage-Blobdaten“ können alle Aktionen für ADLS Gen2-Ordner ausführen.


## <a name="prerequisites"></a>Voraussetzungen
Die folgenden Voraussetzungen müssen erfüllt sein, bevor Sie eine Verbindung mit einem Container oder Ordner in Azure Synapse herstellen:
* Ihre Azure AD-Identität muss über die Rolle „Mitwirkender an Storage Blobdaten“ (Azure RBAC) oder Zugriffssteuerungslisten (ACLs) verfügen.
* Ein mit dem ADLS Gen2-Container verknüpfter Dienst muss im Synapse-Arbeitsbereich erstellt werden.


## <a name="connect-adls-gen2-folder-to-your-azure-synapse-analytics-workspace"></a>Verbinden eines ADLS Gen2-Ordners mit Ihrem Azure Synapse Analytics-Arbeitsbereich
1. Wechseln Sie zur Registerkarte **Verknüpft** von Data Hub.
2. Klicken Sie mit der rechten Maustaste auf **Azure Data Lake Storage Gen2**, und wählen Sie **Verbindung mit Azure Storage herstellen (Vorschau)** aus.
    * Geben Sie die URL des ADLS Gen2-Containers oder Ordners im Format `https://storageaccountname.dfs.core.windows.net/containername/foldername/` an.
    * Geben Sie einen eindeutigen Namen für die Verbindung an.
    * Geben Sie den Namen des Mandanten an, der das ADLS Gen2-Konto enthält. Wird das Feld leer gelassen, wird der dem Synapse-Arbeitsbereich zugeordnete Mandant verwendet.
    ![Herstellen einer Verbindung mit dem Storage-Ordner mit ACLs](./media/connect-to-azure-storage-with-access-control-lists/connect-to-azure-storage-with-acls.png)
3. Klicken Sie auf **Verbinden**. Der verbundene Container bzw. Ordner wird unter **Angefügte Container** angezeigt.


## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über ACLs in Azure Data Lake Storage Gen2.
- [Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md)
- [Verwenden des Azure-Portals zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-acl-azure-portal.md)

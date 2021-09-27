---
title: 'Storage-Explorer: Festlegen von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2'
description: Verwenden Sie Azure Storage-Explorer, um Zugriffssteuerungslisten (Access Control Lists, ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace (HNS) aktiviert ist.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: d97df947309f9ccc9886e45878cfc91ea6423b23
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590364"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von Azure Storage-Explorer zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel wird beschrieben, wie Sie mit [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) Zugriffssteuerungslisten (Access Control Lists, ACLs) in Speicherkonten verwalten, für die der hierarchische Namespace (HNS) aktiviert ist.

Sie können mit Storage-Explorer die Zugriffssteuerungslisten von Verzeichnissen und Dateien anzeigen und aktualisieren. Die Vererbung von Zugriffssteuerungslisten (ACLs) ist für neue untergeordnete Elemente, die unter einem übergeordneten Verzeichnis erstellt werden, bereits verfügbar. Sie können jedoch auch Einstellungen von Zugriffssteuerungslisten rekursiv auf die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses anwenden, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen.

In diesem Artikel erfahren Sie, wie Sie die Zugriffssteuerungslisten von Dateien oder Verzeichnissen ändern und die Einstellungen der Zugriffssteuerungslisten rekursiv auf untergeordnete Verzeichnisse anwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.

- Azure Storage-Explorer muss auf dem lokalen Computer installiert sein. Informationen zum Installieren von Azure Storage-Explorer für Windows, Macintosh oder Linux finden Sie unter [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Storage Explorer nutzt bei Verwendung von Azure Data Lake Storage Gen2 sowohl den Blob-[Endpunkt](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) (blob) als auch den Data Lake Storage Gen2-Endpunkt (dfs). Wenn der Zugriff auf Azure Data Lake Storage Gen2 über private Endpunkte konfiguriert wird, stellen Sie sicher, dass zwei private Endpunkte für das Speicherkonto erstellt werden: einer mit der Zielunterressource `blob` und der andere mit der Zielunterressource `dfs`.

## <a name="sign-in-to-storage-explorer"></a>Anmelden bei Storage-Explorer

Nach dem erstmaligen Starten von Storage-Explorer wird das Fenster **Microsoft Azure Storage-Explorer – Verbinden** angezeigt. Obwohl der Storage-Explorer mehrere Möglichkeiten zur Verbindung mit Speicherkonten bietet, wird derzeit nur eine Möglichkeit zur Verwaltung von ACLs unterstützt.

|Aufgabe|Zweck|
|---|---|
|Hinzufügen eines Azure-Kontos | Führt die Umleitung auf die Anmeldeseite Ihrer Organisation durch, um Sie für Azure zu authentifizieren. Dies ist derzeit die einzige unterstützte Authentifizierungsmethode, wenn Sie ACLs verwalten und festlegen möchten.|
|Verwenden einer Verbindungszeichenfolge oder eines Shared Access Signature-URI | Kann verwendet werden, um mit einem SAS-Token oder einer freigegebenen Verbindungszeichenfolge direkt auf einen Container oder ein Speicherkonto zuzugreifen. |
|Verwenden eines Speicherkontonamens und -schlüssels| Verwenden Sie den Speicherkontonamen und -schlüssel Ihres Speicherkontos, um eine Verbindung mit Azure Storage herzustellen.|

Wählen Sie **Azure-Konto hinzufügen**, und klicken Sie auf **Anmelden...** . Befolgen Sie die Anweisungen auf dem Bildschirm, um sich an Ihrem Azure-Konto anzumelden.

![Screenshot: Microsoft Azure Storage-Explorer mit hervorgehobener Option „Azure-Konto hinzufügen“ und der Schaltfläche „Anmelden“](media/quickstart-storage-explorer/storage-explorer-connect.png)

Nach Abschluss des Verbindungsvorgangs wird Azure Storage-Explorer geladen, und die Registerkarte **Explorer** wird angezeigt. So erhalten Sie einen Einblick in Ihre gesamten Azure Storage-Konten und in den lokalen Speicher, der über den [Azurite-Speicheremulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Konten oder [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Umgebungen konfiguriert wurde.

![Fenster „Microsoft Azure Storage-Explorer – Verbinden“](media/quickstart-storage-explorer/storage-explorer-main-page.png)

## <a name="manage-an-acl"></a>Verwalten einer Zugriffssteuerungsliste

Klicken Sie mit der rechten Maustaste auf einen Container, ein Verzeichnis oder eine Datei, und klicken Sie dann auf **Zugriffssteuerungslisten verwalten**.  Der folgende Screenshot zeigt das Menü, das angezeigt wird, wenn Sie mit der rechten Maustaste auf ein Verzeichnis klicken.

> [!div class="mx-imgBorder"]
> ![Klick mit der rechten Maustaste auf ein Verzeichnis in Azure Storage-Explorer](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

Das Dialogfeld **Zugriff verwalten** ermöglicht Ihnen, Berechtigungen für den Besitzer und die Besitzergruppe zu verwalten. Sie können auch neue Benutzer und Gruppen zur Zugriffssteuerungsliste hinzufügen, für die Sie dann die Berechtigungen verwalten können.

> [!div class="mx-imgBorder"]
> ![Dialogfeld „Zugriff verwalten“](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Um der Zugriffssteuerungsliste einen neuen Benutzer oder eine Gruppe hinzuzufügen, wählen Sie die Schaltfläche **Hinzufügen** aus. Geben Sie den zugehörigen Azure Active Directory-Eintrag (Azure AD) ein, den Sie der Liste hinzufügen möchten, und wählen Sie dann **Hinzufügen** aus.  Der Benutzer oder die Gruppe erscheint nun im Feld **Benutzer und Gruppen:** , sodass Sie mit der Verwaltung ihrer Berechtigungen beginnen können.

> [!NOTE]
> Es ist eine Best Practice, und es wird empfohlen, eine Sicherheitsgruppe in Azure AD zu erstellen und die Berechtigungen für die Gruppe und nicht für einzelne Benutzer zu verwalten. Einzelheiten zu dieser Empfehlung sowie zu anderen bewährten Methoden finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Verwenden Sie die Kontrollkästchen, um den Zugriff und Standard-ACLs festzulegen. Weitere Informationen zum Unterschied zwischen diesen Arten von ACLs finden Sie unter [Typen von ACLs](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>Rekursives Anwenden von Zugriffssteuerungslisten

Sie können Einträge aus Zugriffssteuerungslisten rekursiv auf die vorhandenen untergeordneten Elemente eines übergeordneten Verzeichnisses anwenden, ohne diese Änderungen für jedes untergeordnete Element einzeln vornehmen zu müssen.

Wenn Sie ACL-Einträge rekursiv anwenden möchten, klicken Sie mit der rechten Maustaste auf den Container oder ein Verzeichnis, und klicken Sie dann auf **Zugriffssteuerungslisten weitergeben**.  Der folgende Screenshot zeigt das Menü, das angezeigt wird, wenn Sie mit der rechten Maustaste auf ein Verzeichnis klicken.

> [!div class="mx-imgBorder"]
> ![Klicken mit der rechten Maustaste auf ein Verzeichnis und Auswählen der Einstellung „Zugriffssteuerungslisten weitergeben“](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Berechtigungsmodell von Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)

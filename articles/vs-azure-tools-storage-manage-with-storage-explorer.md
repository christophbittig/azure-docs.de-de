---
title: Erste Schritte mit dem Storage-Explorer | Microsoft-Dokumentation
description: Beginnen Sie mit der Verwaltung von Azure-Speicherressourcen mit Storage-Explorer. Hier erfahren Sie, wie Sie Azure Storage-Explorer herunterladen und installieren, eine Verbindung mit einem Speicherkonto oder einem Dienst herstellen und vieles mehr.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: f08d1c38965121564c21dee7053ae0c152b82814
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021978"
---
# <a name="get-started-with-storage-explorer"></a>Erste Schritte mit dem Storage-Explorer

## <a name="overview"></a>Übersicht

Microsoft Azure Storage-Explorer ist eine eigenständige App, mit der Sie unter Windows, macOS und Linux komfortabel mit Azure Storage-Daten arbeiten können.

In diesem Artikel werden mehrere Möglichkeiten zum Herstellen einer Verbindung mit und zum Verwalten Ihrer Azure-Speicherkonten beschrieben.

:::image type="content" alt-text="Microsoft Azure Storage-Explorer" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Voraussetzungen

# <a name="windows"></a>[Windows](#tab/windows)

Storage-Explorer wird von folgenden Windows-Versionen unterstützt:

* Windows 10 (empfohlen)
* Windows 8
* Windows 7

Für alle Windows-Versionen ist für Storage-Explorer .NET Framework 4.7.2 oder höher erforderlich.

# <a name="macos"></a>[macOS](#tab/macos)

Storage-Explorer wird von folgenden macOS-Versionen unterstützt:

* macOS 10.12 Sierra und höher

# <a name="linux"></a>[Linux](#tab/linux)

Storage-Explorer ist für die meisten gängigen Linux-Distributionen im [Snap Store](https://snapcraft.io/storage-explorer) verfügbar. Es wird empfohlen, Snap Store für diese Installation zu verwenden. Das Storage-Explorer-Snap installiert alle Abhängigkeiten und Updates, sobald neue Versionen im Snap Store veröffentlicht werden.

Informationen zu den unterstützten Distributionen finden Sie auf der [`snapd`-Installationsseite](https://snapcraft.io/docs/installing-snapd).

Für Storage-Explorer ist die Verwendung eines Kennwort-Managers erforderlich. Möglicherweise müssen Sie manuell eine Verbindung mit einem Kennwort-Manager herstellen. Sie können Storage-Explorer mit dem Kennwort-Manager Ihres Systems verbinden, indem Sie den folgenden Befehl ausführen:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage-Explorer ist auch als *.tar.gz*-Download verfügbar. Wenn Sie die Datei *.tar.gz* verwenden, müssen Sie die Abhängigkeiten manuell installieren. Die *.tar.gz*-Installation wird für die folgenden Linux-Distributionen unterstützt:

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

Die *.tar.gz*-Installation könnte auch bei anderen Distributionen funktionieren, wird aber nur für diese aufgeführten Distributionen offiziell unterstützt.

Weitere Hilfe bei der Installation von Storage-Explorer unter Linux finden Sie unter [Linux-Abhängigkeiten](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) im Leitfaden zur Problembehandlung für Azure Storage-Explorer.

---

## <a name="download-and-install"></a>Herunterladen und Installieren

Informationen zum Herunterladen und Installieren von Storage-Explorer finden Sie unter [Azure Storage-Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Herstellen der Verbindung mit einem Speicherkonto oder Dienst

Es gibt verschiedene Möglichkeiten, wie Sie den Storage-Explorer mit Azure-Ressourcen verbinden können:

* [Sich bei Azure anmelden, um auf Ihre Abonnements und deren Ressourcen zuzugreifen](#sign-in-to-azure)
* [An eine einzelne Azure Storage-Ressource anfügen](#attach-to-an-individual-resource)
* [An eine CosmosDB-Ressource anfügen](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

> [!NOTE]
> Um nach der Anmeldung Vollzugriff auf Ressourcen zu haben, benötigt Storage-Explorer Berechtigungen für sowohl die Verwaltung (Azure Resource Manager) als auch Datenebenen. Das bedeutet, dass Sie Azure Active Directory-Berechtigungen (Azure AD) benötigen, um auf Ihr Speicherkonto, die Container im Konto und die Daten in den Containern zuzugreifen. Wenn Sie nur über Berechtigungen auf der Datenebene verfügen, sollten Sie beim Anfügen an eine Ressource die Option **Mit Azure Active Directory (Azure AD) anmelden** auswählen. Weitere Informationen zu den spezifischen Berechtigungen, die Storage-Explorer benötigt, finden Sie unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. Wählen Sie in Storage-Explorer **Anzeigen** > **Kontoverwaltung** aus, oder klicken Sie auf die Schaltfläche **Konten verwalten**.

    :::image type="content" alt-text="Konten verwalten" source ="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-manage-accounts.png":::

1. Unter **KONTOVERWALTUNG** werden jetzt alle Azure-Konten angezeigt, bei denen Sie angemeldet sind. Wählen Sie zum Herstellen einer Verbindung mit einem anderen Konto **Konto hinzufügen** aus.

1. Das Dialogfeld **Verbindung mit Azure Storage herstellen** wird geöffnet. Wählen Sie im Panel **Ressource auswählen** die Option **Abonnement** aus.

    :::image type="content" alt-text="Dialogfeld zum Herstellen der Verbindung" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-dialog.png":::

1. Wählen Sie im Panel **Azure-Umgebung auswählen** eine Azure-Umgebung aus, bei der Sie sich anmelden möchten. Sie können sich bei Azure weltweit, bei einer nationalen Cloud oder einer Azure Stack-Instanz anmelden. Wählen Sie **Weiter** aus.

    :::image type="content" alt-text="Anmeldeoption" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Weitere Informationen zu Azure Stack finden Sie unter [Herstellen einer Verbindung von Storage-Explorer mit einem Azure Stack-Abonnement oder einem Speicherkonto](/azure-stack/user/azure-stack-storage-connect-se).

1. Storage-Explorer öffnet eine Webseite, auf der Sie sich anmelden können.

1. Nach erfolgreicher Anmeldung bei einem Azure-Konto werden unter **KONTOVERWALTUNG** das Konto und die mit diesem Konto verknüpften Azure Stack-Abonnements angezeigt. Wählen Sie die Azure-Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **Übernehmen**.

    :::image type="content" alt-text="Azure-Abonnements auswählen" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-account-panel.png":::

1. Im **EXPLORER** werden die Speicherkonten angezeigt, die mit den ausgewählten Azure-Abonnements verknüpft sind.

    :::image type="content" alt-text="Ausgewählte Azure-Abonnements" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Anfügen an eine einzelne Ressource

Mit Storage-Explorer können Sie eine Verbindung mit einzelnen Ressourcen, z. B. einem Azure Data Lake Storage Gen2-Container, unter Verwendung verschiedener Authentifizierungsmethoden herstellen. Einige Authentifizierungsmethoden werden nur für bestimmte Ressourcentypen unterstützt.

| Ressourcentyp    | Azure AD | Kontoname und -schlüssel | Shared Access Signature (SAS)  | Öffentlich (anonym) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Speicherkonten | Ja      | Ja                  | Ja (Verbindungszeichenfolge oder URL) | Nein                 |
| Blobcontainer  | Ja      | Nein                   | Ja (URL)                      | Ja                |
| Gen2-Container  | Ja      | Nein                   | Ja (URL)                      | Ja                |
| Gen2-Verzeichnisse | Ja      | Nein                   | Ja (URL)                      | Ja                |
| Dateifreigaben      | Nein       | Nein                   | Ja (URL)                      | Nein                 |
| Warteschlangen           | Ja      | Nein                   | Ja (URL)                      | Nein                 |
| Tabellen           | Nein       | Nein                   | Ja (URL)                      | Nein                 |

Storage Explorer kann auch eine Verbindung mit einem [lokalen Speicheremulator](#local-storage-emulator) über die konfigurierten Ports des Emulators herstellen.

Um eine Verbindung mit einer einzelnen Ressource herzustellen, wählen Sie die Schaltfläche **Verbinden** auf der linken Symbolleiste auf. Befolgen Sie dann die Anweisungen für den Ressourcentyp, mit dem Sie eine Verbindung herstellen möchten.

:::image type="content" alt-text="Option „Mit Azure Storage verbinden“" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-button.png":::

Wenn eine Verbindung mit einem Speicherkonto erfolgreich hinzugefügt wurde, wird ein neuer Strukturknoten unter **Lokal & angefügt** > **Speicherkonten** angezeigt.

Für andere Ressourcentypen wird ein neuer Knoten unter **Local & angefügt** > **Speicherkonten** >  **(Angefügte Container)** hinzugefügt. Der Knoten wird unter einem Gruppenknoten angezeigt, der seinem Typ entspricht. Beispielsweise wird eine neue Verbindung zu einem Azure Data Lake Storage Gen2-Container unter **Blobcontainer** angezeigt.

Wenn Storage-Explorer Ihre Verbindung nicht hinzufügen konnte oder Sie nach erfolgreichem Hinzufügen der Verbindung nicht auf Ihre Daten zugreifen können, lesen Sie die Informationen unter [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).

In den folgenden Abschnitten werden die verschiedenen Authentifizierungsmethoden beschrieben, die Sie für die Verbindung mit einzelnen Ressourcen verwenden können.

#### <a name="azure-ad"></a>Azure AD

Storage-Explorer kann Ihr Azure-Konto verwenden, um eine Verbindung mit den folgenden Ressourcentypen herzustellen:
* Blobcontainer
* Azure Data Lake Storage Gen2-Container
* Azure Data Lake Storage Gen2-Verzeichnisse
* Warteschlangen

Azure AD ist die bevorzugte Option, wenn Sie Zugriff auf Ihre Ressource auf der Datenebene, aber nicht auf der Verwaltungsebene haben.

1. Melden Sie sich mit den [oben beschriebenen Schritten](#sign-in-to-azure) bei mindestens einem Azure-Konto an.
1. Wählen Sie im Panel **Ressource auswählen** des Dialogfelds **Mit Azure Storage verbinden** die Optionen **Blobcontainer**, **ADLS Gen2-Container** oder **Warteschlange** aus.
1. Wählen Sie **Über Azure Active Directory anmelden (Azure AD)** und dann **Weiter** aus.
1. Wählen Sie ein Azure-Konto und einen Mandanten aus. Das Konto und der Mandant müssen Zugriff auf die Speicherressource haben, die Sie anfügen möchten. Wählen Sie **Weiter** aus.
1. Geben Sie einen Anzeigenamen für Ihre Verbindung und die URL der Ressource ein. Wählen Sie **Weiter** aus.
1. Überprüfen Sie Ihre Verbindungsinformationen im Panel **Zusammenfassung**. Wenn die Verbindungsinformationen richtig sind, wählen Sie **Verbinden** aus.

#### <a name="account-name-and-key"></a>Kontoname und -schlüssel

Storage-Explorer kann eine Verbindung mit einem Speicherkonto über den Namen und Schlüssel des Speicherkontos herstellen.

Sie finden Ihre Kontoschlüssel im [Azure-Portal](https://portal.azure.com). Öffnen Sie Ihre Speicherkontoseite, und wählen Sie **Einstellungen** > **Zugriffsschlüssel** aus.

1. Wählen Sie im Panel **Ressource wählen** des Dialogfelds **Mit Azure Storage verbinden** die Option **Speicherkonto** aus.
1. Wählen Sie **Kontoname und -schlüssel** und dann **Weiter** aus.
1. Geben Sie einen Anzeigenamen für Ihre Verbindung, den Namen des Kontos und einen der Kontoschlüssel ein. Wählen Sie die entsprechende Azure-Umgebung aus. Wählen Sie **Weiter** aus.
1. Überprüfen Sie Ihre Verbindungsinformationen im Panel **Zusammenfassung**. Wenn die Verbindungsinformationen richtig sind, wählen Sie **Verbinden** aus.

#### <a name="shared-access-signature-sas-connection-string"></a>SAS-Verbindungszeichenfolge (Shared Access Signature)

Storage-Explorer kann eine Verbindung mit einem Speicherkonto über eine Verbindungszeichenfolge mit einer Shared Access Signature (SAS) herstellen. Eine SAS-Verbindungszeichenfolge sieht wie folgt aus:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. Wählen Sie im Panel **Ressource wählen** des Dialogfelds **Mit Azure Storage verbinden** die Option **Speicherkonto** aus.
1. Wählen Sie **Shared Access Signature (SAS)** und dann **Weiter** aus.
1. Geben Sie einen Anzeigenamen für Ihre Verbindung und die SAS-Verbindungszeichenfolge für das Speicherkonto ein. Wählen Sie **Weiter** aus.
1. Überprüfen Sie Ihre Verbindungsinformationen im Panel **Zusammenfassung**. Wenn die Verbindungsinformationen richtig sind, wählen Sie **Verbinden** aus.

#### <a name="shared-access-signature-sas-url"></a>SAS-URL (Shared Access Signature)

Storage-Explorer kann über einen SAS-URI eine Verbindung mit den folgenden Ressourcentypen herstellen:
* BLOB-Container
* Azure Data Lake Storage Gen2-Container oder -Verzeichnis
* Dateifreigabe
* Warteschlange
* Tabelle

Ein SAS-URI sieht wie folgt aus:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. Wählen Sie im Panel **Ressource auswählen** des Dialogfelds **Mit Azure Storage verbinden** die Ressource aus, mit der Sie eine Verbindung herstellen möchten.
1. Wählen Sie **Shared Access Signature (SAS)** und dann **Weiter** aus.
1. Geben Sie einen Anzeigenamen für Ihre Verbindung und den SAS-URI für die Ressource ein. Wählen Sie **Weiter** aus.
1. Überprüfen Sie Ihre Verbindungsinformationen im Panel **Zusammenfassung**. Wenn die Verbindungsinformationen richtig sind, wählen Sie **Verbinden** aus.

#### <a name="local-storage-emulator"></a>Lokaler Speicheremulator

Storage-Explorer kann eine Verbindung mit einem Azure Storage-Emulator herstellen. Derzeit gibt es zwei unterstützte Emulatoren:

* [Azure Storage-Emulator](storage/common/storage-use-emulator.md) (nur Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS oder Linux)

Wenn Ihr Emulator an den Standardports lauscht wird, können Sie den Knoten **Lokal & angefügt** > **Speicherkonten** > **Emulator – Standardports** verwenden, um auf Ihren Emulator zuzugreifen.

Wenn Sie einen anderen Namen für Ihre Verbindung verwenden möchten oder Ihr Emulator nicht auf den Standardports ausgeführt wird:

1. Starten Sie Ihren Emulator.

   > [!IMPORTANT]
   > Storage-Explorer startet Ihren Emulator nicht automatisch. Sie müssen ihn manuell starten.

1. Wählen Sie im Panel **Ressource auswählen** des Dialogfelds **Mit Azure Storage verbinden** die Option **Lokaler Speicheremulator** aus.
1. Geben Sie einen Anzeigenamen für Ihre Verbindung und die Portnummer für jeden emulierten Dienst ein, den Sie verwenden möchten. Wenn Sie einen Dienst nicht verwenden möchten, lassen Sie den entsprechenden Port leer. Wählen Sie **Weiter** aus.
1. Überprüfen Sie Ihre Verbindungsinformationen im Panel **Zusammenfassung**. Wenn die Verbindungsinformationen richtig sind, wählen Sie **Verbinden** aus.

### <a name="connect-to-azure-cosmos-db"></a>Herstellen der Verbindung mit Azure Cosmos DB

Storage-Explorer unterstützt auch die Verbindung mit Azure Cosmos DB-Ressourcen.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto mithilfe einer Verbindungszeichenfolge

Anstatt Azure Cosmos DB-Konten über ein Azure-Abonnement zu verwalten, können Sie sich auch über eine Verbindungszeichenfolge mit Azure Cosmos DB verbinden. Führen Sie die folgenden Schritte aus, um eine Verbindung herzustellen:

1. Erweitern Sie im **EXPLORER** **Lokal und angefügt**. Klicken Sie mit der rechten Maustaste auf **Cosmos DB-Konten**, und wählen Sie **Connect to Azure Cosmos DB** (Mit Azure Cosmos DB verbinden) aus.

    ![Herstellen einer Verbindung mit Azure Cosmos DB mithilfe einer Verbindungszeichenfolge][21]

1. Wählen Sie die Azure Cosmos DB-API aus, fügen Sie Ihre **Verbindungszeichenfolge** ein, und klicken Sie dann auf **OK**, um eine Verbindung mit dem Azure Cosmos DB-Konto herzustellen. Informationen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Verwalten eines Azure Cosmos-Kontos](./cosmos-db/how-to-manage-database-account.md).

    ![Verbindungszeichenfolge][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Herstellen einer Verbindung mit Azure Data Lake Store über den URI

Sie können auf Ressourcen zugreifen, die nicht in Ihrem Abonnement enthalten sind. Sie benötigen eine Person mit Zugriff auf die relevante Ressource, die Ihnen den Ressourcen-URI bereitstellt. Stellen Sich nach der Anmeldung über den URI eine Verbindung mit Data Lake Store her. Führen Sie die folgenden Schritte aus, um eine Verbindung herzustellen:

1. Erweitern Sie im **EXPLORER** den Eintrag **Lokal & angefügt**.

1. Klicken Sie mit der rechten Maustaste auf **Data Lake Storage Gen1**, und wählen Sie **Connect to Data Lake Storage Gen1** (Mit Data Lake Storage Gen1 verbinden) aus.

    ![Kontextmenü „Mit Data Lake Store verbinden“](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Geben Sie den URI ein, und wählen Sie dann **OK** aus. Ihr Data Lake Store wird unter **Data Lake Storage** angezeigt.

    ![Mit Data Lake Store verbinden: Ergebnis](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

In diesem Beispiel wird Data Lake Storage Gen1 verwendet. Azure Data Lake Storage Gen2 ist jetzt verfügbar. Weitere Informationen finden Sie in der [Was ist Azure Data Lake Storage Gen1?](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generieren einer Shared Access Signature-Definition in Storage-Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Shared Access Signature auf Kontoebene

1. Klicken Sie mit der rechten Maustaste auf das Speicherkonto, das Sie freigeben möchten, und wählen Sie **Shared Access Signature abrufen** aus.

    ![Kontextmenüoption „Shared Access Signature abrufen“][14]

1. Geben Sie unter **Shared Access Signature** den Zeitrahmen und die gewünschten Berechtigungen für das Konto an, und wählen Sie **Erstellen** aus.

    ![Abrufen einer Shared Access Signature][15]

1. Kopieren Sie entweder die **Verbindungszeichenfolge** oder die unformatierte **Abfragezeichenfolge** in die Zwischenablage.

### <a name="service-level-shared-access-signature"></a>Shared Access Signature auf Dienstebene

Sie können eine Shared Access Signature auf Dienstebene abrufen. Weitere Informationen finden Sie unter [Abrufen der SAS für einen Blobcontainer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Suchen nach Speicherkonten

Sie können im Bereich **EXPLORER** nach Speicherressourcen suchen.

Sobald Sie Text in das Suchfeld eingeben, werden im Storage-Explorer alle Ressourcen angezeigt, die dem bislang eingegebenen Suchwert entsprechen. Dieses Beispiel zeigt eine Suche nach **Endpunkten**:

![Speicherkontosuche][23]

> [!NOTE]
> Deaktivieren Sie zum Beschleunigen Ihrer Suche im Bereich **Kontoverwaltung** alle Abonnements, die das gesuchte Element nicht enthalten. Sie können auch mit der rechten Maustaste auf einen Knoten klicken, und **Ab hier suchen** wählen, um die Suche ab einem bestimmten Knoten zu starten.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Azure Blob Storage-Ressourcen mit Storage-Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Arbeiten mit Daten unter Verwendung von Azure Storage-Explorer](./cosmos-db/storage-explorer.md)
* [Verwalten von Azure Data Lake Store-Ressourcen mit Storage-Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png

---
title: Generieren Sie ein SAS-Token (Shared Access Signature) für Container und Blobs mit dem Azure-Portal.
description: Wie man ein Shared Access Token (SAS) für Container und Blobs im Azure-Portal generiert
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/16/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 22fe82425285a27d224ea5375bfe209e7ac779da
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700433"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>Generieren von SAS-Tokens für Speichercontainer

 In diesem Artikel lernen Sie, wie Sie Token für die Benutzerdelegation mit gemeinsamer Zugriffssignatur (SAS) generieren können. Ein SAS-Token für die Benutzerdelegation wird mit Azure Active Directory (Azure AD)-Anmeldeinformationen anstelle von Azure-Speicherschlüsseln signiert. Es bietet einen besonders sicheren und delegierten Zugriff auf Ressourcen in Ihrem Azure-Storage-Konto.
Im Großen und Ganzen funktioniert es folgendermaßen: Ihre Anwendung stellt das SAS-Token als Teil einer Anforderung an Azure Storage bereit. Wenn der Speicherdienst prüft, ob das SAS gültig ist, wird die Anforderung autorisiert. Wird das SAS als ungültig erachtet, wird die Anforderung mit dem Fehlercode 403 (Verboten) abgelehnt.

Azure Blob Storage bietet drei Arten von Ressourcen:

* **Speicher** konten bieten einen eindeutigen Namespace in Azure für Ihre Daten.
* **Container** befinden sich in Speicherkonten und organisieren Mengen von Blobs.
* **Blobs** befinden sich in Containern und speichern Text- und Binärdaten.

> [!NOTE]
>
> * Wenn Ihr Azure Speicherkonto durch ein Virtual Network (VNET) oder eine Firewall geschützt ist, können Sie keinen Zugriff mithilfe eines SAS-Tokens gewähren. Sie müssen eine [**verwaltete Identität**](managed-identity-byos.md) verwenden, um Zugriff auf Ihre Speicherressource zu gewähren.
>
> * [**Verwaltete Identität**](managed-identity-byos.md) unterstützt sowohl privat als auch öffentlich zugängliche Azure Blob Speicherkonten.
>

## <a name="when-to-use-a-shared-access-signature"></a>Wann sollte eine Shared Access Signature verwendet werden?

* Wenn Sie Speichercontainer mit öffentlichem Zugriff verwenden, können Sie sich für die Verwendung eines SAS-Tokens entscheiden, um begrenzten Zugriff auf Ihre Speicherressourcen zu gewähren.

* Wenn Sie ein benutzerdefiniertes Modell trainieren, **muss** Ihr zusammengestellter Satz von Trainingsdokumenten in einen Azure Blob Storage Container hochgeladen werden. Sie können die Berechtigung für Ihre Trainingsressourcen mit einem SAS-Token für die Benutzerdelegation erteilen.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/).  Falls Sie noch kein Konto haben, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).
* Eine [**Formularerkennung**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [**Cognitive Services Multi-Service**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Ressource.
* **Standardleistung**[ **Azure Blob Storage Konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie erstellen Container zum Speichern und Organisieren Ihrer Blobdaten unter Ihrem Speicherkonto. Wenn Sie nicht wissen, wie Sie ein Azure Speicherkonto mit einem Container erstellen können, befolgen Sie diese Schnellstarts:

  * [**Erstellen Sie ein Speicherkonto**](/azure/storage/common/storage-account-create). Stellen Sie bei der Erstellung Ihres Speicherkontos sicher, dass Sie Leistung **Standard** im Feld **Instanzdetails -> Leistung** auswählen.
  * [**Erstellen Sie einen Container**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container). Stellen Sie bei der Erstellung Ihres Containers das Feld **Öffentliche Zugriffsebene** auf **Container**(anonymer Lesezugriff für Container und Blobs) im dem Fenster **Neuer Container**.

## <a name="upload-your-documents"></a>Hochladen Ihrer Dokumente

1. Gehen Sie zum [Azure-Portal](https://ms.portal.azure.com/#home) und navigieren Sie wie folgt: **Ihr Speicherkonto** -> **Datenspeicher** -> **Container**

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Screenshot: Menü Datenspeicher im Azure-Portal.":::

1. Wählen Sie einen **Container** aus der Liste aus.
1. Wählen Sie **Hochladen** aus dem Menü am oberen Rand der Seite.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Screenshot: Schaltfläche zum Hochladen von Containern im Azure-Portal.":::

1. Das Fenster **Blob hochladen** wird angezeigt.
1. Wählen Sie Ihre Datei(en) zum Hochladen aus.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Screenshot: Fenster Blob hochladen im Azure-Portal.":::

> [!NOTE]
> Standardmäßig verwendet die REST-API Formulardokumente, die sich im Stammverzeichnis Ihres Containers befinden. Sie können jedoch in Unterordnern organisierte Daten verwenden, wenn dies im API-Aufruf angegeben ist. *Siehe* [**Organisieren Sie Ihre Daten in Unterordnern**](/azure/applied-ai-services/form-recognizer/build-training-data-set.md#organize-your-data-in-subfolders-optional)

## <a name="create-a-sas-with-the-azure-portal"></a>Erstellen Sie ein SAS mit dem Azure-Portal

> [!IMPORTANT]
>
> Erstellen Sie und rufen Sie die SAS für Ihren Container ab, nicht für das Speicherkonto selbst.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/#home) wie folgt:

     **Ihr Speicherkonto** -> **Container**
1. Wählen Sie einen Container aus der Liste aus.
1. Navigieren Sie zur rechten Seite des Hauptfensters und wählen Sie die drei Ellipsen aus, die dem von Ihnen gewählten Container zugeordnet sind.
1. Wählen Sie **SAS erstellen** aus dem Dropdown-Menü, um das **SAS Fenster erstellen** zu öffnen.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="Screenshot (Azure-Portal): Dropdown-Menü SAS-Token erstellen.":::

1. Wählen Sie **Signaturmethode** > **Benutzerdelegierungsschlüssel** aus.

1. Definieren Sie **Berechtigungen** durch Aktivieren oder Deaktivieren des entsprechenden Kontrollkästchens. Stellen Sie sicher, dass die Berechtigungen **Lesen**, **Schreiben**, **Löschen** und **Liste** ausgewählt sind.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Screenshot (Azure-Portal): SAS-Berechtigungsfelder.":::

    >[!IMPORTANT]
    >
    > * Wenn Sie eine Meldung erhalten, die der folgenden ähnelt, müssen Sie den Zugriff auf die Blobdaten in Ihrem Speicherkonto zuweisen:
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="Screenshot: Warnung vor fehlenden Berechtigungen.":::
    >
     > * [**Azure rollenbasierte Zugriffskontrolle** ](/azure/role-based-access-control/overview) (Azure RBAC) ist das Autorisierungssystem, mit dem der Zugriff auf Azure-Ressourcen verwaltet wird. Azure RBAC hilft Ihnen, den Zugriff und die Berechtigungen für Ihre Azure-Ressourcen zu verwalten.
    > * Folgen Sie unserer Führungslinie [**Zuweisen einer Azure-Rolle für den Zugriff auf Blob Daten**](/azure/role-based-access-control/role-assignments-portal?tabs=current), um eine Rolle zuzuweisen, die Lese-, Schreib- und Löschberechtigungen für Ihren Azure-Speichercontainer ermöglicht, z. B. [**Speicher Blob-Daten Mitwirkender**](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor).

1. Geben Sie die Zeiten für den **Start** und **Ablauf** des signierten Schlüssels an. **Der Wert für die Ablaufzeit beträgt maximal sieben Tage ab dem Start des SAS**.

1. Das Feld **Zugelassene IP-Adressen** ist optional. Darin wird eine IP-Adresse oder ein Bereich mit IP-Adressen angegeben, für die Anforderungen akzeptiert werden. Wenn die IP-Adresse der Anforderung nicht mit der IP-Adresse oder dem Adressbereich übereinstimmt, die bzw. der im SAS-Token angegeben ist, wird sie nicht autorisiert.

1. Das Feld **Zugelassene Protokolle** ist optional. Darin wird das Protokoll angegeben, das für das Senden einer Anforderung mit der SAS zulässig ist. Der Standardwert ist „HTTPS“.

1. Überprüfen Sie die Angaben, und wählen Sie dann die Option **SAS-Token und -URL generieren** aus.

1. Die Abfragezeichenfolge für das **Blob-SAS-Token** und die **Blob-SAS-URL** werden unten im Fenster angezeigt. Fügen Sie einen Speicherdienst-URI an, um das **Blob SAS Token** zu verwenden.

1. **Kopieren Sie die Werte für das SAS-Token und die URL des Blobs, und fügen Sie sie an einem sicheren Ort ein. Diese Angaben werden nur einmal angezeigt und können nicht mehr abgerufen werden, nachdem das Fenster geschlossen wurde.**

## <a name="create-a-sas-with-azure-command-line-interface-cli"></a>Erstellen eines SAS mit der Azure-Befehlszeilenschnittstelle (CLI)

1. Um eine Benutzerdelegation SAS für einen Container mit der Azure-Befehlszeilenschnittstelle (CLI) zu erstellen, stellen Sie sicher, dass Sie Version 2.0.78 oder höher installiert haben. Verwenden Sie den Befehl `az --version`, um Ihre installierte Version zu überprüfen.

1. Rufen Sie den Befehl [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas) auf.

1. Die folgenden Parameter sind erforderlich:

    * `auth-mode login`. Dieser Parameter stellt sicher, dass Anforderungen an den Azure Speicher mit Ihren Azure AD-Anmeldeinformationen autorisiert werden.
    * `as-user`.  Dieser Parameter zeigt an, dass das generierte SAS ein Benutzerdelegierungs-SAS ist.

1. Unterstützte Berechtigungen für eine Benutzerdelegierungs-SAS auf einem Container sind Hinzufügen (a), Erstellen (c), Löschen (d), Liste (I), Lesen (r) und Schreiben (w).  Stellen Sie sicher, dass **r**, **w**, **d** und **l** als Teil der Berechtigungsparameter enthalten sind.

1. Wenn Sie mit der Azure CLI eine Benutzerdelegierungs-SAS erstellen, ist die Benutzerdelegierungs-SAS maximal sieben Tage ab dem Startdatum gültig. Daher sollten Sie eine Ablaufzeit für die SAS angeben, die innerhalb von sieben Tagen ab dem Startdatum liegt. *Siehe* [**Erstellen einer Benutzerdelegierungs-SAS für einen Container oder Blob mit der Azure CLI**](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli#use-azure-ad-credentials-to-secure-a-sas)

### <a name="example"></a>Beispiel

Erstellen einer Benutzerdelegierungs-SAS.  Ersetzen Sie die Platzhalterwerte in den Klammern durch Ihre eigenen Werte:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="how-to-use-your-blob-sas-url"></a>Verwenden Ihrer Blob SAS URL

* Um Ihre Blob SAS URL mit der [**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) zu verwenden, fügen Sie die SAS URL zum Anforderungstext hinzu:

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* Um Ihre **Blob SAS URL** mit dem [**Beschriftungstool Formularerkennung**](https://fott-2-1.azurewebsites.net/connections/create) zu verwenden, fügen Sie die SAS URL zum Feld **Verbindungseinstellungen**-> **Azure Blob Container** -> **SAS URI**  hinzu:

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="{alt-text}":::

Das ist alles. Sie haben gelernt, wie man SAS-Tokens erstellt, um den Zugriff von Clients auf Ihre Daten zu autorisieren.

> [!div class="nextstepaction"]
> [Erstellen eines Trainingsdatasets](build-training-data-set.md)

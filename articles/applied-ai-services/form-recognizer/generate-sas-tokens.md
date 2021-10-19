---
title: Generieren von SAS-Token für Container und Blobs über das Azure-Portal
description: Erfahren Sie, wie Sie SAS-Token (Shared Access Signature) für Container und Blobs im Azure-Portal generieren.
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: bed6b3e8d40beab403b8295b9c17fc8b6f6eefbe
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659258"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>Generieren von SAS-Tokens für Speichercontainer

In diesem Artikel lernen Sie, wie Sie Token für die Benutzerdelegation mit gemeinsamer Zugriffssignatur (SAS) für Azure Blob Storage-Container generieren können. Ein SAS-Token für die Benutzerdelegation wird mit Azure Active Directory (Azure AD)-Anmeldeinformationen anstelle von Azure-Speicherschlüsseln signiert. Es bietet einen besonders sicheren und delegierten Zugriff auf Ressourcen in Ihrem Azure-Storage-Konto.

Im Großen und Ganzen funktioniert es folgendermaßen: Ihre Anwendung stellt das SAS-Token als Teil einer Anforderung an Azure Storage bereit. Wenn der Speicherdienst prüft, ob die Shared Access Signature gültig ist, wird die Anforderung autorisiert. Wenn die Shared Access Signature als ungültig angesehen wird, erfolgt eine Ablehnung der Anforderung mit dem Fehlercode 403 (Unzulässig).

Azure Blob Storage bietet drei Arten von Ressourcen:

* **Speicher** konten bieten einen eindeutigen Namespace in Azure für Ihre Daten.
* **Container** befinden sich in Speicherkonten und organisieren Mengen von Blobs.
* **Blobs** befinden sich in Containern und speichern Text- und Binärdaten.

> [!NOTE]
>
> * Wenn Ihr Azure-Speicherkonto durch ein virtuelles Netzwerk oder eine Firewall geschützt ist, können Sie keinen Zugriff mithilfe eines SAS-Tokens gewähren. Sie müssen eine [verwaltete Identität](managed-identity-byos.md) verwenden, um Zugriff auf Ihre Speicherressource zu gewähren.
> * [Verwaltete Identität](managed-identity-byos.md) unterstützt sowohl privat als auch öffentlich zugängliche Azure Blob Storage-Konten.
>

## <a name="when-to-use-a-shared-access-signature"></a>Wann sollte eine Shared Access Signature verwendet werden?

* Wenn Sie Speichercontainer mit öffentlichem Zugriff verwenden, können Sie sich für die Verwendung eines SAS-Tokens entscheiden, um begrenzten Zugriff auf Ihre Speicherressourcen zu gewähren.
* Wenn Sie ein benutzerdefiniertes Modell trainieren, *muss* Ihr zusammengestellter Satz von Trainingsdokumenten in einen Azure Blob Storage-Container hochgeladen werden. Sie können die Berechtigung für Ihre Trainingsressourcen mit einem SAS-Token für die Benutzerdelegation erteilen.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein aktives [Azure-Konto](https://azure.microsoft.com/free/cognitive-services/). Wenn Sie noch nicht über eines verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
* Eine [Formularerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [Cognitive Services Multi-Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Ressource.
* Ein [Azure Blob Storage-Konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) mit **Standardleistung**. Sie erstellen Container zum Speichern und Organisieren Ihrer Blobdaten unter Ihrem Speicherkonto. Wenn Sie nicht wissen, wie Sie ein Azure Speicherkonto mit einem Container erstellen können, befolgen Sie diese Schnellstarts:

  * [Informationen zu Azure-Speicherkonten](/azure/storage/common/storage-account-create) Wenn Sie Ihr Speicherkonto erstellen, wählen Sie **Standardleistung** im Feld **Instanzendetails** > **Leistung** aus.
  * [Erstellen Sie einen Container](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container). Legen Sie bei der Erstellung Ihres Containers das Feld **Öffentliche Zugriffsebene** im Fenster **Neuer Container** auf **Container** fest (anonymer Lesezugriff für Container und Blobs).

## <a name="upload-your-documents"></a>Hochladen Ihrer Dokumente

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/#home). Wählen Sie **Ihr Speicherkonto** > **Datenspeicher** > **Container** aus.

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Screenshot: Menü „Datenspeicher“ im Azure-Portal":::

1. Wählen Sie einen Container aus der Liste aus.
1. Wählen Sie **Hochladen** aus dem Menü am oberen Rand der Seite.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Screenshot: Schaltfläche „Hochladen“ für Container im Azure-Portal":::

   Das Fenster **Blob hochladen** wird angezeigt.
1. Wählen Sie Ihre Dateien zum Hochladen aus.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Screenshot: Fenster „Blob hochladen“ im Azure-Portal":::

> [!NOTE]
> Standardmäßig verwendet die REST-API Formulardokumente, die sich im Stammverzeichnis Ihres Containers befinden. Sie können auch in Unterordnern organisierte Daten verwenden, wenn dies im API-Aufruf angegeben ist. Weitere Informationen finden Sie unter [Organisieren Ihrer Daten in Unterordnern](/azure/applied-ai-services/form-recognizer/build-training-data-set#organize-your-data-in-subfolders-optional).

## <a name="create-a-shared-access-signature-with-the-azure-portal"></a>Erstellen einer Shared Access Signature über das Azure-Portal

> [!IMPORTANT]
>
> Generieren Sie die Shared Access Signature und rufen Sie sie für Ihren Container ab, nicht für das Speicherkonto selbst.

1. Wählen Sie im [Azure-Portal](https://ms.portal.azure.com/#home) die Option **Ihr Speicherkonto** > **Container** aus.
1. Wählen Sie einen Container aus der Liste aus.
1. Wechseln Sie zur rechten Seite des Hauptfensters und wählen Sie die drei Auslassungspunkte aus, die dem von Ihnen gewählten Container zugeordnet sind.
1. Wählen Sie **SAS generieren** aus dem Dropdownmenü aus, um das Fenster **SAS generieren** zu öffnen.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="Screenshot: Dropdownmenü „SAS generieren“ im Azure-Portal":::

1. Wählen Sie **Signaturmethode** > **Benutzerdelegierungsschlüssel** aus.

1. Definieren Sie **Berechtigungen** durch Aktivieren oder Deaktivieren des entsprechenden Kontrollkästchens. Stellen Sie sicher, dass die Berechtigungen **Lesen**, **Schreiben**, **Löschen** und **Liste** ausgewählt sind.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Screenshot: SAS-Berechtigungsfelder im Azure-Portal":::

    >[!IMPORTANT]
    >
    > * Wenn Sie eine Meldung erhalten, die der folgenden ähnelt, müssen Sie den Zugriff auf die Blobdaten in Ihrem Speicherkonto zuweisen:
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="Screenshot: Warnung zu fehlenden Berechtigungen":::
    >
     > * [Azure rollenbasierte Zugriffskontrolle](/azure/role-based-access-control/overview)  (Azure RBAC) ist das Autorisierungssystem, mit dem der Zugriff auf Azure-Ressourcen verwaltet wird. Azure RBAC hilft Ihnen, den Zugriff und die Berechtigungen für Ihre Azure-Ressourcen zu verwalten.
    > * [Zuweisen einer Azure-Rolle für den Zugriff auf Blobdaten](/azure/role-based-access-control/role-assignments-portal?tabs=current) zeigt Ihnen, wie Sie eine Rolle zuweisen, die Lese-, Schreib- und Löschberechtigungen für Ihren Azure-Speichercontainer ermöglicht. Beispiel: [Mitwirkender an Speicherblobdaten](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor).

1. Geben Sie die Zeiten für den **Start** und **Ablauf** des signierten Schlüssels an. Der Wert für die Ablaufzeit beträgt maximal sieben Tage ab dem Beginn der Shared Access Signature.

1. Das Feld **Zugelassene IP-Adressen** ist optional. Darin wird eine IP-Adresse oder ein Bereich mit IP-Adressen angegeben, für die Anforderungen akzeptiert werden. Wenn die IP-Adresse der Anforderung nicht mit der IP-Adresse oder dem Adressbereich übereinstimmt, die bzw. der im SAS-Token angegeben ist, wird sie nicht autorisiert.

1. Das Feld **Zugelassene Protokolle** ist optional. Darin wird das Protokoll angegeben, das für das Senden einer Anforderung mit der Shared Access Signature zulässig ist. Der Standardwert ist „HTTPS“.

1. Wählen Sie **SAS-Token und -URL generieren** aus.

1. Die Abfragezeichenfolge für das **Blob-SAS-Token** und die **Blob-SAS-URL** werden im unteren Bereich des Fensters angezeigt. Um das Blob-SAS-Token zu verwenden, fügen Sie es an einen Speicherdienst-URI an.

1. Kopieren Sie die Werte für das **Blob-SAS-Token** und die **Blob-SAS-URL**, und fügen Sie sie an einem sicheren Ort ein. Sie werden nur einmal angezeigt und können nicht abgerufen werden, nachdem das Fenster geschlossen wurde.

## <a name="create-a-shared-access-signature-with-the-azure-cli"></a>Erstellen einer Shared Access Signature über die Azure CLI

1. Um eine Benutzerdelegation-SAS für einen Container mit der Azure-Befehlszeilenschnittstelle (CLI) zu erstellen, stellen Sie sicher, dass Sie Version 2.0.78 oder höher installiert haben. Verwenden Sie den Befehl `az --version`, um Ihre installierte Version zu überprüfen.

1. Rufen Sie den Befehl [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas) auf.

1. Die folgenden Parameter sind erforderlich:

    * `auth-mode login`. Dieser Parameter stellt sicher, dass Anforderungen an Azure Storage mit Ihren Azure AD-Anmeldeinformationen autorisiert werden.
    * `as-user`. Dieser Parameter zeigt an, dass das generierte SAS ein Benutzerdelegierungs-SAS ist.

1. Unterstützte Berechtigungen für eine Benutzerdelegierungs-SAS auf einem Container sind Hinzufügen (a), Erstellen (c), Löschen (d), Liste (I), Lesen (r) und Schreiben (w). Stellen Sie sicher, dass **r**, **w**, **d** und **l** als Teil der Berechtigungsparameter enthalten sind.

1. Wenn Sie mit der Azure CLI eine Benutzerdelegierungs-SAS erstellen, ist die Benutzerdelegierungs-SAS maximal sieben Tage ab dem Startdatum gültig. Geben Sie einen Ablaufzeitpunkt für die Shared Access Signature an, der innerhalb von sieben Tagen nach dem Startzeitpunkt liegt. Weitere Informationen finden Sie unter [Erstellen einer SAS für die Benutzerdelegierung für einen Container oder ein Blob mit der Azure CLI](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli#use-azure-ad-credentials-to-secure-a-sas).

### <a name="example"></a>Beispiel

Erstellen einer Benutzerdelegierungs-SAS. Ersetzen Sie die Platzhalterwerte in den Klammern durch Ihre eigenen Werte:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="use-your-blob-sas-url"></a>Verwenden Ihrer Blob-SAS-URL

Es stehen zwei Optionen zur Verfügung:

* Um Ihre Blob SAS URL mit der [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) zu verwenden, fügen Sie die SAS URL zum Anforderungstext hinzu:

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* Um Ihre Blob-SAS-URL mit dem [Beschriftungstool Formularerkennung](https://fott-2-1.azurewebsites.net/connections/create) zu verwenden, fügen Sie die SAS URL zum Feld **Verbindungseinstellungen** > **Azure-Blobcontainer** > **SAS URI** hinzu:

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="Screenshot: Feld „SAS-URI“":::

Das ist alles. Sie haben gelernt, wie man SAS-Tokens erstellt, um den Zugriff von Clients auf Ihre Daten zu autorisieren.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Erstellen eines Trainingsdatasets](build-training-data-set.md)

---
title: Erstellen von SAS-Token (Shared Access Signature) für Container und Blobs mit Microsoft Azure Storage-Explorer
description: Es wird beschrieben, wie Sie ein SAS-Token (Shared Access Signature) für Container und Blobs erstellen, indem Sie Microsoft Azure Storage-Explorer und das Azure-Portal verwenden.
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 09/16/2021
ms.openlocfilehash: ecc62d7b45bfb3b0b108057d5e3e25b189297165
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653303"
---
# <a name="generate-sas-tokens-for-your-storage-containers"></a>Erstellen von SAS-Token für Ihre Speichercontainer

In diesem Artikel wird beschrieben, wie Sie SAS-Token (Shared Access Signature) erstellen, indem Sie den Azure Storage-Explorer oder das Azure-Portal nutzen. Ein SAS-Token ermöglicht den sicheren, delegierten Zugriff auf Ressourcen in Ihrem Azure-Speicherkonto.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Erstellen Ihrer SAS-Token mit dem Azure Storage-Explorer

### <a name="prerequisites"></a>Voraussetzungen

* In Ihrer Windows-, macOS- oder Linux-Entwicklungsumgebung muss eine [**Azure Storage-Explorer**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md)-App installiert sein. Azure Storage-Explorer ist ein kostenloses Tool, mit dem Sie Ihre Azure-Cloudspeicherressourcen auf einfache Weise verwalten können.
* Nachdem die Azure Storage-Explorer-App installiert wurde, [verbinden Sie sie mit dem Speicherkonto](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service), das Sie für die Dokumentübersetzung nutzen.

### <a name="create-your-tokens"></a>Erstellen Ihrer Token

### <a name="sas-tokens-for-containers"></a>[SAS-Token für Container](#tab/Containers)

1. Öffnen Sie die Azure Storage-Explorer-App auf Ihrem lokalen Computer, und navigieren Sie zu Ihren verbundenen **Speicherkonten**.
1. Erweitern Sie den Knoten „Speicherkonten“, und wählen Sie die Option **Blobcontainer** aus.
1. Erweitern Sie den Knoten „Blobcontainer“, und klicken Sie mit der rechten Maustaste auf einen **Containerknoten** des Speichers, um das Menü mit den Optionen anzuzeigen.
1. Wählen Sie im Menü mit den Optionen die Option **Shared Access Signature abrufen...** aus.
1. Wählen Sie im Fenster **Shared Access Signature** Folgendes aus:
    * Wählen Sie Ihre **Zugriffsrichtlinie** aus (Standardeinstellung: Keine).
    * Geben Sie das Datum und die Uhrzeit für den **Start** und **Ablauf** des signierten Schlüssels an. Hierbei wird die Verwendung einer kurzen Lebensdauer empfohlen, da eine SAS nach ihrer Generierung nicht mehr widerrufen werden kann.
    * Wählen Sie die **Zeitzone** für das Datum und die Uhrzeit des Starts und Ablaufs aus (Standardeinstellung: Lokal).
    * Definieren Sie Ihren Container **Berechtigungen**, indem Sie das entsprechende Kontrollkästchen aktivieren bzw. deaktivieren.
    * Überprüfen Sie die Angaben, und wählen Sie die Option **Erstellen** aus.

1. Ein neues Fenster wird angezeigt, das den Namen des **Containers**, den **URI** und die **Abfragezeichenfolge** für Ihren Container enthält.  
1. **Kopieren Sie die Werte für den Container, den URI und die Abfragezeichenfolge, und fügen Sie sie zur Aufbewahrung an einem sicheren Ort ein. Diese Angaben werden nur einmal angezeigt und können nicht mehr abgerufen werden, nachdem das Fenster geschlossen wurde.**
1. Fügen Sie zum Erstellen einer SAS-URL das SAS-Token (URI) an die URL für einen Speicherdienst an.

### <a name="sas-tokens-for-blobs"></a>[SAS-Token für Blobs](#tab/blobs)

1. Öffnen Sie die Azure Storage-Explorer-App auf Ihrem lokalen Computer, und navigieren Sie zu Ihren verbundenen **Speicherkonten**.
1. Erweitern Sie Ihren Speicherknoten, und wählen Sie die Option **Blobcontainer** aus.
1. Erweitern Sie den Knoten „Blobcontainer“, und wählen Sie einen **Containerknoten** aus, um den Inhalt des Hauptfensters anzuzeigen.
1. Wählen Sie das Blob aus, für das Sie den SAS-Zugriff delegieren möchten, und klicken Sie mit der rechten Maustaste, um das Menü mit den Optionen anzuzeigen.
1. Wählen Sie im Menü mit den Optionen die Option **Shared Access Signature abrufen...** aus.
1. Wählen Sie im Fenster **Shared Access Signature** Folgendes aus:
    * Wählen Sie Ihre **Zugriffsrichtlinie** aus (Standardeinstellung: Keine).
    * Geben Sie das Datum und die Uhrzeit für den **Start** und **Ablauf** des signierten Schlüssels an. Hierbei wird die Verwendung einer kurzen Lebensdauer empfohlen, da eine SAS nach ihrer Generierung nicht mehr widerrufen werden kann.
    * Wählen Sie die **Zeitzone** für das Datum und die Uhrzeit des Starts und Ablaufs aus (Standardeinstellung: Lokal).
    * Definieren Sie Ihren Container **Berechtigungen**, indem Sie das entsprechende Kontrollkästchen aktivieren bzw. deaktivieren.
    * Überprüfen Sie die Angaben, und wählen Sie die Option **Erstellen** aus.
1. Ein neues Fenster wird angezeigt, das den Namen des **Blobs**, den **URI** und die **Abfragezeichenfolge** für Ihr Blob enthält.  
1. **Kopieren Sie die Werte für das Blob, den URI und die Abfragezeichenfolge, und fügen Sie sie zur Aufbewahrung an einem sicheren Ort ein. Diese Angaben werden nur einmal angezeigt und können nicht mehr abgerufen werden, nachdem das Fenster geschlossen wurde.**
1. Fügen Sie zum Erstellen einer SAS-URL das SAS-Token (URI) an die URL für einen Speicherdienst an.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Erstellen von SAS-Token für Blobs im Azure-Portal

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/).  Falls Sie noch kein Konto haben, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).
* Eine [**Textübersetzungsdienst**](https://ms.portal.azure.com/#create/Microsoft)-Ressource (**keine** Cognitive Services-Ressource für mehrere Dienste).  *Siehe* [Erstellen einer neuen Azure-Ressource](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Ein [**Azure Blob Storage-Konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie erstellen Container zum Speichern und Organisieren Ihrer Blobdaten unter Ihrem Speicherkonto.

### <a name="create-your-tokens"></a>Erstellen Ihrer Token

Navigieren Sie zum [Azure-Portal](https://ms.portal.azure.com/#home), und greifen Sie dann auf Folgendes zu:  

 **Ihr Speicherkonto** > **Container** > **Ihr Container** > **Ihr Blob**.

1. Wählen Sie oben auf der Seite im Menü die Option **SAS generieren** aus.

1. Wählen Sie **Signaturmethode** > **Benutzerdelegierungsschlüssel** aus.

1. Definieren Sie **Berechtigungen**, indem Sie das entsprechende Kontrollkästchen aktivieren bzw. deaktivieren.

1. Geben Sie die Zeiten für den **Start** und **Ablauf** des signierten Schlüssels an.

1. Das Feld **Zugelassene IP-Adressen** ist optional. Darin wird eine IP-Adresse oder ein Bereich mit IP-Adressen angegeben, für die Anforderungen akzeptiert werden. Wenn die IP-Adresse der Anforderung nicht mit der IP-Adresse oder dem Adressbereich übereinstimmt, die bzw. der im SAS-Token angegeben ist, wird sie nicht autorisiert.

1. Das Feld **Zugelassene Protokolle** ist optional. Darin wird das Protokoll angegeben, das für das Senden einer Anforderung mit der SAS zulässig ist. Der Standardwert ist „HTTPS“.

1. Überprüfen Sie die Angaben, und wählen Sie dann die Option **SAS-Token und -URL generieren** aus.

1. Die Abfragezeichenfolge für das **Blob-SAS-Token** und die **Blob-SAS-URL** werden unten im Fenster angezeigt.  

1. **Kopieren Sie die Werte für das SAS-Token und die URL des Blobs, und fügen Sie sie an einem sicheren Ort ein. Diese Angaben werden nur einmal angezeigt und können nicht mehr abgerufen werden, nachdem das Fenster geschlossen wurde.**

1. Fügen Sie zum Erstellen einer SAS-URL das SAS-Token (URI) an die URL für einen Speicherdienst an.

## <a name="learn-more"></a>Erfahren Sie mehr

* [Programmgesteuertes Erstellen von SAS-Token für Blobs oder Container](../../../storage/blobs/sas-service-create.md)
* [Berechtigungen für ein Verzeichnis, einen Container oder ein Blob](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte bei der Dokumentübersetzung](get-started-with-document-translation.md)
>
>

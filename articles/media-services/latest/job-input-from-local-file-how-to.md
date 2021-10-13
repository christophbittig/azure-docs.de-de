---
title: Erstellen einer Auftragseingabe aus einer lokalen Datei
description: In diesem Artikel wird gezeigt, wie Sie eine Azure Media Services-Auftragseingabe aus einer lokalen Datei erstellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 05/25/2021
ms.author: inhenkel
ms.openlocfilehash: fa8b26c2f26ed93bda0ba96b695e94951b72af6b
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658213"
---
# <a name="create-a-job-input-from-a-local-file"></a>Erstellen einer Auftragseingabe aus einer lokalen Datei

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wenn Sie in Media Services v3 Aufträge zur Verarbeitung Ihrer Videos übermitteln, müssen Sie Media Services mitteilen, wo sich das Eingabevideo befindet. Das Eingabevideo kann als Media Service-Medienobjekt gespeichert werden. In diesem Fall erstellen Sie ein Eingabemedienobjekt basierend auf einer Datei (die lokal oder in Azure Blob Storage gespeichert ist). In diesem Thema wird gezeigt, wie Sie eine Auftragseingabe aus einer lokalen Datei erstellen. Ein vollständiges Beispiel finden Sie in diesem [GitHub-Beispiel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md)

## <a name="net-sample"></a>.NET-Beispiel

Der folgende Code zeigt, wie Sie ein Eingabemedienobjekt erstellen und es als Eingabe für den Auftrag nutzen können. Die „CreateInputAsset“-Funktion führt die folgenden Aktionen aus:

* Das Medienobjekt erstellen
* Abrufen einer nicht schreibgeschützten [SAS-URL](../../storage/common/storage-sas-overview.md) für den [Container](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-a-blob-to-a-container) des Objekts im Speicher
* Die Datei in den Container im Speicher mithilfe der SAS-URL hochladen

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Der folgende Codeausschnitt erzeugt ein Ausgabemedienobjekt, wenn es nicht bereits vorhanden ist:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Der folgende Codeausschnitt übermittelt einen Codierungsauftrag:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Auftragsfehlercodes

Weitere Informationen finden Sie unter [Fehlercodes](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Auftragseingabe aus einer HTTPS-URL](job-input-from-http-how-to.md)

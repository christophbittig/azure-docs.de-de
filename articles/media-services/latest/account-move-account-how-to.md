---
title: Verwalten von Azure Media Services v3-Konten
description: Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen. In diesem Artikel wird erläutert, wie Azure Media Services v3-Konten verwaltet werden.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: cbda31a3aaaf741424db174aa6a676858b27a23c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471210"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Verwalten von Azure Media Services v3-Konten

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um mit dem Verwalten, Verschlüsseln, Codieren, Analysieren und Streamen von Medieninhalten in Azure beginnen zu können, müssen Sie ein Media Services-Konto erstellen. Wenn Sie ein Media Services-Konto erstellen, müssen Sie den Namen einer Azure Storage-Kontoressource angeben. Das angegebene Speicherkonto wird an Ihr Media Services-Konto angefügt. Das Media Services-Konto und alle zugeordneten Speicherkonten müssen im gleichen Azure-Abonnement enthalten sein. Weitere Informationen finden Sie unter [Speicherkonten](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="media-services-account-names"></a>Name des Media Services-Kontos

Der Name eines Media Services-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen. Media Services-Kontonamen müssen innerhalb eines Azure-Standorts eindeutig sein.

Wenn ein Media Services-Konto gelöscht wird, wird der Kontoname für ein Jahr reserviert. Für ein Jahr nach dem Löschen des Kontos kann der Kontoname nur von dem Abonnement, das das ursprüngliche Konto enthielt, am gleichen Azure-Standort wiederverwendet werden.

Media Services-Kontonamen werden in DNS-Namen verwendet, Schlüsselübermittlungs-, Liveereignis- und Streamingendpunktnamen inbegriffen. Wenn Sie Firewalls oder Proxys so konfiguriert haben, dass Media Services-DNS-Namen zulässig sind, stellen Sie sicher, dass diese Konfigurationen innerhalb eines Jahres nach dem Löschen eines Media Services-Kontos entfernt werden.

## <a name="moving-a-media-services-account-between-subscriptions"></a>Verschieben eines Media Services-Kontos zwischen Abonnements

Wenn Sie ein Media Services-Konto in ein neues Abonnement verschieben möchten, müssen Sie zunächst die gesamte Ressourcengruppe, die das Media Services-Konto enthält, in das neue Abonnement verschieben. Dabei müssen alle angefügten Ressourcen verschoben werden: Azure Storage-Konten, Azure CDN-Profile usw. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Wie bei allen Ressourcen in Azure kann das Verschieben einer Ressourcengruppe einige Zeit in Anspruch nehmen.

### <a name="considerations"></a>Überlegungen

* Erstellen Sie vor der Migration zu einem anderen Abonnement Sicherungen aller Daten in Ihrem Konto.
* Beenden Sie alle Streamingendpunkte und Livestreamingressourcen. Während der Verschiebung der Ressourcengruppe können die Benutzer nicht auf Ihre Inhalte zugreifen. 

> [!IMPORTANT]
> Starten Sie den Streamingendpunkt erst, nachdem der Verschiebevorgang erfolgreich abgeschlossen wurde.

### <a name="troubleshoot"></a>Problembehandlung

Wenn ein Media Services-Konto oder ein zugehöriges Azure Storage-Konto nach dem Verschieben der Ressourcengruppe „getrennt“ wird, versuchen Sie, die Speicherkontoschlüssel zu rotieren. Sollte das Rotieren der Speicherkontoschlüssel das Problem mit dem getrennten Media Services-Kontos nicht beheben, stellen Sie im Media Services-Konto über das Menü „Support und Problembehandlung“ eine neue Supportanfrage.  

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Kontos](./account-create-how-to.md)

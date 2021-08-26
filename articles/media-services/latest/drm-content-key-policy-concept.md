---
title: 'Richtlinien für Inhaltsschlüssel in Media Services: Azure'
description: In diesem Artikel wird erläutert, was Richtlinien für Inhaltsschlüssel sind und wie sie in Azure Media Services verwendet werden.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: d8bff86065e4849723ab4001bd19218b89a2c8a2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633730"
---
# <a name="content-key-policies"></a>Richtlinien für Inhaltsschlüssel

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit Media Services können Sie Ihre zu übermittelnden Live- und On-Demand-Inhalte dynamisch mit Advanced Encryption Standard (AES-128) oder einem der drei wichtigsten DRM-Systeme verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients. 

Um Verschlüsselungsoptionen für Ihren Datenstrom festzulegen, müssen Sie die [Streamingrichtlinie](stream-streaming-policy-concept.md) erstellen und sie mit Ihrem [Streaminglocator](stream-streaming-locators-concept.md) verknüpfen. Sie erstellen die [Richtlinie für den Inhaltsschlüssel](/rest/api/media/contentkeypolicies), um zu konfigurieren, wie der Inhaltsschlüssel (der den sicheren Zugriff auf Ihre [Medienobjekte](assets-concept.md) ermöglicht) an Endclients übermittelt wird. Sie müssen die Anforderungen (Einschränkungen) für die Richtlinie für den Inhaltsschlüssel festlegen, die erfüllt sein müssen, damit Schlüssel mit der angegebenen Konfiguration an Clients übermittelt werden. Die Richtlinie für den Inhaltsschlüssel ist zum Streamen oder Herunterladen als Klartext nicht erforderlich. 

Normalerweise ordnen Sie Ihre Richtlinie für Inhaltsschlüssel Ihrem [Streaminglocator](stream-streaming-locators-concept.md) zu. Alternativ können Sie die Richtlinie für Inhaltsschlüssel auch in einer [Streamingrichtlinie](stream-streaming-policy-concept.md) angeben (beim Erstellen einer benutzerdefinierten Streamingrichtlinie für erweiterte Szenarien). 

## <a name="best-practices-and-considerations"></a>Bewährte Methoden und Überlegungen

> [!IMPORTANT]
> Berücksichtigen Sie die folgenden Empfehlungen.

* Sie sollten eine begrenzte Sammlung von Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Optionen benötigt werden. Weitere Informationen finden Sie unter [Kontingente und Grenzwerte](limits-quotas-constraints-reference.md).
* Richtlinien für Inhaltsschlüssel sind aktualisierbar. Es kann bis zu 15 Minuten dauern, bis die Schlüsselbereitstellungscaches aktualisiert werden und die aktualisierte Richtlinie übernommen wird. 

   Durch Aktualisieren der Richtlinie wird der vorhandene CDN-Cache überschrieben. Dies kann bei Kunden, die zwischengespeicherte Inhalte verwenden, zu Problemen bei der Wiedergabe führen.  
* Es empfiehlt sich nicht, für jede Ressource eine neue Richtlinie für Inhaltsschlüssel zu erstellen. Die gemeinsame Nutzung der gleichen Richtlinie für Inhaltsschlüssel für Ressourcen, für die die gleichen Richtlinienoptionen erforderlich sind, bietet die folgenden Hauptvorteile:
   
   * Eine kleinere Anzahl von Richtlinien lässt sich einfacher verwalten.
   * Wenn Sie Aktualisierungen an der Richtlinie für Inhaltsschlüssel vornehmen möchten, werden die Änderungen für alle neuen Lizenzanforderungen fast sofort wirksam.
* Wenn Sie eine neue Richtlinie erstellen möchten, müssen Sie einen neuen Streaminglocator für die Ressource erstellen.
* Es wird empfohlen, den Inhaltsschlüssel in Media Services automatisch generieren zu lassen. 

   Normalerweise wird ein langlebiger Schlüssel verwendet und das Vorhandensein der Richtlinie für Inhaltsschlüssel mithilfe von [Get](/rest/api/media/contentkeypolicies/get) überprüft. Um den Schlüssel abzurufen, müssen Sie eine separate Aktionsmethode aufrufen, um geheime Schlüssel oder Anmeldeinformationen abzurufen, wie im folgenden Beispiel gezeigt.

## <a name="example"></a>Beispiel

Verwenden Sie `GetPolicyPropertiesWithSecretsAsync`, um den Schlüssel abzurufen. Dies wird im Beispiel unter [Abrufen eines Signaturschlüssels aus der vorhandenen Richtlinie](drm-get-content-key-policy-dotnet-how-to.md#get-contentkeypolicy-with-secrets) veranschaulicht.

## <a name="filtering-ordering-paging"></a>Filterung, Sortierung, Paging

Informationen finden Sie unter [Filterung, Sortierung, Paginierung von Media Services-Entitäten](filter-order-page-entities-how-to.md).

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Eigenschaften von Richtlinien für Inhaltsschlüssel vom Typ `Datetime` liegen immer im UTC-Format vor.
* Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](drm-playready-license-template-concept.md)
* [Verwenden der dynamischen DRM-Verschlüsselung und des Lizenzbereitstellungsdiensts](drm-protect-with-drm-tutorial.md)
* [Grundlegende AES Clear Key-Verschlüsselung und Streamingbeispielcode](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)

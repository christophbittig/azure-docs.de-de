---
title: Übersicht über Azure Media Player
description: Weitere Informationen zu Azure Media Player
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: faf5bc8a96cb3781ed1d48e9d7f0d1fd7cbc386e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679577"
---
# <a name="azure-media-player-overview"></a>Übersicht über Azure Media Player #

Azure Media Player ist ein Webvideoplayer für die Wiedergabe von Medieninhalten aus [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) über eine Vielzahl von Browsern und Geräten. Der Azure Media Player nutzt Industriestandards wie HTML5, MSE (Media Source Extensions) und EME (Encrypted Media Extensions) für ein funktionsreiches adaptives Streaming.  Wenn diese Standards auf einem Gerät oder in einem Browser nicht verfügbar sind, verwendet Azure Media Player Flash und Silverlight als Fallback-Technologien. Unabhängig von der verwendeten Wiedergabetechnologie verfügen Entwickler über eine einheitliche JavaScript-Schnittstelle für den Zugriff auf APIs. Dies ermöglicht die Inhaltswiedergabe von Azure Media Services über eine breite Palette von Geräten und Browsern ohne jeglichen Zusatzaufwand.

Mit Microsoft Azure Media Services können Inhalte im DASH-, Smooth Streaming- und HLS-Format wiedergegeben werden. Der Azure Media Player erkennt diese unterschiedlichen Formate und übernimmt automatisch die beste Verknüpfung basierend auf den Funktionen von Plattform und Browser. Microsoft Azure Media Services ermöglicht auch die dynamische Verschlüsselung von Medienobjekten mit Common Encryption (PlayReady oder Widevine) oder AES-128-Bit-Umschlagverschlüsselung. Der Azure Media Player ermöglicht bei entsprechender Konfiguration die Entschlüsselung von PlayReady- und AES-128-Bit-verschlüsselten Inhalten.  Informationen zu dieser Konfiguration des Players finden Sie im Abschnitt [Geschützter Inhalt](azure-media-player-protected-content.md).

Bei bestimmten Problemen oder Fragen, oder wenn Sie Fehler finden, [erstellen Sie ein Supportticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) in der Kategorie „Clientwiedergabe“.

> [!NOTE]
> Beachten Sie, dass Azure Media Player nur Mediendatenströme von Azure Media Services unterstützt.

## <a name="license"></a>Lizenz ##

Azure Media Player ist lizenziert und unterliegt den Bestimmungen der Microsoft-Software-Lizenzbedingungen für Azure Media Player. Die vollständigen Bestimmungen finden Sie in der [Lizenzdatei](/legal/azure-media-player/azure-media-player-license). Weitere Informationen finden Sie in den [Datenschutzbestimmungen](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015 Microsoft Corporation

## <a name="next-steps"></a>Nächste Schritte ##

- [Schnellstart für Azure Media Player](azure-media-player-quickstart.md)

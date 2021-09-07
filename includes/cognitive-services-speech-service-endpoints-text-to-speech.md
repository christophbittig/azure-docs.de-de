---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 95f69f04ec1aa0fb0c5588647709d8d938ad2a53
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441378"
---
### <a name="neural-and-standard-voices"></a>Neuronale Stimmen und Standardstimmen

Verwenden Sie diese Tabelle, um die **Verfügbarkeit von neuronalen Stimmen und Standardstimmen** nach Region/Endpunkt zu ermitteln:

| Region | Endpunkt |
|--------|----------|
| Australien (Osten) | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Brasilien Süd | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Kanada, Mitte | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA (Mitte) | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Asien, Osten | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA (Ost) 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| Frankreich, Mitte | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Indien, Mitte | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japan, Osten | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japan, Westen | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Korea, Mitte | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA Nord Mitte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Nordeuropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA Süd Mitte | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Asien, Südosten | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| UK, Süden | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA, Westen-Mitte | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa, Westen | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA (Westen) | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA, Westen 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> [Die sich in der Vorschau befindlichen Stimmen](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) sind nur in den folgenden drei Regionen verfügbar: „USA, Osten“, „Europa, Westen“ und „Asien, Südosten“.

### <a name="custom-voices"></a>Benutzerdefinierte Stimmen

Wenn Sie einen benutzerdefinierten Voicefont erstellt haben, verwenden Sie den von Ihnen erstellten Endpunkt. Sie können auch die unten aufgeführten Endpunkte verwenden und die `{deploymentId}` durch die Bereitstellungs-ID für Ihr Stimmmodell ersetzen.

| Region | Endpunkt |
|--------|----------|
| Australien (Osten) | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brasilien Süd | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kanada, Mitte | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Mitte) | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asien, Osten | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| East US | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Ost) 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Frankreich, Mitte | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Indien, Mitte | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan, Osten | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan, Westen | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea, Mitte | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Nord Mitte | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Nordeuropa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA Süd Mitte | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Asien, Südosten | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| UK, Süden | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa, Westen | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA (Westen) | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA, Westen 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Benutzerdefinierte neuronale Stimme

In der folgenden Tabelle wird die regionale Unterstützung für die Features der benutzerdefinierten neuronalen Stimme erläutert.

| Funktion | Unterstützte Regionen |
|---|---|
| Hosting des Stimmmodells | „USA, Osten“, „USA, Westen 2“, „USA, Süden-Mitte“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „Europa, Westen“, „Australien, Osten“ |
| Echtzeitzeichen | „USA, Osten“, „USA, Westen 2“, „USA, Süden-Mitte“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „Europa, Westen“, „Australien, Osten“ |
| Zeichenanzahl für langes Audio | „USA, Osten“, „Europa, Westen“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „Indien, Mitte“ |
| Benutzerdefiniertes neuronales Training | „USA, Osten“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“ |

### <a name="long-audio-api"></a>API für lange Audioinhalte

Die API für lange Audioinhalte ist in mehreren Regionen mit eindeutigen Endpunkten verfügbar:

| Region | Endpunkt |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indien, Mitte | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Asien, Südosten | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| UK, Süden | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa, Westen | `https://westeurope.customvoice.api.speech.microsoft.com` |

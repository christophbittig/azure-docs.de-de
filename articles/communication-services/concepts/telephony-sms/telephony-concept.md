---
title: Konzepte für die Integration von Public Switched Telephone Network (PSTN) für Azure Communication Services
description: Hier erfahren Sie, wie Sie Festnetztelefoniefunktionen in Ihre Azure Communication Services-Anwendung integrieren.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: 66e39f0ec9beea775c18f0c1619cc8517e9b80a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609648"
---
# <a name="telephony-concepts"></a>Telefoniekonzepte

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Mithilfe der Azure Communication Services Calling SDKs können Sie Ihren Anwendungen Telefonie- und Public Switched Telephone Network-Zugriff hinzufügen. Auf dieser Seite werden wichtige Telefoniekonzepte und -funktionen zusammengefasst. Weitere Informationen zu bestimmten SDK-Sprachen und -Funktionen finden Sie [hier](../../quickstarts/voice-video-calling/calling-client-samples.md).

## <a name="overview-of-telephony"></a>Übersicht über Telefonie
Wenn Ihre Benutzer mit einer herkömmlichen Telefonnummer interagieren, werden Anrufe als Sprachanrufe über das Telefonfestnetz (Public Switched Telephone Network, PSTN) abgewickelt. Für aus- und eingehende PSTN-Anrufe müssen Ihrer Azure Communication Services-Ressource Telefoniefunktionen hinzugefügt werden. In diesem Fall wird für Signale und Medien eine Kombination aus IP- und PSTN-basierten Technologien verwendet, um die Verbindung zwischen den Benutzern herzustellen. Communication Services bietet zwei Möglichkeiten, um das Telefonfestnetz zu erreichen: Sprachanrufe (Festnetz) und direktes Azure-Routing.

### <a name="voice-calling-pstn"></a>Sprachanrufe (Festnetz)

Es gibt eine einfache Möglichkeit, um PSTN-Konnektivität für Ihre App oder Ihren Dienst bereitzustellen. In diesem Fall fungiert Microsoft als Ihr Telekommunikationsanbieter. Nummern können direkt von Microsoft erworben werden. Azure Cloud Calling ist eine reine Cloudtelefonielösung für Communication Services. Es ist die einfachste Möglichkeit eine Verbindung zwischen Communication Services und dem Public Switched Telephone Network (PSTN) herzustellen um Anrufe auf Festnetz- und Mobiltelefonnummern auf der ganzen Welt zu ermöglichen. Microsoft fungiert somit als Ihr Festnetzbetreiber, wie in der folgenden Abbildung zu sehen ist:

![Diagramm: Sprachanrufe (Festnetz)](../media/telephony-concept/azure-calling-diagram.png)

Sprachanrufe (Festnetz) sind die richtige Lösung für Sie, wenn Folgendes erfüllt ist:
- Sprachanrufe (Festnetz) sind in Ihrer Region verfügbar.
- Sie können Ihren aktuellen Festnetzbetreiber wechseln.
- Sie möchten von Microsoft verwalteten Zugriff auf das Telefonfestnetz verwenden.

Merkmale dieser Option:
- Sie erhalten Nummern direkt von Microsoft und können Telefonnummern auf der ganzen Welt anrufen.
- Sie benötigen keine Bereitstellung und müssen sich nicht um die Verwaltung einer lokalen Bereitstellung kümmern, da Sprachanrufe (Festnetz) auf Azure Communication Services basiert.
- Hinweis: Bei Bedarf können Sie über direktes Azure-Routing einen unterstützten Session Border Controller (SBC) verbinden, um Interoperabilität mit Nebenstellenanlagen von Drittanbietern sowie mit analogen Geräten und anderen Drittanbietertelefoniegeräten zu ermöglichen, die von dem SBC unterstützt werden.

Hierfür ist eine ununterbrochene Verbindung mit Azure Communication Services erforderlich.  

Bei Cloudanrufen werden ausgehende Anrufe je nach Zielland nach Minutentarifen abgerechnet. Weitere Informationen finden Sie in der [aktuellen Tarifliste für PSTN-Aufrufe](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv).

### <a name="azure-direct-routing"></a>Direktes Azure-Routing

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

Mit dieser Option können Sie Ihre lokale Legacy-Telefonie und den Netzbetreiber Ihrer Wahl mit Azure Communication Services verbinden. Diese Funktion bietet Festnetztelefoniefunktionen für Ihre Communication Services-Anwendungen, auch wenn Sprachanrufe (PSTN) in Ihrem Land/Ihrer Region nicht verfügbar sind. 

![Diagramm: Direktes Azure-Routing](../media/telephony-concept/sip-interface-diagram.png)

Direktes Azure-Routing ist die richtige Lösung für Sie, wenn eine der folgenden Bedingungen erfüllt ist:

- Sie möchten Communication Services mit Festnetztelefoniefunktionen verwenden.
- Sie möchten bei Ihrem aktuellen Festnetzbetreiber bleiben.
- Sie möchten gemischtes Routing verwenden, bei dem einige Anrufe über Sprachanrufe (Festnetz) und einige über Ihren Betreiber abgewickelt werden.
- Sie müssen mit Nebenstellenanlagen und/oder Geräten von Drittanbietern interagieren (beispielsweise mit Pagern oder analogen Geräte).

Merkmale dieser Option:

- Sie verbinden Ihren eigenen unterstützten SBC ohne eine zusätzliche lokale Software mit Azure Communication Services.
- Sie können einen beliebigen Telefoniebetreiber mit Communication Services verwenden.
- Sie können diese Option bei Bedarf selbst konfigurieren und verwalten oder die Konfiguration und Verwaltung Ihrem Netzbetreiber oder Partner überlassen, sofern dieser eine solche Option anbietet.
- Sie können die Interoperabilität zwischen Ihren Telefoniegeräten (beispielsweise Nebenstellenanlagen von Drittanbietern oder analoge Geräte) und ACS konfigurieren.

Diese Option erfordert:

- Ununterbrochene Verbindung mit Azure
- Bereitstellen und Pflegen eines unterstützten SBC
- Vertrag mit einem externen Netzbetreiber (es sei denn, die Bereitstellung wird als Option für eine Verbindung mit einer Nebenstellenanlage eines Drittanbieters, mit analogen Geräten oder mit anderen Telefoniegeräten für Benutzer in Communication Services vorgenommen).

## <a name="next-steps"></a>Nächste Schritte

### <a name="conceptual-documentation"></a>Dokumentation

- [Telefonnummerntypen in Azure Communication Services](./plan-solution.md)
- [Planen des direkten Azure-Routings](./direct-routing-infrastructure.md)
- [Für das direkte Routing von Azure Communication Services zertifizierte Session Border Controller](./certified-session-border-controllers.md)
- [Preise](../pricing.md)

### <a name="quickstarts"></a>Schnellstarts

- [Beschaffen einer Telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Schnellstart: Auf Telefon anrufen](../../quickstarts/voice-video-calling/pstn-call.md)

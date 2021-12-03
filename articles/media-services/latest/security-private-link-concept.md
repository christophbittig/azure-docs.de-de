---
title: Überblick über die Verwendung privater Links mit Azure Media Services
description: Dieser Artikel gibt einen Überblick über die Verwendung privater Links mit Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 10d27003cfa5300e097c2328deb14c0d9c1f7e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095114"
---
# <a name="overview-of-using-azure-private-link-with-azure-media-services"></a>Überblick über die Verwendung privater Links mit Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel gibt einen Überblick über die Verwendung privater Links mit Azure Media Services.

## <a name="when-to-use-private-link-with-media-services"></a>Wann wird Private Link mit Mediendiensten verwendet?

Private Link ermöglicht den Zugriff auf Mediendienste von privaten Netzwerken aus. In Verbindung mit den von den Mediendiensten bereitgestellten Netzwerkzugangskontrollen können private Links die Nutzung von Mediendiensten ermöglichen, ohne dass die Endpunkte dem öffentlichen Internet ausgesetzt sind.

## <a name="azure-private-endpoint-and-azure-private-link"></a>Azure Private Endpoint und Azure Private Link

Ein [Azure Private Endpoint](/private-link/private-endpoint-overview) ist eine Netzwerkschnittstelle, die eine private IP-Adresse aus Ihrem virtuellen Netzwerk verwendet.  Diese Netzwerkschnittstelle verbindet Sie privat und sicher mit einem Dienst über Azure Private Link.

Auf die Endpunkte der Mediendienste kann von einem virtuellen Netzwerk aus über private Endpunkte zugegriffen werden. Der Zugriff auf private Endpunkte kann auch über virtuelle Netze mit Peer-Rechten oder andere Netze erfolgen, die über Express Route oder VPN mit dem virtuellen Netz verbunden sind.

[Azure Private Links](/private-link/) ermöglichen den Zugriff auf private Media Services-Endpunkte in Ihrem virtuellen Netzwerk, ohne sie dem öffentlichen Internet auszusetzen. Es leitet den Datenverkehr über das Microsoft-Backbone-Netz.

## <a name="restricting-access"></a>Einschränken des Zugriffs

> [!Important]
> Das Anlegen eines privaten Endpunkts **schaltet den Internetzugang zu diesem Endpunkt NICHT implizit aus**.

Der Internetzugriff auf die Endpunkte im Mediendienstekonto kann auf zwei Arten eingeschränkt werden:

- Einschränkung des Zugriffs auf alle Ressourcen innerhalb des Kontos für Mediendienste.
- Zugriffsbeschränkung für jede einzelne Ressource mit Hilfe der IP-Zulassungsliste.

## <a name="media-services-endpoints"></a>Endpunkte der Mediendienste

| Endpunkt                    | BESCHREIBUNG                                                               | Unterstützt privaten Link | Kontrolle des Internetzugangs |
| --------------------------- | ------------------------------------------------------------------------- | --------------------- | ----------------------- |
| Streamingendpunkt          | Der Ursprungsserver für das Streaming von Videos und Medienformaten in HLS und DASH | Ja                   | Liste zugelassener IP-Adressen            |
| Streaming-Endpunkt mit CDN | Streamen Sie Medien an viele Betrachter                                              | Nein                    | Verwaltet von CDN          |
| Wichtigste Lieferung                | Bereitstellung von Schlüsseln für Medieninhalte und DRM-Lizenzen für Medienbetrachter             | Ja                   | Liste zugelassener IP-Adressen            |
| Liveereignis                  | Nimmt Medieninhalte für Live-Streaming auf                                  | Ja                   | Liste zugelassener IP-Adressen            |

> [!NOTE]
> Konten für Mediendienste, die mit API-Versionen vor 2020-05-01 erstellt wurden, verfügen auch über einen Endpunkt für den alten RESTv2-API-Endpunkt (der noch nicht abgeschafft wurde).  Dieser Endpunkt unterstützt keine privaten Verbindungen.

## <a name="other-private-link-enabled-azure-services"></a>Andere Private Link-fähige Azure-Dienste

| Dienst                | Integration von Mediendiensten                      | Private Link-Dokumentation |
| ---------------------- | ----------------------------------------------- | -------------------------- |
| Azure Storage          | Für die Speicherung von Medien                             | [Verwenden privater Endpunkte für Azure Storage](/storage/common/storage-private-endpoints) |
| Azure-Schlüsseltresor        | Dient zur Speicherung von [kundenverwalteten Schlüsseln](security-customer-managed-keys-portal-tutorial.md)             | [Konfigurieren von Azure Key Vault-Netzwerkeinstellungen](/key-vault/general/how-to-azure-key-vault-network-security) |
| Azure Resource Manager | Bietet Zugang zu Mediendienste-APIs          | [Verwenden der REST-API zum Erstellen einer privaten Verbindung zum Verwalten von Azure-Ressourcen](/azure-resource-manager/management/create-private-link-access-rest) |
| Event Grid             | Bietet [Benachrichtigungen über Mediendienste-Ereignisse](./monitoring/job-state-events-cli-how-to.md) | [Konfigurieren privater Endpunkte für Azure Event Grid-Themen oder -Domänen](/event-grid/configure-private-endpoints)  |

## <a name="private-endpoints-are-created-on-the-media-services-account"></a>Private Endpunkte werden unter dem Mediendienste-Konto erstellt

Private Endpunkte für die Schlüsselbereitstellung, Streaming-Endpunkte und Live-Ereignisse werden über das Mediendienste-Konto erstellt, anstatt einzeln erstellt zu werden.

Eine private IP-Adresse wird für jeden Streaming-Endpunkt oder jedes Live-Ereignis im Mediendienste-Konto erstellt, wenn eine private Mediendienste-Endpunktressource erstellt wird. Wenn Sie zum Beispiel zwei gestartete Streaming-Endpunkte haben, sollte ein einzelner privater Endpunkt erstellt werden, um beide Streaming-Endpunkte mit einem virtuellen Netzwerk zu verbinden. Ressourcen können mit mehreren virtuellen Netzwerken gleichzeitig verbunden sein.

Der Internetzugang zum Mediendienste-Konto sollte eingeschränkt werden, entweder für alle Ressourcen innerhalb des Kontos oder für jede einzelne Ressource.

## <a name="private-link-pricing"></a>Private Link Preise
Einzelheiten zur Preisgestaltung finden Sie unter [Azure Private Link Preisgestaltung](https://azure.microsoft.com/pricing/details/private-link)

## <a name="private-link-how-tos-and-faqs"></a>Private Link-Anleitungen und FAQs

- [Erstellen Sie ein Mediendienste- und Speicherkonto mit einem privaten Link unter Verwendung einer Azure Resource Management-Vorlage](security-private-link-arm-how-to.md)
- [Erstellen einer privaten Verbindung für einen Streaming-Endpunkt](security-private-link-streaming-endpoint-how-to.md)
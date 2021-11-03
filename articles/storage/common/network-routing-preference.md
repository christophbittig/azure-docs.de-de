---
title: Netzwerkroutingpräferenz
titleSuffix: Azure Storage
description: Über die Netzwerkroutingpräferenz können Sie angeben, wie Netzwerkdatenverkehr von Clients über das Internet zu Ihrem Konto geleitet wird.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: 0ff5515dce384a43cf54c8fb4d7e4c8fe71e251e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054892"
---
# <a name="network-routing-preference-for-azure-storage"></a>Netzwerkroutingpräferenz für Azure Storage

Sie können die [Netzwerkroutingpräferenz](../../virtual-network/ip-services/routing-preference-overview.md) für Ihr Azure-Speicherkonto konfigurieren, um festzulegen, wie Netzwerkdatenverkehr von Clients über das Internet zu Ihrem Konto geleitet wird. Standardmäßig wird der Datenverkehr aus dem Internet über das [globale Microsoft-Netzwerk](../../networking/microsoft-global-network.md) an den öffentlichen Endpunkt Ihres Speicherkontos geleitet. Azure Storage bietet zusätzliche Optionen für die Konfiguration, wie der Datenverkehr zu Ihrem Speicherkonto geleitet wird.

Das Konfigurieren von Routingpräferenzen bietet Ihnen die Flexibilität, Ihren Datenverkehr entweder für eine erstklassige Netzwerkleistung oder hinsichtlich der Kosten zu optimieren. Wenn Sie eine Routingpräferenz konfigurieren, geben Sie an, wie der Datenverkehr für Ihr Speicherkonto standardmäßig zum öffentlichen Endpunkt geleitet wird. Sie können auch routenspezifische Endpunkte für Ihr Speicherkonto veröffentlichen.

> [!NOTE]
> Dieses Feature wird nicht in Premium-Leistungs-Speicherkonten oder Konten unterstützt, die so konfiguriert sind, dass sie die zonenredundanten Speicher (ZRS) verwenden.

## <a name="microsoft-global-network-versus-internet-routing"></a>Globales Microsoft-Netzwerk und Internetrouting

Standardmäßig greifen Clients außerhalb der Azure-Umgebung über das globale Microsoft-Netzwerk auf Ihr Speicherkonto zu. Das globale Microsoft-Netzwerk ist für die Auswahl von Pfaden mit geringer Latenz optimiert, um eine erstklassige Netzwerkleistung mit hoher Zuverlässigkeit zu erzielen. Sowohl der ein- als auch der ausgehende Datenverkehr wird über den Point of Presence (POP) geleitet, der dem Client am nächsten liegt. Diese standardmäßige Routingkonfiguration stellt sicher, dass der Datenverkehr von und zu Ihrem Speicherkonto für den Großteil des Pfades über das globale Microsoft-Netzwerk verläuft, wodurch die Netzwerkleistung maximiert wird.

Sie können die Routingkonfiguration für Ihr Speicherkonto so ändern, dass sowohl ein- als auch ausgehender Datenverkehr zu und von Clients außerhalb der Azure-Umgebung über den POP-Standort geroutet wird, der dem Speicherkonto am nächsten liegt. Diese Route minimiert die Übertragung Ihres Datenverkehrs über das globale Microsoft-Netzwerk und übergibt ihn zum frühestmöglichen Zeitpunkt an das ISP-Transitnetzwerk. Die Verwendung dieser Routingkonfiguration senkt die Netzwerkkosten.

Das folgende Diagramm zeigt, wie der Datenverkehr zwischen dem Client und dem Speicherkonto für jede Routingpräferenz verläuft:

![Übersicht über die Routingoptionen für Azure Storage](media/network-routing-preference/routing-options-diagram.png)

Weitere Informationen zu den Routingpräferenzen in Azure finden Sie unter [Was ist Routingpräferenz?](../../virtual-network/ip-services/routing-preference-overview.md).

## <a name="routing-configuration"></a>Routingkonfiguration

Eine Schritt-für-Schritt-Anleitung, die das Konfigurieren der Routingpräferenz und routenspezifischer Endpunkte veranschaulicht, finden Sie unter [Konfigurieren der Netzwerkroutingpräferenz für Azure Storage](configure-network-routing-preference.md).

Sie können zwischen dem globalen Microsoft-Netzwerk und dem Internetrouting als standardmäßige Routingpräferenz für den öffentlichen Endpunkt Ihres Speicherkontos wählen. Die standardmäßige Routingpräferenz gilt für den gesamten Datenverkehr von Clients außerhalb von Azure und betrifft die Endpunkte für Azure Data Lake Storage Gen2, Blobspeicher, Azure Files und statische Websites. Das Konfigurieren von Routingpräferenzen wird für Azure-Warteschlangen oder Azure-Tabellen nicht unterstützt.

Sie können auch routenspezifische Endpunkte für Ihr Speicherkonto veröffentlichen. Wenn Sie routenspezifische Endpunkte veröffentlichen, erstellt Azure Storage neue öffentliche Endpunkte für Ihr Speicherkonto, die den Datenverkehr über den gewünschten Pfad leiten. Diese Flexibilität ermöglicht es Ihnen, den Datenverkehr über eine bestimmte Route zu Ihrem Speicherkonto zu leiten, ohne Ihre standardmäßige Routingpräferenz zu ändern.

Wenn Sie z. B. einen von der Internetroute abhängigen Endpunkt für „StorageAccountA“ veröffentlichen, werden die folgenden Endpunkte für Ihr Speicherkonto veröffentlicht:

| Speicherdienst        | Routenspezifischer Endpunkt                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob-Dienst           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Dateidienst           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Statische Websites        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Wenn Sie über ein RA-GRS-Speicherkonto (georedundanter Speicher mit Lesezugriff) oder ein RA-GZRS-Speicherkonto (geozonenredundanter Speicher mit Lesezugriff) verfügen, werden beim Veröffentlichen von routenspezifischen Endpunkten automatisch auch die entsprechenden Endpunkte in der sekundären Region für den Lesezugriff erstellt.

| Speicherdienst        | Routenspezifischer schreibgeschützter sekundärer Endpunkt                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob-Dienst           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Dateidienst           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Statische Websites        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

Die Verbindungszeichenfolgen für die veröffentlichten routenspezifischen Endpunkte können über das [Azure-Portal](https://portal.azure.com) kopiert werden. Diese Verbindungszeichenfolgen können für die Autorisierung mit gemeinsam verwendetem Schlüssel mit allen vorhandenen Azure Storage SDKs und APIs verwendet werden.

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Routingpräferenz für Azure Storage ist in den folgenden Regionen verfügbar:

- USA (Mitte) 
- USA, Mitte (EUAP)
- East US 
- USA (Ost) 2
- USA (Ost) 2 
- USA, Osten 2 (EUAP)
- USA Süd Mitte
- USA, Westen-Mitte
- USA (Westen) 
- USA, Westen 2 
- Frankreich, Mitte 
- Frankreich, Süden 
- Deutschland, Norden 
- Deutschland, Westen-Mitte 
- USA Nord Mitte
- Nordeuropa 
- Norwegen, Osten 
- Schweiz, Norden
- Schweiz, Westen
- UK, Süden 
- UK, Westen 
- Europa, Westen 
- VAE, Mitte
- Asien, Osten 
- Asien, Südosten 
- Japan, Osten 
- Japan, Westen 
- Indien, Westen
- Australien (Osten) 
- Australien, Südosten 

Die folgenden bekannten Probleme betreffen die Routingpräferenz für Azure Storage:

- Zugriffsanforderungen für den routenspezifischen Endpunkt für das globale Microsoft-Netzwerk weisen den HTTP-Fehler 404 oder einen ähnlichen Fehler auf. Das Routing über das globale Microsoft-Netzwerk funktioniert wie erwartet, wenn es als standardmäßige Routingpräferenz für den öffentlichen Endpunkt festgelegt ist.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Einzelheiten zu Preisen und zur Abrechnung finden Sie im Abschnitt **Preise** in [Was ist Routingpräferenz?](../../virtual-network/ip-services/routing-preference-overview.md#pricing).

## <a name="next-steps"></a>Nächste Schritte

- [Was ist Routingpräferenz?](../../virtual-network/ip-services/routing-preference-overview.md)
- [Konfigurieren der Netzwerkroutingpräferenz](configure-network-routing-preference.md)
- [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](storage-network-security.md)
- [Sicherheitsempfehlungen für Blob Storage](../blobs/security-recommendations.md)
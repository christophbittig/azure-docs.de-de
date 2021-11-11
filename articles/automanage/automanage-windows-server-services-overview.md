---
title: Automanage für Windows Server
description: Übersicht über Azure Automanage für Windows Server-Funktionen in der Windows Server Azure Edition
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: efd5b643608b8d3de5cacbc1f810a9b1e9efb612
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501588"
---
# <a name="azure-automanage-for-windows-server"></a>Azure Automanage für Windows Server

Azure Automanage für Windows Server stellt neue Funktionen bereit, insbesondere für die _Windows Server Azure Edition_.  Diese Funktionen umfassen:
- Hotpatch (Vorschau)
- SMB über QUIC
- Erweitertes Netzwerk für Azure

> [!IMPORTANT]
> Der Hotpatch ist zurzeit öffentliche Vorschauversion verfügbar. Es ist ein Anmeldungsverfahren (opt-in) erforderlich, um die unten beschriebene Hotpatch-Funktion verwenden zu können.
> Diese Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und nicht für Produktionsworkloads empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Automanage für Windows Server-Funktionen befinden sich in einem dieser _Windows Server Azure Edition_-Images: 

- Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung)
- Windows Server 2022 Datacenter: Azure Edition (Core)

Die Funktionen variieren je nach Image. Weitere Informationen finden Sie unter [Erste Schritte](#getting-started-with-windows-server-azure-edition).

## <a name="automanage-for-windows-server-capabilities"></a>Funktionen von Automanage für Windows Server

### <a name="hotpatch-preview"></a>Hotpatch (Vorschau)

Hotpatch ist in der öffentlichen Vorschauphase in den folgenden Images verfügbar:

- Windows Server 2022 Datacenter: Azure Edition (Core)

Hotpatch bietet Ihnen die Möglichkeit, Sicherheitsupdates ohne Neustart auf Ihren virtuellen Computer anzuwenden.  Darüber hinaus automatisiert Automanage für Windows Server das Onboarding, die Konfiguration und die Orchestrierung des Hotpatchings.  Weitere Informationen finden Sie unter [Hotpatch](automanage-hotpatch.md).  

### <a name="smb-over-quic"></a>SMB über QUIC

SMB über QUIC ist in der öffentlichen Vorschauphase in den folgenden Images verfügbar:

- Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung)
- Windows Server 2022 Datacenter: Azure Edition (Core)

SMB über QUIC bietet ein SMB-VPN für Telearbeiter, Benutzer mobiler Geräte und Zweigstellen mit sicherer, zuverlässiger Konnektivität mit Edgedateiservern über nicht vertrauenswürdige Netzwerke wie das Internet. [QUIC](https://datatracker.ietf.org/doc/rfc9000/) ist ein Protokoll nach IETF-Standard, das in HTTP/3 verwendet wird und für maximalen Datenschutz mit TLS 1.3 konzipiert ist. Es erfordert eine Verschlüsselung, die nicht deaktiviert werden kann. SMB verhält sich innerhalb des QUIC-Tunnels normal, das bedeutet, die Benutzeroberfläche ändert sich nicht. SMB-Features wie Multichannel, Signierung, Komprimierung, kontinuierliche Verfügbarkeit und Verzeichnisleasing funktionieren normal. 

SMB über QUIC ist auch in [Automanage für Best Practices für Windows Server-Computer](automanage-windows-server.md) integriert, um die Verwaltung von SMB über QUIC zu vereinfachen. QUIC verwendet Zertifikate, um die Verschlüsselung zu ermöglichen, und Organisationen haben häufig Probleme, komplexe Public Key-Infrastrukturen zu verwalten. Automanage für Best Practices für Computer stellt sicher, dass Zertifikate nicht ohne Warnung ablaufen und dass SMB über QUIC aktiviert bleibt, um eine maximale Dienstkontinuität zu gewährleisten.

Weitere Informationen finden Sie unter [SMB über QUIC](https://aka.ms/smboverquic) und [Verwaltung von SMB über QUIC mit Automanage für Best Practices für Computer](automanage-smb-over-quic.md).
 

### <a name="extended-network-for-azure"></a>Erweitertes Netzwerk für Azure

Das erweiterte Netzwerk für Azure ist auf den folgenden Images verfügbar:

- Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung)
- Windows Server 2022 Datacenter: Azure Edition (Core)

Mit Azure Extended Network können Sie ein lokales Subnetz auf Azure ausdehnen, damit lokale virtuelle Computer bei der Migration zu Azure ihre ursprünglichen lokalen privaten IP-Adressen behalten können. Weitere Informationen finden Sie unter [Azure Extended Network](/windows-server/manage/windows-admin-center/azure/azure-extended-network).  


## <a name="getting-started-with-windows-server-azure-edition"></a>Erste Schritte mit Windows Server Azure Edition

Es ist wichtig, im Voraus zu überlegen, welche Funktionen von Automanage für Windows Server Sie verwenden möchten, und dann ein entsprechendes VM-Image auszuwählen, das alle diese Funktionen unterstützt.  Einige der _Windows Server Azure Edition_-Images unterstützen nur eine Teilmenge der Funktionen. Weitere Details hierzu finden Sie in der folgenden Tabelle.

### <a name="deciding-which-image-to-use"></a>Entscheidung über das zu verwendende Image 

|Image|Funktionen|
|--|--|
|Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung) | SMB über QUIC, erweitertes Netzwerk für Azure | 
| Windows Server 2022 Datacenter: Azure Edition (Core) | Hotpatch, SMB über QUIC, erweitertes Netzwerk für Azure | 

### <a name="creating-a-vm"></a>Erstellen eines virtuellen Computers

Um die Funktionen von Automanage für Windows Server auf einem neuen virtuellen Computer zu verwenden, verwenden Sie Ihre bevorzugte Methode zum Erstellen eines virtuellen Azure-Computers, und wählen Sie das _Windows Server Azure Edition_-Image aus, das dem Umfang von [Funktionen](#getting-started-with-windows-server-azure-edition) entspricht.  

> [!IMPORTANT]
> Einige Funktionen verfügen über bestimmte Konfigurationsschritte, die während der VM-Erstellung ausgeführt werden müssen, und einige in der Vorschauphase befindliche Funktionen stellen bestimmte Anforderungen an die Aktivierung und die Anzeige im Portal.  Weitere Informationen zur Verwendung dieser Funktion mit Ihrer VM finden Sie in den obigen Themen zu den einzelnen Funktionen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Weitere Informationen zu Azure Automanage finden Sie [hier](automanage-virtual-machines.md).
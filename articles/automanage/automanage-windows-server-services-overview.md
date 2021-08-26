---
title: Automanage für Windows Server-Dienste (Vorschauversion)
description: Übersicht über Automanage für Windows Server-Dienste und -funktionen mit Windows Server Azure Edition
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: 475ea083a6412f38093f601ce34da8775357fa3e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286449"
---
# <a name="automanage-for-windows-server-services-preview"></a>Automanage für Windows Server-Dienste (Vorschauversion)

Automanage für Windows Server-Dienste stellt neue Funktionen bereit, insbesondere für _Windows Server Azure Edition_.  Diese Funktionen umfassen:
- Hotpatch
- SMB über QUIC
- Erweitertes Netzwerk

> [!IMPORTANT]
> Automanage für Windows Server-Dienste befindet sich derzeit in der öffentlichen Vorschauphase. Es ist ein Anmeldungsverfahren (opt-in) erforderlich, um die unten beschriebene Hotpatch-Funktion verwenden zu können.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Automanage für Windows Server-Funktionen befinden sich in einem dieser _Windows Server Azure Edition_-Images: 

- Windows Server 2019 Datacenter: Azure Edition (Core)
- Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung)
- Windows Server 2022 Datacenter: Azure Edition (Core)

Die Funktionen variieren je nach Image. Weitere Informationen finden Sie unter [Erste Schritte](#getting-started-with-windows-server-azure-edition).

## <a name="automanage-for-windows-server-capabilities"></a>Funktionen von Automanage für Windows Server

### <a name="hotpatch"></a>Hotpatch

Hotpatch ist in der öffentlichen Vorschauphase in den folgenden Images verfügbar:

- Windows Server 2019 Datacenter: Azure Edition (Core)
- Windows Server 2022 Datacenter: Azure Edition (Core)

Hotpatch bietet Ihnen die Möglichkeit, Sicherheitsupdates ohne Neustart auf Ihren virtuellen Computer anzuwenden.  Darüber hinaus automatisiert Automanage für Windows Server das Onboarding, die Konfiguration und die Orchestrierung des Hotpatchings.  Weitere Informationen finden Sie unter [Hotpatch](automanage-hotpatch.md).  

### <a name="smb-over-quic"></a>SMB über QUIC

SMB über QUIC ist in der öffentlichen Vorschauphase in den folgenden Images verfügbar:

- Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung)
- Windows Server 2022 Datacenter: Azure Edition (Core)

SMB über QUIC ermöglicht Benutzern den Zugriff auf Dateien, wenn sie remote ohne VPN arbeiten, indem SMB-Datenverkehr über das QUIC-Protokoll getunnelt wird.  Weitere Informationen finden Sie unter [SMB über QUIC](/windows-server/storage/file-server/smb-over-quic).  

### <a name="azure-extended-network"></a>Azure Extended Network

Azure Extended Network ist in der öffentlichen Vorschau in den folgenden Images verfügbar:

- Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung)
- Windows Server 2022 Datacenter: Azure Edition (Core)

Mit Azure Extended Network können Sie ein lokales Subnetz auf Azure ausdehnen, damit lokale virtuelle Computer bei der Migration zu Azure ihre ursprünglichen lokalen privaten IP-Adressen behalten können. Weitere Informationen finden Sie unter [Azure Extended Network](/windows-server/manage/windows-admin-center/azure/azure-extended-network).  


## <a name="getting-started-with-windows-server-azure-edition"></a>Erste Schritte mit Windows Server Azure Edition

Es ist wichtig, im Voraus zu überlegen, welche Funktionen von Automanage für Windows Server Sie verwenden möchten, und dann ein entsprechendes VM-Image auszuwählen, das alle diese Funktionen unterstützt.  Einige der _Windows Server Azure Edition_-Images unterstützen nur eine Teilmenge der Funktionen. Weitere Details hierzu finden Sie in der folgenden Tabelle.

### <a name="deciding-which-image-to-use"></a>Entscheidung über das zu verwendende Image 

|Image|Funktionen|
|--|--|
| Windows Server 2019 Datacenter: Azure Edition (Core) | Hotpatch | 
|Windows Server 2022 Datacenter: Azure Edition (Desktoperfahrung) | SMB über QUIC, Extended Network | 
| Windows Server 2022 Datacenter: Azure Edition (Core) | Hotpatch, SMB über QUIC, Extended Network | 

### <a name="creating-a-vm"></a>Erstellen eines virtuellen Computers

Um die Funktionen von Automanage für Windows Server auf einem neuen virtuellen Computer zu verwenden, verwenden Sie Ihre bevorzugte Methode zum Erstellen eines virtuellen Azure-Computers, und wählen Sie das _Windows Server Azure Edition_-Image aus, das dem Umfang von [Funktionen](#getting-started-with-windows-server-azure-edition) entspricht.  Die Konfiguration dieser Funktionen muss ggf. während der VM-Erstellung erfolgen. Weitere Informationen zur VM-Konfiguration finden Sie in den einzelnen Funktionsthemen (z. B. [Hotpatch](automanage-hotpatch.md)).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Weitere Informationen zu Azure Automanage finden Sie [hier](automanage-virtual-machines.md).
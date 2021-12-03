---
title: Verwenden von Windows-Clientimages in Azure
description: Verwenden von Visual Studio-Abonnementvorteilen zum Bereitstellen von Windows 7, Windows 8 oder Windows 10 in Azure für Entwicklungs-/Testszenarien
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: fdb0459b796704666fedfd013571f952b8382c8f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466730"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Verwenden des Windows-Clients in Azure für Entwicklungs-/Testszenarien

**Gilt für**: :heavy_check_mark: Windows-VMs 

Sie können Windows 7, Windows 8 oder Windows 10 Enterprise (x64) in Azure für Entwicklungs-/Testszenarien verwenden, sofern Sie über ein entsprechendes Visual Studio-Abonnement (früher MSDN) verfügen. 

Informationen zum Ausführen von Windows 10 in einer Produktionsumgebung finden Sie unter [Bereitstellen von Windows 10 in Azure mit mehrinstanzenfähigen Hostingrechten](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Abonnementberechtigung
Aktive Visual Studio-Abonnenten (Personen, die eine Visual Studio-Abonnementlizenz erworben haben) können Windows-Clientimages für Entwicklungs- und Testzwecke verwenden. Windows-Clientimages können auf Ihrer eigenen Hardware oder auf virtuellen Azure-Computern bereitgestellt werden.

Bestimmte Windows-Clientimages sind im Azure Marketplace verfügbar. Visual Studio-Abonnenten können auch in jedem Angebotstyp 64-Bit-Images von Windows 7, Windows 8 oder Windows 10 [vorbereiten und erstellen](prepare-for-upload-vhd-image.md) und dann [in Azure hochladen](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Berechtigte Angebote und Clientimages
In der folgenden Tabelle werden die Angebots-IDs aufgeführt, die über den Azure Marketplace in Windows-Clientimages bereitgestellt werden können. Die Windows-Clientimages sind nur für die folgenden Angebote sichtbar. 

> [!NOTE]
> Imageangebote unter **Windows Client** im Azure Marketplace. Verwenden Sie den **Windows-Client** beim Suchen nach Clientimages für Visual Studio-Abonnenten. Wenn Sie ein Visual Studio-Abonnement erwerben müssen, sehen Sie sich die verschiedenen Optionen unter [Visual Studio kaufen](https://visualstudio.microsoft.com/vs/pricing/?tab=business) an

| Angebotsname | Angebotsnummer | Verfügbare Client-images | 
|:--- |:---:|:---:|
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N mit SP1 (x64) |
| [Visual Studio Enterprise-Abonnenten (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N mit SP1 (x64) |
| [Visual Studio Professional-Abonnenten](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N mit SP1 (x64) |
| [Visual Studio Test Professional-Abonnenten](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N mit SP1 (x64) |
| [Visual Studio Enterprise-Abonnenten](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N mit SP1 (x64) |
| [Visual Studio Enterprise-Abonnenten (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N mit SP1 (x64) |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N mit SP1 (x64) |

Weitere Informationen finden Sie unter [Informationen zu Microsoft-Angebotstypen](../../cost-management-billing/costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

## <a name="check-your-azure-subscription"></a>Überprüfen Ihres Azure-Abonnements
Wenn Sie Ihre Angebots-ID nicht kennen, können Sie sie über das Azure-Portal abrufen.  
- Im Fenster *Abonnements*: ![Angebots-ID im Azure-Portal](./media/client-images/offer-id-azure-portal.png) 
- Klicken Sie alternativ auf **Abrechnung** und dann auf Ihre Abonnement-ID. Die Angebots-ID wird im Fenster *Abrechnung* angezeigt. 
- Sie können die Angebots-ID auch auf der [Registerkarte „Abonnements“](https://account.windowsazure.com/Subscriptions) des Azure-Kontoportals anzeigen: ![Angebots-ID im Azure-Kontoportal](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Nächste Schritte
Jetzt können Sie Ihre VMs mit [PowerShell](quick-create-powershell.md), [Resource Manager-Vorlagen](ps-template.md) oder [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) bereitstellen.

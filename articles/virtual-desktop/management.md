---
title: Microsoft Endpoint Configuration Manager für Azure Virtual Desktop
description: Empfohlene Methoden zum Verwalten Ihrer Azure Virtual Desktop-Umgebung.
author: heidilohr
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1c44b679daa000602aad83d98a04894c5cc267b3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359113"
---
# <a name="microsoft-endpoint-manager-and-intune-for-azure-virtual-desktop"></a>Microsoft Endpoint Manager und Intune für Azure Virtual Desktop

Es wird empfohlen, [Microsoft Endpoint Manager](https://www.microsoft.com/endpointmanager) zu verwenden, um Ihre Azure Virtual Desktop-Umgebung nach der Bereitstellung zu verwalten. Bei Microsoft Endpoint Manager handelt es sich um eine einheitliche Verwaltungsplattform, die Microsoft Endpoint Configuration Manager und Microsoft Intune umfasst.

## <a name="microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager

Microsoft Endpoint Configuration Manager, Version 1906 und höher, kann zum Verwalten Ihrer Azure Virtual Desktop-Geräte verwendet werden. Weitere Informationen finden Sie unter [Unterstützte Betriebssystemversionen für Clients und Geräte für Configuration Manager](/mem/configmgr/core/plan-design/configs/supported-operating-systems-for-clients-and-devices#windows-virtual-desktop).

## <a name="microsoft-intune"></a>Microsoft Intune

Intune unterstützt Windows 10 Enterprise-VMs für Azure Virtual Desktop. Weitere Informationen zur Unterstützung finden Sie unter [Verwenden von Azure Virtual Desktop mit Intune](/mem/intune/fundamentals/windows-virtual-desktop).

Die Intune-Unterstützung für Windows 10 Enterprise-VMs mit mehreren Sitzungen in Azure Virtual Desktop befindet sich derzeit in der öffentlichen Vorschauphase. Informationen zu den zurzeit unterstützten Features der öffentlichen Vorschauversion finden Sie unter [Windows 10 Enterprise-Remotedesktops mit mehreren Sitzungen](/mem/intune/fundamentals/windows-virtual-desktop-multi-session).

## <a name="licensing"></a>Lizenzierung

[Microsoft Endpoint Configuration Manager- und Microsoft Intune-Lizenzen](https://microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) sind in den meisten Microsoft 365-Abonnements enthalten. 

Weitere Informationen zu Lizenzierungsanforderungen finden Sie in den folgenden Ressourcen:

- [Häufig gestellte Fragen für die Configuration Manager-Verzweigungen und -Lizenzierungen](/mem/configmgr/core/understand/product-and-licensing-faq#bkmk_equiv-sub) 
- [Microsoft Intune-Lizenzierung](/mem/intune/fundamentals/licenses)

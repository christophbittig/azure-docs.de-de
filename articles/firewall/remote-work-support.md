---
title: Unterstützung von Remotearbeit in Azure Firewall
description: In diesem Artikel erfahren Sie, wie Azure Firewall die Anforderungen Ihrer Remotemitarbeiter unterstützen kann.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 5112cf06952754eb1313166b82a9cf14876c797f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598268"
---
# <a name="azure-firewall-remote-work-support"></a>Unterstützung von Remotearbeit in Azure Firewall

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst zum Schutz Ihrer Azure Virtual Network-Ressourcen. Es ist eine vollständig zustandsbehaftete Firewall-as-a-Service mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit.

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>Unterstützung von VDI-Bereitstellungen (Virtual Desktop Infrastructure)

Die Anordnung des Arbeitens im Homeoffice verlangt von vielen IT-Organisationen, sich mit grundlegenden Veränderungen in den Bereichen Kapazität, Netzwerk, Sicherheit und Governance auseinanderzusetzen. Beschäftigte sind nicht durch die mehrstufigen Sicherheitsmaßnahmen in Verbindung mit lokalen Diensten geschützt, wenn sie von zu Hause aus arbeiten. VDI-Bereitstellungen (Virtual Desktop Infrastructure) in Azure können Organisationen dabei unterstützen, schnell auf dieses sich ändernde Umfeld zu reagieren. Sie benötigen jedoch eine Möglichkeit, den ein- und ausgehenden Internetdatenverkehr dieser VDI-Bereitstellungen zu schützen. Sie können[DNAT-Regeln](rule-processing.md) für Azure Firewall zusammen mit den auf [Threat Intelligence](threat-intel.md) basierenden Filterfunktionen verwenden, um Ihre VDI-Bereitstellungen zu schützen.

## <a name="azure-windows-virtual-desktop-support"></a>Unterstützung für Windows Virtual Desktop in Azure

Bei Windows Virtual Desktop handelt es sich um einen umfassenden in Azure ausgeführten Dienst zur Desktop- und App-Virtualisierung. Es ist die einzige virtuelle Desktopinfrastruktur (VDI), die eine vereinfachte Verwaltung, Windows 10-Mehrfachsitzungen, Optimierungen für Microsoft 365 Apps for Enterprise und Unterstützung für RDS-Umgebungen (Remote Desktop Services, Remotedesktopdienste) bietet. Sie können Ihre Windows-Desktops und -Apps in wenigen Minuten in Azure bereitstellen und skalieren und profitieren von integrierten Funktionen für Sicherheit und Compliance. Für Windows Virtual Desktop ist es nicht erforderlich, dass Sie den eingehenden Zugriff auf Ihr virtuelles Netzwerk öffnen. Sie müssen jedoch einen Satz ausgehender Netzwerkverbindungen für die virtuellen Windows Virtual Desktop-Computer zulassen, die in Ihrem virtuellen Netzwerk ausgeführt werden. Weitere Informationen finden Sie unter [Verwenden von Azure Firewall zum Schutz von Windows Virtual Desktop-Bereitstellungen](protect-azure-virtual-desktop.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Windows Virtual Desktop](../virtual-desktop/index.yml).
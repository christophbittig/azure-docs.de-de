---
title: Voraussetzungen und Anforderungen für Azure Network Function Manager
description: Erfahren Sie mehr über die Anforderungen und Voraussetzungen für Network Function Manager.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8843c92943071a96c6e0e9340823a85d01b1c218
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095604"
---
# <a name="network-function-manager-prerequisites-and-requirements"></a>Voraussetzungen und Anforderungen für Network Function Manager

In diesem Artikel werden die Voraussetzungen und Anforderungen beschrieben, die zum Konfigurieren und Verwenden von Network Function Manager erforderlich sind.

## <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>Installiertes und aktiviertes Azure Stack Edge Pro mit GPU

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt werden:

* Der Network Function Manager-Dienst ist auf dem Azure Stack Edge Pro-Gerät aktiviert.
* Vergewissern Sie sich vor dem Bereitstellen der Netzwerkfunktionen, dass Azure Stack Edge Pro installiert und aktiviert ist.
* Die Azure Stack Edge-Ressource muss in einer Region bereitgestellt werden, die von Network Function Manager-Ressourcen unterstützt wird. Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](overview.md#regions).
* Stellen Sie sicher, dass Sie alle Schritte in den [Schnellstartanleitungen](../databox-online/azure-stack-edge-gpu-quickstart.md) und den [Tutorials](../databox-online/azure-stack-edge-gpu-deploy-checklist.md) für Azure Stack Edge Pro ausführen.
* Überprüfen Sie, ob der **Status** des Geräts im Abschnitt „Eigenschaften“ für die Azure Stack Edge-Ressource im Azure-Verwaltungsportal auf **Online** festgelegt ist.

   :::image type="content" source="./media/overview/properties.png" alt-text="Screenshot der Eigenschaften" lightbox="./media/overview/properties.png":::

## <a name="partner-prerequisites"></a><a name="partner-prereq"></a>Voraussetzungen für Partner

Kunden können einen oder mehrere Netzwerkfunktionsmanager-[Partner](partners.md) auswählen, um deren Netzwerkfunktion auf einem Azure Stack Edge-Gerät bereitzustellen. Weitere Informationen zu Network Function Manager-Partnern finden Sie im Artikel [Partner](partners.md).

Jeder Partner hat bestimmte Netzwerkanforderungen für die Bereitstellung seiner Netzwerkfunktionen auf einem Azure Stack Edge-Gerät. Die folgenden Konfigurationsaufgaben finden Sie in der Produktdokumentation der Netzwerkfunktionspartner:

* [Konfigurieren des Netzwerks an verschiedenen Ports](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
* [Aktivieren des Computingnetzwerks auf Ihrem Azure Stack Edge-Gerät](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

## <a name="azure-account"></a><a name="account"></a>Azure-Konto

Partner von Azure Network Function Manager müssen dieselbe Azure-Abonnement-ID aktivieren, um ihre Netzwerkfunktion über Azure Marketplace bereitstellen zu können. Stellen Sie sicher, dass für Ihre Azure-Abonnement-ID ein Onboarding beim Partner durchgeführt wurde.

Der Azure-Netzwerkfunktionsmanager besteht aus **Geräte-** und **Netzwerkfunktions**-Ressourcen. Die Geräte- und Netzwerkfunktionsressourcen befinden sich innerhalb von Azure-Abonnements. Für das Aktivieren des Azure Stack Edge Pro-Geräts und der Netzwerkfunktionsmanager-Ressourcen sollte die gleiche Azure-Abonnement-ID verwendet werden.

## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>Portanforderungen und Firewallregeln

NFM-Dienste (Netzwerkfunktionsmanager), die in Azure Stack Edge ausgeführt werden, erfordern ausgehende Verbindungen mit dem NFM-Clouddienst für Verwaltungsdatenverkehr, um Netzwerkfunktionen bereitzustellen. NFM ist vollständig in den Azure Stack Edge-Dienst integriert. Überprüfen Sie die Netzwerkportanforderungen und Firewallregeln für das [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements)-Gerät.  

Netzwerkfunktionspartner haben unterschiedliche Anforderungen an die Firewall- und Portkonfigurationsregeln, mit denen der Datenverkehr zum Partnerverwaltungsportal verwaltet werden soll. Wenden Sie sich an Ihren Netzwerkfunktionspartner, um Informationen zu bestimmten Anforderungen zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen einer Network Function Manager-Geräteressource](create-device.md).

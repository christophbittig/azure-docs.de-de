---
title: Verwenden von Azure Firewall zum Schutz von Azure Virtual Desktop
description: Informationen zur Verwendung von Azure Firewall zum Schutz von Azure Virtual Desktop-Bereitstellungen
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 08/09/2021
ms.author: victorh
ms.openlocfilehash: 5c165dc8f00bb21894de06e541c02788bd7b51e5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424991"
---
# <a name="use-azure-firewall-to-protect-azure-virtual-desktop-deployments"></a>Verwenden von Azure Firewall zum Schutz von Azure Virtual Desktop-Bereitstellungen

Bei Azure Virtual Desktop handelt es sich um einen Dienst für die Desktop- und App-Virtualisierung, der unter Azure ausgeführt wird. Wenn ein Endbenutzer eine Verbindung mit einer Azure Virtual Desktop-Umgebung herstellt, wird die Sitzung von einem Hostpool ausgeführt. Ein Hostpool ist eine Sammlung von virtuellen Azure-Computern, die unter Azure Virtual Desktop als Sitzungshosts registriert werden. Diese virtuellen Computer werden in Ihrem virtuellen Netzwerk ausgeführt und unterliegen dessen Sicherheitskontrollen. Sie benötigen ausgehenden Zugriff auf das Internet, damit der Azure Virtual Desktop-Dienst ordnungsgemäß arbeiten kann, und benötigen möglicherweise zudem ausgehenden Internetzugriff für Endbenutzer. Mithilfe von Azure Firewall können Sie Ihre Umgebung sperren und ausgehenden Datenverkehr filtern.

[ ![Azure Virtual Desktop-Architektur](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Beachten Sie die Richtlinien in diesem Artikel, um mithilfe von Azure Firewall zusätzlichen Schutz für Ihren Azure Virtual Desktop-Hostpool bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

 - Eine bereitgestellte Azure Virtual Desktop-Umgebung und ein Hostpool
 - Ein Azure Firewall, die mit mindestens einer Firewall Manager-Richtlinie bereitgestellt ist

   Weitere Informationen finden Sie unter [Tutorial: Erstellen eines Hostpools mithilfe des Azure-Portals](../virtual-desktop/create-host-pools-azure-marketplace.md)

Weitere Informationen zu Azure Virtual Desktop-Umgebungen finden Sie unter [Azure Virtual Desktop-Umgebung](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-azure-virtual-desktop"></a>Ausgehender Zugriff für den Hostpool auf Azure Virtual Desktop

Die virtuellen Azure-Computer, die Sie für Azure Virtual Desktop erstellen, müssen Zugriff auf mehrere vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) haben, um ordnungsgemäß funktionieren zu können. Azure Firewall bietet ein FQDN-Tag für Azure Virtual Desktop, um diese Konfiguration zu vereinfachen. Führen Sie die folgenden Schritte aus, um ausgehenden Plattformdatenverkehr für Azure Virtual Desktop zuzulassen:

Sie müssen eine Azure-Firewallrichtlinie und Regelsammlungen für Netzwerkregeln und Anwendungsregeln erstellen. Weisen Sie der Regelsammlung eine Priorität und eine Aktion zum Zulassen oder Verweigern zu.

### <a name="create-network-rules"></a>Erstellen von Netzwerkregeln

| name      | Quellentyp | `Source`                    | Protocol | Zielports | Zieltyp | Destination                       |
| --------- | ----------- | ------------------------- | -------- | ----------------- | ---------------- | --------------------------------- |
| Regelname | IP-Adresse  | VNet- oder Subnetz-IP-Adresse | TCP      | 80                | IP-Adresse       | 169.254.169.254, 168.63.129.16    |
| Regelname | IP-Adresse  | VNet- oder Subnetz-IP-Adresse | TCP      | 443               | Diensttag      | AzureCloud, WindowsVirtualDesktop |
| Regelname | IP-Adresse  | VNet- oder Subnetz-IP-Adresse | TCP, UDP | 53                | IP-Adresse       | *                                 |

> [!NOTE]
> Für einige Bereitstellungen sind möglicherweise keine DNS-Regeln erforderlich. Azure Active Directory Domain Controller leiten beispielsweise DNS-Abfragen an Azure DNS unter 168.63.129.16 weiter.

### <a name="create-application-rules"></a>Erstellen von Anwendungsregeln

| name      | Quellentyp | `Source`                    | Protocol   | Zieltyp | Destination                                                                                 |
| --------- | ----------- | ------------------------- | ---------- | ---------------- | ------------------------------------------------------------------------------------------- |
| Regelname | IP-Adresse  | VNet- oder Subnetz-IP-Adresse | Https:443  | FQDN-Tag         | WindowsVirtualDesktop, WindowsUpdate, Windows-Diagnose, MicrosoftActiveProtectionService |
| Regelname | IP-Adresse  | VNet- oder Subnetz-IP-Adresse | Https:1688 | FQDN             | kms.core.windows.net                                                                        |

> [!IMPORTANT]
> Es wird empfohlen, die TLS-Überprüfung nicht mit Azure Virtual Desktop zu verwenden. Weitere Informationen finden Sie in den [Richtlinien für Proxyserver](../virtual-desktop/proxy-server-support.md#dont-use-ssl-termination-on-the-proxy-server).

## <a name="host-pool-outbound-access-to-the-internet"></a>Ausgehender Zugriff für Hostpools auf das Internet

Je nach Anforderungen Ihrer Organisation möchten Sie möglicherweise sicheren ausgehenden Internetzugriff für Ihre Endbenutzer ermöglichen. Wenn die Liste der zulässigen Ziele klar definiert ist (z. B. beim [Zugriff auf Microsoft 365](/microsoft-365/enterprise/microsoft-365-ip-web-service)), können Sie die Anwendungs- und Netzwerkregeln für Azure Firewall verwenden, um den erforderlichen Zugriff zu konfigurieren. Dadurch wird der Endbenutzerdatenverkehr direkt an das Internet geleitet, um eine optimale Leistung zu erzielen. Wenn Sie die Netzwerkkonnektivität für Windows 365 oder Intune zulassen müssen, finden Sie weitere Informationen unter [Netzwerkanforderungen für Windows 365](/windows-365/requirements-network#allow-network-connectivity) und [Netzwerkendpunkte für Intune](/mem/intune/fundamentals/intune-endpoints).

Wenn Sie den ausgehenden Internetdatenverkehr von Benutzern mithilfe eines vorhandenen lokalen, sicheren Webgateways filtern möchten, können Sie Webbrowser oder andere Anwendungen, die auf dem Azure Virtual Desktop-Hostpool ausgeführt werden, mithilfe einer expliziten Proxykonfiguration konfigurieren. Weitere Informationen hierzu finden Sie beispielsweise unter [So verwenden Sie Microsoft Edge-Befehlszeilenoptionen zum Konfigurieren von Proxyeinstellungen](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Diese Proxyeinstellungen beeinflussen nur den Internetzugriff für Endbenutzer, wodurch der ausgehende Datenverkehr der Azure Virtual Desktop-Plattform direkt über Azure Firewall ermöglicht wird.

## <a name="control-user-access-to-the-web"></a>Steuern des Benutzerzugriffs auf das Web

Administratoren können den Benutzerzugriff auf verschiedene Websitekategorien zulassen oder verweigern. Fügen Sie Ihrer Anwendungssammlung eine Regel von Ihrer spezifischen IP-Adresse zu Webkategorien hinzu, die Sie zulassen oder ablehnen möchten. Überprüfen Sie alle [Webkategorien](web-categories.md).

## <a name="additional-considerations"></a>Weitere Überlegungen

Je nach Anforderungen müssen Sie möglicherweise zusätzliche Firewallregeln konfigurieren:

- NTP-Serverzugriff

  Standardmäßig wird für virtuelle Computer unter Windows über den UDP-Port 123 zur Zeitsynchronisierung eine Verbindung mit `time.windows.com` hergestellt. Erstellen Sie eine Netzwerkregel, um diesen Zugriff zuzulassen, oder erstellen Sie eine solche Regel für einen Zeitserver, den Sie in Ihrer Umgebung verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Virtual Desktop: [Was ist Azure Virtual Desktop?](../virtual-desktop/overview.md)

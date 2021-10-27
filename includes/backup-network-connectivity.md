---
title: Datei einfügen
description: Datei einfügen
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/04/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 234d2510f115edf646ac490126e316a7dd3369c1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019137"
---
Der MARS-Agent benötigt Zugriff auf Azure Active Directory, Azure Storage und Azure Backup-Dienstendpunkte. Informationen zum Abrufen der öffentlichen IP-Adressbereiche finden Sie in der [JSON-Datei](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519&preserveview=true). Erlauben Sie den Zugriff auf die IPs für Azure Backup (`AzureBackup`), Azure Storage (`Storage`) und Azure Active Directory (`AzureActiveDirectory`). Außerdem benötigen Netzwerkkonnektivitätsprüfungen des Betriebssystems abhängig von Ihrer Windows-Version Zugriff auf `www.msftconnecttest.com` oder `www.msftncsi.com`.

Wenn Ihr Rechner nur begrenzten Internetzugang hat, stellen Sie sicher, dass die Firewall-, Proxy- und Netzwerkeinstellungen den Zugriff auf die folgenden FQDNs und öffentlichen IP-Adressen erlauben.

### <a name="url-and-ip-access"></a>Zugriff auf URLs und IP-Adressen

**FQDNs**

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- `*.microsoft.com`
- `*.windowsazure.com`
- `*.microsoftonline.com`
- `*.windows.net`

**IP-Adressen**

- `20.190.128.0/18`
- `40.126.0.0/18`

Wenn Sie US Government-Kunde sind, stellen Sie sicher, dass Sie Zugriff auf die folgenden URLs haben:

- `www.msftncsi.com`
- `*.microsoft.com`
- `*.windowsazure.us`
- `*.microsoftonline.us`
- `*.windows.net`
- `*.usgovcloudapi.net`

Beim Zugriff auf alle oben aufgeführten URLs und IP-Adressen wird das HTTPS-Protokoll auf Port 443 verwendet.

Beim Sichern von Dateien und Ordnern von Azure-VMs mit dem MARS-Agent müssen Sie auch das virtuelle Azure-Netzwerk so konfigurieren, dass es Zugriff zulässt. Wenn Sie Netzwerksicherheitsgruppen (NSG) verwenden, können Sie den ausgehenden Zugriff auf Azure Backup mithilfe des Diensttags AzureBackup zulassen. Zusätzlich zum Tag „Azure Backup“ müssen Sie auch Konnektivität für Authentifizierung und Datenübertragung zulassen, indem Sie ähnliche [NSG-Regeln](../articles/virtual-network/network-security-groups-overview.md#service-tags) für Azure AD (AzureActiveDirectory) und Azure Storage (Storage) erstellen.

Führen Sie die folgenden Schritte aus, um eine Regel für das Tag „Azure Backup“ zu erstellen:

1. Navigieren Sie unter **Alle Dienste** zu **Netzwerksicherheitsgruppen**, und wählen Sie die Netzwerksicherheitsgruppe aus.
1. Wählen Sie unter **Einstellungen** die Option **Ausgangssicherheitsregeln** aus.
1. Wählen Sie **Hinzufügen**.
1. Geben Sie alle erforderlichen Details zum Erstellen einer neuen Regel ein, wie in den [Einstellungen zu Sicherheitsregeln](../articles/virtual-network/manage-network-security-group.md#security-rule-settings) beschrieben.<br>Stellen Sie sicher, dass die Optionen wie folgt festgelegt sind:
   - **Ziel** ist auf _Diensttag_ festgelegt.
   - **Zieldiensttag** ist auf _AzureBackup_ festgelegt.
1. Wählen Sie **Hinzufügen** aus, um die neu erstellte Ausgangssicherheitsregel zu speichern.

Auf ähnliche Weise können Sie ausgehende NSG-Sicherheitsregeln für Azure Storage und Azure AD erstellen. Weitere Informationen zu Diensttags finden Sie unter [Diensttags in virtuellen Netzwerken](../articles/virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-Unterstützung

Sie können Ihre Daten über Azure ExpressRoute mithilfe von öffentlichem Peering (verfügbar für alte Leitungen) sichern. Microsoft-Peeringsicherung über privates Peering wird nicht unterstützt.

Stellen Sie zur Verwendung von öffentlichem Peering sicher, dass Sie an Port 443 über HTTPS auf die folgenden Domänen und Adressen zugreifen können:

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- `*.microsoft.com`
- `*.windowsazure.com`
- `*.microsoftonline.com`
- `*.windows.net`

**IP-Adressen**
- `20.190.128.0/18`
- `40.126.0.0/18`

Um Microsoft-Peering zu verwenden, wählen Sie die folgenden Dienste, Regionen und relevanten Communitywerte aus:
- Azure Active Directory (12076:5060)
- Azure-Region, entsprechend dem Standort Ihres Recovery Services-Tresors
- Azure Storage, entsprechend dem Standort Ihres Recovery Services-Tresors

Weitere Informationen zu [ExpressRoute-Routinganforderungen](../articles/expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Öffentliches Peering gilt für neue Leitungen als veraltet.


### <a name="private-endpoint-support"></a>Unterstützung für den privaten Endpunkt

Sie können nun private Endpunkte verwenden, um Ihre Daten von Servern sicher in Ihrem Recovery Services-Tresor zu sichern. Da Azure AD nicht über private Endpunkte zugänglich ist, müssen Sie IPs und FQDNs, die für Azure AD für den ausgehenden Zugriff erforderlich sind, separat zulassen.

Wenn Sie den MARS-Agent zur Sicherung Ihrer lokalen Ressourcen verwenden, stellen Sie sicher, dass Ihr lokales Netzwerk (das die zu sichernden Ressourcen enthält) ein Peering mit dem virtuellen Azure-Netzwerk aufweist, das einen privaten Endpunkt für den Tresor enthält. Sie können dann mit der Installation des MARS-Agents fortfahren und die Sicherung konfigurieren. Sie müssen jedoch sicherstellen, dass die gesamte Kommunikation für die Sicherung ausschließlich über das Peeringnetzwerk erfolgt.

Wenn Sie private Endpunkte für den Tresor entfernen, nachdem ein MARS-Agent bei ihm registriert wurde, müssen Sie den Container erneut beim Tresor registrieren. Sie müssen den Schutz für sie nicht aufheben. Weitere Informationen finden Sie unter [Private Endpunkte für Azure Backup](../articles/backup/private-endpoints.md).

### <a name="throttling-support"></a>Unterstützung für Drosselung

| Funktion | Details |
|---|---|
| Bandbreitensteuerung | Unterstützt. Verwenden Sie im MARS-Agent **Eigenschaften ändern**, um die Bandbreite anzupassen. |
| Netzwerkdrosselung | Nicht verfügbar für gesicherte Computer, auf denen Windows Server 2008 R2, Windows Server 2008 SP2 oder Windows 7 ausgeführt wird. |

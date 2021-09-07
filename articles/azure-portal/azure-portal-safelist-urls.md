---
title: Zulassen der Azure-Portal-URLs in Ihrer Firewall oder auf Ihrem Proxyserver
description: Zur Optimierung der Konnektivität zwischen Ihrem Netzwerk und dem Azure-Portal sowie dessen Diensten wird empfohlen, diese URLs Ihrer Positivliste hinzuzufügen.
ms.date: 06/21/2021
ms.topic: conceptual
ms.openlocfilehash: 70e2f55a381c38b0a4244f742b7b2c51e6ca81f6
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516359"
---
# <a name="allow-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Zulassen der Azure-Portal-URLs in Ihrer Firewall oder auf Ihrem Proxyserver

Zur Optimierung der Konnektivität zwischen Ihrem Netzwerk und dem Azure-Portal sowie dessen Diensten wird empfohlen, spezifische Azure-Portal-URLs Ihrer Positivliste hinzuzufügen. Dadurch können Sie die Leistung und Konnektivität zwischen Ihrem LAN (Local Area Network) oder WAN (Wide Area Network) und der Azure-Cloud verbessern.

Netzwerkadministratoren stellen häufig Proxyserver, Firewalls oder andere Geräte bereit, mit denen der Internetzugriff von Benutzern gesichert und gesteuert werden kann. Regeln, die Benutzer schützen sollen, können gerechtfertigten und geschäftsbezogenen Internetdatenverkehr blockieren oder verlangsamen. Dieser Datenverkehr umfasst die Kommunikation zwischen Ihnen und Azure über die hier aufgeführten URLs.

> [!TIP]
> Hilfe bei der Diagnose von Problemen mit Netzwerkverbindungen mit diesen Domänen erhalten Sie unter https://portal.azure.com/selfhelp.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure-Portal-URLs für Proxyumgehung

Die für das Azure-Portal zuzulassenden URL-Endpunkte gelten speziell für die Azure-Cloud, in der Ihre Organisation bereitgestellt ist. Um Datenverkehr zu diesen Endpunkten zuzulassen und Einschränkungen zu umgehen, wählen Sie Ihre Cloud aus, und fügen Sie dann die Liste der URLs Ihrem Proxyserver oder Ihrer Firewall hinzu. Obwohl Sie möglicherweise URLs für andere Microsoft-Produkte und -Dienste hinzufügen möchten, raten wir davon ab, zusätzlich zu den hier aufgeführten portalbezogenen URLs weitere URLs hinzuzufügen.

#### <a name="public-cloud"></a>[Öffentliche Cloud](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[US- Government Cloud](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[China-Government Cloud](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Der Datenverkehr für diese Endpunkte verwendet TCP-Standardports für HTTP (80) und HTTPS (443).

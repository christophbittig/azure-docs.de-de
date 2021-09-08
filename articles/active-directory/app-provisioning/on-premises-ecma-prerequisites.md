---
title: Voraussetzungen für den Azure AD-ECMA-Connectorhost
description: In diesem Artikel werden die Voraussetzungen und Hardwareanforderungen des Azure AD-ECMA-Connectorhosts beschrieben.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50bb95b5362902eed2ce297350e9fb2760530e22
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449571"
---
# <a name="prerequisites-for-the-azure-ad-ecma-connector-host"></a>Voraussetzungen für den Azure AD-ECMA-Connectorhost

>[!IMPORTANT]
> Die Vorschauversion zur lokalen Bereitstellung ist derzeit nur mit Einladung verfügbar. Um Zugriff auf die Funktion anzufordern, verwenden Sie das [Zugriffsanforderungsformular](https://aka.ms/onpremprovisioningpublicpreviewaccess). Wir öffnen die Vorschauversion im Rahmen der Vorbereitung auf die allgemeine Verfügbarkeit in den nächsten Monaten für weitere Kunden und Connectors.

Dieser Artikel behandelt die Voraussetzungen für die Verwendung des Azure AD ECMA-Connectorhosts.

Dieser Flow führt Sie durch die Installation und Konfiguration des Azure AD ECMA-Connectorhosts.

 ![Diagramm des Installationsflows.](./media/on-premises-ecma-prerequisites/flow-1.png)

Weitere Installations- und Konfigurationsinformationen finden Sie unter:

   - [Installation des Azure AD-ECMA-Connectorhosts](on-premises-ecma-install.md)
   - [Konfigurieren des Azure AD-ECMA-Connectorhosts und des Bereitstellungsagents](on-premises-ecma-configure.md)
   - [Konfigurieren eines generischen SQL-Connectors für den Azure AD-ECMA-Connectorhost](on-premises-sql-connector-configure.md)

## <a name="on-premises-prerequisites"></a>Lokale Voraussetzungen

 - Ein Zielsystem, z. B. eine SQL-Datenbank, in dem Benutzer erstellt, aktualisiert und gelöscht werden können.
 - Ein ECMA-Connector ab Version 2.0 für dieses Zielsystem, der Export-, Schemaabruf- und optional vollständige Import- oder Deltaimportvorgänge unterstützt. Wenn Sie während der Konfiguration nicht über einen ECMA-Connector verfügen, können Sie den End-to-End-Flow dennoch überprüfen, wenn Sie über eine SQL Server-Instanz in Ihrer Umgebung verfügen und den generischen SQL-Connector verwenden.
 - Ein Computer mit Windows Server 2016 oder höher, einer über das Internet zugänglichen TCP/IP-Adresse, Konnektivität mit dem Zielsystem und ausgehender Konnektivität mit login.microsoftonline.com. Ein Beispiel ist eine Windows Server 2016-VM, die in Azure IaaS oder hinter einem Proxy gehostet wird. Der Server sollte über mindestens 3 GB RAM verfügen.
 - Ein Computer mit .NET Framework 4.7.1

## <a name="cloud-requirements"></a>Cloudanforderungen

 - Ein Azure AD-Mandant mit Azure AD Premium P1 oder Premium P2 (oder EMS E3 oder E5). 
 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
 - Die Rolle „Hybridadministrator“ zum Konfigurieren des Bereitstellungs-Agents und die Rollen „Anwendungsadministrator“ oder „Cloudadministrator“ zum Konfigurieren der Bereitstellung im Azure-Portal.

## <a name="next-steps"></a>Nächste Schritte

- [Azure AD-ECMA-Connectorhost: Installation](on-premises-ecma-install.md)
- [Azure AD-ECMA-Connectorhost: Konfiguration](on-premises-ecma-configure.md)
- [Generischer SQL-Connector](on-premises-sql-connector-configure.md)
- [Tutorial: Generischer SQL-Connector mit ECMA-Connectorhost](tutorial-ecma-sql-connector.md)

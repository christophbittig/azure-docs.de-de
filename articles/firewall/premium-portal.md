---
title: Azure Firewall Premium im Azure-Portal
description: Enthält Informationen zu Azure Firewall Premium im Azure-Portal.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: cfb9f76751bbf5966b4aaa29e382a0946bf7cfd6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440428"
---
# <a name="azure-firewall-premium-in-the-azure-portal"></a>Azure Firewall Premium im Azure-Portal


 Azure Firewall Premium ist eine Firewall der nächsten Generation mit Funktionen, die für streng vertrauliche und regulierte Umgebungen erforderlich sind. Es umfasst die folgenden Features:

- **TLS-Inspektion** – Entschlüsselt den ausgehenden Datenverkehr, verarbeitet die Daten, verschlüsselt sie dann und sendet sie an das Ziel.
- **IDPS** – Ein System zur Erkennung und Verhinderung von Eindringversuchen in Netzwerke (IDPS) ermöglicht es Ihnen, Netzwerkaktivitäten auf schädliche Aktivitäten zu überwachen, Informationen über diese Aktivitäten zu protokollieren, sie zu melden und optional zu versuchen, sie zu blockieren.
- **URL-Filterung** – Erweitert die FQDN-Filterfunktion von Azure Firewall, um eine gesamte URL zu berücksichtigen. Beispiel: `www.contoso.com/a/c` anstelle von `www.contoso.com`.
- **Webkategorien** – Administratoren können den Benutzerzugriff auf Websitekategorien wie Gaming- oder Social Media-Websites zulassen oder verweigern.

Weitere Informationen finden Sie unter [Azure Firewall Premium-Features](premium-features.md).

## <a name="deploy-the-firewall"></a>Bereitstellen der Firewall

Die Bereitstellung von Azure Firewall Premium ähnelt der Bereitstellung von Azure Firewall Standard:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="Bereitstellung über das Portal":::

Für den **Firewalltarif** wählen Sie **Premium** und für die **Firewallrichtlinie** wählen Sie eine vorhandene Premium-Richtlinie aus oder erstellen eine neue.

## <a name="configure-the-premium-policy"></a>Konfigurieren der Premium-Richtlinie

Das Konfigurieren einer Premium-Firewallrichtlinie ähnelt dem Konfigurieren einer Standard-Firewallrichtlinie. Mit einer Premium-Richtlinie können Sie die Premium-Features konfigurieren:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Premium-Richtlinie: Bereitstellung":::

### <a name="rule-configuration"></a>Regelkonfiguration

Wenn Sie Anwendungsregeln in einer Premium-Richtlinie konfigurieren, können Sie zusätzliche Premium-Features konfigurieren:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Premium-Regel":::

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den Azure Firewall Premium-Features finden Sie unter [Bereitstellen und Konfigurieren von Azure Firewall Premium](premium-deploy.md).
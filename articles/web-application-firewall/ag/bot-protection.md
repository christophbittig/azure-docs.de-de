---
title: Konfigurieren von Bot-Schutz für Azure Web Application Firewall (WAF)
description: Erfahren Sie, wie der Bot-Schutz für Web Application Firewall (WAF) in Azure Application Gateway konfiguriert wird.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: a13784f722648f3639bcca7eece46d5128a689de
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344881"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway"></a>Konfigurieren des Bot-Schutzes für Web Application Firewall (WAF) in Azure Application Gateway

In diesem Artikel erfahren Sie, wie Sie eine Bot-Schutzregel in Azure Web Application Firewall (WAF) für Application Gateway über das Azure-Portal konfigurieren. 

Sie können einen verwalteten Bot-Schutzregelsatz für Ihre WAF aktivieren, um Anforderungen von IP-Adressen, die als schädlich bekannt sind, zu blockieren oder zu protokollieren. Die IP-Adressen stammen aus dem Microsoft Threat Intelligence-Feed. Der Intelligente Sicherheitsgraph ist die Grundlage für die Bedrohungsanalyse von Microsoft und wird von mehreren Diensten genutzt, darunter auch Microsoft Defender für Cloud.

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie eine WAF-Basisrichtlinie für Application Gateway, indem Sie die unter [Erstellen von Web Application Firewall-Richtlinien für Application Gateway](create-waf-policy-ag.md) beschriebenen Anleitungen befolgen.

## <a name="enable-bot-protection-rule-set"></a>Aktivieren des Bot-Schutzregelsatzes

1. Wählen Sie auf der Seite mit der **Basisrichtlinie**, die Sie zuvor erstellt haben, unter **Einstellungen** die Option **Regeln** aus.  

2. Aktivieren Sie auf der Detailseite unter dem Abschnitt  **Regeln verwalten**  aus dem Dropdownmenü das Kontrollkästchen für die Bot-Schutzregel, und wählen Sie dann **Speichern** aus.

> [!div class="mx-imgBorder"]
> ![Bot-Schutz](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu benutzerdefinierten Regeln finden Sie unter [Benutzerdefinierte Regeln für Web Application Firewall v2 in Azure Application Gateway](custom-waf-rules-overview.md).

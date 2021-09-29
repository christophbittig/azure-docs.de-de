---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Azure Web Application Firewall
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Azure Web Application Firewall (WAF) zum Schutz Ihrer Anwendungen vor böswilligen Angriffen
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 54cb0cdbff2ac11334f168e41a18107d3d0dfe87
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763216"
---
# <a name="tutorial-configure-azure-web-application-firewall-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von Azure Active Directory B2C mit Azure Web Application Firewall

In diesem Beispieltutorial erfahren Sie, wie Sie die Lösung [Azure Web Application Firewall (WAF)](https://azure.microsoft.com/services/web-application-firewall/#overview) für einen Azure Active Directory (AD) B2C-Mandanten mit einer benutzerdefinierten Domäne aktivieren. Azure WAF bietet zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken.

>[!NOTE]
>Dieses Feature befindet sich in der Phase der öffentlichen Vorschau.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement – Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

- [Ein Azure AD B2C-Mandant](tutorial-create-tenant.md) – Dabei handelt es sich um den Autorisierungsserver, der für die Überprüfung der Anmeldeinformationen des Benutzers gemäß den im Mandanten festgelegten benutzerdefinierten Richtlinien zuständig ist.  Er wird auch als Identitätsanbieter bezeichnet.

- [Azure Front Door](../frontdoor/index.yml) – zuständig für die Aktivierung benutzerdefinierter Domänen für Azure AD B2C-Mandanten.  

- [Azure WAF](https://azure.microsoft.com/services/web-application-firewall/#overview) – Verwaltet den gesamten Datenverkehr, der an den Autorisierungsserver gesendet wird.

## <a name="azure-ad-b2c-setup"></a>Einrichtung von Azure AD B2C

Um benutzerdefinierte Domänen in Azure AD B2C verwenden zu können, müssen Sie die von AFD bereitgestellte Funktion für benutzerdefinierte Domänen verwenden. Lesen Sie den Artikel [Aktivieren von benutzerdefinierten Domänen für Azure Active Directory B2C](./custom-domain.md?pivots=b2c-user-flow).  

Nachdem die benutzerdefinierte Domäne für Azure AD B2C erfolgreich mit AFD konfiguriert wurde, [testen Sie die benutzerdefinierte Domäne](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain), bevor Sie fortfahren.  

## <a name="onboard-with-azure-waf"></a>Onboarding mit Azure WAF

Um Azure WAF zu aktivieren, konfigurieren Sie eine WAF-Richtlinie, und ordnen Sie diese Richtlinie zu Schutzzwecken der AFD zu.

### <a name="create-a-waf-policy"></a>Erstellen einer WAF-Richtlinie

Erstellen Sie im [Azure-Portal](https://portal.azure.com) mithilfe des verwalteten Standardregelsatzes (DRS, Default Rule Set) eine einfache WAF-Richtlinie.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie **Ressource erstellen** aus, und suchen Sie dann nach Azure WAF. Wählen Sie **Azure Web Application Firewall (WAF)**  > **Erstellen**.

2. Rufen Sie die Seite **WAF-Richtlinie erstellen** auf und wählen Sie die Registerkarte **Grundlagen** aus. Geben Sie folgende Informationen ein und übernehmen Sie die Standardwerte für die übrigen Einstellungen.

| Wert | BESCHREIBUNG |
|:--------|:-------|
| Richtlinie für | Globale WAF (Front Door)|
| Front Door-SKU | Wählen Sie zwischen den SKUs „Basic“, „Standard“ oder „Premium“. |
|Subscription | Wählen Sie den Namen Ihres Front Door-Abonnements aus. |
| Resource group | Wählen Sie den Namen Ihrer Front Door-Ressourcengruppe aus. |
| Richtlinienname | Geben Sie einen eindeutigen Namen für Ihre WAF-Richtlinie ein. |
| Richtlinienstatus | Als „Aktiviert“ festlegen. |
| Richtlinienmodus | Als „Erkennung“ festlegen. |

3. Klicken Sie auf **Überprüfen + erstellen**.

4. Rufen Sie die Registerkarte **Zuordnung** auf der Seite „WAF-Richtlinie erstellen“ auf und wählen Sie die Option +**Ein Front Door-Profil zuordnen** aus. Geben Sie die folgenden Einstellungen ein.

| Wert | BESCHREIBUNG |
|:----|:------|
| Front Door | Wählen Sie Ihren Front Door-Namen aus, der der benutzerdefinierten Azure AD B2C-Domäne zugeordnet ist. |
| Domänen | Wählen Sie die benutzerdefinierten Azure AD B2C-Domänen aus, denen Sie die WAF-Richtlinie zuordnen möchten.|

5. Wählen Sie **Hinzufügen**.

6. Klicken Sie auf **Überprüfen + erstellen** und dann auf **Erstellen**.

### <a name="change-policy-mode-from-detection-to-prevention"></a>Ändern des Richtlinienmodus von Erkennung zu Prävention

Wenn Sie eine WAF-Richtlinie erstellen, befindet sich diese standardmäßig im Modus „Erkennung“. Im Modus „Erkennung“ blockiert WAF keine Anforderungen. Stattdessen werden Anforderungen, die den WAF-Regeln entsprechen, in WAF-Protokollen aufgezeichnet. Weitere Informationen zur WAF-Protokollierung finden Sie unter [Azure WAF-Überwachung und -protokollierung](../web-application-firewall/afds/waf-front-door-monitor.md).

Die Beispielabfrage zeigt alle Anforderungen, die in den letzten 24 Stunden durch die WAF-Richtlinie blockiert wurden. Die Details umfassen Regelname, Anforderungsdaten, von der Richtlinie ausgeführte Aktionen und den Richtlinienmodus.

![Abbildung der blockierten Anforderungen](./media/partner-azure-web-application-firewall/blocked-requests-query.png)

![Abbildung der Details blockierter Anforderungen](./media/partner-azure-web-application-firewall/blocked-requests-details.png)

Es wird empfohlen, dass Sie die WAF Anforderungen im Erkennungsmodus erfassen lassen. Überprüfen Sie die WAF-Protokolle, um zu ermitteln, ob die Richtlinie Regeln enthält, die False Positive-Ergebnisse verursachen. Anschließend [Schließen Sie die WAF-Regeln basierend auf den WAF-Protokollen aus](../web-application-firewall/afds/waf-front-door-exclusion.md#define-exclusion-based-on-web-application-firewall-logs).

Um WAF in Aktion zu sehen, wechseln Sie per „Zum Präventionsmodus wechseln“ vom Erkennungs- in den Präventionsmodus. Alle Anforderungen, die mit im Standardregelsatz definierten Regeln übereinstimmen, werden blockiert und in WAF-Protokollen aufgezeichnet.

![Abbildung des Wechsels in den Präventionsmodus](./media/partner-azure-web-application-firewall/switch-to-prevention-mode.png)

Falls Sie wieder in den Erkennungsmodus wechseln möchten, können Sie dazu die Option „Zum Erkennungsmodus wechseln“ verwenden.

![Abbildung des Wechsels in den Erkennungsmodus](./media/partner-azure-web-application-firewall/switch-to-detection-mode.png)

## <a name="next-steps"></a>Nächste Schritte

- [Azure WAF-Überwachung und -Protokollierung](../web-application-firewall/afds/waf-front-door-monitor.md)

- [WAF mit Front Door-Dienstausschlusslisten](../web-application-firewall/afds/waf-front-door-exclusion.md)
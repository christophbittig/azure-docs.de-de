---
title: Verwalten mehrerer Mandanten in Microsoft Sentinel als Dienstanbieter für verwaltete Sicherheit | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mehrere Mandanten in Microsoft Sentinel als Dienstanbieter für verwaltete Sicherheit (Managed Security Service Provider, MSSP) mithilfe von Azure Lighthouse verwalten und deren Onboarding durchführen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b2ae955210a230353cdc6af54c8e17b56388192
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517499"
---
# <a name="manage-multiple-tenants-in-microsoft-sentinel-as-an-mssp"></a>Verwalten mehrerer Mandanten in Microsoft Sentinel als MSSP

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie Dienstanbieter für verwaltete Sicherheit (MSSP) sind und [Azure Lighthouse](../lighthouse/overview.md) verwenden, um Ihren Kunden SOC-Dienste (Security Operations Center) anzubieten, können Sie die Microsoft Sentinel-Ressourcen Ihrer Kunden direkt von Ihrem eigenen Azure-Mandanten aus verwalten, ohne eine Verbindung mit dem Mandanten des Kunden herstellen zu müssen. 

## <a name="prerequisites"></a>Voraussetzungen

- [Onboarding von Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Damit dies ordnungsgemäß funktioniert, muss Ihr Mandant (der MSSP-Mandant) die Microsoft Sentinel-Ressourcenanbieter bei mindestens einem Abonnement registriert haben. Außerdem müssen die Ressourcenanbieter für jeden Mandanten Ihrer Kunden registriert sein. Wenn Sie Microsoft Sentinel in Ihrem Mandanten und Ihre Kunden dies in ihren Mandanten registriert haben, sind Sie bereit für die ersten Schritte. Führen Sie zum Überprüfen der Registrierung die folgenden Schritte aus:

    1. Wählen Sie im Azure-Portal **Abonnements** aus, und wählen Sie dann im Menü ein relevantes Abonnement aus.

    1. Wählen Sie im Navigationsmenü im Abonnementbildschirm unter **Einstellungen** die Option **Ressourcenanbieter** aus.

    1. Suchen Sie im Bildschirm ***Abonnementname* | Ressourcenanbieter** nach *Microsoft.OperationalInsights* und *Microsoft.SecurityInsights-* , wählen Sie diese aus, und überprüfen Sie die Spalte **Status**. Ist der Status des Anbieters *NotRegistered* (Nicht registriert), wählen Sie **Registrieren** aus.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Überprüfen von Ressourcenanbietern":::

## <a name="how-to-access-microsoft-sentinel-in-managed-tenants"></a>Zugreifen auf Microsoft Sentinel in verwalteten Mandanten

1. Wählen Sie unter **Verzeichnis + Abonnement** die delegierten Verzeichnisse (Verzeichnis = Mandant) und die Abonnements aus, in denen sich die Microsoft Sentinel-Arbeitsbereiche Ihres Kunden befinden.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Auswählen von Mandanten und Abonnements":::

1. Öffnen Sie Microsoft Sentinel. Es werden alle Arbeitsbereiche in den ausgewählten Abonnements angezeigt, und Sie können problemlos wie in jedem anderen Arbeitsbereich Ihres eigenen Mandanten damit arbeiten.

> [!NOTE]
> Sie können Connectors in Microsoft Sentinel nicht von einem verwalteten Arbeitsbereich aus bereitstellen. Um einen Connector bereitzustellen, müssen Sie sich direkt bei dem Mandanten anmelden, auf dem Sie einen Connector bereitstellen möchten, und sich dort mit den erforderlichen Berechtigungen authentifizieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie mehrere Microsoft Sentinel-Mandanten problemlos verwalten können. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](detect-threats-built-in.md).

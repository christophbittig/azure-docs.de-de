---
title: Verwalten mehrerer Mandanten in Azure Sentinel als Dienstanbieter für verwaltete Sicherheit | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mehrere Mandanten in Azure Sentinel als Dienstanbieter für verwaltete Sicherheit (Managed Security Service Provider, MSSP) mithilfe von Azure Lighthouse verwalten und deren Onboarding durchführen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3e5c431c8795f464e3ab83e0f6dff47028fdb5a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019778"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Verwalten mehrerer Mandanten in Azure Sentinel als MSSP

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie Dienstanbieter für verwaltete Sicherheit (MSSP) sind und [Azure Lighthouse](../lighthouse/overview.md) verwenden, um Ihren Kunden SOC-Dienste (Security Operations Center) anzubieten, können Sie die Azure Sentinel-Ressourcen Ihrer Kunden direkt von Ihrem eigenen Azure-Mandanten aus verwalten, ohne eine Verbindung mit dem Mandanten des Kunden herstellen zu müssen. 

## <a name="prerequisites"></a>Voraussetzungen

- [Onboarding von Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Damit dies ordnungsgemäß funktioniert, muss Ihr Mandant (der MSSP-Mandant) die Azure Sentinel-Ressourcenanbieter bei mindestens einem Abonnement registriert haben. Außerdem müssen die Ressourcenanbieter für jeden Mandanten Ihrer Kunden registriert sein. Wenn Sie Azure Sentinel in Ihrem Mandanten und Ihre Kunden dies in ihren Mandanten registriert haben, sind Sie bereit für die ersten Schritte. Führen Sie zum Überprüfen der Registrierung die folgenden Schritte aus:

    1. Wählen Sie im Azure-Portal **Abonnements** aus, und wählen Sie dann im Menü ein relevantes Abonnement aus.

    1. Wählen Sie im Navigationsmenü im Abonnementbildschirm unter **Einstellungen** die Option **Ressourcenanbieter** aus.

    1. Suchen Sie im Bildschirm ***Abonnementname* | Ressourcenanbieter** nach *Microsoft.OperationalInsights* und *Microsoft.SecurityInsights-* , wählen Sie diese aus, und überprüfen Sie die Spalte **Status**. Ist der Status des Anbieters *NotRegistered* (Nicht registriert), wählen Sie **Registrieren** aus.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Überprüfen von Ressourcenanbietern":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Zugreifen auf Azure Sentinel in verwalteten Mandanten

1. Wählen Sie unter **Verzeichnis + Abonnement** die delegierten Verzeichnisse (Verzeichnis = Mandant) und die Abonnements aus, in denen sich die Azure Sentinel-Arbeitsbereiche Ihres Kunden befinden.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Auswählen von Mandanten und Abonnements":::

1. Öffnen Sie Azure Sentinel. Es werden alle Arbeitsbereiche in den ausgewählten Abonnements angezeigt, und Sie können problemlos wie in jedem anderen Arbeitsbereich Ihres eigenen Mandanten damit arbeiten.

> [!NOTE]
> Sie können Connectors in Azure Sentinel nicht von einem verwalteten Arbeitsbereich aus bereitstellen. Um einen Connector bereitzustellen, müssen Sie sich direkt bei dem Mandanten anmelden, auf dem Sie einen Connector bereitstellen möchten, und sich dort mit den erforderlichen Berechtigungen authentifizieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie mehrere Azure Sentinel-Mandanten problemlos verwalten können. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).

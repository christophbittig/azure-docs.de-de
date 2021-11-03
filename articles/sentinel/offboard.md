---
title: Entfernen von Azure Sentinel | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Ihre Azure Sentinel-Instanz löschen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: afb17cf0cc999680d2cd60a0be8e4a227aa146bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009185"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Entfernen von Azure Sentinel aus Ihrem Arbeitsbereich

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie Azure Sentinel nicht mehr verwenden möchten, können Sie die Lösung mithilfe der Anleitung in diesem Artikel aus Ihrem Arbeitsbereich entfernen.

## <a name="how-to-remove-azure-sentinel"></a>Entfernen von Azure Sentinel

Gehen Sie folgendermaßen vor, um Azure Sentinel aus Ihrem Arbeitsbereich zu entfernen:

1. Wechseln Sie zu **Azure Sentinel** und dann zu **Einstellungen**, und wählen Sie die Registerkarte **Remove Azure Sentinel** (Azure Sentinel entfernen) aus.

1. Bevor Sie Azure Sentinel entfernen, teilen Sie uns bitte mithilfe der Kontrollkästchen mit, warum Sie es entfernen.

1. Wählen Sie **Remove Azure Sentinel from your workspace** (Azure Sentinel aus Ihrem Arbeitsbereich entfernen) aus.
    
    ![Löschen der Lösung „SecurityInsights“](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Was passiert im Hintergrund?

Wenn Sie die Lösung entfernen, dauert es bis zu 48 Stunden, bis die erste Phase des Löschvorgangs von Azure Sentinel abgeschlossen ist.

Nach Abschluss der Verbindungstrennung beginnt der Offboardingprozess.

**Die Konfiguration der folgenden Connectors wird entfernt:**
-   Office 365

-   AWS

-   Sicherheitsbenachrichtigungen von Microsoft-Diensten: Microsoft Defender for Identity (*ehemals Azure ATP*), Microsoft Cloud App Security mit Cloud Discovery-Schatten-IT-Berichterstellung, Azure AD Identity Protection, Microsoft Defender für Endpunkt (*ehemals Microsoft Defender ATP*), Azure Defender-Benachrichtigungen aus dem Azure Security Center

-   Threat Intelligence

-   Häufig verwendete Sicherheitsprotokolle (z. B. CEF-basierte Protokolle, Barracuda und Syslog) (Wenn Sie Azure Defender-Benachrichtigungen aus dem Azure Security Center erhalten, werden diese Protokolle weiterhin erfasst.)

-   Windows-Sicherheitsereignisse (Wenn Sie Azure Defender-Benachrichtigungen aus dem Azure Security Center erhalten, werden diese Protokolle weiterhin erfasst.)

Innerhalb der ersten 48 Stunden kann auf die Daten und Analyseregeln (einschließlich Automatisierungskonfiguration in Echtzeit) in Azure Sentinel nicht mehr zugegriffen werden, und sie können auch nicht mehr abgefragt werden.

**Nach 30 Tagen werden die folgenden Ressourcen entfernt:**

-   Incidents (einschließlich Untersuchungsmetadaten)

-   Analyseregeln

-   Lesezeichen

Ihre Playbooks, gespeicherten Arbeitsmappen, gespeicherten Suchabfragen und Notebooks werden nicht entfernt. **Aufgrund der entfernten Daten kann es in einigen Fällen zu Fehlern kommen. Diese können Sie manuell entfernen.**

Nach dem Entfernen des Diensts gilt eine Toleranzperiode von 30 Tagen, während der Sie die Lösung wieder aktivieren können. Ihre Daten und Analyseregeln werden dann automatisch wiederhergestellt, aber die Verbindungen für die konfigurierten Connectors, die getrennt wurden, müssen wiederhergestellt werden.

> [!NOTE]
> Wenn Sie die Lösung entfernen, ist Ihr Abonnement weiterhin beim Azure Sentinel-Ressourcenanbieter registriert. **Sie können dies manuell entfernen.**




## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie den Azure Sentinel-Dienst entfernen. Gehen Sie wie folgt vor, falls Sie Ihre Meinung ändern und den Dienst wieder installieren möchten:
- Führen Sie das [Onboarding für Azure Sentinel](quickstart-onboard.md) durch.

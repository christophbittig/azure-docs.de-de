---
title: Microsoft Sentinel entfernen | Microsoft-Dokumentation
description: Löschen Ihrer Microsoft Sentinel-Instanz
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e811cfb24505c00f521121ce846cc58da9cff2f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517347"
---
# <a name="remove-microsoft-sentinel-from-your-workspace"></a>Entfernen von Microsoft Sentinel aus Ihrem Arbeitsbereich

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn Sie Microsoft Sentinel nicht mehr verwenden möchten, können Sie die Lösung mithilfe der Anleitung in diesem Artikel aus Ihrem Arbeitsbereich entfernen.

## <a name="how-to-remove-microsoft-sentinel"></a>Entfernen von Microsoft Sentinel

Gehen Sie folgendermaßen vor, um Microsoft Sentinel aus Ihrem Arbeitsbereich zu entfernen:

1. Wechseln Sie zu **Microsoft Sentinel** und dann zu **Einstellungen**, und wählen Sie die Registerkarte **Microsoft Sentinel entfernen** aus.

1. Bevor Sie Microsoft Sentinel entfernen, teilen Sie uns bitte mithilfe der Kontrollkästchen mit, warum Sie es entfernen.

1. Wählen Sie **Entfernen von Microsoft Sentinel aus Ihrem Arbeitsbereich** aus.
    
    ![Löschen der Lösung „SecurityInsights“](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Was passiert im Hintergrund?

Wenn Sie die Lösung entfernen, dauert es bis zu 48 Stunden, bis die erste Phase des Löschvorgangs von Microsoft Sentinel abgeschlossen ist.

Nach Abschluss der Verbindungstrennung beginnt der Offboardingprozess.

**Die Konfiguration der folgenden Connectors wird entfernt:**
-   Office 365

-   AWS

-   Sicherheitsbenachrichtigungen von Microsoft-Diensten: Microsoft Defender for Identity *(ehemals Azure ATP),* Microsoft Defender für Cloud-Apps einschließlich Cloud Discovery Schatten-IT-Berichterstellung, Azure AD Identity Protection, Microsoft Defender für Endpunkt *(ehemals Microsoft Defender ATP),* Sicherheitswarnungen von Microsoft Defender für Cloud

-   Threat Intelligence

-   Häufig verwendete Sicherheitsprotokolle (z. B. CEF-basierte Protokolle, Barracuda und Syslog) (Wenn Sie Sicherheitswarnungen von Microsoft Defender für Cloud erhalten, werden diese Protokolle weiterhin erfasst.)

-   Windows-Sicherheitsereignisse (Wenn Sie Sicherheitswarnungen von Microsoft Defender für Cloud erhalten, werden diese Protokolle weiterhin erfasst.)

Innerhalb der ersten 48 Stunden kann auf die Daten und Analyseregeln (einschließlich Automatisierungskonfiguration in Echtzeit) in Microsoft Sentinel nicht mehr zugegriffen werden, und sie können auch nicht mehr abgefragt werden.

**Nach 30 Tagen werden die folgenden Ressourcen entfernt:**

-   Incidents (einschließlich Untersuchungsmetadaten)

-   Analyseregeln

-   Lesezeichen

Ihre Playbooks, gespeicherten Arbeitsmappen, gespeicherten Suchabfragen und Notebooks werden nicht entfernt. **Aufgrund der entfernten Daten kann es in einigen Fällen zu Fehlern kommen. Diese können Sie manuell entfernen.**

Nach dem Entfernen des Diensts gilt eine Toleranzperiode von 30 Tagen, während der Sie die Lösung wieder aktivieren können. Ihre Daten und Analyseregeln werden dann automatisch wiederhergestellt, aber die Verbindungen für die konfigurierten Connectors, die getrennt wurden, müssen wiederhergestellt werden.

> [!NOTE]
> Wenn Sie die Lösung entfernen, ist Ihr Abonnement weiterhin beim Microsoft Sentinel-Ressourcenanbieter registriert. **Sie können dies manuell entfernen.**




## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie den Microsoft Sentinel-Dienst entfernen. Gehen Sie wie folgt vor, falls Sie Ihre Meinung ändern und den Dienst wieder installieren möchten:
- Führen Sie das [Onboarding für Microsoft Sentinel](quickstart-onboard.md) durch.

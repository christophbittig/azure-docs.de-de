---
title: Sicherheitswarnungen für Umgebungen
description: In diesem Artikel erfahren Sie, wie Sie Sicherheitswarnungen für eine Umgebung in DevTest Labs anzeigen und eine entsprechende Aktion durchführen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 437117e29ac09e52d2cd15740d60d942170b9c0d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654193"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Sicherheitswarnungen für Umgebungen in Azure DevTest Labs
Als Labbenutzer können Sie jetzt Azure Security Center-Warnungen für Ihre Lab-Umgebungen anzeigen. Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von verbundenen Partnerlösungen, z.B. Lösungen zum Schutz von Firewalls und Endpunkten, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste mit priorisierten Sicherheitswarnungen wird im Security Center zusammen mit den Informationen angezeigt, die Sie zum schnellen Untersuchen des Problems benötigen. Außerdem sind Empfehlungen zum Reagieren auf einen Angriff vorhanden. [Weitere Informationen zu Sicherheitswarnungen in Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Voraussetzungen
Derzeit können Sie Sicherheitswarnungen nur für PaaS-Umgebungen (Platform-as-a-Service) anzeigen, die in Ihrem Lab bereitgestellt wurden. Um dieses Feature zu testen oder zu verwenden, [stellen Sie eine Umgebung in Ihrem Lab bereit](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Anzeigen von Sicherheitswarnungen für eine Umgebung

1. Wählen Sie auf der Homepage Ihres Labs im linken Menü **Sicherheitswarnungen** aus. Die Anzahl der Sicherheitswarnungen (hoch, mittel und niedrig) sollte angezeigt werden. Erfahren Sie mehr über die [Klassifizierung von Warnungen](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Sicherheitswarnungen – Übersicht](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Klicken Sie mit der rechten Maustaste auf die drei Punkte (...) in der letzten Spalte, und wählen Sie **Sicherheitswarnungen anzeigen** aus. 

    ![Screenshot, der die Seite mit den Sicherheitswarnungen mit ausgewählter Option „Sicherheitswarnungen anzeigen“ zeigt.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Es werden weitere Informationen zu den Warnungen sowie Ratgeberempfehlungen angezeigt. Erfahren Sie mehr über das [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Anzeigen von Sicherheitswarnungen](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Umgebungen finden Sie in den folgenden Artikeln:

- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
- [Konfigurieren und Verwenden von öffentlichen Umgebungen](devtest-lab-configure-use-public-environments.md)

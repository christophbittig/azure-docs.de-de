---
title: Aktivieren des integrierten Workloadschutzes von Microsoft Defender für Cloud
description: Es wird beschrieben, wie Sie erweiterte Sicherheitsfeatures aktivieren, um die Schutzmaßnahmen von Microsoft Defender für Cloud auf Ihre Hybrid- und Multi-Cloud-Ressourcen zu erweitern.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 06/07/2021
ms.openlocfilehash: 689fe8336594e26c283a75b5f4dfb0026c73ef32
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441874"
---
# <a name="quickstart-enable-enhanced-security-features"></a>Schnellstart: Aktivieren erweiterter Sicherheitsfeatures

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Informationen zu den Vorteilen der erweiterten Sicherheitsfeatures finden Sie unter [Erweiterte Sicherheitsfeatures von Microsoft Defender für Cloud](enhanced-security-features-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Schnellstarts und Tutorials von Defender für Cloud müssen Sie die erweiterten Sicherheitsfeatures aktivieren. 

Sie können mit den erweiterten Sicherheitsfeatures von Defender für Cloud ein gesamtes Azure-Abonnement schützen. Die Schutzmaßnahmen werden dann von allen Ressourcen innerhalb des Abonnements geerbt.

Sie können eine kostenlose 30-Tage-Testversion nutzen. Einzelheiten zu den Preisen in der von Ihnen gewählten Währung und je nach Region finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-enhanced-security-features-from-the-azure-portal"></a>Aktivieren der erweiterten Sicherheitsfeatures über das Azure-Portal

Wenn Sie alle Defender für Cloud-Features, einschließlich Bedrohungsschutz, aktivieren möchten, müssen Sie für das Abonnement mit den entsprechenden Workloads die erweiterten Sicherheitsfeatures aktivieren. Durch die Aktivierung auf der Arbeitsbereichsebene werden Just-In-Time-VM-Zugriff, adaptive Anwendungssteuerungen und Netzwerkerkennungen für Azure-Ressourcen nicht aktiviert. Darüber hinaus sind auf der Arbeitsbereichsebene nur die Microsoft Defender-Pläne „Microsoft Defender für Server“ und „Microsoft Defender für SQL Server-Instanzen auf Computern“ verfügbar.

- Sie können Konten vom Typ **Microsoft Defender für Speicher** entweder auf Abonnement- oder auf Ressourcenebene aktivieren.
- Sie können **Microsoft Defender für SQL** entweder auf Abonnement- oder auf Ressourcenebene aktivieren.
- Sie können **Microsoft Defender für relationale Open-Source-Datenbanken** nur auf Ressourcenebene aktivieren.

### <a name="to-enable-enhanced-security-features-on-your-subscriptions-and-workspaces"></a>Aktivieren Sie erweiterte Sicherheitsfeatures für Ihre Abonnements und Arbeitsbereiche wie folgt:

- Aktivieren Sie erweiterte Sicherheitsfeatures für nur ein Abonnement wie folgt:

    1. Wählen Sie im Hauptmenü von Defender für Cloud die Option **Umgebungseinstellungen** aus.
    1. Wählen Sie das Abonnement oder den Arbeitsbereich aus, das bzw. den Sie schützen möchten.
    1. Wählen Sie **Alle Microsoft Defender für Cloud-Pläne aktivieren** aus, um das Upgrade durchzuführen.
    1. Wählen Sie **Speichern** aus.

    > [!TIP]
    > Sie sehen, dass jeder Microsoft Defender-Plan separat abgerechnet wird und einzeln aktiviert oder deaktiviert werden kann. Beispielsweise kann es sinnvoll sein, Defender für App Service bei Abonnements zu deaktivieren, denen kein Azure App Service-Plan zugeordnet ist. 

    :::image type="content" source="./media/enhanced-security-features-overview/pricing-tier-page.png" alt-text="Seite „Preise“ für Defender für Cloud im Portal":::

- Aktivieren Sie die erhöhte Sicherheit für mehrere Abonnements oder Arbeitsbereiche wie folgt:

    1. Wählen Sie im Menü von Defender für Cloud die Option **Erste Schritte** aus.

        Auf der Registerkarte **Upgrade** werden für das Onboarding geeignete Abonnements und Arbeitsbereiche aufgeführt.

        :::image type="content" source="./media/enable-enhanced-security/get-started-upgrade-tab.png" alt-text="Registerkarte „Upgrade“ der Seite „Erste Schritte“"::: 

    1. Wählen Sie in der Liste **Abonnements und Arbeitsbereiche auswählen, die mit Microsoft Defender für Cloud geschützt werden sollen** die Abonnements und Arbeitsbereiche aus, für die das Upgrade durchgeführt werden soll, und dann die Option **Upgrade ausführen**, um alle Sicherheitsfeatures von Microsoft Defender für Cloud zu aktivieren.

       - Wenn Sie Abonnements und Arbeitsbereiche auswählen, die nicht für eine Testversion geeignet sind, wird für diese im nächsten Schritt ein Upgrade durchgeführt, und es fallen ab diesem Zeitpunkt Kosten an.
       - Wenn Sie einen Arbeitsbereich auswählen, der für eine kostenlose Testversion geeignet ist, wird im nächsten Schritt eine Testversion gestartet.

        :::image type="content" source="./media/enable-enhanced-security/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Aktualisieren aller ausgewählten Arbeitsbereiche und Abonnements über die Seite „Erste Schritte“":::


## <a name="disable-enhanced-security-features"></a>Aktivieren der erweiterten Sicherheitsfeatures

Wenn Sie die erweiterten Sicherheitsfeatures für ein Abonnement deaktivieren müssen, ist das Verfahren identisch, aber Sie wählen stattdessen die Option **Erhöhte Sicherheit deaktiviert** aus.
 
1. Öffnen Sie im Menü von Defender für Cloud **Umgebungseinstellungen**.
1. Wählen Sie das relevante Abonnement aus.
1. Wählen Sie **Defender-Pläne** und dann die Option **Erhöhte Sicherheit deaktiviert** aus.

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Aktivieren bzw. Deaktivieren der erweiterten Sicherheitsfeatures von Defender für Cloud":::

1. Wählen Sie **Speichern** aus.

> [!NOTE]
> Nachdem Sie die erweiterten Sicherheitsfeatures deaktiviert haben – unabhängig davon, ob Sie einen einzelnen Plan oder alle Pläne gleichzeitig deaktivieren –, wird die Datensammlung ggf. noch für kurze Zeit fortgesetzt. 

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die erweiterten Sicherheitsfeatures aktiviert haben, aktivieren Sie als Nächstes die erforderlichen Agents und Erweiterungen. Dies ist im Artikel zur [automatischen Bereitstellung von Agents und Erweiterungen](enable-data-collection.md) beschrieben.
---
title: Erweiterte Sicherheitsfeatures von Microsoft Defender für Cloud
description: Es wird beschrieben, wie Sie die erweiterten Sicherheitsfeatures von Microsoft Defender für Cloud aktivieren.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f194a0326f4caa90fc57bfd9af2991a9f4e1fbeb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029495"
---
# <a name="quickstart-set-up-microsoft-defender-for-cloud"></a>Schnellstart: Einrichten von Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender für Cloud verfügt über einheitliche Funktionen für die Sicherheitsverwaltung und den Bedrohungsschutz für Ihre Hybrid Cloud- und Multi-Cloud-Workloads. Die kostenlosen Features bieten lediglich eingeschränkte Sicherheit für Ihre Azure-Ressourcen. Durch die Aktivierung der erweiterten Sicherheitsfeatures können Sie diese Funktionen auf lokale Umgebungen und andere Clouds ausweiten. Defender für Cloud hilft Ihnen dabei, Sicherheitsrisiken zu ermitteln und zu beseitigen, Zugriffs- und Anwendungssteuerungen anzuwenden, um schädliche Aktivitäten zu blockieren, Bedrohungen mithilfe von Analysen und intelligenten Funktionen zu erkennen und bei Angriffen schnell zu reagieren. Sie können die erweiterten Sicherheitsfeatures kostenlos testen. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/).

In diesem Abschnitt der Schnellstartanleitung werden alle empfohlenen Schritte zum Aktivieren von Microsoft Defender für Cloud und der erweiterten Sicherheitsfeatures genau beschrieben. Wenn Sie alle Schnellstartschritte ausgeführt haben, gilt Folgendes:

> [!div class="checklist"]
> * Defender für Cloud ist für Ihre Azure-Abonnements aktiviert.
> * [Erweiterte Sicherheitsfeatures](enhanced-security-features-overview.md) sind für Ihre Azure-Abonnements aktiviert.
> * Die automatische Datensammlung ist eingerichtet.
> * [E-Mail-Benachrichtigungen](configure-email-notifications.md) für Sicherheitswarnungen sind eingerichtet.
> * Ihre Hybrid Cloud- und Multi-Cloud-Computer sind mit Azure verbunden.

## <a name="prerequisites"></a>Voraussetzungen
Für den Einstieg in Defender für Cloud benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.

Wenn Sie die erweiterten Sicherheitsfeatures für ein Abonnement aktivieren möchten, muss Ihnen die Rolle „Abonnementbesitzer“, „Mitwirkender des Abonnements“ oder „Sicherheitsadministrator“ zugewiesen sein.

## <a name="enable-defender-for-cloud-on-your-azure-subscription"></a>Aktivieren von Defender für Cloud für Ihr Azure-Abonnement

> [!TIP]
> Informationen zum Aktivieren von Defender für Cloud für alle Abonnements einer Verwaltungsgruppe finden Sie in [diesem Artikel](onboard-management-group.md).

1. Melden Sie sich beim [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) an.

1. Wählen Sie im Portal im Menü die Option **Defender für Cloud** aus. 

    Die Übersichtsseite von Defender für Cloud wird geöffnet.

    :::image type="content" source="./media/get-started/overview.png" alt-text="Dashboard mit Übersicht für Defender für Cloud" lightbox="./media/get-started/overview.png":::

    Unter **Defender für Cloud – Übersicht** haben Sie einen Gesamtüberblick über den Sicherheitsstatus Ihrer Hybrid Cloud-Workloads und können die Sicherheit Ihrer Workloads ermitteln und bewerten und Risiken identifizieren und mindern. Weitere Informationen finden Sie auf der [Übersichtsseite zu Microsoft Defender für Cloud](overview-page.md).

    Mit Defender für Cloud werden automatisch und kostenlos Ihre gesamten Azure-Abonnements aktiviert, für die zuvor von Ihnen oder einem anderen Abonnementbenutzer kein Onboarding durchgeführt wurde.

Sie können die Liste der Abonnements anzeigen und filtern, indem Sie das Menüelement **Abonnements** auswählen. Die Anzeige wird von Defender für Cloud angepasst, um den Sicherheitsstatus der ausgewählten Abonnements widerzuspiegeln. 

Innerhalb weniger Minuten nach dem ersten Start von Defender für Cloud wird unter Umständen Folgendes angezeigt:

- **Empfehlungen** zur Verbesserung der Sicherheit Ihrer verbundenen Ressourcen.
- Der Bestand Ihrer Ressourcen, die nun von Defender für Cloud bewertet werden, sowie der jeweilige Sicherheitsstatus.

Fahren Sie mit den nächsten Schnellstartschritten fort, damit Sie in den Genuss aller Vorteile von Defender für Cloud kommen.



## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie Defender für Cloud aktiviert. Im nächsten Schritt aktivieren Sie erweiterte Sicherheitsfeatures für die einheitliche Sicherheitsverwaltung und den Bedrohungsschutz Ihrer Hybrid Cloud-Workloads.

> [!div class="nextstepaction"]
> [Schnellstart: Aktivieren erweiterter Sicherheitsfeatures](enable-enhanced-security.md)

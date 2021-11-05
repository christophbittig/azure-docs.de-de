---
title: Schützen von Windows Admin Center-Servern mit Microsoft Defender für Cloud
description: In diesem Artikel wird erläutert, wie Sie Microsoft Defender für Cloud mit Windows Admin Center integrieren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c1c5af62c723d207b9eb5bdfc3c38fa57e9b4e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055937"
---
# <a name="protect-windows-admin-center-resources-with-microsoft-defender-for-cloud"></a>Schützen von Windows Admin Center-Ressourcen mit Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Windows Admin Center ist ein Verwaltungstool für Ihre Windows-Server. Es stellt den zentralen Ort dar, von dem aus Systemadministratoren auf die meisten der am häufigsten verwendeten Verwaltungstools zugreifen. Sie können direkt in Windows Admin Center das Onboarding in Microsoft Defender für Cloud für Ihre lokalen Server durchführen. Anschließend können Sie eine Zusammenfassung Ihrer Sicherheitsempfehlungen und -warnungen direkt auf der Benutzeroberfläche von Windows Admin Center anzeigen.

> [!NOTE]
> Für Ihr Azure-Abonnement und den zugehörigen Log Analytics-Arbeitsbereich müssen die erweiterten Sicherheitsfeatures von Microsoft Defender für Cloud aktiviert sein, damit die Integration mit Windows Admin Center aktiviert werden kann.
Erweiterte Sicherheitsfeatures sind die ersten 30 Tage lang kostenlos, sofern Sie sie noch nicht für das Abonnement und den Arbeitsbereich verwendet haben. Einzelheiten zu den Preisen in der von Ihnen gewählten Währung und je nach Region finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/).
>

Wenn Sie in Windows Admin Center das Onboarding eines Servers in Microsoft Defender für Cloud erfolgreich durchgeführt haben, können Sie folgende Aktionen ausführen:

* Anzeigen von Sicherheitswarnungen und -empfehlungen innerhalb der Security Center-Erweiterung in Windows Admin Center
* Anzeigen des Sicherheitsstatus und Abrufen weiterer detaillierter Informationen zu Ihren mit Windows Admin Center verwalteten Servern in Defender für Cloud im Azure-Portal (oder über eine API)

Durch die Kombination dieser beiden Tools wird Defender für Cloud zum zentralen Anzeigebereich für alle Ihre Sicherheitsinformationen – und zwar unabhängig von der Ressource. Dadurch werden sowohl Ihre lokalen, in Windows Admin Center verwalteten Server als auch Ihre virtuellen Computer und alle sonstigen PaaS-Workloads geschützt.

## <a name="onboard-windows-admin-center-managed-servers-into-defender-for-cloud"></a>Onboarding von in Windows Admin Center verwalteten Servern in Defender für Cloud

1. Wählen Sie in Windows Admin Center einen Ihrer Server und dann im Bereich **Tools** die Azure Security Center-Erweiterung aus:

    ![Azure Security Center-Erweiterung in Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Wenn das Onboarding des Servers in Defender für Cloud bereits durchgeführt wurde, wird das Einrichtungsfenster nicht angezeigt.

1. Klicken Sie auf **Sign in to Azure and set up** (Bei Azure anmelden und einrichten).
    ![Onboarding der Windows Admin Center-Erweiterung in Defender für Cloud](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Befolgen Sie die Anweisungen zum Verbinden Ihres Servers mit Defender für Cloud. Nachdem Sie die erforderlichen Informationen eingegeben und bestätigt haben, nimmt Defender für Cloud die erforderlichen Konfigurationsänderungen vor, um sicherzustellen, dass Folgendes gilt:
    * Ein Azure-Gateway ist registriert.
    * Der Server verfügt über einen Arbeitsbereich für Berichte und ein zugeordnetes Abonnement.
    * Die Log Analytics-Lösung von Defender für Cloud ist im Arbeitsbereich aktiviert. Diese Lösung bietet die Azure Defender-Features für *alle* Server und virtuellen Computer, die Berichte an diesen Arbeitsbereich übermitteln.
    * Microsoft Defender für Server ist für das Abonnement aktiviert.
    * Der Log Analytics-Agent ist auf dem Server installiert und so konfiguriert, dass er Berichte an den ausgewählten Arbeitsbereich übermittelt. Wenn der Server bereits Berichte an einen anderen Arbeitsbereich übermittelt, wird er so konfiguriert, dass er auch Berichte an den neu ausgewählten Arbeitsbereich übermittelt.

    > [!NOTE]
    > Es kann nach dem Onboarding einige Zeit dauern, bis Empfehlungen angezeigt werden. Tatsächlich kann es abhängig von Ihrer Serveraktivität auch vorkommen, dass Sie *überhaupt keine* Warnungen erhalten. Wenn Sie Testwarnungen generieren möchten, um die ordnungsgemäße Funktion Ihrer Warnungen zu testen, befolgen Sie die Anweisungen im [Verfahren zur Warnungsüberprüfung](alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Anzeigen von Sicherheitsempfehlungen und -warnungen in Windows Admin Center

Nach dem Onboarding können Sie Ihre Warnungen und Empfehlungen direkt im Azure Security Center-Bereich von Windows Admin Center anzeigen. Klicken Sie auf eine Empfehlung oder Warnung, um sie im Azure-Portal anzuzeigen. Dort erhalten Sie weitere Informationen und erfahren, wie Sie die Probleme korrigieren.

[![Defender für Cloud-Empfehlungen und -Warnungen in Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-defender-for-cloud"></a>Anzeigen von Sicherheitsempfehlungen und -warnungen für in Windows Admin Center verwaltete Server in Defender für Cloud
In Microsoft Defender für Cloud:

* Wenn Sie Sicherheitsempfehlungen für alle Windows Admin Center-Server anzeigen möchten, öffnen Sie [Ressourcenbestand](asset-inventory.md), und filtern Sie nach dem Computertyp, den Sie untersuchen möchten. Wählen Sie die Registerkarte **VMs und Computer** aus.

* Um Sicherheitswarnungen für alle Ihre Windows Admin Center-Server anzuzeigen, öffnen Sie **Sicherheitswarnungen**. Klicken Sie auf **Filter**, und vergewissern Sie sich, dass **nur** „Nicht-Azure“ ausgewählt ist:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtern von Sicherheitswarnungen für in Windows Admin Center verwaltete Server" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::

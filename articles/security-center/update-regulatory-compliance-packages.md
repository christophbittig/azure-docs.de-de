---
title: Dashboard für die Einhaltung gesetzlicher Bestimmungen in Microsoft Defender für Cloud
description: Hier erfahren Sie, wie Sie gesetzliche Standards auf dem Dashboard für die Einhaltung gesetzlicher Bestimmungen in Defender für Cloud hinzufügen und entfernen.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/05/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d5d54fe73c417f3d79c518ec1e78bade71135cfb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037383"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud vergleicht die Konfiguration Ihrer Ressourcen kontinuierlich mit den Anforderungen von Branchenstandards, Vorschriften und Benchmarks. Das **Dashboard für die Einhaltung gesetzlicher Bestimmungen** bietet Erkenntnisse zu Ihrem Compliancestatus basierend auf der Erfüllung bestimmter Complianceanforderungen.

> [!TIP]
> Weitere Informationen zum Dashboard für die Einhaltung gesetzlicher Bestimmungen in Defender für Cloud finden Sie unter den [häufig gestellten Fragen](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard).

## <a name="how-are-regulatory-compliance-standards-represented-in-defender-for-cloud"></a>Wie werden die Standards für die Einhaltung gesetzlicher Bestimmungen in Defender für Cloud dargestellt?

Branchenstandards, gesetzliche Standards und Benchmarks werden in Defender für Cloud im Dashboard für die Einhaltung gesetzlicher Bestimmungen dargestellt. Jeder Standard kann als eine in Azure Policy definierte Initiative verstanden werden.

Fügen Sie der Verwaltungsgruppe oder dem Abonnement auf der Seite **Sicherheitsrichtlinie** ein Compliancestandard hinzu, um Compliancedaten als zugeordnete Bewertungen in Ihrem Dashboard anzuzeigen. Weitere Informationen zu Azure Policy und Initiativen finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](tutorial-security-policy.md).

Nachdem Sie dem ausgewählten Bereich einen Standard oder Vergleichstest zugewiesen haben, wird der Standard in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen mit allen zugeordneten Compliancedaten in Form von Bewertungen angezeigt. Für alle Standards, die Sie zugewiesen haben, können Sie einen zusammenfassenden Bericht herunterladen.

Microsoft überwacht die gesetzlichen Standards selbst und passt die Pakete im Laufe der Zeit entsprechend an. Wenn Microsoft neue Inhalte für die Initiative veröffentlicht,werden diese in Ihrem Dashboard automatisch als neue Richtlinien, die Regeln im Standard entsprechen, hinzugefügt.


## <a name="what-regulatory-compliance-standards-are-available-in-defender-for-cloud"></a>Welche Standards für die Einhaltung gesetzlicher Bestimmungen sind in Defender für Cloud verfügbar?

Standardmäßig ist jedem Abonnement der **Azure Security-Vergleichstest** zugewiesen. Hierbei handelt es sich um von Microsoft erstellte, Azure-spezifische Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. [Weitere Informationen zum Azure-Sicherheitsvergleichstest](/security/benchmark/azure/introduction)

Sie können auch beispielsweise folgende Standards hinzufügen:

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official und UK NHS
- Canada Federal PBMM
- Azure CIS 1.3.0
- CMMC Level 3
- Durch New Zealand ISM eingeschränkt

Standards werden dem Dashboard hinzugefügt, sobald Sie verfügbar werden.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Hinzufügen eines gesetzlichen Standards in Ihrem Dashboard

In den folgenden Schritten wird erklärt, wie Sie ein Paket hinzufügen, um Ihre Compliance mit einem der unterstützten gesetzlichen Standards zu überwachen.

### <a name="prerequisites"></a>Voraussetzungen
Zum Hinzufügen von Standards in Ihrem Dashboard ist Folgendes erforderlich:

- Für das Abonnement müssen die erweiterten Sicherheitsfeatures von Defender für Cloud aktiviert sein.
- Der Benutzer muss über die Berechtigungen „Besitzer“ oder „Richtlinienmitwirkender“ verfügen.

### <a name="add-a-standard"></a>Hinzufügen eines Standards

1. Wählen Sie im Menü von Defender für Cloud die Option **Einhaltung gesetzlicher Bestimmungen** aus, um das Dashboard für die Einhaltung gesetzlicher Bestimmungen zu öffnen. Hier sehen Sie die Compliancestandards, die den aktuell ausgewählten Abonnements zugeordnet sind.   

1. Wählen Sie oben auf der Seite **Konformitätsrichtlinien verwalten** aus. Dadurch wird die Seite für die Richtlinienverwaltung geöffnet.

1. Wählen Sie das Abonnement oder die Verwaltungsgruppe aus, für das bzw. die Sie den Status der Einhaltung gesetzlicher Bestimmungen verwalten möchten. 

    > [!TIP]
    > Es wird empfohlen, den höchsten Bereich auszuwählen, für den der Standard gilt, damit Compliancedaten für alle geschachtelten Ressourcen aggregiert und nachverfolgt werden. 

1. Um die für Ihre Organisation relevanten Standards hinzuzufügen, erweitern Sie den Abschnitt **Branchen- und gesetzliche Standards**, und wählen Sie **Weitere Standards hinzufügen** aus.

1. Auf der Seite **Standards zur Einhaltung gesetzlicher Bestimmungen hinzufügen** können Sie nach verfügbaren Standards suchen, z. B.:

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO und UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC Level 3**
    - **Durch New Zealand ISM eingeschränkt**
    
    ![Hinzufügen gesetzlicher Standards im Dashboard für die Einhaltung gesetzlicher Bestimmungen in Microsoft Defender für Cloud](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Klicken Sie auf **Hinzufügen**, und geben Sie alle notwendigen Details für die jeweilige Initiative ein, z. B. Bereich, Parameter und Wartung.

1. Wählen Sie im Menü von Defender für Cloud erneut die Option **Einhaltung gesetzlicher Bestimmungen** aus, um zum Dashboard für die Einhaltung gesetzlicher Bestimmungen zurückzukehren.

    Ihr neuer Standard wird jetzt in der Liste der Branchen- und gesetzlichen Standards aufgeführt. 

    > [!NOTE]
    > Es kann einige Stunden dauern, bis ein neu hinzugefügter Standard im Compliance-Dashboard angezeigt wird.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard für die Einhaltung gesetzlicher Bestimmungen." lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Entfernen eines Standards aus Ihrem Dashboard

Falls einer der angegebenen gesetzlichen Standards für Ihre Organisation nicht relevant ist, ist es problemlos möglich, ihn aus der Benutzeroberfläche zu entfernen. Auf diese Weise können Sie das Dashboard für die Einhaltung gesetzlicher Bestimmungen weiter anpassen und nur die Standards nutzen, die für Sie zutreffen.

Entfernen Sie einen Standard wie folgt:

1. Wählen Sie im Menü von Defender für Cloud die Option **Sicherheitsrichtlinie** aus.

1. Wählen Sie das relevante Abonnement aus, aus dem Sie einen Standard entfernen möchten.

    > [!NOTE]
    > Sie können einen Standard aus einem Abonnement entfernen, aber nicht aus einer Verwaltungsgruppe. 

    Die Seite mit der Sicherheitsrichtlinie wird geöffnet. Für das ausgewählte Abonnement werden die Standardrichtlinie, die Branchen- und gesetzlichen Standards sowie alle von Ihnen erstellten benutzerdefinierten Initiativen angezeigt.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Entfernen eines gesetzlichen Standards aus Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen in Microsoft Defender für Cloud":::

1. Wählen Sie für den Standard, den Sie entfernen möchten, die Option **Deaktivieren** aus. Ein Bestätigungsfenster wird angezeigt.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Vergewissern, dass der ausgewählte gesetzliche Standard wirklich entfernt werden soll.":::

1. Wählen Sie **Ja**. Der Standard wird entfernt. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zum **Hinzufügen von Compliancestandards** zum Überwachen Ihrer Compliance mit gesetzlichen und Branchenstandards erhalten.

Verwandte Informationen finden Sie auf den folgenden Seiten:

- [Einführung zum Azure Security-Vergleichstest](/security/benchmark/azure/introduction)
- [Defender für Cloud-Dashboard für die Einhaltung gesetzlicher Bestimmungen](regulatory-compliance-dashboard.md) – Erfahren Sie, wie Sie Ihre Daten über die Einhaltung gesetzlicher Vorschriften mit Defender für Cloud und externen Tools verfolgen und exportieren.
- [Arbeiten mit Sicherheitsrichtlinien](tutorial-security-policy.md)

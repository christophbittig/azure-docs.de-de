---
title: Arbeiten mit Sicherheitsrichtlinien | Microsoft-Dokumentation
description: In diesem Artikel ist beschrieben, wie Sie mit Sicherheitsrichtlinien in Azure Security Center arbeiten.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: memildin
ms.openlocfilehash: b7c8003bd40df21fb749fcb576afeaec5dacead9
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236839"
---
# <a name="manage-security-policies"></a>Verwalten von Sicherheitsrichtlinien

In diesem Artikel wird beschrieben, wie Sicherheitsrichtlinien konfiguriert werden und wie Sie sie in Security Center anzeigen. 

Um Die Beziehungen zwischen Initiativen, Richtlinien und Empfehlungen zu verstehen, lesen Sie [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md)

## <a name="who-can-edit-security-policies"></a>Wer kann Sicherheitsrichtlinien bearbeiten?

Security Center verwendet die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC). Hierbei werden integrierte Rollen bereitgestellt, die Sie Azure-Benutzern, -Gruppen und -Diensten zuweisen können. In Security Center werden den Benutzern nur Informationen zu den Ressourcen angezeigt, auf die sie Zugriff haben. Dies bedeutet, dass Benutzern die Rolle *Besitzer*, *Mitwirkender* oder *Leser* für das Abonnement der Ressource zugewiesen wird. Darüber hinaus gibt es noch zwei spezifische Security Center-Rollen:

- **Sicherheitsleseberechtigter**: Verfügt über Rechte zum Anzeigen von Security Center-Elementen, z. B. Empfehlungen, Warnungen, Richtlinie und Integrität. Änderungen können nicht vorgenommen werden.
- **Sicherheitsadministrator**: Verfügt über die gleichen Rechte wie *Sicherheitsleseberechtigter*. Kann außerdem die Sicherheitsrichtlinie aktualisieren und Warnungen schließen.

Sie können Sicherheitsrichtlinien über das Azure Policy-Portal, über die REST-API oder über Windows PowerShell bearbeiten.

## <a name="manage-your-security-policies"></a>Verwalten der Sicherheitsrichtlinien

Zeigen Sie Ihre Sicherheitsrichtlinien in Security Center wie folgt an:

1. Wählen Sie im **Security Center**-Dashboard die Option **Sicherheitsrichtlinie**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="Seite „Richtlinienverwaltung“":::

   Auf dem Bildschirm **Richtlinienverwaltung** können Sie die Anzahl von Verwaltungsgruppen, Abonnements und Arbeitsbereichen sowie Ihre Verwaltungsgruppenstruktur anzeigen.

1. Wählen Sie das Abonnement oder die Verwaltungsgruppe aus, zu dem bzw. der Sie die Richtlinien anzeigen möchten.

1. Die Seite „Sicherheitsrichtlinie“ für dieses Abonnement bzw. diese Verwaltungsgruppe wird angezeigt. Sie enthält die verfügbaren und die zugewiesenen Richtlinien.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Seite mit Sicherheitsrichtlinien von Security Center" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Wenn neben Ihrer Standardrichtlinie die Bezeichnung „Verwaltungsgruppe geerbt“ steht, bedeutet dies, dass die Richtlinie einer Verwaltungsgruppe zugewiesen und vom angezeigten Abonnement geerbt wurde.

1. Wählen Sie in den verfügbaren Optionen auf dieser Seite aus:

    1. Wenn Sie mit Branchenstandards arbeiten möchten, wählen Sie **Weitere Standards hinzufügen** aus. Weitere Informationen finden Sie unter [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md).

    1. Um benutzerdefinierte Initiativen zuzuweisen und zu verwalten, wählen Sie **Benutzerdefinierte Initiativen hinzufügen** aus. Weitere Informationen finden Sie unter [Verwenden von benutzerdefinierten Sicherheitsinitiativen und -richtlinien](custom-security-policies.md).

    1. Zum Anzeigen und Bearbeiten der Standardinitiative wählen Sie **Effektive Richtlinie anzeigen** aus, und fahren wie unten beschrieben fort. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Bildschirm mit der effektiven Richtlinie":::

       Auf diesem Bildschirm **Sicherheitsrichtlinie** wird die Aktion der Richtlinien widergespiegelt, die dem von Ihnen ausgewählten Abonnement oder der Verwaltungsgruppe zugewiesen sind.
       
       * Verwenden Sie die oben angegebenen Links, um eine **Richtlinienzuweisung** zu öffnen, die für das Abonnement bzw. die Verwaltungsgruppe gilt. Mit diesen Links können Sie auf die Zuweisung zuzugreifen und die Richtlinie bearbeiten oder deaktivieren. Wenn Sie beispielsweise feststellen, dass eine bestimmte Richtlinienzuweisung den Endpunktschutz verhindert, können Sie die Richtlinie über den Link bearbeiten oder deaktivieren.
       
       * In der Liste mit den Richtlinien können Sie die aktive Anwendung der Richtlinie auf Ihr Abonnement oder die Verwaltungsgruppe anzeigen. Die Einstellungen der einzelnen Richtlinien, die für den Bereich gelten, werden berücksichtigt, und das kumulierte Ergebnis der von der Richtlinie durchgeführten Aktionen wird angezeigt. Wenn die Richtlinie beispielsweise in einer Zuweisung deaktiviert wird, aber in einer anderen auf „AuditIfNotExist“ festgelegt ist, wird aufgrund der Kumulation „AuditIfNotExist“ angewandt. Die aktivere Auswirkung hat immer Vorrang.
       
       * Für die Richtlinien können sich die folgenden Auswirkungen ergeben: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Weitere Informationen zur Anwendung von Auswirkungen finden Sie unter [Grundlegendes zu Richtlinienauswirkungen](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Beim Anzeigen von zugewiesenen Richtlinien können Sie mehrere Zuweisungen sehen und die jeweilige Konfiguration der Zuweisungen auch einzeln prüfen.


## <a name="disable-security-policies-and-disable-recommendations"></a>Deaktivieren von Sicherheitsrichtlinien und Empfehlungen

Wenn von Ihrer Sicherheitsinitiative eine Empfehlung ausgelöst wird, die für Ihre Umgebung irrelevant ist, können Sie verhindern, dass die Empfehlung erneut angezeigt wird. Deaktivieren Sie die spezifische Richtlinie, von der die Empfehlung generiert wird, um eine Empfehlung zu deaktivieren.

Die Empfehlung, die Sie deaktivieren möchten, wird weiterhin angezeigt, wenn sie für einen gesetzlichen Standard erforderlich ist, den Sie mit den Security Center-Tools zur Einhaltung gesetzlicher Bestimmungen angewendet haben. Auch wenn Sie eine Richtlinie in der integrierten Initiative deaktiviert haben, wird die Empfehlung von einer Richtlinie in der Initiative zum gesetzlichen Standard weiterhin ausgelöst, falls dies aus Compliancegründen erforderlich ist. Sie können Richtlinien aus Initiativen für gesetzliche Standards nicht deaktivieren.

Weitere Informationen zu Empfehlungen finden Sie unter [Verwalten von Sicherheitsempfehlungen](security-center-recommendations.md).

1. Wählen Sie in Security Center im Abschnitt **Richtlinie und Konformität** die Option **Sicherheitsrichtlinie** aus.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Starten des Richtlinienverwaltungsprozesses in Azure Security Center":::

1. Wählen Sie das Abonnement oder die Verwaltungsgruppe aus, für das/die Sie die Empfehlung (und Richtlinie) deaktivieren möchten.

   > [!NOTE]
   > Denken Sie daran, dass eine Verwaltungsgruppe ihre Richtlinien auf ihre Abonnements anwendet. Wenn Sie die Richtlinie eines Abonnements deaktivieren und es zu einer Verwaltungsgruppe gehört, in der dieselbe Richtlinie weiterhin angewendet wird, erhalten Sie auch weiterhin die Empfehlungen für die Richtlinie. Die Richtlinie wird immer noch von der Verwaltungsebene aus angewandt, und die Empfehlungen werden immer noch generiert.

1. Wählen Sie in den Abschnitten **Security Center-Standardrichtlinie**, **Branchen- und gesetzliche Standards** oder **Ihre benutzerdefinierten Initiativen** die relevante Initiative aus, die die Richtlinie enthält, die Sie deaktivieren möchten.

1. Suchen Sie im Abschnitt **Parameter** die Richtlinie, die die zu Empfehlung aufruft, die Sie deaktivieren möchten.

1. Ändern Sie in der Dropdownliste den Wert für die entsprechende Richtlinie in **Disabled**.

   ![Deaktivieren der Richtlinie](./media/tutorial-security-policy/disable-policy.png)

1. Wählen Sie **Speichern** aus.

   > [!NOTE]
   > Es kann bis zu 12 Stunden dauern, bis die Änderung in Kraft tritt.


## <a name="enable-a-security-policy"></a>Aktivieren einer Sicherheitsrichtlinie

Einige Richtlinien in Ihren Initiativen sind möglicherweise standardmäßig deaktiviert. In der Initiative „Vergleichstest für die Azure-Sicherheit“ werden z. B. einige Richtlinien bereitgestellt, die Sie nur aktivieren können, wenn sie bestimmte gesetzliche oder Complianceanforderungen für Ihre Organisation erfüllen. Zu diesen Richtlinien gehören Empfehlungen zum Verschlüsseln ruhender Daten mit vom Kunden verwalteten Schlüsseln, z. B. „Containerregistrierungen müssen mit einem vom Kunden verwalteten Schlüssel (CMK) verschlüsselt werden“.

So aktivieren Sie eine deaktivierte Richtlinie und stellen sicher, dass sie für Ihre Ressourcen bewertet wird:

1. Wählen Sie in Security Center im Abschnitt **Richtlinie und Konformität** die Option **Sicherheitsrichtlinie** aus.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Starten des Richtlinienverwaltungsprozesses in Azure Security Center":::

1. Wählen Sie das Abonnement oder die Verwaltungsgruppe aus, für das/die Sie die Empfehlung (und Richtlinie) aktivieren möchten.

1. Wählen Sie in den Abschnitten **Security Center-Standardrichtlinie**, **Branchen- und gesetzliche Standards** oder **Ihre benutzerdefinierten Initiativen** die relevante Initiative mit der Richtlinie aus, die Sie aktivieren möchten.

1. Suchen Sie im Abschnitt **Parameter** die Richtlinie, die die zu Empfehlung aufruft, die Sie deaktivieren möchten.

1. Ändern Sie in der Dropdownliste den Wert für die entsprechende Richtlinie in **AuditIfNotExists** oder **Enforce**.

1. Wählen Sie **Speichern** aus.

   > [!NOTE]
   > Es kann bis zu 12 Stunden dauern, bis die Änderung in Kraft tritt.


## <a name="next-steps"></a>Nächste Schritte
Auf dieser Seite werden die Sicherheitsrichtlinien beschrieben. Zugehörige Informationen finden Sie auf den folgenden Seiten:

- [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mit Azure PowerShell](../governance/policy/assign-policy-powershell.md)
- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)
- [Weitere Informationen dazu, wie Sie eine Richtlinie für mehrere Abonnements oder Verwaltungsgruppen mit Azure Policy festlegen](../governance/policy/overview.md)
- [Aktivieren von Security Center in allen Abonnements einer Verwaltungsgruppe](onboard-management-group.md)
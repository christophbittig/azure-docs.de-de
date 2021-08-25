---
title: Aktivieren Sie Azure Site Recovery für Ihre VMs mit Azure Policy
description: Erfahren Sie, wie Sie die Richtlinienunterstützung aktivieren, um Ihre VMs mithilfe von Azure Site Recovery zu schützen.
author: rishjai-msft
ms.author: rishjai
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1c936df9ffb467d732e0c07651e7c6fb31f28b5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346664"
---
# <a name="using-policy-with-azure-site-recovery-public-preview"></a>Verwendung von Policy mit Azure Site Recovery (Public Preview)

In diesem Artikel wird beschrieben, wie Sie [Azure Site Recovery](./site-recovery-overview.md) für Ihre Ressourcen mithilfe von Azure Policy einrichten. [Azure Policy](../governance/policy/overview.md) hilft dabei, bestimmte Geschäftsregeln für Ihre Azure-Ressourcen durchzusetzen und die Konformität dieser Ressourcen zu bewerten.

## <a name="disaster-recovery-with-azure-policy"></a>Notfallwiederherstellung mit Azure Policy
Site Recovery hilft Ihnen, Ihre Anwendungen im Falle geplanter oder ungeplanter zonaler/regionaler Ausfälle betriebsbereit zu halten. Es kann eine Herausforderung sein, Site Recovery auf Ihren Maschinen in großem Umfang über das Azure-Portal zu aktivieren. Nun haben Sie die Möglichkeit, Site Recovery über das Portal für bestimmte Ressourcengruppen _(Bereich_ der Policy) zu aktivieren.

Azure Policy löst dieses Problem. Sobald Sie eine Notfallwiederherstellungsrichtlinie für eine Ressourcengruppe erstellt haben, werden alle neuen virtuellen Maschinen, die der Ressourcengruppe hinzugefügt werden, automatisch für Site Recovery aktiviert. Darüber hinaus können Sie für alle virtuellen Maschinen, die bereits in der Ressourcengruppe vorhanden sind, Site Recovery über einen Prozess namens _Wiederherstellung_ aktiviert werden (Details unten).

>[!NOTE]
>Der _Bereich_ dieser Richtlinie sollte auf der Ressourcengruppenebene liegen.

## <a name="prerequisites"></a>Voraussetzungen

- Informationen zum Zuweisen einer Richtlinie finden Sie [hier.](../governance/policy/assign-policy-portal.md)
- Weitere Informationen über die Architektur von Azure zu Azure Notfallwiederherstellung [hier](./azure-to-azure-architecture.md).
- Lesen Sie die Unterstützungsmatrix für Azure Site Recovery Richtlinienunterstützung:

**Szenario** | **Supporthinweis**
--- | ---
Managed Disks | Unterstützt
Unmanaged Disks  | Nicht unterstützt
Multiple Disks | Unterstützt
Verfügbarkeitsgruppen | Unterstützt
Verfügbarkeitszonen | Unterstützt
Azure Disk Encryption (ADE) aktivierte VMs | Nicht unterstützt
Näherungsplatzierungsgruppen (PPG) | Unterstützt
Kundenverwaltete Schlüssel (CMK) aktivierte Disks | Nicht unterstützt
Überblick über direkte Speicherplätze S2D-Gruppen | Nicht unterstützt
Azure Resource Manager-Bereitstellungsmodell | Unterstützt
Klassisches Bereitstellungsmodell | Nicht unterstützt
Zone zu Zone DR  | Unterstützt
Interoperabilität mit anderen Richtlinien, die standardmäßig von Azure angewendet werden (sofern verfügbar) | Unterstützt

>[!NOTE]
>In den folgenden Fällen wird Site Recovery für diese nicht aktiviert. Allerdings werden sie in „Ressourcencompliance“ als _Nicht konform_ angezeigt:
>1. Wenn ein nicht unterstützter virtueller Computer innerhalb des Geltungsbereichs der Richtlinie erstellt wird.
>1. Wenn ein virtueller Computer sowohl Teil einer Verfügbarkeitsgruppe als auch einer PPG ist.

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung
In diesem Abschnitt erstellen Sie eine Richtlinienzuweisung, die Azure Site Recovery für alle neu erstellten Ressourcen aktiviert.
1. Navigieren Sie zum **Azure Portal** und anschließend zu **Azure Policy**
1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Zuweisungen**. Eine Zuweisung ist eine zugewiesene Richtlinie, die innerhalb eines bestimmten Bereichs angewendet werden soll.
   :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assignments.png" alt-text="Screenshot: Auswählen der Seite „Zuweisungen“ auf der Übersichtsseite für Richtlinien" border="false":::

1. Wählen Sie im oberen Bereich der Seite **Richtlinien – Zuweisungen** die Option **Richtlinie zuweisen**.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assign-policy.png" alt-text="Screenshot: Auswählen von „Richtlinie zuweisen“ auf der Seite „Zuweisungen“" border="false":::

1. Auf der Seite **Richtlinie zuweisen** stellen Sie den **Bereich** ein, durch Auswahl der Ellipse und Auswahl eines Abonnements und anschließend einer Ressourcengruppe. Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Verwenden Sie anschließend unten auf der Seite **Bereich** die Schaltfläche **Auswählen** aus.

1. Starten Sie _die Richtliniendefinitionsauswahl,_ indem Sie die Ellipse neben **Richtliniendefinition auswählen.** _Suchen Sie nach "Konfigurieren der Notfallwiederherstellung auf virtuellen Maschinen durch Aktivieren der Replikation",_ und wählen Sie die Richtlinie aus.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-policy-definition.png" alt-text="Screenshot der Auswahl von &quot;Richtliniendefinition&quot; auf der Grundlagenseite." border="true":::

1. Der **Zuweisungsname** wird automatisch mit dem ausgewählten Richtliniennamen gefüllt, kann aber geändert werden. Es kann hilfreich sein, wenn Sie mehrere Richtlinien Azure Site Recovery demselben Bereich zuweisen möchten.

1. Wählen **Sie Weiter** aus, Azure Site Recovery Eigenschaften für die Policy zu konfigurieren.

## <a name="configure-target-settings-and-properties"></a>Konfigurieren von Zieleinstellungen und -eigenschaften
Sie sind dabei, eine Policy zu erstellen, um Azure Site Recovery zu aktivieren. Konfigurieren wir nun die Zieleinstellungen und -eigenschaften:
1. Sie befinden sich im Abschnitt _Parameter_ des Workflows _Policy zuweisen_, der folgendermaßen aussieht: :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/specify-parameters.png" alt-text="Screenshot der Einstellung von Parametern auf der Seite Parameter." border="true":::
1. Wählen Sie die entsprechenden Werte für diese Parameter aus:
    - **Quellregion:** Die Quellregion der virtuellen Maschinen, für die die Richtlinie gilt.
    >[!NOTE]
    >Die Richtlinie gilt für alle virtuellen Maschinen, die zur Quellregion im Bereich der Policy gehören. Virtuelle Maschinen, die sich nicht in der Quellregion befinden, werden nicht im _Ressourcen-Compliance_ berücksichtigt.
    - **Zielregion**: Der Ort, an dem die Daten Ihrer virtuellen Quellmaschine repliziert werden. Site Recovery enthält die Liste der Zielregionen, in die der Kunde replizieren kann. Es empfiehlt sich, denselben Standort wie für den Recovery Services-Tresor zu verwenden.
    - **Zielressourcengruppe:** Die Ressourcengruppe, zu der alle Ihre replizierten virtuellen Maschinen gehören. Site Recovery erstellt standardmäßig in der Zielregion eine neue Ressourcengruppe.
    - **Vault Resource Group**: Die Ressourcengruppe, in der Recovery Services Vault vorhanden ist.
    - **Recovery Services Vault**: Die Vault, mit dem alle VMs des Bereichs geschützt werden. Azure Policy kann bei Bedarf einen neuen Tresor in Ihrem Namen erstellen.
    - **Recovery Virtual Network**: Wählen Sie ein vorhandenes virtuelles Netzwerk in der Zielregion, das als virtuelle Maschine für die Wiederherstellung verwendet werden soll. Azure Policy kann bei Bedarf auch ein neues virtuelles Netzwerk für Sie erstellen.
    - **Target Availability Zone**: Geben Sie die Verfügbarkeitszone der Zielregion ein, in der die virtuelle Maschine ausfallen werden soll.
    >[!NOTE]
    >Für das Zone-zu-Zone-Szenario wählen Sie dieselbe Zielregion wie die Quellregion und entscheiden Sie sich für eine andere Availability Zone in _Target Availability Zone_.     
    >Sollten sich einige der virtuellen Maschinen in Ihrer Ressourcengruppe bereits in der angestrebten Verfügbarkeitszone befinden, dann wird die Richtlinie nicht auf sie angewendet, sollten Sie Zone zu Zone DR einrichten.
    - **Auswirkung:** Aktivieren oder deaktivieren Sie die Ausführung der Richtlinie. Wählen Sie _DeployIfNotExists_ aus, um die Richtlinie zu aktivieren, sobald sie erstellt wird.

1. Wählen Sie **Weiter**, um sich für einen Wartungstask zu entscheiden.

## <a name="remediation-and-other-properties"></a>Wartung und andere Eigenschaften
1. Die Zieleigenschaften für Azure Site Recovery wurden konfiguriert. Diese Richtlinie wird jedoch nur für neu erstellte virtuelle Maschinen im Bereich der Richtlinie wirksam. Sie kann über einen Wartungstask auf vorhandene Ressourcen angewendet werden, nachdem die Richtlinie zugewiesen wurde. Sie können hier einen Wartungstask erstellen, indem Sie das Kontrollkästchen _Wartungstask erstellen_ aktivieren.

1. Azure Policy erstellt eine [Verwaltete Identität](../governance/policy/how-to/remediate-resources.md),die über Eigentümerrechte verfügt, um Azure Site Recovery für die Ressourcen im Bereich zu aktivieren.

1. Sie können eine benutzerdefinierte Non-Compliance-Meldung für die Richtlinie auf der Registerkarte _Non-Compliance-Meldungen_ konfigurieren.

1. Wählen Sie unten auf der Seite die Option Weiter oder oben die Option _Überprüfen + erstellen_ aus, um zum nächsten Teil des Assistenten für die Zuweisung zu wechseln.

1. Überprüfen Sie die ausgewählten Optionen, und wählen Sie anschließend am unteren Rand der Seite die Option _Erstellen_ aus.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](site-recovery-test-failover-to-azure.md) über die Ausführung von Testfailovervorgängen.

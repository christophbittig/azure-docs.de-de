---
title: 'Tutorial: Neue Richtlinienzuweisung per Azure-Portal'
description: In diesem Tutorial erstellen Sie über das Azure-Portal eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: be7752b5ccf73269b1e4715fc05c85162b4ea546
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770341"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Tutorial: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln. Azure Policy unterstützt die Überwachung des Zustands Ihres Servers mit Arc-Unterstützung mithilfe von Gastkonfigurationsrichtlinien. Die Gastkonfigurationsdefinitionen von Azure Policy können Einstellungen auf dem Computer überwachen oder anwenden. In diesem Tutorial durchlaufen Sie den Prozess zum Erstellen und Zuweisen einer Richtlinie und identifizieren, auf welchen Ihrer Server mit Arc-Unterstützung der Log Analytics-Agent nicht installiert ist.

Am Ende dieses Prozesses können Sie erfolgreich Computer identifizieren, für die der Log Analytics-Agent für Windows oder Linux nicht installiert ist. Sie sind mit der Richtlinienzuweisung _nicht konform_.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In diesem Tutorial erstellen Sie eine Richtlinienzuweisung und weisen die folgende Richtliniendefinition zu: _\[Preview]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Linux installiert sein_.

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Richtlinie** suchen und die entsprechende Option auswählen.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Suchen nach „Policy“ unter „Alle Dienste“" border="false":::

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Zuweisungen**. Eine Zuweisung ist eine zugewiesene Richtlinie, die innerhalb eines bestimmten Bereichs angewendet werden soll.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Auswählen von „Zuweisungen“ auf der Übersichtsseite von Policy" border="false":::

1. Wählen Sie im oberen Bereich der Seite **Richtlinien – Zuweisungen** die Option **Richtlinie zuweisen**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Zuweisen einer Richtliniendefinition auf der Seite „Zuweisungen“" border="false":::

1. Wählen Sie auf der Seite **Richtlinie zuweisen** den **Bereich** aus, indem Sie auf die Auslassungspunkte klicken und entweder eine Verwaltungsgruppe oder ein Abonnement auswählen. Wählen Sie optional eine Ressourcengruppe aus. Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Klicken Sie dann unten im Abschnitt der Seite **Bereich** auf **Auswählen**.

   In diesem Beispiel wird das Abonnement **Parnell Aerospace** verwendet. Ihr Abonnement wird sich davon unterscheiden.

1. Ressourcen können basierend auf dem **Bereich** ausgeschlossen werden. **Ausschlüsse** beginnen auf einer Ebene unterhalb der Ebene des **Bereichs**. **Ausschlüsse** sind optional, lassen Sie sie daher vorerst leer.

1. Wählen Sie die **Richtliniendefinition** mit den Auslassungspunkten, um die Liste der verfügbaren Definitionen zu öffnen. Azure Policy umfasst integrierte Richtliniendefinitionen, die Sie verwenden können. Zu den vielen verfügbaren Definitionen zählen z.B.:

   - Enforce tag and its value (Tag und zugehörigen Wert erzwingen)
   - Apply tag and its value (Tag und zugehörigen Wert anwenden)
   - Tag von der Ressourcengruppe erben, falls nicht vorhanden

   Eine Liste mit einem Teil der verfügbaren integrierten Richtlinien finden Sie unter [Azure Policy-Beispiele](../../../governance/policy/samples/index.md).

1. Durchsuchen Sie die Liste mit den Richtliniendefinitionen nach der Definition _\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Windows installiert sein_, falls Sie den Agent für Server mit Arc-Aktivierung auf einem Windows-basierten Computer aktiviert haben. Suchen Sie für einen Linux-basierten Computer nach der Richtliniendefinition _\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Linux installiert sein_. Klicken Sie auf diese Richtlinie und anschließend auf **Auswählen**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Suchen der korrekten Richtliniendefinition" border="false":::

1. Der **Zuweisungsname** wird automatisch mit dem ausgewählten Richtliniennamen gefüllt, kann aber geändert werden. Übernehmen Sie für dieses Beispiel _\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Windows installiert sein_ oder _\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Linux installiert sein_ (je nachdem, was Sie zuvor ausgewählt haben). Geben Sie ggf. auch eine **Beschreibung** ein. Die Beschreibung enthält Details zu dieser Richtlinienzuweisung.
   Über **Zugewiesen von** werden die Daten abhängig vom angemeldeten Benutzer automatisch ausgefüllt. Dieses Feld ist optional. Daher können auch benutzerdefinierte Werte eingegeben werden.

1. Lassen Sie **Verwaltete Identität erstellen** deaktiviert. Dieses Feld _muss_ aktiviert werden, wenn die Richtlinie oder Initiative eine Richtlinie mit dem Effekt [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists) enthält. Da dies bei der für diesen Schnellstart verwendeten Richtlinie nicht der Fall ist, lassen Sie es deaktiviert. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../../active-directory/managed-identities-azure-resources/overview.md) und [Funktionsweise der Wiederherstellungssicherheit](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Klicken Sie auf **Zuweisen**.

Sie können nun nicht konforme Ressourcen identifizieren, um den Konformitätszustand Ihrer Umgebung nachzuvollziehen.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Wählen Sie links auf der Seite die Option **Konformität** aus. Suchen Sie anschließend nach der von Ihnen erstellten Richtlinienzuweisung **\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Windows installiert sein** oder **\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Linux installiert sein**.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Konformitätsdetails auf der Seite zur Richtlinienkonformität" border="false":::

Falls Ressourcen vorhanden sind, die mit dieser neuen Zuweisung nicht konform sind, werden diese unter **Nicht konforme Ressourcen** angezeigt.

Wenn eine Bedingung für einige Ihrer vorhandenen Ressourcen als erfüllt ausgewertet wird, werden diese Ressourcen als nicht mit der Richtlinie konform markiert. Die folgende Tabelle gibt Aufschluss über das Zusammenspiel zwischen den verschiedenen Richtlinienauswirkungen, der Bedingungsauswertung und dem resultierenden Konformitätszustand. Die Auswertungslogik wird zwar im Azure-Portal nicht angezeigt, Sie sehen aber die Ergebnisse für den Konformitätszustand. Das Ergebnis für den Konformitätszustand ist entweder „konform“ oder „nicht konform“.

| **Ressourcenzustand** | **Auswirkung** | **Richtlinienauswertung** | **Konformitätszustand** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nicht konform |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Konform |
| Neu | Audit, AuditIfNotExist\* | True | Nicht konform |
| Neu | Audit, AuditIfNotExist\* | False | Konform |

\* Für die Auswirkungen „Append“, „DeployIfNotExist“ und „AuditIfNotExist“ muss die IF-Anweisung auf TRUE festgelegt sein.
Für die Auswirkungen muss die Existenzbedingung außerdem auf FALSE festgelegt sein, damit sie nicht konform sind. Bei TRUE löst die IF-Bedingung die Auswertung der Existenzbedingung für die zugehörigen Ressourcen aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die erstellte Zuweisung zu entfernen, gehen Sie folgendermaßen vor:

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Konformität** (oder **Zuweisungen**) aus, und suchen Sie nach der von Ihnen erstellten Richtlinienzuweisung **\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Windows installiert sein** oder **\[Vorschau]: Der Log Analytics-Agent muss auf Ihren Azure Arc-Computern unter Linux installiert sein**.

1. Klicken Sie mit der rechten Maustaste auf die Richtlinienzuweisung, und wählen Sie **Zuweisung löschen** aus.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Löschen einer Zuweisung auf der Seite zur Konformität" border="false":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einem Bereich eine Richtliniendefinition zugewiesen und deren Konformitätsbericht ausgewertet. Die Richtliniendefinition überprüft, ob alle Ressourcen im Bereich konform sind, und identifiziert die Ressourcen, die nicht konform sind. Sie können Ihren Computer mit Servern mit Azure Arc-Unterstützt jetzt überwachen, indem Sie [VM Insights](../../../azure-monitor/vm/vminsights-overview.md) aktivieren.

Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie die Leistung, den aktiven Prozess und die zugehörigen Abhängigkeiten auf Ihrem Computer überwachen und anzeigen:

> [!div class="nextstepaction"]
> [Aktivieren von VM Insights](tutorial-enable-vm-insights.md)
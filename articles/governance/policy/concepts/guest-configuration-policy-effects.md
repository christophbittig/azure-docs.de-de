---
title: Wartungsoptionen für die Gastkonfiguration
description: Die Azure Policy-Gastkonfigurationsfunktion bietet Optionen für die kontinuierliche Wartung oder Steuerung mithilfe von Korrekturtasks.
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 6c16f4a2b20ea753b1ded4e8d389babc613b4b36
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772923"
---
# <a name="remediation-options-for-guest-configuration"></a>Wartungsoptionen für die Gastkonfiguration

Bevor Sie beginnen, sollten Sie die Übersichtsseite zur [Gastkonfiguration](../concepts/guest-configuration.md) lesen.

> [!IMPORTANT]
> Die Gastkonfigurationserweiterung ist für virtuelle Computer in Azure erforderlich. Weisen Sie die folgende Richtliniendefinition zu, um die Erweiterung auf allen Computern im gewünschten Umfang bereitzustellen: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> Für die Verwendung von Gastkonfigurationspaketen, die Konfigurationen anwenden, ist die Azure-VM-Gastkonfigurationserweiterung Version **1.29.24** oder höher oder der Arc-Agent **1.10.0** oder höher erforderlich.
>
> Benutzerdefinierte Gastkonfigurationsrichtliniendefinitionen, die **AuditlfNotExists** verwenden, sind allgemein verfügbar, aber Definitionen, die **HDeploylfNotExists** mit Gastkonfiguration verwenden, sind **in der Vorschau**.

## <a name="how-remediation-set-is-managed-by-guest-configuration"></a>Wie die Wartung (Set) von der Gastkonfiguration verwaltet wird

Die Gastkonfiguration verwendet den Richtlinieneffekt [DeployIfNotExists](../concepts/effects.md#deployifnotexists) für Definitionen, die Änderungen auf Computern bereitstellen.
Legen Sie die Eigenschaften einer Richtlinienzuweisung fest, um zu steuern, wie die [Auswertung](../concepts/effects.md#deployifnotexists-evaluation) Konfigurationen automatisch oder bei Bedarf liefert.

[Für dieses Dokument ist ein Video zur exemplarischen Vorgehensweise verfügbar](https://youtu.be/rjAk1eNmDLk).

### <a name="guest-configuration-assignment-types"></a>Gastkonfiguration-Zuweisungstypen

Es gibt drei verfügbare Zuweisungstypen, wenn Gastzuweisungen erstellt werden.
Die Eigenschaft ist als Parameter von Gastkonfigurationsdefinitionen verfügbar, die **DeployIfNotExists** unterstützen.

| Zuweisungstyp | Verhalten |
|-|-|
| Audit | Meldet den Zustand des Computers, nimmt jedoch keine Änderungen vor. |
| ApplyandMonitor | Wird einmal auf den Computer angewendet, und dann wird überwacht, ob Änderungen auftreten. Wenn die Konfiguration abweicht und NonCompliant wird, wird sie nicht automatisch korrigiert, es sei denn, die Wartung wird ausgelöst. |
| ApplyandAutoCorrect | Wird auf den Computer angewendet. Bei Abweichungen wird vom lokalen Dienst auf dem Computer bei der nächsten Auswertung eine Korrektur vorgenommen. |

Wenn einem vorhandenen Computer eine neue Richtlinienzuweisung zugewiesen wird, wird in jedem der drei Zuweisungstypen automatisch eine Gastzuweisung erstellt, um zuerst den Zustand der Konfiguration zu überwachen und Informationen bereitzustellen, um entscheiden zu können, bei welchen Computern Wartungsbedarf besteht.

## <a name="remediation-on-demand-applyandmonitor"></a>Bedarfsbasierte Wartung (ApplyAndMonitor)

Standardmäßig werden Gastkonfigurationszuweisungen in einem „Bedarfsbasierte Wartung“-Szenario verwendet. Die Konfiguration wird angewendet und darf dann von der Konformität abweichen. Der Konformitätsstatus der Gastzuweisung ist „Compliant“, es sei denn, beim Anwenden der Konfiguration tritt ein Fehler auf, oder der Computer befindet sich während der nächsten Auswertung nicht mehr im gewünschten Zustand. Der Agent meldet den Status „NonCompliant“, und es wird keine automatische Wartung durchgeführt.

Um dieses Verhalten zu aktivieren, legen Sie die [assignmentType-Eigenschaft](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) der Gastkonfigurationszuweisung auf „ApplyandMonitor“ fest. Jedes Mal, wenn die Zuweisung auf dem Computer verarbeitet wird, gibt die [Test](/powershell/scripting/dsc/resources/get-test-set#test)-Methode für die jeweilige Ressource „true“ zurück, oder wenn die Methode „false“ zurückgibt, meldet der Agent „NonCompliant“.

## <a name="continuous-remediation-autocorrect"></a>Kontinuierliche Wartung (AutoCorrect)

Die Gastkonfiguration unterstützt das Konzept der „kontinuierlichen Wartung“. Wenn der Computer bei einer Konfiguration von der Konformität abweicht, wird die Konfiguration bei der nächsten Auswertung automatisch korrigiert. Sofern kein Fehler auftritt, meldet der Computer immer den Status „Compliant“ für die Konfiguration. Bei Verwendung der kontinuierlichen Wartung gibt es keine Möglichkeit, zu melden, wann eine Abweichung automatisch korrigiert wurde.

Um dieses Verhalten zu aktivieren, legen Sie die [assignmentType-Eigenschaft](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) der Gastkonfigurationszuweisung auf „ApplyandAutoCorrect“ fest. Jedes Mal, wenn die Zuweisung auf dem Computer verarbeitet wird, wird für jede Ressource, für die die [Test](/powershell/scripting/dsc/resources/get-test-set#test)-Methode „false“ zurückgibt, automatisch die [Set](/powershell/scripting/dsc/resources/get-test-set#set)-Methode ausgeführt.

## <a name="disable-remediation"></a>Deaktivieren der Wartung

Wenn die `assignmentType`-Eigenschaft auf „Audit“ festgelegt ist, führt der Agent nur eine Überwachung des Computers durch und versucht nicht, die Konfiguration zu korrigieren, wenn sie nicht konform ist.

### <a name="disable-remediation-of-custom-content"></a>Deaktivieren der Wartung von benutzerdefiniertem Inhalt

Sie können die Zuweisungstypeigenschaft für benutzerdefinierte Inhaltspakete überschreiben, indem Sie dem Computer ein Tag mit dem Namen **CustomGuestConfigurationSetPolicy** hinzufügen und den Wert **deaktivieren**. Durch das Hinzufügen des Tags wird die Wartung nur für benutzerdefinierte Inhaltspakete deaktiviert, nicht für integrierte Inhalte, die von Microsoft bereitgestellt werden.

## <a name="azure-policy-enforcement"></a>Azure Policy-Erzwingung

Azure Policy-Zuweisungen enthalten die erforderliche Eigenschaft [Erzwingungsmodus](../concepts/assignment-structure.md#enforcement-mode), die das Verhalten für neue und vorhandene Ressourcen bestimmt.
Steuern Sie mit dieser Eigenschaft, ob Konfigurationen automatisch auf Computer angewendet werden.

**Standardmäßig ist die Erzwingung „Aktiviert“** . Wenn ein neuer Computer bereitgestellt wird **oder die Eigenschaften eines Computers aktualisiert werden**, und sich der Computer im Bereich einer Azure Policy-Zuweisung mit einer Richtliniendefinition in der Kategorie „Gastkonfiguration“ befindet, wendet Azure Policy die Konfiguration automatisch an. **Updatevorgänge umfassen Aktionen, die in Azure Resource Manager auftreten**, z. B. das Hinzufügen oder Ändern eines Tags, und für virtuelle Computer Änderungen wie das Ändern der Größe oder das Anfügen eines Datenträgers. Lassen Sie die Erzwingung aktiviert, wenn die Konfiguration korrigiert werden soll, wenn Änderungen an der Computerressource in Azure auftreten. Auf dem Computer vorgenommene Änderungen lösen keine automatische Wartung aus, solange sie nicht die Computerressource in Azure Resource Manager ändern.

Wenn die Erzwingung auf „Deaktiviert“ festgelegt ist, wird der Zustand des Computers so lange von der Konfigurationszuweisung überwacht, bis das Verhalten durch einen [Korrekturtask](../how-to/remediate-resources.md) geändert wird. Standardmäßig aktualisieren Gastkonfigurationsdefinitionen die [assignmentType-Eigenschaft](/rest/api/guestconfiguration/guest-configuration-assignments/get#assignmenttype) von „Audit“ in „ApplyandMonitor“, sodass die Konfiguration einmal angewendet wird und dann erst wieder angewendet wird, wenn eine Wartung ausgelöst wird.

## <a name="optional-remediate-all-existing-machines"></a>OPTIONAL: Korrigieren aller vorhandenen Computer

Wenn eine Azure Policy-Zuweisung über das Azure-Portal erstellt wird, ist auf der Registerkarte „Korrektur“ das Kontrollkästchen „Korrekturtask erstellen“ verfügbar. Wenn das Kontrollkästchen aktiviert wird, werden nach dem Erstellen der Richtlinienzuweisung alle als „NonCompliant“ ausgewerteten Ressourcen automatisch durch Korrekturtasks korrigiert.

Diese Einstellung für die Gastkonfiguration hat zur Folge, dass Sie eine Konfiguration auf vielen Computern bereitstellen können, indem Sie einfach eine Richtlinie zuweisen. Sie müssen den Korrekturtask nicht auch manuell für Computer ausführen, die nicht konform sind.

## <a name="manually-trigger-remediation-outside-of-azure-policy"></a>Manuelles Auslösen der Wartung außerhalb von Azure Policy

Es ist auch möglich, die Wartung außerhalb von Azure Policy zu orchestrieren, indem auch dann eine Gastzuweisungsressource aktualisiert wird, wenn das Update keine Änderungen an den Ressourceneigenschaften vornimmt.

Wenn eine Gastkonfigurationszuweisung erstellt wird, wird die [complianceStatus-Eigenschaft](/rest/api/guestconfiguration/guest-configuration-assignments/get#compliancestatus) auf „Ausstehend“ festgelegt.
Der (von der [Gastkonfigurationserweiterung](../../../virtual-machines/extensions/guest-configuration.md) für virtuelle Azure-Computer bereitgestellte und in Servern mit Arc-Unterstützung enthaltene) Gastkonfigurationsdienst auf dem Computer fordert alle 5 Minuten eine Liste von Zuweisungen an.
Wenn die Gastkonfigurationszuweisung beide Anforderungen stellt, den `complianceStatus` „Ausstehend“ und entweder den `configurationMode` „ApplyandMonitor“ oder „ApplyandAutoCorrect“, wendet der Dienst auf dem Computer die Konfiguration an. Nachdem die Konfiguration angewendet wurde, bestimmt der Konfigurationsmodus im [nächsten Intervall](./guest-configuration.md#validation-frequency), ob nur der Konformitätsstatus gemeldet und Abweichungen zugelassen werden sollen, oder ob automatisch korrigiert werden soll.

## <a name="understanding-combinations-of-settings"></a>Grundlegendes zu Kombinationen von Einstellungen

|~| Audit | ApplyandMonitor | ApplyandAutoCorrect |
|-|-|-|-|
| Erzwingung aktiviert | Meldet nur den Status | Konfiguration wird bei der VM-Erstellung angewendet **und erneut beim Update angewendet**, andernfalls Abweichung zulässig | Konfiguration wird bei VM-Erstellung angewendet und erneut beim Update angewendet und im nächsten Intervall korrigiert, wenn Abweichungen auftreten |
| Erzwingung deaktiviert | Meldet nur den Status | Konfiguration wird angewendet, aber Abweichung zulässig | Konfiguration wird bei VM-Erstellung oder Update angewendet und im nächsten Intervall korrigiert, wenn Abweichungen auftreten |

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den Artikel mit [Informationen zu Guest Configuration von Azure Policy](./guest-configuration.md).
- Richten Sie eine [Entwicklungsumgebung](../how-to/guest-configuration-create-setup.md) für ein benutzerdefiniertes Gastkonfigurationspaket ein.
- [Erstellen Sie ein Paketartefakt](../how-to/guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](../how-to/guest-configuration-create-test.md) in Ihrer Entwicklungsumgebung.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy-Definition](../how-to/guest-configuration-create-definition.md) für die Verwaltung Ihrer Umgebung im großen Stil.
- [Weisen Sie Ihre benutzerdefinierte Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Informieren Sie sich, wie Sie die [Compliancedetails für die Richtlinienzuweisungen der Gastkonfiguration](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.

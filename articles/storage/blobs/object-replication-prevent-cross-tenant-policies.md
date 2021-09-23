---
title: Verhindern der Objektreplikation zwischen Azure Active Directory-Mandanten (Vorschau)
titleSuffix: Azure Storage
description: Verhindern der mandantenübergreifenden Objektreplikation
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a44ac9aa7e7f8a924621345c3b7316012eeddae1
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452546"
---
# <a name="prevent-object-replication-across-azure-active-directory-tenants-preview"></a>Verhindern der Objektreplikation zwischen Azure Active Directory-Mandanten (Vorschau)

Bei der Objektreplikation werden Blockblobs asynchron aus einem Container in einem Speicherkonto in einen Container in einem anderen Speicherkonto kopiert. Wenn Sie eine Objektreplikationsrichtlinie konfigurieren, geben Sie das Quellkonto und den Quellcontainer sowie das Zielkonto und den Zielcontainer an. Nach dem Konfigurieren der Richtlinie kopiert Azure Storage automatisch die Ergebnisse von Erstellungs-, Aktualisierungs- und Löschvorgängen für ein Quellobjekt in das Zielobjekt. Weitere Informationen zur Objektreplikation in Azure Storage finden Sie unter [Objektreplikation für Blockblobs](object-replication-overview.md).

Standardmäßig kann ein autorisierter Benutzer eine Objektreplikationsrichtlinie konfigurieren, bei der sich das Quellkonto in einem Azure Active Directory -Mandanten (Azure AD) und das Zielkonto in einem anderen Mandanten befindet. Wenn Ihre Sicherheitsrichtlinien erfordern, dass die Objektreplikation auf Speicherkonten innerhalb desselben Mandanten beschränkt wird, können Sie die Erstellung von Richtlinien unterbinden, bei denen sich die Quell- und Zielkonten in unterschiedlichen Mandanten befinden (Vorschauversion). Standardmäßig ist die mandantenübergreifende Objektreplikation für ein Speicherkonto aktiviert, sofern Sie sie nicht explizit deaktivieren.

In diesem Artikel wird beschrieben, wie Sie die mandantenübergreifende Objektreplikation für Ihre Speicherkonten korrigieren. Außerdem wird die Erstellung von Richtlinien beschrieben, mit denen eine Sperre für die mandantenübergreifende Objektreplikation für neue und vorhandene Speicherkonten durchgesetzt wird.

Weitere Informationen zum Konfigurieren von Objektreplikationsrichtlinien, einschließlich mandantenübergreifender Richtlinien, finden Sie unter [Konfigurieren der Objektreplikation für Blockblobs](object-replication-configure.md).

## <a name="remediate-cross-tenant-object-replication"></a>Korrigieren der mandantenübergreifenden Objektreplikation

Um die Objektreplikation zwischen Azure AD-Mandanten zu verhindern, legen Sie die Eigenschaft **AllowCrossTenantReplication** für das Speicherkonto auf **false** fest. Wenn für ein Speicherkonto derzeit keine Richtlinien für die mandantenübergreifende Objektreplikation gelten, verhindert das Festlegen der Eigenschaft **AllowCrossTenantReplication** auf *false* die zukünftige Konfiguration von Richtlinien für die mandantenübergreifende Objektreplikation, bei denen dieses Speicherkonto als Quelle oder Ziel verwendet wird. Wenn ein Speicherkonto derzeit jedoch an mindestens einer Richtlinie für die mandantenübergreifende Objektreplikation beteiligt ist, kann die Eigenschaft **AllowCrossTenantReplication** erst dann auf *false* festgelegt werden, wenn Sie die vorhandenen mandantenübergreifenden Richtlinien löschen.

Mandantenübergreifende Richtlinien sind für ein Speicherkonto standardmäßig zulässig. Die Eigenschaft **AllowCrossTenantReplication** wird jedoch nicht standardmäßig für ein neues oder vorhandenes Speicherkonto festgelegt und gibt erst dann einen Wert zurück, wenn Sie sie explizit festlegen. Das Speicherkonto kann von Richtlinien für die mandantenübergreifende Objektreplikation betroffen sein, wenn der Eigenschaftswert entweder **null** oder **true** lautet.

### <a name="remediate-cross-tenant-replication-for-a-new-account"></a>Korrigieren der mandantenübergreifenden Replikation für ein neues Konto

Um die mandantenübergreifende Replikation für ein neues Speicherkonto zu verbieten, verwenden Sie das Azure-Portal, PowerShell oder die Azure CLI.

#### <a name="portal"></a>[Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um die mandantenübergreifende Objektreplikation für ein neues Speicherkonto zu verbieten:

1. Navigieren Sie im Azure-Portal zur Seite **Speicherkonten**, und klicken Sie auf **Erstellen**.
1. Füllen Sie die Registerkarte **Grundlagen** für das neue Speicherkonto aus.
1. Suchen Sie auf der Registerkarte **Erweitert** im Abschnitt **Blobspeicher** die Einstellung **Mandantenübergreifende Replikation zulassen**, und deaktivieren Sie das Kontrollkästchen.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-create-account.png" alt-text="Screenshot: Unterbinden der mandantenübergreifenden Objektreplikation für ein neues Speicherkonto":::

1. Schließen Sie den Vorgang zum Erstellen des Kontos ab.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die mandantenübergreifende Objektreplikation für ein neues Speicherkonto zu verbieten, rufen Sie den Befehl [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) auf, und geben Sie den Parameter `AllowCrossTenantReplication` mit dem Wert *$false* an.

```azurepowershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account and disallow cross-tenant replication.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_LRS
    -AllowCrossTenantReplication $false

# Read the property for the new storage account
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowCrossTenantReplication
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die mandantenübergreifende Objektreplikation für ein neues Speicherkonto zu verbieten, rufen Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) auf, und geben Sie den Parameter `allow-cross-tenant-replication` mit dem Wert *false* an.

```azurecli
# Create a storage account with cross-tenant replication disallowed.
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS 
    --allow-cross-tenant-replication false

# Read the property for the new storage account
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowCrossTenantReplication \
    --output tsv 
```

---

### <a name="remediate-cross-tenant-replication-for-an-existing-account"></a>Korrigieren der mandantenübergreifenden Replikation für ein vorhandenes Konto

Um die mandantenübergreifende Replikation für ein vorhandenes Speicherkonto zu verbieten, verwenden Sie das Azure-Portal, PowerShell oder die Azure CLI.

#### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Gehen Sie folgendermaßen vor, um die mandantenübergreifende Objektreplikation für ein vorhandenes Speicherkonto zu verbieten, für das derzeit keine mandantenübergreifenden Richtlinien gelten:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie unter **Datenverwaltung** die Option **Objektreplikation** aus.
1. Wählen Sie **Erweiterte Einstellungen**.
1. Deaktivieren Sie **Mandantenübergreifende Replikation zulassen**. Standardmäßig ist dieses Kontrollkästchen aktiviert, weil die mandantenübergreifende Objektreplikation für ein Speicherkonto so lange zulässig ist, bis Sie sie explizit deaktivieren.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-update-account.png" alt-text="Screenshot: Unterbinden der mandantenübergreifenden Objektreplikation für ein vorhandenes Speicherkonto":::

1. Klicken Sie auf **OK**, um die Änderungen zu speichern.

Wenn das Speicherkonto derzeit von mindestens einer mandantenübergreifenden Replikationsrichtlinie betroffen ist, können Sie die mandantenübergreifende Objektreplikation erst unterbinden, wenn Sie die entsprechenden Richtlinien löschen. In diesem Szenario ist die Einstellung im Azure-Portal nicht verfügbar, wie in der folgenden Abbildung dargestellt.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/cross-tenant-object-replication-policies-in-effect-portal.png" alt-text="Screenshot":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die mandantenübergreifende Objektreplikation für ein vorhandenes Speicherkonto zu verbieten, für das derzeit keine mandantenübergreifenden Richtlinien gelten, installieren Sie zunächst das [Az.Storage-PowerShell-Modul](https://www.powershellgallery.com/packages/Az.Storage)\,, Version 3.7.0 oder höher. Konfigurieren Sie als Nächstes die Eigenschaft **AllowCrossTenantReplication** für das Speicherkonto.

Das folgende Beispiel zeigt, wie Sie die mandantenübergreifende Objektreplikation für ein vorhandenes Speicherkonto mithilfe von PowerShell unterbinden. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowCrossTenantReplication $false
```

Wenn das Speicherkonto derzeit von mindestens einer mandantenübergreifenden Replikationsrichtlinie betroffen ist, können Sie die mandantenübergreifende Objektreplikation erst unterbinden, wenn Sie die entsprechenden Richtlinien löschen. PowerShell gibt einen Fehler aus, der darauf hinweist, dass der Vorgang aufgrund vorhandener mandantenübergreifender Replikationsrichtlinien nicht durchgeführt werden konnte.

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die mandantenübergreifende Objektreplikation für ein vorhandenes Speicherkonto zu verbieten, für das derzeit keine mandantenübergreifenden Richtlinien gelten, installieren Sie zunächst die Azure CLI, Version 2.24.0 oder höher. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli). Konfigurieren Sie als Nächstes die Eigenschaft **allowCrossTenantReplication** für ein neues oder vorhandenes Speicherkonto.

Das folgende Beispiel zeigt, wie Sie die mandantenübergreifende Objektreplikation für ein vorhandenes Speicherkonto mithilfe der Azure CLI unterbinden. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-cross-tenant-replication false
```

Wenn das Speicherkonto derzeit von mindestens einer mandantenübergreifenden Replikationsrichtlinie betroffen ist, können Sie die mandantenübergreifende Objektreplikation erst unterbinden, wenn Sie die entsprechenden Richtlinien löschen. Die Azure CLI gibt einen Fehler aus, der darauf hinweist, dass der Vorgang aufgrund vorhandener mandantenübergreifender Replikationsrichtlinien nicht durchgeführt werden konnte.

---

Nachdem Sie die mandantenübergreifende Replikation unterbunden haben, tritt bei dem Versuch, eine mandantenübergreifende Richtlinie mit dem Speicherkonto als Quelle oder Ziel zu konfigurieren, ein Fehler auf. Azure Storage gibt einen Fehler zurück, der darauf hinweist, dass die mandantenübergreifende Objektreplikation für das Speicherkonto nicht zulässig ist.

Wenn die mandantenübergreifende Objektreplikation für ein Speicherkonto unterbunden wird, müssen alle neuen Objektreplikationsrichtlinien, die Sie mit diesem Konto erstellen, die vollständigen Azure Resource Manager-IDs für das Quell- und Zielkonto enthalten. Azure Storage erfordert die vollständige Ressourcen-ID, um zu überprüfen, ob sich das Quell- und das Zielkonto innerhalb desselben Mandanten befinden. Weitere Informationen finden Sie unter [Angeben vollständiger Ressourcen-IDs für das Quell- und das Zielkonto](object-replication-overview.md#specify-full-resource-ids-for-the-source-and-destination-accounts).

Die Eigenschaft **AllowCrossTenantReplication** wird nur bei Speicherkonten unterstützt, die das Azure Resource Manager-Bereitstellungsmodell verwenden. Informationen dazu, welche Speicherkonten das Azure Resource Manager-Bereitstellungsmodell verwenden, finden Sie unter [Typen von Speicherkonten](../common/storage-account-overview.md#types-of-storage-accounts).

## <a name="permissions-for-allowing-or-disallowing-cross-tenant-replication"></a>Berechtigungen zum Zulassen oder Unterbinden der mandantenübergreifenden Replikation

Zum Festlegen der Eigenschaft **AllowCrossTenantReplication** für ein Speicherkonto muss ein Benutzer über Berechtigungen zum Erstellen und Verwalten von Speicherkonten verfügen. Azure RBAC-Rollen (Role-Based Access Control, rollenbasierte Zugriffssteuerung), die diese Berechtigungen bieten, enthalten die Aktion **Microsoft.Storage/storageAccounts/write** oder **Microsoft.Storage/storageAccounts/\*** . In diese Aktion sind folgende Rollen integriert:

- Die Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner)
- Die Azure Resource Manager-Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor)
- Die Rolle [Speicherkontomitwirkender](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Diese Rollen bieten keinen Zugriff auf Daten in einem Speicherkonto über Azure Active Directory (Azure AD). Sie enthalten jedoch die Aktion **Microsoft.Storage/storageAccounts/listkeys/action**, die Zugriff auf die Kontozugriffsschlüssel gewährt. Bei dieser Berechtigung kann ein Benutzer mithilfe der Kontozugriffsschlüssel auf alle Daten in einem Speicherkonto zuzugreifen.

Der Bereich von Rollenzuweisungen muss auf die Ebene des Speicherkontos oder höher festgelegt werden, damit ein Benutzer die mandantenübergreifende Objektreplikation für das Speicherkonto zulassen oder verweigern darf. Weitere Informationen zum Rollenbereich finden Sie unter [Grundlegendes zum Bereich für Azure RBAC](../../role-based-access-control/scope-overview.md).

Beschränken Sie die Zuweisung dieser Rollen unbedingt auf diejenigen Benutzer, denen es möglich sein muss, ein Speicherkonto zu erstellen oder dessen Eigenschaften zu aktualisieren. Verwenden Sie das Prinzip der geringsten Rechte, um sicherzustellen, dass Benutzer die geringsten Berechtigungen haben, die sie zum Ausführen ihrer Aufgaben benötigen. Weitere Informationen zum Verwalten des Zugriffs mit Azure RBAC finden Sie unter [Bewährte Methoden für Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Die zu „Administrator für klassisches Abonnement“ gehörigen Rollen „Dienstadministrator“ und „Co-Administrator“ schließen die Entsprechung der Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) ein. Weil die Rolle **Besitzer** alle Aktionen einschließt, kann ein Benutzer mit einer dieser administrativen Rollen auch Speicherkonten erstellen und verwalten. Weitere Informationen finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="use-azure-policy-to-audit-for-compliance"></a>Verwenden von Azure Policy zur Überwachung der Konformität

Wenn Sie über eine große Anzahl von Speicherkonten verfügen, sollten Sie anhand einer Überwachung sicherstellen, dass diese Konten für das Verhindern der mandantenübergreifenden Objektreplikation konfiguriert sind. Verwenden Sie Azure Policy, um eine Reihe von Speicherkonten auf ihre Konformität zu überwachen. Azure Policy ist ein Dienst, mit dem Sie Richtlinien zum Anwenden von Regeln auf Azure-Ressourcen erstellen, zuweisen und verwalten können. Azure Policy hilft Ihnen, die Konformität dieser Ressourcen mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel sicherzustellen. Weitere Informationen finden Sie in der [Übersicht über Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Erstellen einer Richtlinie mit der Auswirkung „Audit“

Azure Policy unterstützt Auswirkungen, die bestimmen, was passiert, wenn eine Richtlinie anhand einer Ressource ausgewertet wird. Die Auswirkung „Audit“ erzeugt eine Warnung, wenn eine Ressource nicht konform ist, beendet aber die Anforderung nicht. Weitere Informationen zu Auswirkungen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md).

Führen Sie die folgenden Schritte aus, um über das Azure-Portal eine Richtlinie mit einer Auswirkung „Audit“ für die Einstellung der mandantenübergreifenden Objektreplikation für ein Speicherkonto zu erstellen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie unter dem Abschnitt **Autorisierung** die Option **Definitionen** aus.
1. Wählen Sie **Richtliniendefinition hinzufügen** aus, um eine neue Richtliniendefinition zu erstellen.
1. Wählen Sie für das Feld **Definitionsspeicherort** die Schaltfläche **Mehr** aus, um anzugeben, wo sich die Ressource für die Überwachungsrichtlinie befindet.
1. Geben Sie einen Namen für die Richtlinie an. Sie können optional eine Beschreibung und eine Kategorie angeben.
1. Fügen Sie unter **Richtlinienregel** die folgende Richtliniendefinition zum Abschnitt **policyRule** hinzu.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Speichern Sie die Richtlinie.

### <a name="assign-the-policy"></a>Zuweisen der Richtlinie

Anschließend weisen Sie die Richtlinie einer Ressource zu. Der Umfang der Richtlinie entspricht der Ressource und den darunter liegenden Ressourcen. Weitere Informationen zur Zuweisung von Richtlinien finden Sie unter [Azure Policy-Zuweisungsstruktur](../../governance/policy/concepts/assignment-structure.md).

Führen Sie die folgenden Schritte aus, um die Richtlinie dem Azure-Portal zuzuweisen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie unter dem Abschnitt **Autorisierung** die Option **Zuweisungen** aus.
1. Wählen Sie **Richtlinie zuweisen** aus, um eine neue Richtlinienzuweisung zu erstellen.
1. Wählen Sie für das Feld **Umfang** den Umfang der Richtlinienzuweisung aus.
1. Wählen Sie für das Feld **Richtliniendefinition** die Schaltfläche **Mehr** und dann die im vorherigen Abschnitt definierte Richtlinie aus der Liste aus.
1. Geben Sie einen Namen für die Richtlinienzuweisung an. Die Angabe einer Beschreibung ist optional.
1. Behalten Sie für **Richtlinienerzwingung** die Einstellung *Aktiviert* bei. Diese Einstellung hat keine Auswirkung auf die Überwachungsrichtlinie.
1. Klicken Sie zum Erstellen der Zuweisung auf **Überprüfen + erstellen**.

### <a name="view-compliance-report"></a>Anzeigen des Konformitätsberichts

Nachdem Sie die Richtlinie zugewiesen haben, können Sie den Konformitätsbericht anzeigen. Der Konformitätsbericht für eine Überwachungsrichtlinie enthält Informationen darüber, welche Speicherkonten weiterhin Richtlinien zur mandantenübergreifenden Objektreplikation zulassen. Weitere Informationen finden Sie unter [Abrufen von Daten zur Richtlinienkonformität](../../governance/policy/how-to/get-compliance-data.md).

Es kann einige Minuten dauern, bis der Konformitätsbericht nach Erstellung der Richtlinienzuweisung verfügbar ist.

Führen Sie die folgenden Schritte aus, um den Konformitätsbericht im Azure-Portal anzuzeigen:

1. Navigieren Sie im Azure-Portal zum Azure Policy-Dienst.
1. Wählen Sie **Compliance** aus.
1. Filtern Sie die Ergebnisse nach dem Namen der Richtlinienzuweisung, die Sie im vorherigen Schritt erstellt haben. Der Bericht zeigt Ressourcen, die mit der Richtlinie nicht konform sind.
1. Sie können einen Drilldown in den Bericht ausführen, um weitere Details anzuzeigen, einschließlich einer Liste von Speicherkonten, die nicht konform sind.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/compliance-report-cross-tenant-audit-effect-policy.png" alt-text="Screenshot: Konformitätsbericht zur Überwachungsrichtlinie für die mandantenübergreifende Blobobjektreplikation":::

## <a name="use-azure-policy-to-enforce-same-tenant-replication-policies"></a>Verwenden von Azure Policy zum Durchsetzen von Richtlinien für die Replikation innerhalb desselben Mandanten

Azure Policy unterstützt die Cloudgovernance, indem es sicherstellt, dass Azure-Ressourcen den Anforderungen und Standards entsprechen. Um sicherzustellen, dass Speicherkonten in Ihrer Organisation die mandantenübergreifende Replikation nicht zulassen, können Sie eine Richtlinie erstellen, die die Erstellung eines neuen Speicherkontos verhindert, welches Richtlinien zur mandantenübergreifenden Objektreplikation zulässt. Die Erzwingungsrichtlinie nutzt die Auswirkung „Deny“, um eine Anforderung zu verhindern, durch die ein Speicherkonto zum Ermöglichen der mandantenübergreifenden Objektreplikation erstellt oder geändert wird. Die Verweigerungsrichtlinie verhindert zudem alle Konfigurationsänderungen an einem bestehenden Konto, wenn die Einstellung der mandantenübergreifenden Objektreplikation für dieses Konto nicht der Richtlinie entspricht. Weitere Informationen zur Auswirkung „Deny“ finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](../../governance/policy/concepts/effects.md).

Um eine Richtlinie mit der Auswirkung „Deny“ für die mandantenübergreifende Objektreplikation zu erstellen, befolgen Sie dieselben Schritte, die unter [Verwenden von Azure Policy zur Überwachung der Konformität](#use-azure-policy-to-audit-for-compliance) beschrieben werden, aber geben Sie im Abschnitt **policyRule** der Richtliniendefinition den folgenden JSON-Code an:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Nachdem Sie die Richtlinie mit der Auswirkung „Deny“ erstellt und einem Bereich zugewiesen haben, kann ein Benutzer kein Speicherkonto erstellen, das die mandantenübergreifende Objektreplikation gestattet. Ebenso wenig kann ein Benutzer Konfigurationsänderungen an einem vorhandenen Speicherkonto vornehmen, das die mandantenübergreifende Objektreplikation zurzeit zulässt. Ein entsprechender Versuch führt zu einem Fehler. Die Eigenschaft **AllowCrossTenantReplication** für das Speicherkonto muss auf **false** festgelegt werden, um gemäß Richtlinie mit der Kontoerstellung oder mit Konfigurationsaktualisierungen fortzufahren.

Die folgende Abbildung zeigt den Fehler beim Erstellen eines Speicherkontos, das die mandantenübergreifende Objektreplikation erlaubt (die Standardeinstellung für ein neues Konto), wenn eine Richtlinie mit der Auswirkung „Deny“ erfordert, dass die mandantenübergreifende Objektreplikation nicht zulässig ist.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-replication-deny-policy-error-portal.png" alt-text="Screenshot mit dem Fehler, der beim Erstellen eines Speicherkontos bei einem Verstoß gegen die Richtlinie auftritt":::

## <a name="see-also"></a>Weitere Informationen

- [Objektreplikation für Blockblobs](object-replication-overview.md)
- [Konfigurieren der Objektreplikation für Blockblobs](object-replication-configure.md)
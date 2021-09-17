---
title: Grundlegendes zu den Zuweisungsressourcen der Gastkonfiguration
description: Die Gastkonfiguration erstellt Erweiterungsressourcen namens Gastkonfigurationszuweisungen, die Konfigurationen Computern zuordnen.
ms.date: 08/15/2021
ms.topic: conceptual
ms.openlocfilehash: c106ca492166cc604607bfc8da3f9c7d7b3bff2d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772930"
---
# <a name="understand-guest-configuration-assignment-resources"></a>Grundlegendes zu den Zuweisungsressourcen der Gastkonfiguration

Wenn eine Azure Policy zugewiesen ist, enthält die Kategorie „Gastkonfiguration“ Metadaten, die eine Gastzuweisung beschreiben.

[Für dieses Dokument ist ein Video zur exemplarischen Vorgehensweise verfügbar](https://youtu.be/DmCphySEB7A).

Sie können sich eine Gastzuweisung als Verknüpfung zwischen einem Computer und einem Azure Policy-Szenario vorstellen. Beispielsweise ordnet der folgende Codeausschnitt allen Computern im Geltungsbereich der Richtlinie die Azure Windows-Baselinekonfiguration mit der Mindestversion `1.0.0` zu.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

## <a name="how-azure-policy-uses-guest-configuration-assignments"></a>Verwenden von Gastkonfigurationszuweisungen durch Azure Policy

Die Metadateninformationen werden vom Gastkonfigurationsdienst verwendet, um automatisch eine Überwachungsressource für Definitionen mit den Richtlinienauswirkungen **AuditIfNotExists** oder **DeployIfNotExists** zu erstellen. Der Ressourcentyp lautet `Microsoft.GuestConfiguration/guestConfigurationAssignments`. Azure Policy verwendet die **complianceStatus**-Eigenschaft der Gastzuweisungsressource, um den Konformitätsstatus zu melden. Weitere Informationen finden Sie unter [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).

### <a name="deletion-of-guest-assignments-from-azure-policy"></a>Löschen von Gastzuweisungen aus Azure Policy

Wenn eine Azure Policy-Zuweisung gelöscht wird und eine Gastkonfigurationszuweisung von der Richtlinie erstellt wurde, wird auch die Gastkonfigurationszuweisung gelöscht.

## <a name="manually-creating-guest-configuration-assignments"></a>Manuelles Erstellen von Gastkonfigurationszuweisungen

Gastzuweisungsressourcen in Azure Resource Manager können von Azure Policy oder einem beliebigen Client-SDK erstellt werden.

Beispiel für eine Bereitstellungsvorlage:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2021-01-25",
      "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
      "name": "myMachine/Microsoft.GuestConfiguration/myConfig",
      "location": "westus2",
      "properties": {
        "guestConfiguration": {
          "name": "myConfig",
          "contentUri": "https://mystorageaccount.blob.core.windows.net/mystoragecontainer/myConfig.zip?sv=SASTOKEN",
          "contentHash": "SHA256HASH",
          "version": "1.0.0",
          "assignmentType": "ApplyAndMonitor",
          "configurationParameter": {}
        }
      }
    }    
  ]
}
```

In der folgenden Tabelle werden die einzelnen Eigenschaften von Gastzuweisungsressourcen beschrieben.

| Eigenschaft | BESCHREIBUNG |
|-|-|
| name | Name der Konfiguration in der MOF-Inhaltspaketdatei. |
| contentUri | HTTPS-URI-Pfad zum Inhaltspaket (.zip). |
| contentHash | Ein SHA256-Hashwert des Inhaltspakets, der verwendet wird, um sicherzustellen, dass es sich nicht geändert hat. |
| version | Version des Inhaltspakets. Wird nur für integrierte Pakete und nicht für benutzerdefinierte Inhaltspakete verwendet. |
| assignmentType | Verhalten der Zuweisung. Zulässige Werte: `Audit`, `ApplyandMonitor` und `ApplyandAutoCorrect`. |
| configurationParameter | Liste der DSC-Ressourcentypen, -namen und -werte in der MOF-Inhaltspaketdatei, die nach dem Herunterladen auf dem Computer überschrieben werden soll. |

### <a name="deletion-of-manually-created-guest-configuration-assignments"></a>Löschen manuell erstellter Gastkonfigurationszuweisungen

Gastkonfigurationszuweisungen, die mit einem manuellen Ansatz (z. B. einer Azure Resource Manager-Vorlagenbereitstellung) erstellt wurden, müssen manuell gelöscht werden.
Beim Löschen der übergeordneten Ressource (VM oder Arc-fähiger Computer) wird auch die Gastkonfigurationszuweisung gelöscht.

Verwenden Sie das folgende Beispiel, um eine Gastkonfigurationszuweisung manuell zu löschen. Stellen Sie sicher, dass Sie alle Beispielzeichenfolgen ersetzen, die durch „\<\>“ Klammern gekennzeichnet sind.

```PowerShell
# First get details about the guest configuration assignment
$resourceDetails = @{
  ResourceGroupName = '<myResourceGroupName>'
  ResourceType      = 'Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments/'
  ResourceName      = '<myVMName>/Microsoft.GuestConfiguration'
  ApiVersion        = '2020-06-25'
}
$guestAssignment = Get-AzResource @resourceDetails

# Review details of the guest configuration assignment
$guestAssignment

# After reviewing properties of $guestAssignment to confirm
$guestAssignment | Remove-AzResource
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Übersicht über die Gastkonfiguration](./guest-configuration.md).
- Richten Sie eine [Entwicklungsumgebung](../how-to/guest-configuration-create-setup.md) für ein benutzerdefiniertes Gastkonfigurationspaket ein.
- [Erstellen Sie ein Paketartefakt](../how-to/guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](../how-to/guest-configuration-create-test.md) aus Ihrer Entwicklungsumgebung.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy Definition](../how-to/guest-configuration-create-definition.md) für die skalierungsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie die [Konformitätsdetails für die Zuweisung von Gastkonfigurationsrichtlinien](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen können.

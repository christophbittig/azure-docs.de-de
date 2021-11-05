---
title: Beheben von häufigen Bereitstellungsfehlern
description: Beschreibt, wie Sie häufig auftretende Fehler beheben, wenn Sie Azure-Ressourcen mithilfe von Azure Resource Manager-Vorlagen (ARM-Vorlagen) oder Bicep-Dateien bereitstellen.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe10ccd523618c1afbc9bf5214bcd51bc18419ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095593"
---
# <a name="troubleshoot-common-azure-deployment-errors"></a>Problembehandlung für häufige Azure-Bereitstellungsfehler

In diesem Artikel werden einige häufige Azure-Bereitstellungsfehler beschrieben. Er enthält darüber hinaus Informationen zur Behebung der Fehler. Azure-Ressourcen können mithilfe von Azure Resource Manager-Vorlagen (ARM-Vorlagen) oder Bicep-Dateien bereitgestellt werden. Sie können unter [Ermitteln des Fehlercodes](#find-error-code) nachsehen, falls Sie den Fehlercode für Ihren Bereitstellungsfehler nicht kennen.

Wenn Sie Informationen zu einem Fehlercode suchen und diese Informationen in diesem Artikel nicht angegeben sind, teilen Sie uns dies bitte mit. Unten auf dieser Seite können Sie uns Feedback geben. Das Feedback wird über GitHub-Probleme nachverfolgt.

Um PowerShell- und Azure CLI-Befehle über das Portal auszuführen, verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md). Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps) oder die [Azure CLI](/cli/azure/install-azure-cli), um Befehle auf Ihrem Computer auszuführen.

## <a name="error-codes"></a>Fehlercodes

| Fehlercode | Minderung | Weitere Informationen |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Beachten Sie die Einschränkungen bei der Namensgebung für Speicherkonten. | [Resolve errors for storage account names](error-storage-account-name.md) (Beheben von Fehlern für Namen von Speicherkonten) |
| AccountPropertyCannotBeSet | Überprüfen Sie die verfügbaren Speicherkontoeigenschaften. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Im Cluster oder in der Region sind keine Ressourcen verfügbar, oder die angeforderte Größe des virtuellen Computers kann nicht unterstützt werden. Wiederholen Sie die Anforderung zu einem späteren Zeitpunkt, oder fordern Sie eine andere Größe für den virtuellen Computer an. | [Bereitstellungs- und Zuteilungsprobleme bei Linux](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) <br><br> [Bereitstellungs- und Zuteilungsprobleme bei Windows](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) <br><br> [Problembehandlung bei Zuordnungsfehlern](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Warten Sie, bis der gleichzeitige Vorgang abgeschlossen ist. | |
| AuthorizationFailed | Ihr Konto oder Dienstprinzipal verfügt nicht über ausreichende Zugriffsberechtigungen zum Durchführen der Bereitstellung. Überprüfen Sie die Rolle, zu der Ihr Konto gehört, sowie deren Zugriffsberechtigungen für den Bereitstellungsumfang.<br><br>Unter Umständen wird dieser Fehler angezeigt, wenn ein erforderlicher Ressourcenanbieter nicht registriert ist. | [Rollenbasierte Zugriffssteuerung von Azure (Azure-RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Lösen von Registrierungsfehlern](error-register-resource-provider.md) |
| BadRequest | Sie haben Bereitstellungswerte gesendet, die nicht den von Resource Manager erwarteten Werten entsprechen. Überprüfen Sie die interne Statusmeldung, um Hilfe zur Problembehandlung zu erhalten. | [Vorlagenreferenz](/azure/templates/) und [Unterstützte Speicherorte](../templates/resource-location.md) |
| Konflikt: | Sie fordern einen Vorgang an, der im aktuellen Zustand der Ressource nicht zulässig ist. Eine Größenänderung für den Datenträger ist beispielsweise nur zulässig, wenn ein virtueller Computer erstellt wird oder die Zuweisung des virtuellen Computers aufgehoben wurde. | |
| DeploymentActiveAndUneditable | Warten Sie, bis die gleichzeitige Bereitstellung für diese Ressourcengruppe abgeschlossen ist. | |
| DeploymentFailedCleanUp | Bei Bereitstellungen im vollständigen Modus werden alle Ressourcen, die nicht in der Vorlage enthalten sind, gelöscht. Dieser Fehler tritt auf, wenn Sie nicht über die erforderlichen Berechtigungen verfügen, um alle Ressourcen zu löschen, die nicht in der Vorlage enthalten sind. Verwenden Sie zur Vermeidung dieses Fehlers den inkrementellen Bereitstellungsmodus. | [Azure Resource Manager-Bereitstellungsmodi](../templates/deployment-modes.md) |
| DeploymentNameInvalidCharacters | Der Bereitstellungsname darf nur Buchstaben, Ziffern, Bindestriche `(-)`, Punkte `(.)` oder Unterstriche `(_)` enthalten. | |
| DeploymentNameLengthLimitExceeded | Die Länge von Bereitstellungsnamen ist auf 64 Zeichen beschränkt.  | |
| DeploymentFailed | „DeploymentFailed“ ist ein allgemeiner Fehler, der nicht die Informationen bereitstellt, die Sie zum Beheben des Fehlers benötigen. Suchen Sie in den Fehlerdetails nach einem Fehlercode, der weitere Informationen bereitstellt. | [Ermitteln des Fehlercodes](#find-error-code) |
| DeploymentQuotaExceeded | Wenn der Grenzwert von 800 Bereitstellungen pro Ressourcengruppe erreicht ist, löschen Sie nicht mehr benötigte Bereitstellungen aus dem Verlauf. | [Beheben des Fehlers, dass die Anzahl der Bereitstellungen 800 überschreitet](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Vereinfachen Sie die Vorlage, um die Größe zu verringern. | [Beheben von Fehlern bei der Vorlagengröße](error-job-size-exceeded.md) |
| DnsRecordInUse | Der Name des DNS-Eintrags muss eindeutig sein. Geben Sie einen anderen Namen ein. | |
| ImageNotFound | Überprüfen Sie die Einstellungen für das VM-Image. |  |
| InternalServerError | Wird durch ein vorübergehendes Problem verursacht. Versuchen Sie die Bereitstellung erneut. | |
| InUseSubnetCannotBeDeleted | Dieser Fehler tritt gegebenenfalls auf, wenn versucht wird, eine Ressource zu aktualisieren, die Anforderung jedoch durch Löschen und Erstellen der Ressource verarbeitet wird. Stellen Sie sicher, dass Sie alle unveränderten Werte angeben. | [Aktualisieren von Ressourcen](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Rufen Sie das Zugriffstoken für den entsprechenden Mandanten ab. Sie können das Token nur von dem Mandanten erhalten, zu dem Ihr Konto gehört. | |
| InvalidContentLink | Sie haben wahrscheinlich versucht, eine Verknüpfung mit einer geschachtelten Vorlage herzustellen, die nicht verfügbar ist. Überprüfen Sie den URI, den Sie für die geschachtelte Vorlage angegeben haben. Wenn die Vorlage in einem Speicherkonto vorhanden ist, stellen Sie sicher, dass auf den URI zugegriffen werden kann. Möglicherweise müssen Sie ein SAS-Token übergeben. Derzeit ist es nicht möglich, eine Verknüpfung mit einer Vorlage zu erstellen, die sich in einem Speicherkonto hinter einer [Azure Storage-Firewall](../../storage/common/storage-network-security.md) befindet. Erwägen Sie, Ihre Vorlage in ein anderes Repository, etwa GitHub, zu verschieben. | [Verknüpfte Vorlagen](../templates/linked-templates.md) |
| InvalidDeploymentLocation | Beim Bereitstellen auf der Abonnementebene haben Sie einen anderen Ort für einen zuvor verwendeten Bereitstellungsnamen angegeben. | [Bereitstellungen auf Abonnementebene](../templates/deploy-to-subscription.md) |
| InvalidParameter | Einer der Werte, die Sie für eine Ressource angegeben haben, stimmt nicht mit dem erwarteten Wert überein. Ursache für diesen Fehler können viele unterschiedliche Bedingungen sein. Beispielsweise kann ein Kennwort unzureichend oder ein Blobname fehlerhaft sein. In der Fehlermeldung sollte angegeben sein, welcher Wert korrigiert werden muss. | |
| InvalidRequestContent | Die Bereitstellungswerte enthalten entweder unerwartete Werte, oder es fehlen erforderliche Werte. Bestätigen Sie die Werte für Ihren Ressourcentyp. | [Vorlagenreferenz](/azure/templates/) |
| InvalidRequestFormat | Aktivieren Sie die Debugprotokollierung, wenn Sie die Bereitstellung ausführen, und überprüfen Sie den Inhalt der Anforderung. | [Debugprotokollierung](#enable-debug-logging) |
| InvalidResourceLocation | Geben Sie einen eindeutigen Namen für das Speicherkonto an. | [Resolve errors for storage account names](error-storage-account-name.md) (Beheben von Fehlern für Namen von Speicherkonten) |
| InvalidResourceNamespace | Überprüfen Sie den Ressourcennamespace, den Sie in der **type**-Eigenschaft angegeben haben. | [Vorlagenreferenz](/azure/templates/) |
| InvalidResourceReference | Die Ressource ist entweder noch nicht vorhanden, oder der Verweis darauf ist fehlerhaft. Überprüfen Sie, ob Sie eine Abhängigkeit hinzufügen müssen. Überprüfen Sie, ob Ihre Verwendung der Funktion **reference** die für Ihr Szenario erforderlichen Parameter umfasst. | [Auflösen von Abhängigkeiten](error-not-found.md) |
| InvalidResourceType | Überprüfen Sie den Ressourcentyp, den Sie in der **type**-Eigenschaft angegeben haben. | [Vorlagenreferenz](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrieren Sie Ihr Abonnement beim Ressourcenanbieter. | [Lösen von Registrierungsfehlern](error-register-resource-provider.md) |
| InvalidTemplateDeployment <br> InvalidTemplate | Überprüfen Sie die Vorlagensyntax auf Fehler. | [Beheben von Fehlern für eine ungültige Vorlage](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Entfernen Sie unnötige Abhängigkeiten. | [Beheben von Ringabhängigkeiten](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Vereinfachen Sie die Vorlage, um die Größe zu verringern. | [Beheben von Fehlern bei der Vorlagengröße](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Überprüfen Sie, ob Ihr Konto zu demselben Mandanten wie die Ressourcengruppe gehört, für die Sie die Bereitstellung durchführen. | |
| LinkedInvalidPropertyId | Die Ressourcen-ID für eine Ressource wird nicht richtig aufgelöst. Überprüfen Sie, ob Sie alle erforderlichen Werte für die Ressourcen-ID angegeben haben, z.B. Abonnement-ID, Name der Ressourcengruppe, Ressourcentyp, Name der übergeordneten Ressource (falls erforderlich) und Ressourcenname. | |
| LocationRequired | Geben Sie einen Speicherort für die Ressource an. | [Standort festlegen](../templates/resource-location.md) |
| MismatchingResourceSegments | Stellen Sie sicher, dass eine geschachtelte Ressource die richtige Anzahl an Segmenten in Name und Typ aufweist. | [Auflösen von Ressourcensegmenten](error-invalid-template.md#incorrect-segment-lengths) |
| MissingRegistrationForLocation | Überprüfen Sie den Registrierungsstatus des Ressourcenanbieters und die unterstützten Speicherorte. | [Lösen von Registrierungsfehlern](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registrieren Sie Ihr Abonnement beim Ressourcenanbieter. | [Lösen von Registrierungsfehlern](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Überprüfen Sie den Registrierungsstatus des Ressourcenanbieters. | [Lösen von Registrierungsfehlern](error-register-resource-provider.md) |
| NotFound | Unter Umständen versuchen Sie, eine abhängige Ressource parallel zu einer übergeordneten Ressource bereitzustellen. Überprüfen Sie, ob Sie eine Abhängigkeit hinzufügen müssen. | [Auflösen von Abhängigkeiten](error-not-found.md) |
| OperationNotAllowed | Bei der Bereitstellung wird versucht, einen Vorgang durchzuführen, bei dem das Kontingent für das Abonnement, die Ressourcengruppe oder die Region überschritten wird. Ändern Sie die Bereitstellung nach Möglichkeit so, dass die Kontingentwerte eingehalten werden. Erwägen Sie andernfalls, eine Änderung der Kontingente anzufordern. | [Beheben von Fehlern für Kontingente](error-resource-quota.md) |
| ParentResourceNotFound | Stellen Sie sicher, dass eine übergeordnete Ressource vorhanden ist, bevor Sie die untergeordneten Ressourcen erstellen. | [Beheben von Fehlern für die übergeordnete Ressource](error-parent-resource.md) |
| PasswordTooLong | Möglicherweise haben Sie ein Kennwort mit zu vielen Zeichen ausgewählt, oder Sie haben Ihr Kennwort eventuell in eine sichere Zeichenfolge konvertiert, bevor es als Parameter übergeben wurde. Wenn die Vorlage einen Parameter **Sichere Zeichenfolge** enthält, müssen Sie den Wert nicht in eine sichere Zeichenfolge konvertieren. Geben Sie den Kennwortwert als Text an. |  |
| PrivateIPAddressInReservedRange | Die angegebene IP-Adresse enthält einen Adressbereich, der für Azure benötigt wird. Ändern Sie die IP-Adresse, um die Nutzung des reservierten Bereichs zu vermeiden. | [IP-Adressen](../../virtual-network/ip-services/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | Die angegebene IP-Adresse liegt außerhalb des Subnetzbereichs. Ändern Sie die IP-Adresse so, das Sie innerhalb des Subnetzbereichs liegt. | [IP-Adressen](../../virtual-network/ip-services/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Einige Eigenschaften können in einer bereitgestellten Ressource nicht geändert werden. Beschränken Sie Ihre Änderungen beim Aktualisieren einer Ressource auf die zulässigen Eigenschaften. | [Aktualisieren von Ressourcen](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Ihr Abonnement enthält eine Ressourcenrichtlinie, durch die eine Aktion verhindert wird, die Sie während der Bereitstellung ausführen möchten. Suchen Sie nach der Richtlinie, die die Aktion blockiert. Ändern Sie die Bereitstellung nach Möglichkeit so, dass die Einschränkungen der Richtlinie beachtet werden. | [Beheben von Fehlern für Richtlinien](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Geben Sie einen Ressourcennamen an, der keinen reservierten Name enthält. | [Resolve reserved resource name errors](error-reserved-resource-name.md) (Beheben von Fehlern mit reservierten Ressourcennamen) |
| ResourceGroupBeingDeleted | Warten Sie, bis der Löschvorgang abgeschlossen ist. | |
| ResourceGroupNotFound | Überprüfen Sie den Namen der Zielressourcengruppe für die Bereitstellung. Die Zielressourcengruppe muss bereits in Ihrem Abonnement vorhanden sein. Überprüfen Sie Ihren Abonnementkontext. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Ihre Bereitstellung verweist auf eine Ressource, die nicht aufgelöst werden kann. Überprüfen Sie, ob Ihre Verwendung der Funktion **reference** die für Ihr Szenario erforderlichen Parameter enthält. | [Beheben von Fehlern für Verweise](error-not-found.md) |
| ResourceQuotaExceeded | Bei der Bereitstellung wird versucht, Ressourcen zu erstellen, für die das Kontingent für das Abonnement, die Ressourcengruppe oder die Region überschritten wird. Ändern Sie Ihre Infrastruktur nach Möglichkeit so, dass die Kontingentvorgaben eingehalten werden. Erwägen Sie andernfalls, eine Änderung der Kontingente anzufordern. | [Beheben von Fehlern für Kontingente](error-resource-quota.md) |
| SkuNotAvailable | Wählen Sie die SKU (z.B. die VM-Größe) aus, die für den ausgewählten Speicherort verfügbar ist. | [Beheben von SKU-Fehlern](error-sku-not-available.md) |
| StorageAccountAlreadyExists <br> StorageAccountAlreadyTaken | Geben Sie einen eindeutigen Namen für das Speicherkonto an. | [Resolve errors for storage account names](error-storage-account-name.md) (Beheben von Fehlern für Namen von Speicherkonten)  |
| StorageAccountNotFound | Überprüfen Sie das Abonnement, die Ressourcengruppe und den Namen des Speicherkontos, das Sie verwenden möchten. | |
| SubnetsNotInSameVnet | Ein virtueller Computer kann nur über ein virtuelles Netzwerk verfügen. Stellen Sie beim Bereitstellen von mehreren NICs sicher, dass diese demselben virtuellen Netzwerk angehören. | [Mehrere NICs](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Der Zugriff auf ein angegebenes Abonnement für die Bereitstellung ist nicht möglich. Möglicherweise ist die Abonnement-ID falsch, der Benutzer, der die Vorlage bereitstellt, verfügt nicht über die erforderlichen Berechtigungen zur Bereitstellung des Abonnements, oder die Abonnement-ID weist das falsche Format auf. Wenn Sie für die [bereichsübergreifende Bereitstellung](../templates/deploy-to-resource-group.md) geschachtelte Bereitstellungen verwenden, geben Sie die GUID für das Abonnement an. | |
| SubscriptionNotRegistered | Wenn Sie eine Ressource bereitstellen, muss der Ressourcenanbieter für Ihr Abonnement registriert sein. Wenn Sie eine Azure Resource Manager-Vorlage für die Bereitstellung verwenden, wird der Ressourcenanbieter automatisch im Abonnement registriert. Manchmal wird die automatische Registrierung nicht rechtzeitig abgeschlossen. Um diesen zeitweiligen Fehler zu vermeiden, registrieren Sie den Ressourcenanbieter vor der Bereitstellung. | [Lösen von Registrierungsfehlern](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Entfernen Sie unnötige Abhängigkeiten. | [Beheben von Ringabhängigkeiten](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Verringern Sie die Anzahl von Ressourcengruppen für eine einzelne Bereitstellung. | [Bereichsübergreifende Bereitstellungen](../templates/deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Ermitteln des Fehlercodes

Es gibt zwei Arten von Fehlern, die auftreten können:

- Überprüfungsfehler
- Bereitstellungsfehler

Überprüfungsfehler entstehen durch Szenarien, die vor der Bereitstellung bestimmt werden können. Dazu gehören z. B. Syntaxfehler in Ihrer Vorlage oder Versuche, Ressourcen bereitzustellen, die Ihre Abonnementkontingente überschreiten würden. Bereitstellungsfehler können durch Bedingungen verursacht werden, die während des Bereitstellungsprozesses auftreten. Dazu gehören z. B. Versuche, auf eine Ressource zuzugreifen, die parallel bereitgestellt wird.

Für ARM-Vorlagen geben beide Fehlertypen einen Fehlercode zurück, der für die Problembehandlung der Bereitstellung genutzt werden kann. Beide Fehlertypen werden im [Aktivitätsprotokoll](../../azure-monitor/essentials/activity-log.md) angezeigt. Überprüfungsfehler werden allerdings nicht im Bereitstellungsverlauf festgehalten, da die Bereitstellung tatsächlich nie gestartet wurde. Bicep-Dateivalidierungsfehler werden nicht in Ihrem Aktivitätsprotokoll oder dem Bereitstellungsverlauf angezeigt. Es dauert einige Minuten, bis das Aktivitätsprotokoll die neuesten Bereitstellungsinformationen anzeigt.

### <a name="validation-errors"></a>Überprüfungsfehler

Sie können eine ARM-Vorlage in das Portal hochladen und Ressourcen bereitstellen. Wenn die Vorlage Syntaxfehler enthält, wird beim Versuch, die Bereitstellung ausführen, ein Validierungsfehler angezeigt. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../templates/deploy-portal.md#deploy-resources-from-custom-template).

Im folgenden Beispiel wird versucht, ein Speicherkonto bereitzustellen, und es tritt ein Validierungsfehler auf.

:::image type="content" source="media/common-deployment-errors/validation-error.png" alt-text="Screenshot: Validierungsfehler im Azure-Portal.":::

Wählen Sie die Meldung aus, um weitere Details zu sehen. Die Vorlage weist einen Syntaxfehler mit dem Fehlercode `InvalidTemplate` auf. Die **Zusammenfassung** zeigt, dass einem Ausdruck eine schließende Klammer fehlt.

:::image type="content" source="media/common-deployment-errors/validation-details.png" alt-text="Screenshot: Validierungsfehlermeldung, die einen Syntaxfehler zeigt.":::

### <a name="deployment-errors"></a>Bereitstellungsfehler

Wenn der Vorgang die Überprüfung besteht, aber ein Fehler während der Bereitstellung auftritt, wird ein Bereitstellungsfehler angezeigt.

Um den Bereitstellungsfehler im Portal anzuzeigen, navigieren Sie zur Ressourcengruppe, und wählen Sie dann **Einstellungen** > **Bereitstellungen** aus. Wählen Sie **Fehlerdetails** aus.

:::image type="content" source="media/common-deployment-errors/deployment-error-details.png" alt-text="Screenshot: Link einer Ressourcengruppe zu Fehlerdetails für eine fehlerhafte Bereitstellung.":::

Die Fehlermeldung und der Fehlercode `NoRegisteredProviderFound` werden angezeigt.

:::image type="content" source="media/common-deployment-errors/deployment-error-summary.png" alt-text="Screenshot: Meldung mit Details zu Bereitstellungsfehlern.":::

### <a name="powershell"></a>PowerShell

Verwenden Sie [Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment), um Fehlercodes und -meldungen bei der Bereitstellung mit PowerShell anzuzeigen.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).StatusMessage
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list), um Fehlercodes und -meldungen bei der Bereitstellung mit der Azure CLI anzuzeigen.

```azurecli
az deployment operation group list --name exampledeployment --resource-group examplegroup --query "[*].properties.statusMessage"
```

## <a name="enable-debug-logging"></a>Debugprotokollierung aktivieren

Manchmal benötigen Sie weitere Informationen zur Anforderung und zur Antwort, um den genauen Fehler zu ermitteln. Sie können anfordern, dass während der Bereitstellung zusätzliche Informationen protokolliert werden.

### <a name="powershell"></a>PowerShell

Verwenden Sie in PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment), um den `DeploymentDebugLogLevel`-Parameter auf `All`, `ResponseContent` oder `RequestContent` festzulegen.

> [!NOTE]
> Wenn Debugprotokollierung aktiviert ist, wird eine Warnung angezeigt, dass Geheimnisse wie Kennwörter oder listKeys mit Befehlen wie [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) protokolliert werden können.
>
> Verwenden Sie die Azure CLI, um die `request`- und `response`-Debuginformationen abzurufen, da `Get-AzResourceGroupDeploymentOperation` in den Az-Modulversionen 4.8 und höher nicht `properties` in der Ausgabe enthält.  Weitere Informationen finden Sie unter dem [GitHub-Issue #14706](https://github.com/Azure/azure-powershell/issues/14706).


```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Überprüfen Sie den Anforderungsinhalt mit folgendem Cmdlet:

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Oder überprüfen Sie den Antwortinhalt mit:

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Mithilfe dieser Informationen können Sie ermitteln, ob ein Wert in der Vorlage nicht ordnungsgemäß festgelegt wurde.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Derzeit unterstützt Azure CLI das Aktivieren der Debugprotokollierung nicht, Sie können diese jedoch abrufen.

Untersuchen Sie die Bereitstellungsvorgänge mit folgendem Befehl:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Überprüfen Sie den Anforderungsinhalt mit folgendem Befehl:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

Überprüfen Sie den Antwortinhalt mit folgendem Befehl:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Geschachtelte Vorlage

Verwenden Sie zum Protokollieren von Debuginformationen für eine [geschachtelte](../templates/linked-templates.md#nested-template) ARM-Vorlage das `debugSetting`-Element.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep verwendet [Module](../bicep/modules.md) anstelle von [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments). Mit Modulen können Sie Ihren Code wiederverwenden, um eine Bicep-Datei aus einer anderen Bicep-Datei bereitzustellen.

## <a name="create-a-troubleshooting-template"></a>Erstellen einer Vorlage zur Problembehandlung

Mitunter ist die einfachste Möglichkeit für die Behandlung von Problemen bei Ihrer Vorlage das Testen von Teilen davon. Sie können eine vereinfachte Vorlage erstellen, die es Ihnen ermöglicht, sich auf den Teil zu konzentrieren, der Ihrer Meinung nach den Fehler verursacht. Nehmen wir beispielsweise an, dass ein Fehler auftritt, wenn Sie auf eine Ressource verweisen. Anstatt sich mit einer gesamten Vorlage zu beschäftigen, erstellen Sie eine Vorlage, die den Teil wiedergibt, der Ihr Problem möglicherweise verursacht. So können Sie besser ermitteln, ob die richtigen Parameter übergeben, Vorlagenfunktionen ordnungsgemäß genutzt und die Ressourcen abgerufen werden, die Sie erwarten.

In den folgenden Beispielen für ARM-Vorlagen und Bicep-Dateen wird ein vorhandenes Speicherkonto verwendet. Die Parameter geben den Namen und die Ressourcengruppe des Speicherkontos an. Sie können Azure Cloud Shell verwenden, um die [ARM-Vorlage](../templates/deploy-cloud-shell.md#deploy-local-template) oder [Bicep-Datei](../bicep/deploy-cloud-shell.md) bereitzustellen. Die Ausgabe ist ein Objekt mit den Eigenschaftennamen und -werten des Speicherkontos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

Führen Sie für das Bicep-Beispiel den Bereitstellungsbefehl aus der Ressourcengruppe aus, in der das Speicherkonto vorhanden ist.

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

Ein anderes Beispiel: Es treten Bereitstellungsfehler auf, von denen Sie annehmen, dass sie auf falsch festgelegte Abhängigkeiten zurückzuführen sind. Testen Sie Ihre Vorlage, indem Sie sie in einfachere Vorlagen aufteilen. Erstellen Sie zunächst eine Vorlage, mit der nur eine einzige Ressource bereitgestellt wird (z.B. eine SQL Server-Instanz). Wenn Sie sicher sind, dass die Ressource korrekt definiert ist, fügen Sie eine Ressource hinzu, die davon abhängig ist (beispielsweise eine SQL-Datenbank). Wenn diese beiden Ressourcen richtig definiert sind, fügen Sie weitere abhängige Ressourcen hinzu (z.B. Überwachungsrichtlinien). Löschen Sie zwischen den jeweiligen Testbereitstellungen die Ressourcengruppe, um sicherzustellen, dass Sie die Abhängigkeiten angemessen testen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Problembehandlung von ARM-Vorlagen finden Sie unter [Schnellstart: Problembehandlung von ARM-Vorlagenbereitstellungen](quickstart-troubleshoot-arm-deployment.md).
- Weitere Informationen zur Problembehandlung von Bicep-Dateien finden Sie unter [Schnellstart: Problembehandlung von Bereitstellungen mit Bicep-Dateien](quickstart-troubleshoot-bicep-deployment.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](../templates/deployment-history.md).

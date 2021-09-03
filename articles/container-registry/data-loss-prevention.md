---
title: Deaktivieren des Exports von Artefakten
description: Legen Sie eine Registrierungseigenschaft fest, um die Datenexfiltration aus einer Premium Azure-Containerregistrierung zu verhindern.
ms.topic: how-to
ms.date: 07/27/2021
ms.openlocfilehash: 8fa32197069376c71c035df0285852f4041efc64
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345790"
---
# <a name="disable-export-of-artifacts-from-an-azure-container-registry"></a>Deaktivieren des Exports von Artefakten aus einer Azure-Containerregistrierung 

Um zu verhindern, dass Registrierungsbenutzer in einer Organisation in böswilliger Absicht oder versehentlich Artefakte außerhalb eines virtuellen Netzwerks preisgeben, können Sie die *Exportrichtlinie* der Registrierung so konfigurieren, dass Exporte deaktiviert werden.

Die Exportrichtlinie ist eine Eigenschaft, die in der API-Version **2021-06-01-preview** für Premium-Containerregistrierungen eingeführt wurde. Wenn der Status der `exportPolicy`-Eigenschaft auf `disabled` festgelegt ist, wird der Export von Artefakten aus einer Registrierung mit Netzwerkeinschränkungen blockiert, wenn ein Benutzer Folgendes versucht:

* [Importieren](container-registry-import-images.md) der Artefakte der Registrierung in eine andere Azure-Containerregistrierung
* Erstellen einer [Exportpipeline](container-registry-transfer-images.md) für die Registrierung zum Übertragen von Artefakten in eine andere Containerregistrierung

> [!NOTE]
> Das Deaktivieren des Exports von Artefakten verhindert nicht den Zugriff autorisierter Benutzer auf die Registrierung innerhalb des virtuellen Netzwerks, um Artefakte zu pullen oder andere Vorgänge auf Datenebene auszuführen. Um diese Verwendung zu überwachen, empfiehlt es sich, Diagnoseeinstellungen zum [Überwachen](monitor-service.md) von Registrierungsvorgängen zu konfigurieren. 

## <a name="prerequisites"></a>Voraussetzungen

* Eine Premium-Containerregistrierung, die mit einem [privaten Endpunkt](container-registry-private-link.md) konfiguriert ist.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="other-requirements-to-disable-exports"></a>Weitere Anforderungen für das Deaktivieren von Exporten

* **Deaktivieren des Zugriffs aus öffentlichen Netzwerken**: Um den Export von Artefakten zu deaktivieren, muss auch der öffentliche Zugriff auf die Registrierung deaktiviert werden (die `publicNetworkAccess`-Eigenschaft der Registrierung muss auf `disabled` festgelegt sein). Sie können den Zugriff aus öffentlichen Netzwerken auf die Registrierung deaktivieren, bevor Sie den Export deaktivieren oder beides gleichzeitig deaktivieren.

    Durch die Deaktivierung des Zugriffs auf den öffentlichen Endpunkt der Registrierung stellen Sie sicher, dass Registrierungsvorgänge nur innerhalb des virtuellen Netzwerks zulässig sind. Der öffentliche Zugriff auf die Registrierung, um Artefakte zu pullen und andere Vorgänge auszuführen, ist nicht zulässig. 

*  **Entfernen von Exportpipelines**: Löschen Sie alle vorhandenen Exportpipelines, die für die Registrierung konfiguriert sind, bevor Sie den `exportPolicy`-Status der Registrierung auf `disabled` festlegen. Wenn eine Pipeline konfiguriert ist, können Sie den `exportPolicy`-Status nicht ändern.

## <a name="disable-exportpolicy-for-an-existing-registry"></a>Deaktivieren von „exportPolicy“ für eine vorhandene Registrierung

Wenn Sie eine Registrierung erstellen, wird der `exportPolicy`-Status standardmäßig auf `enabled` festgelegt, wodurch der Export von Artefakten zulässig wird. Sie können den Status mithilfe einer ARM-Vorlage oder mit dem Befehl `az resource update` auf `disabled` aktualisieren.

### <a name="arm-template"></a>ARM-Vorlage 

Nehmen Sie den folgenden JSON-Code auf, um den `exportPolicy`-Status zu aktualisieren und die `publicNetworkAccess`-Eigenschaft auf `disabled` festzulegen. Erfahren Sie mehr über das [Bereitstellen von Ressourcen mit ARM-Vorlagen](../azure-resource-manager/templates/deploy-cli.md).

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
      "publicNetworkAccess": "disabled",
      "policies": {
        "exportPolicy": {
          "status": "disabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

Führen Sie [az resource update](/cli/azure/resource/#az_resource_update) aus, um den `exportPolicy`-Status in einer vorhandenen Registrierung auf `disabled` festzulegen. Ersetzen Sie die Namen Ihrer Registrierung und Ressourcengruppe.

Wie in diesem Beispiel gezeigt, legen Sie beim Deaktivieren der `exportPolicy`-Eigenschaft außerdem die `publicNetworkAccess`-Eigenschaft auf `disabled` fest.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=disabled" \
    --set "properties.publicNetworkAccess=disabled"  
```

Die Ausgabe zeigt, dass der Status der Exportrichtlinie „deaktiviert“ ist.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "identity": null,
  "kind": null,
  "location": "centralus",
  "managedBy": null,
  "name": "myregistry",
  "plan": null,
  "properties": {
    [...]
    "policies": {
      "exportPolicy": {
        "status": "disabled"
      },
      "quarantinePolicy": {
        "status": "disabled"
      },
      "retentionPolicy": {
        "days": 7,
        "lastUpdatedTime": "2021-07-20T23:20:30.9985256+00:00",
        "status": "disabled"
      },
      "trustPolicy": {
        "status": "disabled",
        "type": "Notary"
      },
    "privateEndpointConnections": [],
    "provisioningState": "Succeeded",
    "publicNetworkAccess": "Disabled",
    "zoneRedundancy": "Disabled"
[...]
}
```

## <a name="enable-exportpolicy"></a>Aktivieren von „exportPolicy“ 

Nachdem Sie den `exportPolicy`-Status in einer Registrierung deaktiviert haben, können Sie ihn jederzeit mithilfe einer ARM-Vorlage oder mit dem Befehl `az resource update` erneut aktivieren.

### <a name="arm-template"></a>ARM-Vorlage 

Nehmen Sie den folgenden JSON-Code auf, um den `exportPolicy`-Status auf `enabled` zu aktualisieren. Erfahren Sie mehr über das [Bereitstellen von Ressourcen mit ARM-Vorlagen](../azure-resource-manager/templates/deploy-cli.md).

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
     "policies": {
        "exportPolicy": {
          "status": "enabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

Führen Sie [az resource update](/cli/azure/resource/#az_resource_update) aus, um den `exportPolicy`-Status auf `enabled` festzulegen. Ersetzen Sie die Namen Ihrer Registrierung und Ressourcengruppe.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=enabled"
```
 
## <a name="next-steps"></a>Nächste Schritte

* Informationen zu [Azure Container Registry-Rollen und -Berechtigungen](container-registry-roles.md).
* Informationen zum Verhindern des versehentlichen Löschens von Registrierungsartefakten finden Sie unter [Sperren von Containerimages](container-registry-image-lock.md).
* Erfahren Sie mehr über integrierte [Azure-Richtlinien](container-registry-azure-policy.md) zum Schützen Ihrer Azure-Containerregistrierung.

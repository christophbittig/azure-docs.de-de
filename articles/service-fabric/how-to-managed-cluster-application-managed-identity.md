---
title: Konfigurieren und Verwenden von Anwendungen mit einer verwalteten Identität auf einem verwalteten Service Fabric-Cluster
description: Erfahren Sie, wie Sie eine Anwendung mit einer verwalteten Identität in einer ARM-Vorlage (Azure Resource Manager) konfigurieren und verwenden, die in einem verwalteten Azure Service Fabric-Cluster bereitgestellt wird.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: e3fcae9a77a4fb559d8109dd2cdd1b2a9be77b73
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615256"
---
# <a name="deploy-an-application-with-managed-identity-to-a-service-fabric-managed-cluster"></a>Bereitstellen einer Anwendung mit verwalteter Identität in einem verwalteten Service Fabric-Cluster

Zum Bereitstellen einer Service Fabric Anwendung mit verwalteter Identität muss die Anwendung über Azure Resource Manager bereitgestellt werden – in der Regel mit einer Azure Resource Manager-Vorlage. Weitere Informationen zur Bereitstellung von Service Fabric-Anwendungen über Azure Resource Manager finden Sie unter [Bereitstellen einer Anwendung in einem verwalteten Cluster mit Azure Resource Manager](how-to-managed-cluster-app-deployment-template.md).

> [!NOTE] 
> 
> Anwendungen, die nicht als Azure-Ressource bereitgestellt werden, können **keine** verwalteten Identitäten besitzen. 
>
> Die Bereitstellung von Service Fabric-Anwendungen mit verwalteten Identitäten wird mit der API-Version `"2021-05-01"` auf verwalteten Clustern unterstützt.

Beispielvorlagen für verwaltete Cluster finden Sie hier: [Service Fabric verwaltete Cluster - Vorlagen](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="managed-identity-support-in-service-fabric-managed-cluster"></a>Unterstützung der verwalteten Identität im verwalteten Cluster von Service Fabric

Wenn eine Service Fabric-Anwendung mit [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) konfiguriert und im Cluster bereitgestellt wird, wird die automatische Konfiguration des *Tokendiensts für verwaltete Identitäten* im Service Fabric ausgelöst. Dieser Dienst ist für die Authentifizierung von Service Fabric-Anwendungen anhand ihrer verwalteten Identitäten und für den Abruf von Zugriffstoken in deren Auftrag zuständig. Nachdem der Dienst aktiviert wurde, wird er im Service Fabric Explorer im Abschnitt **System** im linken Bereich unter dem Namen **fabric:/System/ManagedIdentityTokenService** angezeigt.

>[!NOTE]
>Wenn eine Anwendung zum ersten Mal mit verwalteten Identitäten bereitgestellt wird, sollten Sie aufgrund der automatischen Cluster-Konfigurationsänderung mit einer einmaligen längeren Bereitstellung rechnen. Sie sollten davon ausgehen, dass dies von 15 Minuten für einen zonalen Cluster bis zu 45 Minuten für einen zonenübergreifenden Cluster dauert. Wenn noch andere Bereitstellungen im Einsatz sind, wartet die MITS-Konfiguration, bis diese abgeschlossen sind.

Die Anwendungsressource unterstützt die Zuweisung von SystemAssigned oder UserAssigned, und die Zuweisung kann wie im folgenden Codeausschnitt gezeigt erfolgen.

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "apiVersion": "2021-05-01",
  "identity": {
    "type": "SystemAssigned",
    "userAssignedIdentities": {}
  },
}

```
[Vollständige JSON-Referenz](/azure/templates/microsoft.servicefabric/2021-05-01/managedclusters/applications?tabs=json)

## <a name="user-assigned-identity"></a>Vom Benutzer zugewiesene Identität

Wenn Sie die Anwendung mit einer vom Benutzer zugewiesenen Identität aktivieren möchten, fügen Sie zunächst der Anwendungsressource mit dem Typ **userAssigned** und den referenzierten benutzerseitig zugewiesenen Identitäten die **identity**-Eigenschaft hinzu. Fügen Sie dann im Abschnitt **properties** für die **Anwendungsressource** einen Abschnitt **managedIdentities** ein, der eine Liste von Zuordnungen von Anzeigename zu principalId für jede vom Benutzer zugewiesenen Identität enthält. Weitere Informationen zu benutzerseitig zugewiesenen Identitäten finden Sie unter [Erstellen, Auflisten oder Löschen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

### <a name="application-template"></a>Anwendungsvorlage

Wenn Sie die Anwendung mit einer vom Benutzer zugewiesenen Identität aktivieren möchten, fügen Sie zunächst der Anwendungsressource mit dem Typ **userAssigned** und den referenzierten vom Benutzer zugewiesenen Identitäten die **identity**-Eigenschaft hinzu. Fügen Sie dann ein **managedIdentities**-Objekt innerhalb des Abschnitts **properties** hinzu, das eine Liste der Zuordnungen von Anzeigename zu principalId für jede vom Benutzer zugewiesene Identität enthält.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[parameters('applicationVersion')]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "version": "[parameters('applicationVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

Im Beispiel oben wird der Ressourcenname der vom Benutzer zugewiesenen Identität als Anzeigename der verwalteten Identität für die Anwendung verwendet. In den folgenden Beispielen wird davon ausgegangen, dass der tatsächliche Anzeigename „AdminUser“ lautet.

### <a name="application-package"></a>Anwendungspaket

1. Fügen Sie für jede Identität, die im Abschnitt `managedIdentities` der Azure Resource Manager-Vorlage definiert ist, im Anwendungsmanifest im Abschnitt **Principals** ein `<ManagedIdentity>`-Tag hinzu. Das `Name`-Attribut muss der im Abschnitt `managedIdentities` definierten `name`-Eigenschaft entsprechen.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Fügen Sie im Abschnitt **ServiceManifestImport** eine **IdentityBindingPolicy** für den Dienst hinzu, der die verwaltete Identität verwendet. Diese Richtlinie ordnet die Identität `AdminUser` einem dienstspezifischen Identitätsnamen zu, der später dem Dienstmanifest hinzugefügt werden muss.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Aktualisieren Sie das Dienstmanifest, um im Abschnitt **Resources** eine **ManagedIdentity** hinzuzufügen, deren Name dem `ServiceIdentityRef` in der `IdentityBindingPolicy` des Anwendungsmanifests entspricht:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

### <a name="application-template"></a>Anwendungsvorlage

Wenn Sie eine Anwendung mit einer systemseitig zugewiesenen verwalteten Identität aktivieren möchten, fügen Sie der Anwendungsressource die Eigenschaft **identity** mit dem Typ **systemAssigned** hinzu, wie im folgenden Beispiel zu sehen:

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Diese Eigenschaft deklariert (gegenüber Azure Resource Manager sowie gegenüber dem Anbieter der verwalteten Identität bzw. dem Service Fabric-Ressourcenanbieter), dass diese Ressource über eine implizite verwaltete Identität (vom Typ `system assigned`) verfügen soll.

### <a name="application-and-service-package"></a>Anwendungs- und Dienstpaket

1. Aktualisieren Sie das Anwendungsmanifest, um im Abschnitt **Principals** ein Element vom Typ **ManagedIdentity** mit einem einzelnen Eintrag hinzuzufügen, wie im Anschluss zu sehen:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Dadurch wird die der Anwendung zugewiesene Identität als Ressource einem Anzeigenamen zugeordnet, um die weitere Zuweisung zu den Diensten zu ermöglichen, aus denen sich die Anwendung zusammensetzt. 

2. Fügen Sie im Abschnitt **ServiceManifestImport** für den Dienst, dem die verwaltete Identität zugewiesen wird, ein Element vom Typ **IdentityBindingPolicy** hinzu, wie hier gezeigt:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Dieses Element weist die Identität der Anwendung dem Dienst zu. Ohne diese Zuweisung kann der Dienst nicht auf die Identität der Anwendung zugreifen. Im obigen Codeausschnitt wird die Identität `SystemAssigned` (reserviertes Schlüsselwort) der Definition des Diensts unter dem Anzeigenamen `WebAdmin` zugeordnet.

3. Aktualisieren Sie das Dienstmanifest, um im Abschnitt **Resources** ein Element vom Typ **ManagedIdentity** hinzuzufügen, dessen Name dem Wert der Einstellung `ServiceIdentityRef` aus der Definition `IdentityBindingPolicy` im Anwendungsmanifest entspricht:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Dies ist die gleiche Zuordnung einer Identität zu einem Dienst, die oben beschrieben wurde, diesmal allerdings aus der Perspektive der Dienstdefinition. Auf die Identität wird hier anhand ihres im Anwendungsmanifest deklarierten Anzeigenamens (`WebAdmin`) verwiesen.

## <a name="next-steps"></a>Nächste Schritte
* [Gewähren des Zugriffs auf Azure-Ressourcen für die verwaltete Identität einer Service Fabric-Anwendung in einem verwalteten Service Fabric-Cluster](how-to-managed-cluster-grant-access-other-resources.md)
* [Nutzen der verwalteten Identität einer Service Fabric-Anwendung aus dem Dienstcode](how-to-managed-identity-service-fabric-app-code.md)

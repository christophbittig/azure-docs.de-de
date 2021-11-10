---
title: Schützen einer App in Azure Container Apps (Vorschauversion)
description: Erfahren Sie, wie Sie Anwendungen in Azure Container Apps schützen.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: aca235582babedaa9bf6a7c44a835734ba4cb5dc
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026993"
---
# <a name="secure-an-app-in-azure-container-apps-preview"></a>Schützen einer App in Azure Container Apps (Vorschauversion)

Mithilfe von Azure Container Apps kann Ihre Anwendung vertrauliche Konfigurationswerte sicher speichern. Nach der Definition auf Anwendungsebene stehen gesicherte Werte für Container, in Skalierungsregeln und über Dapr zur Verfügung.

- Geheimnisse sind einer Anwendung zugeordnet, unabhängig von spezifischen Revisionen einer Anwendung.
- Das Hinzufügen, Entfernen oder Ändern von Geheimnissen führt nicht zu neuen Revisionen.
- Jede Anwendungsrevision kann auf mindestens ein Geheimnis verweisen.
- Mehrere Revisionen können auf dieselben Geheimnisse verweisen.

Wenn ein Geheimnis aktualisiert oder gelöscht wird, haben Sie zwei Möglichkeiten, auf die Änderungen zu reagieren:

 1. Eine neue Revision bereitstellen.
 2. Eine bestehende Revision neu starten.

Ein aktualisiertes oder entferntes Geheimnis führt nicht automatisch zu einem Neustart einer Revision.

- Stellen Sie vor dem Löschen eines Geheimnisses eine neue Revision bereit, die nicht mehr auf das alte Geheimnis verweist.
- Wenn Sie einen Geheimniswert ändern, müssen Sie die Revision neu starten, um den neuen Wert zu nutzen.

## <a name="defining-secrets"></a>Definieren von Geheimnissen

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm-template)

Geheimnisse werden auf Anwendungsebene im Abschnitt `resources.properties.configuration.secrets` definiert.

```json
"resources": [
{
    ...
    "properties": {
        "configuration": {
            "secrets": [
            {
                "name": "queue-connection-string",
                "value": "<MY-CONNECTION-STRING-VALUE>"
            }],
        }
    }
}
```

Hier wird eine Verbindungszeichenfolge für ein Warteschlangenspeicherkonto im `secrets`-Array deklariert. Um diese Konfiguration zu verwenden, ersetzen Sie `<MY-CONNECTION-STRING-VALUE>` durch den Wert Ihrer Verbindungszeichenfolge.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Geheimnisse werden mit dem `--secrets`-Parameter definiert.

- Der Parameter akzeptiert einen durch Trennzeichen getrennten Satz von Name/Wert-Paaren.
- Jedes Paar wird durch ein Gleichheitszeichen (`=`) getrennt.

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING" \
```

Hier wird eine Verbindungszeichenfolge für ein Warteschlangenspeicherkonto im `--secrets`-Parameter deklariert. Der Wert für `queue-connection-string` stammt aus einer Umgebungsvariablen namens `$CONNECTION_STRING`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Geheimnisse werden mit dem `--secrets`-Parameter definiert.

- Der Parameter akzeptiert einen durch Trennzeichen getrennten Satz von Name/Wert-Paaren.
- Jedes Paar wird durch ein Gleichheitszeichen (`=`) getrennt.

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name queuereader `
  --environment "my-environment-name" `
  --image demos/queuereader:v1 `
  --secrets "queue-connection-string=$CONNECTION_STRING" `
```

Hier wird eine Verbindungszeichenfolge für ein Warteschlangenspeicherkonto im `--secrets`-Parameter deklariert. Der Wert für `queue-connection-string` stammt aus einer Umgebungsvariablen namens `$CONNECTION_STRING`.

---

## <a name="using-secrets"></a>Verwenden von Geheimnissen

Auf Anwendungsgeheimnisse wird über die Eigenschaft `secretref` verwiesen. Geheimniswerte werden Geheimnissen auf Anwendungsebene zugeordnet, wobei der `secretref`-Wert mit dem auf Anwendungsebene deklarierten Geheimnisnamen übereinstimmt.

## <a name="example"></a>Beispiel

Das folgende Beispiel zeigt eine Anwendung, die eine Verbindungszeichenfolge auf Anwendungsebene deklariert, und die in der gesamten Konfiguration über `secretref` verwendet wird.

# <a name="arm-template"></a>[ARM-Vorlage](#tab/arm-template)

In diesem Beispiel wird die Anwendungsverbindungszeichenfolge als `queue-connection-string` deklariert und wird an anderer Stelle in den Konfigurationsabschnitten verfügbar.

:::code language="json" source="code/secure-app-arm-template.json" highlight="11,12,13,27,28,29,30,31,44,45,61,62":::

Hier ruft die Umgebungsvariable mit dem Namen `connection-string` ihren Wert aus dem Geheimnis auf Anwendungsebene `queue-connection-string` ab. Darüber hinaus verwendet die Autorisierungskonfiguration der Azure Queue Storage-Skalierungsregel `queue-connection-string`, wenn eine Verbindung hergestellt wird.

Um das Committen von Geheimniswerten in die Quellcodeverwaltung mit Ihrer ARM-Vorlage zu vermeiden, übergeben Sie Geheimniswerte als ARM-Vorlagenparameter.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

In diesem Beispiel erstellen Sie mithilfe des Azure CLI eine Anwendung mit einem Geheimnis, auf das in einer Umgebungsvariablen verwiesen wird.

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

Hier ruft die Umgebungsvariable mit dem Namen `connection-string` ihren Wert aus dem Geheimnis auf Anwendungsebene `queue-connection-string` mithilfe von `secretref` ab.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In diesem Beispiel erstellen Sie mithilfe des Azure CLI eine Anwendung mit einem Geheimnis, auf das in einer Umgebungsvariablen verwiesen wird.

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name myQueueApp `
  --environment "my-environment-name" `
  --image demos/myQueueApp:v1 `
  --secrets "queue-connection-string=$CONNECTIONSTRING" `
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

Hier ruft die Umgebungsvariable mit dem Namen `connection-string` ihren Wert aus dem Geheimnis auf Anwendungsebene `queue-connection-string` mithilfe von `secretref` ab.

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Container](containers.md)

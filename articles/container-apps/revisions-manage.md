---
title: Verwalten von Revisionen in Azure Container Apps (Vorschau)
description: Verwalten von Revisionen und Datenverkehrstrennung in Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: a1818ba143e05656cb94f70f0c849fc2dd39ba96
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026366"
---
# <a name="manage-revisions-azure-container-apps-preview"></a>Verwalten von Revisionen in Azure Container Apps (Vorschau)

Durch die Unterstützung mehrerer Revisionen in Azure Container Apps können Sie die Versionsverwaltung und den Umfang des Datenverkehrs verwalten, der [an jede Revision gesendet wird](#traffic-splitting). Verwenden Sie die folgenden Befehle, um zu steuern, wie Ihre Container-App Revisionen verwaltet.

## <a name="list"></a>List

Listen Sie mit `az containerapp revision list` alle Revisionen auf, die Ihrer Container-App zugeordnet sind.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision list `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  -o table
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

## <a name="show"></a>Anzeigen

Zeigen Sie Details zu einer bestimmten Revision mithilfe von `az containerapp revision show` an.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision show \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision show `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

## <a name="update"></a>Aktualisieren

Verwenden Sie `az containerapp update`, um eine Container-App zu aktualisieren.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp update \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp update `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

## <a name="activate"></a>Aktivieren

Aktivieren Sie eine Revision mithilfe von `az containerapp revision activate`.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision activate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```poweshell
az containerapp revision activate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

## <a name="deactivate"></a>Deaktivieren

Deaktivieren Sie Revisionen, die nicht mehr verwendet werden, mithilfe von `az container app revision deactivate`. Die Deaktivierung beendet alle ausgeführten Replikate einer Revision.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision deactivate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision deactivate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

## <a name="restart"></a>Neu starten

Alle vorhandenen Container-App-Revisionen haben erst dann Zugriff auf dieses Geheimnis, wenn sie neu gestartet werden.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision restart \
  --name <REVISION_NAME> \
  --app <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision restart `
  --name <REVISION_NAME> `
  --app <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

Ersetzen Sie bei der Interaktion mit diesem Beispiel die Platzhalter, die von `<>` umschlossen sind, durch Ihre Werte.

## <a name="set-active-revision-mode"></a>Festlegen des aktiven Revisionsmodus

Konfigurieren Sie, ob Ihre Container-App mehrere aktive Revisionen unterstützt.

Die `activeRevisionsMode`-Eigenschaft akzeptiert zwei Werte:

- `multiple`: Konfiguriert die Container-App, sodass mehr als eine aktive Revision zugelassen wird

- `single`: Deaktiviert automatisch alle anderen Revisionen, wenn eine Revision aktiviert wird Die Aktivierung des `single`-Modus sorgt dafür, dass beim Erstellen einer Änderung im Revisionsbereich und einer neuen Revision alle anderen Revisionen automatisch deaktiviert werden.

```json
{
  ...
  "resources": [
  {
    ...
    "properties": {
        "configuration": {
          "activeRevisionsMode": "multiple"
      }
    }
  }]
}
```

Das folgende Konfigurationsfragment zeigt, wie die `activeRevisionsMode`-Eigenschaft festgelegt wird. Änderungen an dieser Eigenschaft erfordern den Kontext der vollständigen ARM-Vorlage der Container-App.

## <a name="traffic-splitting"></a>Trennung von Datenverkehr

Wenn Sie Prozentwerte zuweisen, können Sie entscheiden, wie der Datenverkehr zwischen verschiedenen Revisionen ausgeglichen wird. Regeln für die Datenverkehrstrennung werden zugewiesen, indem Gewichtungen auf verschiedene Revisionen festgelegt werden.

Das folgende Beispiel zeigt, wie der Datenverkehr auf drei Revisionen aufgeteilt wird.

```json
{
  ...
  "configuration": {
    "ingress": {
      "traffic": [
        {
          "revisionName": <REVISION1_NAME>,
          "weight": 50
        },
        {
          "revisionName": <REVISION2_NAME>,
          "weight": 30
        },
        {
          "latestRevision": true,
          "weight": 20
        }
      ]
    }
  }
}
```

Jede Revision erhält Datenverkehr basierend auf den folgenden Regeln:

- 50 % der Anforderungen gehen an REVISION1.
- 30 % der Anforderungen gehen an REVISION2.
- 20 % der Anforderungen gehen an die letzte Revision.

Die Summe aller Revisionsgewichtungen muss gleich 100 sein.

Ersetzen Sie in diesem Beispiel die `<REVISION*_NAME>`-Platzhalter durch Revisionsnamen in Ihrer Container-App. Sie greifen über den Befehl [Liste](#list) auf Revisionsnamen zu.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte](get-started.md)

---
title: 'Tutorial: Erstellen und Bereitstellen einer Vorlage'
description: Erstellen Sie Ihre erste Azure Resource Manager-Vorlage (ARM-Vorlage). In diesem Tutorial lernen Sie die Syntax der Vorlagendatei kennen und erfahren, wie Sie ein Speicherkonto bereitstellen.
author: mumian
ms.date: 12/17/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 83a043084af78d382e06b3f860addb986423e9cb
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187125"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage

Dieses Tutorial enthält eine Einführung in ARM-Vorlagen (Azure Resource Manager). Es wird gezeigt, wie Sie eine Startvorlage erstellen und in Azure bereitstellen. Sie erfahren mehr über die Struktur der Vorlage und die Tools, die Sie für die Arbeit mit Vorlagen benötigen. Dieses Tutorial dauert ungefähr **12 Minuten**, die tatsächliche Zeit variiert jedoch je nach Anzahl der Tools, die Sie installieren müssen.

Dieses Tutorial ist das erste einer Reihe. Im Laufe der Reihe ändern Sie die Startvorlage Schritt für Schritt, bis Sie alle wichtigen Bestandteile einer ARM-Vorlage erkundet haben. Diese Elemente sind die Bausteine für weitaus komplexere Vorlagen. Wir hoffen, dass Sie am Ende der Reihe Ihre eigenen Vorlagen erstellen können und bereit sind, Ihre Bereitstellungen mit Vorlagen zu automatisieren.

Weitere Informationen zu den Vorteilen der Verwendung von Vorlagen und der Automatisierung von Bereitstellungen mit Vorlagen finden Sie unter [ARM-Vorlagen: Übersicht](overview.md). Falls Sie sich anhand der Module und der zugehörigen Anleitungen unter Microsoft Learn über ARM-Vorlagen informieren möchten, helfen Ihnen die Informationen unter [Bereitstellen und Verwalten von Ressourcen in Azure mithilfe von ARM-Vorlagen](/learn/paths/deploy-manage-resource-manager-templates/) weiter.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="get-tools"></a>Erforderliche Tools

Zunächst sorgen wir dafür, dass Sie über die Tools verfügen, die Sie zum Erstellen und Bereitstellen von Vorlagen benötigen. Installieren Sie diese Tools auf Ihrem lokalen Computer.

### <a name="editor"></a>Editor

Vorlagen sind JSON-Dateien. Zum Erstellen von Vorlagen benötigen Sie einen guten JSON-Editor. Wir empfehlen Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“. Informationen zum Installieren dieser Tools finden Sie bei Bedarf unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

### <a name="command-line-deployment"></a>Befehlszeilenbereitstellung

Zum Bereitstellen der Vorlage benötigen Sie außerdem entweder Azure PowerShell oder die Azure CLI. Wenn Sie die Azure CLI verwenden, müssen Sie über die neueste Version verfügen. Die Installationsanweisungen finden Sie unter:

- [Installieren von Azure PowerShell](/powershell/azure/install-az-ps)
- [Installieren der Azure CLI unter Windows](/cli/azure/install-azure-cli-windows)
- [Installieren der Azure CLI unter Linux](/cli/azure/install-azure-cli-linux)
- [Installieren der Azure CLI unter macOS](/cli/azure/install-azure-cli-macos)

Melden Sie sich nach der Installation von Azure PowerShell oder der Azure CLI zum ersten Mal an. Hilfe finden Sie unter [Anmelden –PowerShell](/powershell/azure/install-az-ps#sign-in) oder [Anmelden – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

> [!IMPORTANT]
> Wenn Sie Azure CLI verwenden, stellen Sie sicher, dass Sie Version 2.6 oder höher verwenden. Die in diesem Tutorial gezeigten Befehle funktionieren nicht, wenn Sie eine niedrigere Version verwenden. Führen Sie `az --version` aus, um Ihre installierte Version zu überprüfen.

Okay, Sie sind bereit, mehr über Vorlagen zu erfahren.

## <a name="create-your-first-template"></a>Erstellen Ihrer ersten Vorlage

1. Öffnen Sie Visual Studio Code mit installierter Erweiterung „Azure Resource Manager-Tools“.
1. Wählen Sie im Menü **Datei** zum Erstellen einer neuen Datei **Neue Datei** aus.
1. Wählen Sie im Menü **Datei** die Option **Speichern unter**.
1. Nennen Sie die Datei _azuredeploy_, und wählen Sie die Dateierweiterung _JSON_ aus. Der vollständige Name der Datei lautet _azuredeploy.json_.
1. Speichern Sie die Datei auf Ihrer Arbeitsstation. Wählen Sie einen einprägsamen Pfad aus, da Sie diesen Pfad später beim Bereitstellen der Vorlage angeben.
1. Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in die Datei ein:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Die Visual Studio Code-Umgebung sieht wie folgt aus:

    ![ARM-Vorlage, Visual Studio Code, erste Vorlage](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Diese Vorlage stellt keine Ressourcen bereit. Wir beginnen mit einer leeren Vorlage, damit Sie sich mit den Schritten zum Bereitstellen einer Vorlage vertraut machen und so die Wahrscheinlichkeit minimieren, dass etwas schief geht.

    Die JSON-Datei enthält die folgenden Elemente:

    - `$schema`: Gibt den Speicherort der JSON-Schemadatei an. Die Schemadatei beschreibt die Eigenschaften, die innerhalb einer Vorlage verfügbar sind. Das Schema definiert z. B. `resources` als eine der gültigen Eigenschaften für eine Vorlage. Machen Sie sich keine Sorgen, dass das Datum für das Schema der 01.04.2019 ist. Diese Schemaversion ist aktuell und umfasst alle neuesten Features. Das Schemadatum wurde nicht geändert, da seit seiner Einführung keine Breaking Changes vorgenommen wurden.
    - `contentVersion`: Gibt die Version der Vorlage an (z. B. 1.0.0.0). Sie können einen beliebigen Wert für dieses Element resources. Mit diesem Wert können Sie wichtige Änderungen in der Vorlage dokumentieren. Bei der Bereitstellung von Ressourcen mithilfe der Vorlage kann mit diesem Wert sichergestellt werden, dass die richtige Vorlage verwendet wird.
    - `resources`: Enthält die Ressourcen, die Sie bereitstellen oder aktualisieren möchten. Zurzeit ist das Element leer, aber Sie werden später Ressourcen hinzufügen.

1. Speichern Sie die Datei .

Herzlichen Glückwunsch, Sie haben Ihre erste Vorlage erstellt.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihren Azure-Anmeldeinformationen an, um Azure PowerShell/Azure CLI zu verwenden.

Wählen Sie die Registerkarten in den folgenden Codeabschnitten aus, um zwischen Azure PowerShell und der Azure CLI zu wählen. Die CLI-Beispiele in diesem Artikel sind für die Bash-Shell geschrieben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az login
```

---

Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie das Abonnement aus, das Sie verwenden möchten. Ersetzen Sie `[SubscriptionID/SubscriptionName]` und die eckigen Klammern `[]` durch Ihre Abonnementinformationen:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Beim Bereitstellen einer Vorlage geben Sie eine Ressourcengruppe an, die die bereitgestellten Ressourcen enthalten soll. Erstellen Sie die Ressourcengruppe vor dem Ausführen des Bereitstellungsbefehls entweder mit der Azure CLI oder Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Zum Bereitstellen der Vorlage verwenden Sie entweder die Azure CLI oder Azure PowerShell. Verwenden Sie die Ressourcengruppe aus, die Sie erstellt haben. Geben Sie der Bereitstellung einen Namen, damit Sie sie im Bereitstellungsverlauf problemlos identifizieren können. Erstellen Sie der Einfachheit halber auch eine Variable, in der der Pfad zur Vorlagendatei gespeichert wird. Mit dieser Variablen können Sie die Bereitstellungsbefehle leichter ausführen, da Sie den Pfad nicht bei jeder Bereitstellung erneut eingeben müssen. Ersetzen Sie `{provide-the-path-to-the-template-file}` und die geschweiften Klammern `{}` durch den Pfad zu Ihrer Vorlagendatei.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.

```azurecli
$templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Der Bereitstellungsbefehl gibt Ergebnisse zurück. Suchen Sie nach `ProvisioningState`, um zu sehen, ob die Bereitstellung erfolgreich war.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Bereitstellungsstatus der PowerShell-Bereitstellung](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

![Bereitstellungsstatus der Azure CLI-Bereitstellung](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Wenn bei der Bereitstellung ein Fehler aufgetreten ist, verwenden Sie den Schalter `verbose`, um Informationen zu den erstellten Ressourcen abzurufen. Verwenden Sie den Schalter `debug`, um weitere Informationen zum Debuggen zu erhalten.

## <a name="verify-deployment"></a>Überprüfen der Bereitstellung

Sie können die Bereitstellung überprüfen, indem Sie sich die Ressourcengruppe im Azure-Portal ansehen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü **Ressourcengruppen** aus.

1. Wählen Sie die im letzten Verfahren bereitgestellte Ressourcengruppe aus. Der Standardname ist **myResourceGroup**. In der Ressourcengruppe sollte keine Ressource bereitgestellt sein.

1. Hinweis: In der Übersicht wird rechts oben der Status der Bereitstellung angezeigt. Wählen Sie **1 Erfolgreich** aus.

   ![Anzeigen des Bereitstellungsstatus](./media/template-tutorial-create-first-template/deployment-status.png)

1. Der Verlauf der Bereitstellungen für die Ressourcengruppe wird angezeigt. Wählen Sie **blanktemplate** aus.

   ![Auswählen der Bereitstellung](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Sie sehen eine Zusammenfassung der Bereitstellung. In diesem Fall ist nicht viel zu sehen, da keine Ressourcen bereitgestellt wurden. Das Überprüfen der Zusammenfassung im Bereitstellungsverlauf kann später in dieser Reihe hilfreich sein. Hinweis: Auf der linken Seite können Sie Eingaben, Ausgaben und die während der Bereitstellung verwendete Vorlage anzeigen.

   ![Zusammenfassung der Bereitstellungen anzeigen](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit dem nächsten Tutorial fortfahren, müssen Sie die Ressourcengruppe nicht löschen.

Wenn Sie jetzt aufhören, sollten Sie die Ressourcengruppe löschen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine einfache Vorlage zum Bereitstellen in Azure erstellt. Im nächsten vorherigen Tutorial fügen Sie der Vorlage ein Speicherkonto hinzu und stellen sie für Ihre Ressourcengruppe bereit.

> [!div class="nextstepaction"]
> [Ressource hinzufügen](template-tutorial-add-resource.md)

---
title: Bereitstellen geschachtelter Vorlagenumgebungen
description: Erfahren Sie, wie Sie geschachtelte Azure Resource Manager-Vorlagen bereitstellen, um Umgebungen mit Azure DevTest Labs bereitzustellen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 03738697f35df16fb2915fd7ece21a77b205eca1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278320"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Bereitstellen geschachtelter Azure Resource Manager-Vorlagen für Testumgebungen
Mithilfe einer geschachtelten Bereitstellung können Sie andere Azure Resource Manager-Vorlagen der wichtigsten Resource Manager-Vorlage ausführen. Sie können Ihre Bereitstellung in eine Reihe von zielgerichteten und zweckspezifischen Vorlagen aufteilen. Eine verschachtelte Bereitstellung bietet Vorteile beim Testen, der Wiederverwendung und der Lesbarkeit. Der Artikel [Verwenden verknüpfter Vorlagen bei der Bereitstellung von Azure-Ressourcen](../azure-resource-manager/templates/linked-templates.md) bietet einen guten Überblick über diese Projektmappe mit mehreren Beispielcodes. Dieser Artikel enthält ein Beispiel, das für Azure DevTest Labs gilt. 

## <a name="key-parameters"></a>Schlüsselparameter
Sie können zwar Ihre eigene Ressourcenmanager-Vorlage von Grund auf neu erstellen, aber wir empfehlen Ihnen, das [Azure-Ressourcengruppen-Projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) in Visual Studio zu verwenden. Dieses Projekt erleichtert die Entwicklung und das Debugging von Vorlagen. Wenn Sie „azuredeploy.json“ eine geschachtelte Bereitstellungsressource hinzufügen, fügt Visual Studio einige Elemente hinzu, um die Vorlage flexibler zu gestalten. Dazu gehören:

- Der Unterordner mit der sekundären Vorlage und der Parameterdatei
- Variablennamen innerhalb der Hauptvorlagendatei
- Zwei Parameter für den Speicherort für die neuen Dateien. **_artifactsLocation** und **_artifactsLocationSasToken** sind die von DevTest Labs verwendeten Schlüsselparameter. 

Wenn Sie nicht wissen, wie DevTest Labs mit Umgebungen arbeitet, lesen Sie bitte [Erstellen von Multi-VM-Umgebungen und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md). Die Vorlagen werden in dem mit dem Lab in DevTest Labs verknüpften Repository gespeichert. Wenn Sie eine neue Umgebung mit diesen Vorlagen erstellen, werden die Dateien in einen Azure Storage-Container im Labor verschoben. Um die verschachtelten Dateien zu finden und zu kopieren, identifiziert DevTest Labs die Parameter _artifactsLocation und _artifactsLocationSasToken und kopiert die Unterordner in den Storage Container. Anschließend fügt DevTest Labs automatisch den Speicherort und das Shared Access Signature (SaS)-Token in die Parameter ein. 

## <a name="nested-deployment-example"></a>Beispiel für die geschachtelte Bereitstellung
Hier ist ein einfaches Beispiel für eine verschachtelte Bereitstellung:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Der Ordner im Repository, der diese Vorlage enthält, hat einen Unterordner (`nestedtemplates`) mit den Dateien **NestOne.json** und **NestOne.parameters.json**. In der **azuredeploy.json**-Vorlage wird der URI für die Vorlage mithilfe des Artefaktspeicherorts, dem geschachtelten Vorlagenordner und der geschachtelten Dateinamenvorlage erstellt. Auf ähnliche Weise wird der URI für diese Parameter mithilfe des Artefaktspeicherorts, dem geschachtelten Vorlagenordner und der Parameterdatei für die geschachtelte Vorlage erstellt. 

Hier ist das Bild der gleichen Projektstruktur in Visual Studio: 

![Screenshot der Projektstruktur in Visual Studio.](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Sie können weitere Ordner in den Hauptordner einfügen, aber nicht tiefer als eine Ebene. 

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Artikel enthalten ausführliche Informationen zu Umgebungen: 

- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
- [Konfigurieren und Verwenden von öffentlichen Umgebungen in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Herstellen einer Verbindung zwischen einer Umgebung und dem virtuellen Netzwerk Ihres Labs in Azure DevTest Labs](connect-environment-lab-virtual-network.md)
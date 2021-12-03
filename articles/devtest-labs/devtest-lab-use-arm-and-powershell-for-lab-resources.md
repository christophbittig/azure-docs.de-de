---
title: Erstellen oder Ändern von Labs mithilfe von Azure Resource Manager-Vorlagen
description: Informationen zum automatischen Erstellen oder Ändern von Labs mithilfe von Azure Resource Manager-Vorlagen mit PowerShell.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 61f605e5b6c65c57caf37bdc5178143e0304f02d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398533"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Automatisches Erstellen oder Ändern von Labs mit Azure Resource Manager-Vorlagen und PowerShell

Azure DevTest Labs stellt viele Azure Resource Manager-Vorlagen und PowerShell-Skripts bereit. Diese Vorlagen und Skripts können Ihnen dabei helfen, Labs und Lab-Ressourcen schnell und automatisch zu erstellen, zu ändern und bereitzustellen.

In diesem Artikel wird das Verfahren zum Verwenden dieser Vorlagen und Skripts beschrieben, um das Erstellen, Ändern und Bereitstellen Ihrer Labs zu automatisieren. In diesem Artikel wird auch erläutert, wo Sie weitere Informationen zur Verwendung von PowerShell finden, um häufige Aufgaben in DevTest Labs auszuführen.

## <a name="step-1-gather-your-templates-and-scripts"></a>Schritt 1: Suchen der Vorlagen und Skripts
Sie finden vordefinierte [Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) und [PowerShell-Skripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) im öffentlichen [GitHub-Repository](https://github.com/Azure/azure-devtestlab). Diese können Sie unverändert übernehmen oder an Ihre Anforderungen anpassen und in einem eigenen [privaten Git-Repository](devtest-lab-add-artifact-repo.md) speichern.

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Schritt 2: Bearbeiten der Azure Resource Manager-Vorlagen
Folgen Sie den Schritten unter [Erstellen Ihrer ersten Azure Resource Manager-Vorlage](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), wenn Sie noch nie eine Vorlage erstellt haben.

Unter [Bewährte Methoden für das Erstellen von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/best-practices.md) finden Sie zuverlässige und leicht anzuwendende Richtlinien und Vorschläge zum Erstellen einer Azure Resource Manager-Vorlage. Sie können eine der angegebenen Vorgehensweisen oder eines der Beispiele verwenden, um die Vorlage Ihren Wünschen entsprechend zu ändern.

## <a name="step-3-deploy-resources-with-powershell"></a>Schritt 3: Bereitstellen von Ressourcen mit PowerShell
Nachdem Sie die Vorlagen und Skripts angepasst haben, führen Sie die erforderlichen Schritte zum [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) aus. Der Artikel enthält allgemeine Informationen zur Verwendung von Azure PowerShell mit Azure Resource Manager-Vorlagen, um Ressourcen in Azure bereitzustellen.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Allgemeine Aufgaben, die in DevTest Labs mithilfe von PowerShell ausgeführt werden können
Es gibt zahlreiche andere allgemeine Aufgaben, die mithilfe von PowerShell automatisiert werden können. In den folgenden Abschnitten der Dokumentation werden die erforderlichen Schritte zum Ausführen dieser Aufgaben aufgeführt.

* [Erstellen eines benutzerdefinierten Images aus einer VHD-Datei mithilfe von PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Hochladen von VHD-Dateien in das Speicherkonto des Labs mithilfe von PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Hinzufügen eines externen Benutzers zu einem Lab mit PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Erstellen einer benutzerdefinierten Labrolle mithilfe von PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Nächste Schritte
* Hier finden Sie Informationen zum Erstellen eines [privaten Git-Repositorys](devtest-lab-add-artifact-repo.md), in dem Sie Ihre angepassten Vorlagen oder Skripts speichern können.
* Sehen Sie sich die [Azure Resource Manager-Vorlagen aus dem Katalog mit Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) an.
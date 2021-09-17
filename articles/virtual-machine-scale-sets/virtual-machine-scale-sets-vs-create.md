---
title: Bereitstellen einer VM-Skalierungsgruppe in Visual Studio
description: Bereitstellen von VM-Skalierungsgruppen mit Visual Studio und einer Resource Manager-Vorlage
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f33cedfa9220510e23cfa5b1c72ac4182e9dd028
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692369"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Erstellen einer VM-Skalierungsgruppe mit Visual Studio

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

In diesem Artikel erfahren Sie, wie Sie eine Azure-VM-Skalierungsgruppe mithilfe einer Visual Studio Ressourcengruppe bereitstellen.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) ist eine Azure Compute-Ressource zur Bereitstellung und Verwaltung mehrerer ähnlicher virtueller Computer mit automatischer Skalierung und automatischem Lastenausgleich. Sie können VM-Skalierungsgruppen über [Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-quickstart-templates) bereitstellen. Azure Resource Manager-Vorlagen können mithilfe von Azure CLI, PowerShell, REST und auch direkt über Visual Studio bereitgestellt werden. Visual Studio bietet eine Reihe von Beispielvorlagen, die als Teil eines Azure-Projekts zur Ressourcengruppenbereitstellung bereitgestellt werden können.

Azure-Ressourcengruppenbereitstellungen bieten eine Möglichkeit, mehrere zusammengehörige Azure-Ressourcen in einem einzelnen Bereitstellungsvorgang zu gruppieren und zu veröffentlichen. [Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="prerequisites"></a>Voraussetzungen

Um mit der Bereitstellung von Microsoft Azure Virtual Machine Scale Sets in Visual Studio zu beginnen, benötigen Sie Folgendes:

* Visual Studio 2013 oder höher
* Azure SDK 2.7, 2.8 oder 2.9

>[!NOTE]
>In diesem Artikel wird Visual Studio 2019 mit [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) verwendet.

## <a name="create-a-project"></a>Erstellen eines Projekts <a name="creating-a-project"></a> 

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

1. Wählen Sie in **Neues Projekt erstellen** für C# die **Azure-Ressourcengruppe** und danach **Weiter** aus.

1. Geben Sie der App unter **Neues Projekt konfigurieren** einen Namen, und wählen Sie **Erstellen** aus.

    ![Benennen und Erstellen des Projekts](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Wählen Sie aus der Liste der Vorlagen entweder **Windows Virtual Machine Scale Set** oder **Linux Virtual Machine Scale Set** aus. Klicken Sie auf **OK**.

   ![Eine Vorlage für einen virtuellen Computer auswählen](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Nachdem das Projekt erstellt wurde, werden im **Projektmappen-Explorer** PowerShell-Bereitstellungsskripts, eine Azure Resource Manager-Vorlage und eine Parameterdatei für die VM-Skalierungsgruppe angezeigt.

## <a name="customize-your-project"></a>Anpassen Ihres Projekts

Nun können Sie die Vorlage bearbeiten, um Sie an die Anforderungen Ihrer App anzupassen. Sie können Eigenschaften für VM-Erweiterungen hinzufügen oder die Lastenausgleichsregeln bearbeiten. Die VM-Skalierungsgruppenvorlagen sind standardmäßig so konfiguriert, dass die **AzureDiagnostics**-Erweiterung bereitgestellt wird, mit der ganz einfach Regeln für die automatische Skalierung hinzugefügt werden können. Außerdem stellen die Vorlagen einen Lastenausgleich mit einer öffentlichen IP-Adresse bereit, der mit NAT-Regeln für eingehenden Datenverkehr konfiguriert ist.

Der Lastenausgleich ermöglicht es Ihnen, Verbindungen mit den VM-Instanzen über SSH (Linux) oder RDP (Windows) herzustellen. Die Front-End-Portbereich beginnt bei 50000. Wenn Sie für Linux SSH mit Port 50000 herstellen, leitet der Lastenausgleich Sie an Port 22 des ersten virtuellen Computers in der Skalierungsgruppe weiter. Beim Herstellen einer Verbindung mit Port 50001 werden Sie an Port 22 des zweiten virtuellen Computers weitergeleitet usw.

 Eine gute Möglichkeit zum Bearbeiten Ihrer Vorlagen mit Visual Studio bietet die **JSON-Gliederung**. Sie können die Parameter, Variablen und Ressourcen organisieren. Mit Kenntnis des Schemas kann Visual Studio vor der Bereitstellung Fehler in der Vorlage aufzeigen.

![JSON-Explorer](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Bereitstellen des Projekts

Stellen Sie die Azure Resource Manager-Vorlage bereit, um die VM-Skalierungsgruppenressource zu erstellen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Bereitstellen** > **Neu** aus.

    ![Bereitstellen des Projekts](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. Markieren Sie unter **Für Ressourcengruppe bereitstellen** das zu verwendende Abonnement, und wählen Sie eine Ressourcengruppe aus. Bei Bedarf kann eine neue Ressourcengruppe erstellt werden.

1. Klicken Sie nun auf **Parameter bearbeiten**, um Parameter einzugeben, die in Ihre Vorlage aufgenommen werden.

   ![Abonnement und Ressourcengruppe eingeben](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Geben Sie den Benutzernamen und das Kennwort für das Betriebssystem ein. Diese Werte sind erforderlich, um die Bereitstellung zu erstellen. Falls die PowerShell Tools for Visual Studio nicht installiert sind, empfiehlt es sich, das Kontrollkästchen **Kennwörter speichern** zu aktivieren, um eine ausgeblendete PowerShell-Eingabeaufforderung zu vermeiden. Alternativ können Sie auch die [Key Vault-Unterstützung](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/) verwenden. Wählen Sie **Save** (Speichern) aus, um den Vorgang fortzusetzen.

    ![Bearbeiten von Bereitstellungsparametern](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. Wählen Sie im Dialogfeld **In Ressourcengruppe bereitstellen** dann **Bereitstellen** aus. Beachten Sie, dass bei der Aktion das Skript **Deploy-AzureResourceGroup.ps1** ausgeführt wird. Das Fenster **Ausgabe** zeigt den Fortschritt der Bereitstellung.

   ![Ausgabe der Ergebnisse](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Auswerten der VM-Skalierungsgruppe <a name="exploring-your-virtual-machine-scale-set"></a>

Wählen Sie **Ansicht** > **Cloud-Explorer**, um die neue VM-Skalierungsgruppe anzuzeigen. Verwenden Sie falls nötig **Alle aktualisieren**.

![Cloud-Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

Mit dem **Cloud-Explorer** können Sie Azure-Ressourcen in Visual Studio verwalten und gleichzeitig Anwendungen entwickeln. Sie können außerdem Ihre VM-Skalierungsgruppe im [Azure-Portal](https://portal.azure.com) und im [Azure-Ressourcen-Explorer](https://resources.azure.com/) anzeigen.

 Das Portal ist die beste Methode zur Verwaltung Ihrer Azure-Infrastruktur mit einem Webbrowser. Der Azure-Ressourcen-Explorer bietet eine einfache Möglichkeit zum Untersuchen und Debuggen von Azure-Ressourcen. Der Azure-Ressourcen-Explorer bietet die Instanzenansicht und zeigt außerdem PowerShell-Befehle für die Ressourcen an, die Sie sich ansehen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Virtual Machine Scale Sets erfolgreich über Visual Studio bereitgestellt haben, können Sie Ihr Projekt den Anforderungen der Anwendung entsprechend weiter anpassen. Konfigurieren Sie z. B. die automatische Skalierung durch Hinzufügen einer **Insights**-Ressource. Sie können Ihrer Vorlage eine Infrastruktur hinzufügen, z. B. eigenständige virtuelle Computer, oder Anwendungen mithilfe der benutzerdefinierten Skript Erweiterung bereitstellen. Eine gute Quelle für Beispielvorlagen ist das GitHub-Repository für [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates). Suchen Sie nach `vmss`.

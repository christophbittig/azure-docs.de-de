---
title: Speichern von Geheimnissen in einem Schlüsseltresor
description: Informationen zum Speichern von Geheimnissen in einer Azure Key Vault-Instanz und zu deren Verwendung beim Erstellen einer VM, Formel oder Umgebung.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2c1849b6bf2ab5876fcbe0960df9bb8a7004bb78
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398096"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Speichern von Geheimnissen in einem Schlüsseltresor in Azure DevTest Labs
Möglicherweise müssen Sie ein komplexes Geheimnis eingeben, wenn Sie Azure DevTest Labs verwenden. Geheimnisse sind z. B. Kennwörter für Ihre Windows-VMs, öffentliche SSH-Schlüssel für Ihre Linux-VMs oder ein persönliches Zugriffstoken zum Klonen Ihres Git-Repositorys über ein Artefakt. Geheimnisse sind in der Regel lang und weisen Zufallszeichen auf. Die Eingabe kann schwierig und umständlich sein, vor allem, wenn Sie dasselbe Geheimnis mehrmals verwenden.

Um dieses Problem zu lösen und Ihre Geheimnisse an einem sicheren Ort aufzubewahren, unterstützt DevTest Labs die Speicherung von Geheimnissen in einem [Azure-Schlüsseltresor](../key-vault/general/overview.md). Wenn ein Benutzer ein Geheimnis zum ersten Mal speichert, erstellt DevTest Labs in der Ressourcengruppe des Labs automatisch einen Schlüsseltresor und speichert das Geheimnis. DevTest Labs erstellt für jeden Benutzer einen separaten Schlüsseltresor. 

Benutzer des Labs müssen eine Lab-VM erstellen, bevor sie ein Geheimnis im Schlüsseltresor erstellen können. Der Grund ist, dass DevTest Labs Benutzer des Labs mit gültigen Benutzerdokumenten verknüpfen muss. DevTest Labs ermöglicht Benutzern anschließend das Erstellen und Speichern von Geheimnissen in ihren Schlüsseltresoren.


## <a name="save-a-secret-in-azure-key-vault"></a>Speichern eines Geheimnisses in Azure Key Vault
Um Ihr Geheimnis in Azure Key Vault zu speichern, führen Sie die folgenden Schritte aus:

1. Wählen Sie im linken Menü **Meine Geheimnisse** aus.
2. Geben Sie einen **Namen** für das Geheimnis ein. Sie sehen diesen Namen beim Erstellen einer VM, Formel oder Umgebung in der Dropdownliste. 
3. Geben Sie das Geheimnis als **Wert** ein.

    ![Screenshot: Speichern eines Geheimnisses](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Verwenden eines Geheimnisses aus Azure Key Vault
Wenn Sie ein Geheimnis zum Erstellen einer VM, Formel oder Umgebung eingeben, können Sie es entweder manuell eingeben oder ein im Schlüsseltresor gespeichertes Geheimnis auswählen. Um ein in Ihrem Schlüsseltresor gespeichertes Geheimnis zu verwenden, führen Sie die folgenden Aktionen aus:

1. Wählen Sie **Gespeichertes Geheimnis verwenden** aus. 
2. Wählen Sie Ihr Geheimnis aus der Dropdownliste für **Geheimnis auswählen** aus. 

    ![Screenshot der Verwendung eines Geheimnisses bei Erstellung einer VM.](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Verwenden eines Geheimnisses in einer Azure Resource Manager-Vorlage
Sie können den Namen Ihres Geheimnisses in einer Azure Resource Manager-Vorlage angeben, die zum Erstellen eines virtuellen Computers verwendet wird, wie im folgenden Beispiel gezeigt:

![Screenshot der Verwendung eines Geheimnisses in einer Formel oder Umgebung.](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen eines virtuellen Computers zu einem Lab in Azure DevTest Labs](devtest-lab-add-vm.md) 
- [Verwalten von Azure DevTest Labs-Formeln](devtest-lab-manage-formulas.md)
- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)

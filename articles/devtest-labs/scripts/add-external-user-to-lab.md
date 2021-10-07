---
title: 'PowerShell: Hinzufügen eines externen Benutzers zu einem Lab'
description: Dieser Artikel stellt ein PowerShell-Beispielskript vor, das einem Lab in Azure DevTest Labs einen externen Benutzer hinzufügt.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3268e124e72e7532709bf9278d644fade0c205a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651102"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Hinzufügen eines externen Benutzers zu einem Lab in Azure DevTest Labs mithilfe von PowerShell

Dieses PowerShell-Beispielskript fügt einem Lab in Azure DevTest Labs einen externen Benutzer hinzu. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Ein Lab.** Das Skript erfordert ein vorhandenes Lab. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: 

| Get-Help | Notizen |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Wiederholt das Benutzerobjekt aus Azure Active Directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Weist dem angegebenen Dienstprinzipal die angegebene Rolle im angegebenen Bereich zu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).

Weitere PowerShell-Skriptbeispiele für Azure Lab Services finden Sie unter [Azure Lab Services – Beispiele für PowerShell](../samples-powershell.md).

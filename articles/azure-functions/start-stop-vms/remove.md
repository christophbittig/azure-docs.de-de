---
title: 'Entfernen von „VMs starten/beenden v2 (Vorschau)“: Übersicht'
description: In diesem Artikel wird beschrieben, wie Sie das Feature „VMs starten/beenden v2 (Vorschau)“ entfernen.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 1a22faba480eb9c130cc7c883abfc3e53ef58fe5
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970155"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Vorgehensweise zum Entfernen von „VMs starten/beenden v2 (Vorschau)“

Nachdem das Feature „VMs starten/beenden v2 (Vorschau)“ aktiviert wurde, um den Betriebszustand Ihrer Azure-VMs zu verwalten, entscheiden Sie möglicherweise, es nicht mehr zu verwenden. Sie können diese Funktion entfernen, indem Sie die Ressourcengruppe löschen, die dediziert für die Speicherung der folgenden Ressourcen vorgesehen ist, um „VMs starten/beenden v2 (Vorschau)“ zu unterstützen:

- Die Azure Functions-Anwendungen
- Zeitpläne in Azure Logic Apps
- Die Application Insights-Instanz
- Azure Storage-Konto

> [!NOTE]
> Falls bei der Bereitstellung bzw. Verwendung von „VMs starten/beenden v2 (Vorschau)“ Probleme auftreten oder Sie eine Frage haben, können Sie auf [GitHub](https://github.com/microsoft/startstopv2-deployments/issues) ein Problem melden. Das Erstellen eines Azure-Supportvorfalls über die [Azure-Supportwebsite](https://azure.microsoft.com/support/options/) ist für diese Vorschauversion nicht verfügbar. 

## <a name="delete-the-dedicated-resource-group"></a>Löschen der dedizierten Ressourcengruppe

Um die Ressourcengruppe zu löschen, befolgen Sie die Schritte, die im Artikel [Azure Resource Manager-Ressourcengruppe und Löschen von Ressourcen](../../azure-resource-manager/management/delete-resource-group.md) beschrieben sind.

## <a name="next-steps"></a>Nächste Schritte

Informationen, wie Sie diese Funktion erneut bereitstellen, finden Sie unter [Starten/Beenden v2 bereitstellen](deploy.md) (Vorschau).
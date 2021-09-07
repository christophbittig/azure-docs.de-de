---
title: Zuweisen des Zugriffs einer verwalteten Identität auf eine Ressource über das Azure-Portal – Azure AD
description: Hier finden Sie Schritt-für-Schritt-Anleitungen für das Zuweisen einer verwalteten Identität für eine Ressource und für das Zuweisen des Zugriffs auf eine andere Ressource über das Azure-Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/24/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeff666af9d680baa037415cedb19077de9c061f
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966354"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Zuweisen des Zugriffs einer verwalteten Identität auf eine Ressource über das Azure-Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Nachdem Sie eine Azure-Ressource mit einer verwalteten Identität konfiguriert haben, können Sie der verwalteten Identität genau wie bei allen anderen Sicherheitsprinzipalen Zugriff auf eine andere Ressource erteilen. In diesem Artikel wird gezeigt, wie Sie der verwalteten Identität eines virtuellen Azure-Computers oder einer VM-Skalierungsgruppe über das Azure-Portal den Zugriff auf ein Azure-Speicherkonto gewähren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#managed-identity-types)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Verwenden von Azure RBAC zum Zuweisen des Zugriffs einer verwalteten Identität auf eine andere Ressource

Gehen Sie wie folgt vor, wenn Sie die verwaltete Identität auf einer Azure-Ressource (z. B. einem [virtuellen Azure-Computer](qs-configure-portal-windows-vm.md) oder einer [Azure-VM-Skalierungsgruppe](qs-configure-portal-windows-vmss.md)) aktiviert haben:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, unter dem Sie die verwaltete Identität konfiguriert haben.

2. Navigieren Sie zu der gewünschten Ressource, für die Sie die Zugriffssteuerung ändern möchten. In diesem Beispiel wird einem virtuellen Azure-Computer Zugriff auf ein Speicherkonto gewährt. Aus diesem Grund navigieren wir zu dem Speicherkonto.

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Wählen Sie die Rolle und die verwaltete Identität aus. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../../includes/role-based-access-control/media/add-role-assignment-page.png)
     
## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Informationen zum Aktivieren einer verwalteten Identität auf einem virtuellen Azure-Computer finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](qs-configure-portal-windows-vm.md).
- Informationen zum Aktivieren einer verwalteten Identität in einer Azure-VM-Skalierungsgruppe finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe über das Azure-Portal](qs-configure-portal-windows-vmss.md).



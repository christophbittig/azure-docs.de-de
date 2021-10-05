---
title: Verknüpfen einer Partner-ID mit Ihren Power Apps-Konten mithilfe der Azure-Anmeldeinformationen
description: In diesem Artikel erfahren Sie, wie Microsoft-Partner ihre Azure-Anmeldeinformationen verwenden können, um Kunden bei der Verwendung von Microsoft Power Apps zu unterstützen.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7bc61691f0035c9f1aa0361ae3439cfb1e1485e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604396"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Verknüpfen einer Partner-ID mit Ihren Power Apps-Konten

Dieser Artikel hilft Microsoft-Partnern, die Power Apps-Dienstanbieter sind, ihren Dienst mit Kunden in Microsoft Power Apps zu verknüpfen. Wenn Sie (der Microsoft-Partner) Power Apps-Dienste für Ihren Kunden verwalten, konfigurieren und unterstützen, haben Sie Zugriff auf die Umgebung des Kunden. Sie können Ihre Azure-Anmeldeinformationen und einen Partner Admin Link (PAL) verwenden, um Ihre Partnernetzwerk-ID den Kontoanmeldeinformationen zuzuordnen, die für die Dienstbereitstellung verwendet werden.

Mithilfe des PAL kann Microsoft Partner mit Power Apps-Kunden ermitteln und würdigen. Microsoft ordnet die Nutzung basierend auf den Berechtigungen (Power Apps-Rolle) und dem Bereich (Mandant, Ressourcengruppe, Ressource) einer Partnerorganisation zu.

## <a name="get-access-from-your-customer"></a>Erlangen des Zugriffs seitens Ihres Kunden

Bevor Sie Ihre Partner-ID verknüpfen, muss Ihr Kunde Ihnen über eine der folgenden Optionen Zugriff auf seine Power Apps-Ressourcen erteilen:

- **Gastbenutzer:** Ihr Kunde kann Sie als Gastbenutzer hinzufügen und Ihnen eine beliebige Power Apps-Rolle zuweisen. Weitere Informationen finden Sie unter [Hinzufügen von Gastbenutzern aus einem anderen Verzeichnis](../../active-directory/external-identities/what-is-b2b.md).
- **Verzeichniskonto:** Ihr Kunde kann ein Benutzerkonto für Sie in seinem eigenen Verzeichnis erstellen und dem Konto eine beliebige Power Apps-Rolle zuweisen.
- **Dienstprinzipal:** Ihr Kunde kann eine App oder ein Skript von Ihrer Organisation in seinem Verzeichnis erstellen und dieser bzw. diesem eine beliebige Power Apps-Rolle zuweisen. Die Identität der App oder des Skripts wird als Dienstprinzipal bezeichnet.
- **Delegierter Administrator:** Ihr Kunde kann eine Ressourcengruppe delegieren, sodass Ihre Benutzer innerhalb Ihres Mandanten daran arbeiten können. Weitere Informationen finden Sie unter [Für Partner: Stellvertretender Administrator](/power-platform/admin/for-partners-delegated-administrator).

## <a name="link-customer-to-a-partner-id"></a>Verknüpfen eines Partners mit einer Partner-ID

Wenn Sie Zugriff auf die Ressourcen des Kunden haben, können Sie Ihre Microsoft Partner Network-ID (MPN-ID) im Azure-Portal, mit PowerShell oder mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) mit Ihrer Benutzer-ID oder Ihrem Dienstprinzipal verknüpfen. Verknüpfen Sie die Partner-ID mit jedem Kundenmandanten.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wechseln Sie im Azure-Portal zu [Mit einer Partner-ID verknüpfen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade).
1. Geben Sie die [Microsoft Partner Network](https://partner.microsoft.com/)-ID für Ihre Organisation ein. Verwenden Sie die in Ihrem Partnerprofil angezeigte **zugeordnete MPN-ID**. Dies wird in der Regel als [MPN-ID ihres Partnerstandortkontos](/partner-center/account-structure) bezeichnet.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Screenshot: Fenster „Mit einer Partner-ID verknüpfen“" lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Wenn Sie die Partner-ID mit einem anderen Kunden verknüpfen möchten, wechseln Sie das Verzeichnis. Wählen Sie unter **Verzeichnis wechseln** das entsprechende Verzeichnis aus.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Screenshot: Fenster „Verzeichnis + Abonnement“, in dem Sie das Verzeichnis wechseln können" lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID mit PowerShell

Installieren Sie das Azure PowerShell-Modul [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

Melden Sie sich entweder mit dem Benutzerkonto oder mit dem Dienstprinzipal beim Mandanten des Kunden an. Weitere Informationen finden Sie unter [Anmelden mit PowerShell](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Verknüpfen Sie die neue Partner-ID. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation. Verwenden Sie die in Ihrem Partnerprofil angezeigte **zugeordnete MPN-ID**.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

Abrufen der verknüpften Partner-ID

```azurepowershell-interactive
get-AzManagementPartner
```

Aktualisieren der verknüpften Partner-ID

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Löschen der verknüpften Partner-ID

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID mit der Azure-Befehlszeilenschnittstelle

Installieren Sie zunächst die Erweiterung für die Azure-Befehlszeilenschnittstelle.

```azurecli-interactive
az extension add --name managementpartner
```

Melden Sie sich entweder mit dem Benutzerkonto oder mit dem Dienstprinzipal beim Mandanten des Kunden an. Weitere Informationen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).

```azurecli-interactive
az login --tenant TenantName
```

Verknüpfen Sie die neue Partner-ID. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

Abrufen der verknüpften Partner-ID

```azurecli-interactive
az managementpartner show
```

Aktualisieren der verknüpften Partner-ID

```azurecli-interactive
az managementpartner update --partner-id 12345
```

Löschen der verknüpften Partner-ID

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>Nächste Schritte

- In den [häufig gestellten Fragen zu Cost Management + Billing](../cost-management-billing-faq.yml) finden Sie Fragen und Antworten zum Verknüpfen einer Partner-ID mit Power Apps-Konten.
- Schließen Sie sich der Diskussion in der [Microsoft-Partner-Community](https://aka.ms/PALdiscussion) an, um Aktualisierungen zu erhalten oder Feedback zu senden.
- Lesen Sie die [häufig gestellten Fragen zur fortgeschrittenen Spezialisierung bei der Anwendungsentwicklung mit wenig Code](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) für PAL-basierte Power Apps-Zuordnungen.

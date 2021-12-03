---
title: Verknüpfen eines Azure-Kontos mit einer Partner-ID
description: Verfolgen Sie Bindungen mit Azure-Kunden nach, indem Sie eine Partner-ID mit dem Benutzerkonto verknüpfen, mit dem Sie die Ressourcen des Kunden verwalten.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 11/04/2021
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b5951b46448d7bfa1ac5010bd17a0a5e7d98b89f
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577800"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Verknüpfen einer Partner-ID mit Ihren Azure-Konten

Microsoft-Partner stellen Dienste bereit, die Kunden beim Erreichen ihrer Geschäftsziele und der Ziele beim Einsatz von Microsoft-Produkten unterstützen. Wenn die Partner Azure-Dienste im Auftrag des Kunden verwalten, konfigurieren und unterstützen, benötigen sie als Benutzer Zugriff auf die Umgebung des Kunden. Mit dem Partner Admin Link (PAL) können Partner ihre Partnernetzwerk-ID mit den für die Bereitstellung von Diensten verwendeten Anmeldeinformationen verknüpfen.

Mithilfe des PALs kann Microsoft Partner ermitteln und würdigen, die zum Erfolg von Azure-Kunden beitragen. Microsoft kann den Einfluss und den Verbrauch in Azure basierend auf den Berechtigungen des Kontos (Azure-Rolle) sowie auf dem Umfang (Abonnement, Ressourcengruppe, Ressource) Ihrem Unternehmen zuordnen. Wenn eine Gruppe über Azure RBAC-Zugriff verfügt, wird PAL für alle Benutzer in der Gruppe erkannt.

## <a name="get-access-from-your-customer"></a>Erlangen des Zugriffs seitens Ihres Kunden

Bevor Sie Ihre Partner-ID verknüpfen, muss Ihr Kunde Ihnen über eine der folgenden Optionen Zugriff auf ihre Azure-Ressourcen erteilen:

- **Gastbenutzer**: Ihr Kunde kann Sie als Gastbenutzer hinzufügen und Ihnen eine beliebige Azure-Rolle zuweisen. Weitere Informationen finden Sie unter [Hinzufügen von Gastbenutzern aus einem anderen Verzeichnis](../../active-directory/external-identities/what-is-b2b.md).

- **Verzeichniskonto**: Ihr Kunde kann ein Benutzerkonto für Sie in seinem eigenen Verzeichnis erstellen und dem Konto eine beliebige Azure-Rolle zuweisen.

- **Dienstprinzipal**: Ihr Kunde kann eine App oder ein Skript von Ihrer Organisation in seinem Verzeichnis hinzufügen und dieser oder diesem eine beliebige Azure-Rolle zuweisen. Die Identität der App oder des Skripts wird als Dienstprinzipal bezeichnet.

- **Azure Lighthouse**: Ihr Kunde kann ein Abonnement (oder eine Ressourcengruppe) delegieren, sodass Ihre Benutzer innerhalb Ihres Mandanten daran arbeiten können. Weitere Informationen finden Sie unter [Azure Lighthouse](../../lighthouse/overview.md).

## <a name="link-to-a-partner-id"></a>Verknüpfen einer Partner-ID

Wenn Sie Zugriff auf die Ressourcen des Kunden haben, können Sie Ihre Microsoft Partner Network-ID (MPN-ID) im Azure-Portal, mit PowerShell oder mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) mit Ihrer Benutzer-ID oder Ihrem Dienstprinzipal verknüpfen. Verknüpfen Sie die Partner-ID für jeden Kundenmandanten.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID im Azure-Portal

1. Wechseln Sie im Azure-Portal zu [Mit einer Partner-ID verknüpfen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade).

2. Melden Sie sich beim Azure-Portal an.

3. Geben Sie die Microsoft-Partner-ID ein. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation. Verwenden Sie die in Ihrem Partnerprofil angezeigte **zugeordnete MPN-ID**.

   ![Screenshot von „Mit einer Partner-ID verknüpfen“](./media/link-partner-id/link-partner-id01.png)

4. Wenn Sie die Partner-ID für einen anderen Kunden verknüpfen möchten, wechseln Sie das Verzeichnis. Wählen Sie unter **Verzeichnis wechseln** das gewünschte Verzeichnis aus.

   ![Screenshot der Option „Verzeichnis wechseln“](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID mit PowerShell

1. Installieren Sie das PowerShell-Modul [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Melden Sie sich entweder mit dem Benutzerkonto oder mit dem Dienstprinzipal beim Mandanten des Kunden an. Weitere Informationen finden Sie unter [Anmelden mit PowerShell](/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Verknüpfen Sie die neue Partner-ID. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation. Verwenden Sie die in Ihrem Partnerprofil angezeigte **zugeordnete MPN-ID**.


    ```azurepowershell-interactive
    C:\> New-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Abrufen der verknüpften Partner-ID
```azurepowershell-interactive
C:\> Get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualisieren der verknüpften Partner-ID
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Löschen der verknüpften Partner-ID
```azurepowershell-interactive
C:\> Remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Verknüpfen einer neuen Partner-ID mit der Azure-Befehlszeilenschnittstelle
1. Installieren Sie die Erweiterung für die Azure-Befehlszeilenschnittstelle (Azure CLI).

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Melden Sie sich entweder mit dem Benutzerkonto oder mit dem Dienstprinzipal beim Mandanten des Kunden an. Weitere Informationen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Verknüpfen Sie die neue Partner-ID. Die Partner-ID ist die [Microsoft Partner Network](https://partner.microsoft.com/)-ID Ihrer Organisation.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Abrufen der verknüpften Partner-ID
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualisieren der verknüpften Partner-ID
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Löschen der verknüpften Partner-ID
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Nächste Schritte

Schließen Sie sich der Diskussion in der [Microsoft-Partner-Community](https://aka.ms/PALdiscussion) an, um Aktualisierungen zu erhalten oder Feedback zu senden.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Wer kann die Partner-ID verknüpfen?**

Jeder Benutzer der Partnerorganisation, der Azure-Ressourcen des Kunden verwaltet, kann die Partner-ID mit dem Konto verknüpfen.

**Kann eine Partner-ID geändert werden, nachdem sie verknüpft wurde?**

Ja. Eine verknüpfte Partner-ID kann geändert, hinzugefügt oder entfernt werden.

**Was geschieht, wenn ein Benutzer ein Konto bei mehreren Kundenmandanten besitzt?**

Die Verknüpfung zwischen der Partner-ID und dem Konto erfolgt für jeden Kundenmandanten. Verknüpfen Sie die Partner-ID für jeden Kundenmandanten.

Wenn Sie jedoch Kundenressourcen über Azure Lighthouse verwalten, sollten Sie den Link im Mandanten Ihres Dienstanbieters erstellen und dabei ein Konto verwenden, das Zugriff auf die Kundenressourcen hat. Weitere Informationen finden Sie im Thema zum [Verknüpfen der Partner-ID, um die Auswirkungen auf delegierte Ressourcen nachzuverfolgen](../../lighthouse/how-to/partner-earned-credit.md).

**Können andere Partner oder Kunden die Verknüpfung mit der Partner-ID bearbeiten oder entfernen?**

Die Verknüpfung wird auf Benutzerkontoebene zugeordnet. Nur Sie können die Verknüpfung mit der Partner-ID bearbeiten oder entfernen. Weder der Kunde noch andere Partner können die Verknüpfung mit der Partner-ID ändern.

**Welche MPN-ID muss ich verwenden, wenn mein Unternehmen über mehrere MPN-IDs verfügt?**

Verwenden Sie die in Ihrem Partnerprofil angezeigte **zugeordnete MPN-ID**.

**Wo finde ich die beeinflusste Umsatzberichterstattung für die verknüpfte Partner-ID?**

Berichte zur Cloudproduktleistung stehen für Partner im Partner Center unter [Dashboard „My Insights“](https://partner.microsoft.com/membership/reports/myinsights) zur Verfügung. Sie müssen „Partner Admin Link“ als Partnerzuordnungstyp auswählen.

**Warum wird mein Kunde in den Berichten nicht angezeigt?**

Der Kunde wird aus folgenden Gründen in den Berichten nicht angezeigt:

1. Das verknüpfte Benutzerkonto verfügt nicht über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) für ein Azure-Kundenabonnement oder eine entsprechende Ressource.

2. Das Azure-Abonnement, für das der Benutzer über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) verfügt, wird nicht verwendet.

**Funktioniert das Verknüpfen der Partner-ID mit Azure Stack?**

Ja, Sie können Ihre Partner-ID mit Azure Stack verknüpfen.

**Wie kann ich meine Partner-ID verknüpfen, wenn mein Unternehmen über [Azure Lighthouse](../../lighthouse/overview.md) auf Kundenressourcen zugreift?**

Damit Azure Lighthouse-Aktivitäten erkannt werden, müssen Sie Ihre MPN-ID mit mindestens einem Benutzerkonto verknüpfen, das Zugriff auf die einzelnen integrierten Abonnements hat. Dieser Schritt muss im Mandanten Ihres Dienstanbieters und nicht in jedem einzelnen Kundenmandanten ausgeführt werden. Der Einfachheit halber empfehlen wir, ein Dienstprinzipalkonto in Ihrem Mandanten zu erstellen, es mit Ihrer MPN-ID zu verknüpfen und dem Konto dann Zugriff auf jeden Kunden zu gewähren, für den Sie mit einer [integrierten Azure-Rolle, die für PEC (Partner Earned Credit) berechtigt ist](/partner-center/azure-roles-perms-pec), ein Onboarding durchführen. Weitere Informationen finden Sie im Thema zum [Verknüpfen der Partner-ID, um die Auswirkungen auf delegierte Ressourcen nachzuverfolgen](../../lighthouse/how-to/partner-earned-credit.md).

**Wie kann ich meinem Kunden Partner Admin Link (PAL) erklären?**

Mit Partner Admin Link (PAL) kann Microsoft die Partner identifizieren und erkennen, die Kunden bei der Erreichung von Geschäftszielen und beim Schaffen von Wert in der Cloud unterstützen. Kunden müssen zunächst Partnerzugriff auf ihre Azure-Ressource gewähren. Anschließend wird die Microsoft Partner Network-ID (MPN-ID) des Partners zugeordnet. Diese Zuordnung dient als Hilfe für Microsoft. Sie ermöglicht ein besseres Verständnis des Ökosystems der IT-Dienstanbieter und der Optimierung der Tools und Programme, die benötigt werden, um unsere gemeinsamen Kunden bestmöglich unterstützen zu können.

**Welche Daten werden über PAL gesammelt?**

Bei der PAL-Zuordnung zu vorhandenen Anmeldeinformationen werden keine neuen Kundendaten an Microsoft geliefert. Für Microsoft werden lediglich die Telemetriedaten bereitgestellt, wenn ein Partner aktiv an der Azure-Umgebung eines Kunden beteiligt ist. Basierend auf den Daten zu den Berechtigungen (Azure-Rolle) und zum Umfang (Verwaltungsgruppe, Abonnement, Ressourcengruppe, Ressource) des Kontos, die vom Kunden für den Partner bereitgestellt werden, kann Microsoft den Einfluss und den Azure-bezogenen Umsatz aus der Kundenumgebung der Partnerorganisation zuordnen. 

**Wird hierdurch die Sicherheit der Azure-Umgebung eines Kunden beeinträchtigt?**

Bei der PAL-Zuordnung wird lediglich die MPN-ID des Partners den bereits bereitgestellten Anmeldeinformationen hinzugefügt. Es werden keine Berechtigungen (Azure-Rolle) geändert und keine zusätzlichen Daten von Azure-Diensten an den Partner oder an Microsoft gesendet.

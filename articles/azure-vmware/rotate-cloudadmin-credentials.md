---
title: Rotieren der Anmeldeinformationen von Cloudadministratoren für Azure VMware Solution
description: Erfahren Sie, wie Sie die vCenter Server- und NSX-T Manager-Anmeldeinformationen für Ihre private Cloud in Azure VMware Solution rotieren.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 884e3f09e429fff39c0dfafcb5d0c9a7b4915e03
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351500"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Rotieren der Anmeldeinformationen von Cloudadministratoren für Azure VMware Solution

In diesem Artikel rotieren Sie die Cloudadmin-Anmeldeinformationen (vCenter- und NSX-T-Anmeldeinformationen) für Ihre private Azure VMware Solution-Cloud.  Obwohl die Kennwörter für diese Konten nicht ablaufen, können Sie neue erstellen. Nachdem Sie neue Kennwörter generiert haben, müssen Sie den VMware HCX-Connector mit den neuesten übernommenen Anmeldeinformationen aktualisieren.

Sie können sich auch ein Video zum [Zurücksetzen der Kennwörter des vCenter-Cloudadministrators und des NSX-T-Administrators](https://youtu.be/cK1qY3knj88) ansehen. 

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie Ihre Anmeldeinformationen für den Cloudadministrator für verbundene Dienste wie HCX, vRealize Orchestrator, vRealize, Operations Manager oder VMware Horizon verwenden, werden Ihre Verbindungen unterbrochen, sobald Sie Ihr Kennwort aktualisieren.  Beenden Sie daher diese Dienste, bevor Sie die Kennwortrotation initiieren. Andernfalls kann dies zu vorübergehendem Sperren Ihrer Konten für den vCenter-Cloudadministrator und den NSX-T-Administrator führen, da diese Dienste kontinuierlich Aufrufe mit Ihren alten Anmeldeinformationen übermitteln.  Weitere Informationen zur Einrichtung separater Konten für verbundene Dienste finden Sie unter [Zugriffs- und Identitätskonzepte](./concepts-identity.md).

## <a name="reset-your-azure-vmware-solution-cloudadmin-credentials"></a>Zurücksetzen der Anmeldeinformationen für Azure VMware Solution Cloudadmin

In diesem Schritt rotieren Sie die Anmeldeinformationen für den Cloudadministrator Ihrer Azure VMware Solution-Komponenten. 

>[!NOTE]
>Denken Sie daran, **{SubscriptionID}** , **{ResourceGroup}** und **{PrivateCloudName}** durch die Daten Ihrer privaten Cloud zu ersetzen.

1. Öffnen Sie im Azure-Portal eine Azure Cloud Shell-Sitzung.

2. Aktualisieren Sie das Kennwort Ihres vCenter-Cloudadministrators.  

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```
          
3. Aktualisieren Sie das Kennwort Ihres NSX-T-Administrators. 

   ```azurecli-interactive
   az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

## <a name="update-hcx-connector-with-the-latest-cloudadmin-credentials"></a>Aktualisieren des HCX-Connectors mit den neuesten Cloudadmin-Anmeldeinformationen

In diesem Schritt überprüfen Sie, ob der HCX-Connector über die aktualisierten Anmeldeinformationen verfügt.

1. Wechseln Sie unter https://{IP-Adresse_der_HCX-Connectorappliance}:443 zum lokalen HCX-Connector, und melden Sie sich mit den neuen Anmeldeinformationen an.

   Achten Sie darauf, Port 443 zu verwenden. 

2. Wählen Sie auf dem VMware-HCX-Dashboard **Site Pairing** (Websitekopplung) aus.
    
   :::image type="content" source="media/rotate-cloudadmin-credentials/hcx-site-pairing.png" alt-text="Screenshot des VMware-HCX-Dashboards mit hervorgehobener Websitekopplung.":::
 
3. Wählen Sie die richtige Verbindung mit Azure VMware Solution und dann **Verbindung bearbeiten** aus.
 
4. Geben Sie die neuen vCenter Server CloudAdmin-Benutzer-Anmeldeinformationen an und wählen Sie **Bearbeiten** aus. Dadurch werden die Anmeldeinformationen gespeichert. Das Speichern sollte erfolgreich verlaufen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Zurücksetzen von vCenter Server- und NSX-T Manager-Anmeldeinformationen in Azure VMware Solution vertraut gemacht haben, sollten Sie sich den folgenden Themen zuwenden:

- [Integrieren von nativen Azure-Diensten in Azure VMware Solution](integrate-azure-native-services.md)
- [Bereitstellen der Notfallwiederherstellung für Azure VMware Solution Workloads mit VMware HCX](deploy-disaster-recovery-using-vmware-hcx.md)

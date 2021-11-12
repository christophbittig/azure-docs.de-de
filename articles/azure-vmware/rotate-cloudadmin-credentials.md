---
title: Rotieren der Anmeldeinformationen von Cloudadministratoren für Azure VMware Solution
description: Erfahren Sie, wie Sie die vCenter Server-Anmeldeinformationen für Ihre private Cloud in Azure VMware Solution rotieren.
ms.topic: how-to
ms.date: 09/10/2021
ms.openlocfilehash: f8ab35888d2d298d1dcb6acd2abd6d659b782fd7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461765"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Rotieren der Anmeldeinformationen von Cloudadministratoren für Azure VMware Solution

>[!IMPORTANT]
>Derzeit wird das Rotieren der *admin*-Anmeldeinformationen für NSX-T Manager nicht unterstützt.  Senden Sie eine [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support), um Ihr NSX-T Manager-Kennwort zu rotieren. Dieser Prozess kann sich auf die Ausführung von HCX-Diensten auswirken.

In diesem Artikel rotieren Sie die cloudadmin-Anmeldeinformationen (*CloudAdmin*-Anmeldeinformationen in vCenter Server) für Ihre private Azure VMware Solution-Cloud.  Obwohl das Kennwort für dieses Konto nicht abläuft, können Sie jederzeit ein neues generieren.

>[!CAUTION]
>Wenn Sie Ihre cloudadmin-Benutzeranmeldeinformationen verwenden, um Dienste mit vCenter in Ihrer privaten Cloud zu verbinden, werden diese Verbindungen nicht mehr funktionieren, sobald Sie Ihr Kennwort rotieren. In diesen Verbindungen wird auch das cloudadmin-Konto gesperrt, es sei denn, Sie beenden diese Dienste, bevor Sie das Kennwort rotieren.

## <a name="prerequisites"></a>Voraussetzungen

Überlegen und ermitteln Sie, welche Dienste mit vCenter eine Verbindung als *cloudadmin@vsphere.local* herstellen, bevor Sie das Kennwort rotieren. Diese Dienste können VMware-Dienste wie HCX, vRealize Orchestrator, vRealize Operations Manager, VMware Horizon oder andere Drittanbietertools umfassen, die für die Überwachung oder Bereitstellung verwendet werden. 

Eine Möglichkeit zu ermitteln, welche Dienste sich mit dem cloudadmin-Benutzer bei vCenter authentifizieren, besteht darin, vSphere-Ereignisse mithilfe des vSphere-Clients für Ihre private Cloud zu überprüfen. Nachdem Sie derartige Dienste identifiziert haben, müssen Sie sie beenden, bevor Sie das Kennwort rotieren. Andernfalls funktionieren die Dienste nicht mehr, nachdem Sie das Kennwort rotiert haben. Außerdem werden Sie temporäre Sperren für Ihr vCenter CloudAdmin-Konto feststellen, weil diese Dienste kontinuierlich versuchen, sich mithilfe einer zwischengespeicherten Version der alten Anmeldeinformationen zu authentifizieren. 

Anstatt den cloudadmin-Benutzer zu verwenden, um Dienste mit vCenter zu verbinden, empfehlen wir für jeden Dienst einzelne Konten. Weitere Informationen zur Einrichtung separater Konten für verbundene Dienste finden Sie unter [Zugriffs- und Identitätskonzepte](./concepts-identity.md).

## <a name="reset-your-vcenter-credentials"></a>Zurücksetzen Ihrer vCenter-Anmeldeinformationen

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud die Option **Identität**.

1. Klicken Sie auf **Neues Kennwort generieren**.

   :::image type="content" source="media/rotate-cloudadmin-credentials/reset-vcenter-credentials-1.png" alt-text="Screenshot, der die vCenter-Anmeldeinformationen und eine Möglichkeit zum Kopieren oder Generieren eines neuen Kennworts zeigt." lightbox="media/rotate-cloudadmin-credentials/reset-vcenter-credentials-1.png":::

1. Aktivieren Sie das Bestätigungskontrollkästchen, und wählen Sie dann **Kennwort generieren** aus.


### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

So beginnen Sie mit der Verwendung der Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Öffnen Sie in Ihrer privaten Azure VMware Solution Cloud eine Azure Cloud Shell Sitzung.

2. Aktualisieren Sie Ihre vCenter-*CloudAdmin*-Anmeldeinformationen.  Denken Sie daran, **{SubscriptionID}** , **{ResourceGroup}** und **{PrivateCloudName}** durch die Daten Ihrer privaten Cloud zu ersetzen. 

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

---




 
## <a name="update-hcx-connector"></a>Aktualisieren des HCX-Connectors 

1. Wechseln Sie unter https://{IP-Adresse_der_HCX-Connectorappliance}:443 zum lokalen HCX-Connector, und melden Sie sich mit den neuen Anmeldeinformationen an.

   Achten Sie darauf, Port **443** zu verwenden. 

2. Wählen Sie auf dem VMware-HCX-Dashboard **Site Pairing** (Websitekopplung) aus.
    
   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot des VMware-HCX-Dashboards mit hervorgehobener Websitekopplung.":::
 
3. Wählen Sie die richtige Verbindung mit Azure VMware Solution und dann **Verbindung bearbeiten** aus.
 
4. Geben Sie die neuen vCenter-Benutzeranmeldeinformationen an, und klicken Sie auf **Bearbeiten**, um die neuen Anmeldeinformationen zu speichern. Das Speichern sollte erfolgreich verlaufen.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Zurücksetzen von vCenter-Anmeldeinformationen in Azure VMware Solution vertraut gemacht haben, sollten Sie sich den folgenden Themen zuwenden:

- [Integrieren von nativen Azure-Diensten in Azure VMware Solution](integrate-azure-native-services.md)
- [Bereitstellen der Notfallwiederherstellung für Azure VMware Solution Workloads mit VMware HCX](deploy-disaster-recovery-using-vmware-hcx.md)

---
title: Austauschen oder Wechseln von Bereitstellungen in Azure Cloud Services (erweiterter Support)
description: Erfahren Sie mehr über das Austauschen oder Wechseln von Bereitstellungen in Azure Cloud Services (erweiterter Support).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 3321152d5d7b753ddca23a8810f0d1ae1b3d4399
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967020"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Austauschen oder Wechseln von Bereitstellungen in Azure Cloud Services (erweiterter Support)

In Azure Cloud Services (erweiterter Support) können Sie zwei unabhängige Clouddienstbereitstellungen gegeneinander austauschen. Im Gegensatz zu Azure Cloud Services (klassisch) verwendet das Azure Resource Manager-Modell in Azure Cloud Services (erweiterter Support) keine Bereitstellungsslots. Wenn Sie in Azure Cloud Services (erweiterter Support) ein neues Release eines Clouddiensts bereitstellen, können Sie den Clouddienst so konfigurieren, dass er gegen einen vorhandenen Clouddienst in Azure Cloud Services (erweiterter Support) ausgetauscht werden kann.

Nachdem Sie die Bereitstellungen ausgetauscht haben, können Sie Ihr neues Release mithilfe der neuen Clouddienstbereitstellung bereitstellen und testen. Tatsächlich wird bei einem Austausch ein neuer (gestageter) Clouddienst zum Produktionsrelease höhergestuft.

> [!NOTE]
> Sie können nicht zwischen einer Azure Cloud Services-Bereitstellung (klassisch) und einer Azure Cloud Services-Bereitstellung (erweiterter Support) wechseln.

Sie müssen festlegen, dass ein Clouddienst gegen einen anderen austauschbar ist, wenn Sie den zweiten Dienst eines Clouddienstpaars zum ersten Mal bereitstellen. Sobald das zweite Clouddienstpaar bereitgestellt ist, kann es in nachfolgenden Updates nicht mehr gegen einen vorhandenen Clouddienst ausgetauscht werden.

Sie können die Bereitstellungen mithilfe einer ARM-Vorlage (Azure Resource Manager), dem Azure-Portal oder der REST-API austauschen.

Bei der Bereitstellung des zweiten Clouddiensts ist für beide Clouddienste die SwappableCloudService-Eigenschaft so festgelegt, dass sie aufeinander verweisen. In einem kommenden Update dieser Clouddienste muss angegeben werden, dass diese Eigenschaft fehlerhaft ist, wobei eine Fehlermeldung zurückgegeben wird, die angibt, dass die SwappableCloudService-Eigenschaft nicht gelöscht oder aktualisiert werden kann.

Nach dem Festlegen wird die SwappableCloudService-Eigenschaft als schreibgeschützte Eigenschaft behandelt. Sie kann nicht gelöscht oder in einen anderen Wert geändert werden. Das Löschen eines der Clouddienste (des austauschbaren Paars) führt dazu, dass die SwappableCloudService-Eigenschaft des verbleibenden Clouddiensts gelöscht wird.


## <a name="arm-template"></a>ARM-Vorlage

Wenn Sie als Bereitstellungsmethode eine ARM-Vorlage verwenden, legen Sie die Eigenschaft `SwappableCloudService` im `networkProfile` des Objekts `cloudServices` auf die ID des gekoppelten Clouddiensts fest, um den Austausch der Clouddienste zu ermöglichen:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure-Portal

So tauschen Sie eine Bereitstellung im Azure-Portal aus:

1. Wählen Sie im Portalmenü **Cloud Services (erweiterter Support)** oder **Dashboard** aus.
1. Wählen Sie dann den Clouddienst aus, den Sie aktualisieren möchten.
1. Wählen Sie unter **Übersicht** für den Clouddienst die Option **Austauschen** aus:

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Screenshot: Registerkarte „Austausch“ für den Clouddienst":::

1. Überprüfen Sie im Bereich zur Bestätigung des Austauschs die Bereitstellungsinformationen. Klicken Sie dann auf **OK** aus, um die Bereitstellungen auszutauschen:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Screenshot: Bestätigen der Informationen zum Bereitstellungsaustausch":::

Der Bereitstellungsaustausch erfolgt schnell, da sich nur die virtuellen IP-Adresse (VIP) für den bereitgestellten Clouddienst ändert.

Um Computekosten zu sparen, können Sie einen der (als Stagingumgebung für Ihre Anwendungsbereitstellung festgelegten) Clouddienste löschen, nachdem Sie sich vergewissert haben, dass Ihr ausgetauschter Clouddienst erwartungsgemäß funktioniert.

## <a name="rest-api"></a>REST-API

Verwenden Sie den folgenden Befehl und die nachstehend gezeigte JSON-Konfiguration, um mithilfe der [REST-API](https://review.docs.microsoft.com/rest/api/compute/load-balancers/swap-public-ip-addresses?branch=net202102) zu einer neuen Clouddienstbereitstellung in Azure Cloud Services (erweiterter Support) zu wechseln:

```http
POST https://management.azure.com/subscriptions/subid/providers/Microsoft.Network/locations/westus/setLoadBalancerFrontendPublicIpAddresses?api-version=2021-02-01
```

```json
{
  "frontendIPConfigurations": [
    {
      "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb1/frontendIPConfigurations/lbfe1",
      "properties": {
        "publicIPAddress": {
          "id": "/subscriptions/subid/resourceGroups/rg2/providers/Microsoft.Network/publicIPAddresses/pip2"
        }
      }
    },
    {
      "id": "/subscriptions/subid/resourceGroups/rg2/providers/Microsoft.Network/loadBalancers/lb2/frontendIPConfigurations/lbfe2",
      "properties": {
        "publicIPAddress": {
          "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/publicIPAddresses/pip1"
        }
      }
    }
  ]
}
```

## <a name="common-questions-about-swapping-deployments"></a>Häufig gestellte Fragen zum Austauschen der Bereitstellungen

Lesen Sie diese Antworten auf häufig gestellte Fragen zum Bereitstellungsaustausch in Azure Cloud Services (erweiterter Support).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Welche Voraussetzungen müssen erfüllt sein, um zu einer neuen Clouddienstbereitstellung wechseln zu können?

Für einen erfolgreichen Bereitstellungsaustausch in Azure Cloud Services (erweiterter Support) müssen zwei Voraussetzungen erfüllt sein:

* Wenn Sie für einen der austauschbaren Clouddienste eine statische/reservierte IP-Adresse verwenden möchten, muss der andere Clouddienst ebenfalls eine reservierte IP-Adresse verwenden. Andernfalls tritt bei dem Austausch ein Fehler auf.
* Alle Instanzen Ihrer Rollen müssen ausgeführt werden, damit der Austausch erfolgreich durchgeführt werden kann. Um den Status Ihrer Instanzen zu überprüfen, wechseln Sie im Azure-Portal für den neu bereitgestellten Clouddienst zu **Übersicht**, oder verwenden Sie den Befehl `Get-AzRole` in Windows PowerShell.

Updates von Gastbetriebssystemen sowie Dienstreparaturvorgänge können ebenfalls dazu führen, dass ein Bereitstellungsaustausch nicht erfolgreich durchgeführt werden kann. Weitere Informationen finden Sie unter [Behandeln von Bereitstellungsproblemen mit Azure Cloud Services (klassisch)](../cloud-services/cloud-services-troubleshoot-deployment-problems.md).

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Kann ein VIP-Austausch gleichzeitig mit einem anderen Änderungsvorgang durchgeführt werden?

Nein. Ein VIP-Austausch ist eine reine Netzwerkänderung. Die Änderung muss abgeschlossen sein, bevor ein anderer Computevorgang für einen Clouddienst gestartet wird. Das Starten einer Aktualisierung, Löschung oder automatischen Skalierung für einen Clouddienst während eines VIP-Austauschs oder das Auslösen eines VIP-Austauschs während der Durchführung eines anderen Computevorgangs kann dazu führen, dass der Clouddienst in einen nicht behebbaren Fehlerzustand wechselt.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Führt ein Austausch zu einer Downtime für die Anwendung? Wie sollte ich dem begegnen?

Der Austausch von Clouddiensten erfolgt in der Regel schnell, da es sich lediglich um eine Konfigurationsänderung in Azure Load Balancer handelt. In einigen Fällen kann der Austausch jedoch 10 Sekunden oder länger dauern und zu vorübergehenden Verbindungsausfällen führen. Um die Auswirkungen des Austauschs auf Benutzer zu begrenzen, erwägen Sie die Implementierung einer Clientwiederholungslogik.

## <a name="next-steps"></a>Nächste Schritte 

* Informieren Sie sich über die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Azure Cloud Services (erweiterter Support).
* Sehen Sie sich den Abschnitt [Häufig gestellte Fragen zu Azure Cloud Services (erweiterter Support)](faq.yml) an.
* Stellen Sie einen Azure Cloud Services-Clouddienst (erweiterter Support) mithilfe einer der folgenden Optionen bereit:
  * [Azure portal](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM-Vorlage](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)

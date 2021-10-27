---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 6ccdeea7b283ab4e4674e2a7959ead4553c43f2f
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130078017"
---
Wenn Sie eine Azure Machine Learning-__Compute-Instanz__ (mit öffentlicher IP-Adresse) oder einen -__Computecluster__ verwenden, gestatten Sie den eingehenden Datenverkehr von Azure Batch Management- und Azure Machine Learning-Diensten. Compute-Instanzen ohne öffentliche IP-Adresse (Vorschau) erfordern diese eingehende Kommunikation nicht. Eine Netzwerksicherheitsgruppe, die diesen Datenverkehr zulässt, wird dynamisch für Sie erstellt. Sie müssen jedoch möglicherweise auch benutzerdefinierte Routen (UDR) erstellen, wenn Sie über eine Firewall verfügen. Wenn Sie eine benutzerdefinierte Route für diesen Datenverkehr erstellen, können Sie entweder **IP-Adressen** oder **Diensttags** verwenden, um den Datenverkehr weiterzuleiten.

> [!IMPORTANT]
> Die Verwendung von Diensttags mit benutzerdefinierten Routen ist derzeit in der Vorschauversion und wird möglicherweise nicht vollständig unterstützt. Weitere Informationen finden Sie unter [Routing in virtuellen Netzwerken](../articles/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview).

> [!TIP]
> Während eine Compute-Instanz ohne öffentliche IP-Adresse (eine Previewfunktion) für diesen eingehenden Datenverkehr keine UDR erfordert, benötigen Sie diese UDRs weiterhin, wenn Sie auch einen Computecluster oder eine Compute-Instanz mit einer öffentlichen IP-Adresse verwenden.


# <a name="ip-address-routes"></a>[IP-Adressrouten](#tab/ipaddress)

Für den Azure Machine Learning Service müssen Sie die IP-Adresse sowohl den __primären__ als auch den __sekundären__ Regionen hinzufügen. Informationen zum Auffinden der sekundären Region finden Sie im Abschnitt [Sicherstellen von Geschäftskontinuität und Notfallwiederherstellung mit Azure-Regionspaaren](../articles/best-practices-availability-paired-regions.md#azure-regional-pairs). Wenn sich Ihr Azure Machine Learning Service z. B. in „USA, Osten 2“ befindet, ist die sekundäre Region „USA, Mitte“. 

Um eine Liste der IP-Adressen des Batchdiensts und des Azure Machine Learning Service zu erhalten, laden Sie die [Azure-IP-Bereiche und -Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) herunter und suchen Sie in der Datei nach `BatchNodeManagement.<region>` und `AzureMachineLearning.<region>`, wobei `<region>` Ihre Azure-Region angibt.

> [!IMPORTANT]
> Die IP-Adressen können sich im Laufe der Zeit ändern.

Legen Sie beim Erstellen des UDR die Option __Typ des nächsten Hops__ auf __Internet__ fest. Die folgende Abbildung zeigt ein Beispiel für eine IP-Adress-basierte benutzerdefinierte Route (UDR) im Azure-Portal:

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="Abbildung einer benutzerdefinierten Routenkonfiguration":::

# <a name="service-tag-preview-routes"></a>[Diensttagrouten (Vorschau)](#tab/servicetag)

Erstellen Sie benutzerdefinierte Routen für die folgenden Diensttags:

* `AzureMachineLearning`
* `BatchNodeManagement.<region>`, wobei `<region>` Ihre Azure-Region darstellt.

Die folgenden Befehle veranschaulichen das Hinzufügen von Routen für diese Diensttags:

```azurecli
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n AzureMLRoute --address-prefix AzureMachineLearning --next-hop-type Internet
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n BatchRoute --address-prefix BatchNodeManagement.westus2 --next-hop-type Internet
```

---

Informationen zum Konfigurieren von benutzerdefinierten Routen finden Sie unter [Weiterleiten von Netzwerkdatenverkehr mit einer Routingtabelle](/azure/virtual-network/tutorial-create-route-table-portal).

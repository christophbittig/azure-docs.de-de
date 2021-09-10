---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 06/24/2021
ms.author: larryfr
ms.openlocfilehash: 99d885ab35eb04963d9d2b0c6c9c14dbd10f2e2c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559661"
---
> [!IMPORTANT]
> Diese Cosmos DB-Instanz wird in einer von Microsoft verwalteten Ressourcengruppe in __Ihrem Abonnement__ erstellt. Die folgenden Dienste werden ebenfalls in dieser Ressourcengruppe erstellt und von der Konfiguration des kundenseitig verwalteten Schlüssels verwendet:
> * Azure Storage-Konto
> * Azure Search
>
> Da diese Dienste in Ihrem Azure-Abonnement erstellt werden, bedeutet dies, dass Ihnen diese Dienstinstanzen in Rechnung gestellt werden. 
>
> Die verwaltete Ressourcengruppe wird im Format `<AML Workspace Resource Group Name><GUID>` benannt. Wenn im Azure Machine Learning-Arbeitsbereich ein privater Endpunkt verwendet wird, wird in dieser Ressourcengruppe auch ein virtuelles Netzwerk erstellt. Dieses VNet wird verwendet, um die Kommunikation zwischen den Diensten in dieser Ressourcengruppe und Ihrem Azure Machine Learning-Arbeitsbereich zu sichern.
> 
> * __Löschen Sie weder die Ressourcengruppe__, die diese Cosmos DB-Instanz enthält, noch Ressourcen, die automatisch in dieser Gruppe erstellt wurden. Wenn Sie die Ressourcengruppe, die Cosmos DB-Instanz usw. löschen müssen, müssen Sie den Azure Machine Learning-Arbeitsbereich löschen, der diese verwendet. Die Ressourcengruppe, die Cosmos DB-Instanz und andere automatisch erstellte Ressourcen werden gelöscht, wenn der zugehörige Arbeitsbereich gelöscht wird.
> * Die von diesem Cosmos DB-Konto verwendeten [__Anforderungseinheiten__](../articles/cosmos-db/request-units.md) werden bei Bedarf automatisch skaliert. Die __Mindestanzahl__ von RUs beträgt __1200__. Die __Höchstzahl__ von RUs beträgt __12000__.
> * Sie __können kein eigenes VNet für die Verwendung mit der erstellten Cosmos DB-Instanz bereitstellen__. Außerdem __können Sie das virtuelle Netzwerk nicht ändern__. Beispielsweise können Sie den verwendeten IP-Adressbereich nicht ändern.
> 
> Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die zusätzlichen Kosten für die Azure Cosmos DB-Instanz zu schätzen.
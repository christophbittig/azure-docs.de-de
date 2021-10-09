---
title: Skalierungsquoten und -grenzen in Ihrem Labor
description: In diesem Artikel wird beschrieben, wie Sie Ihr Lab in Azure DevTest Labs skalieren. Zeigen Sie Ihre Nutzungskontingente und -limits an, und fordern Sie eine Erhöhung an.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 357e8b4746aa5be368ecf87d12c86014cc525704
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666550"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skalierungskontingente und -beschränkungen in DevTest Labs
Bei der Arbeit mit DevTest Labs werden Sie möglicherweise feststellen, dass bestimmte Standardbeschränkungen für Azure-Ressourcen gelten, die sich auf den DevTest Labs-Dienst auswirken können. Diese Beschränkungen werden als **Kontingente** bezeichnet.

> [!NOTE]
> Für den DevTest Labs-Dienst sind keine Kontingente festgelegt. Bei den Kontingenten, mit denen Sie eventuell konfrontiert werden, handelt es sich um Standardbeschränkungen des allgemeinen Azure-Abonnements.

Sie können jede Azure-Ressource bis zur Ausschöpfung des Kontingents nutzen. Jedes Abonnement verfügt über separate Kontingente; die Nutzungsnachverfolgung erfolgt pro Abonnement.

Jedes Abonnement weist z.B. ein Standardkontingent von 20 Kernen auf. Wenn Sie in Ihrem Lab demnach VMs mit jeweils vier Kernen erstellen, können Sie lediglich fünf VMs erstellen.

Unter [Beschränkungen des Azure-Abonnements und -Diensts](../azure-resource-manager/management/azure-subscription-service-limits.md) werden einige der am häufigsten geltenden Kontingente für Azure-Ressourcen aufgeführt. Zu den am häufigsten in einem Lab verwendeten Ressourcen, für die eventuell Kontingente gelten, zählen VM-Kerne, öffentliche IP-Adressen, Netzwerkschnittstellen, verwaltete Datenträger, Azure-Rollenzuweisung und ExpressRoute-Verbindungen.

## <a name="view-your-usage-and-quotas"></a>Anzeigen von Nutzungsdaten und Kontingenten
In den folgenden Schritten wird gezeigt, wie die aktuellen Kontingente in Ihrem Abonnement für bestimmte Azure-Ressourcen angezeigt werden und der Prozentsatz von jedem Kontingent, das Sie genutzt haben, ermittelt wird.

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie **Weitere Dienste** und in der Liste dann **Abrechnung** aus.
1. Wählen Sie auf dem Blatt „Abrechnung“ ein Abonnement aus.
4. Wählen Sie **Nutzung + Kontingente** aus.

   ![Schaltfläche „Nutzung + Kontingente“](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   Auf dem Blatt „Nutzung + Kontingente“ werden die verschiedenen, in diesem Abonnement verfügbaren Ressourcen sowie der Prozentsatz des Kontingents angezeigt, der pro Ressource genutzt wurde.

   ![Kontingente und Nutzung](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Anfordern von weiteren Ressourcen im Abonnement
Wenn Sie eine Kontingentgrenze erreichen, kann die Standardbeschränkung einer Ressource in einem Abonnement bis zu einem Höchstwert erhöht werden, wie unter [Azure-Abonnement und Dienstbeschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md) beschrieben wird.

In diesen Schritten wird erläutert, wie Sie eine Kontingenterhöhung über das [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) anfordern.

1. Wählen Sie **Weitere Dienste**, **Abrechnung** und dann **Nutzung + Kontingente** aus.
1. Wählen Sie auf dem Blatt „Nutzung + Kontingente“ die Schaltfläche **Erhöhung anfordern** aus.

   ![Schaltfläche „Erhöhung anfordern“](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. Um die Anforderung abzuschließen und zu senden, füllen Sie die erforderlichen Informationen auf allen drei Registerkarten des Formulars **Neue Supportanfrage** aus.

   ![Formular „Erhöhung anfordern“](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

Der Artikel [Grundlegendes zu Azure-Grenzwerten und -Erhöhungen](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) enthält weitere Informationen, um sich zur Anforderung einer Kontingenterhöhung mit dem Azure-Support in Verbindung zu setzen.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Nächste Schritte
* Erkunden Sie den [DevTest Labs-Azure Resource Manager-Katalog mit Schnellstartvorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).

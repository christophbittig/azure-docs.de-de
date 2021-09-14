---
title: Automatisches Hochskalieren von Durchsatzeinheiten in Azure Event Hubs
description: Aktivieren der automatischen Vergrößerung für einen Namespace zur automatischen Hochskalierung der Durchsatzeinheiten (Standard-Tarif).
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 05ea9a76ec5ece9bf522679c85f3671d600d41e8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444098"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units-standard-tier"></a>Automatisches Hochskalieren von Azure Event Hubs-Durchsatzeinheiten (Standard-Tarif) 
Azure Event Hubs ist eine extrem skalierbare Datenstreamingplattform. Daher nimmt die Event Hubs-Nutzung häufig zu, nachdem mit der Nutzung des Diensts begonnen wurde. Für solche Nutzung müssen die vordefinierten [Durchsatzeinheiten](event-hubs-scalability.md#throughput-units) (Throughput Units, TUs) zur Skalierung von Event Hubs und zur Verarbeitung größerer Übertragungsraten erweitert werden. Das Feature **Automatische Vergrößerung** von Event Hubs wird automatisch durch Erhöhung der Anzahl von Durchsatzeinheiten zentral hochskaliert, um den Nutzungsanforderungen gerecht zu werden. Durch die Erweiterung von TUs werden Drosselungsszenarien verhindert, bei denen Folgendes vorliegt:

* Die Datenerfassungsrate überschreitet die festgelegten TUs 
* Die Rate für ausgehende Datenanforderungen überschreitet die festgelegten TUs

Der Event Hubs-Dienst erhöht den Durchsatz, wenn die Last den minimalen Schwellenwert überschreitet, ohne dass Anforderungen mit ServerBusy-Fehlern auftreten.

> [!NOTE]
> Weitere Informationen zum **Premium**-Tarif finden Sie unter [Event Hubs Premium](event-hubs-premium-overview.md).

## <a name="how-auto-inflate-works-in-standard-tier"></a>Funktionsweise der automatischen Vergrößerung im Standard-Tarif
Event Hubs-Datenverkehr wird von TUs gesteuert (Standard-Tarif). Informationen zu den Grenzwerten wie Ein- und Ausgangsraten pro TU finden Sie unter [Event Hubs-Kontingente und -Grenzwerte](event-hubs-quotas.md). Dank der automatischen Vergrößerung können Sie zunächst mit den mindestens erforderlichen Durchsatzeinheiten beginnen. Je nach der Zunahme des Datenverkehrs passt die Funktion die Durchsatzeinheiten automatisch bis zur maximal erforderlichen Anzahl an. Die automatische Vergrößerung hat folgende Vorteile:

- Effizienter Skalierungsmechanismus, der eine Vergrößerung entsprechend der Zunahme des Datenverkehrs ermöglicht
- Automatische Skalierung bis zur festgelegten Obergrenze ohne Probleme aufgrund einer Drosselung
- Mehr Kontrolle über die Skalierung, da Sie bestimmen, wann in welchem Umfang skaliert wird

> [!NOTE]
> Die automatische Vergrößerung skaliert die Anzahl der TUs nicht *automatisch* herunter, wenn die Eingangs- oder Ausgangsrate die Grenzwerte unterschreitet. 

 ## <a name="enable-auto-inflate-on-a-namespace"></a>Aktivieren der automatischen Vergrößerung in einem Namespace
Sie können automatische Vergrößerung für einen Event Hubs-Namespace im Standard-Tarif aktivieren oder deaktivieren, indem Sie das [Azure-Portal](https://portal.azure.com) oder eine [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate) verwenden.

Für einen Premium-Event Hubs-Namespace ist das Feature automatisch aktiviert. Sie können es nicht deaktivieren. 

> [!NOTE]
> Event Hubs-Namespaces des Tarifs Basic unterstützen keine automatische Vergrößerung.

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
Im Azure-Portal können Sie das Feature aktivieren, wenn Sie einen Standard-Event Hubs-Namespace erstellen oder nachdem der Namespace erstellt wurde. Sie können auch TUs für den Namespace festlegen und die maximale Anzahl von TUs angeben. 

Sie können die Funktion für automatische Vergrößerung aktivieren, **wenn Sie einen Event Hubs-Namespace erstellen**. Die folgende Abbildung zeigt, wie Sie das Feature für automatische Vergrößerung für einen Namespace im Standard-Tarif aktivieren, anfängliche TUs konfigurieren und die maximale Anzahl von TUs angeben. 

:::image type="content" source="./media/event-hubs-auto-inflate/event-hubs-auto-inflate.png" alt-text="Screenshot: Aktivieren der automatischen Vergrößerung zum Zeitpunkt der Event Hub-Erstellung für einen Namespace im Standard-Tarif":::

Wenn diese Option aktiviert ist, können Sie mit wenigen Durchsatzeinheiten beginnen und die Anzahl mit zunehmenden Nutzungsanforderungen anpassen. Die Obergrenze für die Vergrößerung hat keine unmittelbaren Auswirkungen auf den Preis. Dieser hängt von der Anzahl von genutzten TUs pro Stunde ab.

Führen Sie die folgenden Schritte aus, um die Funktion für automatische Vergrößerung zu aktivieren bzw. ihre Einstellungen für eine vorhandene Funktion zu ändern:

1. Wählen Sie auf der Seite **Event Hubs-Namespace** im linken Menü unter **Einstellungen** die Option **Skalieren** aus.
2. Aktivieren Sie auf der Seite **Scale Settings** (Skalierungseinstellungen) das Kontrollkästchen **Aktivieren** (sofern die Autoskalierungsfunktion nicht aktiviert war).

    :::image type="content" source="./media/event-hubs-auto-inflate/scale-settings.png" alt-text="Screenshot: Aktivieren von automatischer Vergrößerung für einen vorhandenen Standardnamespace":::
3. Geben Sie die **maximale** Anzahl von Durchsatzeinheiten ein, oder verwenden Sie die Scrollleiste, um den Wert festzulegen.
4. (optional) Aktualisieren Sie die **minimale** Anzahl von Durchsatzeinheiten am oberen Rand dieser Seite.

> [!NOTE]
> Wenn Sie die Konfiguration zur automatischen Vergrößerung anwenden, um die Durchsatzeinheiten zu erhöhen, sendet der Event Hubs-Dienst Diagnoseprotokolle, die Informationen dazu enthalten, warum und wann der Durchsatz gestiegen ist. Um die Diagnoseprotokollierung für einen Event Hub zu aktivieren, wählen Sie im Azure-Portal auf der „Event Hub“-Seite im linken Menü **Diagnoseeinstellungen** aus. Weitere Informationen finden Sie unter [Einrichten von Diagnoseprotokollen für einen Event Hub in Azure](monitor-event-hubs-reference.md#resource-logs).


## <a name="use-an-azure-resource-manager-template"></a>Verwenden einer Azure Resource Manager-Vorlage

Sie können die automatische Vergrößerung bei der Bereitstellung einer Azure Resource Manager-Vorlage aktivieren. Legen Sie beispielsweise die Eigenschaft `isAutoInflateEnabled` auf **TRUE** und `maximumThroughputUnits` auf 10 fest. Beispiele:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Die vollständige Vorlage finden Sie unter [Create Event Hubs namespace and enable inflate (Erstellen eines Event Hubs-Namespace und aktivieren der Vergrößerung)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate) auf GitHub.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](./event-hubs-about.md)

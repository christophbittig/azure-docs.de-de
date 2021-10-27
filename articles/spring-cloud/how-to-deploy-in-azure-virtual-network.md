---
title: Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk
description: Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk (VNET-Einschleusung)
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli, subject-rbac-steps
ms.openlocfilehash: 5d19799d688e8273960b92efb3d60a3afc90bb18
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074169"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In diesem Tutorial wird erläutert, wie Sie eine Azure Spring Cloud-Instanz in Ihrem virtuellen Netzwerk bereitstellen. Diese Bereitstellung wird manchmal als VNET-Einschleusung bezeichnet.

Die Bereitstellung ermöglicht Folgendes:

* Isolierung von Azure Spring Cloud-Apps und der Dienstruntime vom Internet in Ihrem Unternehmensnetzwerk.
* Azure Spring Cloud-Interaktion mit Systemen in lokalen Rechenzentren oder Azure-Diensten in anderen virtuellen Netzwerken.
* Befähigung von Kunden zum Steuern der eingehenden und ausgehenden Netzwerkkommunikation für Azure Spring Cloud.

Im folgenden Video wird beschrieben, wie Sie Spring Boot-Anwendungen mithilfe verwalteter virtueller Netzwerke schützen.

<br>

> [!VIDEO https://www.youtube.com/embed/LbHD0jd8DTQ?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

> [!Note]
> Sie können Ihr virtuelles Azure-Netzwerk nur auswählen, wenn Sie eine neue Azure Spring Cloud-Dienstinstanz erstellen. Nach dem Erstellen der Azure Spring Cloud-Instanz können Sie nicht mehr zu einem anderen virtuellen Netzwerk wechseln.

## <a name="prerequisites"></a>Voraussetzungen

Registrieren der Azure Spring Cloud-Ressourcenanbieter **Microsoft.AppPlatform** und **Microsoft.ContainerService** gemäß den Anweisungen zum [Registrieren des Ressourcenanbieters im Azure-Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) oder durch Ausführung des folgenden Azure CLI-Befehls:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Anforderungen für virtuelle Netzwerke

Das virtuelle Netzwerk, in dem Sie die Azure Spring Cloud-Instanz bereitstellen, muss die folgenden Anforderungen erfüllen:

* **Standort**: Das virtuelle Netzwerk muss sich am selben Ort wie die Azure Spring Cloud-Instanz befinden.
* **Abonnement**: Das virtuelle Netzwerk muss sich im selben Abonnement wie die Azure Spring Cloud-Instanz befinden.
* **Subnetze**: Das virtuelle Netzwerk muss zwei Subnetze enthalten, die für eine Azure Spring Cloud-Instanz bestimmt sind:

    * Ein Subnetz für die Dienstruntime.
    * Ein Subnetz für Ihre Spring Boot-Microserviceanwendungen.
    * Zwischen diesen Subnetzen und einer Azure Spring Cloud-Instanz besteht eine 1:1-Beziehung. Verwenden Sie für jede Dienstinstanz, die Sie bereitstellen, ein neues Subnetz. Jedes Subnetz kann nur eine einzelne Dienstinstanz enthalten.
* **Adressraum:** CIDR-Blöcke bis zu */28* für das Subnetz der Dienstruntime und das Subnetz der Spring Boot-Microserviceanwendungen.
* **Routingtabelle**: Den Subnetzen darf keine vorhandene Routingtabelle zugeordnet sein. Sie können [Ihre eigene Routingtabelle verwenden](#bring-your-own-route-table).

In den folgenden Verfahren wird das Einrichten des virtuellen Netzwerks beschrieben, das die Instanz von Azure Spring Cloud enthalten soll.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

#### <a name="portal"></a>[Portal](#tab/azure-portal)
Wenn Sie bereits über ein virtuelles Netzwerk zum Hosten einer Azure Spring Cloud-Dienstinstanz verfügen, überspringen Sie die Schritte 1, 2 und 3. Sie können mit Schritt 4 beginnen, um Subnetze für das virtuelle Netzwerk vorzubereiten.

1. Wählen Sie im Menü des Azure-Portals die Option **Ressource erstellen** aus. Wählen Sie in Azure Marketplace die Option **Netzwerk** > **Virtuelles Netzwerk** aus.

1. Geben Sie im Dialogfeld **Virtuelles Netzwerk erstellen** die folgenden Informationen an, oder wählen Sie sie aus:

    |Einstellung          |Wert                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |Wählen Sie Ihr Abonnement aus.                         |
    |Resource group   |Wählen Sie Ihre Ressourcengruppe aus, oder erstellen Sie eine neue.  |
    |Name             |Geben Sie **azure-spring-cloud-vnet** ein.                 |
    |Standort         |Wählen Sie **USA, Osten** aus.                               |

1. Klicken Sie auf **Weiter: IP-Adressen**.

1. Geben Sie als „IPv4-Adressraum“ den Adressraum **10.1.0.0/16** ein.

1. Wählen Sie **Subnetz hinzufügen** aus. Geben Sie dann unter **Subnetzname** den Namen **service-runtime-subnet** und unter **Subnetzadressbereich** den Bereich **10.1.0.0/28** ein. Wählen Sie anschließend **Hinzufügen**.

1. Wählen Sie erneut **Subnetz hinzufügen** aus, und geben Sie **Subnetzname** und **Subnetzadressbereich** ein. Geben Sie z. B. **apps-subnet** und **10.1.1.0/28** ein. Wählen Sie anschließend **Hinzufügen**.

1. Klicken Sie auf **Überprüfen + erstellen**. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **Erstellen** aus.

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-CLI)
Wenn Sie bereits über ein virtuelles Netzwerk zum Hosten einer Azure Spring Cloud-Instanz verfügen, überspringen Sie die Schritte 1, 2, 3 und 4. Sie können mit Schritt 5 beginnen, um Subnetze für das virtuelle Netzwerk vorzubereiten.

1. Definieren Sie Variablen für Ihr Abonnement, Ihre Ressourcengruppe und Ihre Azure Spring Cloud-Instanz. Passen Sie die Werte basierend auf Ihrer realen Umgebung an.

   ```azurecli
   SUBSCRIPTION='subscription-id'
   RESOURCE_GROUP='my-resource-group'
   LOCATION='eastus'
   SPRING_CLOUD_NAME='spring-cloud-name'
   VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
   ```

1. Melden Sie sich bei der Azure CLI an, und wählen Sie Ihr aktives Abonnement aus.

   ```azurecli
   az login
   az account set --subscription ${SUBSCRIPTION}
   ```

1. Erstellen Sie eine Ressourcengruppe für Ihre Ressourcen.

   ```azurecli
   az group create --name $RESOURCE_GROUP --location $LOCATION
   ```

1. Erstellen des virtuellen Netzwerks

   ```azurecli
   az network vnet create --resource-group $RESOURCE_GROUP \
       --name $VIRTUAL_NETWORK_NAME \
       --location $LOCATION \
       --address-prefix 10.1.0.0/16
   ```

1. Erstellen Sie zwei Subnetze in diesem virtuellen Netzwerk. 

   ```azurecli
   az network vnet subnet create --resource-group $RESOURCE_GROUP \
       --vnet-name $VIRTUAL_NETWORK_NAME \
       --address-prefixes 10.1.0.0/28 \
       --name service-runtime-subnet 
   az network vnet subnet create --resource-group $RESOURCE_GROUP \
       --vnet-name $VIRTUAL_NETWORK_NAME \
       --address-prefixes 10.1.1.0/28 \
       --name apps-subnet 
   ```

---

## <a name="grant-service-permission-to-the-virtual-network"></a>Erteilen der Dienstberechtigung für das virtuelle Netzwerk

Azure Spring Cloud erfordert die Berechtigung vom Typ **Besitzer** für Ihr virtuelles Netzwerk, damit ein dedizierter und dynamischer Dienstprinzipal im virtuellen Netzwerk für die weitere Bereitstellung und Wartung gewährt werden kann.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie das virtuelle Netzwerk **azure-spring-cloud-vnet** aus, das Sie zuvor erstellt haben.

1. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **Hinzufügen** > **Rollenzuweisung hinzufügen** aus.

    ![Screenshot: Bildschirm „Zugriffssteuerung“.](./media/spring-cloud-v-net-injection/access-control.png)

1. Weisen Sie dem **Azure Spring Cloud-Ressourcenanbieter** die Rolle *Besitzer* zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md#step-2-open-the-add-role-assignment-page).

    ![Screenshot der Zuweisung des Besitzers zum Ressourcenanbieter.](./media/spring-cloud-v-net-injection/assign-owner-resource-provider.png)

    Das gleiche Ergebnis erzielen Sie, wenn Sie den folgenden Azure CLI-Befehl ausführen:

    ```azurecli
    VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
        --name ${NAME_OF_VIRTUAL_NETWORK} \
        --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
        --query "id" \
        --output tsv`

    az role assignment create \
        --role "Owner" \
        --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
        --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
    ```

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-CLI)

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name $VIRTUAL_NETWORK_NAME \
    --resource-group $RESOURCE_GROUP \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

---

## <a name="deploy-an-azure-spring-cloud-instance"></a>Bereitstellen einer Azure Spring Cloud-Instanz

#### <a name="portal"></a>[Portal](#tab/azure-portal)
So stellen Sie die Azure Spring Cloud-Instanz im virtuellen Netzwerk bereit:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Suchen Sie im oberen Suchfeld nach **Azure Spring Cloud**. Wählen Sie in den Ergebnissen **Azure Spring Cloud** aus.

1. Wählen Sie auf der Seite **Azure Spring Cloud** die Option **Hinzufügen** aus.

1. Füllen Sie das Formular auf der Azure Spring Cloud-Seite **Erstellen** aus.

1. Wählen Sie die gleiche Ressourcengruppe und die gleiche Region wie für das virtuelle Netzwerk aus.

1. Wählen Sie unter **Dienstdetails** für **Name** die Option **azure-spring-cloud-vnet** aus.

1. Wählen Sie die Registerkarte **Netzwerk** und dann die folgenden Werte aus:

    |Einstellung                                |Wert                                             |
    |---------------------------------------|--------------------------------------------------|
    |Bereitstellen im eigenen virtuellen Netzwerk     |Wählen Sie **Ja** aus.                                   |
    |Virtuelles Netzwerk                        |Wählen Sie **azure-spring-cloud-vnet** aus.               |
    |Subnetz der Dienstruntime                 |Wählen Sie **service-runtime-subnet** aus.                |
    |Subnetz für Spring Boot-Microservice-Apps   |Wählen Sie **apps-subnet** aus.                           |

    ![Screenshot: Registerkarte „Netzwerk“ auf der Azure Spring Cloud-Erstellungsseite.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Klicken Sie auf **Überprüfen und erstellen**.

1. Überprüfen Sie Ihre Spezifikationen, und wählen Sie **Erstellen** aus.

    ![Screenshot: Überprüfen der Spezifikationen.](./media/spring-cloud-v-net-injection/verify-specifications.png)

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-CLI)
So stellen Sie die Azure Spring Cloud-Instanz im virtuellen Netzwerk bereit:

Erstellen Sie Ihre Azure Spring Cloud-Instanz, indem Sie das virtuelle Netzwerk und die Subnetze angeben, die Sie gerade erstellt haben.

   ```azurecli
   az spring-cloud create  \
       --resource-group "$RESOURCE_GROUP" \
       --name "$SPRING_CLOUD_NAME" \
       --vnet $VIRTUAL_NETWORK_NAME \
       --service-runtime-subnet service-runtime-subnet \
       --app-subnet apps-subnet \
       --enable-java-agent \
       --sku standard \
       --location $LOCATION
   ```

---

Nach der Bereitstellung werden zwei zusätzliche Ressourcengruppen in Ihrem Abonnement erstellt, um die Netzwerkressourcen für die Azure Spring Cloud-Instanz zu hosten. Navigieren Sie zur **Startseite**, und wählen Sie dann **Ressourcengruppen** aus den oberen Menüelementen aus, um die folgenden neuen Ressourcengruppen zu suchen.

Die Ressourcengruppe mit dem Namen **ap-svc-rt_{Dienstinstanzname}_{Dienstinstanzregion}** enthält Netzwerkressourcen für die Dienstruntime der Dienstinstanz.

  ![Screenshot: Dienstruntime.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Die Ressourcengruppe mit dem Namen **ap-app_{Dienstinstanzname}_{Dienstinstanzregion}** enthält Netzwerkressourcen für die Spring Boot-Microserviceanwendungen der Dienstinstanz.

  ![Screenshot: Apps-Ressourcengruppe.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Diese Netzwerkressourcen sind mit dem in der vorherigen Abbildung erstellten virtuellen Netzwerk verbunden.

  ![Screenshot: Virtuelles Netzwerk mit verbundenen Geräten.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Die Ressourcengruppen werden vollständig vom Azure Spring Cloud-Dienst verwaltet. Löschen oder ändern Sie die Ressourcen darin *nicht* manuell.


## <a name="using-smaller-subnet-ranges"></a>Verwenden kleinerer Subnetzbereiche

Diese Tabelle enthält die maximale Anzahl von App-Instanzen, die Azure Spring Cloud mit kleineren Subnetzbereichen unterstützt:

| App-Subnetz-CIDR | IP-Adressen gesamt | Verfügbare IP-Adressen | Maximale Anzahl App-Instanzen                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> App mit 1 Kern:  96 <br/> App mit 2 Kernen: 48<br/>  App mit 3 Kernen: 32 <br/> App mit 4 Kernen: 24 </p> |
| /27             | 32        | 24            | <p> App mit 1 Kern:  228<br/> App mit 2 Kernen: 144<br/>  App mit 3 Kernen: 96 <br/>  App mit 4 Kernen: 72</p> |
| /26             | 64        | 56            | <p> App mit 1 Kern:  500<br/> App mit 2 Kernen: 336<br/>  App mit 3 Kernen: 224<br/>  App mit 4 Kernen: 168</p> |
| /25             | 128       | 120           | <p> App mit 1 Kern:  500<br> App mit 2 Kernen:  500<br>  App mit 3 Kernen:  480<br>  App mit 4 Kernen: 360</p> |
| /24             | 256       | 248           | <p> App mit 1 Kern:  500<br/> App mit 2 Kernen:  500<br/>  App mit 3 Kernen: 500<br/>  App mit 4 Kernen: 500</p> |

Für Subnetze werden fünf IP-Adressen von Azure reserviert, und für Azure Spring Cloud sind mindestens drei IP-Adressen erforderlich. Mindestens acht IP-Adressen sind erforderlich, sodass /29 und /30 nicht betriebsbereit sind.

Bei einem Dienstruntime-Subnetz beträgt die Mindestgröße /28. Diese Größe hat keine Auswirkungen auf die Anzahl der App-Instanzen.

## <a name="bring-your-own-route-table"></a>Eigene Routingtabelle verwenden

Azure Spring Cloud unterstützt die Verwendung vorhandener Subnetze und Routingtabellen.

Wenn Ihre benutzerdefinierten Subnetze keine Routingtabellen enthalten, erstellt Azure Spring Cloud diese für jedes der Subnetze und fügt ihnen während des Instanzlebenszyklus Regeln hinzu. Wenn Ihre benutzerdefinierten Subnetze Routingtabellen enthalten, erkennt Azure Spring Cloud die vorhandenen Routingtabellen bei Instanzvorgängen und fügt Und/Oder-Regeln für Vorgänge entsprechend hinzu bzw. aktualisiert sie.

> [!Warning]
> Benutzerdefinierte Regeln können zu benutzerdefinierten Routingtabellen hinzugefügt und aktualisiert werden. Regeln werden jedoch von Azure Spring Cloud hinzugefügt und dürfen nicht aktualisiert oder entfernt werden. Regeln wie „0.0.0.0/0“ müssen in jeder Routingtabelle enthalten und dem Ziel Ihres Internetgateways (beispielsweise einer virtuellen Netzwerkappliance oder einem anderen Ausgangsgateway) zugeordnet sein. Achten Sie beim Aktualisieren von Regeln unbedingt darauf, dass nur Ihre benutzerdefinierten Regeln geändert werden.

### <a name="route-table-requirements"></a>Anforderungen an die Routingtabelle

Die Routingtabellen, denen Ihr benutzerdefiniertes virtuelles Netzwerk zugeordnet ist, müssen die folgenden Anforderungen erfüllen:

* Sie können Ihre Azure-Routingtabellen nur dann Ihrem virtuellen Netzwerk zuordnen, wenn Sie eine neue Azure Spring Cloud-Dienstinstanz erstellen. Nach dem Erstellen der Azure Spring Cloud-Instanz können Sie nicht mehr zu einer anderen Routingtabelle wechseln.
* Sowohl dem Microserviceanwendungs-Subnetz als auch dem Dienst-Runtime-Subnetz müssen unterschiedliche Routingtabellen zugeordnet sein oder keinem von beiden.
* Vor dem Erstellen einer Instanz müssen Berechtigungen zugewiesen werden. Stellen Sie sicher, dass Sie dem **Azure Spring Cloud-Ressourcenanbieter** die Berechtigung *Besitzer* für Ihre Routentabellen erteilen.
* Die zugeordnete Routingtabellenressource kann nach der Clustererstellung nicht mehr aktualisiert werden. Benutzerdefinierte Regeln in der Routingtabelle können jedoch geändert werden.
* Sie können eine Routingtabelle nicht für mehrere Instanzen verwenden, da es zu Konflikten bei den Routingregeln kommen kann.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen der Anwendung in Azure Spring Cloud in Ihrem VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)
- [Problembehandlung für Azure Spring Cloud im VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)

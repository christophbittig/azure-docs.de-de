---
title: Verwenden eines benutzerdefinierten DNS-Servers
titleSuffix: Azure Machine Learning
description: Konfigurieren eines benutzerdefinierten DNS-Servers für die Arbeit mit einem Azure Machine Learning-Arbeitsbereich und einem privaten Endpunkt.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/29/2021
ms.topic: how-to
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: f683d7192d73cc04b23b58243ba27ccc62afb010
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468630"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Verwenden Ihres Arbeitsbereichs mit einem benutzerdefinierten DNS-Server

Wenn Sie einen Azure Machine Learning-Arbeitsbereich mit einem privaten Endpunkt verwenden, gibt es [verschiedene Möglichkeiten, die DNS-Namensauflösung zu verarbeiten](../private-link/private-endpoint-dns.md). Standardmäßig übernimmt Azure automatisch die Namensauflösung für Ihren Arbeitsbereich und Ihren privaten Endpunkt. Wenn Sie stattdessen __Ihren eigenen benutzerdefinierten DNS-Server__ verwenden, müssen Sie DNS-Einträge für den Arbeitsbereich manuell erstellen oder bedingte Weiterleitungen verwenden.

> [!IMPORTANT]
> In diesem Artikel wird beschrieben, wie Sie die vollqualifizierten Domänennamen (Fully Qualified Domain Namas, FQDN) und IP-Adressen für diese Einträge finden, wenn Sie DNS-Einträge manuell in Ihrer DNS-Lösung registrieren möchten. Darüber hinaus enthält dieser Artikel Architekturempfehlungen zum Konfigurieren Ihrer benutzerdefinierten DNS-Lösung, um FQDNs automatisch in die richtigen IP-Adressen aufzulösen. Dieser Artikel enthält KEINE Informationen zum Konfigurieren der DNS-Einträge für diese Elemente. Weitere Informationen zum Hinzufügen von Einträgen finden Sie in der Dokumentation zu Ihrer DNS-Software.

> [!WARNING]
> Wenn Sie einen privaten Endpunkt für andere Azure-Dienste verwenden, auf denen der Machine Learning-Arbeitsbereich basiert (z. B. Azure Storage, Azure Key Vault oder Azure Container Registry), müssen Sie auch für diese Dienste DNS-Einträge erstellen oder eine bedingte Weiterleitung einrichten. Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](/azure/private-link/private-endpoint-dns).

> [!TIP]
> Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:
>
> * [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
> * [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
> * [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
> * [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
> * [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
> * [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Virtual Network, das [Ihren eigenen DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) verwendet.

- Ein Azure Machine Learning-Arbeitsbereich mit einem privaten Endpunkt. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

- Vertrautheit mit der Verwendung von [Netzwerkisolation während Training und Rückschluss](./how-to-network-security-overview.md).

- Vertrautheit mit der [DNS-Zonenkonfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md)

- Vertrautheit mit [Azure Private DNS](../dns/private-dns-privatednszone.md)

- Optional die [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="automated-dns-server-integration"></a>Automatisierte DNS-Serverintegration

### <a name="introduction"></a>Einführung

Es gibt zwei gängige Architekturen für die Verwendung der automatisierten DNS-Serverintegration mit Azure Machine Learning:

* Ein benutzerdefinierter [DNS-Server, der in einem Azure Virtuelles Netzwerk gehostet wird](#dns-vnet).
* Ein benutzerdefinierter, [lokal gehosteter DNS-Server](#dns-on-premises), der über ExpressRoute mit Azure Machine Learning verbunden ist.

Ihre Architektur kann sich zwar von diesen Beispielen unterscheiden, Sie können sie jedoch als Bezugspunkt verwenden. Beide Beispielarchitekturen bieten Schritte zur Problembehandlung, mit denen Sie Komponenten identifizieren können, die möglicherweise falsch konfiguriert sind.

Eine weitere Möglichkeit besteht in der Änderung der `hosts`-Datei auf dem Client, der eine Verbindung mit dem Azure Virtual Network (VNet) herstellt, das Ihren Arbeitsbereich enthält. Weitere Informationen finden Sie im Abschnitt [Hostdatei](#hosts).
### <a name="workspace-dns-resolution-path"></a>DNS-Auflösungspfad des Arbeitsbereichs

Der Zugriff auf einen bestimmten Azure Machine Learning-Arbeitsbereich über Private Link erfolgt durch die Kommunikation mit den folgenden vollqualifizierten Domänen (als Arbeitsbereichs-FQDNs bezeichnet), die unten aufgeführt sind:

**öffentliche Azure-Regionen**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.azureml.ms```
- ```<compute instance name>.<region the workspace was created in>.instances.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.azure.net```

**Azure China 21Vianet-Regionen**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.cn```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.chinacloudapi.cn```

**Azure US Government-Regionen**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.us```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.usgovcloudapi.net```

Die vollqualifizierten Domänen werden in die folgenden kanonischen Namen (Canonical Names, CNAMEs) namens Arbeitsbereich Private Link FQDNs aufgelöst:

**öffentliche Azure-Regionen**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.azure.net```

**Azure China-Regionen**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.chinacloudapi.cn```

**Azure US Government-Regionen**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.usgovcloudapi.net```

Die FQDNs werden in die IP-Adressen des Azure Machine Learning-Arbeitsbereichs in dieser Region auflösen. Die Auflösung der FQDNs des Arbeitsbereichs „Private Link“ kann jedoch mithilfe eines benutzerdefinierten DNS-Servers, der im virtuellen Netzwerk gehostet wird, außer Kraft gesetzt werden. Ein Beispiel für diese Architektur finden Sie in dem Beispiel für den [benutzerdefinierten DNS-Server, der in einem VNet gehostet wird](#example-custom-dns-server-hosted-in-vnet).

## <a name="manual-dns-server-integration"></a>Manuelle DNS-Serverintegration

In diesem Abschnitt wird erläutert, für welche vollqualifizierten Domänen A-Einträge auf einem DNS-Server erstellt werden und auf welche IP-Adresse der Wert des A-Eintrags festgelegt werden soll.

### <a name="retrieve-private-endpoint-fqdns"></a>Abrufen von FQDNs für private Endpunkte

#### <a name="azure-public-region"></a>Öffentliche Azure-Region

Die folgende Liste enthält den FQDN (vollqualifizierter Domänenname), der von Ihrem Arbeitsbereich verwendet wird, wenn dieser sich in einer Azure Public Cloud befindet:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > Der Arbeitsbereichsname für diesen FQDN wird unter Umständen abgeschnitten. Hierdurch wird das Limit von 63 Zeichen oder weniger für `ml-<workspace-name, truncated>-<region>-<workspace-guid>` eingehalten.
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * Auf Compute-Instanzen kann nur innerhalb des virtuellen Netzwerks zugegriffen werden.
    > * Die IP-Adresse für diesen vollqualifizierten Domänennamen ist **nicht** die IP-Adresse der Compute-Instanz. Verwenden Sie stattdessen die private IP-Adresse des privaten Arbeitsbereichsendpunkts (die IP-Adresse der `*.api.azureml.ms`-Einträge).

#### <a name="azure-china-region"></a>Azure China-Region

Die folgenden FQDNs gelten für Azure China-Regionen:

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > Der Arbeitsbereichsname für diesen FQDN wird unter Umständen abgeschnitten. Hierdurch wird das Limit von 63 Zeichen oder weniger für `ml-<workspace-name, truncated>-<region>-<workspace-guid>` eingehalten.
    
* `<instance-name>.<region>.instances.ml.azure.cn`

   * Die IP-Adresse für diesen vollqualifizierten Domänennamen ist **nicht** die IP-Adresse der Compute-Instanz. Verwenden Sie stattdessen die private IP-Adresse des privaten Arbeitsbereichsendpunkts (die IP-Adresse der `*.api.azureml.ms`-Einträge).

#### <a name="azure-us-government"></a>Azure US Government

Die folgenden FQDNs gelten für Azure US Government-Regionen:

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.us`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.us`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.usgovcloudapi.net`

    > [!NOTE]
    > Der Arbeitsbereichsname für diesen FQDN wird unter Umständen abgeschnitten. Hierdurch wird das Limit von 63 Zeichen oder weniger für `ml-<workspace-name, truncated>-<region>-<workspace-guid>` eingehalten.
* `<instance-name>.<region>.instances.ml.azure.us`
    > * Die IP-Adresse für diesen vollqualifizierten Domänennamen ist **nicht** die IP-Adresse der Compute-Instanz. Verwenden Sie stattdessen die private IP-Adresse des privaten Arbeitsbereichsendpunkts (die IP-Adresse der `*.api.azureml.ms`-Einträge).

### <a name="find-the-ip-addresses"></a>Suchen der IP-Adressen

Verwenden Sie eine der folgenden Methoden, um die internen IP-Adressen für die FQDNs im VNet zu finden:

> [!NOTE]
> Die vollqualifizierten Domänennamen und IP-Adressen variieren je nach Ihrer Konfiguration. Beispielsweise ist der GUID-Wert im Domänennamen spezifisch für Ihren Arbeitsbereich.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Verwenden Sie den folgenden Befehl, um die ID der Netzwerkschnittstelle des privaten Endpunkts zu erhalten:

    ```azurecli
    az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'networkInterfaces[*].id' --output table
    ```

1. Verwenden Sie den folgenden Befehl, um die IP-Adresse und FQDN-Informationen zu erhalten. Ersetzen Sie `<resource-id>` durch die ID aus dem vorherigen Schritt:

    ```azurecli
    az network nic show --ids <resource-id> --query 'ipConfigurations[*].{IPAddress: privateIpAddress, FQDNs: privateLinkConnectionProperties.fqdns}'
    ```

    Die Ausgabedaten ähneln dem folgenden Text:

    ```json
    [
        {
            "FQDNs": [
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms",
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms"
            ],
            "IPAddress": "10.1.0.5"
        },
        {
            "FQDNs": [
            "ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net"
            ],
            "IPAddress": "10.1.0.6"
        }
    ]
    ```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihren Azure Machine Learning-__Arbeitsbereich__ aus.
1. Wählen Sie im Abschnitt __Einstellungen__ die Option __Private Endpunktverbindungen__ aus.
1. Wählen Sie den Link in der Spalte __Privater Endpunkt__ aus, der angezeigt wird.
1. Eine Liste der vollqualifizierten Domänennamen (FQDN) und IP-Adressen für den privaten Endpunkt des Arbeitsbereichs finden Sie unten auf der Seite.

    :::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Liste der FQDNs im Portal":::

    > [!TIP]
    > Wenn die DNS-Einstellungen unten auf der Seite nicht angezeigt werden, verwenden Sie den Link __DNS-Konfiguration__ links auf der Seite, um die FQDNs anzuzeigen.

---

Die Informationen, die von allen Methoden zurückgegeben werden, sind dieselben: Eine Liste des FQDN und der privaten IP-Adresse für die Ressourcen. Das folgende Beispiel ist aus der Azure Public Cloud:

| FQDN | IP-Adresse |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

Die folgende Tabelle enthält Beispiele für IP-Adressen aus Azure China-Regionen:

| FQDN | IP-Adresse |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |

Die folgende Tabelle enthält Beispiele für IP-Adressen aus Azure US Government-Regionen:

| FQDN | IP-Adresse |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.us` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.us` | `10.1.0.5` |
| `ml-mype-plt-usgovvirginia-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.usgovcloudapi.net` | `10.1.0.6` |

<a id='dns-vnet'></a>

### <a name="create-a-records-in-custom-dns-server"></a>Erstellen von A-Einträgen auf einem benutzerdefinierten DNS-Server

Nachdem die Liste der FQDNs und entsprechenden IP-Adressen erfasst wurde, fahren Sie mit dem Erstellen von A-Einträgen auf dem konfigurierten DNS-Server fort. Informationen zum Erstellen von A-Einträgen finden Sie in der Dokumentation für Ihren DNS-Server. Beachten Sie, dass empfohlen wird, eine eindeutige Zone für den gesamten FQDN und den A-Eintrag im Stamm der Zone zu erstellen.

## <a name="example-custom-dns-server-hosted-in-vnet"></a>Beispiel: Benutzerdefinierter DNS-Server, der im VNET gehostet wird

In dieser Architektur wird die allgemeine Hub-and-Spoke-Topologie des virtuellen Netzwerks verwendet. Das eine virtuelle Netzwerk enthält den DNS-Server und das andere den privaten Endpunkt für den Azure Machine Learning-Arbeitsbereich und die zugehörigen Ressourcen. Zwischen beiden virtuellen Netzwerken muss eine gültige Route vorhanden sein. Beispielsweise über eine Reihe von mittels Peering vernetzten virtuellen Netzwerken.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-topology.svg" alt-text="Diagramm des benutzerdefinierten DNS, das in der Azure-Topologie gehostet wird":::

Die folgenden Schritte beschreiben, wie diese Topologie funktioniert:

1. **Erstellen Sie eine Private DNS-Zone und verknüpfen Sie sie mit DNS Server Virtual Network**:

    Der erste Schritt, um sicherzustellen, dass eine benutzerdefinierte DNS-Lösung mit Ihrem Azure Machine Learning-Arbeitsbereich funktioniert, besteht darin, zwei Private DNS-Zonen zu erstellen, die von den folgenden Domänen entfernt sind:

    **öffentliche Azure-Regionen**:
    - ```privatelink.api.azureml.ms```
    - ```privatelink.notebooks.azure.net```
    
    **Azure China-Regionen**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government-Regionen**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    Nach der Erstellung der Private DNS-Zone muss sie mit der DNS-Server-Virtual Network verknüpft werden. Das virtuelle Netzwerk, das den DNS-Server enthält.

    Eine Private DNS-Zone überschreibt die Namensauflösung für alle Namen innerhalb des Bereichs des Stamms der Zone. Diese Außerkraftsetzung gilt für alle virtuellen Netzwerke, mit der Privaten DNS-Zone verknüpft sind. Wenn z. B. eine Private DNS-Zone mit `privatelink.api.azureml.ms`-Stamm mit Virtual Network foo verknüpft ist, erhalten alle Ressourcen in Virtual Network foo, die versuchen, `bar.workspace.westus2.privatelink.api.azureml.ms` aufzulösen, alle in der `privatelink.api.azureml.ms` -Zone aufgeführten Daten.

    Datensätze, die in Privaten DNS-Zonen aufgeführt sind, werden jedoch nur an Geräte zurückgegeben, die Domänen mithilfe der Standard-Azure DNS IP-Adresse des virtuellen Servers auflösen. Daher löst der benutzerdefinierte DNS-Server Domänen für Geräte auf, die in Ihrer Netzwerktopologie verteilt sind. Der benutzerdefinierte DNS-Server muss jedoch Azure Machine Learning-Domänen mit der IP-Adresse des Azure DNS Virtual Servers auflösen.

2. **Erstellen Sie einen privaten Endpunkt mit privater DNS-Integration für die Private DNS-Zone, die mit DNS Server Virtual Network verbunden ist**:

    Im nächsten Schritt erstellen Sie einen privaten Endpunkt für den Azure Machine Learning-Arbeitsbereich. Der private Endpunkt ist für beide Private DNS-Zonen, die in Schritt 1 erstellt wurden. Dadurch wird sichergestellt, dass die gesamte Kommunikation mit dem Arbeitsbereich über den privaten Endpunkt im Azure Machine Learning Virtual Network durchgeführt wird.

    > [!IMPORTANT]
    > Für den privaten Endpunkt muss die Integration von „Privates DNS“ aktiviert sein, damit dieses Beispiel ordnungsgemäß funktioniert.

3. **Erstellen Sie eine bedingte Weiterleitungsanweisung auf dem DNS-Server, um sie an Azure DNS weiterzuleiten**: 

    Erstellen Sie als Nächstes eine bedingte Weiterleitung an den Azure DNS Virtual Server. Die bedingte Weiterleitung stellt sicher, dass der DNS-Server die IP-Adresse Azure DNS virtuellen Servers immer nach FQDNs abfragt, die mit Ihrem Arbeitsbereich verknüpft sind. Dies bedeutet, dass der DNS-Server den entsprechenden Eintrag aus der Private DNS-Zone zurückgibt.

    Die Zonen, die bedingt weitergeleitet werden sollen, sind unten aufgeführt. Die Azure DNS-IP-Adresse des virtuellen Servers lautet 168.63.129.16:

    **öffentliche Azure-Regionen**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.ms```
    
    **Azure China-Regionen**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Azure US Government-Regionen**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Konfigurationsschritte für den DNS-Server sind hier nicht enthalten, da viele DNS-Lösungen verfügbar sind, die als benutzerdefinierter DNS-Server verwendet werden können. Informationen zum ordnungsgemäßen Konfigurieren der bedingten Weiterleitung finden Sie in der Dokumentation zu Ihrer DNS-Lösung.

4. **Auflösen der Arbeitsbereichsdomäne**:

    An diesem Punkt wird das Setup-Programm durchgeführt. Nun kann jeder Client, der DNS-Server für die Namensauflösung verwendet und über eine Route zum privaten Endpunkt von Azure Machine Learning verfügt mit dem Zugriff auf den Arbeitsbereich fortfahren.
    Der Client beginnt zunächst mit der Abfrage des DNS-Servers nach der Adresse der folgenden FQDNs:

    **öffentliche Azure-Regionen**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Azure China-Regionen**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Azure US Government-Regionen**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

5. **Azure DNS löst die Arbeitsbereichsdomäne rekursiv zu CNAME auf**:

    Der DNS-Server wird die FQDNs aus Schritt 4 von Azure DNS auflösen. Azure DNS antwortet mit einer der in Schritt 1 aufgeführten Domänen.

6. **Der DNS-Server löst den CNAME-Eintrag der Arbeitsbereichsdomäne rekursiv aus Azure DNS auf**:

    Der DNS-Server löst den in Schritt 5 empfangenen CNAME rekursiv auf. Da in Schritt 3 eine bedingte Weiterleitung eingerichtet wurde, sendet der DNS-Server die Anforderung zur Auflösung an die IP-Adresse des Azure DNS Virtual Servers.

7. **Azure DNS gibt Datensätze aus Private DNS-Zone zurück**:

    Die entsprechenden Datensätze, die in den Private DNS-Zonen gespeichert sind, werden an den DNS-Server zurückgegeben. Dies bedeutet, dass Azure DNS Virtueller Server die IP-Adressen des privaten Endpunkts zurückgibt.

8. **Der benutzerdefinierte DNS-Server löst den Domänennamen des Arbeitsbereichs in die Adresse des privaten Endpunkts auf**:

    Letztendlich gibt der benutzerdefinierte DNS-Server nun die IP-Adressen des privaten Endpunkts aus Schritt 4 an den Client zurück. Dadurch wird sichergestellt, dass der gesamte Datenverkehr zum Azure Machine Learning-Arbeitsbereich über den privaten Endpunkt erfolgt.

#### <a name="troubleshooting"></a>Problembehandlung

Wenn Sie von einem virtuellen Computer aus nicht auf den Arbeitsbereich zugreifen können oder bei Aufträgen für Computeressourcen im virtuellen Netzwerk Fehler verursachen, führen Sie die folgenden Schritte aus, um die Ursache zu ermitteln:

1. **Suchen Sie die Arbeitsbereichs-FQDNs auf dem privaten Endpunkt**:

    Navigieren Sie über einen der folgenden Links zum Azure-Portal:
    - [öffentliche Azure-Regionen](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure China-Regionen](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure US Government-Regionen](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Navigieren Sie zum privaten Endpunkt zum Azure Machine Learning-Arbeitsbereich. Die FQDNs des Arbeitsbereichs werden auf der Registerkarte „Übersicht“ aufgeführt.

1. **Zugreifen auf Computeressourcen in der Virtual Network-Topologie**:

    Fahren Sie mit dem Zugriff auf eine Computeressource in der Azure Virtual Network-Topologie fort. Dies erfordert wahrscheinlich den Zugriff auf einen virtuellen Computer in einem virtuellen Netzwerk, das mit dem Hub Virtual Network mittels Peering verbunden ist. 

1. **Auflösen der Arbeitsbereich-FQDNs**:

    Öffnen Sie eine Eingabeaufforderung, Shell oder PowerShell. Führen Sie dann für jeden der Arbeitsbereichs-FQDNs den folgenden Befehl aus:

    `nslookup <workspace FQDN>`
        
    Das Ergebnis jedes nslookup-Endpunkts sollte eine der beiden privaten IP-Adressen auf dem privaten Endpunkt an den Azure Machine Learning-Arbeitsbereich zurückgeben. Wenn dies nicht der Fehler ist, wurde in der benutzerdefinierten DNS-Lösung etwas falsch konfiguriert.

    Mögliche Ursachen:
    - Die Computeressource, auf der die Problembehandlungsbefehle ausgeführt werden, verwendet keinen DNS-Server für die DNS-Auflösung
    - Die Private DNS-Zonen, die beim Erstellen des privaten Endpunkts ausgewählt wurden, sind nicht mit dem DNS-Server-VNet verknüpft
    - Die bedingten Weiterleitungen an Azure DNS IP-Adresse des virtuellen Servers wurden nicht ordnungsgemäß konfiguriert

<a id='dns-on-premises'></a>

## <a name="example-custom-dns-server-hosted-on-premises"></a>Beispiel: Benutzerdefinierter DNS-Server, der lokal gehostet wird

In dieser Architektur wird die allgemeine Hub-and-Spoke-Topologie des virtuellen Netzwerks verwendet. ExpressRoute wird verwendet, um eine Verbindung zwischen Ihrem lokalen Netzwerk und dem virtuellen Hubnetzwerk herzustellen. Der benutzerdefinierte DNS-Server wird lokal gehostet. Ein gesondertes virtuelles Netzwerk enthält den privaten Endpunkt für den Azure Machine Learning-Arbeitsbereich und die zugehörigen Ressourcen. Bei dieser Topologie muss ein weiteres virtuelles Netzwerk vorhanden sein, das einen DNS-Server hosten kann, der Anforderungen an die IP-Adresse Azure DNS Virtual Servers senden kann.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-express-route.svg" alt-text="Diagramm der benutzerdefinierten, lokal gehosteten DNS-Topologie":::

Die folgenden Schritte beschreiben, wie diese Topologie funktioniert:

1. **Erstellen Sie eine Private DNS-Zone und verknüpfen Sie sie mit DNS Server Virtual Network**:

    Der erste Schritt, um sicherzustellen, dass eine benutzerdefinierte DNS-Lösung mit Ihrem Azure Machine Learning-Arbeitsbereich funktioniert, besteht darin, zwei Private DNS-Zonen zu erstellen, die von den folgenden Domänen entfernt sind:
    
    **öffentliche Azure-Regionen**:
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Azure China-Regionen**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government-Regionen**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    Nach der Erstellung der Private DNS-Zone muss diese mit dem DNS-Server-VNet verknüpft werden: dem virtuellen Netzwerk, das den DNS-Server enthält. 

    > [!NOTE]
    > Der DNS-Server im virtuellen Netzwerk ist vom lokalen DNS-Server getrennt.

    Eine Private DNS-Zone überschreibt die Namensauflösung für alle Namen innerhalb des Bereichs des Stamms der Zone. Diese Außerkraftsetzung gilt für alle virtuellen Netzwerke, mit der Privaten DNS-Zone verknüpft sind. Wenn z. B. eine Private DNS-Zone mit `privatelink.api.azureml.ms`-Stamm mit Virtual Network foo verknüpft ist, erhalten alle Ressourcen in Virtual Network foo, die versuchen, `bar.workspace.westus2.privatelink.api.azureml.ms` aufzulösen, alle in der privatelink.api.azureml.ms-Zone aufgeführten Daten.

    Datensätze, die in Private DNS-Zonen aufgeführt sind, werden jedoch nur an Geräte zurückgegeben, die Domänen mithilfe der Standard-Azure DNS IP-Adresse des virtuellen Servers auflösen. Die IP-Adresse des Azure DNS Virtual Servers ist nur im Kontext eines virtuellen Netzwerks gültig. Bei Verwendung eines lokalen DNS-Servers kann die IP-Adresse des Azure DNS Virtual Servers nicht abgefragt werden, um Datensätze abzurufen.

    Um dieses Verhalten zu umgehen, erstellen Sie einen zwischengeschalteten DNS-Server in einem virtuellen Netzwerk. Dieser DNS-Server kann die IP-Adresse des Azure DNS Virtual Servers abfragen, um Datensätze für alle Private DNS-Zonen abzurufen, die mit dem virtuellen Netzwerk verknüpft sind.

    Während der lokale DNS-Server Domänen für Geräte auflöst, die in Ihrer Netzwerktopologie verteilt sind, löst er Azure Machine Learning bezogene Domänen für den DNS-Server auf. Der DNS-Server löst diese Domänen aus der IP-Adresse Azure DNS Virtual Servers auf.

2. **Erstellen Sie einen privaten Endpunkt mit privater DNS-Integration für die Private DNS-Zone, die mit DNS Server Virtual Network verbunden ist**:

    Im nächsten Schritt erstellen Sie einen privaten Endpunkt für den Azure Machine Learning-Arbeitsbereich. Der private Endpunkt ist für beide Private DNS-Zonen, die in Schritt 1 erstellt wurden. Dadurch wird sichergestellt, dass die gesamte Kommunikation mit dem Arbeitsbereich über den privaten Endpunkt im Azure Machine Learning Virtual Network durchgeführt wird.

    > [!IMPORTANT]
    > Für den privaten Endpunkt muss die Integration von „Privates DNS“ aktiviert sein, damit dieses Beispiel ordnungsgemäß funktioniert.

3. **Erstellen Sie eine bedingte Weiterleitungsanweisung auf dem DNS-Server, um sie an Azure DNS weiterzuleiten**:

    Erstellen Sie als Nächstes eine bedingte Weiterleitung an den Azure DNS Virtual Server. Die bedingte Weiterleitung stellt sicher, dass der DNS-Server die IP-Adresse Azure DNS virtuellen Servers immer nach FQDNs abfragt, die mit Ihrem Arbeitsbereich verknüpft sind. Dies bedeutet, dass der DNS-Server den entsprechenden Eintrag aus der Private DNS-Zone zurückgibt.

    Die Zonen, die bedingt weitergeleitet werden sollen, sind unten aufgeführt. Die Azure DNS-IP-Adresse des virtuellen Servers lautet 168.63.129.16.

    **öffentliche Azure-Regionen**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```     
    
    **Azure China-Regionen**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```

    **Azure US Government-Regionen**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Konfigurationsschritte für den DNS-Server sind hier nicht enthalten, da viele DNS-Lösungen verfügbar sind, die als benutzerdefinierter DNS-Server verwendet werden können. Informationen zum ordnungsgemäßen Konfigurieren der bedingten Weiterleitung finden Sie in der Dokumentation zu Ihrer DNS-Lösung.

4. **Erstellen Sie eine bedingte Weiterleitung auf dem lokalen DNS-Server, um sie an den DNS-Server weiterzuleiten**:

    Erstellen Sie als Nächstes im DNS Server Virtual Network eine bedingte Weiterleitung an den DNS-Server. Diese Weiterleitung gilt für die in Schritt 1 aufgeführten Zonen. Dies ähnelt Schritt 3, aber anstatt an die IP-Adresse des Azure DNS Virtual Servers weiterzusenden, wird der lokale DNS-Server auf die IP-Adresse des DNS-Servers abzielen. Da sich der lokale DNS-Server nicht in Azure befindet, ist er nicht in der Lage, Datensätze in Private DNS-aufzulösen. In diesem Fall sendet der DNS-Server Proxyanforderungen vom lokalen DNS-Server an die IP-Adresse des Azure DNS Virtual Server. Dadurch kann der lokale DNS-Server Datensätze in den Private DNS-Zonen abrufen, die mit dem DNS Server Virtual Network verknüpft sind. 

    Die Zonen, die bedingt weitergeleitet werden sollen, sind unten aufgeführt. Die IP-Adressen, an die weitergeleitet werden soll, sind die IP-Adressen Ihrer DNS-Server:

    **öffentliche Azure-Regionen**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    **Azure China-Regionen**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Azure US Government-Regionen**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Konfigurationsschritte für den DNS-Server sind hier nicht enthalten, da viele DNS-Lösungen verfügbar sind, die als benutzerdefinierter DNS-Server verwendet werden können. Informationen zum ordnungsgemäßen Konfigurieren der bedingten Weiterleitung finden Sie in der Dokumentation zu Ihrer DNS-Lösung.

5. **Auflösen der Arbeitsbereichsdomäne**:

    An diesem Punkt wird das Setup-Programm durchgeführt. Jeder Client, der lokale DNS-Server für die Namensauflösung verwendet und über eine Route zum privaten Endpunkt von Azure Machine Learning verfügt, kann mit dem Zugriff auf den Arbeitsbereich fortfahren.

    Der Client beginnt zunächst mit der Abfrage des DNS-Servers nach der Adresse der folgenden FQDNs:

    **öffentliche Azure-Regionen**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Azure China-Regionen**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Azure US Government-Regionen**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

6. **Der lokale DNS-Server löst die Arbeitsbereichsdomäne rekursiv auf**:

    Der lokale DNS-Server löst die FQDNs aus Schritt 5 vom DNS-Server auf. Da es eine bedingte Weiterleitung gibt (Schritt 4), sendet der lokale DNS-Server die Anforderung zur Auflösung an den DNS-Server.

7. **Der DNS-Server löst die Arbeitsbereichsdomäne in CNAME aus Azure DNS auf**:

    Der DNS-Server wird die FQDNs aus Schritt 5 von Azure DNS auflösen. Azure DNS antwortet mit einer der in Schritt 1 aufgeführten Domänen.

8. **Der lokale DNS-Server löst den CNAME-Eintrag der Arbeitsbereichsdomäne rekursiv aus dem DNS-Server auf**:

    Der lokale DNS-Server löst den in Schritt 7 empfangenen CNAME rekursiv auf. Da in Schritt 4 eine bedingte Weiterleitung eingerichtet wurde, sendet der lokale DNS-Server die Anforderung zur Auflösung an den DNS-Server.

9. **Der DNS-Server löst den CNAME-Eintrag der Arbeitsbereichsdomäne rekursiv aus Azure DNS auf**:

    Der DNS-Server löst den in Schritt 7 empfangenen CNAME rekursiv auf. Da in Schritt 3 eine bedingte Weiterleitung eingerichtet wurde, sendet der DNS-Server die Anforderung zur Auflösung an die IP-Adresse des Azure DNS Virtual Servers.

10. **Azure DNS gibt Datensätze aus Private DNS-Zone zurück**:

    Die entsprechenden Datensätze, die in den Private DNS-Zonen gespeichert sind, werden an den DNS-Server zurückgegeben. Dies bedeutet, dass der Azure DNS Virtual Server die IP-Adressen des privaten Endpunkts zurückgibt.

11. **Der lokale DNS-Server löst den Domänennamen des Arbeitsbereichs in die Adresse des privaten Endpunkts auf**:

    Die Abfrage vom lokalen DNS-Server zum DNS-Server in Schritt 8 gibt letztendlich die IP-Adressen, die dem privaten Endpunkt zugeordnet sind, an den Azure Machine Learning-Arbeitsbereich zurück. Diese IP-Adressen werden an den ursprünglichen Client zurückgegeben, der nun über den in Schritt 1 konfigurierten privaten Endpunkt mit dem Azure Machine Learning-Arbeitsbereich kommuniziert.

<a id="hosts"></a>
## <a name="example-hosts-file"></a>Beispiel: Datei „hosts“

Die `hosts`-Datei ist ein Textdokument, das Linux, macOS und Windows alle verwenden, um die Namensauflösung für den lokalen Computer außer Kraft zu setzen. Die Datei enthält eine Liste von IP-Adressen und den entsprechenden Hostnamen. Wenn der lokale Computer versucht, einen Hostnamen aufzulösen, wird der Name in die entsprechende IP-Adresse aufgelöst, wenn der Hostname in der `hosts`-Datei aufgeführt ist.

> [!IMPORTANT]
> Die `hosts`-Datei setzt nur die Namensauflösung für den lokalen Computer außer Kraft. Wenn Sie eine `hosts`-Datei mit mehreren Computern verwenden möchten, müssen Sie sie auf jedem Computer einzeln ändern.

In der folgenden Tabelle ist der Speicherort der `hosts`-Datei aufgeführt:

| Betriebssystem | Standort |
| ----- | ----- |
| Linux | `/etc/hosts` |
| macOS | `/etc/hosts` |
| Windows | `%SystemRoot%\System32\drivers\etc\hosts` |

> [!TIP]
> Der Name der `hosts`-Datei hat keine Erweiterung. Verwenden Sie beim Bearbeiten der Datei den Administratorzugriff. Unter Linux oder macOS können Sie z. B. `sudo vi` verwenden. Unter Windows führen Sie Editor als Administrator aus.

Im Folgenden finden Sie ein Beispiel für `hosts`-Dateieinträge für Azure Machine Learning:

```
# For core Azure Machine Learning hosts
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms
10.1.0.6    ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net

# For a compute instance named 'mycomputeinstance'
10.1.0.5    mycomputeinstance.eastus.instances.azureml.ms
```

Weitere Informationen zur `hosts`-Datei finden Sie unter [https://wikipedia.org/wiki/Hosts_(file)](https://wikipedia.org/wiki/Hosts_(file)).

#### <a name="troubleshooting"></a>Problembehandlung

Wenn Sie nach dem Ausführen der oben genannten Schritte nicht von einem virtuellen Computer aus auf den Arbeitsbereich zugreifen können oder Aufträge auf Computeressourcen im virtuellen Netzwerk mit dem privaten Endpunkt für den Azure Machine Learning-Arbeitsbereich fehlschlagen, führen Sie die folgenden Schritte aus, um die Ursache zu ermitteln.

1. **Suchen Sie die Arbeitsbereichs-FQDNs auf dem privaten Endpunkt**:

    Navigieren Sie über einen der folgenden Links zum Azure-Portal:
    - [öffentliche Azure-Regionen](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure China-Regionen](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure US Government-Regionen](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Navigieren Sie zum privaten Endpunkt zum Azure Machine Learning-Arbeitsbereich. Die FQDNs des Arbeitsbereichs werden auf der Registerkarte „Übersicht“ aufgeführt.

1. **Zugreifen auf Computeressourcen in der Virtual Network-Topologie**:

    Fahren Sie mit dem Zugriff auf eine Computeressource in der Azure Virtual Network-Topologie fort. Dies erfordert wahrscheinlich den Zugriff auf einen virtuellen Computer in einem virtuellen Netzwerk, das mit dem Hub Virtual Network mittels Peering verbunden ist. 

1. **Auflösen der Arbeitsbereich-FQDNs**:

    Öffnen Sie eine Eingabeaufforderung, Shell oder PowerShell. Führen Sie dann für jeden der Arbeitsbereichs-FQDNs den folgenden Befehl aus:

    `nslookup <workspace FQDN>`
        
    Das Ergebnis jedes nslookup-Endpunkts sollte eine der beiden privaten IP-Adressen auf dem privaten Endpunkt am Azure Machine Learning-Arbeitsbereich anhalten. Wenn dies nicht der Fehler ist, wurde in der benutzerdefinierten DNS-Lösung etwas falsch konfiguriert.

    Mögliche Ursachen:
    - Die Computeressource, auf der die Problembehandlungsbefehle ausgeführt werden, verwendet keinen DNS-Server für die DNS-Auflösung
    - Die Private DNS-Zonen, die beim Erstellen des privaten Endpunkts ausgewählt wurden, sind nicht mit dem DNS-Server-VNet verknüpft
    - Die bedingten Weiterleitungen vom DNS-Server an Azure DNS IP-Adresse des virtuellen Servers wurden nicht ordnungsgemäß konfiguriert
    - Bedingte Weiterleitungen vom lokalen DNS-Server zum DNS-Server wurden nicht ordnungsgemäß konfiguriert

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

* [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
* [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Schützen der Trainingsumgebung](how-to-secure-training-vnet.md)
* [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
* [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)

Informationen zur Integration privater Endpunkte in Ihre DNS-Konfiguration finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

Informationen zum Bereitstellen von Modellen mit einem benutzerdefinierten DNS-Namen oder TLS-Sicherheit finden Sie unter [Absichern von Webdiensten mit TLS](how-to-secure-web-service.md).

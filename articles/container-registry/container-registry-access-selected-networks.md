---
title: Konfigurieren des öffentlichen Registrierungszugriffs
description: Konfigurieren von IP-Regeln, um den Zugriff auf eine Azure-Containerregistrierung über ausgewählte öffentliche IP-Adressen oder -Adressbereiche zu ermöglichen.
ms.topic: article
ms.date: 07/30/2021
ms.openlocfilehash: 3a4a4a28dfbcd859cf97be6799e24a8110add436
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627005"
---
# <a name="configure-public-ip-network-rules"></a>Konfigurieren von Netzwerkregeln für öffentliche IP-Adressen

Eine Azure-Containerregistrierung akzeptiert standardmäßig Verbindungen über das Internet von Hosts in beliebigen Netzwerken. In diesem Artikel wird gezeigt, wie Sie Ihre Containerregistrierung für den Zugriff von nur bestimmten öffentlichen IP-Adressen oder -Adressbereichen aus konfigurieren. Die Schritte werden jeweils bei Verwendung der Azure-Befehlszeilenschnittstelle (CLI) bzw. des Azure-Portals erläutert.

IP-Netzwerkregeln werden auf dem öffentlichen Registrierungsendpunkt konfiguriert. IP-Netzwerkregeln gelten nicht für private Endpunkte, die mit [Private Link](container-registry-private-link.md) konfiguriert sind.

Das Konfigurieren von IP-Zugriffsregeln steht im Tarif **Premium** der Containerregistrierung zur Verfügung. Weitere Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

Pro Registrierung werden maximal 100 IP-Zugriffsregeln unterstützt.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Zugriff aus einem ausgewählten öffentlichen Netzwerk – CLI

### <a name="change-default-network-access-to-registry"></a>Ändern des Standardnetzwerkzugriffs auf die Registrierung

Um den Zugriff auf ein ausgewähltes öffentliches Netzwerk zu beschränken, ändern Sie zunächst die Standardaktion so, dass der Zugriff verweigert wird. Ersetzen Sie im folgenden Befehl [az acr update][az-acr-update] den Wert durch den Namen Ihrer Registrierung:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Hinzufügen einer Netzwerkregel zur Registrierung

Fügen Sie mit dem Befehl [az acr network-rule add][az-acr-network-rule-add] Ihrer Registrierung eine Netzwerkregel hinzu, die den Zugriff von einer öffentlichen IP-Adresse oder einem öffentlichen IP-Adressbereich erlaubt. Ersetzen Sie beispielsweise den Namen der Containerregistrierung und die öffentliche IP-Adresse eines virtuellen Computers in einem virtuellen Netzwerk.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Nach dem Hinzufügen einer Regel dauert es einige Minuten, bis die Regel wirksam wird.

## <a name="access-from-selected-public-network---portal"></a>Zugriff aus einem ausgewählten öffentlichen Netzwerk – Portal

1. Navigieren Sie im Azure-Portal zu Ihrer Containerregistrierung.
1. Wählen Sie unter **Einstellungen** die Option **Netzwerk** aus.
1. Treffen Sie auf der Registerkarte **Öffentlicher Zugriff** eine Auswahl, um öffentlichen Zugriff von **Ausgewählten Netzwerken** zuzulassen.
1. Geben Sie unter **Firewall** eine öffentliche IP-Adresse ein, z. B. die öffentliche IP-Adresse einer VM in einem virtuellen Netzwerk. Oder geben Sie einen Adressbereich in CIDR-Notation ein, der die IP-Adresse des virtuellen Computers enthält.
1. Wählen Sie **Speichern** aus.

![Konfigurieren einer Firewallregel für die Containerregistrierung][acr-access-selected-networks]

> [!NOTE]
> Nach dem Hinzufügen einer Regel dauert es einige Minuten, bis die Regel wirksam wird.

> [!TIP]
> Aktivieren Sie optional den Registrierungszugriff von einem lokalen Clientcomputer oder einem IP-Adressbereich aus. Um diesen Zugriff zuzulassen, benötigen Sie die öffentliche IPv4-Adresse des Computers. Sie finden diese Adresse, indem Sie in einem Internetbrowser nach „Wie lautet meine IP-Adresse“ suchen. Die aktuelle IPv4-Adresse des Clients wird auch automatisch angezeigt, wenn Sie Firewalleinstellungen auf der Seite **Netzwerk** im Portal konfigurieren.

## <a name="disable-public-network-access"></a>Deaktivieren des Zugriffs auf das öffentliche Netzwerk

Deaktivieren Sie optional den öffentlichen Endpunkt in der Registrierung. Durch das Deaktivieren des öffentlichen Endpunkts werden alle Firewallkonfigurationen außer Kraft gesetzt. Beispielsweise können Sie den öffentlichen Zugriff auf eine in einem virtuellen Netzwerk mithilfe von [Private Link](container-registry-private-link.md) gesicherte Registrierung deaktivieren.

> [!NOTE]
> Wenn die Registrierung in einem virtuellen Netzwerk mit einem [Dienstendpunkt](container-registry-vnet.md) eingerichtet ist, wird durch das Deaktivieren des Zugriffs auf den öffentlichen Endpunkt der Registrierung auch der Zugriff auf die Registrierung im virtuellen Netzwerk deaktiviert.

### <a name="disable-public-access---cli"></a>Deaktivieren des öffentlichen Zugriffs – CLI

Um den öffentlichen Zugriff mithilfe der Azure CLI zu deaktivieren, führen Sie [az acr update][az-acr-update] aus, und legen Sie `--public-network-enabled` auf `false` fest. Das Argument `public-network-enabled` erfordert Azure CLI 2.6.0 oder höher. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Deaktivieren des öffentlichen Zugriffs – Portal

1. Navigieren Sie im Portal zu Ihrer Containerregistrierung, und wählen Sie **Einstellungen > Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Öffentlicher Zugriff** in **Öffentlichen Netzwerkzugriff zulassen** die Option **Deaktiviert** aus. Klicken Sie dann auf **Speichern**.

![Deaktivieren des öffentlichen Zugriffs][acr-access-disabled]


## <a name="restore-public-network-access"></a>Wiederherstellen des öffentlichen Netzwerkzugriffs

Um den öffentlichen Endpunkt neu zu aktivieren, aktualisieren Sie die Netzwerkeinstellungen, um den öffentlichen Zugriff zuzulassen. Durch das Aktivieren des öffentlichen Endpunkts werden alle Firewallkonfigurationen außer Kraft gesetzt. 

### <a name="restore-public-access---cli"></a>Wiederherstellen des öffentlichen Zugriffs – CLI

Führen Sie [az acr update][az-acr-update] aus, und legen Sie `--public-network-enabled` auf `true` fest. 

> [!NOTE]
> Das Argument `public-network-enabled` erfordert Azure CLI 2.6.0 oder höher. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Wiederherstellen des öffentlichen Zugriffs – Portal

1. Navigieren Sie im Portal zu Ihrer Containerregistrierung, und wählen Sie **Einstellungen > Netzwerk** aus.
1. Wählen Sie auf der Registerkarte **Öffentlicher Zugriff** in **Öffentlichen Netzwerkzugriff zulassen** die Option **Alle Netzwerke** aus. Klicken Sie dann auf **Speichern**.

![Öffentlicher Zugriff aus allen Netzwerken][acr-access-all-networks]

## <a name="troubleshoot"></a>Problembehandlung

### <a name="access-behind-https-proxy"></a>Zugriff hinter HTTPS-Proxy

Wenn eine Regel für das öffentliche Netzwerk festgelegt ist oder der öffentliche Zugriff auf die Registrierung verweigert wird, tritt beim Versuch, sich aus dem nicht zulässigen öffentlichen Netzwerk bei der Registrierung anzumelden, ein Fehler auf. Der Clientzugriff aus einer Umgebung hinter einem HTTPS-Proxy ist ebenfalls nicht möglich, wenn keine Zugriffsregel für den Proxy festgelegt ist. Es wird eine Fehlermeldung ähnlich der folgenden angezeigt: `Error response from daemon: login attempt failed with status: 403 Forbidden` oder `Looks like you don't have access to registry`.

Diese Fehler können auch dann auftreten, wenn Sie einen HTTPS-Proxy verwenden, der durch eine Netzwerkzugriffsregel zugelassen ist, aber in der Clientumgebung nicht ordnungsgemäß konfiguriert wurde. Überprüfen Sie, ob sowohl Ihr Docker-Client als auch Ihr Docker-Daemon für das Proxyverhalten konfiguriert sind. Details finden Sie unter [HTTP/HTTPS-Proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) in der Docker-Dokumentation.

### <a name="access-from-azure-pipelines"></a>Zugriff aus Azure Pipelines

Wenn Sie Azure Pipelines mit einer Azure-Containerregistrierung verwenden, die den Zugriff auf bestimmte IP-Adressen beschränkt, kann die Pipeline möglicherweise nicht auf die Registrierung zugreifen, da die ausgehende IP-Adresse von der Pipeline nicht festgelegt ist. Standardmäßig wendet die Pipeline Aufträge mit einem von Microsoft gehosteten [Agent](/azure/devops/pipelines/agents/agents) auf einen VM-Pool mit einem sich ändernden Satz von IP-Adressen an.

Eine Abhilfemaßnahme ist, den für die Ausführung der Pipeline verwendeten Agent von „Von Microsoft gehostet“ in „Selbstgehostet“ zu ändern. Mit einem selbstgehosteten Agent, der auf einem von Ihnen verwalteten [Windows](/azure/devops/pipelines/agents/v2-windows)- oder [Linux](/azure/devops/pipelines/agents/v2-linux)-Computer läuft, kontrollieren Sie die ausgehende IP-Adresse der Pipeline und können diese Adresse einer IP-Zugriffsregel in der Registrierung hinzufügen.

### <a name="access-from-aks"></a>Zugriff aus AKS

Wenn Sie Azure Kubernetes Service (AKS) mit einer Azure-Containerregistrierung verwenden, die den Zugriff auf bestimmte IP-Adressen beschränkt, können Sie standardmäßig keine feste IP-Adresse für AKS konfigurieren. Die ausgehende IP-Adresse aus dem AKS-Cluster wird nach dem Zufallsprinzip zugewiesen.

Um dem AKS-Cluster den Zugriff auf die Registrierung zu ermöglichen, haben Sie die folgenden Optionen:

* Wenn Sie den Azure Load Balancer Basic verwenden, richten Sie eine [statische IP-Adresse](../aks/egress.md) für den AKS-Cluster ein. 
* Wenn Sie den Azure Load Balancer Standard verwenden, konsultieren Sie den Leitfaden zum [Steuern des ausgehenden Datenverkehrs](../aks/limit-egress-traffic.md) aus dem Cluster.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Einschränken des Zugriffs auf eine Registrierung mithilfe eines privaten Endpunkts in einem virtuellen Netzwerk finden Sie unter [Konfigurieren von Azure Private Link für eine Azure-Container Containerregistrierung](container-registry-private-link.md).
* Wenn Sie Zugriffsregeln für die Registrierung hinter einer Clientfirewall einrichten müssen, finden Sie Informationen hierzu unter [Konfigurieren von Regeln für den Zugriff auf eine Azure-Containerregistrierung hinter einer Firewall](container-registry-firewall-access-rules.md).
* Weitere Anleitungen zur Problembehandlung finden Sie unter [Beheben von Netzwerkproblemen mit der Registrierung](container-registry-troubleshoot-access.md).

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png

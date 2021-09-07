---
title: Aktivieren des privaten Zugriffs mit Private Link (Vorschau)
titleSuffix: Azure Digital Twins
description: In diesem Artikel erfahren Sie, wie Sie den privaten Zugriff für Azure Digital Twins-Lösungen mit Private Link aktivieren.
author: baanders
ms.author: baanders
ms.date: 7/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 93435355a2d75a2346d076c44b377726789431a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452333"
---
# <a name="enable-private-access-with-private-link-preview"></a>Aktivieren des privaten Zugriffs mit Private Link (Vorschau)

In diesem Artikel werden verschiedene Methoden zum [Aktivieren von Private Link mit einem privaten Endpunkt für eine Azure Digital Twins-Instanz](concepts-security.md#private-network-access-with-azure-private-link-preview) beschrieben (befindet sich derzeit in der Vorschauphase). Das Konfigurieren eines privaten Endpunkts für Ihre Azure Digital Twins-Instanz ermöglicht es Ihnen, Ihre Azure Digital Twins-Instanz zu schützen und eine öffentliche Offenlegung zu vermeiden. Außerdem wird auch die Datenexfiltration aus Ihrer [Azure Virtual Network-Instanz (virtuelles Netzwerk)](../virtual-network/virtual-networks-overview.md) verhindert.

Die folgenden Schritte werden in diesem Artikel behandelt: 
1. Aktivieren Sie Private Link, und konfigurieren Sie einen privaten Endpunkt für eine Azure Digital Twins-Instanz.
1. Zeigen Sie einen privaten Endpunkt einer Instanz an, und bearbeiten oder löschen Sie ihn.
1. Deaktivieren oder aktivieren Sie Zugriffsflags für das öffentliche Netzwerkzugriff, um den API-Zugriff ausschließlich auf Private Link-Verbindungen einzuschränken.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen privaten Endpunkt einrichten können, benötigen Sie eine [Azure Virtual Network-Instanz (virtuelles Netzwerk)](../virtual-network/virtual-networks-overview.md) , in dem der Endpunkt bereitgestellt werden kann. Wenn Sie noch nicht über ein virtuelles Netzwerk verfügen, können Sie einen [Schnellstart für Azure Virtual Network](../virtual-network/quick-create-portal.md) durchlaufen, um eines einzurichten.

## <a name="add-a-private-endpoint-to-azure-digital-twins"></a>Hinzufügen eines privaten Endpunkts zu Azure Digital Twins 

Sie können entweder das [Azure-Portal](https://portal.azure.com) oder die [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure/what-is-azure-cli) verwenden, um Private Link mit einem privaten Endpunkt für eine Azure Digital Twins-Instanz zu aktivieren. 

Wenn Sie Private Link bei der Ersteinrichtung der Instanz einrichten möchten, müssen Sie das Azure-Portal verwenden. Wenn Sie Private Link für eine Instanz aktivieren möchten, nachdem diese erstellt wurde, können Sie das Azure-Portal oder die Azure CLI verwenden. Die Konfigurationsoptionen und das Endergebnis für Ihre Instanz sind bei beiden Erstellungsmethoden identisch.

Wählen Sie in den folgenden Abschnitten die Registerkarte „Portal“ oder „CLI“ aus, um die Anweisungen für Ihre bevorzugte Methode anzuzeigen.

>[!TIP]
> Sie können einen Private Link-Endpunkt auch über den Private Link-Dienst einrichten, anstatt über Ihre Azure Digital Twins-Instanz. Dies bietet Ihnen dieselben Konfigurationsoptionen und dasselbe Endergebnis.
>
> Weitere Informationen zum Einrichten von Private Link-Ressourcen finden Sie in der Private Link-Dokumentation für das [Azure-Portal](../private-link/create-private-endpoint-portal.md), die [Azure CLI](../private-link/create-private-endpoint-cli.md), [Azure Resource Manager](../private-link/create-private-endpoint-template.md) oder [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Hinzufügen eines privaten Endpunkts beim Erstellen der Instanz

In diesem Abschnitt erstellen Sie einen privaten Endpunkt mit Private Link im Rahmen der Ersteinrichtung einer Azure Digital Twins-Instanz. Dieser Vorgang kann nur im Azure-Portal ausgeführt werden.

# <a name="portal"></a>[Portal](#tab/portal)

In diesem Abschnitt wird beschrieben, wie Sie Private Link beim Einrichten einer Azure Digital Twins-Instanz im Azure-Portal aktivieren. 

Die Private Link-Optionen finden Sie auf der Registerkarte **Netzwerk** des Instanzsetups.

1. Beginnen Sie mit dem Einrichten einer Azure Digital Twins-Instanz im Azure-Portal. Befolgen Sie dazu die Anweisungen unter [Einrichten einer Instanz und der Authentifizierung](how-to-set-up-instance-portal.md).
1. Auf der Registerkarte **Netzwerk** im Einrichtungsprozess für die Instanz können Sie private Endpunkte aktivieren, indem Sie die Option **Privater Endpunkt** als **Verbindungsmethode** auswählen.

    Dadurch wird ein Abschnitt namens **Private Endpunktverbindungen** hinzugefügt, in dem Sie die Details Ihres privaten Endpunkts konfigurieren können. Klicken Sie auf **+ Hinzufügen**, um fortzufahren.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Screenshot des Azure-Portals mit der Registerkarte „Netzwerk“ einer neuen Azure Digital Twins-Instanz, wo hervorgehoben wird, wie ein privater Endpunkt erstellt wird. Die Schaltfläche „Hinzufügen“ ist hervorgehoben." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

1. Die Seite **Privaten Endpunkt erstellen** wird geöffnet. Geben Sie die Details eines neuen privaten Endpunkts ein.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Screenshot: Azure-Portal mit der Seite „Privaten Endpunkt erstellen“ mit den im Folgenden beschriebenen Feldern":::

    1. Wählen Sie Ihr **Abonnement** und Ihre **Ressourcengruppe** aus. Legen Sie denselben **Standort** fest, den Ihr virtuelles Netzwerk aufweist. Geben Sie einen **Namen** für den Endpunkt ein, und wählen Sie für **Target sub-resources** (Untergeordnete Zielressourcen) die Option *API* aus.

    1. Wählen Sie als Nächstes das **virtuelle Netzwerk** und **Subnetz** aus, die Sie zum Bereitstellen des Endpunkts verwenden möchten.

    1. Entscheiden Sie zuletzt, ob Sie die **Integration mit einer privaten DNS-Zone** verwenden möchten. Sie können die Standardoption **Ja** verwenden, oder dem Link im Portal verwenden, um [mehr über die private DNS-Integration zu erfahren](../private-link/private-endpoint-overview.md#dns-configuration).

    1. Nachdem Sie die Konfigurationsoptionen ausgefüllt haben, wählen Sie **OK** aus, um den Vorgang abzuschließen.

1. Daraufhin wird wieder die Registerkarte **Netzwerk** des Einrichtungsprozesses für die Azure Digital Twins-Instanz angezeigt. Vergewissern Sie sich, dass Ihr neuer Endpunkt unter **Verbindungen mit privatem Endpunkt** angezeigt wird.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Screenshot des Azure-Portals mit der Registerkarte „Netzwerk“ eines Azure Digital Twins mit einem neu erstellten privaten Endpunkt." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

1. Verwenden Sie die unteren Navigationsschaltflächen, um mit der Einrichtung der Instanz fortzufahren.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Beim Erstellen einer Instanz mit der Azure CLI ist es nicht möglich, einen Private Link-Endpunkt hinzuzufügen. 

Sie können zum Azure-Portal wechseln, um den Endpunkt während der Erstellung der Instanz hinzuzufügen, oder mit dem nächsten Abschnitt fortfahren, um nach dem Erstellen der Instanz mithilfe der CLI einen privaten Endpunkt hinzuzufügen.

--- 

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Hinzufügen eines privaten Endpunkts zu einer vorhandenen Instanz

In diesem Abschnitt aktivieren Sie Private Link mit einem privaten Endpunkt für eine Azure Digital Twins-Instanz, die bereits vorhanden ist.

# <a name="portal"></a>[Portal](#tab/portal)

1. Öffnen Sie zunächst das [Azure-Portal](https://portal.azure.com) in einem Browser. Rufen Sie Ihre Azure Digital Twins-Instanz auf, indem Sie mithilfe der Suchleiste des Portals nach ihrem Namen suchen.

1. Klicken Sie im linken Menü auf **Netzwerk (Vorschau)** .

1. Wechseln Sie zur Registerkarte **Privater Endpunktverbindungen**.

1. Klicken Sie auf **+ Privater Endpunkt**, um das Dialogfeld **Privaten Endpunkt erstellen** zu öffnen.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Screenshot des Azure-Portals mit der Seite „Netzwerk“ für eine vorhandene Azure Digital Twins-Instanz, der hervorhebt, wie private Endpunkte erstellt werden." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Geben Sie auf der Registerkarte  **Grundlagen** das **Abonnement** und die **Ressourcengruppe** Ihres Projekts sowie einen **Namen** und eine **Region** für Ihren Endpunkt an. Die Region muss mit der Region des verwendeten virtuellen Netzwerks übereinstimmen.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Screenshot: Azure-Portal mit der Registerkarte „Grundlagen“ des Dialogfelds „Privaten Endpunkt erstellen“ und den oben beschriebenen Feldern":::

    Wenn Sie fertig sind, klicken Sie auf **Weiter: Ressource >** , um zur nächsten Registerkarte zu gelangen.

1. Geben Sie auf der Registerkarte **Ressource** die folgenden Informationen an: 
    * **Verbindungsmethode:** Klicken Sie auf **Connect to an Azure resource in my directory** (Verbindung mit einer Azure-Ressource in meinem Verzeichnis herstellen), um nach Ihrer Azure Digital Twins-Instanz zu suchen.
    * **Abonnement**: Geben Sie Ihr Abonnement an.
    * **Ressourcentyp:** Wählen Sie die Option **Microsoft.DigitalTwins/digitalTwinsInstances** aus.
    * **Ressource:** Wählen Sie den Namen Ihrer Azure Digital Twins-Instanz aus.
    * **Target sub-resource** (Untergeordnete Zielressourcen): Wählen Sie die Option **API** aus.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Screenshot: Azure-Portal mit der zweiten Registerkarte „Ressource“ des Dialogfelds „Privaten Endpunkt erstellen“ und den oben beschriebenen Feldern":::

    Wenn Sie fertig sind, klicken Sie auf **Weiter: Konfiguration >** , um zur nächsten Registerkarte zu gelangen.    

1. Geben Sie die folgenden Informationen auf der Registerkarte  **Konfiguration** an:
    * **Virtuelles Netzwerk:** Wählen Sie Ihr virtuelles Netzwerk aus.
    * **Subnetz**: Wählen Sie ein Subnetz Ihres virtuellen Netzwerks aus.
    * **Integration in eine private DNS-Zone**: Entscheiden Sie, ob Sie die **Integration mit einer privaten DNS-Zone** verwenden möchten. Sie können die Standardoption **Ja** verwenden, oder dem Link im Portal verwenden, um [mehr über die private DNS-Integration zu erfahren](../private-link/private-endpoint-overview.md#dns-configuration).
    Wenn Sie **Ja** auswählen möchten, können Sie die Standardoption beibehalten.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Screenshot: Azure-Portal mit der dritte Registerkarte „Konfiguration“ des Dialogfelds „Privaten Endpunkt erstellen“ und den oben beschriebenen Feldern":::

    Wenn Sie fertig sind, können Sie auf **Überprüfen und erstellen** klicken, um das Setup abzuschließen. 

1. Überprüfen Sie auf der Registerkarte **Überprüfen und erstellen** Ihre Angaben, und klicken Sie dann auf  **Erstellen**. 

Wenn die Bereitstellung des Endpunkts abgeschlossen ist, sollte dieser unter den privaten Endpunktverbindungen für Ihre Azure Digital Twins-Instanz angezeigt werden.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Verwenden Sie den Befehl [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create), um mithilfe der Azure CLI einen privaten Endpunkt zu erstellen und mit einer Azure Digital Twins-Instanz zu verknüpfen. Identifizieren Sie die Azure Digital Twins-Instanz mithilfe ihrer vollqualifizierten ID im Parameter `--private-connection-resource-id`.

Im folgenden Beispiel wird der Befehl zum Erstellen eines privaten Endpunkts mit nur den erforderlichen Parametern verwendet.

```azurecli-interactive
az network private-endpoint create --connection-name <private-link-service-connection> --name <name-for-private-endpoint> --resource-group <resource-group> --subnet <subnet-ID> --private-connection-resource-id "/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<Azure-Digital-Twins-instance-name>" 
```

Eine vollständige Liste der erforderlichen und optionalen Parameter sowie weitere Beispiele zur Erstellung privater Endpunkte finden Sie in der Referenzdokumentation zu [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create).

--- 

## <a name="manage-private-endpoint-connections"></a>Verwalten von Verbindungen mit dem privaten Endpunkt

In diesem Abschnitt erfahren Sie, wie Sie einen privaten Endpunkt anzeigen, bearbeiten und löschen, nachdem er erstellt wurde.

# <a name="portal"></a>[Portal](#tab/portal)

Nachdem Sie einen privaten Endpunkt für Ihre Azure Digital Twins-Instanz erstellt haben, können Sie diesen auf der Registerkarte **Netzwerk (Vorschau)** für Ihre Azure Digital Twins-Instanz anzeigen. Auf dieser Seite werden alle Verbindungen mit privaten Endpunkten angezeigt, die der Instanz zugeordnet sind.

:::image type="content" source="media/how-to-enable-private-link/view-endpoint-digital-twins.png" alt-text="Screenshot der Seite „Netzwerk“ im Azure-Portal für eine vorhandene Azure Digital Twins-Instanz mit einem privaten Endpunkt" lightbox="media/how-to-enable-private-link/view-endpoint-digital-twins.png":::


Wählen Sie den Endpunkt aus, um detaillierte Informationen anzuzeigen, Änderungen an den Konfigurationseinstellungen vorzunehmen oder die Verbindung zu löschen.

>[!TIP]
> Der Endpunkt kann auch über Private Link Center im Azure-Portal angezeigt werden.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Sobald ein privater Endpunkt für Ihre Azure Digital Twins-Instanz erstellt wurde, können Sie den Befehl [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) verwenden, um mit der Verwaltung **privater Endpunktverbindungen** in Bezug auf die Instanz fortzufahren. Folgende Vorgänge sind möglich:
* Anzeigen einer privaten Endpunktverbindung
* Festlegen des Zustands der privaten Endpunktverbindung
* Löschen der privaten Endpunktverbindung
* Auflisten aller privaten Endpunktverbindungen für eine Instanz

Weitere Informationen und Beispiele finden Sie in der Referenzdokumentation zu [az dt network private-endpoint](/cli/azure/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true).

### <a name="get-additional-private-link-information"></a>Abrufen zusätzlicher Private Link-Informationen

Sie können weitere Informationen zum Private Link-Status Ihrer Instanz mithilfe des Befehls [az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true) abrufen. Folgende Vorgänge sind möglich:
* Auflisten privater Verbindungen einer Azure Digital Twins-Instanz
* Anzeigen einer privaten Verbindung der Instanz

Weitere Informationen und Beispiele finden Sie in der Referenzdokumentation zu [az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true).

--- 

## <a name="disable--enable-public-network-access-flags"></a>Deaktivieren und Aktivieren von Zugriffsflags für das öffentliche Netzwerk

Sie können Ihre Azure Digital Twins-Instanz so konfigurieren, dass alle öffentlichen Verbindungen verweigert und nur Verbindungen über private Endpunkte zugelassen werden, um die Netzwerksicherheit zu erhöhen. Diese Aktion erfolgt mithilfe eines **Zugriffsflags für das öffentliche Netzwerk**. 

Mithilfe dieser Richtlinie können Sie den API-Zugriff auf Private Link-Verbindungen einschränken. Wenn das Zugriffsflag für das öffentliche Netzwerk auf *deaktiviert* festgelegt wird, geben alle REST-API-Aufrufe der Azure Digital Twins-Instanzdatenebene über die öffentliche Cloud `403, Unauthorized` zurück. Wenn die Richtlinie auf *deaktiviert* festgelegt ist und eine Anforderung über einen privaten Endpunkt erfolgt, ist der API-Aufruf erfolgreich.

Sie können den Wert des Netzwerkflags im [Azure-Portal](https://portal.azure.com), mit der [Azure CLI](/cli/azure/) oder mit dem [ARMClient-Befehlstool](https://github.com/projectkudu/ARMClient) ändern.

# <a name="portal"></a>[Portal](#tab/portal-2)

Zum Deaktivieren oder Aktivieren des öffentlichen Netzwerkzugriffs über das [Azure-Portal](https://portal.azure.com) öffnen Sie das Portal, und navigieren Sie zu Ihrer Azure Digital Twins-Instanz.

1. Klicken Sie im linken Menü auf **Netzwerk (Vorschau)** .

1. Legen Sie auf der Registerkarte **Öffentlicher Zugriff** für **Allow public network access to** (Öffentlichen Netzwerkzugriff zulassen auf) entweder **Deaktiviert** oder **Alle Netzwerke** fest.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Screenshot des Azure-Portals mit der Seite „Netzwerk“ für eine Azure Digital Twins-Instanz, der hervorhebt, wie der öffentliche Zugriff umgeschaltet wird." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Wählen Sie **Speichern** aus.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli-2)

In der Azure CLI können Sie den öffentlichen Netzwerkzugriff deaktivieren oder aktivieren, indem Sie einen `--public-network-access`-Parameter zum `az dt create`-Befehl hinzufügen. Obwohl dieser Befehl auch zum Erstellen einer neuen Instanz verwendet werden kann, können Sie ihn zum Bearbeiten der Eigenschaften einer vorhandenen Instanz verwenden, indem Sie den Namen einer bereits vorhandenen Instanz angeben. (Weitere Informationen zu diesem Befehl finden Sie in der zugehörigen [Referenzdokumentation](/cli/azure/dt?view=azure-cli-latest&preserve-view=true#az_dt_create) oder in den [allgemeinen Anweisungen zum Einrichten einer Azure Digital Twins-Instanz](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).)

Verwenden Sie den Parameter `--public-network-access` wie folgt, um den öffentlichen Netzwerkzugriff für eine Azure Digital Twins-Instanz zu **deaktivieren**:

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Disabled
```

Verwenden Sie den folgenden ähnlichen Befehl, um den öffentlichen Netzwerkzugriff für eine Instanz zu **aktivieren**, für die er derzeit deaktiviert ist:

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Enabled
```

# <a name="armclient"></a>[ARMClient](#tab/arm-client-2)

Mit dem [ARMClient-Befehlstool](https://github.com/projectkudu/ARMClient) können Sie den öffentlichen Netzwerkzugriff mithilfe der folgenden Befehle aktivieren oder deaktivieren. 

So **deaktivieren** Sie den öffentlichen Netzwerkzugriff:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

So **aktivieren** Sie den öffentlichen Netzwerkzugriff:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

---


## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr über Private Link für Azure: 
* [Was ist der Azure Private Link-Dienst?](../private-link/private-link-service-overview.md)
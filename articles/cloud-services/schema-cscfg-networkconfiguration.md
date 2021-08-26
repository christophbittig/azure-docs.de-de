---
title: 'Azure Cloud Services (klassisch): Netzwerkkonfigurationsschema | Microsoft-Dokumentation'
description: Hier erfahren Sie mehr über die untergeordneten Elemente des NetworkConfiguration-Elements der Dienstkonfigurationsdatei, die Virtual Network- und DNS-Werte angibt.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
'---thor': tagore
ms.openlocfilehash: 009e1c19cc3f4349651c5b17d99c8b94e7dfbecf
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822661"
---
# <a name="azure-cloud-services-classic-config-networkconfiguration-schema"></a>Azure Cloud Services (klassisch): Netzwerkkonfigurationsschema

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Das `NetworkConfiguration`-Element der Dienstkonfigurationsdatei gibt Werte für virtuelles Netzwerk und DNS an. Diese Einstellungen sind für Clouddienste optional.

Sie können die folgenden Ressourcen verwenden, um mehr über virtuelle Netzwerke und die zugehörigen Schemata zu erfahren:

- [Azure Cloud Services Config Schema (.cscfg File)](schema-cscfg-file.md) (Azure Cloud Services-Konfigurationsschema (CSCFG-Datei))
- [Clouddienst-Definitionsschema (klassisch)](schema-csdef-file.md)
- [Erstellen eines virtuellen Netzwerks (klassisch)](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)

## <a name="networkconfiguration-element"></a>NetworkConfiguration-Element
Das folgende Beispiel zeigt das `NetworkConfiguration`-Element und seine untergeordneten Elemente.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

Die folgende Tabelle beschreibt die untergeordneten Elemente des `NetworkConfiguration`-Elements.

| Element       | BESCHREIBUNG |
| ------------- | ----------- |
| AccessControl | Optional. Legt die Regeln für den Zugriff auf Endpunkte in einem Clouddienst fest. Der Name der Zugriffssteuerung wird durch eine Zeichenfolge für das `name`-Attribut definiert. Das `AccessControl`-Element enthält ein oder mehrere `Rule`-Elemente. Mehrere `AccessControl`-Elemente können definiert werden.|
| Regel | Optional. Gibt die Aktion an, die für einen angegebenen Subnetzbereich von IP-Adressen ausgeführt werden soll. Die Reihenfolge der Regel wird durch einen Zeichenfolgenwert für das `order`-Attribut definiert. Je niedriger die Regelzahl, desto höher die Priorität. Beispielsweise können Regeln mit den Ordnungszahlen 100, 200 und 300 angegeben werden. Die Regel mit der Ordnungszahl 100 hat Vorrang vor der Regel mit der Ordnungszahl 200.<br /><br /> Die Aktion für die Regel wird durch eine Zeichenfolge für das `action`-Attribut definiert. Mögliche Werte:<br /><br /> -   `permit` – Gibt an, dass nur Pakete aus dem angegebenen Subnetzbereich mit dem Endpunkt kommunizieren können.<br />-   `deny` – Gibt an, dass der Zugriff auf die Endpunkte im angegebenen Subnetzbereich verweigert wird.<br /><br /> Der Subnetzbereich der IP-Adressen, die von der Regel betroffen sind, wird durch eine Zeichenfolge für das `remoteSubnet`-Attribut definiert. Die Beschreibung für die Regel wird durch eine Zeichenfolge für das `description`-Attribut definiert.|
| EndpointAcl | Optional. Gibt die Zuweisung von Regeln zur Steuerung des Zugriffs auf einen Endpunkt an. Der Name der Rolle, der den Endpunkt enthält, wird durch eine Zeichenfolge für das `role`-Attribut definiert. Der Name des Endpunkts wird durch eine Zeichenfolge für das `endpoint`-Attribut definiert. Der Name des Satzes von `AccessControl`-Regeln, die auf den Endpunkt angewendet werden sollen, wird durch eine Zeichenfolge für das `accessControl`-Attribut definiert. Mehrere `EndpointAcl`-Elemente können definiert werden kann.|
| DnsServer | Optional. Gibt die Einstellungen für einen DNS-Server an. Sie können Einstellungen für DNS-Server ohne virtuelles Netzwerk angeben. Der Name des DNS-Servers wird durch eine Zeichenfolge für das `name`-Attribut definiert. Die IP-Adresse des DNS-Servers wird durch eine Zeichenfolge für das `IPAddress`-Attribut definiert. Die IP-Adresse muss eine gültige IPv4-Adresse sein.|
| VirtualNetworkSite | Optional. Gibt den Namen der Website im virtuellen Netzwerk an, wo Sie Ihren Clouddienst bereitstellen möchten. Mit dieser Einstellung wird kein virtuelles Netzwerk erstellt. Sie verweist auf eine Website, die zuvor in der Netzwerkdatei für Ihr virtuelles Netzwerk definiert wurde. Ein Clouddienst kann nur Mitglied eines einzelnen virtuellen Netzwerks sein. Wenn Sie diese Einstellung nicht angeben, wird der Clouddienst nicht in einem virtuellen Netzwerk bereitgestellt. Der Name des virtuellen Netzwerks wird durch eine Zeichenfolge für das `name`-Attribut definiert.|
| InstanceAddress | Optional. Gibt die Zuordnung einer Rolle zu einem Subnetz oder eine Gruppe von Subnetzen im virtuellen Netzwerk an. Wenn Sie einen Rollennamen einer Instanzadresse zuordnen, können Sie die Subnetze angeben, denen diese Rolle zugeordnet werden soll. Die `InstanceAddress` enthält ein Subnets-Element. Der Name der Rolle, die dem Subnetz oder den Subnetzen zugeordnet ist, wird durch eine Zeichenfolge für das `roleName`-Attribut definiert.|
| Subnet | Optional. Gibt das Subnetz an, das dem Subnetznamen in der Netzwerkkonfigurationsdatei entspricht. Der Name des Subnetzes wird durch eine Zeichenfolge für das `name`-Attribut definiert.|
| ReservedIP | Optional. Gibt die reservierte IP-Adresse an, die der Bereitstellung zugeordnet werden soll. Sie müssen mit „Reservierte IP-Adresse erstellen“ die reservierte IP-Adresse erstellen. Jede Bereitstellung in einem Clouddienst kann einer reservierten IP-Adresse zugeordnet werden. Der Name der reservierten IP-Adresse wird durch eine Zeichenfolge für das `name`-Attribut definiert.|

## <a name="see-also"></a>Weitere Informationen
[Azure Cloud Services Config Schema (.cscfg File)](schema-cscfg-file.md) (Azure Cloud Services-Konfigurationsschema (CSCFG-Datei))
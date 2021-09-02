---
title: 'Azure Cloud Services (erweiterter Support): NetworkConfiguration-Schema | Microsoft-Dokumentation'
description: Informationen zum Netzwerkkonfigurationsschema für Cloud Services (erweiterter Support)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e6b55782c7bdb8404bb19e4b7d58faf62178f73d
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220797"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services (erweiterter Support):Konfigurieren des networkConfiguration-Schemas

Das `NetworkConfiguration`-Element der Dienstkonfigurationsdatei gibt Werte für virtuelles Netzwerk und DNS an. Diese Einstellungen sind für Cloud Services optional.

Sie können die folgenden Ressourcen verwenden, um mehr über virtuelle Netzwerke und die zugehörigen Schemata zu erfahren:

- [Cloud Services (erweiterter Support): Definition des Schemas](schema-cscfg-file.md).
- [Cloud Service (erweiterter Support): Definition des Schemas](schema-csdef-file.md).
- [Erstellen eines virtuellen Netzwerks](../virtual-network/manage-virtual-network.md).

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
| AccessControl | Optional. Gibt die Regeln für den Zugriff auf Endpunkte in einem Clouddienst an. Der Name der Zugriffssteuerung wird durch eine Zeichenfolge für das `name`-Attribut definiert. Das `AccessControl`-Element enthält ein oder mehrere `Rule`-Elemente. Mehrere `AccessControl`-Elemente können definiert werden.|
| Regel | Optional. Gibt die Aktion an, die für einen angegebenen Subnetzbereich von IP-Adressen ausgeführt werden soll. Die Reihenfolge der Regel wird durch einen Zeichenfolgenwert für das `order`-Attribut definiert. Je niedriger die Regelzahl, desto höher die Priorität. Beispielsweise können Regeln mit den Ordnungszahlen 100, 200 und 300 angegeben werden. Die Regel mit der Ordnungszahl 100 hat Vorrang vor der Regel mit der Ordnungszahl 200.<br /><br /> Die Aktion für die Regel wird durch eine Zeichenfolge für das `action`-Attribut definiert. Mögliche Werte:<br /><br /> -   `permit` – Gibt an, dass nur Pakete aus dem angegebenen Subnetzbereich mit dem Endpunkt kommunizieren können.<br />-   `deny` – Gibt an, dass der Zugriff auf die Endpunkte im angegebenen Subnetzbereich verweigert wird.<br /><br /> Der Subnetzbereich der IP-Adressen, die von der Regel betroffen sind, wird durch eine Zeichenfolge für das `remoteSubnet`-Attribut definiert. Die Beschreibung für die Regel wird durch eine Zeichenfolge für das `description`-Attribut definiert.|
| EndpointAcl | Optional. Gibt die Zuweisung von Regeln zur Steuerung des Zugriffs auf einen Endpunkt an. Der Name der Rolle, der den Endpunkt enthält, wird durch eine Zeichenfolge für das `role`-Attribut definiert. Der Name des Endpunkts wird durch eine Zeichenfolge für das `endpoint`-Attribut definiert. Der Name des Satzes von `AccessControl`-Regeln, die auf den Endpunkt angewendet werden sollen, wird durch eine Zeichenfolge für das `accessControl`-Attribut definiert. Mehrere `EndpointAcl`-Elemente können definiert werden kann.|
| DnsServer | Optional. Gibt die Einstellungen für einen DNS-Server an. Sie können Einstellungen für DNS-Server ohne virtuelles Netzwerk angeben. Der Name des DNS-Servers wird durch eine Zeichenfolge für das `name`-Attribut definiert. Die IP-Adresse des DNS-Servers wird durch eine Zeichenfolge für das `IPAddress`-Attribut definiert. Die IP-Adresse muss eine gültige IPv4-Adresse sein.|
| VirtualNetworkSite | Erforderlich. Gibt den Namen der Website im virtuellen Netzwerk an, auf der Sie Ihren Clouddienst bereitstellen möchten. Mit dieser Einstellung wird kein virtuelles Netzwerk erstellt. Sie verweist auf eine Website, die zuvor in der Netzwerkdatei für Ihr virtuelles Netzwerk definiert wurde. Ein Clouddienst (erweiterter Support) kann nur Mitglied eines einzelnen virtuellen Netzwerks sein. Der Name des virtuellen Netzwerks wird durch eine Zeichenfolge für das `name`-Attribut definiert.|
| InstanceAddress | Erforderlich. Gibt die Zuordnung einer Rolle zu einem Subnetz oder eine Gruppe von Subnetzen im virtuellen Netzwerk an. Wenn Sie einen Rollennamen einer Instanzadresse zuordnen, können Sie die Subnetze angeben, denen diese Rolle zugeordnet werden soll. Die `InstanceAddress` enthält ein Subnets-Element. Der Name der Rolle, die dem Subnetz oder den Subnetzen zugeordnet ist, wird durch eine Zeichenfolge für das Attribut `roleName` definiert. Sie müssen für jede Rolle, die für Ihren Clouddienst definiert ist, eine Instanzadresse angeben.|
| Subnet | Erforderlich. Gibt das Subnetz an, das dem Subnetznamen in der Netzwerkkonfigurationsdatei entspricht. Der Name des Subnetzes wird durch eine Zeichenfolge für das `name`-Attribut definiert.|
| ReservedIP | Optional. Gibt die reservierte IP-Adresse an, die der Bereitstellung zugeordnet werden soll. Die Zuweisungsmethode für eine reservierte IP-Adresse muss für Vorlagen- und PowerShell-Bereitstellungen als `Static` angegeben werden. Jede Bereitstellung in einem Clouddienst kann nur einer reservierten IP-Adresse zugeordnet werden. Der Name der reservierten IP-Adresse wird durch eine Zeichenfolge für das `name`-Attribut definiert.|

## <a name="see-also"></a>Siehe auch
[Azure Cloud Services (erweiterter Support): Konfigurationsschema (CSCFG-Datei)](schema-cscfg-file.md)

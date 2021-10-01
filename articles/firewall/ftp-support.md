---
title: FTP-Unterstützung in Azure Firewall
description: Standardmäßig ist aktives FTP in Azure Firewall deaktiviert. Sie können es mithilfe der PowerShell, CLI und ARM-Vorlage aktivieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: victorh
ms.openlocfilehash: 7aeb7c596f1b4ca286cc9fce22965418c7c61c85
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084241"
---
# <a name="azure-firewall-ftp-support"></a>FTP-Unterstützung in Azure Firewall

Um FTP zu unterstützen, muss eine Firewall die folgenden wichtigen Aspekte berücksichtigen:
- FTP-Modus – aktiv oder passiv
- Client-/Serverstandort – Internet oder Intranet
- Flussrichtung – eingehend oder ausgehend. 

Azure Firewall unterstützt sowohl aktive als auch passive FTP-Szenarien. Weitere Informationen zum FTP-Modus finden Sie unter [Active FTP vs. Passive FTP, a Definitive Explanation](https://slacksite.com/other/ftp.html) (Aktives oder passives FTP: eine definitive Erklärung). 

Standardmäßig ist passives FTP aktiviert, und die Unterstützung von aktivem FTP ist deaktiviert, um vor FTP-Bounce-Angriffen mithilfe des FTP-PORT-Befehls zu schützen. 

Sie können jedoch aktives FTP aktivieren, wenn Sie Azure PowerShell, die Azure-Befehlszeilenschnittstelle oder eine Azure ARM-Vorlage verwenden. Azure Firewall kann sowohl aktives als auch passives FTP gleichzeitig unterstützen. *ActiveFTP* ist eine Firewalleigenschaft, die sowohl für Premium- als auch für Standard-SKUs, für sichere Hub- und VNet-Firewalls sowie bei Verwendung von Firewallrichtlinien und klassischen Regeln aktiviert werden kann.


## <a name="supported-scenarios"></a>Unterstützte Szenarios

Die folgende Tabelle zeigt die Konfiguration, die zur Unterstützung verschiedener FTP-Szenarien erforderlich ist:


|Firewallszenario  |Aktiver FTP-Modus   |Passiver FTP-Modus  |
|---------|---------|---------|
|VNet-VNet     |Zu konfigurierende Netzwerkregeln:<br>– Zulassen vom Quell-VNet zum Ziel-IP-Port 21<br>– Zulassen vom Ziel-IP-Port 20 zum Quell-VNet |Zu konfigurierende Netzwerkregeln:<br>– Zulassen vom Quell-VNet zum Ziel-IP-Port 21<br>– Zulassen vom Quell-VNet zum Ziel-IP-\<Range of Data Ports>|
|Ausgehendes VNet – Internet<br><br>(FTP-Client im VNet, Server im Internet)      |Nicht unterstützt *|**Vorbedingung**: Konfigurieren Sie den FTP-Server so, dass er Daten- und Steuerungskanäle von verschiedenen Quell-IP-Adressen akzeptiert. Alternativ können Sie die Azure Firewall mit einer einzelnen öffentlichen IP-Adresse konfigurieren.<br><br>Zu konfigurierende Netzwerkregeln:<br>– Zulassen vom Quell-VNet zum Ziel-IP-Port 21<br>– Zulassen vom Quell-VNet zum Ziel-IP-\<Range of Data Ports> |
|Eingehendes DNAT<br><br>(FTP-Client im Internet, Server im VNet)      |Zu konfigurierende DNAT-Regel:<br>– DNAT von der Internetquelle zum VNet-IP-Port 21<br><br>Zu konfigurierende Netzwerkregel:<br>– Zulassen von VNet-IP-Port 20 zur Internetquelle |**Vorbedingung**:<br>Konfigurieren Sie den FTP-Server so, dass er Daten- und Steuerungskanäle von verschiedenen Quell-IP-Adressen akzeptiert.<br><br>Tipp: Azure Firewall unterstützt eine begrenzte Anzahl von DNAT-Regeln. Es ist wichtig, den FTP-Server so zu konfigurieren, dass er einen kleinen Portbereich im Datenkanal verwendet.<br><br>Zu konfigurierende DNAT-Regeln:<br>– DNAT von der Internetquelle zum VNet-IP-Port 21<br>– DNAT von der Internetquelle zum VNet-IP-\<Range of Data Ports> |

\* Aktives FTP funktioniert nicht, wenn der FTP-Client einen FTP-Server im Internet erreichen muss. Aktives FTP verwendet einen PORT-Befehl vom FTP-Client, der dem FTP-Server mitteilt, welche IP-Adresse und welcher Port für den Datenkanal verwendet werden soll. Dieser PORT-Befehl verwendet die private IP-Adresse des Clients, die nicht geändert werden kann. Clientseitiger Datenverkehr, der die Azure Firewall durchquert, wird für internetbasierte Kommunikation per NAT übersetzt, sodass der PORT-Befehl vom FTP-Server als ungültig angesehen wird. Dies ist eine allgemeine Einschränkung von aktivem FTP bei Verwendung mit einer clientseitigen NAT. 


## <a name="deploy-using-azure-powershell"></a>Bereitstellen mit Azure PowerShell

Verwenden Sie zur Bereitstellung mithilfe von Azure PowerShell den Parameter `AllowActiveFTP`. Weitere Informationen finden Sie unter [Erstellen einer Firewall durch Zulassen von aktivem FTP](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-).

## <a name="deploy-using-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

Verwenden Sie zur Bereitstellung mithilfe der Azure-Befehlszeilenschnittstelle den Parameter `--allow-active-ftp`. Weitere Informationen finden Sie unter [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters). 

## <a name="deploy-azure-resource-manager-arm-template"></a>Bereitstellen einer ARM-Vorlage (Azure Resource Manager)

Für die Bereitstellung mithilfe einer ARM-Vorlage verwenden Sie das Feld `AdditionalProperties`:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Weitere Informationen finden Sie unter [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen einer Azure Firewall finden Sie unter [Bereitstellen und Konfigurieren von Azure Firewall mithilfe von Azure PowerShell](deploy-ps.md).
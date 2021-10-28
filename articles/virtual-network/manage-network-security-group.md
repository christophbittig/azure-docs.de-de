---
title: Erstellen, Ändern oder Löschen einer Azure-Netzwerksicherheitsgruppe
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wo Sie Informationen zu Sicherheitsregeln finden und wie Sie eine Netzwerksicherheitsgruppe erstellen, ändern oder löschen.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 6dd46099a3f4450dd175d0e61c2a6817de0f76f3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224816"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe

Mit Sicherheitsregeln in Netzwerksicherheitsgruppen können Sie den Typ des ein- und ausgehenden Netzwerkdatenverkehrs von Subnetzen virtueller Netzwerke und Netzwerkschnittstellen filtern. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen – Übersicht](./network-security-groups-overview.md). Absolvieren Sie als Nächstes das Tutorial [Filtern von Netzwerkdatenverkehr](tutorial-filter-network-traffic.md), um sich mit Netzwerksicherheitsgruppen vertraut zu machen.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Falls Sie noch nicht über ein Azure-Konto verfügen, richten Sie ein Azure-Konto mit einem aktiven Abonnement ein. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Führen Sie eine dieser Aufgaben aus, bevor Sie mit dem Rest dieses Artikels starten:

- **Portalbenutzer**: Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

- **PowerShell-Benutzer:** Führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder in PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Wechseln Sie auf der Browserregisterkarte „Azure Cloud Shell“ zur Dropdownliste **Umgebung auswählen**, und wählen Sie dann **PowerShell** aus, falls nicht bereits ausgewählt.

    Wenn Sie PowerShell lokal ausführen, müssen Sie mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az.Network` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Führen Sie zum Starten `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

- **Azure CLI-Benutzer**: Führen Sie die Befehle in [Azure Cloud Shell](https://shell.azure.com/bash) oder über die CLI auf Ihrem Computer aus. Verwenden Sie bei lokaler Ausführung der Azure CLI mindestens die Version 2.0.28. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Führen Sie zum Starten `az login` aus, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

## <a name="work-with-network-security-groups"></a>Arbeiten mit Netzwerksicherheitsgruppen

Sie können eine Netzwerksicherheitsgruppe erstellen, [komplett anzeigen](#view-all-network-security-groups), [Details von ihr anzeigen](#view-details-of-a-network-security-group), sie [ändern](#change-a-network-security-group) und [löschen](#delete-a-network-security-group). Sie können eine Netzwerksicherheitsgruppe auch einer Netzwerkschnittstelle oder einem Subnetz [zuordnen bzw. davon trennen](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Die Anzahl der Netzwerksicherheitsgruppen, die Sie pro Azure-Standort und -Abonnement erstellen können, ist begrenzt. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Wählen Sie im Menü des [Azure-Portals](https://portal.azure.com) oder auf der **Startseite** die Option **Ressource erstellen** aus.

2. Wählen Sie die Option **Netzwerk** und dann **Netzwerksicherheitsgruppe** aus.

3. Legen Sie auf der Seite **Netzwerksicherheitsgruppe erstellen** auf der Registerkarte **Grundlagen** Werte für die folgenden Einstellungen fest:

    | Einstellung | Aktion |
    | --- | --- |
    | **Abonnement** | Wählen Sie Ihr Abonnement aus. |
    | **Ressourcengruppe** | Wählen Sie eine vorhandene Ressourcengruppe oder **Neu erstellen**, um eine neue Ressourcengruppe zu erstellen. |
    | **Name** | Geben Sie eine eindeutige Textzeichenfolge für die Ressourcengruppe ein. |
    | **Region** | Wählen Sie den gewünschten Standort. |

4. Klicken Sie auf **Überprüfen + erstellen**.

5. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Anzeigen aller Netzwerksicherheitsgruppen

Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Netzwerksicherheitsgruppen anzuzeigen. Navigieren Sie zum Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus. Die Liste der Netzwerksicherheitsgruppen für Ihr Abonnement wird angezeigt.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Anzeigen von Details einer Netzwerksicherheitsgruppe

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Netzwerksicherheitsgruppen anzuzeigen. Navigieren Sie zum Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.

2. Wählen Sie den Namen der Netzwerksicherheitsgruppe aus.

Auf der Menüleiste der Netzwerksicherheitsgruppe sehen Sie unter **Einstellungen** die **Eingangssicherheitsregeln** und **Ausgangssicherheitsregeln**, **Netzwerkschnittstellen** und **Subnetze**, denen die Netzwerksicherheitsgruppe zugeordnet ist.

Unter **Überwachung** können Sie **Diagnoseeinstellungen** aktivieren oder deaktivieren. Unter **Support + Problembehandlung** können Sie **Effektive Sicherheitsregeln** einsehen. Weitere Informationen finden Sie unter [Diagnoseprotokollierung für eine Netzwerksicherheitsgruppe](virtual-network-nsg-manage-log.md) und [Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr](diagnose-network-traffic-filter-problem.md).

Weitere Informationen zu allgemeinen aufgelisteten Azure-Einstellungen finden Sie in den folgenden Artikeln:

- [Aktivitätsprotokoll](../azure-monitor/essentials/platform-logs-overview.md)
- [Zugriffssteuerung (IAM)](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automatisierungsskript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Ändern einer Netzwerksicherheitsgruppe

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Netzwerksicherheitsgruppen anzuzeigen. Navigieren Sie zum Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.

2. Wählen Sie den Namen der Netzwerksicherheitsgruppe aus, die Sie ändern möchten.

Die gängigsten Änderungen sind das [Hinzufügen einer Sicherheitsregel](#create-a-security-rule), [Entfernen einer Regel](#delete-a-security-rule) und [Zuordnen einer Netzwerksicherheitsgruppe zu einem Subnetz oder einer Netzwerkschnittstelle bzw. das Aufheben dieser Zuordnung](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Zuordnen einer Netzwerksicherheitsgruppe zu einer Netzwerkschnittstelle oder einem Subnetz bzw. deren Trennung davon

Wie Sie eine Netzwerksicherheitsgruppe einer Netzwerkschnittstelle zuordnen bzw. davon trennen, erfahren Sie unter [Zuordnen einer Netzwerksicherheitsgruppe zu einer Netzwerkschnittstelle bzw. Trennen davon](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Wie Sie eine Netzwerksicherheitsgruppe einem Subnetz zuordnen bzw. davon trennen, erfahren Sie unter [Ändern von Subnetzeinstellungen](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Löschen einer Netzwerksicherheitsgruppe

Wenn eine Netzwerksicherheitsgruppe Subnetzen bzw. Netzwerkschnittstellen zugeordnet ist, kann sie nicht gelöscht werden. Trennen Sie eine Netzwerksicherheitsgruppe von allen Subnetzen und Netzwerkschnittstellen, bevor Sie versuchen, sie zu löschen.

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Netzwerksicherheitsgruppen anzuzeigen. Navigieren Sie zum Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.

2. Wählen Sie den Namen der Netzwerksicherheitsgruppe aus, die Sie löschen möchten.

3. Wählen Sie auf der Symbolleiste der Netzwerksicherheitsgruppe **Löschen** aus. Wählen Sie anschließend im Bestätigungsdialogfeld die Option **Ja**.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Arbeiten mit Sicherheitsregeln

Eine Netzwerksicherheitsgruppe muss keine Sicherheitsregeln enthalten, kann aber Sicherheitsregeln enthalten. Sie können eine Sicherheitsregel erstellen, [komplett anzeigen](#view-all-security-rules), [Details von ihr anzeigen](#view-details-of-a-security-rule), sie [ändern](#change-a-security-rule) und [löschen](#delete-a-security-rule).

### <a name="create-a-security-rule"></a>Erstellen einer Sicherheitsregel

Die Anzahl der Regeln pro Netzwerksicherheitsgruppe, die Sie für Azure-Standorte und -Abonnements erstellen können, ist begrenzt. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Netzwerksicherheitsgruppen anzuzeigen. Navigieren Sie zum Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.

2. Wählen Sie den Namen der Netzwerksicherheitsgruppe aus, der Sie eine Sicherheitsregel hinzufügen möchten.

3. Wählen Sie auf der Menüleiste der Netzwerksicherheitsgruppe **Eingangssicherheitsregeln** oder **Ausgangssicherheitsregeln** aus.

    Es sind mehrere bestehende Regeln aufgeführt, darunter auch einige, die Sie möglicherweise nicht hinzugefügt haben. Wenn Sie eine Netzwerksicherheitsgruppe erstellen, werden mehrere Standardsicherheitsregeln darin erstellt. Weitere Informationen finden Sie unter [Standardsicherheitsregeln](./network-security-groups-overview.md#default-security-rules).  Sie können Standardsicherheitsregeln nicht löschen, können jedoch mit Regeln mit höherer Priorität überschreiben.

4. <a name="security-rule-settings"></a>Wählen Sie **Hinzufügen** aus. Wählen oder ergänzen Sie Werte für die folgenden Einstellungen, und wählen Sie dann **OK** aus:

    | Einstellung | Wert | Details |
    | ------- | ----- | ------- |
    | **Quelle** | Enthält einen der folgenden Werte:<ul><li>**Alle**</li><li>**IP-Adressen**</li><li>**Diensttag** (Eingangssicherheitsregel) oder **VirtualNetwork** (Ausgangssicherheitsregel)</li><li>**Anwendungs&nbsp;sicherheits&nbsp;gruppe**</li></ul> | <p>Bei Wahl von **IP-Adressen** müssen Sie auch **Quell-IP-Adressen/CIDR-Bereiche** angeben.</p><p>Wenn Sie **Diensttag** wählen, können Sie auch ein **Quelldiensttag** wählen.</p><p>Wenn Sie **Anwendungssicherheitsgruppe** wählen, müssen Sie auch eine vorhandene Anwendungssicherheitsgruppe wählen. Wenn Sie sowohl für **Quelle** als auch für **Ziel** die Option **Anwendungssicherheitsgruppe** wählen, müssen sich die Netzwerkschnittstellen beider Anwendungssicherheitsgruppen im selben virtuellen Netzwerk befinden.</p> |
    | **IP-Quelladressen/CIDR-Bereiche** | Eine durch Trennzeichen getrennte Liste von IP-Adressen und CIDR-Bereichen (Classless Interdomain Routing) | <p>Diese Einstellung wird angezeigt, wenn Sie **Quelle** in **IP-Adressen** ändern. Sie müssen einen einzelnen Wert oder eine durch Trennzeichen getrennte Liste mit mehreren Werten angeben. Ein Beispiel mehrerer Werte ist `10.0.0.0/16, 192.188.1.1`. Es gibt Grenzen für die Anzahl der Werte, die Sie angeben können. Ausführliche Informationen finden Sie unter [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Wenn die von Ihnen angegebene IP-Adresse einer Azure-VM zugewiesen ist, geben Sie deren private IP-Adresse und nicht deren öffentliche IP-Adresse an. Azure verarbeitet Sicherheitsregeln, nachdem die öffentliche IP-Adresse von Azure in eine private IP-Adresse für Eingangssicherheitsregeln und bevor eine private IP-Adresse in eine öffentliche IP-Adresse für Ausgangsregeln übersetzt wurde. Weitere Informationen zu öffentlichen und privaten IP-Adressen in Azure finden Sie unter [IP-Adresstypen und Zuordnungsmethoden in Azure](./ip-services/public-ip-addresses.md).</p> |
    | **Quelldiensttag** | Ein Diensttag in der Dropdownliste | Diese optionale Einstellung wird angezeigt, wenn Sie **Quelle** für eine Eingangssicherheitsregel auf **Diensttag** festlegen. Ein Diensttag ist ein vordefinierter Bezeichner für eine Kategorie von IP-Adressen. Weitere Informationen zu verfügbaren Diensttags und dazu, was die einzelnen Tag darstellen, finden Sie unter [Diensttags](./network-security-groups-overview.md#service-tags). |
    | **Quell-Anwendungssicherheitsgruppe** | Eine vorhandene Anwendungssicherheitsgruppe | Diese Einstellung wird angezeigt, wenn Sie **Quelle** auf **Anwendungssicherheitsgruppe** festlegen. Wählen Sie eine Anwendungssicherheitsgruppe aus, die in derselben Region wie die Netzwerkschnittstelle vorhanden ist. Informieren Sie sich über das [Erstellen einer Anwendungssicherheitsgruppe](#create-an-application-security-group). |
    | **Quellportbereiche** | Enthält einen der folgenden Werte:<ul><li>Ein einzelner Port, z. B. `80`</li><li>Ein Portbereich, z. B. `1024-65535`</li><li>Eine durch Trennzeichen getrennte Liste mit einzelnen Ports und/oder Portbereichen wie z. B. `80, 1024-65535`</li><li>Ein Sternchen (`*`), um Datenverkehr an jedem beliebigen Port zuzulassen</li></ul> | Diese Einstellung gibt die Ports an, an denen die Regel Datenverkehr zulässt oder verweigert. Es gibt Grenzen für die Anzahl der Ports, die Sie angeben können. Ausführliche Informationen finden Sie unter [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Ziel** | Enthält einen der folgenden Werte:<ul><li>**Alle**</li><li>**IP-Adressen**</li><li>**Diensttag** (Ausgangssicherheitsregel) oder **VirtualNetwork** (Eingangssicherheitsregel)</li><li>**Anwendungs&nbsp;sicherheits&nbsp;gruppe**</li></ul> | <p>Bei Wahl von **IP-Adressen** müssen Sie **Ziel-IP-Adressen/CIDR-Bereiche** angeben.</p><p>Bei Wahl von **VirtualNetwork** wird Datenverkehr zu allen IP-Adressen innerhalb des Adressraums des virtuellen Netzwerks zugelassen. **VirtualNetwork** ist ein Diensttag.</p><p>Wenn Sie **Anwendungssicherheitsgruppe** auswählen, müssen Sie eine vorhandene Anwendungssicherheitsgruppe auswählen. Informieren Sie sich über das [Erstellen einer Anwendungssicherheitsgruppe](#create-an-application-security-group).</p> |
    | **Ziel-IP-Adressen/CIDR-Bereiche** | Eine durch Trennzeichen getrennte Liste von IP-Adressen und CIDR-Bereichen | <p>Diese Einstellung wird angezeigt, wenn Sie **Ziel** in **IP-Adressen** ändern. Ähnlich wie bei **Quelle** und **Quell-IP-Adressen/CIDR-Bereiche** können Sie eine einzelne oder mehrere Adressen bzw. Bereiche angeben. Die Anzahl, die Sie angeben können, ist begrenzt. Ausführliche Informationen finden Sie unter [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Wenn die von Ihnen angegebene IP-Adresse einer Azure-VM zugewiesen ist, geben Sie unbedingt deren private IP-Adresse und nicht deren öffentliche IP-Adresse an. Azure verarbeitet Sicherheitsregeln, nachdem die öffentliche IP-Adresse von Azure in eine private IP-Adresse für Eingangssicherheitsregeln und bevor eine private IP-Adresse in eine öffentliche IP-Adresse für Ausgangsregeln übersetzt wurde. Weitere Informationen zu öffentlichen und privaten IP-Adressen in Azure finden Sie unter [IP-Adresstypen und Zuordnungsmethoden in Azure](./ip-services/public-ip-addresses.md).</p> |
    | **Zieldiensttag** | Ein Diensttag in der Dropdownliste | Diese optionale Einstellung wird angezeigt, wenn Sie **Ziel** für eine Ausgangssicherheitsregel in **Diensttag** ändern. Ein Diensttag ist ein vordefinierter Bezeichner für eine Kategorie von IP-Adressen. Weitere Informationen zu verfügbaren Diensttags und dazu, was die einzelnen Tag darstellen, finden Sie unter [Diensttags](./network-security-groups-overview.md#service-tags). |
    | **Ziel-Anwendungssicherheitsgruppe** | Eine vorhandene Anwendungssicherheitsgruppe | Diese Einstellung wird angezeigt, wenn Sie **Ziel** auf **Anwendungssicherheitsgruppe** festlegen. Wählen Sie eine Anwendungssicherheitsgruppe aus, die in derselben Region wie die Netzwerkschnittstelle vorhanden ist. Informieren Sie sich über das [Erstellen einer Anwendungssicherheitsgruppe](#create-an-application-security-group). |
    | **Zielportbereiche** | Enthält einen der folgenden Werte:<ul><li>Ein einzelner Port, z. B. `80`</li><li>Ein Portbereich, z. B. `1024-65535`</li><li>Eine durch Trennzeichen getrennte Liste mit einzelnen Ports und/oder Portbereichen wie z. B. `80, 1024-65535`</li><li>Ein Sternchen (`*`), um Datenverkehr an jedem beliebigen Port zuzulassen</li></ul> | Wie bei **Quellportbereichen** können Sie einzelne oder mehrere Ports und Bereiche angeben. Die Anzahl, die Sie angeben können, ist begrenzt. Ausführliche Informationen finden Sie unter [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokoll** | **Alle**, **TCP**, **UDP** oder **ICMP** | Sie können die Regel auf Transmission Control Protocol (TCP), User Datagram Protocol (UDP) oder Internet Control Message Protocol (ICMP) beschränken. Standardmäßig gilt die Regel für alle Protokolle. |
    | **Aktion** | **Zulassen** oder **Verweigern** | Diese Einstellung gibt an, ob diese Regel den Zugriff für die bereitgestellte Quell- und Zielkonfiguration zulässt oder verweigert. |
    | **Priority** | Ein Wert von 100 bis 4.096, der für alle Sicherheitsregeln innerhalb der Netzwerksicherheitsgruppe eindeutig ist | Azure verarbeitet Sicherheitsregeln in der Reihenfolge ihrer Priorität. Je niedriger die Zahl, desto höher die Priorität. Sie sollten beim Erstellen von Regeln eine Lücke zwischen Prioritätswerten lassen, z. B. 100, 200 und 300. Wenn Sie Lücken lassen, ist es einfacher, in Zukunft Regeln so hinzuzufügen, dass Sie ihnen eine höhere oder niedrigere Priorität als bestehenden Regeln geben können. |
    | **Name** | Ein eindeutiger Name für die Regel in der Netzwerksicherheitsgruppe | Der Name kann bis zu 80 Zeichen umfassen. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und mit einem Buchstaben, einer Zahl oder einem Unterstrich enden. Der Name darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. |
    | **Beschreibung** | Eine Textbeschreibung | Sie können optional eine Textbeschreibung für die Sicherheitsregel angeben. Die Beschreibung darf nicht mehr als 140 Zeichen enthalten. |

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Anzeigen aller Sicherheitsregeln

Eine Netzwerksicherheitsgruppe muss keine Regeln enthalten, kann aber Regeln enthalten. Weitere Informationen zu den Details, die beim Anzeigen von Regeln aufgeführt werden, finden Sie unter [Netzwerksicherheit](./network-security-groups-overview.md).

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um die Regeln einer Netzwerksicherheitsgruppe anzuzeigen. Navigieren Sie zum Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.

2. Wählen Sie den Namen der Netzwerksicherheitsgruppe aus, deren Regeln Sie anzeigen möchten.

3. Wählen Sie auf der Menüleiste der Netzwerksicherheitsgruppe **Eingangssicherheitsregeln** oder **Ausgangssicherheitsregeln** aus.

Die Liste enthält alle Regeln, die Sie erstellt haben, und die [Standardsicherheitsregeln](./network-security-groups-overview.md#default-security-rules) der Netzwerksicherheitsgruppe.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Anzeigen von Details einer Sicherheitsregel

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um die Regeln einer Netzwerksicherheitsgruppe anzuzeigen. Navigieren Sie zum Eintrag **Netzwerksicherheitsgruppen**, und wählen Sie ihn aus.

2. Wählen Sie den Namen der Netzwerksicherheitsgruppe aus, für die Sie die Details einer Regel anzeigen möchten.

3. Wählen Sie auf der Menüleiste der Netzwerksicherheitsgruppe **Eingangssicherheitsregeln** oder **Ausgangssicherheitsregeln** aus.

4. Wählen Sie die Regel aus, für die Sie Details anzeigen möchten. Eine Erläuterung aller Einstellungen finden Sie unter [Sicherheitsregeleinstellungen](#security-rule-settings).

    > [!NOTE]
    > Dieses Verfahren gilt nur für eine benutzerdefinierte Sicherheitsregel. Es funktioniert nicht, wenn Sie eine Standardsicherheitsregel wählen.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg rule show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Ändern einer Sicherheitsregel

1. Führen Sie die Schritte in [Anzeigen von Details einer Sicherheitsregel](#view-details-of-a-security-rule) aus.

2. Ändern Sie die Einstellungen nach Bedarf, und wählen Sie dann **Speichern** aus. Eine Erläuterung aller Einstellungen finden Sie unter [Sicherheitsregeleinstellungen](#security-rule-settings).

    > [!NOTE]
    > Dieses Verfahren gilt nur für eine benutzerdefinierte Sicherheitsregel. Sie dürfen eine Standardsicherheitsregel nicht ändern.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Löschen einer Sicherheitsregel

1. Führen Sie die Schritte in [Anzeigen von Details einer Sicherheitsregel](#view-details-of-a-security-rule) aus.

2. Wählen Sie **Löschen** und dann **Ja**.

    > [!NOTE]
    > Dieses Verfahren gilt nur für eine benutzerdefinierte Sicherheitsregel. Sie dürfen eine Standardsicherheitsregel nicht löschen.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network nsg rule delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Arbeiten mit Anwendungssicherheitsgruppen

Eine Anwendungssicherheitsgruppe muss keine Netzwerkschnittstellen enthalten, kann aber Netzwerkschnittstellen enthalten. Weitere Informationen finden Sie unter [Anwendungssicherheitsgruppen](./network-security-groups-overview.md#application-security-groups). Alle Netzwerkschnittstellen innerhalb einer Anwendungssicherheitsgruppe müssen sich im gleichen virtuellen Netzwerk befinden. Wie Sie eine Netzwerkschnittstelle einer Anwendungssicherheitsgruppe hinzufügen, erfahren Sie unter [Erstellen, Ändern oder Löschen von Netzwerkschnittstellen](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Erstellen einer Anwendungssicherheitsgruppe

1. Wählen Sie im Menü des [Azure-Portals](https://portal.azure.com) oder auf der **Startseite** die Option **Ressource erstellen** aus.

2. Geben Sie in das Suchfeld *Anwendungssicherheitsgruppe* ein.

3. Wählen Sie auf der Seite **Anwendungssicherheitsgruppe** die Option **Erstellen** aus.

4. Legen Sie auf der Seite **Anwendungssicherheitsgruppe erstellen** auf der Registerkarte **Grundlagen** Werte für die folgenden Einstellungen fest:

    | Einstellung | Aktion |
    | --- | --- |
    | **Abonnement** | Wählen Sie Ihr Abonnement aus. |
    | **Ressourcengruppe** | Wählen Sie eine vorhandene Ressourcengruppe oder **Neu erstellen**, um eine neue Ressourcengruppe zu erstellen. |
    | **Name** | Geben Sie eine eindeutige Textzeichenfolge für die Ressourcengruppe ein. |
    | **Region** | Wählen Sie den gewünschten Standort. |

5. Klicken Sie auf **Überprüfen + erstellen**.

6. Wählen Sie auf der Registerkarte **Überprüfen und erstellen**, nachdem die Meldung **Überprüfung erfolgreich** angezeigt wurde, **Erstellen** aus.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network asg create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Anzeigen aller Anwendungssicherheitsgruppen

Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Anwendungssicherheitsgruppe anzuzeigen. Suchen Sie nach **Anwendungssicherheitsgruppen**, und wählen Sie die Option aus. Im Azure-Portal wird eine Liste Ihrer Anwendungssicherheitsgruppen angezeigt.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network asg list](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Anzeigen von Details einer bestimmten Anwendungssicherheitsgruppe

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um eine Anwendungssicherheitsgruppe anzuzeigen. Suchen Sie nach **Anwendungssicherheitsgruppen**, und wählen Sie die Option aus.

2. Wählen Sie den Namen der Anwendungssicherheitsgruppe aus, deren Details Sie anzeigen möchten.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network asg show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Ändern einer Anwendungssicherheitsgruppe

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um eine Anwendungssicherheitsgruppe anzuzeigen. Suchen Sie nach **Anwendungssicherheitsgruppen**, und wählen Sie die Option aus.

2. Wählen Sie den Namen der Anwendungssicherheitsgruppe aus, die Sie ändern möchten.

3. Wählen Sie neben der Einstellung, die Sie ändern möchten, **Ändern** aus. Sie können z. B. **Tags** hinzufügen oder entfernen oder die **Ressourcengruppe** oder das **Abonnement** ändern.

    > [!NOTE]
    > Der Standort kann nicht geändert werden.

    Auf der Menüleiste können Sie auch **Zugriffssteuerung (IAM)** auswählen. Auf der Seite **Zugriffssteuerung (IAM)** können Sie der Anwendungssicherheitsgruppe Berechtigungen zuweisen oder diese entfernen.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network asg update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | Kein PowerShell-Cmdlet |

### <a name="delete-an-application-security-group"></a>Löschen einer Anwendungssicherheitsgruppe

Sie können eine Anwendungssicherheitsgruppe nicht löschen, wenn sie Netzwerkschnittstellen enthält. Um alle Netzwerkschnittstellen aus der Anwendungssicherheitsgruppe zu entfernen, ändern Sie entweder die Einstellungen der Netzwerkschnittstelle, oder löschen Sie die Netzwerkschnittstellen. Weitere Informationen finden Sie unter [Hinzufügen einer Netzwerkschnittstelle zu einer Anwendungssicherheitsgruppe bzw. deren Entfernen daraus](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) oder [Löschen einer Netzwerkschnittstelle](virtual-network-network-interface.md#delete-a-network-interface).

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), um Ihre Anwendungssicherheitsgruppe zu verwalten. Suchen Sie nach **Anwendungssicherheitsgruppen**, und wählen Sie die Option aus.

2. Wählen Sie den Namen der Anwendungssicherheitsgruppe aus, die Sie löschen möchten.

3. Wählen Sie **Löschen** und dann **Ja**, um die Anwendungssicherheitsgruppe zu löschen.

#### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure-Befehlszeilenschnittstelle | [az network asg delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Berechtigungen

Zum Durchführen von Aufgaben für Netzwerksicherheitsgruppen, Sicherheitsregeln und Anwendungssicherheitsgruppen muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die entsprechenden in den folgenden Tabellen aufgeführten Berechtigungen zugewiesen sind:

### <a name="network-security-group"></a>Netzwerksicherheitsgruppe

| Aktion                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Netzwerksicherheitsgruppe abrufen                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Netzwerksicherheitsgruppe erstellen oder aktualisieren                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Netzwerksicherheitsgruppe löschen                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Netzwerksicherheitsgruppe einem Subnetz oder einer Netzwerkschnittstelle zuordnen 


>[!NOTE]
> Um `write`-Vorgänge für eine Netzwerksicherheitsgruppe auszuführen, muss das Abonnementkonto mindestens über `read`-Berechtigungen für die Ressourcengruppe sowie über die Berechtigung `Microsoft.Network/networkSecurityGroups/write` verfügen.


### <a name="network-security-group-rule"></a>Netzwerksicherheitsgruppen-Regel

| Aktion                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Regel abrufen                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Regel erstellen oder aktualisieren                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Regel löschen                                                         |

### <a name="application-security-group"></a>Anwendungssicherheitsgruppe

| Aktion                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP-Konfiguration mit einer Anwendungssicherheitsgruppe verknüpfen|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Sicherheitsregel mit einer Anwendungssicherheitsgruppe verknüpfen    |
| Microsoft.Network/applicationSecurityGroups/read                           | Anwendungssicherheitsgruppe abrufen                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Anwendungssicherheitsgruppe erstellen oder aktualisieren           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Löschen einer Anwendungssicherheitsgruppe                     |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen einer Netzwerk- oder Anwendungssicherheitsgruppe mithilfe von [PowerShell](powershell-samples.md)- oder [Azure CLI](cli-samples.md)-Beispielskripts oder Azure [Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Zuweisen von [Azure Policy-Definitionen](./policy-reference.md) für virtuelle Netzwerke
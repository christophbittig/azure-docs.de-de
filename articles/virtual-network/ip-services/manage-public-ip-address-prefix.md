---
title: Erstellen, Ändern oder Löschen des Präfix einer öffentlichen Azure-IP-Adresse
titlesuffix: Azure Virtual Network
description: Hier erhalten Sie Informationen zu Präfixen öffentlicher IP-Adressen und dazu, wie Sie diese erstellen, ändern und löschen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 824d89126252a0690b93a2129f47d8e02b728694
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560110"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Erstellen, Ändern oder Löschen des Präfix einer öffentlichen IP-Adresse

Erfahren Sie mehr über Präfixe öffentlicher IP-Adressen. Das Präfix einer öffentlichen IP-Adresse ist ein zusammenhängender Bereich öffentlicher IP-Adressen mit Standard-SKU.  Wenn Sie eine öffentliche IP-Adressressource erstellen, können Sie eine statische öffentliche IP-Adresse aus dem Präfix zuweisen und Azure-Ressourcen zuordnen. Weitere Informationen finden Sie in der Übersicht [Präfix für öffentliche IP-Adressen](public-ip-address-prefix.md).

## <a name="create-a-public-ip-address-prefix"></a>Erstellen des Präfix einer öffentlichen IP-Adresse

Im folgenden Abschnitt werden die Parameter beim Erstellen eines Präfix für öffentliche IP-Adressen erläutert.

   |Einstellung|Erforderlich?|Details|
   |---|---|---|
   |Subscription|Ja|Muss im selben [Abonnement](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) wie die Ressource vorhanden sein, der Sie die öffentliche IP-Adresse zuordnen möchten|
   |Resource group|Ja|Kann in derselben oder in einer anderen [Ressourcengruppe](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) wie die Ressource vorhanden sein, der Sie die öffentliche IP-Adresse zuordnen möchten|
   |Name|Ja|Der Name muss innerhalb der ausgewählten Ressourcengruppe eindeutig sein.|
   |Region|Ja|Muss in der gleichen [Region](https://azure.microsoft.com/regions) wie die öffentlichen IP-Adressen vorhanden sein, die Sie aus dem Bereich zuweisen.|
   |IP-Version|Ja| IP-Version des Präfixes (v4 oder v6)
   |Präfixgröße|Ja| Die benötigte Größe des Präfix. Ein Bereich mit 16 IP-Adressen (/28 für v4 oder /124 für v6) ist die Standardeinstellung.

Alternativ können Sie die unten angegebenen CLI- und PowerShell-Befehle verwenden, um ein Präfix für öffentliche IP-Adressen zu erstellen.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>In Regionen mit Verfügbarkeitszonen können Sie PowerShell- oder CLI-Befehle verwenden, um ein Präfix für eine öffentliche IP-Adresse zu erstellen: nicht zonal, mit einer bestimmten Zone verknüpft oder zur Verwendung von Zonenredundanz.  Wenn kein Zonenparameter bereitgestellt wird, wird für API-Version 2020-08-01 oder höher ein nicht zonales öffentliches IP-Adresspräfix erstellt. Für ältere API-Versionen als 2020-08-01 wird ein zonenredundantes öffentliches IP-Adresspräfix erstellt. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Erstellen einer statischen öffentlichen IP-Adresse aus einem Präfix

Im folgenden Abschnitt werden die Parameter erläutert, die beim Erstellen einer statischen öffentlichen IP-Adresse aus einem Präfix erforderlich sind.

   |Einstellung|Erforderlich?|Details|
   |---|---|---|
   |Name|Ja|Der Name der öffentlichen IP-Adresse muss innerhalb der ausgewählten Ressourcengruppe eindeutig sein.|
   |Leerlaufzeitüberschreitung (Minuten)|Nein|Gibt an, wie viele Minuten eine TCP- oder HTTP-Verbindung geöffnet bleiben soll, ohne dass Clients Keep-Alive-Meldungen senden müssen. |
   |DNS-Namensbezeichnung|Nein|Muss in der Azure-Region, in der Sie den Namen erstellen, eindeutig sein (über alle Abonnements und Kunden hinweg). Azure registriert den Namen und die IP-Adresse automatisch im DNS, sodass Sie über den Namen eine Verbindung mit der Ressource herstellen können. Azure fügt das Standardsubnetz *standort.cloudapp.azure.com* an den von Ihnen bereitgestellten Namen an, um den vollqualifizierten DNS-Namen zu erstellen. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternativ können Sie die folgenden CLI- und PowerShell-Befehle mit den Parametern **--public-ip-prefix** (CLI) und **-PublicIpPrefix** (PowerShell) verwenden, um eine öffentliche IP-Adressressource aus einem Präfix zu erstellen. 

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

>[!NOTE]
>Wenn Sie eine öffentliche IP-Adresse von einem Präfix für öffentliche IP-Adressen anfordern, ist die Zuordnung nicht deterministisch oder sequenziell. Wenn eine bestimmte öffentliche IP-Adresse aus einem Präfix für öffentliche IP-Adressen erforderlich ist, können Sie dies mit PowerShell- oder CLI-Befehlen realisieren.  Für PowerShell sollte der Parameter `IpAddress` (gefolgt von der gewünschten IP-Adresse) verwendet werden. Bei der Befehlszeilenschnittstelle verwenden Sie den Parameter `ip-address` (gefolgt von der gewünschten IP-Adresse).

>[!NOTE]
>Nur statische öffentliche IP-Adressen, die mit der Standard-SKU erstellt wurden, können aus dem Bereich des Präfix zugewiesen werden. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [Öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses).

## <a name="view-or-delete-a-prefix"></a>Anzeigen oder Löschen eines Präfix

Um ein Präfix anzuzeigen oder zu löschen, können Sie die folgenden Befehle an der Azure-Befehlszeilenschnittstelle und in Azure PowerShell verwenden.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) zum Auflisten öffentlicher IP-Adressen<br>[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) zum Anzeigen von Einstellungen<br> [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) zum Aktualisieren<br>[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) zum Löschen|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) zum Abrufen eines öffentlichen IP-Adressobjekts und zum Anzeigen seiner Einstellungen<br>[Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) zum Aktualisieren von Einstellungen<br> [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) zum Löschen|

## <a name="permissions"></a>Berechtigungen

Damit Ihr Konto über die erforderlichen Berechtigungen für das Verwalten öffentlicher IP-Adressen verfügt, muss es der Rolle [Netzwerkmitwirkender](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein. 

| Aktion                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Lesen des Präfix einer öffentlichen IP-Adresse                                |
| Microsoft.Network/publicIPPrefixes/write                          | Erstellen oder Aktualisieren des Präfix einer öffentlichen IP-Adresse                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Löschen des Präfix einer öffentlichen IP-Adresse                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Erstellen einer öffentlichen IP-Adresse aus einem Präfix |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über Szenarien und Vorteile der Verwendung des [Präfix einer öffentlichen IP](public-ip-address-prefix.md).

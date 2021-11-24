---
title: Gewähren von Zugriff auf die Indexer-IP-Adressbereiche
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie IP-Firewallregeln, um den Datenzugriff durch einen Azure Cognitive Search-Indexer zuzulassen.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 50ee624a3795015d35637e0e984a48025c802698
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492718"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>Konfigurieren von IP-Firewallregeln zum Zulassen von Indexerverbindungen in Azure Cognitive Search

Im Namen eines Indexers gibt ein Suchdienst ausgehende Aufrufe an eine externe Azure-Ressource aus, um während der Indizierung Daten einzuholen. Wenn Ihre Azure-Ressource IP-Firewall-Regeln zum Filtern eingehender Anrufe verwendet, müssen Sie eine eingehende Regel in Ihrer Firewall erstellen, die Indexer-Anfragen zulässt.

In diesem Artikel wird erklärt, wie Sie die IP-Adresse Ihres Suchdienstes herausfinden und dann das Azure-Portal verwenden, um eine eingehende IP-Regel für ein Azure-Storage-Konto zu konfigurieren. Dieser Ansatz ist zwar spezifisch für Azure Storage, funktioniert aber auch für andere Azure-Ressourcen, die IP-Firewall-Regeln für den Datenzugriff verwenden, wie Cosmos DB und Azure SQL.

> [!NOTE]
> IP-Firewall-Regeln für ein Speicherkonto sind nur wirksam, wenn sich das Speicherkonto und der Suchdienst in unterschiedlichen Regionen befinden. Wenn Ihre Einrichtung dies nicht zulässt, empfehlen wir als Alternative die Verwendung der Option [Vertrauenswürdige Dienstausnahme](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-a-search-service-ip-address"></a>IP-Adresse eines Suchdienstes abrufen

1. Ermitteln Sie den vollständig qualifizierten Domänennamen (FQDN) Ihres Suchdienstes. Dieser sieht wie `<search-service-name>.search.windows.net` aus. Sie können den FQDN ermitteln, indem Sie Ihren Suchdienst im Azure-Portal suchen.

   ![Abrufen des Dienst-FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Abrufen des Dienst-FQDN")

1. Suchen Sie die IP-Adresse des Suchdienstes, indem Sie einen `nslookup` (oder `ping`) des FQDN in einer Eingabeaufforderung durchführen.

1. Kopieren Sie die IP-Adresse, damit Sie sie im nächsten Schritt in einer eingehenden Regel angeben können. Im folgenden Beispiel lautet die IP-Adresse, die Sie kopieren sollten, "150.0.0.1".

   ```azurepowershell
   nslookup contoso.search.windows.net
   Server:  server.example.org
   Address:  10.50.10.50
    
   Non-authoritative answer:
   Name:    <name>
   Address:  150.0.0.1
   aliases:  contoso.search.windows.net
   ```

## <a name="get-ip-addresses-for-azurecognitivesearch-service-tag"></a>IP-Adressen für das Dienst-Tag "AzureCognitiveSearch" abrufen

Je nach Konfiguration Ihres Suchdienstes müssen Sie möglicherweise auch eine eingehende Regel erstellen, die Anfragen von einer Reihe von IP-Adressen zulässt. Insbesondere werden zusätzliche IP-Adressen für Anfragen verwendet, die aus der mandantenfähigen [Ausführungsumgebung des Indexers stammen](search-indexer-securing-resources.md#indexer-execution-environment). 

Sie können diesen IP-Adressbereich über das Dienst-Tag `AzureCognitiveSearch` abrufen.

1. Rufen Sie die IP-Adressbereiche für das `AzureCognitiveSearch` Service-Tag entweder über die [Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) oder die [herunterladbare JSON-Datei](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ab.

1. Handelt es sich bei dem Suchdienst um die Azure Public Cloud, sollte die [Azure Public JSON-Datei](https://www.microsoft.com/download/details.aspx?id=56519) heruntergeladen werden.

   ![Herunterladen der JSON-Datei](media\search-indexer-howto-secure-access\service-tag.png "Herunterladen der JSON-Datei")

1. Aus der JSON-Datei sind unter der Annahme, dass sich der Suchdienst in „USA, Westen-Mitte“ befindet, die IP-Adressen für die Ausführungsumgebung des Indexers für mehrere Mandanten nachfolgend aufgeführt.

```json
{
"name": "AzureCognitiveSearch.WestCentralUS",
"id": "AzureCognitiveSearch.WestCentralUS",
"properties": {
   "changeNumber": 1,
   "region": "westcentralus",
   "platform": "Azure",
   "systemService": "AzureCognitiveSearch",
   "addressPrefixes": [
      "52.150.139.0/26",
      "52.253.133.74/32"
   ]
}
}
```

Für `/32` IP-Adressen lassen Sie das "/32" weg (52.253.133.74/32 wird in der Regeldefinition zu 52.253.133.74). Alle anderen IP-Adressen können wortwörtlich verwendet werden.

## <a name="add-ip-addresses-to-ip-firewall-rules"></a>IP-Adressen zu IP-Firewall-Regeln hinzufügen

Sobald Sie die IP-Adressen haben, können Sie die Regel einrichten. Der einfachste Weg, IP-Adressbereiche zur Firewallregel eines Speicherkontos hinzuzufügen, ist über das Azure-Portal. 

1. Suchen Sie das Speicherkonto auf dem Portal, und navigieren Sie zur Registerkarte **Firewalls und virtuelle Netzwerke**.

   ![Firewall und virtuelle Netzwerke](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall und virtuelle Netzwerke")

1. Fügen Sie die drei zuvor erhaltenen IP-Adressen (eine für die Suchdienst-IP, zwei für das `AzureCognitiveSearch`-Dienst-Tag) in den Adressbereich ein und wählen Sie **Speichern**.

   ![IP-Regeln der Firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "IP-Regeln der Firewall")

Es kann fünf bis zehn Minuten dauern, bis die Firewall-Regeln aktualisiert sind. Danach sollten die Indexierer auf die Daten im Speicherkonto zugreifen können.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Azure Storage-Firewalls](../storage/common/storage-network-security.md)
- [Konfigurieren der IP-Firewall für Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Konfigurieren der IP-Firewall für Azure SQL Server](../azure-sql/database/firewall-configure.md)

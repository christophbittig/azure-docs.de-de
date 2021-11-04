---
title: Datei einfügen
description: include file
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 10/22/2021
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 777af1f15441ab4849292e40b3a27863276f3fce
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473343"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Enthaltener Speicher<sup>1</sup> (GiB) | 10 | 100 | 500 |
| Speicherbegrenzung (TiB) | 20| 20 | 20 |
| Maximale Größe für Imageebenen (GiB) | 200 | 200 | 200 |
| Maximale Manifestgröße (MiB) | 4 | 4 | 4 |
| Lesevorgänge pro Minute<sup>2, 3</sup> | 1\.000 | 3,000 | 10.000 |
| Schreibvorgänge pro Minute<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| Downloadbandbreite<sup>2</sup> (MBit/s) | 30 | 60 | 100 |
| Uploadbandbreite <sup>2</sup> (MBit/s) | 10 | 20 | 50 |
| webhooks | 2 | 10 | 500 |
| Georeplikation | – | – | [Unterstützt][geo-replication] |
| Verfügbarkeitszonen | – | – | [Vorschau][zones] |
| Inhaltsvertrauensstellung | – | – | [Unterstützt][content-trust] |
| Private Link mit privaten Endpunkten | – | – | [Unterstützt][plink] |
| &bull; Private Endpunkte | – | Nicht zutreffend | 200 |
| Netzwerkregeln für öffentliche IP-Adressen | – | – | 100 |
| Dienstendpunkt-VNET-Zugriff | – | – | [Vorschau][vnet] |
| &bull; VNet-Regeln | – | – | 100 |
| Vom Kunden verwaltete Schlüssel | – | – | [Unterstützt][cmk] |
| Repositorybezogene Berechtigungen | – | – | [Vorschau][token]|
| &bull; Token | – | – | 20.000 |
| &bull; Bereichszuordnungen | – | – | 20.000 |
| &bull; Repositorys pro Bereichszuordnung | – | – | 500 |


<sup>1</sup> In der täglichen Rate für jede Ebene inbegriffener Speicher. Für eine zusätzliche Tagesgebühr pro GiB kann weiterer Speicher bis zum Grenzwert des Registrierungsspeichers genutzt werden. Weitere Informationen zu diesen Tarifen finden Sie unter [Container Registry – Preise][pricing]. Falls Sie Speicher benötigen, der über den Grenzwert des Registrierungsspeichers hinausgeht, können Sie sich an den Azure-Support wenden.

<sup>2</sup>*Lesevorgänge*, *Schreibvorgänge* und *Bandbreite* sind geschätzte Mindestwerte. Die Azure Container Registry soll die Leistung der Nutzung nach Bedarf verbessern.

<sup>3</sup> Ein [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image)-Befehl wird basierend auf der Anzahl von Ebenen im Image in mehrere Lesevorgänge plus Manifestabruf übersetzt.

<sup>4</sup> Ein [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image)-Befehl wird basierend auf der Anzahl von Ebenen, die per Push übertragen werden müssen, in mehrere Schreibvorgänge übersetzt. Ein `docker push` enthält *Lesevorgänge* zum Abrufen eines Manifests für ein vorhandenes Image.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md

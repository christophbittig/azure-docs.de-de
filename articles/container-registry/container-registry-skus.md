---
title: Registrierungstarife und -funktionen
description: Erfahren Sie mehr über die Features und Beschränkungen (Kontingente) der Diensttarife (SKUs) „Basic“, „Standard“ und „Premium“ von Azure Container Registry.
ms.topic: article
ms.date: 08/12/2021
ms.openlocfilehash: 8cda435193e5a54216a562bdac51f036f93829f1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473344"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry-Tarife

Azure Container Registry steht in verschiedenen Tarifen (auch als SKUs bezeichnet) zur Verfügung. Durch diese Tarife können Preise im Voraus eingeschätzt werden. Darüber hinaus bieten sie verschiedene Optionen zur Anpassung an die Kapazität und das Nutzungsverhalten Ihrer privaten Docker-Registrierung in Azure.

| Tarif | BESCHREIBUNG |
| --- | ----------- |
| **Grundlegend** | Ein kostenoptimierter Einstiegspunkt für Entwickler, die sich mit Azure Container Registry vertraut machen. Basic-Registrierungen verfügen über die gleichen Programmfunktionen wie Standard- und Premium-Registrierungen (z.B. Azure Active Directory-[Authentifizierungsintegration](container-registry-authentication.md#individual-login-with-azure-ad), [Löschen von Images][container-registry-delete] und [Webhooks][container-registry-webhook]). Der gebotene Speicher- und Imagedurchsatz ist jedoch für Szenarien mit geringerer Nutzung am besten geeignet. |
| **Standard** | Standard-Registrierungen bieten die gleichen Funktionen wie Basic, jedoch höhere Speichergrenzwerte und einen höheren Imagedurchsatz. Standard-Registrierungen erfüllen üblicherweise die Bedürfnisse der meisten Produktionsszenarios. |
| **Premium** | Premium-Registrierungen bieten den größten Umfang an Speicher und gleichzeitigen Vorgängen, sodass Szenarien mit großen Volumen möglich sind. Neben einem höheren Imagedurchsatz bietet Premium Funktionen wie [Georeplikation][container-registry-geo-replication] zum regionsübergreifenden Verwalten einer einzelnen Registrierung, [Inhaltsvertrauensstellung](container-registry-content-trust.md) für das Signieren von Imagetags sowie [private Links mit privaten Endpunkten](container-registry-private-link.md) zum Einschränken des Zugriffs auf die Registrierung. |

Die Tarife „Basic“, „Standard“ und „Premium“ bieten alle dieselben programmgesteuerten Funktionen. Sie alle profitieren auch von [Imagespeicher][container-registry-storage], der vollständig von Azure verwaltet wird. Wenn Sie sich für einen Tarif auf höherer Ebene entscheiden, können Sie von einer höheren Leistung und Skalierung profitieren. Wenn mehrere Dienstebenen vorhanden sind, können Sie mit „Basic“ beginnen und dann bei zunehmender Registrierungsnutzung in „Standard“ und „Premium“ konvertieren.

## <a name="service-tier-features-and-limits"></a>Tariffunktionen und -limits

Die folgende Tabelle enthält die Features und Registrierungsgrenzwerte der Dienstebenen „Basic“, „Standard“ und „Premium“:

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="registry-throughput-and-throttling"></a>Registrierungsdurchsatz und -drosselung

### <a name="throughput"></a>Throughput 

Wenn Sie eine hohe Rate von Registrierungsvorgängen generieren, orientieren Sie sich beim zu erwartenden maximalen Durchsatz an den Dienstebenengrenzwerten für Lese- und Schreibvorgänge sowie für die Bandbreite. Diese Grenzwerte wirken sich auf Datenebenenvorgänge wie Auflisten, Löschen, Pushen und Pullen von Images und anderen Artefakten aus.

Berücksichtigen Sie speziell bei der Schätzung des Durchsatzes von Pull- und Pushvorgängen für Images die Registrierungsgrenzwerte und die folgenden Faktoren: 

* Anzahl und Größe von Imageebenen
* Wiederverwendung von Ebenen oder Basisimages in verschiedenen Images
* Zusätzliche API-Aufrufe, die ggf. für jeden Pull- oder Pushvorgang erforderlich sind

Ausführliche Informationen finden Sie in der Dokumentation zur [Docker-HTTP-API V2](https://docs.docker.com/registry/spec/api/).

Berücksichtigen Sie bei der Auswertung oder Problembehandlung im Zusammenhang mit dem Registrierungsdurchsatz auch die Konfiguration Ihrer Clientumgebung:

* Ihre Docker-Daemonkonfiguration für gleichzeitige Vorgänge
* Ihre Netzwerkverbindung mit dem Datenendpunkt der Registrierung (oder mit mehreren Endpunkten, falls Ihre Registrierung [georepliziert](container-registry-geo-replication.md) wird)

Informationen zu Problemen mit dem Registrierungsdurchsatz finden Sie bei Bedarf unter [Beheben von Problemen mit der Registrierungsleistung](container-registry-troubleshoot-performance.md). 

#### <a name="example"></a>Beispiel

Beim Pushen eines einzelnen Images vom Typ `nginx:latest` mit 133 MB in eine Azure-Containerregistrierung sind mehrere Lese- und Schreibvorgänge für die fünf Ebenen des Images erforderlich: 

* Lesevorgänge zum Lesen des Imagemanifests, sofern es in der Registrierung vorhanden ist
* Schreibvorgänge zum Schreiben des Konfigurationsblobs des Images
* Schreibvorgänge zum Schreiben des Imagemanifests

### <a name="throttling"></a>Drosselung

Pull- oder Pushvorgänge können gedrosselt werden, wenn von der Registrierung festgestellt wird, dass die Anforderungsrate die für die Dienstebene der Registrierung zulässigen Grenzwerte übersteigt. Möglicherweise wird ein HTTP 429-Fehler wie der folgende angezeigt: `Too many requests`.

Eine Drosselung kann vorübergehend auftreten, wenn Sie innerhalb eines sehr kurzen Zeitraums eine große Menge von Pull- oder Pushvorgängen für Images generieren, auch wenn die durchschnittliche Rate von Lese- und Schreibvorgängen innerhalb der Registrierungsgrenzwerte liegt. Unter Umständen müssen Sie eine Wiederholungslogik mit Backoff in Ihrem Code implementieren oder die maximale Anforderungsrate für die Registrierung verringern.

## <a name="show-registry-usage"></a>Registrierungsnutzung anzeigen

Verwenden Sie den Befehl [az acr show-usage](/cli/azure/acr#az_acr_show_usage) oder die REST-API [Nutzungen auflisten](/rest/api/containerregistry/registries/list-usages), um eine Momentaufnahme Ihres Speicherverbrauchs und anderer Ressourcen im Vergleich zu den Grenzwerten für die Dienstebene dieser Registrierung zu erhalten. Speicherverwendung wird auch auf der Seite **Übersicht** der Registrierung im Portal angezeigt.

Nutzungsinformationen helfen Ihnen, Entscheidungen zum [Ändern der Dienstebene](#changing-tiers) zu treffen, wenn sich ihre Registrierung einem Grenzwert nähert. Diese Informationen helfen Ihnen auch dabei, [Ihren Verbrauch unter Kontrolle zu halten](container-registry-best-practices.md#manage-registry-size). 

> [!NOTE]
> Die Speichernutzung der Registrierung sollte nur als Leitfaden verwendet werden und spiegelt möglicherweise nicht die aktuellen Registrierungsvorgänge wider. Überwachen Sie die [Metrik des verwendeten Speichers](monitor-service-reference.md#container-registry-metrics) der Registrierung, um aktuelle Daten zu erhalten. 

Abhängig von der Dienstebene Ihrer Registrierung umfassen die Nutzungsinformationen einige oder alle der folgenden Angaben sowie den Grenzwert in dieser Ebene:

* Verbrauchter Speicher in Bytes<sup>1</sup>
* Anzahl von [Webhooks](container-registry-webhook.md)
* Anzahl der [Georeplikate](container-registry-geo-replication.md) (einschließlich des Homereplikats)
* Anzahl der [privaten Endpunkte](container-registry-private-link.md)
* Anzahl von [IP-Zugriffsregeln](container-registry-access-selected-networks.md)
* Anzahl von [Regeln für das virtuelle Netzwerk](container-registry-vnet.md)

<sup>1</sup>In einer geo-replizierten Registrierung wird Speichernutzung für die Heimregion angezeigt. Multiplizieren Sie mit der Anzahl der Replikationen, um den gesamten verbrauchten Speicher zu erhalten.

## <a name="changing-tiers"></a>Wechseln von Tarifen

Sie können den Tarif einer Registrierung über die Azure CLI oder im Azure-Portal wechseln. Zwischen den Tarifen können Sie sich frei bewegen, solange der Tarif, zu dem Sie wechseln, über die erforderliche maximale Speicherkapazität verfügt. 

Wenn Sie zwischen den Tarifen wechseln, treten keine Registrierungsausfälle oder Auswirkungen auf Registrierungsvorgänge auf.

### <a name="azure-cli"></a>Azure CLI

Zwischen Tarifen kann mithilfe des Azure CLI-Befehls [az acr update][az-acr-update] gewechselt werden. Gehen Sie beispielsweise wie folgt vor, um zu Premium zu wechseln:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure-Portal

Klicken Sie im Azure-Portal in der Containerregistrierung **Übersicht** auf **Update**, und wählen Sie anschließend im Dropdownmenü die neue **SKU** aus.

![Aktualisieren der Containerregistrierung-SKU im Azure-Portal][update-registry-sku]

## <a name="pricing"></a>Preise

Informationen zu Preisen für jeden Tarif von Azure Container Registry finden Sie unter [Preise von Container Registry][container-registry-pricing].

Weitere Informationen zu den Preisen für Datenübertragungen finden Sie unter [Bandbreite: Preisdetails](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Nächste Schritte

**Azure Container Registry – Roadmap**

Besuchen Sie [ACR Roadmap][acr-roadmap] auf GitHub, wo Sie Informationen zu zukünftigen Features des Diensts finden.

**Azure Container Registry – UserVoice**

Unter [ACR UserVoice][container-registry-uservoice] können Sie neue Featurevorschläge einsenden oder für diese abstimmen.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/d365community/forum/180a533d-0d25-ec11-b6e6-000d3a4f0858

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md

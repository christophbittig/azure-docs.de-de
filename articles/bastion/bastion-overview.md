---
title: Azure Bastion | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über Azure Bastion. Dieser Dienst ermöglicht sichere und nahtlose RDP-/SSH-Konnektivität mit Ihren virtuellen Computern, ohne RDP-/SSH-Ports extern verfügbar zu machen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: contperf-fy2q1-portal, ignite-fall-2021
ms.openlocfilehash: 6fbfd04283e5bf89be32c89294ad5d26fb6e5af2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426694"
---
# <a name="what-is-azure-bastion"></a>Was ist Azure Bastion?

Azure Bastion ist ein von Ihnen bereitgestellter Dienst, mit dem Sie eine Verbindung mit einem virtuellen Computer herstellen können, indem Sie Ihren Browser und das Azure-Portal verwenden. Bei Azure Bastion handelt es sich um einen vollständig verwalteten PaaS-Dienst, den Sie in Ihrem virtuellen Netzwerk bereitstellen können. Dieser Dienst ermöglicht sichere und nahtlose RDP- und SSH-Verbindungen mit Ihren virtuellen Computern über TLS direkt im Azure-Portal. Beim Herstellen einer Verbindung über Azure Bastion benötigen Ihre virtuellen Computer keine öffentliche IP-Adresse, keinen Agent und keine spezielle Clientsoftware.

Bastion bietet sichere RDP- und SSH-Verbindungen mit allen virtuellen Computern in dem virtuellen Netzwerk, in dem der Dienst bereitgestellt wird. Durch die Verwendung von Azure Bastion wird verhindert, dass Ihre virtuellen Computer RDP- und SSH-Ports öffentlich verfügbar machen. Gleichzeitig wir weiterhin der sichere Zugriff per RDP/SSH ermöglicht.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Diagramm der Azure Bastion-Architektur":::

## <a name="key-benefits"></a><a name="key"></a>Hauptvorteile

* **RDP und SSH direkt im Azure-Portal:** Sie können RDP- und SSH-Sitzungen nahtlos mit nur einem Klick direkt über das Azure-Portal aufrufen.
* **Remotesitzung über TLS und Firewalldurchlauf für RDP/SSH:** Azure Bastion verwendet einen HTML5-basierten Webclient, der automatisch an Ihr lokales Gerät gestreamt wird. Sie können die RDP- oder SSH-Sitzung über TLS an Port 443 aufrufen und so Firewalls des Unternehmens sicher durchlaufen.
* **Keine öffentliche IP-Adresse erforderlich für den virtuellen Azure-Computer:** Azure Bastion öffnet die RDP- oder SSH-Verbindung mit Ihrem virtuellen Azure-Computer über eine private IP-Adresse für den virtuellen Computer. Sie benötigen keine öffentliche IP-Adresse für den virtuellen Computer.
* **Kein Aufwand beim Verwalten von Netzwerksicherheitsgruppen (NSGs):** Azure Bastion ist ein vollständig verwalteter PaaS-Dienst von Azure, der intern gehärtet ist, um sichere RDP- und SSH-Konnektivität zu bieten. Sie müssen im Azure Bastion-Subnetz keine NSGs anwenden. Da mit Azure Bastion Verbindungen mit Ihren virtuellen Computern über private IP-Adressen hergestellt werden, können Sie die Netzwerksicherheitsgruppen so konfigurieren, dass RDP/SSH nur über Azure Bastion zulässig ist. Dadurch entfällt der Aufwand für die Verwaltung von Netzwerksicherheitsgruppen, wenn Sie eine sichere Verbindung mit den virtuellen Computern herstellen. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules).
* **Schutz vor Portscans:** Da Ihre VMs nicht im öffentlichen Internet verfügbar gemacht werden müssen, sind sie vor Portscans durch nicht autorisierte und böswillige Benutzer außerhalb Ihres virtuellen Netzwerks geschützt.
* **Schutz vor Zero-Day-Exploits. Härtung an nur einem Ort:** Azure Bastion ist ein vollständig verwalteter PaaS-Dienst. Da er sich im Umkreis Ihres virtuellen Netzwerks befindet, ist eine Härtung der einzelnen virtuellen Computer im virtuellen Netzwerk nicht erforderlich. Dank konsequenter Härtung und Aktualisierung von Azure Bastion bietet die Azure-Plattform Schutz vor Zero-Day-Exploits.

## <a name="skus"></a><a name="sku"></a>SKUs

Für Azure Bastion sind zwei SKUs verfügbar: Basic und Standard. Weitere Informationen, auch zum Upgraden einer SKU, finden Sie im Artikel [Konfigurationseinstellungen](configuration-settings.md#skus).

Die folgende Tabelle enthält die Features der entsprechenden SKUs.

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

## <a name="architecture"></a><a name="architecture"></a>Architektur

Azure Bastion wird in einem virtuellen Netzwerk bereitgestellt und unterstützt das Peering virtueller Netzwerke. Insbesondere verwaltet Azure Bastion RDP-/SSH-Verbindungen mit VMs, die in den lokalen oder mit Peering verbundenen virtuellen Netzwerken erstellt wurden.

RDP und SSH sind grundlegende Protokolle, über die Sie eine Verbindung mit Ihren in Azure ausgeführten Workloads herstellen können. Das Verfügbarmachen von RDP- oder SSH-Ports über das Internet ist nicht erwünscht und wird als erhebliche Angriffsfläche angesehen. Dies ist häufig auf Protokollschwachstellen zurückzuführen. Um diese Angriffsfläche einzudämmen, können Sie Bastion-Hosts (auch Jumpserver genannt) auf der öffentlichen Seite Ihres Umkreisnetzwerks bereitstellen. Bastion-Hostserver sind so konzipiert und konfiguriert, dass sie Angriffen standhalten. Bastion-Server bieten außerdem RDP- und SSH-Verbindungen mit den Workloads, die sich hinter dem Bastion-Server und weiter innen im Netzwerk befinden.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Diagramm der Azure Bastion-Architektur":::

In dieser Abbildung ist die Architektur einer Azure Bastion-Bereitstellung wie folgt In diesem Diagramm:

* Der Bastionhost wird in dem virtuellen Netzwerk bereitgestellt, in dem das Subnetz „AzureBastionSubnet“ (mindestens Präfix „/26“) enthalten ist.
* Der Benutzer stellt in einem HTML5-Browser eine Verbindung mit dem Azure-Portal her.
* Der Benutzer wählt den virtuellen Computer für die Verbindung aus.
* Mit nur einem Klick wird die RDP- oder SSH-Sitzung im Browser geöffnet.
* Für den virtuellen Azure-Computer ist keine öffentliche IP-Adresse erforderlich.

## <a name="host-scaling"></a><a name="host-scaling"></a>Hostskalierung

Azure Bastion unterstützt die manuelle Hostskalierung. Sie können die Anzahl der Hostinstanzen (Skalierungseinheiten) konfigurieren, um zu verwalten, wie viele gleichzeitige RDP-/SSH-Verbindungen Azure Bastion unterstützen kann. Wenn Sie die Anzahl der Hostinstanzen erhöhen, kann Azure Bastion mehr gleichzeitige Sitzungen verwalten. Bei einer Verringerung der Anzahl von Instanzen wird die Anzahl unterstützter gleichzeitiger Sitzungen reduziert. Azure Bastion unterstützt bis zu 50 Hostinstanzen. Dieses Feature ist nur für die Standard-SKU von Azure Bastion verfügbar.

Weitere Informationen finden Sie im Artikel zu den [Konfigurationseinstellungen](configuration-settings.md#instance).

## <a name="pricing"></a><a name="pricing"></a>Preise

Die Preise für Azure Bastion setzen sich aus einer Kombination der Stundenpreise basierend auf der SKU, den Skalierungseinheiten und den Datenübertragungsraten zusammen. Informationen zu den Preisen finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/azure-bastion) .

## <a name="whats-new"></a><a name="new"></a>Neuerungen

Abonnieren Sie den RSS-Feed, und zeigen Sie die neuesten Azure Bastion-Featureupdates auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion) an.

## <a name="bastion-faq"></a>Häufig gestellte Fragen zu Bastion

Häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen zu Azure Bastion](bastion-faq.md).

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erstellen eines Azure Bastion-Hosts und Herstellen einer Verbindung mit einem virtuellen Windows-Computer](tutorial-create-host-portal.md).
* [Learn-Modul: Einführung in Azure Bastion](/learn/modules/intro-to-azure-bastion/)
* Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/fundamentals/networking-overview.md) von Azure.

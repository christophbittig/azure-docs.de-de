---
title: Automatische Skalierung und zonenredundantes Application Gateway v2
description: In diesem Artikel werden die Standard_v2- und WAF_v2-SKU der Azure-Anwendung vorgestellt, die Autoskalierung und zonenredundante Features umfassen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 17e50b602bcab7dd3a40e6823bea44eb63603b9d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313088"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatische Skalierung und zonenredundantes Application Gateway v2 

Application Gateway ist in einer Standard_v2-SKU erhältlich. Web Application Firewall (WAF) ist in einer WAF_v2-SKU erhältlich. Die v2-SKU bietet Leistungsverbesserungen und zusätzliche Unterstützung für wichtige neue Features wie automatische Skalierung und Zonenredundanz sowie Unterstützung für statische VIPs. Vorhandene Features in der Standard- and WAF-SKU werden in der neuen v2-SKU weiterhin unterstützt. Es gelten aber einige wenige Ausnahmen, die im Abschnitt mit dem [Vergleich](#differences-from-v1-sku) aufgeführt sind.

Die neue v2-SKU enthält die folgenden Verbesserungen:

- **Automatische Skalierung**: Für Application Gateway- oder WAF-Bereitstellungen unter der SKU mit automatischer Skalierung ist nun das Hochskalieren und Herunterskalieren je nach Veränderung der Netzwerkdatenverkehr-Auslastungsmuster möglich. Durch die automatische Skalierung entfällt auch die Notwendigkeit, während des Bereitstellens eine Bereitstellungsgröße oder eine Anzahl von Instanzen auszuwählen. Diese SKU bietet wahre Elastizität. In der Standard_v2- und WAF_v2-SKU kann Application Gateway sowohl mit fester Kapazität (automatische Skalierung deaktiviert) als auch mit aktivierter automatischer Skalierung betrieben werden. Der Modus mit fester Kapazität empfiehlt sich für Szenarien mit einheitlichen und vorhersagbaren Workloads. Der Modus mit automatischer Skalierung empfiehlt sich für Anwendungen, bei denen der Anwendungsdatenverkehr Schwankungen unterworfen ist.
- **Zonenredundanz**: Eine Application Gateway- oder WAF-Bereitstellung kann sich über mehrere Verfügbarkeitszonen erstrecken, sodass nicht mehr in jeder Zone mit einem Traffic Manager separate Application Gateway-Instanzen bereitgestellt werden müssen. Sie können eine einzelne Zone oder mehrere Zonen mit bereitgestellten Application Gateway-Instanzen auswählen, um für größere Stabilität gegenüber Zonenausfällen zu sorgen. Der Back-End-Pool für Anwendungen kann auf ähnliche Weise auf Verfügbarkeitszonen verteilt werden.

  Zonenredundanz ist nur dort verfügbar, wo auch Azure-Zonen verfügbar sind. In anderen Regionen werden alle anderen Features unterstützt. Weitere Informationen finden Sie unter [Was sind Verfügbarkeitszonen in Azure?](../availability-zones/az-overview.md).
- **Statische VIP**: Die Application Gateway v2-SKU unterstützt exklusiv den statischen VIP-Typ. Dadurch wird sichergestellt, dass die dem Application Gateway zugeordnete VIP während des Lebenszyklus der Bereitstellung unverändert bleibt, selbst nach einem Neustart.  Es gibt keine statische VIP in v1, daher müssen Sie für das Routing des Domänennamens an App Services über das Application Gateway die URL des Application Gateways anstelle der IP-Adresse verwenden.
- **Erneutes Generieren von Headern**: Application Gateway ermöglicht Ihnen das Hinzufügen, Entfernen oder Aktualisieren von HTTP-Anforderungs- und Antwortheadern mit dem v2-SKU. Weitere Informationen finden Sie unter [Erneutes Generieren von HTTP-Headern mit Application Gateway](./rewrite-http-headers-url.md).
- **Key Vault-Integration**: Application Gateway v2 unterstützt die Integration in Key Vault für Serverzertifikate, die HTTPS-fähigen Listenern zugeordnet sind. Weitere Informationen finden Sie unter [TLS-Terminierung mit Key Vault-Zertifikaten](key-vault-certs.md).
- **Azure Kubernetes Service-Eingangscontroller**: Der Application Gateway v2-Eingangscontroller ermöglicht die Verwendung von Azure Application Gateway als Eingang für einen Azure Kubernetes Service (AKS), der als AKS-Cluster bezeichnet wird. Weitere Informationen finden Sie unter [Was ist der Application Gateway-Eingangscontroller?](ingress-controller-overview.md).
- **Leistungsverbesserungen**: Die v2-SKU bietet eine bis zu fünfmal bessere TLS-Auslagerungsleistung verglichen mit der Standard-/WAF-SKU.
- **Schnellere Bereitstellung und Aktualisierung**: Die v2-SKU ermöglicht verglichen mit der Standard/WAF-SKU eine schnellere Bereitstellung und Aktualisierung. Dies umfasst auch Änderungen an der WAF-Konfiguration.

![Diagramm der Zone mit automatischer Skalierung.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Unterstützte Regionen

Die Standard_v2- und WAF_v2-SKU ist in den folgenden Regionen verfügbar: USA, Norden-Mitte, USA, Süden-Mitte, USA, Westen, USA, Westen 2, USA, Osten, USA, Osten 2, USA, Mitte, Europa, Norden, Europa, Westen, Asien, Südosten, Frankreich, Mitte, Vereinigtes Königreich, Westen, Japan, Osten, Japan, Westen, Australien, Osten, Australien, Südosten, Brasilien, Süden, Kanada, Mitte, Kanada, Osten, Asien, Osten, Südkorea, Mitte, Südkorea, Süden, Vereinigtes Königreich, Süden, Indien, Mitte, Indien, Westen, Indien, Süden, Jio Indien, Westen, Norwegen, Osten, Schweiz, Norden, VAE, Norden, Südafrika, Norden, Deutschland, Westen-Mitte.

## <a name="pricing"></a>Preise

Bei der v2-SKU basiert das Preismodell auf der Nutzung und ist nicht mehr an die Anzahl oder Größe von Instanzen gebunden. Der Preis der v2-SKU setzt sich aus zwei Komponenten zusammen:

- **Festpreis**: Dies ist der Preis pro Stunde (oder Teil einer Stunde) für die Bereitstellung eines Standard_v2- oder WAF_v2-Gateways. Beachten Sie, dass auch mit 0 zusätzlichen Mindestinstanzen weiterhin die Hochverfügbarkeit des Diensts gewährleistet wird, die grundsätzlich im Festpreis inbegriffen ist.
- **Preis nach Kapazitätseinheit**: Dies sind die nutzungsbasierten Kosten, die zusätzlich zu den Fixkosten berechnet werden. Die Gebühr für Kapazitätseinheiten wird ebenfalls für volle oder teilweise Stunden berechnet. Die Kapazitätseinheit setzt sich aus drei Größen zusammen: Compute-Einheit, permanente Verbindungen und Durchsatz. Die Compute-Einheit ein Maß für die genutzte Prozessorkapazität. Faktoren, die sich auf die Compute-Einheit auswirken, sind TLS-Verbindungen/Sekunde, URL-Rewrite-Berechnungen und Verarbeitung von WAF-Regeln. Permanente Verbindung ist ein Maß für eingerichtete TCP-Verbindungen mit Application Gateway in einem bestimmten Abrechnungsintervall. Der Durchsatz ist die durchschnittliche Anzahl von Megabits pro Sekunde, die vom System in einem bestimmten Abrechnungsintervall verarbeitet werden.  Die Abrechnung erfolgt für alles, was die reservierte Instanzanzahl übersteigt, auf Kapazitätseinheitsebene.

Jede Kapazitätseinheit setzt sich maximal zusammen aus: 1 Compute-Einheit, 2500 permanente Verbindungen und 2,22 MBit/s Durchsatz.

Weitere Informationen finden Sie unter [Grundlegendes zu Preisen für Azure Application Gateway und Web Application Firewall](understanding-pricing.md).

## <a name="scaling-application-gateway-and-waf-v2"></a>Skalierung von Application Gateway und WAF v2

Application Gateway und WAF können für die Skalierung in zwei Modi konfiguriert werden:

- **Automatische Skalierung**: Bei aktivierter automatischer Skalierung können Application Gateway und WAF v2-SKUs basierend auf den Datenverkehrsanforderungen der Anwendung hoch- oder herunterskalieren. Dieser Modus bietet bessere Elastizität für Ihre Anwendung und Größe oder Anzahl der Instanzen von Application Gateway müssen nicht geschätzt werden. Außerdem ermöglicht dieser Modus Kosteneinsparungen, weil das Gateway nicht mit bereitgestellter Spitzenkapazität für die erwartete maximale Auslastung ausgeführt werden muss. Sie müssen eine Mindestanzahl und optional eine Höchstanzahl von Instanzen angeben. Durch eine Mindestkapazität wird sichergestellt, dass Application Gateway und WAF v2 nicht unter die angegebene Mindestanzahl von Instanzen abfallen, selbst wenn kein Datenverkehr vorhanden ist. Jede Instanz entspricht in etwa zehn zusätzlichen reservierten Kapazitätseinheiten. Bei 0 wird keine Kapazität reserviert. Dieser Wert dient ausschließlich zur automatischen Skalierung. Sie können optional auch eine maximale Anzahl von Instanzen angeben, durch die sichergestellt wird, dass Application Gateway nicht über die angegebene Anzahl von Instanzen hinaus skaliert. Ihnen wird nur die vom Gateway bereitgestellte Menge an Datenverkehr in Rechnung gestellt. Die Anzahl der Instanzen kann zwischen 0 und 125 liegen. Wenn kein Wert angegeben ist, liegt der Standardwert für die maximale Anzahl von Instanzen bei 20.
- **Manuell**: Sie können alternativ auch den manuellen Modus auswählen, in dem das Gateway nicht automatisch skaliert wird. Wenn in diesem Modus mehr Datenverkehr vorliegt, als von Application Gateway oder WAF verarbeitet werden kann, kann dies zu einem Datenverlust führen. Beim manuellen Modus ist die Angabe der Anzahl von Instanzen obligatorisch. Die Anzahl der Instanzen kann zwischen 1 und 125 variieren.

## <a name="autoscaling-and-high-availability"></a>Automatische Skalierung und Hochverfügbarkeit

Azure Application Gateway-Instanzen werden immer hochverfügbar bereitgestellt. Der Dienst setzt sich aus mehreren Instanzen zusammen, die entweder gemäß der Konfiguration (bei deaktivierter automatischer Skalierung) oder unter Berücksichtigung der Anwendungslast (bei aktivierter automatischer Skalierung) erstellt werden. Beachten Sie, dass aus Benutzersicht nicht unbedingt ein Einblick in die einzelnen Instanzen, sondern lediglich in den Application Gateway-Dienst als Ganzes möglich ist. Sollte in einer bestimmten Instanz ein Problem auftreten und die Instanz nicht mehr funktionieren, wird von Azure Application Gateway auf transparente Weise eine neue Instanz erstellt.

Beachten Sie, dass der Dienst auch dann noch hochverfügbar ist, wenn Sie die automatische Skalierung mit null Mindestinstanzen konfigurieren. Dies ist immer im Festpreis enthalten.

Die Erstellung einer neuen Instanz kann jedoch etwas dauern (etwa sechs oder sieben Minuten). Zur Vermeidung dieser Downtime können Sie daher die Mindestanzahl von Instanzen auf „2“ festlegen (idealerweise mit Verfügbarkeitszonenunterstützung). Dadurch sind im Normalfall mindestens zwei Instanzen in Azure Application Gateway vorhanden. Sollte nun in einer dieser Instanzen ein Problem auftreten, wird während der Erstellung einer neuen Instanz von der anderen Instanz versucht, den Datenverkehr zu bewältigen. Beachten Sie, dass von einer Azure Application Gateway-Instanz etwa zehn Kapazitätseinheiten unterstützt werden können. Daher empfiehlt es sich gegebenenfalls, die Einstellung für die Mindestanzahl von Instanzen für die automatische Skalierung abhängig von Ihrer üblichen Datenverkehrsmenge auf einen höheren Wert als „2“ festzulegen.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Funktionsvergleich zwischen v1 SKU und v2 SKU

In der folgenden Tabelle werden die Features der einzelnen SKUs gegenübergestellt.

| Funktion                                           | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatische Skalierung                                       |          | &#x2713; |
| Zonenredundanz                                   |          | &#x2713; |
| Statische VIP                                        |          | &#x2713; |
| AKS-Eingangscontroller (Azure Kubernetes Service) |          | &#x2713; |
| Azure-Schlüsseltresor-Integration                       |          | &#x2713; |
| Erneutes Generieren von HTTP(S)-Headern                           |          | &#x2713; |
| URL-basiertes Routing                                 | &#x2713; | &#x2713; |
| Hosten mehrerer Websites                             | &#x2713; | &#x2713; |
| Umleitung des Datenverkehrs                               | &#x2713; | &#x2713; |
| Web Application Firewall (WAF)                    | &#x2713; | &#x2713; |
| Benutzerdefinierte WAF-Regeln                                  |          | &#x2713; |
| Beendigung mit TLS (Transport Layer Security)/SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| End-to-End-TLS-Verschlüsselung                         | &#x2713; | &#x2713; |
| Sitzungsaffinität                                  | &#x2713; | &#x2713; |
| Benutzerdefinierte Fehlerseiten                                | &#x2713; | &#x2713; |
| WebSocket-Unterstützung                                 | &#x2713; | &#x2713; |
| HTTP/2-Unterstützung                                    | &#x2713; | &#x2713; |
| Verbindungsausgleich                               | &#x2713; | &#x2713; |

> [!NOTE]
> Die v2-SKU mit automatischer Skalierung unterstützt nun [Standardintegritätstests](application-gateway-probe-overview.md#default-health-probe), um die Integrität aller Ressourcen im Back-End-Pool automatisch zu überwachen und solche Back-End-Elemente zu markieren, die als fehlerhaft gelten. Der Standardintegritätstest wird automatisch für Back-Ends konfiguriert, die keine benutzerdefinierte Testkonfiguration aufweisen. Weitere Informationen finden Sie unter [Systemüberwachung des Application Gateways](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>Unterschiede zur v1-SKU

In diesem Abschnitt werden die Features und Einschränkungen der v2-SKU beschrieben, die sich von denen der v1-SKU unterscheiden.

|Unterschied|Details|
|--|--|
|Authentifizierungszertifikat|Wird nicht unterstützt.<br>Weitere Informationen finden Sie unter [Übersicht über End-to-End-TLS mit Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Mischen von Standard_v2 und Standardanwendungsgateway in demselben Subnetz|Nicht unterstützt|
|Benutzerdefinierte Route im Application Gateway-Subnetz|Unterstützt (spezifische Szenarien). In der Vorschauversion.<br> Weitere Informationen zu unterstützten Szenarien finden Sie unter [Application Gateway-Konfiguration: Übersicht](configuration-infrastructure.md#supported-user-defined-routes).|
|NSG für Eingangsportbereich| - 65200 bis 65535 für Standard_v2-SKU<br>- 65503 bis 65534 für Standard-SKU.<br>Weitere Informationen finden Sie in den [häufig gestellten Fragen](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Leistungsprotokolle in Azure-Diagnose|Wird nicht unterstützt.<br>Es sollten Azure-Metriken verwendet werden.|
|Abrechnung|Die Abrechnung soll planmäßig am 1. Juli 2019 beginnen.|
|FIPS-Modus|Diese werden derzeit nicht unterstützt.|
|Reiner ILB-Modus|Dies wird derzeit nicht unterstützt. Öffentlicher und ILB-Modus kombiniert werden unterstützt.|
|Integration mit Net Watcher|Wird nicht unterstützt.|
|Integration von Microsoft Defender für Cloud|Noch nicht verfügbar.

## <a name="migrate-from-v1-to-v2"></a>Migrieren von v1 zu v2

Im PowerShell-Katalog ist ein Azure PowerShell-Skript verfügbar, mit dessen Hilfe Sie Ihr v1-Application Gateway / Ihre v1-WAF zur v2-SKU mit automatischer Skalierung migrieren können. Mithilfe dieses Skript können Sie die Konfiguration von Ihrem v1-Gateway kopieren. Die Datenverkehrsmigration liegt weiterhin in Ihrer Verantwortung. Weitere Informationen finden Sie unter [Migrieren von Azure Application Gateway und Web Application Firewall von v1 zu v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal](quick-create-portal.md)
- [Erstellen Sie mit Azure PowerShell ein zonenredundantes Application Gateway mit automatischer Skalierung und einer reservierten virtuellen IP-Adresse.](tutorial-autoscale-ps.md)
- Erfahren Sie mehr über [Application Gateway](overview.md).
- Erfahren Sie mehr über [Azure Firewall](../firewall/overview.md).

---
title: Was ist eine verbundene Registrierung?
description: Übersicht und Szenarien zum Feature für die verbundene Registrierung von Azure Container Registry
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ccc80f2ce5fb8393824686101d2069beee58efa4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029721"
---
# <a name="what-is-a-connected-registry"></a>Was ist eine verbundene Registrierung? 

In diesem Artikel lernen Sie das Feature *verbundene Registrierung* von [Azure Container Registry](container-registry-intro.md) kennen. Eine verbundene Registrierung ist ein lokales Replikat oder ein Remotereplikat, das Containerimages und andere OCI-Artefakte mit Ihrer cloudbasierten Azure-Containerregistrierung synchronisiert. Verwenden Sie eine verbundene Registrierung, um den Zugriff auf lokale Registrierungsartefakte zu beschleunigen und erweiterte Szenarien zu erstellen, z. B. über [IoT Edge mit Schachtelung](../iot-edge/tutorial-nested-iot-edge.md).

> [!NOTE]
> Die verbundene Registrierung ist ein Vorschaufeature der Container Registry-Dienstebene **Premium**, die [Einschränkungen](#limitations) unterliegt. Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

## <a name="available-regions"></a>Verfügbare Regionen

* Asien, Osten
* Europa, Norden
* Europa, Westen
* USA, Osten

## <a name="scenarios"></a>Szenarien

Eine cloudbasierte Azure-Containerregistrierung bietet [Features](container-registry-intro.md#key-features) wie Georeplikation, integrierte Sicherheit, von Azure verwalteten Speicher und die Integration in Azure-Entwicklungs- und -Bereitstellungspipelines. Gleichzeitig weiten Kunden Cloudinvestitionen auf ihre lokalen und auf Remotelösungen aus.

Damit Containerworkloads mit der erforderlichen Leistung und Zuverlässigkeit in lokalen oder Remoteumgebungen ausgeführt werden können, müssen Containerimages und zugehörige Artefakte in der Nähe verfügbar sein. Die verbundene Registrierung bietet eine leistungsfähige, lokale Registrierungslösung, die regelmäßig Inhalte mit einer cloudbasierten Azure-Containerregistrierung synchronisiert.

Zu den Szenarien für eine verbundene Registrierung gehören:

* Vernetzte Fabriken
* Point-of-Sale-Einzelhandelsstandorte
* Transportwesen, Erdölgewinnung, Bergbau und andere Umgebungen mit gelegentlicher Verbindung

## <a name="how-does-the-connected-registry-work"></a>Wie funktioniert die verbundene Registrierung?

Die folgende Abbildung zeigt ein typisches Bereitstellungsmodell für die verbundene Registrierung.

:::image type="content" source="media/intro-connected-registry/connected-registry-overview.svg" alt-text="Übersicht über die verbundene Registrierung":::

### <a name="deployment"></a>Bereitstellung

Jede verbundene Registrierung ist eine Ressource, die Sie mithilfe einer cloudbasierten Azure-Containerregistrierung verwalten. Das oberste übergeordnete Element in der Hierarchie der verbundenen Registrierung ist eine Azure-Containerregistrierung in einer Azure-Cloud oder einer privaten Bereitstellung von [Azure Stack Hub](/azure-stack/operator/azure-stack-overview).

Verwenden Sie Azure-Tools, um die verbundene Registrierung auf einem Server oder Gerät in Ihrer lokalen Umgebung oder einer Umgebung zu installieren, die lokale Containerworkloads wie [Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md) unterstützt.

Der *Aktivierungsstatus* der verbundenen Registrierung gibt an, ob sie lokal bereitgestellt wird.

* **Aktiv**: Die verbundene Registrierung wird derzeit lokal bereitgestellt. Sie kann erst dann erneut bereitgestellt werden, wenn sie deaktiviert wurde. 
* **Inaktiv**: Die verbundene Registrierung wird derzeit nicht lokal bereitgestellt. Sie kann jederzeit bereitgestellt werden.  
 
### <a name="content-synchronization"></a>Inhaltssynchronisierung

Die verbundene Registrierung greift regelmäßig auf die Cloudregistrierung zu, um Containerimages und OCI-Artefakte zu synchronisieren. 

Sie kann auch so konfiguriert werden, dass eine Teilmenge der Repositorys der Cloudregistrierung synchronisiert wird oder die Synchronisierung nur in bestimmten Intervallen erfolgt, um den Datenverkehr zwischen der Cloud und der lokalen Umgebung zu reduzieren.

### <a name="modes"></a>Modi

Eine verbundene Registrierung kann in einem von zwei Modi arbeiten: *ReadWrite* oder *ReadOnly*.

- **ReadWrite-Modus**: Der Standardmodus ermöglicht Clients das Pullen und Pushen von Artefakten (Lesen und Schreiben) in die verbundene Registrierung. Per Push an die verbundene Registrierung übertragene Artefakte werden mit der Cloudregistrierung synchronisiert. 
        
  Der ReadWrite-Modus ist nützlich, wenn eine lokale Entwicklungsumgebung vorhanden ist. Die Images werden per Push an die lokale verbundene Registrierung übertragen und von dort aus mit der Cloud synchronisiert.

- **ReadOnly-Modus**: Wenn sich die verbundene Registrierung im ReadOnly-Modus befindet, dürfen Clients Artefakte nur pullen (lesen). Diese Konfiguration wird für IoT Edge-Szenarien mit Schachtelung oder andere Szenarien verwendet, in denen Clients ein Containerimage pullen müssen, um zu funktionieren.

### <a name="registry-hierarchy"></a>Registrierungshierarchie

Jede verbundene Registrierung muss mit einem übergeordneten Element verknüpft sein. Das oberste übergeordnete Element ist die Cloudregistrierung. Für hierarchische Szenarien wie [IoT Edge mit Schachtelung](overview-connected-registry-and-iot-edge.md) können Sie verbundene Registrierungen in beiden Modi schachteln. Das übergeordnete Element, das mit der Cloudregistrierung verknüpft ist, kann in beiden Modi ausgeführt werden. 

Untergeordnete Registrierungen müssen mit den Funktionen ihrer übergeordneten Elemente kompatibel sein. Deshalb können sowohl verbundene Registrierungen im ReadWrite- als auch im ReadOnly-Modus untergeordnete Elemente einer verbundenen Registrierung im ReadWrite-Modus sein, aber einer verbundenen Registrierung im ReadOnly-Modus kann nur eine Registrierung im ReadOnly-Modus untergeordnet werden.  

## <a name="client-access"></a>Clientzugriff

Lokale Clients verwenden Standardtools wie die Docker CLI, um Inhalte per Push an eine verbundene Registrierung zu übertragen oder per Pull daraus abzurufen. Zum Verwalten des Clientzugriffs erstellen Sie Azure Container Registry-[Token][repository-scoped-permissions] für den Zugriff auf jede verbundene Registrierung. Sie können den Geltungsbereich der Clienttoken für den Pull- oder Pushzugriff auf ein oder mehrere Repositorys in der Registrierung festlegen.

Jede verbundene Registrierung muss außerdem regelmäßig mit ihrer übergeordneten Registrierung kommunizieren. Zu diesem Zweck wird von der Cloudregistrierung ein *Synchronisierungstoken* für die Registrierung ausgestellt. Dieses Token wird bei Synchronisierungs- und Verwaltungsvorgängen für die Authentifizierung bei der übergeordneten Registrierung verwendet.

Weitere Informationen finden Sie im Dokument zum [Verwalten des Zugriffs auf eine verbundene Registrierung][overview-connected-registry-access].

## <a name="limitations"></a>Einschränkungen

- Die Anzahl von Token und Bereichszuordnungen ist für eine einzelne Containerregistrierung auf jeweils 20.000 [beschränkt](container-registry-skus.md). Dies schränkt indirekt die Anzahl verbundener Registrierungen für eine Cloudregistrierung ein, da jede verbundene Registrierung ein Synchronisierungs- und Clienttoken benötigt.
- Die Anzahl von Repositoryberechtigungen in einer Bereichszuordnung ist auf 500 beschränkt.
- Die Anzahl von Clients für die verbundene Registrierung ist derzeit auf 20 beschränkt.
- Das [Sperren von Images](container-registry-image-lock.md) über Repository-/Manifest-/Tagmetadaten wird für verbundene Registrierungen derzeit nicht unterstützt.
- Das [Löschen von Repositorys](container-registry-delete.md) wird für die verbundene Registrierung im ReadOnly-Modus nicht unterstützt.
- [Ressourcenprotokolle](monitor-service-reference.md#resource-logs) für verbundene Registrierungen werden derzeit nicht unterstützt.
- Die verbundene Registrierung ist an den Datenendpunkt der Startregion der Registrierung gekoppelt. Die automatische Migration der [Georeplikation](container-registry-geo-replication.md) wird nicht unterstützt.
- Zum Löschen einer verbundenen Registrierung müssen die Container lokal manuell entfernt und die entsprechenden Bereichszuordnungen bzw. die Token in der Cloud entfernt werden.
- Die Einschränkungen für die Synchronisierung einer verbundenen Registrierung lauten wie folgt:
  - Für die fortlaufende Synchronisierung:
    - `minMessageTtl`: 1 Tag
    - `maxMessageTtl`: 90 Tage
  - Für Szenarien mit gelegentlicher Verbindung, in denen Sie ein Synchronisierungsfenster angeben möchten:
    - `minSyncWindow`: 1 Stunde
    - `maxSyncWindow`: 7 Tage

## <a name="next-steps"></a>Nächste Schritte

In dieser Übersicht haben Sie die verbundene Registrierung und einige grundlegende Konzepte kennengelernt. Fahren Sie mit einem der folgenden Artikel fort, um sich über spezifische Szenarien zu informieren, in denen die verbundene Registrierung verwendet werden kann.

> [!div class="nextstepaction"]
> [Übersicht: Zugriff auf eine verbundene Registrierung][overview-connected-registry-access]
> 
> [!div class="nextstepaction"]
> [Übersicht: Verbundene Registrierung und IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[overview-connected-registry-access]:overview-connected-registry-access.md
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md

---
title: 'Schnellstart: Erstellen einer verbundenen Registrierung über das Portal'
description: Verwenden Sie das Azure-Portal, um eine verbundene Azure-Containerregistrierungsressource zu erstellen, die Images und andere Artefakte mit der Cloudregistrierung synchronisieren kann.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6181f0fbdea8c03741f28dc9023a6761971cf2e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029705"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-portal"></a>Schnellstart: Erstellen einer verbundenen Registrierung über das Azure-Portal

In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um eine [verbundene Registrierungsressource](intro-connected-registry.md) in Azure zu erstellen. Mit dem Azure Container Registry-Feature für verbundene Registrierungen können Sie eine Registrierung remote oder lokal bereitstellen und Images und andere Artefakte mit der Cloudregistrierung synchronisieren. 

Hier erstellen Sie zwei verbundene Registrierungsressourcen für eine Cloudregistrierung: Eine verbundene Registrierung ermöglicht Lese- und Schreibfunktionen (Pull- und Pushvorgänge für Artefakte), die andere ermöglicht schreibgeschützte Funktionen. 

Nach dem Erstellen der verbundenen Registrierung können Sie in anderen Leitfäden erfahren, wie Sie diese in Ihrer lokalen oder Remoteinfrastruktur bereitstellen und verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Azure Container Registry: Wenn Sie noch nicht über eine Containerregistrierung verfügen, [erstellen Sie eine](container-registry-get-started-portal.md) (Premium-Tarif erforderlich) in einer [Region](intro-connected-registry.md#available-regions), die verbundene Registrierungen unterstützt. 

Verwenden Sie zum Importieren von Images in die Containerregistrierung die Azure CLI: [!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>Aktivieren des dedizierten Datenendpunkts für die Cloudregistrierung

Aktivieren Sie den [dedizierten Datenendpunkt](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) für die Azure-Containerregistrierung in der Cloud. Dieser Schritt ist erforderlich, damit eine verbundene Registrierung mit der Cloudregistrierung kommunizieren kann.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Containerregistrierung.
1. Wählen Sie **Netzwerkzugriff > Öffentlicher Zugriff** aus.
Aktivieren Sie das Kontrollkästchen **Dedizierten Datenendpunkt aktivieren**.
1. Wählen Sie **Speichern** aus.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>Erstellen einer verbundenen Registrierungsressource für Lese- und Schreibfunktionen

Mit den folgenden Schritten wird eine verbundene Registrierung im [ReadWrite-Modus](intro-connected-registry.md#modes) erstellt, die mit der Cloudregistrierung verknüpft ist.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Containerregistrierung.
1. Wählen Sie **Verbundene Registrierungen (Vorschau) > + Erstellen** aus.
1. Geben Sie die Werte in der folgenden Tabelle ein, oder wählen Sie sie aus, und wählen Sie dann **Speichern** aus.


|Element  |BESCHREIBUNG  |
|---------|---------|
|Parent     | Wählen Sie **Kein übergeordnetes Element** für eine verbundene Registrierung aus, die mit der Cloudregistrierung verknüpft ist.        |
|Mode     | Wählen Sie **ReadWrite** aus.         |
|Name     | Der Name der verbundenen Registrierung muss mit einem Buchstaben beginnen und darf nur alphanumerische Zeichen enthalten. Er muss 5 bis 40 Zeichen lang und in der Hierarchie für diese Azure-Containerregistrierung eindeutig sein.       |
|Protokolleigenschaften     | Übernehmen Sie die Standardeinstellungen.       |
|Synchronisierungseigenschaften    | Übernehmen Sie die Standardeinstellungen. Da standardmäßig kein Synchronisierungszeitplan definiert ist, werden die Repositorys ohne Unterbrechungen zwischen der Cloudregistrierung und der verbundenen Registrierung synchronisiert.      |
|Repositorys     | Wählen Sie die Namen der Repositorys aus, die Sie im vorherigen Schritt importiert haben, oder geben Sie sie ein. Die angegebenen Repositorys werden nach der Bereitstellung zwischen der Cloudregistrierung und der verbundenen Registrierung synchronisiert.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readwrite-connected-registry.png" alt-text="Erstellen einer verbundenen Registrierung im ReadWrite-Modus":::


> [!IMPORTANT]
> Zur Unterstützung von geschachtelten Szenarien, in denen niedrigere Ebenen keinen Internetzugriff haben, müssen Sie die Synchronisierung des Repositorys `acr/connected-registry` immer zulassen. Dieses Repository enthält das Image für die Runtime der verbundenen Registrierung.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>Erstellen einer verbundenen Registrierungsressource für schreibgeschützte Funktionen

Mit den folgenden Schritten wird eine verbundene Registrierung im [ReadOnly-Modus](intro-connected-registry.md#modes) erstellt, deren übergeordnete Registrierung die verbundene Registrierung ist, die Sie im vorherigen Abschnitt erstellt haben. Diese verbundene Registrierung ermöglicht nach der Bereitstellung schreibgeschützte Funktionen (Pullvorgänge für Artefakte).

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Containerregistrierung.
1. Wählen Sie **Verbundene Registrierungen (Vorschau) > + Erstellen** aus.
1. Geben Sie die Werte in der folgenden Tabelle ein, oder wählen Sie sie aus, und wählen Sie dann **Speichern** aus.


|Element  |BESCHREIBUNG  |
|---------|---------|
|Parent     | Wählen Sie die verbundene Registrierung aus, die Sie zuvor erstellt haben.        |
|Mode     | Wählen Sie **ReadOnly** aus.         |
|Name     | Der Name der verbundenen Registrierung muss mit einem Buchstaben beginnen und darf nur alphanumerische Zeichen enthalten. Er muss 5 bis 40 Zeichen lang und in der Hierarchie für diese Azure-Containerregistrierung eindeutig sein.      |
|Protokolleigenschaften     | Übernehmen Sie die Standardeinstellungen.       |
|Synchronisierungseigenschaften    | Übernehmen Sie die Standardeinstellungen. Da standardmäßig kein Synchronisierungszeitplan definiert ist, werden die Repositorys ohne Unterbrechungen zwischen der Cloudregistrierung und der verbundenen Registrierung synchronisiert.      |
|Repositorys     | Wählen Sie die Namen der Repositorys aus, die Sie im vorherigen Schritt importiert haben, oder geben Sie sie ein. Die angegebenen Repositorys werden nach der Bereitstellung zwischen der übergeordneten Registrierung und der verbundenen Registrierung synchronisiert.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readonly-connected-registry.png" alt-text="Erstellen einer verbundenen Registrierung im ReadOnly-Modus":::

## <a name="view-connected-registry-properties"></a>Anzeigen der Eigenschaften einer verbundenen Registrierung

Wählen Sie eine verbundene Registrierung im Portal aus, um deren Eigenschaften anzuzeigen, z. B. den Verbindungsstatus (offline, online oder fehlerhaft) und den Aktivierungsstatus (lokal bereitgestellt). Im folgenden Beispiel ist die verbundene Registrierung nicht bereitgestellt. Der Verbindungsstatus „offline“ gibt an, dass die Verbindung mit der Cloud derzeit getrennt ist.

:::image type="content" source="media/quickstart-connected-registry-portal/connected-registry-properties.png" alt-text="Anzeigen der Eigenschaften einer verbundenen Registrierung":::

In dieser Ansicht können Sie auch eine Verbindungszeichenfolge und optional Kennwörter für das [Synchronisierungstoken](overview-connected-registry-access.md#sync-token) generieren. Eine Verbindungszeichenfolge enthält Konfigurationseinstellungen, die zum Bereitstellen einer verbundenen Registrierung und zum Synchronisieren von Inhalten mit einer übergeordneten Registrierung verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie das Azure-Portal verwendet, um zwei verbundene Registrierungsressourcen in Azure zu erstellen. Diese neuen verbundenen Registrierungsressourcen sind an Ihre Cloudregistrierung gebunden und ermöglichen die Synchronisierung von Artefakten mit der Cloudregistrierung.

Fahren Sie mit den Leitfäden zur Bereitstellung verbundener Registrierungen fort, um zu erfahren, wie Sie eine verbundene Registrierung in Ihrer IoT Edge-Infrastruktur bereitstellen und verwenden.

> [!div class="nextstepaction"]
> [Schnellstart: Bereitstellen einer verbundenen Registrierung in IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md

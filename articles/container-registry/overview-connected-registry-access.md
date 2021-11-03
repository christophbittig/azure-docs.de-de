---
title: Grundlegendes zum Zugriff auf eine verbundene Registrierung
description: Einführung in die tokenbasierte Authentifizierung und Autorisierung für verbundene Registrierungen in Azure Container Registry
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a98772cced7b5f7e72c66d134e9ff652f64b4086
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100323"
---
# <a name="understand-access-to-a-connected-registry"></a>Grundlegendes zum Zugriff auf eine verbundene Registrierung

Für den Zugriff auf und die Verwaltung einer [verbundenen Registrierung](intro-connected-registry.md) wird derzeit nur die ACR-[tokenbasierte Authentifizierung](container-registry-repository-scoped-permissions.md) unterstützt. Wie in der folgenden Abbildung dargestellt, werden zwei verschiedene Tokentypen von jeder verbundenen Registrierung verwendet:

* [**Clienttoken**](#client-tokens): Ein oder mehrere Token, die lokale Clients verwenden, um sich bei einer verbundenen Registrierung zu authentifizieren und Images und Artefakte per Push oder Pull zu übertragen.
* [**Synchronisierungstoken**](#sync-token): Ein Token, das von jeder verbundenen Registrierung für den Zugriff auf das übergeordnete Element und die Synchronisierung von Inhalten verwendet wird.

![Übersicht über die Authentifizierung verbundener Registrierungen](media/overview-connected-registry-access/connected-registry-authentication-overview.svg)

> [!IMPORTANT]
> Speichern Sie Tokenkennwörter für jede verbundene Registrierung an einem sicheren Ort. Nach der Erstellung können Tokenkennwörter nicht mehr abgerufen werden. Tokenkennwörter können jederzeit neu generiert werden.

## <a name="client-tokens"></a>Clienttoken

Um den Clientzugriff auf eine verbundene Registrierung zu verwalten, erstellen Sie Token für Aktionen in einem oder mehreren Repositorys. Konfigurieren Sie nach dem Erstellen eines Tokens die verbundene Registrierung mit dem Befehl [az acr connected-registry update](/cli/azure/acr/connected-registry#az_acr_connected_registry_update), um das Token zu akzeptieren. Ein Client kann dann die Tokenanmeldeinformationen verwenden, um auf einen verbundenen Registrierungsendpunkt zuzugreifen, z. B. um Docker-CLI-Befehle zum Übertragen von Images per Pull oder Push an die bzw. aus der verbundenen Registrierung zu verwenden.

Ihre Optionen zum Konfigurieren von Clienttokenaktionen hängen davon ab, ob die verbundene Registrierung sowohl Push- als auch Pullvorgänge zulässt oder als reiner Pullspiegel fungiert. 
* Eine verbundene Registrierung im standardmäßigen [ReadWrite-Modus](intro-connected-registry.md#modes) ermöglicht sowohl Pull- als auch Pushvorgänge. Daher können Sie ein Token erstellen, das Aktionen zum *Lesen* und *Schreiben* von Repositoryinhalten in dieser Registrierung ermöglicht. 
* Bei einer verbundenen Registrierung im [ReadOnly-Modus](intro-connected-registry.md#modes) können Clienttoken nur Aktionen zum *Lesen* von Repositoryinhalten zulassen.

### <a name="manage-client-tokens"></a>Verwalten von Clienttoken

Aktualisieren Sie Clienttoken, Kennwörter oder Bereichszuordnungen nach Bedarf mithilfe der Befehle [az acr token](/cli/az/acr#az_acr_token) und [az acr scope-map](/cli/az/acr#az_acr_scope-map). Clienttokenupdates werden automatisch an die verbundenen Registrierungen weitergegeben, die das Token akzeptieren.

## <a name="sync-token"></a>Synchronisierungstoken

Jede verbundene Registrierung verwendet ein Synchronisierungstoken, um sich bei ihrem unmittelbar übergeordneten Element zu authentifizieren. Hierbei kann es sich um eine andere verbundene Registrierung oder um die Cloudregistrierung handeln. Die verbundene Registrierung verwendet dieses Token automatisch bei der Synchronisierung von Inhalten mit dem übergeordneten Element oder bei der Durchführung anderer Updates. 

* Das Synchronisierungstoken und die Kennwörter werden automatisch generiert, wenn Sie die verbundene Registrierungsressource erstellen. Führen Sie den Befehl [az acr connected-registry install renew-credentials][az-acr-connected-registry-install-renew-credentials] aus, um die Kennwörter neu zu generieren.
* Schließen Sie Synchronisierungstoken-Anmeldeinformationen in die Konfiguration ein, die zum lokalen Bereitstellen der verbundenen Registrierung verwendet wird. 
* Standardmäßig wird dem Synchronisierungstoken die Berechtigung erteilt, ausgewählte Repositorys mit dem übergeordneten Repository zu synchronisieren. Sie müssen beim Erstellen der verbundenen Registrierungsressource ein vorhandenes Synchronisierungstoken oder ein oder mehrere Repositorys angeben, die synchronisiert werden sollen.
* Außerdem verfügt das Token über Berechtigungen zum Lesen und Schreiben von Synchronisierungsnachrichten auf einem Gateway, das für die Kommunikation mit dem übergeordneten Element der verbundenen Registrierung verwendet wird. Diese Nachrichten steuern den Synchronisierungszeitplan und verwalten andere Updates zwischen der verbundenen Registrierung und dem übergeordneten Element.

### <a name="manage-sync-token"></a>Verwalten des Synchronisierungstokens

Aktualisieren Sie Synchronisierungstoken, Kennwörter oder Bereichszuordnungen nach Bedarf mithilfe der Befehle [az acr token](/cli/az/acr#az_acr_token) und [az acr scope-map](/cli/az/acr#az_acr_scope-map). Aktualisierungen von Synchronisierungstoken werden automatisch an die verbundene Registrierung weitergegeben. Befolgen Sie beim Aktualisieren des Synchronisierungstokens die Standardmethoden zum Rotieren von Kennwörtern.

> [!NOTE]
> Das Synchronisierungstoken kann erst gelöscht werden, nachdem die dem Token zugeordnete verbundene Registrierung gelöscht wurde. Sie können eine verbundene Registrierung deaktivieren, indem Sie den Status des Synchronisierungstokens auf `disabled` festlegen. 

## <a name="registry-endpoints"></a>Registrierungsendpunkte

Tokenanmeldeinformationen für verbundene Registrierungen gelten für den Zugriff auf bestimmte Registrierungsendpunkte:

* Ein Clienttoken greift auf den Endpunkt der verbundenen Registrierung zu. Der Endpunkt der verbundenen Registrierung ist der Anmeldeserver-URI, bei dem es sich in der Regel um die IP-Adresse des Servers oder Geräts handelt, auf dem er gehostet wird.

* Ein Synchronisierungstoken greift auf den Endpunkt der übergeordneten Registrierung zu, bei dem es sich entweder um einen anderen verbundenen Registrierungsendpunkt oder um die Cloudregistrierung selbst handelt. Wenn die Cloudregistrierung als Zugriffsbereich festgelegt wurde, muss das Synchronisierungstoken zwei Registrierungsendpunkte erreichen:

    - Den vollqualifizierten Anmeldeservernamen, z. B. `contoso.azurecr.io`. Dieser Endpunkt wird für die Authentifizierung verwendet.
    - Einen vollqualifizierten regionalen [Datenendpunkt](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) für die Cloudregistrierung, z. B. `contoso.westus2.data.azurecr.io`. Dieser Endpunkt wird zum Austauschen von Nachrichten mit der verbundenen Registrierung zu Synchronisierungszwecken verwendet. 

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem folgenden Artikel fort, um sich über bestimmte Szenarien zu informieren, in denen die verbundene Registrierung verwendet werden kann.

> [!div class="nextstepaction"]
> [Übersicht: Verbundene Registrierung und IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[az-acr-connected-registry-update]: /cli/azure/acr/connected-registry#az_acr_connected_registry_update
[az-acr-connected-registry-install-renew-credentials]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_install_renew_credentials
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md

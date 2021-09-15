---
title: Überwachen der Gerätekonnektivität mit dem Azure IoT Central-Explorer
description: Überwachen Sie Gerätemeldungen, und beobachten Sie Änderungen bei Gerätezwillingen über die Befehlszeilenschnittstelle des IoT Central-Explorers.
author: viv-liu
ms.author: viviali
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: fe42d9bcfef72dfb8f2deebd3fb147ed2025f150
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479404"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Überwachen der Gerätekonnektivität per Azure CLI

Verwenden Sie die IoT-Erweiterung der Azure CLI, um Meldungen anzuzeigen, die Ihre Geräte an IoT Central senden und Änderungen im Gerätezwilling zu beobachten. Mit diesem Tool können Sie die Gerätekonnektivität debuggen und beobachten und Probleme von Gerätemeldungen diagnostizieren, die die Cloud oder Geräte nicht erreichen, die auf Änderungen beim Zwilling nicht reagieren.

[Weitere Einzelheiten erhalten Sie in der Referenz zu Azure CLI-Erweiterungen.](/cli/azure/iot/central)

## <a name="prerequisites"></a>Voraussetzungen

Ein Geschäfts-, Schul- oder Unikonto in Azure, hinzugefügt als Benutzer in einer IoT Central-Anwendung.

[!INCLUDE [azure-cli-prepare-your-environment-h3](../../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="install-the-iot-central-extension"></a>Installieren der IoT Central-Erweiterung

Führen Sie den folgenden Befehl an der Befehlszeile zum Installieren aus:

```azurecli
az extension add --name azure-iot
```

Überprüfen Sie die Version der Erweiterung, indem Sie Folgendes ausführen:

```azurecli
az --version
```

Die Version der Erweiterung „azure-iot“, 0.9.9 oder höher, sollte angezeigt werden. Führen Sie andernfalls Folgendes aus:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Verwenden der Erweiterung

Die folgenden Abschnitte beschreiben allgemeine Befehle und Optionen, die Sie beim Ausführen von `az iot central` verwenden können. Wenn Sie die vollständige Sammlung von Befehlen und Optionen anzeigen möchten, übergeben Sie `--help` an `az iot central` oder einen seiner Unterbefehle.

### <a name="login"></a>Anmeldename

Melden Sie zuerst bei der Azure CLI an. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Abrufen der Anwendungs-ID Ihrer IoT Central-App
Kopieren Sie in **Verwaltung/Anwendungseinstellungen** die **Anwendungs-ID**. Sie verwenden diesen Wert in späteren Schritten.

### <a name="monitor-messages"></a>Überwachen von Meldungen
Überwachen Sie die Nachrichten, die von ihren Geräten an die IoT Central-App gesendet werden. Zu der Ausgabe zählen auch alle Header und Anmerkungen.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Anzeigen von Geräteeigenschaften
Zeigen Sie die aktuellen Lesen- und Lesen/Schreiben-Geräteeigenschaften für ein bestimmtes Gerät an.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes sollten Sie sich über die [Gerätekonnektivität in Azure IoT Central](./concepts-get-connected.md) informieren.
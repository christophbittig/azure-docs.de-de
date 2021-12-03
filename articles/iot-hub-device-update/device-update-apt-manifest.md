---
title: Grundlegendes zum APT-Manifest für Device Update for Azure IoT Hub | Microsoft-Dokumentation
description: Erfahren Sie, wie Device Update for IoT Hub ein APT-Manifest für ein paketbasiertes Update verwendet.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 24c0d04b6a29b813dc23604594ca4b1b1235dc37
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373211"
---
# <a name="device-update-apt-manifest"></a>APT-Manifest für Device Update

Das APT Manifest ist eine JSON-Datei, in der die vom APT-Updatehandler benötigten Updatedetails beschrieben werden. Diese Datei kann in Device Update for IoT Hub wie jedes andere Update importiert werden.

[Erfahren Sie mehr](import-update.md) über das Importieren von Updates in Device Update.

## <a name="overview"></a>Übersicht

Wenn ein APT-Manifest als Update an einen Device Update-Agent geliefert wird, verarbeitet der Agent das Manifest und führt die erforderlichen Vorgänge aus. Diese Vorgänge umfassen das Herunterladen und Installieren der in der APT-Manifestdatei angegebenen Pakete und ihrer Abhängigkeiten von einem bestimmten Repository.

Device Update unterstützt den APT-Updatetyp und den APT-Updatehandler. Diese Unterstützung ermöglicht es dem Device Update-Agent, die installierten Debian-Pakete auszuwerten und die erforderlichen Pakete zu aktualisieren. 

## <a name="schema"></a>Schema

Eine APT-Manifestdatei ist eine JSON-Datei mit einem Schema mit Versionsverwaltung.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Beispiel:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Der Name für dieses APT-Manifest. Dies kann ein beliebiger Name oder eine ID sein, die für Ihre Szenarien sinnvoll ist. Beispiel: `contoso-iot-edge`.

### <a name="version"></a>version

Eine Versionsnummer für dieses APT-Manifest. Beispiel: `1.0.0.0`.


### <a name="packages"></a>packages

Eine Liste von Objekten, die paketspezifische Eigenschaften enthalten.

#### <a name="name"></a>name

Der Name oder die ID des Pakets. Beispiel: `iotedge`.

#### <a name="version"></a>version

Die gewünschten Versionskriterien für das Paket. Beispiel: `1.0.8-2`.

Derzeit wird nur die exakte Versionsnummer unterstützt. Die Versionsnummer ist die gewünschte Debian-Paketversion im Format [epoch:]upstream_version[-debian_revision].

**epoch** ist eine ganze Zahl ohne Vorzeichen.

**upstream_version** kann alphanumerische Zeichen und Zeichen wie „.“, „+“, „-“ und „~“ enthalten. Es sollte mit einer Ziffer beginnen.
> [!NOTE]
> „1.0.8“ ist gleich „1.0.8-0“.

**`"name":"iotedge" and "version":"1.0.8-2"`** ist z. B. äquivalent zur Installation eines Pakets mithilfe des Befehls `apt-get install iotedge=1.0.8-2`.

> [!NOTE]
> Der Versionswert enthält kein Gleichheitszeichen.

Wenn die Version ausgelassen wird, erfolgt die Installation der neuesten verfügbaren Version des angegebenen Pakets.

[Erfahren Sie mehr](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) über die Versionsverwaltung von Debian-Paketen.

> [!NOTE]
> Der APT-Paket-Manager ignoriert die von einem Paket angegebenen Anforderungen zur Versionsverwaltung, wenn die zu installierenden abhängigen Pakete automatisch aufgelöst werden. Wenn keine expliziten Versionen von abhängigen Paketen angegeben werden, wird die neueste Version verwendet, auch wenn das Paket selbst eine strikte Anforderung (=) an eine bestimmte Version stellt. Diese automatische Auflösung kann zu Fehlern bezüglich einer nicht erfüllten Abhängigkeit führen. [Weitere Informationen](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Wenn Sie eine bestimmte Version des Azure IoT Edge-Sicherheitsdaemons aktualisieren, sollten Sie die gewünschte Version des `iotedge`-Pakets und des abhängigen `libiothsm-std`-Pakets in Ihr APT-Manifest aufnehmen.
[Weitere Informationen](../iot-edge/how-to-update-iot-edge.md#update-the-security-daemon)

> [!NOTE]
> Ein APT-Manifest kann verwendet werden, um den Device Update-Agent und seine Abhängigkeiten zu aktualisieren. Führen Sie den Namen des Device Update-Agents und die gewünschte Version im APT-Manifest auf, wie bei jedem anderen Paket auch. Dieses APT-Manifest kann dann über die Device Update for IoT Hub-Pipeline importiert und bereitgestellt werden. 

## <a name="removing-packages"></a>Entfernen von Paketen

Sie können ein APT-Manifest auch dazu verwenden, um installierte Pakete von Ihrem Gerät zu entfernen. Mit einem einzelnen APT-Manifest können Sie mehrere Pakete entfernen, hinzufügen und aktualisieren. Fügen Sie ein Minuszeichen „-“ nach dem Paketnamen hinzu, um ein Paket zu entfernen. Sie sollten keine Versionsnummer für die Pakete einbeziehen, die Sie entfernen. Wenn Sie Pakete über ein APT-Manifest entfernen, werden die Abhängigkeiten und Konfigurationen nicht entfernt.

Beispiel:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Dieses APT-Manifest entfernt das Paket „foo“ von den Geräten, auf denen es bereitgestellt wird. 

## <a name="recommended-value-for-installed-criteria"></a>Empfohlener Wert für installierte Kriterien

Die installierten Kriterien für ein APT-Manifest sind `<name>-<version>`, wobei `<name>` der Name und `<version>` die Version des APT-Manifests ist. Beispiel: `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Richtlinien zum Erstellen eines APT-Manifests

Beim Erstellen des APT-Manifests sind einige Richtlinien zu beachten:

- Stellen Sie immer sicher, dass das APT-Manifest eine wohlgeformte JSON-Datei ist.
- Jedes APT-Manifest sollte über eine eindeutige Version verfügen. Versuchen Sie, eine standardisierte Methodik zur Erhöhung der Version des APT-Manifests zu entwickeln, sodass sie für Ihre Szenarien sinnvoll ist und leicht befolgt werden kann.
- Wenn es um den gewünschten Zustand der einzelnen Pakete geht, geben Sie den genauen Namen und die Version des Pakets an, das Sie auf Ihrem Gerät installieren möchten. Überprüfen Sie die Werte immer anhand des Paketrepositorys, das Sie als Quelle für das Paket verwenden möchten.
- Stellen Sie sicher, dass die Pakete im APT-Manifest in der Reihenfolge aufgeführt sind, in der sie installiert/entfernt werden sollen.
- Überprüfen Sie die Installation von Paketen immer auf einem Testgerät, um sicherzustellen, dass Sie das gewünschte Ergebnis erhalten.
- Wenn Sie eine bestimmte Version eines Pakets installieren (z. B. `iotedge 1.0.9-1`), ist es eine bewährte Methode, im APT-Manifest auch die expliziten Versionen der abhängigen Pakete anzugeben, die installiert werden sollen (z. B. `libiothsm 1.0.9-1`).
- Obwohl es nicht vorgeschrieben ist, sollten Sie immer sicherstellen, dass Ihr APT-Manifest kumulativ ist, um zu vermeiden, dass Ihr Gerät in einen unbekannten Zustand wechselt. Ein kumulatives Update stellt sicher, dass Ihre Geräte über die gewünschte Version der einzelnen Pakete verfügen, die Ihnen wichtig sind, selbst wenn das Gerät eine APT-Updatebereitstellung übersprungen hat, da bei der Installation ein Fehler aufgetreten ist oder es offline genommen wurde.

Beispiel:

**APT-Basismanifest**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**UNGÜLTIGES UPDATE**

Dieses Update enthält das Paket „bar“, aber nicht das Paket „foo“.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**GÜLTIGES UPDATE**

Dieses Update enthält das Paket „foo“ und auch das Paket „bar“.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Importieren eines neuen Updates](import-update.md)
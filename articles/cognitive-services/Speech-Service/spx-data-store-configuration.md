---
title: 'Konfigurationsoptionen für die Speech-Befehlszeilenschnittstelle: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Konfigurationsdateien für die Verwendung mit der Azure Speech-Befehlszeilenschnittstelle erstellen und verwalten.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: eur
ms.openlocfilehash: 7c323c6900930cda901ffb0ec09ce96150eafce2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505553"
---
# <a name="speech-cli-configuration-options"></a>Konfigurationsoptionen für die Speech-Befehlszeilenschnittstelle

Das Verhalten der Speech-Befehlszeilenschnittstelle kann auf Einstellungen in Konfigurationsdateien basieren, auf die mit dem Symbol `@` verwiesen werden kann. Von der Speech-Befehlszeilenschnittstelle wird eine neue Einstellung im neuen Unterverzeichnis `./spx/data` gespeichert, das im aktuellen Arbeitsverzeichnis für die Speech-Befehlszeilenschnittstelle erstellt wird. Bei der Suche nach einem Konfigurationswert werden von der Speech-Befehlszeilenschnittstelle zuerst Ihr aktuelles Arbeitsverzeichnis und dann der Datenspeicher unter `./spx/data` sowie weitere Datenspeicher bis hin zu einem letzten schreibgeschützten Datenspeicher in der Binärdatei `spx` durchsucht. 

In der Schnellstartanleitung zur Speech-Befehlszeilenschnittstelle haben Sie den Datenspeicher zum Speichern Ihrer Werte `@key` und `@region` verwendet und mussten sie daher nicht bei jedem Befehl vom Typ `spx` angeben. Wie bereits erwähnt, können Sie Konfigurationsdateien verwenden, um Ihre eigenen Konfigurationseinstellungen zu speichern, und Sie können mit ihnen sogar URLs und andere dynamische Inhalte übergeben, die zur Laufzeit generiert werden.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Erstellen und Verwalten von Konfigurationsdateien im Datenspeicher

In diesem Abschnitt erfahren Sie, wie Sie eine Konfigurationsdatei im lokalen Datenspeicher verwenden, um Befehlseinstellungen unter Verwendung von `spx config` zu speichern und abzurufen sowie um die Ausgabe der Speech-Befehlszeilenschnittstelle mithilfe der Option `--output` zu speichern.

Im folgenden Beispiel wird die Konfigurationsdatei `@my.defaults` geleert, es werden Schlüssel-Wert-Paare für **key** und **region** in der Datei hinzugefügt, und die Konfiguration wird in einem Aufruf von `spx recognize` verwendet.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Sie können auch dynamischen Inhalt in eine Konfigurationsdatei schreiben. Mit dem folgenden Befehl wird ein benutzerdefiniertes Sprachmodell erstellt und die URL des neuen Modells in einer Konfigurationsdatei gespeichert. Der nächste Befehl wartet, bis das Modell unter dieser URL für die Verwendung bereit ist, bevor die Rückgabe erfolgt.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

Im folgenden Beispiel werden zwei URLs in die Konfigurationsdatei `@my.datasets.txt` geschrieben. In diesem Szenario kann `--output` das optionale Schlüsselwort **add** zum Erstellen einer Konfigurationsdatei bzw. zum Anfügen an die vorhandene Datei enthalten.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Geben Sie den folgenden Befehl ein, um weitere Informationen zu Datenspeicherdateien anzuzeigen, z. B. zur Verwendung von Standardkonfigurationsdateien (`@spx.default`, `@default.config` und `@*.default.config` für befehlsspezifische Standardeinstellungen):

```console
spx help advanced setup
```

## <a name="next-steps"></a>Nächste Schritte 

* [Batchvorgänge für die Speech-Befehlszeilenschnittstelle](./spx-batch-operations.md)

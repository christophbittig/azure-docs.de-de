---
title: Produktionsbereitschaft und Best Practices
description: Dieser Artikel enthält eine Anleitung zum Konfigurieren und Bereitstellen des Azure Video Analyzer-Edgemoduls in Produktionsumgebungen.
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d25965ed8143b8d74f33794fbc97282502b84b96
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560186"
---
# <a name="production-readiness-and-best-practices"></a>Produktionsbereitschaft und Best Practices

[!INCLUDE [header](includes/edge-env.md)]

Dieser Artikel enthält eine Anleitung zum Konfigurieren und Bereitstellen des Azure Video Analyzer Edge-Moduls und Clouddienste in Produktionsumgebungen. Lesen Sie zur Vorbereitung Ihrer IoT Edge-Lösung außerdem den Artikel [Vorbereiten der Bereitstellung einer IoT Edge-Lösung für die Produktion](../../../iot-edge/production-checklist.md).

Informieren Sie sich bei den IT-Abteilungen Ihrer Organisationen über mögliche Sicherheitsaspekte.

## <a name="creating-the-video-analyzer-account"></a>Erstellen des Azure Video Analyzer-Kontos

Für das [Erstellen](../create-video-analyzer-account.md) eines Azure Video Analyzer-Kontos wird Folgendes empfohlen:

1. Der Abonnementbesitzer sollte eine Ressourcengruppe erstellen, in der alle von Azure Video Analyzer benötigten Ressourcen erstellt werden sollen.
1. Anschließend sollte der Besitzer Ihnen die Rollen [Mitwirkender](../../../role-based-access-control/built-in-roles.md#contributor) und [Benutzerzugriffsadministrator](../../../role-based-access-control/built-in-roles.md#user-access-administrator) für diese Ressourcengruppe erteilen.
1. Sie können dann die relevanten Ressourcen erstellen: Speicherkonto, IoT Hub, benutzerseitig zugewiesene verwaltete Identität und Azure Video Analyzer-Konto unter dieser Ressourcengruppe.

## <a name="running-the-module-as-a-local-user"></a>Ausführen des Moduls als lokaler Benutzer

Wenn Sie das Azure Video Analyzer Edge-Modul in IoT Edge auf einem Edge-Gerät bereitstellen, wird es standardmäßig mit erhöhten Rechten ausgeführt. Sie können dies mithilfe der Protokolle aus dem Modul (`sudo iotedge logs {name-of-module}`) überprüfen, die Folgendes anzeigen sollten:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
```

In den folgenden Abschnitten wird erläutert, wie Sie die obige Warnung behandeln können.

### <a name="creating-and-using-a-local-user-account"></a>Erstellen und Verwenden eines lokalen Benutzerkontos

Sie können und sollten das Azure Video Analyzer Edge-Modul in einer Produktionsumgebung unter einem Konto mit möglichst geringen Berechtigungen ausführen. Die folgenden Befehle zeigen z. B., wie Sie auf einer Linux-VM ein lokales Benutzerkonto erstellen:

```
sudo groupadd -g 1010 localedgegroup
sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
```

Als Nächstes können Sie im Bereitstellungsmanifest die Umgebungsvariablen LOCAL_USER_ID und LOCAL_GROUP_ID auf Werte von anderen Benutzern und Gruppen als root festlegen:

```
"avaedge": {
  "version": "1.0",
  …
  "env": {
    "LOCAL_USER_ID": {
       "value": "1010"
    },
    "LOCAL_GROUP_ID": {
     "value": "1010"
    }
  }
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Zuweisen von Berechtigungen für den Gerätespeicher

Das Azure Video Analyzer Edge-Modul erfordert die Fähigkeit, Dateien in das lokale Dateisystem zu schreiben, wenn Folgendes zutrifft:

- Sie verwenden die Modulzwillingseigenschaft [`applicationDataDirectory`](module-twin-configuration-schema.md): Geben Sie ein Verzeichnis im lokalen Dateisystem zum Speichern von Konfigurationsdaten an.
- Sie verwenden eine Pipeline, um Videos in der Cloud aufzuzeichnen. Das Modul benötigt ein Verzeichnis auf dem Edge-Gerät als Cache. (Weitere Informationen finden Sie im Artikel zur [fortlaufenden Videoaufzeichnung](../continuous-video-recording.md)).
- [Die Aufzeichnung erfolgt in eine lokale Datei](../event-based-video-recording-concept.md), in der Sie einen Dateipfad für das aufgezeichnete Video angeben.

Wenn Sie beabsichtigen, eine der oben genannten Optionen anzuwenden, sollten Sie sicherstellen, dass das obige Benutzerkonto Zugriff auf das entsprechende Verzeichnis hat. Betrachten `applicationDataDirectory` Sie z. B. Sie können ein Verzeichnis auf dem Edgegerät erstellen und den Gerätespeicher mit dem Modulspeicher verknüpfen.

```
sudo mkdir /var/lib/videoanalyzer
sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer
```

Als nächstes können Sie in den Erstellungsoptionen für das Edge-Modul im Bereitstellungsmanifest eine `binds`-Einstellung hinzufügen, die das obige Verzeichnis ("/var/lib/videoanalyzer") auf ein Verzeichnis im Modul abbildet (z. B. "/var/lib/videoanalyzer"). Sie würden das letztgenannte Verzeichnis als Wert für `applicationDataDirectory` verwenden.

```
        "modules": {
          "avaedge": {
            "version": "1.1",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/video-analyzer:1",
              "createOptions": "{ \"HostConfig\": { \"LogConfig\": { \"Type\": \"\", \"Config\": { \"max-size\": \"10m\", \"max-file\": \"10\" } }, \"Binds\": [ \"/var/media/:/var/media/\", \"/var/lib/videoanalyzer/:/var/lib/videoanalyzer\" ], \"IpcMode\": \"host\", \"ShmSize\": 1536870912 } }"
            },
            "env": {
              "LOCAL_USER_ID": {
                "value": "1010"
              },
              "LOCAL_GROUP_ID": {
                "value": "1010"
              }
            }
          },
          …
        },
        
    …
    
    "avaedge": {
       "properties.desired": {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "{your-token}",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": false,
          "telemetryOptOut": false
    }
}
```

Wenn Sie sich die Beispielpipelines für den Schnellstart und die Tutorials ansehen z. B. zur [fortlaufenden Videoaufzeichnung](use-continuous-video-recording.md), werden Sie feststellen, dass das Medien-Cache-Verzeichnis (`localMediaCachePath`) ein Unterverzeichnis unter `applicationDataDirectory` verwendet. Dies ist die empfohlene Vorgehensweise, da der Cache vorübergehende Daten enthält.

Beachten Sie auch, dass für Produktionsumgebungen, in denen Sie TLS-Verschlüsselung zur Sicherung des Datenverkehrs verwenden, die Einstellung `allowedUnsecuredEndpoints` auf `false` empfohlen wird.

### <a name="tips-about-maintaining-your-edge-device"></a>Tipps zur Wartung Ihres Edge-Geräts

Die folgenden Tipps sind keine vollständige Liste, aber sie sollten bei allgemein bekannten Problemen helfen, auf die wir gestoßen sind.

Es kann vorkommen, das der virtuelle Linux-Computer, den Sie als Azure IoT Edge-Gerät verwenden, mit mehr reagiert, wenn er nicht in regelmäßigen Abständen verwaltet wird. Es ist sehr wichtig, die Caches zu bereinigen und unnötige Pakete sowie nicht verwendete Container von der VM zu entfernen. Zu diesem Zweck finden Sie im Folgenden eine Reihe empfohlener Befehle, die Sie auf Ihrer Edge-VM verwenden können.

- `sudo apt-get clean`

Der Befehl „apt-get clean“ löscht das lokale Repository der abgerufenen Paketdateien, die in „/var/cache“ verblieben sind. Dabei werden die Verzeichnisse „/var/cache/apt/archives/“ und „/var/cache/apt/archives/partial/“ bereinigt. Die einzigen Dateien, die in „/var/cache/apt/archives“ verbleiben, sind die Sperrdatei und das Unterverzeichnis „partial“. Der Befehl „apt-get clean“ wird normalerweise verwendet, um bei Bedarf Speicherplatz freizugeben. Dies erfolgt im Allgemeinen im Rahmen der regelmäßigen geplanten Wartung. Weitere Informationen finden Sie unter [Bereinigen mit apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).

- `sudo apt-get autoclean`

Die Option „apt-get autoclean“ löscht wie „apt-get clean“ abgerufene Paketdateien aus dem lokalen Repository, beschränkt dies jedoch ausschließlich auf Dateien, die nicht mehr heruntergeladen werden können und nicht mehr benötigt werden. Damit können Sie verhindern, dass der Cache zu groß wird.

- `sudo apt-get autoremove`

Mit der Option „autoremove“ werden Pakete entfernt, die automatisch installiert wurden, da sie von einem anderen Paket benötigt wurden. Wenn aber diese anderen Pakete entfernt wurden, werden sie nicht mehr benötigt.

- `sudo docker image ls` zeigt eine Liste der Docker-Images auf Ihrem Edgesystem an.

- `sudo docker system prune`

Docker nutzt zum Bereinigen nicht verwendeter Objekte einen konservativen Ansatz (häufig als „Garbage Collection“ bezeichnet), der z. B. Images, Container, Volumes und Netzwerke umfasst: Diese Objekte werden in der Regel nur dann entfernt, wenn Sie Docker explizit dazu auffordern. Dies kann dazu führen, dass Docker zusätzlichen Speicherplatz verwendet. Für jeden Objekttyp bietet Docker einen Befehl zum Löschen („prune“). Darüber hinaus können Sie `docker system prune` verwenden, um mehrere Objekttypen gleichzeitig zu bereinigen. Weitere Informationen finden Sie unter [Löschen nicht verwendeter Docker-Objekte](https://docs.docker.com/config/pruning/).

- `sudo docker rmi REPOSITORY:TAG`

Da die Updates im Edgemodul durchgeführt werden, kann Ihre Docker-Instanz auch ältere Versionen des Edgemoduls enthalten. In einem solchen Fall wird empfohlen, den Befehl `docker rmi` zu verwenden, um bestimmte Images zu entfernen, die durch das Image-Versions-Tag gekennzeichnet sind.

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erste Schritte – Azure Video Analyzer](get-started-detect-motion-emit-events.md)

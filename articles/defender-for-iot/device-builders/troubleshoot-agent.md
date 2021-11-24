---
title: Behandeln von Problemen beim Starten des Sicherheits-Agents (Linux)
description: Beheben Sie Probleme bei der Verwendung von Microsoft Defender für IoT-Sicherheits-Agents für Linux.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: c0e85e0628599af88d86c567b559ca6c721ab732
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305995"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Leitfaden zur Problembehandlung für den Sicherheits-Agent (Linux)

In diesem Artikel wird erläutert, wie Sie potenzielle Probleme beim Startvorgang des Sicherheits-Agents lösen.

Der Microsoft Defender für IoT-Agents startet direkt nach der Installation selbst. Der Startvorgang des Agents beinhaltet das Lesen der lokalen Konfiguration, das Herstellen einer Verbindung mit Azure IoT Hub und das Abrufen der Remotezwillingskonfiguration. Wenn bei einem dieser Schritte ein Fehler auftritt, kann dies zu einem Problem mit dem Sicherheits-Agent führen.

In diesem Leitfaden zur Problembehandlung lernen Sie Folgendes:

- Überprüfen, ob der Sicherheits-Agent ausgeführt wird
- Abrufen von Fehlern des Sicherheits-Agents
- Verstehen und Beheben von Fehlern des Sicherheits-Agents

## <a name="validate-if-the-security-agent-is-running"></a>Überprüfen, ob der Sicherheits-Agent ausgeführt wird

1. Wenn Sie überprüfen möchten, ob der Sicherheits-Agent ausgeführt wird, warten Sie nach der Installation des Agents einige Minuten, und führen Sie dann den folgenden Befehl aus.
     <br>

    **C-Agent**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C#-Agent**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Gibt der Befehl eine leere Zeile zurück, konnte der Sicherheits-Agent nicht gestartet werden.

## <a name="force-stop-the-security-agent"></a>Erzwingen der Beendigung des Sicherheits-Agents

Wenn der Sicherheits-Agent nicht gestartet werden kann, beenden Sie den Agent mit dem folgenden Befehl, und fahren Sie mit der Fehlertabelle weiter unten fort:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Abrufen von Fehlern des Sicherheits-Agents

1. Rufen Sie Sicherheits-Agent-Fehler mit dem folgenden Befehl ab:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Mit dem Befehl für den Sicherheits-Agent-Fehler werden alle vom Defender für IoT-Agent erstellten Protokolle abgerufen. Die Tabelle weiter unten enthält eine Beschreibung der Fehler sowie die entsprechenden Schritte zur Behebung.

> [!Note]
> Fehlerprotokolle werden in chronologischer Reihenfolge angezeigt. Notieren Sie sich den Zeitstempel des jeweiligen Fehlers, um die Behebung zu vereinfachen.

## <a name="restart-the-agent"></a>Neustarten des Agents

1. Nachdem Sie einen Sicherheits-Agent-Fehler ermittelt und behoben haben, starten Sie den Agent mit dem folgenden Befehl neu:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Wiederholen Sie den vorherigen Vorgang, um die Fehler abzurufen, wenn der Agent weiterhin einen Fehler beim Startvorgang verursacht.

## <a name="understand-security-agent-errors"></a>Grundlegendes zu den Fehlern des Sicherheits-Agents

Die meisten Fehler des Sicherheits-Agents werden im folgenden Format angezeigt:

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Fehlercode | Untergeordneter Fehlercode | Fehlerdetails | Behebung für C | Behebung für C# |
|--|--|--|--|--|
| Lokale Konfiguration | Fehlende Konfiguration | In der lokalen Konfigurationsdatei fehlt eine Konfiguration. In der Fehlermeldung sollte angegeben sein, welcher Schlüssel fehlt. | Fügen Sie der Datei „/var/LocalConfiguration.json“ den fehlenden Schlüssel hinzu. Ausführliche Informationen hierzu finden Sie unter [Understanding the LocalConfiguration.json file - C agent](azure-iot-security-local-configuration-c.md) (Grundlegendes zur Datei „LocalConfiguration.json“: C-Agent). | Fügen Sie der Datei „General.config“ den fehlenden Schlüssel hinzu. Ausführliche Informationen hierzu finden Sie unter [Understanding the local configuration file (C# agent)](azure-iot-security-local-configuration-csharp.md) (Grundlegendes zur lokalen Konfigurationsdatei (C#-Agent)). |
| Lokale Konfiguration | Analyse der Konfiguration nicht möglich | Ein Konfigurationswert kann nicht analysiert werden. In der Fehlermeldung sollte angegeben sein, welcher Schlüssel nicht analysiert werden kann. Ein Konfigurationswert kann nicht analysiert werden, weil der Wert nicht vom erwarteten Typ ist oder weil der Wert außerhalb des Bereichs liegt. | Korrigieren Sie den Wert des Schlüssels in der Datei „/var/LocalConfiguration.json“, sodass er dem LocalConfiguration-Schema entspricht. Ausführliche Informationen finden Sie unter [Understanding the local configuration file (C# agent)](azure-iot-security-local-configuration-csharp.md) (Grundlegendes zur lokalen Konfigurationsdatei (C#-Agent)). | Korrigieren Sie den Wert des Schlüssels in der Datei „General.config“, sodass er dem Schema entspricht. Ausführliche Informationen finden Sie unter [Understanding the LocalConfiguration.json file - C agent](azure-iot-security-local-configuration-c.md) (Grundlegendes zur Datei „LocalConfiguration.json“: C-Agent). |
| Lokale Konfiguration | Dateiformat | Fehler beim Analysieren der Konfigurationsdatei | Die Konfigurationsdatei ist beschädigt. Laden Sie den Agent herunter, und installieren Sie ihn neu. | - |
| Remotekonfiguration | Timeout | Der Agent konnte den azureiotsecurity-Modulzwilling nicht innerhalb des Timeoutzeitraums abrufen. | Stellen Sie sicher, dass die Authentifizierungskonfiguration richtig ist, und wiederholen Sie den Vorgang. | Der Agent konnte den azureiotsecurity-Modulzwilling nicht innerhalb des Timeoutzeitraums abrufen. Stellen Sie sicher, dass die Authentifizierungskonfiguration richtig ist, und wiederholen Sie den Vorgang. |
| Authentifizierung | Datei nicht vorhanden | Die Datei im angegebenen Pfad ist nicht vorhanden. | Stellen Sie sicher, dass die Datei im angegebenen Pfad vorhanden ist, oder navigieren Sie zur Datei **LocalConfiguration.json**, und ändern Sie die Konfiguration für **FilePath**. | Stellen Sie sicher, dass die Datei im angegebenen Pfad vorhanden ist, oder navigieren Sie zur Datei **Authentication.config**, und ändern Sie die Konfiguration für **filePath**. |
| Authentifizierung | Dateiberechtigung | Der Agent verfügt nicht über ausreichende Berechtigungen zum Öffnen der Datei. | Erteilen Sie dem Benutzer **asciotagent** Leseberechtigungen für die Datei im angegebenen Pfad. | Stellen Sie sicher, dass auf die Datei zugegriffen werden kann. |
| Authentifizierung | Dateiformat | Die angegebene Datei liegt nicht im richtigen Format vor. | Stellen Sie sicher, dass die angegebene Datei das richtige Format hat. Die Dateitypen „.pfx“ und „.pem“ werden unterstützt. | Stellen Sie sicher, dass es sich bei der Datei um eine gültige Zertifikatsdatei handelt. |
| Authentifizierung | Nicht autorisiert | Der Agent konnte mit den angegebenen Anmeldeinformationen nicht für IoT Hub authentifiziert werden. | Überprüfen Sie die Authentifizierungskonfiguration in der Datei „LocalConfiguration“. Vergewissern Sie sich, dass alle Angaben der Authentifizierungskonfiguration korrekt sind, und überprüfen Sie, ob das Geheimnis in der Datei mit der authentifizierten Identität übereinstimmt. | Überprüfen Sie die Authentifizierungskonfiguration in der Datei „Authentication.config“. Vergewissern Sie sich, dass alle Angaben der Authentifizierungskonfiguration korrekt sind, und überprüfen Sie dann, ob das Geheimnis in der Datei mit der authentifizierten Identität übereinstimmt. |
| Authentifizierung | Nicht gefunden | Das Gerät/Modul wurde gefunden. | Überprüfen Sie die Authentifizierungskonfiguration: Stellen Sie sicher, dass der Hostname korrekt ist und dass das Gerät in IoT Hub vorhanden ist und über ein azureiotsecurity-Zwillingsmodul verfügt. | Überprüfen Sie die Authentifizierungskonfiguration: Stellen Sie sicher, dass der Hostname korrekt ist und dass das Gerät in IoT Hub vorhanden ist und über ein azureiotsecurity-Zwillingsmodul verfügt. |
| Authentifizierung | Fehlende Konfiguration | In der Datei *Authentication.config* fehlt eine Konfiguration. In der Fehlermeldung sollte angegeben sein, welcher Schlüssel fehlt. | Fügen Sie der Datei *LocalConfiguration.json* den fehlenden Schlüssel hinzu. | Fügen Sie der Datei *Authentication.config* den fehlenden Schlüssel hinzu. Ausführliche Informationen hierzu finden Sie unter [Understanding the local configuration file (C# agent)](azure-iot-security-local-configuration-csharp.md) (Grundlegendes zur lokalen Konfigurationsdatei (C#-Agent)). |
| Authentifizierung | Analyse der Konfiguration nicht möglich | Ein Konfigurationswert kann nicht analysiert werden. In der Fehlermeldung sollte angegeben sein, welcher Schlüssel nicht analysiert werden kann. Ein Konfigurationswert kann nicht analysiert werden, weil der Wert nicht den erwarteten Typ hat oder weil der Wert außerhalb des Bereichs liegt. | Korrigieren Sie den Wert des Schlüssels in der Datei **LocalConfiguration.json**. | Korrigieren Sie den Wert des Schlüssels in der Datei **Authentication.config**, sodass er dem Schema entspricht. Ausführliche Informationen finden Sie unter [Understanding the LocalConfiguration.json file - C agent](azure-iot-security-local-configuration-c.md) (Grundlegendes zur Datei „LocalConfiguration.json“: C-Agent).|

## <a name="next-steps"></a>Nächste Schritte

- Lesen der [Übersicht](overview.md) über den Defender für IoT-Dienst
- Weitere Informationen zu Defender für IoT: [Info zu Agent-basierten Lösungen für Gerätehersteller](architecture-agent-based.md)
- Aktivieren des Defender für IoT-[Diensts](quickstart-onboard-iot-hub.md)
- Lesen Sie die [häufig gestellten Fragen zu Defender für IoT](resources-agent-frequently-asked-questions.md).
- Informieren Sie sich, wie Sie auf [Sicherheitsrohdaten](how-to-security-data-access.md) zugreifen.
- Machen Sie sich mit [Empfehlungen](concept-recommendations.md) vertraut.
- Machen Sie sich mit [Sicherheitswarnungen](concept-security-alerts.md) vertraut.

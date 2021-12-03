---
title: Lokale Konfiguration des Sicherheits-Agents von Defender für IoT (C#)
description: Erfahren Sie mehr über den Defender für IoT-Sicherheitsdienst und die lokale Konfigurationsdatei des Sicherheits-Agents für C#.
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: bfd70e8576bb139feb39d4a15e7076da0a4e0131
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293590"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Grundlegendes zur lokalen Konfigurationsdatei (C#-Agent)

Der Defender für IoT-Sicherheits-Agent verwendet Konfigurationen aus einer lokalen Konfigurationsdatei.

Der Sicherheits-Agent liest die Konfigurationsdatei einmal bei Beginn seiner Ausführung. Die Konfigurationen in der lokalen Konfigurationsdatei enthalten sowohl die Authentifizierungskonfiguration als auch andere Konfigurationen im Zusammenhang mit dem Agent.

Der C#-Sicherheits-Agent verwendet mehrere Konfigurationsdateien:

- **General.config** – Konfigurationen im Zusammenhang mit dem Agent.
- **Authentication.config** – Authentifizierungsbezogene Konfiguration (einschließlich Authentifizierungsdetails).
- **SecurityIotInterface.config** – IoT-bezogene Konfigurationen.

Die Konfigurationsdateien enthalten die Standardkonfiguration. Die Authentifizierungskonfiguration wird während der Agentinstallation aufgefüllt, und Änderungen an der Konfigurationsdatei werden beim Neustart des Agents vorgenommen.

## <a name="configuration-file-location"></a>Speicherort der Konfigurationsdatei

Linux:

- Konfigurationsdateien für das Betriebssystem befinden sich in `/var/ASCIoTAgent`.

Windows:

- Konfigurationsdateien für das Betriebssystem befinden sich im Verzeichnis des Sicherheits-Agents.

### <a name="generalconfig-configurations"></a>„General.config“-Konfigurationen

| Konfigurationsname | Mögliche Werte | Details |
|:-----------|:---------------|:--------|
| agentId | GUID | Eindeutiger Agent-Bezeichner |
| readRemoteConfigurationTimeout | TimeSpan | Zeitraum für das Abrufen der Remotekonfiguration aus IoT Hub. Wenn der Agent die Konfiguration innerhalb des angegebenen Zeitraums nicht abrufen kann, kommt es bei diesem Vorgang zu einem Timeout.|
| schedulerInterval | TimeSpan | Internes Scheduler-Intervall. |
| producerInterval | TimeSpan | Worker-Intervall für Ereignisersteller. |
| consumerInterval | TimeSpan | Worker-Intervall für Ereignisconsumer. |
| highPriorityQueueSizePercentage | 0 < Zahl < 1 | Der Teil des gesamten Caches, der für Nachrichten mit hoher Priorität reserviert ist. |
| logLevel | „Off“, „Fatal“, „Error“, „Warning“, „Information“, „Debug“  | Protokollmeldungen gleich und über diesem Schweregrad werden in der Debugging-Konsole („Syslog“ unter Linux) protokolliert. |
| fileLogLevel |  „Off“, „Fatal“, „Error“, „Warning“, „Information“, „Debug“| Protokollmeldungen gleich und über diesem Schweregrad werden in einer Datei („Syslog“ unter Linux) protokolliert. |
| diagnosticVerbosityLevel | „None“, „Some“, „All“ | Ausführlichkeitsgrad von Diagnoseereignissen. None: Diagnoseereignisse werden nicht gesendet. Some: Nur einige Diagnoseereignisse mit hoher Wichtigkeit werden gesendet. All: Alle Protokolle werden ebenfalls als Diagnoseereignisse gesendet. |
| logFilePath | Pfad zur Datei | Wenn „fileLogLevel > Off“ lautet, werden Protokolle in diese Datei geschrieben. |
| defaultEventPriority | „High“, „Low“, „Off“ | Standardereignispriorität. |

### <a name="generalconfig-example"></a>Beispiel für „General.config“

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Konfigurationsname | Mögliche Werte | Details |
|:-----------|:---------------|:--------|
| moduleName | Zeichenfolge | Name der Defender-IoT-micro-agent-Identität Dieser Name muss dem Namen der Modulidentität im Gerät entsprechen. |
| deviceId | Zeichenfolge | ID des Geräts (wie in Azure IoT Hub registriert). |
| schedulerInterval | „TimeSpan“-Zeichenfolge | Internes Scheduler-Intervall. |
| gatewayHostname | Zeichenfolge | Hostname des Azure IoT Hubs. Normalerweise \<my-hub\>.azure-devices.net |
| filePath | Zeichenfolge – Pfad zur Datei | Der Pfad zu der Datei, die das Authentifizierungsgeheimnis enthält.|
| type | „SymmetricKey“, „SelfSignedCertificate“ | Das Benutzergeheimnis für die Authentifizierung. Wählen Sie *SymmetricKey* aus, wenn das Benutzergeheimnis ein symmetrischer Schlüssel ist. Wählen Sie *SelfSignedCertificate* aus, wenn das Geheimnis ein selbstsigniertes Zertifikat ist. |
| identity | „DPS“, „Module“, „Device“ | Authentifizierungsidentität – „DPS“, wenn die Authentifizierung über DPS erfolgt, „Module“, wenn sie mit Anmeldeinformationen für das Modul erfolgt, oder „Device“, wenn sie mit Geräteanmeldeinformationen erfolgt.
| certificateLocationKind |  „LocalFile“, „Store“ | „LocalFile“, wenn das Zertifikat in einer Datei gespeichert ist; „Store“, wenn sich das Zertifikat in einem Zertifikatspeicher befindet. |
| idScope | Zeichenfolge | ID-Bereich von DPS |
| registrationId | Zeichenfolge  | Registrierungs-ID für DPS-Geräte. |
|

### <a name="authenticationconfig-example"></a>Beispiel für „Authentication.config“

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Konfigurationsname | Mögliche Werte | Details |
|:-----------|:---------------|:--------|
| transportType | „Ampq“ „Mqtt“ | IoT-Hub-Transporttyp. |
|

### <a name="securityiotinterfaceconfig-example"></a>Beispiel für „SecurityIotInterface.config“

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen der [Übersicht](overview.md) über den Defender für IoT-Dienst
- Informieren Sie sich ausführlicher über die [Architektur einer Agent-basierten Lösung](architecture-agent-based.md) für Defender für IoT.
- Aktivieren des Defender für IoT-[Diensts](quickstart-onboard-iot-hub.md)
- Lesen der [Häufig gestellten Fragen](resources-agent-frequently-asked-questions.md) über den Defender für IoT-Dienst
- Informieren Sie sich, wie Sie auf [Sicherheitsrohdaten](how-to-security-data-access.md) zugreifen.
- Machen Sie sich mit [Empfehlungen](concept-recommendations.md) vertraut.
- Machen Sie sich mit [Sicherheitswarnungen](concept-security-alerts.md) vertraut.

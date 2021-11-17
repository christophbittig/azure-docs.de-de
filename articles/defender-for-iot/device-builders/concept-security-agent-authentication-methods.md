---
title: Authentifizierungsmethoden des Sicherheits-Agents
description: Lernen Sie die verschiedenen Authentifizierungsmethoden kennen, die für den Defender für IoT-Dienst verfügbar sind.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 1d5aa69b5a44b07f90b063f741e65236f74e7dbd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331564"
---
# <a name="security-agent-authentication-methods"></a>Authentifizierungsmethoden des Sicherheits-Agents

In diesem Artikel werden die verschiedenen Authentifizierungsmethoden erläutert, die Sie mit dem AzureIoTSecurity-Agent für die Authentifizierung beim IoT Hub verwenden können.

Für jedes Gerät, für das in der IoT Hub-Instanz ein Onboarding in Defender für IoT durchgeführt wurde, ist ein IoT-Micro-Agent von Defender erforderlich. Für die Authentifizierung des Geräts kann Defender für IoT eine der beiden Methoden verwenden. Wählen Sie die Methode aus, die sich am besten für Ihre vorhandene IoT-Lösung eignet.

- Option „SecurityModule“
- Geräteoption

## <a name="authentication-methods"></a>Authentifizierungsmethoden

Für den AzureIoTSecurity-Agent von Azure Defender für IoT gibt es die beiden folgenden Authentifizierungsmethoden:

- Authentifizierungsmodus **IoT-Micro-Agent von Defender**<br>
Der Agent wird unabhängig von der Geräteidentität mithilfe der Identität „IoT-Micro-Agent von Defender“ authentifiziert.
Diese Authentifizierung bietet sich an, wenn der Sicherheits-Agent eine dedizierte Authentifizierungsmethode über den IoT-Micro-Agent von Defender (nur symmetrischer Schlüssel) verwenden soll.

- Authentifizierungsmodus **Gerät**<br>
Bei dieser Methode wird der Sicherheits-Agent zunächst mit der Geräteidentität authentifiziert. Nach der ersten Authentifizierung führt der Defender für IoT-Agent den Aufruf **REST** am IoT Hub durch und verwendet dabei die REST-API mit den Authentifizierungsdaten des Geräts. Der Defender für IoT-Agent fordert dann die Authentifizierungsmethode „IoT-Micro-Agent von Defender“ und die zugehörigen Daten von der IoT Hub-Instanz an. Im letzten Schritt nimmt der Defender für IoT-Agent eine Authentifizierung beim Defender für IoT-Modul vor.

Verwenden Sie diesen Authentifizierungstyp, wenn der Sicherheits-Agent eine vorhandene Geräteauthentifizierungsmethode (selbstsigniertes Zertifikat oder symmetrischer Schlüssel) erneut verwenden soll.

Informationen zur Konfiguration finden Sie unter [Security agent installation parameters (Installationsparameter für den Sicherheits-Agent)](#security-agent-installation-parameters).

## <a name="authentication-methods-known-limitations"></a>Bekannte Einschränkungen von Authentifizierungsmethoden

- Der Authentifizierungsmodus **SecurityModule** unterstützt nur die Authentifizierung mit symmetrischen Schlüsseln.
- Der Authentifizierungsmodus **Gerät** unterstützt kein von der Zertifizierungsstelle signiertes Zertifikat.

## <a name="security-agent-installation-parameters"></a>Installationsparameter für den Sicherheits-Agent

Beim [Bereitstellen eines Sicherheits-Agents](how-to-deploy-agent.md) müssen Authentifizierungsdetails als Argumente angegeben werden.
Diese Argumente werden in der folgenden Tabelle gezeigt.

|Linux-Parametername | Windows-Parametername | Kurzform für Parameter |BESCHREIBUNG|Tastatur|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity|AuthenticationIdentity|aui|Authentifizierungsidentität| **SecurityModule** oder **Device**|
|authentication-method|AuthenticationMethod|aum|Authentifizierungsmethode|**SymmetricKey** oder **SelfSignedCertificate**|
|file-path|FilePath|f|Vollständiger Pfad der Datei, die das Zertifikat oder den symmetrischen Schlüssel enthält| |
|host-name|HostName|hn|Vollqualifizierter Domänenname (FQDN) des IoT Hubs|Beispiel: ContosoIotHub.azure-devices.net|
|device-id|deviceId|di|Geräte-ID|Beispiel: MyDevice1|
|certificate-location-kind|CertificateLocationKind|cl|Speicherort des Zertifikats|**LocalFile** oder **Store**|
|

Wenn Sie das Installationsskript des Sicherheits-Agents verwenden, wird die folgende Konfiguration automatisch ausgeführt. Um die Authentifizierung des Sicherheits-Agents manuell zu bearbeiten, bearbeiten Sie die Konfigurationsdatei.

## <a name="change-authentication-method-after-deployment"></a>Ändern der Authentifizierungsmethode nach der Bereitstellung

Wenn Sie einen Sicherheits-Agent mit einem Installationsskript bereitstellen, wird automatisch eine Konfigurationsdatei erstellt.

Um Authentifizierungsmethoden nach der Bereitstellung zu ändern, muss die Konfigurationsdatei manuell bearbeitet werden.

### <a name="c-based-security-agent"></a>C#-basierter Sicherheits-Agent

Bearbeiten Sie _Authentication.config_ mit den folgenden Parametern:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C-basierter Sicherheits-Agent

Bearbeiten Sie _LocalConfiguration.json_ mit den folgenden Parametern:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Weitere Informationen

- [Security agents overview (Sicherheits-Agents (Übersicht))](security-agent-architecture.md)
- [Deploy security agent (Bereitstellen eines Sicherheits-Agents)](how-to-deploy-agent.md)
- [Access raw security data (Zugreifen auf Sicherheitsrohdaten)](how-to-security-data-access.md)

---
title: Azure IoT Edge-Sicherheits-Manager/-Modulruntime – Azure IoT Edge
description: Verwaltet das Sicherheitsniveau für IoT Edge-Gerät und die Integrität von Sicherheitsdiensten.
services: iot-edge
keywords: Sicherheit, sicheres Element, Enclave, TEE, IoT Edge
author: kgremban
ms.author: kgremban
ms.reviewer: eustacea
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: a7c714a818282ef42905e711c9f016c2e9c3e31e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286410"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge-Sicherheits-Manager

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Der Azure IoT Edge-Sicherheits-Manager wird ein streng abgegrenzter Sicherheitskern für den Schutz der IoT Edge-Geräte und aller zugehörigen Komponenten durch das Abstrahieren der sicheren Hardware. Der Sicherheits-Manager fungiert als zentraler Punkt zur Erhöhung der Sicherheit und stellt den Integrationspunkt für Technologie durch Gerätehersteller (Original Equipment Manufacturer, OEM) dar.

<!--1.1-->
:::moniker range="iotedge-2018-06"
Der Sicherheits-Manager abstrahiert die sichere Hardware (Secure Silicon) eines IoT Edge-Geräts.

![Azure IoT Edge-Sicherheits-Manager](media/edge-security-manager/iot-edge-security-manager.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Der Sicherheits-Manager abstrahiert die sichere Hardware (Secure Silicon) eines IoT Edge-Geräts und stellt ein Erweiterbarkeitsframework für zusätzliche Sicherheitsdienste bereit.

<!-- add new graphic here -->
:::moniker-end

Der IoT Edge-Sicherheits-Manager soll die Integrität des IoT Edge-Geräts und aller zugehörigen Softwarevorgänge schützen. Der Sicherheits-Manager überträgt die Vertrauensstellung von der zugrunde liegenden Hardware des Hardware-Vertrauensankers (falls verfügbar), um das Bootstrapping der IoT Edge-Runtime auszuführen und laufende Vorgänge zu überwachen.  Der IoT Edge-Sicherheits-Manager ist eine Software, die mit sicherer Hardware (sofern verfügbar und aktiviert) zusammenarbeitet, um die höchstmögliche Sicherheit zu bieten.  

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Darüber hinaus bietet der IoT Edge-Sicherheits-Manager ein sicheres Framework für Sicherheitsdiensterweiterungen über Module auf Hostebene. Zu diesen Diensten gehören Sicherheitsüberwachungfunktionen und Updates, für die Agents innerhalb des Geräts mit privilegiertem Zugriff auf einige Komponenten des Geräts erforderlich sind. Das Erweiterbarkeitsframework stellt sicher, dass solche Integrationen die allgemeine Systemsicherheit aufrechterhalten.
:::moniker-end

Zu den Aufgaben des IoT Edge-Sicherheits-Managers gehören unter anderem:

<!--1.1-->
:::moniker range="iotedge-2018-06"

* Durchführen von Bootstraps für das Azure IoT Edge-Gerät
* Steuern des Zugriffs auf den Vertrauensanker der Gerätehardware über Notardienste
* Überwachen der Integrität der IoT Edge-Vorgänge zur Laufzeit
* Empfang der Vertrauensdelegierung vom Hardwaresicherheitsmodul (HSM)
* Bereitstellen der Geräteidentität und Verwalten des Übergangs der Vertrauensstellung bei Bedarf
* Hosten und Schützen von Gerätekomponenten von Clouddiensten wie dem Device Provisioning-Dienst
* Bereitstellen von IoT Edge-Modulen mit eindeutigen Identitäten
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Durchführen von Bootstraps für das Azure IoT Edge-Gerät
* Steuern des Zugriffs auf den Vertrauensanker der Gerätehardware über Notardienste
* Überwachen der Integrität der IoT Edge-Vorgänge zur Laufzeit
* Bereitstellen der Geräteidentität und Verwalten des Vertrauensstellungsübergangs bei Bedarf
* Gewährleisten eines sicheren Betriebs von Client-Agents für Dienste, einschließlich der Geräteaktualisierung für IoT Hub und Azure Defender für IoT.
:::moniker-end

Der IoT Edge-Sicherheits-Manager besteht aus drei Komponenten:

<!--1.1-->
:::moniker range="iotedge-2018-06"

* Der IoT Edge-Sicherheits-Daemon
* Die HSM-PAL (Plattformabstraktionsschicht für Hardwaresicherheitsmodule)
* Hardwarevertrauensanker oder HSM (optional, aber dringend empfohlen)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Die IoT Edge-Modulruntime
* Abstraktionen des Hardwaresicherheitsmoduls (HSM) durch Standardimplementierungen wie PKCS#11 und Trusted Platform Module (TPM)
* Hardwarevertrauensanker oder HSM (optional, aber dringend empfohlen)

## <a name="changes-in-version-12"></a>Änderungen in Version 1.2

In den Versionen 1.0 und 1.1 von IoT Edge war eine Komponente namens **Sicherheitsdaemon** für die logischen Sicherheitsvorgänge des Sicherheits-Managers verantwortlich. Im Update auf Version 1.2 wurden mehrere wichtige Zuständigkeiten an das Sicherheitssubsystem des [Azure IoT-Identitätsdiensts](https://azure.github.io/iot-identity-service/) delegiert. Nachdem diese sicherheitsbezogenen Aufgaben aus dem Sicherheitsdaemon entfernt wurden, ergab sein Name keinen Sinn mehr. Um die Arbeit dieser Komponente in Version 1.2 und höher besser widerzuspiegeln, haben wir sie in **Modulruntime** umbenannt.
:::moniker-end

<!--1.1-->
:::moniker range="iotedge-2018-06"
## <a name="the-iot-edge-security-daemon"></a>Der IoT Edge-Sicherheits-Daemon

Der IoT Edge-Sicherheitsdaemon ist für die logischen Sicherheitsvorgänge des Sicherheits-Managers verantwortlich. Er stellt einen großen Teil der vertrauenswürdigen Rechenbasis des IoT Edge-Geräts dar.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
## <a name="the-iot-edge-module-runtime"></a>Die IoT Edge-Modulruntime

Die IoT Edge-Modulruntime delegiert die Vertrauensstellung vom Sicherheitssubsystem des [Azure IoT-Identitätsdiensts](https://azure.github.io/iot-identity-service/), um die Laufzeitumgebung für IoT Edge-Container zu schützen. Die Modulruntime ist für die logischen Sicherheitsvorgänge des Sicherheits-Managers verantwortlich. Er stellt einen großen Teil der vertrauenswürdigen Rechenbasis des IoT Edge-Geräts dar. Die Modulruntime verwendet Sicherheitsdienste des IoT-Identitätsdiensts, der wiederum durch das vom Gerätehersteller ausgewählte Hardwaresicherheitsmodul (HSM) verstärkt wird. Es wird dringend empfohlen, HSMs für die Gerätehärtung zu verwenden.
:::moniker-end

### <a name="design-principles"></a>Entwurfsprinzipien

IoT Edge folgt zwei Kernprinzipien: Maximieren der betrieblichen Integrität und Minimieren von Überfrachtung und Churn.

#### <a name="maximize-operational-integrity"></a>Maximieren der operativen Integrität

<!--1.1-->
:::moniker range="iotedge-2018-06"
Der IoT Edge-Sicherheits-Daemon wird mit der höchsten Integrität ausgeführt, die im Rahmen der Abwehrfunktionalität eines Hardware-Vertrauensankers möglich ist. Mit einer angemessenen Integration misst und überwacht der Hardware-Vertrauensanker den Sicherheits-Daemon statisch und zur Laufzeit, um Manipulationen zu verhindern.
:::moniker-end

<!--1.1-->
:::moniker range=">=iotedge-2020-11"
Die IoT Edge-Modulruntime wird mit der höchsten Integrität ausgeführt, die im Rahmen der Abwehrfunktionalität einer Vertrauensankerhardware möglich ist. Mit einer angemessenen Integration misst und überwacht der Hardware-Vertrauensanker den Sicherheits-Daemon statisch und zur Laufzeit, um Manipulationen zu verhindern.
:::moniker-end

Böswilliger physischer Zugriff auf Geräte ist in IoT grundsätzlich eine Bedrohung. Der Hardwarevertrauensanker spielt eine wichtige Rolle beim Schutz der Integrität des IoT Edge-Geräts.  Es gibt zwei Arten von Hardware-Vertrauensankern:

* Secure Elements für den Schutz vertraulicher Informationen wie Geheimnissen und kryptografischen Schlüsseln
* Secure Enclaves für den Schutz von Geheimnissen wie Schlüsseln sowie von sensiblen Workloads wie vertraulichen Machine Learning-Modellen und Messvorgängen

Es gibt zwei Arten von Ausführungsumgebungen, die Hardware-Vertrauensanker verwenden:

* Die Standardumgebung oder Rich Execution Environment (REE) basiert auf der Verwendung von Secure Elements zum Schutz vertraulicher Informationen.
* Die vertrauenswürdige Ausführungsumgebung (Trusted Execution Environment, TEE) basiert auf der Verwendung der Secure Enclave-Technologie zum Schutz vertraulicher Informationen und der Ausführung von Software.

<!--1.1-->
:::moniker range="iotedge-2018-06"
Bei Geräten, die Secure Enclaves als Hardwarevertrauensanker verwenden, sollte sich sensible Logik im IoT Edge-Sicherheitsdaemon innerhalb der Enclave befinden.  Nicht sensible Bereiche des Sicherheits-Daemons können sich auch außerhalb der TEE befinden.  In jedem Fall sollten ODMs (Original Design Manufacturer) und OEMs (Original Equipment Manufacturer) unbedingt die Vertrauensstellung aus ihrem HSM erweitern, um die Integrität des IoT Edge-Sicherheitsdaemons beim Start und zur Laufzeit zu überwachen und zu schützen.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Bei Geräten, die Secure Enclaves als Hardwarevertrauensanker verwenden, sollte sich sensible Logik in der IoT Edge-Modulruntime innerhalb der Enclave befinden.  Nicht sensible Bereiche der Modulruntime können sich auch außerhalb der TEE befinden.  In jedem Fall sollten ODMs (Original Design Manufacturer) und OEMs (Original Equipment Manufacturer) unbedingt die Vertrauensstellung aus ihrem HSM erweitern, um die Integrität der IoT Edge-Modulruntime beim Start und zur Laufzeit zu überwachen und zu schützen.
:::moniker-end

#### <a name="minimize-bloat-and-churn"></a>Minimale Überfrachtung und Änderung

<!--1.1-->
:::moniker range="iotedge-2018-06"
Eine weiteres Kernprinzip der IoT Edge-Sicherheits-Daemons ist die Minimierung von Änderungen.  Für die höchste Vertrauensstufe kann der IoT Edge-Sicherheits-Daemon eng mit dem Hardware-Vertrauensanker des Geräts verknüpft und als nativer Code ausgeführt werden.  In diesen Fällen ist es üblich, die IoT Edge-Software über die sicheren Updatepfade des Hardwarevertrauensankers statt über die Updatemechanismen des Betriebssystems zu aktualisieren, was schwierig sein kann.  Die Sicherheitsaktualisierung wird für IoT-Geräte empfohlen, aber übermäßige Updateanforderungen oder umfangreiche Updatepayloads können die Angriffsfläche auf vielfältige Weise vergrößern. Beispielsweise werden Sie möglicherweise dazu verleitet, einige Updates zu überspringen, um die Geräteverfügbarkeit zu maximieren. Daher ist das Design des IoT Edge-Sicherheitsdaemons recht kompakt, um die gut isolierte, vertrauenswürdige Rechenbasis klein zu halten und häufige Updates zu fördern.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Ein weiteres Kernprinzip der IoT Edge-Modulruntime ist die Begrenzung von Churn auf ein Mindestmaß.  Für die höchste Vertrauensstufe kann die IoT Edge-Modulruntime eng mit dem Hardwarevertrauensanker des Geräts verknüpft und als nativer Code ausgeführt werden.  In diesen Fällen ist es üblich, die IoT Edge-Software über die sicheren Updatepfade des Hardwarevertrauensankers statt über die Updatemechanismen des Betriebssystems zu aktualisieren, was schwierig sein kann.  Die Sicherheitsaktualisierung wird für IoT-Geräte empfohlen, aber übermäßige Updateanforderungen oder umfangreiche Updatepayloads können die Angriffsfläche auf vielfältige Weise vergrößern. Beispielsweise werden Sie möglicherweise dazu verleitet, einige Updates zu überspringen, um die Geräteverfügbarkeit zu maximieren. Daher ist das Design der IoT Edge-Modulruntime recht kompakt, um die gut isolierte, vertrauenswürdige Rechenbasis klein zu halten und häufige Updates zu fördern.
:::moniker-end

### <a name="architecture"></a>Architektur

<!--1.1-->
:::moniker range="iotedge-2018-06"

Der IoT Edge-Sicherheits-Daemon nutzt die gesamte verfügbare Technologie der Hardware-Vertrauensanker für die Erhöhung der Sicherheit.  Sie ermöglicht auch den Betrieb in unterschiedlichen Umgebungen mit einer Standardausführungsumgebung (bzw. Rich Execution Environment, REE) und einer vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE), wenn Hardwaretechnologien TEEs bieten. Rollenspezifische Schnittstellen ermöglichen es den Hauptkomponenten von IoT Edge, die Integrität des IoT Edge-Geräts und seiner Vorgänge zu gewährleisten.

![Architektur des Azure IoT Edge-Sicherheitsdaemons](media/edge-security-manager/iot-edge-security-daemon.png)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

Die IoT Edge-Modulruntime nutzt die verfügbare Hardwarevertrauensanker-Technologie zur Sicherheitsverstärkung.  Sie ermöglicht auch den Betrieb in unterschiedlichen Umgebungen mit einer Standardausführungsumgebung (bzw. Rich Execution Environment, REE) und einer vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE), wenn Hardwaretechnologien TEEs bieten. Rollenspezifische Schnittstellen ermöglichen es den Hauptkomponenten von IoT Edge, die Integrität des IoT Edge-Geräts und seiner Vorgänge zu gewährleisten.

![Architektur der Azure IoT Edge-Modulruntime](media/edge-security-manager/iot-edge-module-runtime.png)
:::moniker-end

#### <a name="cloud-interface"></a>Cloudschnittstelle

Die Cloudschnittstelle ermöglicht den Zugriff auf Clouddienste, die die Gerätesicherheit ergänzen.  Beispielsweise erlaubt diese Schnittstelle den Zugriff auf den [Device Provisioning Service](../iot-dps/index.yml) für das Identity Lifecycle Management des Geräts.  

#### <a name="management-api"></a>Verwaltungs-API

<!--1.1-->
:::moniker range="iotedge-2018-06"
Die Verwaltungs-API wird vom IoT Edge-Agent beim Erstellen, Starten, Beenden und Entfernen eines IoT Edge-Moduls aufgerufen. Der Sicherheitsdaemon speichert „Registrierungen“ für alle aktiven Module. Diese Registrierungen ordnen die Modulidentität einigen Eigenschaften des Moduls zu. Zu diesen Moduleigenschaften gehören beispielsweise die Prozess-ID (pid) des Prozesses, der im Container ausgeführt wird, und der Hash des Docker-Containerinhalts.

Mithilfe dieser Eigenschaften überprüft die Workload-API (siehe unten), ob der Aufrufer für eine Aktion autorisiert ist.

Die Verwaltungs-API ist eine privilegierte API, die nur vom IoT Edge-Agent aufgerufen werden kann.  Da der IoT Edge-Sicherheits-Daemon das Bootstrapping für den IoT Edge-Agent ausführt und ihn startet, überprüft er, ob der Agent nicht manipuliert wurde, und kann dann eine implizite Registrierung für den Agent erstellen. Derselbe Nachweisprozess, den die Workload-API verwendet, beschränkt auch den Zugriff auf die Verwaltungs-API auf ausschließlich den IoT Edge-Agent.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Die Verwaltungs-API wird vom IoT Edge-Agent beim Erstellen, Starten, Beenden und Entfernen eines IoT Edge-Moduls aufgerufen. Die Modulruntime speichert „Registrierungen“ für alle aktiven Module. Diese Registrierungen ordnen die Modulidentität einigen Eigenschaften des Moduls zu. Zu diesen Moduleigenschaften gehören beispielsweise die Prozess-ID (pid) des Prozesses, der im Container ausgeführt wird, und der Hash des Docker-Containerinhalts.

Mithilfe dieser Eigenschaften überprüft die Workload-API (siehe unten), ob der Aufrufer für eine Aktion autorisiert ist.

Die Verwaltungs-API ist eine privilegierte API, die nur vom IoT Edge-Agent aufgerufen werden kann.  Da die IoT Edge-Modulruntime das Bootstrapping für den IoT Edge-Agent ausführt und ihn startet, stellt sie sicher, dass der IoT Edge-Agent nicht manipuliert wurde. Anschließend kann sie eine implizite Registrierung für den IoT Edge-Agent erstellen. Derselbe Nachweisprozess, den die Workload-API verwendet, beschränkt auch den Zugriff auf die Verwaltungs-API auf ausschließlich den IoT Edge-Agent.
:::moniker-end

#### <a name="container-api"></a>Container-API

Die Container-API interagiert mit dem Containersystem, das für die Modulverwaltung verwendet wird, wie z.B. Moby oder Docker.

#### <a name="workload-api"></a>Workload-API

Die Workload-API ist für alle Module zugänglich. Sie ermöglicht einen Identitätsnachweis – entweder als vom HSM signiertes Token oder ein X.509-Zertifikat – und die zugehörige Vertrauenssammlung für ein Modul. Die Vertrauenssammlung enthält ZS-Zertifikate für alle anderen Server, denen die Module vertrauen sollen.

<!--1.1-->
:::moniker range="iotedge-2018-06"
Der IoT Edge-Sicherheits-Daemon verwendet einen Nachweisprozess zum Schutz dieser API. Wenn ein Modul diese API aufruft, versucht der Sicherheits-Daemon, eine Registrierung für die Identität zu finden. Ist er erfolgreich, verwendet er die Eigenschaften der Registrierung, um das Modul zu bewerten. Wenn das Ergebnis des Messprozesses mit der Registrierung übereinstimmt, wird ein neuer Identitätsnachweis generiert. Die zugehörigen ZS-Zertifikate (Vertrauenssammlung) werden an das Modul zurückgegeben.  Das Modul verwendet dieses Zertifikat zum Verbinden mit IoT Hub oder anderen Modulen oder zum Starten eines Servers. Wenn das signierte Token oder das Zertifikat demnächst abläuft, muss das Modul ein neues Zertifikat anfordern.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Die IoT Edge-Modulruntime verwendet einen Nachweisprozess zum Schutz dieser API. Wenn ein Modul diese API aufruft, versucht die Modulruntime eine Registrierung für die Identität zu finden. Ist er erfolgreich, verwendet er die Eigenschaften der Registrierung, um das Modul zu bewerten. Wenn das Ergebnis des Messprozesses mit der Registrierung übereinstimmt, wird ein neuer Identitätsnachweis generiert. Die zugehörigen ZS-Zertifikate (Vertrauenssammlung) werden an das Modul zurückgegeben.  Das Modul verwendet dieses Zertifikat zum Verbinden mit IoT Hub oder anderen Modulen oder zum Starten eines Servers. Wenn das signierte Token oder das Zertifikat demnächst abläuft, muss das Modul ein neues Zertifikat anfordern.
:::moniker-end

### <a name="integration-and-maintenance"></a>Integration und Wartung

<!--1.1-->
:::moniker range="iotedge-2018-06"
Microsoft verwaltet die Hauptcodebasis für den [IoT Edge-Sicherheits-Daemon auf GitHub](https://github.com/Azure/iotedge/tree/release/1.1/edgelet).
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Microsoft verwaltet die Hauptcodebasis für die [IoT Edge-Modulruntime](https://github.com/Azure/iotedge/tree/master/edgelet) und den [Azure IoT-Identitätsdienst](https://github.com/Azure/iot-identity-service) auf GitHub.

Beachten Sie beim Lesen der IoT Edge-Codebasis, dass sich die **Modulruntime** aus dem **Sicherheitsdaemon entwickelt hat.** Die Codebasis kann weiterhin Verweise auf den Sicherheitsdaemon enthalten.
:::moniker-end

#### <a name="installation-and-updates"></a>Installation und Updates

<!--1.1-->
:::moniker range="iotedge-2018-06"
Installation und Updates des IoT Edge-Sicherheits-Daemons werden über das Paketverwaltungssystem des Betriebssystems verwaltet. IoT Edge-Geräte mit einem Hardware-Vertrauensanker sollten zusätzliche Sicherheitsmaßnahmen für die Integrität des Daemons bereitstellen, indem sie den Lebenszyklus über sichere Start- und Updateverwaltungssysteme verwalten. Geräteersteller sollten diese Möglichkeiten basierend auf den jeweiligen Gerätefunktionen durchsuchen.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Installation und Updates der IoT Edge-Modulruntime werden über das Paketverwaltungssystem des Betriebssystems verwaltet. IoT Edge-Geräte mit einem Hardwarevertrauensanker sollten zusätzliche Sicherheitsmaßnahmen für die Integrität der Modulruntime bereitstellen, indem sie den Lebenszyklus über sichere Start- und Updateverwaltungssysteme verwalten. Geräteersteller sollten diese Möglichkeiten basierend auf den jeweiligen Gerätefunktionen durchsuchen.
:::moniker-end

#### <a name="versioning"></a>Versionsverwaltung

<!--1.1-->
:::moniker range="iotedge-2018-06"
Die IoT Edge-Runtime verfolgt und meldet die Version des IoT Edge-Sicherheits-Daemons. Die Version wird als das *runtime.platform.version*-Attribut der gemeldeten Eigenschaft des IoT Edge-Agent-Moduls gemeldet.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Die IoT Edge-Runtime verfolgt und meldet die Version der IoT Edge-Modulruntime. Die Version wird als das *runtime.platform.version*-Attribut der gemeldeten Eigenschaft des IoT Edge-Agent-Moduls gemeldet.
:::moniker-end

## <a name="hardware-security-module"></a>Hardwaresicherheitsmodul

<!--1.1-->
:::moniker range="iotedge-2018-06"
Die PAL (Platform Abstraction Layer) des Hardwaresicherheitsmoduls (HSM) abstrahiert die gesamte Vertrauensankerhardware, damit der Entwickler oder Benutzer sich nicht mit der Komplexität von IoT Edge befassen muss.  Sie enthält eine Kombination aus Anwendungsprogrammierschnittstelle (API) und domänenübergreifenden Kommunikationsverfahren (beispielsweise die Kommunikation zwischen einer Standardausführungsumgebung und einer Secure Enclave-Instanz).  Die eigentliche Implementierung der HSM-PAL hängt von der verwendeten Sicherheitshardware ab. Ihr Vorhandensein ermöglicht die Verwendung praktisch jeder Sicherheitshardware.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Der IoT Edge-Sicherheits-Manager implementiert die Schnittstellenstandards Trusted Platform Module und PKCS#11 für die Integration von Hardwaresicherheitsmodulen (HSMs). Mit diesen Standards kann praktisch jedes HSM integriert werden, selbst HSMs mit proprietären Schnittstellen. Es wird dringend empfohlen, HSMs für die Sicherheitshärtung zu verwenden.
:::moniker-end

## <a name="secure-silicon-root-of-trust-hardware"></a>Sicherer Hardware-Vertrauensanker

Sichere Hardware ist erforderlich, um innerhalb der IoT Edge-Geräthardware einen Vertrauensanker zu schaffen.  Es gibt verschiedene Arten von Sicherheitshardware, z. B. Trusted Platform Module (TPM), eingebettete Secure Elements (eSE), ARM TrustZone, Intel SGX oder benutzerdefinierte sichere Hardwaretechnologien.  Die Verwendung eines sicheren Vertrauensankers in Geräten wird aufgrund der Bedrohungen durch den physischen Zugriff auf IoT-Geräte empfohlen.

Der IoT Edge-Sicherheits-Manager versucht, die Komponenten, die bei benutzerdefinierten Sicherheitsmaßnahmen zum Schutz der Sicherheit und Integrität der Azure IoT Edge-Plattform beitragen, zu identifizieren und zu isolieren. Drittanbieter wie Gerätehersteller sollten die ihnen zur Verfügung stehenden benutzerdefinierten Sicherheitsfeatures für ihre Gerätehardware nutzen.  

Informieren Sie sich, wie Sie den Azure IoT-Sicherheits-Manager mit dem Trusted Platform Module (TPM) mithilfe von Software oder virtuellen TPMs härten können:  

Erstellen und Bereitstellen eines [IoT Edge-Geräts mit einem virtuellen TPM auf einem virtuellen Linux-Computer](how-to-provision-devices-at-scale-linux-tpm.md)

<!--1.1-->
:::moniker range="iotedge-2018-06"
Erstellen und Bereitstellen eines [IoT Edge-Geräts mit simuliertem TPM unter Windows](how-to-provision-devices-at-scale-windows-tpm.md)
:::moniker-end

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Schützen Ihrer IoT Edge-Geräte finden Sie in den folgenden Blogbeiträgen:

* [Schützen einer Intelligent Edge-Lösung](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)
* [Sichere Umsetzung der komplexen, bedarfsorientierten Zero-Touch-Bereitstellung von IoT-Geräten](https://azure.microsoft.com/blog/the-blueprint-to-securely-solve-the-elusive-zerotouch-provisioning-of-iot-devices-at-scale/)
* [Lösen der IoT-Gerätesicherheit im großen Stil durch Standards](https://azure.microsoft.com/blog/solving-iot-device-security-at-scale-through-standards/)

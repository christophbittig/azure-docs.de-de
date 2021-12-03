---
title: 'Edge Secured-Core: Zertifizierungsanforderungen'
description: 'Edge Secured-Core: Zertifizierungsanforderungen'
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 35091e1ccde554ce897dc629c3c372af05650458
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293856"
---
# <a name="edge-secured-core-certification-requirements-preview"></a>Edge Secured-Core: Zertifizierungsanforderungen (Vorschau) #

In diesem Dokument werden die gerätespezifischen Funktionen und Anforderungen beschrieben, die erfüllt werden müssen, um die Zertifizierung abzuschließen und ein Gerät im Azure IoT-Gerätekatalog mit der Bezeichnung „Edge Secured-Core“ aufzulisten.

## <a name="program-purpose"></a>Zweck des Programms ##
Edge Secured-Core ist eine inkrementelle Zertifizierung im Azure Certified Device-Programm für IoT-Geräte, die ein vollständiges Betriebssystem ausführen, wie etwa Linux oder Windows 10 IoT. Dieses Programm ermöglicht es Gerätepartnern, ihre Geräte zu differenzieren, indem ein zusätzlicher Satz an Sicherheitskriterien erfüllt wird. Geräte, die diesen Kriterien genügen, können diese Zusagen erfüllen:
1. Hardwarebasierte Geräteidentität 
2. Fähigkeit zum Erzwingen der Systemintegrität 
3. Auf dem neuesten Stand bleiben und Fähigkeit zur Remoteverwaltung.
4. Schutz von ruhenden Daten
5. Schutz von Daten während der Übertragung
6. Integrierter Sicherheits-Agent und integrierte Härtung
## <a name="requirements"></a>Anforderungen ##

---
|Name|SecuredCore.Built-in.Security|
|:---|:---|
|Status|Erforderlich|
|Beschreibung|Der Zweck des Tests besteht darin, sicherzustellen, dass die Geräte Sicherheitsinformationen und -ereignisse melden können, indem sie Daten an Microsoft Defender für IoT senden.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen |Das Gerät muss Sicherheitsprotokolle und Warnungen generieren. Geräteprotokolle und Warnungsmeldungen an Microsoft Defender für Cloud.<ol><li>Herunterladen und Bereitstellen des Sicherheits-Agents von GitHub</li><li>Überprüfen von Warnmeldungen von Microsoft Defender für IoT.</li></ol>|
|Ressourcen|[Azure-Dokumentation IoT Defender für IOT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Name|SecuredCore.Encryption.Storage|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests besteht darin, zu überprüfen, ob vertrauliche Daten in nichtflüchtigem Speicher verschlüsselt werden können.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss über das Toolset validiert werden, um sicherzustellen, dass die Speicherverschlüsselung aktiviert ist und der Standardalgorithmus XTS-AES mit einer Schlüssellänge von 128 Bit oder höher ist. </br></br>Hinweis: In der Vorschauversion vom Juni 2021 wird nur überprüft, ob DM-Crypt auf dem Gerät installiert ist und ob das Gerät über eine verschlüsselte Partition verfügt.|
|Ressourcen||

---
|Name|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|Status|Optional|
|BESCHREIBUNG|Zweck des Tests ist es, das Vorhandensein einer sicheren Enklave zu validieren und zu bestätigen, dass auf die Enklave von einem sicheren Agent aus zugegriffen werden kann.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset validiert werden, um sicherzustellen, dass der Azure Security Agent mit der sicheren Enklave kommunizieren kann|
|Ressourcen|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore.Hardware.Identity|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests besteht darin, zu überprüfen, ob die Geräteidentifizierung in der Hardware verankert ist.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss über das Toolset validiert werden, um sicherzustellen, dass das Gerät über ein TPM verfügt und dass es über den IoT Hub mit dem TPM Endorsement Key bereitgestellt werden kann.|
|Ressourcen|[Automatische Setupbereitstellung mit DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore.Update|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist die Überprüfung, ob das Gerät seine Firmware und Software empfangen und aktualisieren kann.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Bestätigung des Partners, dass er ein Update über Microsoft Update an das Gerät senden konnte. Weitere Informationen finden Sie unter [Device Update for IoT Hub (ADU)](../iot-hub-device-update/understand-device-update.md). Bei Linux-Geräten, die Device Update for IoT Hub verwenden, müssen zur Zertifizierung beim Testprozess für den geschützten Kern eine SWU-Updatedatei sowie gerätespezifische Informationen für den Zertifizierungsdienst bereitgestellt werden, um eine [Updatemanifestdatei](../iot-hub-device-update/update-manifest.md) zu generieren.|
|Ressourcen|[Device Update für IoT Hub](../iot-hub-device-update/index.yml)|

---
|Name|SecuredCore.Manageability.Configuration|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, zu überprüfen, ob die Geräte die Remotesicherheitsverwaltung unterstützen.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss mit Hilfe des Toolsets validiert werden, um sicherzustellen, dass das Gerät die Fähigkeit zur Remoteverwaltung und spezielle Sicherheitskonfigurationen unterstützt. Der Status wird an IoT Hub/Microsoft Defender für IoT zurückgemeldet.|
|Ressourcen||

---
|Name|SecuredCore.Manageability.Reset|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck dieses Tests ist es, das Gerät für zwei Anwendungsfälle zu validieren: a) Fähigkeit zur Durchführung einer Rücksetzung (Entfernen von Benutzerdaten, Entfernen von Benutzerkonfigurationen), b) Wiederherstellung des letzten als funktionierend bekannten Zustands des Geräts im Falle eines Updates, das Probleme verursacht.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch eine Kombination aus Toolset und eingereichter Dokumentation validiert werden, um zu bestätigen, dass das Gerät diese Funktionalität unterstützt. Der Gerätehersteller kann bestimmen, ob diese Funktionen für die Unterstützung von Remotezurücksetzung oder nur lokaler Zurücksetzung implementiert werden.|
|Ressourcen||

---
|Name|SecuredCore.Updates.Duration|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck dieser Richtlinie besteht darin, sicherzustellen, dass das Gerät sicher bleibt.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell|
|Überprüfen|Mit der Einreichung einhergehende Verpflichtung, dass zertifizierte Geräte für 60 Monate ab dem Datum der Einreichung auf dem neuesten Stand gehalten werden müssen. Aus den dem Käufer zur Verfügung stehenden Spezifikationen und den Geräten selbst sollte in irgendeiner Form die Dauer hervorgehen, für die ihre Software aktualisiert wird.|
|Ressourcen||

---
|Name|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck dieser Richtlinie ist es, sicherzustellen, dass es einen Mechanismus zum Sammeln und Verteilen von Berichten über Sicherheitslücken im Produkt gibt.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell|
|Überprüfen|Die Dokumentation zum Prozess zum Einreichen und Empfangen von Berichten über Sicherheitslücken für die zertifizierten Geräte wird überprüft.|
|Ressourcen||

---
|Name|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Mit dieser Richtlinie soll sichergestellt werden, dass Sicherheitslücken, die als hoch/kritisch eingestuft werden (unter Verwendung von CVSS 3.0), innerhalb von 180 Tagen nach Verfügbarkeit der Korrektur behoben werden.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell|
|Überprüfen|Die Dokumentation zum Prozess zum Einreichen und Empfangen von Berichten über Sicherheitslücken für die zertifizierten Geräte wird überprüft.|
|Ressourcen||


---
|Name|SecuredCore.Encryption.TLS|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist die Überprüfung der Unterstützung für die erforderlichen TLS-Versionen und Verschlüsselungssammlungen.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
Überprüfen|Das Gerät muss durch das Toolset validiert werden, um sicherzustellen, dass das Gerät mindestens die TLS-Version 1.2 und die folgenden erforderlichen TLS-Verschlüsselungssammlungen unterstützt.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Ressourcen| [TLS-Unterstützung in IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [TLS-Verschlüsselungssammlungen in Windows 10](/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Name|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist die Überprüfung, ob Updates signiert werden müssen.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset validiert werden, um sicherzustellen, dass Updates des Betriebssystems, der Treiber, der Anwendungssoftware, der Bibliotheken, der Pakete und der Firmware nur dann angewendet werden, wenn sie ordnungsgemäß signiert und validiert sind.
|Ressourcen||

---
|Name|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, die Startintegrität des Geräts zu überprüfen.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset validiert werden, um sicherzustellen, dass die Firmware- und Kernel-Signaturen bei jedem Start des Geräts validiert werden. <ul><li>UEFI: Sicherer Start ist aktiviert</li><li>Uboot: Überprüfter Start ist aktiviert</li></ul> </br> </br>Hinweis: In der Vorschauversion vom Juni 2021 wird nur das Vorhandensein von UEFI überprüft.|
|Ressourcen||

---
|Name|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck dieses Tests ist es, zu überprüfen, ob Codeintegrität auf diesem Gerät vorhanden ist.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset überprüft werden, um sicherzustellen, dass Codeintegrität aktiviert ist. </br> Windows: HVCI </br> Linux: dm-verity und IMA|
|Ressourcen||

---
|Name|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, zu bestätigen, dass Anwendungen, die Eingaben aus dem Netzwerk akzeptieren, nicht mit erhöhten Rechten ausgeführt werden.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset überprüft werden, um sicherzustellen, dass Dienste, die Netzwerkverbindungen akzeptieren, nicht mit SYSTEM- oder Stammberechtigungen ausgeführt werden.|
|Ressourcen||

---
|Name|SecuredCore.Protection.Baselines|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, zu überprüfen, ob das System einer Baseline-Sicherheitskonfiguration entspricht.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset überprüft werden, um sicherzustellen, dass die Benchmarks der Defender für Cloud IoT-Systemkonfigurationen ausgeführt wurden.|
|Ressourcen| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore.Firmware.Protection|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, sicherzustellen, dass das Gerät über angemessene Schutzmaßnahmen gegen Firmware-Sicherheitsbedrohungen verfügt.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset überprüft werden, um zu bestätigen, dass es durch einen der folgenden Ansätze vor Sicherheitsbedrohungen durch Firmware geschützt ist: <ul><li>DRTM + UEFI-Verwaltungsmodus-Gegenmaßnahmen</li><li>DRTM + UEFI-Verwaltungsmodushärtung</li><li>Genehmigte FW, die SRTM + Laufzeit-Firmwarehärtung durchführt</li></ul> |
|Ressourcen| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore.Firmware.Attestation|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, sicherzustellen, dass das Gerät eine Remotebescheinigung für den Microsoft Azure Attestation-Dienst ausstellen kann.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset überprüft werden, um sicherzustellen, dass Plattform-Startprotokolle und Messungen der Startaktivität gesammelt und per Fernzugriff an den Microsoft Azure Attestation-Dienst übermittelt werden können.|
|Ressourcen| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Name|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, zu bestätigen, dass DMA an extern zugänglichen Ports nicht aktiviert ist.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Wenn auf dem Gerät DMA-fähige externe Ports vorhanden sind, muss das Toolset überprüfen, ob die IOMMU oder SMMU für diese Anschlüsse Ports und konfiguriert ist.|
|Ressourcen||

---
|Name|SecuredCore.Protection.Debug|
|:---|:---|
|Status|Erforderlich|
|BESCHREIBUNG|Der Zweck des Tests ist es, zu bestätigen, dass die Debugfunktionalität auf dem Gerät deaktiviert ist.|
|Zielverfügbarkeit|2021|
|Gilt für|Jedes Gerät|
|Betriebssystem|Agnostisch|
|Überprüfungstyp|Manuell/Tools|
|Überprüfen|Das Gerät muss durch das Toolset überprüft werden, um sicherzustellen, dass die Debugfunktionalität eine Autorisierung zur Aktivierung erfordert.|
|Ressourcen||

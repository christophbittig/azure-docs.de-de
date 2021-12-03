---
title: Von Microsoft Defender für IoT unterstützte Protokolle
description: Hier erfahren Sie etwas über die Protokolle, die von Microsoft Defender für IoT unterstützt werden.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: ebfd983baf2e925fda66c5fc23edc380420db591
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305958"
---
# <a name="support-for-iot-ot-ics-and-scada-protocols"></a>Unterstützung für IoT-, OT-, ICS- und SCADA-Protokolle

Microsoft Defender für IoT stellt eine offene und vollständig kompatible OT-Cybersicherheitsplattform (Operation Technology = Bedientechnik) bereit. Defender für IoT wird an vielen verschiedenen Standorten bereitgestellt und reduziert IoT-, IT- und ICS-Risiken bei Bereitstellungen in anspruchsvollen und komplexen OT-Umgebungen in allen Schlüsselbranchen und geografischen Regionen.

## <a name="supported-protocols"></a>Unterstützte Protokolle

Azure Defender für IoT unterstützt eine breite Palette an Protokollen in einem vielfältigen Unternehmen und umfasst industrielle Automatisierungsanlagen für alle Industriebranchen, Unternehmensnetzwerke und Umgebungen von Gebäudeverwaltungssystemen (Building Management System, BMS). Für benutzerdefinierte oder proprietäre Protokolle bietet Microsoft ein SDK, mit dem benutzerdefinierte Protokollzergliederungen als Plug-Ins auf einfache Weise entwickelt, getestet und bereitgestellt werden können. Das SDK übernimmt all dies, ohne proprietäre Informationen (z. B. die Art und Weise, wie die Protokolle entworfen wurden) preiszugeben oder PCAPs zu teilen, die möglicherweise vertrauliche Informationen enthalten. Die unterstützten Protokolle sind nachfolgend aufgeführt.

### <a name="supported-protocols-passive-monitoring"></a>Unterstützte Protokolle (passive Überwachung)

In diesem Abschnitt werden Protokolle aufgeführt, die mithilfe von passiver Überwachung erkannt werden.

**ABB:** IEC61850 MMS (einschließlich ABB-Erweiterung)

**ASHRAE:** BACnet, BACnet BACapp, BACnet BVLC

**Beckhoff:** AMS (ADS), Twincat 

**Cisco:** CAPWAP Control, CAPWAP Data, CDP, LWAPP

**DNP.org:** DNP3

**Emerson:** DeltaV, Emerson OpenBSI/BSAP, Ovation DCS ADMD, Ovation DCS DPUSTAT, Ovation DCS SSRPC

**Emerson Fischer:** ROC

**Eurocontrol:** ASTERIX

**GE:** Bentley Nevada (System 1), EGD, GSM (GE MarkVI und MarkVIe), SRTP (GE)

**Honeywell:** ENAP, Experion DCS CDA, Experion DCS FDA

**IEC:** Codesys V3, ICCP TASE.2/IEC-60870, IEC60870-5 (IEC104/101), IEC60870-5-103 (gekapselt seriell), IEC61850 GOOSE, IEC61850 MMS, IEC61850 SMV (SAMPLED-VALUES), LonTalk (LonWorks)

**IEEE**: LLC, STP, VLAN

**IETF:** ARP, DCE RPC, DNS, FTP (FTP_ADAT, FTP_DATA), GSSAPI (RFC2743), HTTP, ICMP, IPv4, IPv6, LLDP, MDNS, NBNS, NTLM (NTLMSSP Auth Protocol), RPC, SMB/Browse/NBDGM, SMB/CIFS, SNMP, SPNEGO (RFC4178), SSH, Syslog, Telnet, TFTP, TPKT, UDP

**ISO:** CLNP (ISO 8473), COTP (ISO 8073), ISO Industrial Protocol, MQTT (IEC 20922)

**Medizin:** ASTM, HL7

**Microsoft:** Horizon-Communityzergliederungen, proprietäre Horizon-Zergliederungen (von Kunden entwickelt). Ausführliche Informationen finden Sie unter [Proprietäre Horizon-Protokollzergliederung](references-horizon-sdk.md).

**Mitsubishi:** Melsoft/Melsec (Mitsubishi Electric)

**Omron:** FINS

**Oracle:** TDS, TNS

**Rockwell Automation:** ENIP, EtherNet/IP CIP (einschließlich Rockwell-Erweiterung), EtherNet/IP CIP FW, Version 27 und höher

**Schneider Electric:** Modbus/TCP, Modbus TCP–Schneider Unity Extensions, OASYS (Schneider Electric Telvant) 

**Schneider Electric/Invensys:** Foxboro Evo, Foxboro I/A, Trident, TriGP, TriStation

**Schneider Electric/Modicon:** Modbus RTU

**Schneider Electric/Wonderware:** Wonderware Suitelink

**Siemens:** CAMP, PCS7, PCS7 WinCC – Historian, Profinet DCP, Profinet Realtime, Siemens PHD, Siemens S7, Siemens S7-Plus, Siemens SICAM, Siemens WinCC

**Toshiba:** Toshiba Computer Link

**Yokogawa:** Centum ODEQ (Centum/ProSafe DCS), HIS Equalize, Vnet/IP

### <a name="supported-protocols-active-monitoring"></a>Unterstützte Protokolle (aktive Überwachung)

In diesem Abschnitt werden Protokolle aufgeführt, die mithilfe von aktivem Probing erkannt werden, z. B. Ping-Sweeps und Abfragen.

**IETF:** Ping-Sweep, SNMP-Netzwerklayoutabfrage, SNMP-Abfrage

**Microsoft:** Windows WMI-Abfrage (WMI/WinRM erforderlich): Hardware, BIOS, Version, Software, Patches

**Rockwell Automation:** ENIP-Abfrage, ENIP-Scan, EtherNet/IP CIP (CIP-Abfrage)

**Siemens:** Siemens S7

## <a name="quickly-add-support-for-proprietary-restricted-protocols"></a>Schnelles Hinzufügen von Unterstützung für proprietäre, eingeschränkte Protokolle

Digitalisierung fördert die Bereitstellung von Milliarden von IoT-Geräten, gefolgt von einer erheblichen Zunahme bei der Konnektivität zwischen IT- und OT-Netzwerken. Dies bedeutet, dass Angriffsflächen größer werden, einschließlich eines höheren Risikos von gefährlichen Cyberangriffen auf Industriekontrollsysteme.

Zur Verringerung der Angriffsfläche ermöglicht das Horizon Protocol SDK die schnelle, sichere Unterstützung jedes Protokolls, das in IoT- und ICS-Umgebungen verwendet wird.

Horizon bietet Kunden und Technologiepartnern die folgenden Lösungen:

- Unbegrenzte, vollständige Unterstützung für allgemeine, proprietäre, benutzerdefinierte Protokolle oder Protokolle, die von einem beliebigen Standard abweichen.

- Ein neues Maß an Flexibilität und Umfang für DPI-Entwicklung.

- Ein Tool, das die OT-Sichtbarkeit und -Steuerung exponentiell erweitert, ohne dass ein Upgrade auf neue Defender für IoT-Plattformversionen durchgeführt werden muss.

- Die Sicherheit, die eine proprietäre Entwicklung (ohne Weitergabe vertraulicher Informationen) ermöglicht.

Mit dem Horizon SDK können Sie Plug-Ins schreiben, die Deep Packet Inspection (DPI) für den Datenverkehr aktivieren und Bedrohungen in Echtzeit erkennen. Das Horizon SDK ermöglicht auch zusätzliche Anpassungen. Das Horizon SDK ermöglicht es beispielsweise Ressourcenanbietern, Partnern oder Plattformbesitzern, den Text für Warnungen, Ereignisse und Protokollparameter zu lokalisieren und anzupassen.

[![Das Horizon SDK ermöglicht schnelle Unterstützung für 100 % der Protokolle, die in IoT- und ICS-Umgebungen verwendet werden.](media/concept-supported-protocols/sdk-horizon.png)](media/concept-supported-protocols/sdk-horizon-expanded.png#lightbox)

## <a name="collaborate-with-the-horizon-community"></a>Zusammenarbeiten mit der Horizon-Community

Werden Sie Teil einer Community, die den Weg zur digitalen Transformation und branchenweiten Zusammenarbeit für Protokollunterstützung vorangeht. Die Horizon ICS-Community ermöglicht den Wissensaustausch für Domänenexperten in kritischen Infrastrukturen, Gebäudeverwaltung, Produktionslinien, Transportsystemen und anderen Branchenführern.

Die Community bietet Tutorials, Diskussionsforen, Präsenzschulung, Whitepaper zu Schulungszwecken, Webinare und mehr.

Wir laden Sie ein, unserer Community hier beizutreten: <horizon-community@microsoft.com>

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [Proprietäre Horizon-Protokollzergliederung](references-horizon-sdk.md).

Sehen Sie sich unsere [Horizon-API](references-horizon-api.md) an.

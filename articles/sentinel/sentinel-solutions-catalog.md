---
title: Microsoft Sentinel Content Hub Katalog | Microsoft-Dokumentation
description: In diesem Artikel werden die derzeit verfügbaren Microsoft Sentinel Content Hub-Pakete aufgeführt und ausführlich beschrieben.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 375f6150c9d08f06c09f51ab18276a5ec97f576c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516919"
---
# <a name="microsoft-sentinel-content-hub-catalog"></a>Microsoft Sentinel Content Hub-Katalog

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Die Microsoft Sentinel Content Hub-Erfahrung befindet sich derzeit genau wie alle einzelnen Lösungspakete in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

[Microsoft Sentinel-Lösungen](sentinel-solutions.md) bieten eine konsolidierte Möglichkeit, Microsoft Sentinel-Inhalte (z. B. Datenconnectors, Arbeitsmappen, Analysen und Automatisierungen) in Ihrem Arbeitsbereich mit einem einzigen Bereitstellungsschritt abzurufen.

In diesem Artikel werden die vordefinierten (integrierten), bedarfsorientierten Microsoft Sentinel Datenconnectors und Lösungen aufgeführt, die für die Bereitstellung in Ihrem Arbeitsbereich verfügbar sind. Durch die Bereitstellung einer Lösung werden alle enthaltenen Sicherheitsinhalte, z. B. Datenconnectoren, Playbooks, Arbeitsmappen oder Regeln, im relevanten Bereich von Microsoft Sentinel erstellt. 

Weitere Informationen finden Sie unter [Zentrales Entdecken und Bereitstellen von vorkonfigurierten Microsoft Sentinel-Inhalten und -Lösungen](sentinel-solutions-deploy.md).

## <a name="domain-solutions"></a>Domänenlösungen

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Microsoft Insider-Risikomanagement** |[Datenconnector](data-connectors-reference.md#microsoft-365-insider-risk-management-irm-preview), Arbeitsmappe, Analyseregeln, Hunting-Abfragen |Sicherheit - Insider-Bedrohung | Microsoft|
| **Microsoft MITRE ATT&CK-Lösung für die Cloud**| Arbeitsmappen, Analyseregeln, Hunting-Abfragen|Sicherheit - Bedrohungsschutz, Sicherheit - Andere |Microsoft |
| **Microsoft Sentinel Deception** | [Arbeitsmappen, Analyseregeln, Watchlists](monitor-key-vault-honeytokens.md)  | Sicherheit - Bedrohungsschutz  |Microsoft |
|**Zero Trust** (TIC3.0) |Arbeitsmappen |Identität, Sicherheit - Andere |Microsoft |
| | | | |

## <a name="arista-networks"></a>Arista Networks

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Arista Networks** (Awake Security) |Datenconnector, Arbeitsmappen, Analyseregeln | Sicherheit - Netzwerk |[Arista - Awake Security](https://awakesecurity.com/) |
| | | | |


## <a name="armorblox"></a>Armorblox

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Armorblox - Sentinel** |Datenconnector | Sicherheit - Bedrohungsschutz |[Armorblox](https://www.armorblox.com/contact/) |
| | | | |




## <a name="azure"></a>Azure

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Azure Firewall-Lösung für Sentinel**| [Datenconnector](data-connectors-reference.md#azure-firewall), Arbeitsmappe, Analyseregeln, Playbooks, Hunting-Abfragen, benutzerdefinierter Logik-App-Connector |Sicherheit - Netzwerksicherheit, Netzwerkbetrieb | Community|
|**Microsoft Sentinel für SQL PaaS**     |  [Datenconnector](data-connectors-reference.md#azure-sql-databases), Arbeitsmappe, Analyseregeln, Playbooks, Hunting-Abfragen     | Application        |      Community   |
|**Microsoft Sentinel Training Lab** |Arbeitsmappe, Analyseregeln, Playbooks, Hunting-Abfragen | Training und Tutorials |Microsoft |
|**Azure SQL** | [Datenconnector](data-connectors-reference.md#azure-sql-databases), Arbeitsmappe, Analysen, Playbooks, Hunting-Abfragen  | Application |Microsoft  |
| | | | |


## <a name="box"></a>Feld

|Name   |Includes  |Kategorien |Unterstützt von  |
|------------------|---------|---------|---------|
|**Box-Lösung**| Datenconnector, Arbeitsmappe, Analyseregeln, Hunting-Abfragen, Parser |  Speicher, Anwendung  | Microsoft|
| | | | |


## <a name="check-point"></a>Check Point

|Name   |Includes  |Kategorien |Unterstützt von  |
|------------------|---------|---------|---------|
|**Check Point Microsoft Sentinel-Lösungen**   |[Datenconnector](data-connectors-reference.md#check-point), Playbooks, benutzerdefinierter Logik App-Connector  | Sicherheit - Automatisierung (SOAR) | [Prüfpunkt](https://www.checkpoint.com/support-services/contact-support/)|
| | | | |


## <a name="cisco"></a>Cisco

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Cisco ACI** |Datenconnector, Parser |Sicherheit - Netzwerk |Microsoft |
|**Cisco ASA** |[Datenconnector](data-connectors-reference.md#cisco-asa), Playbooks, benutzerdefinierter Logik App-Connector |Sicherheit - Automatisierung (SOAR) |Microsoft |
|**Cisco Duo Security** |Datenconnector, Parser | Identität|Microsoft |
|**Cisco ISE**  |Datenconnector, Arbeitsmappen, Analyseregeln, Playbooks, Hunting-Abfragen, Parser, benutzerdefinierter Logik-App-Connector |Netzwerkbetrieb, Sicherheit - Andere | Microsoft |
|**Cisco Meraki** |[Data connector](data-connectors-reference.md#cisco-meraki-preview), playbooks, benutzerdefinierter Logik-App-Connector |Sicherheit - Netzwerk |Microsoft |
|**Cisco Secure Email Gateway / ESA** |Datenconnector, Parser |Sicherheit - Bedrohungsschutz |Microsoft |
|**Cisco StealthWatch** |Datenconnector, Parser |Sicherheit - Netzwerk | Microsoft|
|**Cisco Umbrella** |[Datenconnector](data-connectors-reference.md#cisco-umbrella-preview), Arbeitsmappen, Analyseregeln, Playbooks, Hunting-Abfragen, Parser, benutzerdefinierter Logik-App-Connector |Sicherheit - Cloudsicherheit |Microsoft |
|**Cisco Web Security Appliance (WSA)** | Datenconnector, Parser|Sicherheit - Netzwerk |Microsoft |
| | | | |


## <a name="cloudflare"></a>Cloudflare


|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Cloudflare-Lösung**|Datenconnector, Arbeitsmappen, Analyseregeln, Hunting-Abfragen, Parser| Security - Netzwerk, Netzwerkbetrieb |Microsoft |
| | | | |


## <a name="contrast-security"></a>Contrast Security



|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Microsoft Sentinel-Lösung zum Schutz von Kontrasten**|Datenconnector, Arbeitsmappen, Analyseregeln |Sicherheit - Bedrohungsschutz |Microsoft  |
| | | | |

## <a name="crowdstrike"></a>Crowdstrike


|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**CrowdStrike Falcon Endpoint Protection-Lösung**| Datenconnector, Arbeitsmappen, Analyseregeln, Playbooks, Parser| Sicherheit - Bedrohungsschutz| Microsoft|
| | | | |

## <a name="digital-guardian"></a>Digital Guardian


|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Digital Guardian** |Datenconnector, Parser |Sicherheit - Informationsschutz |Microsoft |
| | | |

## <a name="falconforce"></a>FalconForce

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**FalconFriday-Inhalt - FalconFriday** |Analyseregeln |Benutzerverhalten (UEBA), Sicherheit - Insiderbedrohung | [FalconForce](https://www.falconforce.nl/en/)|
| | | |

## <a name="fireeye-nx-network-security"></a>FireEye NX (Netzwerksicherheit)

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**FireEye NX (Netzwerksicherheit)** |Datenconnector, Parser |Sicherheit - Netzwerk| Microsoft|
| | | |

## <a name="flare-systems-firework"></a>Flare Systems Firework

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Flare Systems Firework** |Datenconnector |Sicherheit - Bedrohungsschutz |Microsoft|
| | | |

## <a name="forescout"></a>Forescout

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Forescout** |Datenconnector, Parser |Sicherheit - Netzwerk | Microsoft|
| | | |

## <a name="fortinet-fortigate"></a>Fortinet Fortigate

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Fortinet Fortigate** |[Datenconnector](data-connectors-reference.md#fortinet), Playbooks, benutzerdefinierter Logik App-Connector|Sicherheit - Automatisierung (SOAR) | Microsoft|
| | | |


## <a name="google"></a>Google

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Google Cloud Platform DNS-Lösung** |Datenconnector, Parser |Cloudanbieter, Netzwerkbetrieb |Microsoft |
|**Google Cloud Platform Cloud Monitoring-Lösung**|Datenconnector, Parser |Cloud-Anbieter | Microsoft|
|**Google Cloud Platform Identitäts- und Zugriffsverwaltungslösung**|Datenconnector, Arbeitsmappe, Analyseregeln, Playbooks, Hunting-Abfragen, Parser, benutzerdefinierter Logik-App-Connector|Cloudanbieter, Identität |Microsoft |
| | | | |


## <a name="hyas"></a>HYAS

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**HYAS Insight-Lösungskatalog für Microsoft Sentinel**| Playbooks| Sicherheit - Threat Intelligence, Sicherheit - Automatisierung (SOAR) |Microsoft |
| | | | |

## <a name="imperva"></a>Imperva

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Imperva Cloud WAF** (formal Imperva Incapsula)| [Datenconnector](data-connectors-reference.md#imperva-waf-gateway-preview), Parser| Sicherheit - Netzwerk | Microsoft|
| | | | |

## <a name="infoblox"></a>InfoBlox

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**InfoBlox Bedrohungsschutz / InfoBlox Cloud Datenconnector**| [Datenconnector](data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview), Arbeitsmappe, Analyseregeln| Sicherheit - Bedrohungsschutz | Microsoft|
| | | | |


## <a name="ironnet"></a>IronNet

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**IronNet CyberSecurity Iron Defense - Microsoft Sentinel** | |Sicherheit - Netzwerk |Microsoft |
| | | |



## <a name="juniper"></a>Juniper

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Juniper IDP** |Datenconnector, Parser|Sicherheit - Netzwerk |Microsoft |
| | | | |


## <a name="kaspersky"></a>Kaspersky

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Kaspersky AntiVirus** |Datenconnector, Parser   | Sicherheit - Bedrohungsschutz|Microsoft |
| | | | |


## <a name="lookout"></a>Lookout

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Lookout Mobile Threat Defense für Microsoft Sentinel**| [Datenconnector](data-connectors-reference.md#lookout-mobile-threat-defense-preview)|Sicherheit - Netzwerk |[Lookout](https://www.lookout.com/support) |
| | | |

## <a name="mcafee"></a>McAfee

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**McAfee ePolicy Orchestrator-Lösung**| Datenconnector, Arbeitsmappe, Analyseregeln, Playbooks, Hunting-Abfragen, Parser, benutzerdefinierter Logik-App-Connector| Sicherheit - Bedrohungsschutz| Microsoft |
|**McAfee Netzwerksicherheit-Plattformlösung** (Intrushield) + Anti Antivirus Information (T1 minus Logic Apps) |Datenconnector, Arbeitsmappen, Analyseregeln, Hunting-Abfragen, Parser |Sicherheit - Bedrohungsschutz | Microsoft|
| | | | |

## <a name="microsoft"></a>Microsoft

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Microsoft Sentinel 4 Microsoft Dynamics 365**     |   [Datenconnector](data-connectors-reference.md#dynamics-365), Arbeitsmappen, Analyseregeln und Hunting-Abfragen |      Application   |Microsoft         |
|**Microsoft Sentinel für Teams**     | Datenconnector, Analyseregeln, Playbooks, Hunting-Abfragen      |   Application      |    Community     |
| | | | |


## <a name="oracle"></a>Oracle


|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Oracle Cloud Infrastructure** |Datenconnector, Parser | Cloud-Anbieter | Microsoft|
|**Oracle Database-Auditlösung** | Datenconnector, Arbeitsmappe, Analyseregeln, Hunting-Abfragen, Parser| Application|Microsoft |
| | | | |

## <a name="palo-alto"></a>Garmisch-Partenkirchen

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Palo Alto PAN-OS**|[Datenconnector](#palo-alto), Playbooks, benutzerdefinierter Logik App-Connector |Sicherheit - Automatisierung (SOAR), Sicherheit - Netzwerk |Microsoft |
|**Prisma-Lösung von Palo Alto**|[Datenconnector](#palo-alto), Arbeitsmappen, Analyseregeln, Hunting-Abfragen, Parser |Sicherheit - Cloudsicherheit |Microsoft |
| | | | |

## <a name="ping-identity"></a>Ping Identity

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**PingFederate-Lösung** |Datenconnector, Arbeitsmappen, Analyseregeln, Hunting-Abfragen, Parser| Identität|Microsoft |
| | | | |

## <a name="proofpoint"></a>Proofpoint

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Proofpoint POD-Lösung** |[Datenconnector](data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview), Arbeitsmappe, Analyseregeln, Hunting-Abfragen, Parser| Sicherheit - Bedrohungsschutz|Microsoft |
|**Proofpoint TAP-Lösung** | Arbeitsmappen, Analyseregeln, Playbooks, benutzerdefinierte Logik App-Verbindung|Sicherheit - Automatisierung (SOAR), Sicherheit - Bedrohungsschutz |Microsoft |
| | | |

## <a name="qualys"></a>Qualys

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Qualys VM-Lösung** |Arbeitsmappen, Analyseregeln |Sicherheit - Sicherheitsrisiko-Management |Microsoft |
| | | | |

## <a name="rapid7"></a>Rapid7

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Rapid7 InsightVM CloudAPI-Lösung** |Datenconnector, Parser|Sicherheit - Sicherheitsrisiko-Management |Microsoft |
| | | | |

## <a name="reversinglabs"></a>ReversingLabs

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**ReversingLabs TitaniumCloud File Enrichment-Lösung**|Playbooks |Sicherheit – Threat Intelligence |[ReversingLabs](https://support.reversinglabs.com/hc/en-us) |
| | | | |

## <a name="riskiq"></a>RiskIQ

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**RiskIQ-Playbooks für Security Intelligence**|Playbooks |Sicherheit - Threat Intelligence, Sicherheit - Automatisierung (SOAR) |[RiskIQ](https://www.riskiq.com/integrations/microsoft/) |
| | | | |

## <a name="rsa"></a>RSA

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**RSA SecurID** |Datenconnector, Parser |Sicherheit - Andere, Identität |Microsoft |
| | | |



## <a name="sap"></a>SAP

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Continuous Threat Monitoring für SAP**|[Datenconnector](sap-deploy-solution.md), [Arbeitsmappen, Analyseregeln, Watchlisten](sap-solution-security-content.md) | Application  |Community |
| | | | |

## <a name="semperis"></a>Semperis

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Semperis**|Datenconnector, Arbeitsmappen, Analyseregeln, Parser | Sicherheit - Bedrohungsschutz, Identität  |[Semperis](https://www.semperis.com/contact-us/) |
| | | | |

## <a name="senserva-pro"></a>Senserva Pro

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Senserva-Angebot für Microsoft Sentinel** |Datenconnector, Arbeitsmappen, Analyseregeln, Hunting-Abfragen |Kompatibilität |[Senserva](https://www.senserva.com/contact/) |
| | | | |


## <a name="sonrai-security"></a>Sonrai-Sicherheit

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Sonrai-Sicherheit – Microsoft Sentinel** |Datenconnector, Arbeitsmappen, Analyseregeln   | Kompatibilität|Sonrai-Sicherheit |
| | | | |

## <a name="slack"></a>Puffer

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Slack Audit-Lösung**|Datenconnector, Arbeitsmappen, Analyseregeln, Hunting-Abfragen, Parser |Application| Microsoft|
| | | | |


## <a name="sophos"></a>Sophos

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Sophos Endpoint Protection-Lösung** |Datenconnector, Parser| Sicherheit - Bedrohungsschutz |Microsoft |
|**Sophos XG Firewall-Lösung**| Arbeitsmappen, Analyseregeln, Parser |Sicherheit - Netzwerk |Microsoft |
| | | | |


## <a name="symantec"></a>Symantec

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Symantec-Endpunkt**|Datenconnector, Arbeitsmappe, Analyseregeln, Playbooks, Hunting-Abfragen, Parser| Sicherheit - Bedrohungsschutz|Microsoft |
|**Symantec ProxySG-Lösung**|Arbeitsmappen, Analyseregeln |Sicherheit - Netzwerk |Symantec |
| | | | |

## <a name="tenable"></a>Tenable

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Tenable Nessus Scanner/IO VM-Berichte für die Cloud**  | Datenconnector, Parser| Sicherheit - Sicherheitsrisiko-Management| Microsoft |
| | | | |


## <a name="trend-micro"></a>Trend Micro

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Trend Micro Apex One-Lösung**  | Datenconnector, Hunting-Abfragen, Parser| Sicherheit - Bedrohungsschutz|Microsoft |
| | | | |




## <a name="ubiquiti"></a>Ubiquiti

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Ubiquiti UniFi-Lösung**|Datenconnector, Arbeitsmappen, Analyseregeln, Hunting-Abfragen, Parser |Sicherheit - Netzwerk |Microsoft |
| | | | |


## <a name="varmour"></a>vArmour

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**vArmour Application Controller- und Microsoft Sentinel-Lösung**|Datenconnector, Arbeitsmappe, Analyseregeln |IT-Betrieb |[vArmour](https://www.varmour.com/contact-us/) |
| | | | |

## <a name="vectra"></a>Vectra

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Vectra Stream-Lösung** |Datenconnector, Hunting-Abfragen, Parser |Sicherheit - Netzwerk |Microsoft |
| | | |


## <a name="vmware"></a>VMware

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**VMware Carbon Black-Lösung**|Arbeitsmappen, Analyseregeln| Sicherheit - Bedrohungsschutz| Microsoft|
| | | | |

## <a name="zeek-network"></a>Zeek Network

|Name    |Includes  |Kategorien |Unterstützt von  |
|---------|---------|---------|---------|
|**Corelight für Microsoft Sentinel**|Datenconnector, Arbeitsmappen, Analyseregeln, Hunting-Abfragen, Parser | IT-Vorgänge, Sicherheit - Netzwerk | [Zeek Network](https://support.corelight.com/)|
| | | | |


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie mehr über Microsoft Sentinel-Lösungen gelernt und erfahren, wie Sie diese suchen und bereitstellen.

- Erfahren Sie mehr über [Microsoft Sentinel-Lösungen](sentinel-solutions.md).
- [Finden und Bereitstellen von Microsoft Sentinel-Lösungen](sentinel-solutions-deploy.md)

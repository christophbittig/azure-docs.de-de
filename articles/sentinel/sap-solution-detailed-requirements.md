---
title: Detaillierte SAP-Anforderungen für die Microsoft Sentinel-Lösung für SAP | Microsoft-Dokumentation
description: Erfahren Sie mehr über die detaillierten SAP-Systemanforderungen für die Microsoft Sentinel-Lösung für SAP.
author: batamig
ms.author: bagold
ms.service: microsoft-sentinel
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: f826ac6f3d5ec00ca2cf11948a1a18d0cb95de32
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521109"
---
# <a name="microsoft-sentinel-sap-solution-detailed-sap-requirements-public-preview"></a>Detaillierte SAP-Anforderungen für die Microsoft Sentinel-Lösung für SAP (öffentliche Vorschau)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das [Standardverfahren für die Bereitstellung der Microsoft Sentinel-Lösung für SAP](sap-deploy-solution.md) umfasst die erforderlichen SAP-Änderungsanforderungen und SAP-Hinweise und stellt eine integrierte Rolle mit allen erforderlichen Berechtigungen bereit.

In diesem Artikel sind die erforderlichen SAP-Änderungsanforderungen, -Hinweise und -Berechtigungen im Detail aufgeführt.

Verwenden Sie diesen Artikel als Referenz, wenn Sie Administrator sind oder [die SAP-Lösung manuell bereitstellen](sap-solution-deploy-alternate.md). Dieser Artikel richtet sich an fortgeschrittene SAP-Benutzer.


> [!IMPORTANT]
> Die Microsoft Sentinel-Lösung für SAP befindet sich derzeit in der PREVIEW. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

> [!NOTE]
> Zusätzliche Anforderungen werden aufgeführt, wenn Sie Ihren SAP-Datenconnector über eine sichere SNC-Verbindung bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md).
>
## <a name="recommended-virtual-machine-sizing"></a>Empfohlen Dimensionierung für virtuelle Computer

In der folgenden Tabelle wird die empfohlene Dimensionierung für Ihren virtuellen Computer beschrieben, je nach beabsichtigter Nutzung:

|Verwendung  |Empfohlene Dimensionierung  |
|---------|---------|
|**Mindestspezifikation**, z. B. für eine Laborumgebung     |   Eine *Standard_B2s*-VM      |
|**Standardconnector** (Standard)     |   Eine *DS2_v2*-VM mit: <br>– 2 Kernen<br>– 8 GB Arbeitsspeicher      |
|**Mehrere Connectors**     |Eine *Standard_B4ms*-VM mit: <br>– 4 Kernen<br>– 16 GB Arbeitsspeicher         |
|     |         |

## <a name="required-sap-log-change-requests"></a>Erforderliche SAP-Protokolländerungsanforderungen

Abhängig von Ihrer SAP-Basisversion sind die folgenden SAP-Protokolländerungsanforderungen für die SAP-Lösung erforderlich:

- Für **SAP-Basisversionen ab 7.50** installieren Sie NPLK900144.
- **Für niedrigere Versionen** installieren Sie NPLK900146.
- **Zum Erstellen einer SAP-Rolle mit den erforderlichen Autorisierungen** installieren Sie für jede unterstützte SAP-Basisversion NPLK900163. Weitere Informationen finden Sie unter [Konfigurieren Ihres SAP-Systems](sap-deploy-solution.md#configure-your-sap-system) und [Erforderliche ABAP-Autorisierungen](#required-abap-authorizations).

> [!NOTE]
> Durch die erforderlichen SAP-Protokolländerungsanforderungen werden benutzerdefinierte RFC-FMs verfügbar gemacht, die für den Connector erforderlich sind. Es werden keine Standardobjekte oder benutzerdefinierten Objekte geändert.
>

## <a name="required-sap-notes"></a>Erforderliche SAP-Hinweise

Wenn Sie über eine SAP-Basisversion 7.50 oder niedriger verfügen, installieren Sie die folgenden SAP-Hinweise:

|SAP BASIS-Versionen  |Erforderlicher Hinweis |
|---------|---------|
|– 750 SP01 bis SP12<br>– 751 SP01 bis SP06<br>– 752 SP01 bis SP03     |  2641084: Standardisierter Lesezugriff für die Protokolldaten der Sicherheitsüberwachung       |
|– 700 bis 702<br>– 710 bis 711, 730, 731, 740 und 750     | 2173545: CD: CHANGEDOCUMENT_READ_ALL        |
|– 700 bis 702<br>– 710 bis 711, 730, 731 und 740<br>– 750 bis 752     | 2502336: CD (Change Document): RSSCD100 – schreibgeschützt aus dem Archiv, nicht aus der Datenbank        |
|     |         |

Greifen Sie über die [Launchpad-Website des SAP-Supports](https://support.sap.com/en/index.html) auf die SAP-Hinweise zu.
## <a name="requires-sap-ports-access"></a>Erfordert Zugriff auf SAP-Ports:
Der Host der SAP-Umgebung über die folgenden TCP-Ports: 32xx, 5xx13 und 33xx, wobei xx für die SAP-Instanznummer steht.

## <a name="required-abap-authorizations"></a>Erforderliche ABAP-Autorisierungen

In der folgenden Tabelle sind die ABAP-Autorisierungen aufgeführt, die der Back-End-SAP-Benutzer benötigt, um Microsoft Sentinel mit den SAP-Protokollen zu verbinden. Weitere Informationen finden Sie unter [Konfigurieren Ihres SAP-Systems](sap-deploy-solution.md#configure-your-sap-system).

Die erforderlichen Autorisierungen sind nach Protokolltyp aufgelistet. Sie benötigen nur die Autorisierungen, die für die Protokolltypen aufgeführt sind, die Sie in Microsoft Sentinel erfassen möchten.

> [!TIP]
> Um die Rolle mit allen erforderlichen Autorisierungen zu erstellen, stellen Sie die SAP-Änderungsanforderung NPLK900163 auf Ihrem SAP-System bereit. Diese Änderungsanforderung erstellt die Rolle **/MSFTSEN/SENTINEL_CONNECTOR**, und Sie, in der Regel ein SAP-Basis- oder Rollenbesitzer, müssen die Rolle dem ABAP-Benutzer zuweisen, der eine Verbindung mit Azure Sentinel herstellt.
>

| Autorisierungsobjekt | Feld | Wert |
| -------------------- | ----- | ----- |
| **Alle RFC-Protokolle** | | |
| S_RFC | FUGR | /OSP/SYSTEM_TIMEZONE |
| S_RFC | FUGR | ARFC |
| S_RFC | FUGR | STFC |
| S_RFC | FUGR | RFC1 |
| S_RFC | FUGR | SDIFRUNTIME  |
| S_RFC | FUGR | SMOI |
| S_RFC | FUGR | SYST |
| S_RFC | FUGR/FUNC | SRFC/RFC_SYSTEM_INFO |
| S_RFC | FUGR/FUNC | THFB/TH_SERVER_LIST |
| S_TCODE | TCD | SM51 |
| **ABAP-Anwendungsprotokoll**  | | |
| S_APPL_LOG | ACTVT | Anzeige |
| S_APPL_LOG | ALG_OBJECT | * |
| S_APPL_LOG | ALG_SUBOBJ | * |
| S_RFC | FUGR | SXBP_EXT |
| S_RFC | FUGR | /MSFTSEN/_APPLOG |
| **Protokoll für ABAP-Änderungsdokumente** | | |
| S_RFC | FUGR | /MSFTSEN/_CHANGE_DOCS |
| **ABAP CR-Protokoll** | | |
| S_RFC | FUGR | CTS_API |
| S_RFC | FUGR | /MSFTSEN/_CR |
| S_TRANSPRT | ACTVT | Anzeige |
| S_TRANSPRT | TTYPE | * |
| **ABAP DB-Tabellendatenprotokoll** | | |
| S_RFC | FUGR | /MSFTSEN/_TD |
| S_TABU_DIS | ACTVT | Anzeige |
| S_TABU_DIS | DICBERCLS | &NC& |
| S_TABU_DIS | DICBERCLS | + Beliebiges Objekt, das für die Protokollierung erforderlich ist |
| S_TABU_NAM | ACTVT | Anzeige |
| S_TABU_NAM | TABLE | + Beliebiges Objekt, das für die Protokollierung erforderlich ist |
| S_TABU_NAM | TABLE | DBTABLOG |
| **ABAP-Auftragsprotokoll** | | |
| S_RFC | FUGR | SXBP |
| S_RFC | FUGR | /MSFTSEN/_JOBLOG |
| **ABAP-Auftragsprotokoll, ABAP-Anwendungsprotokoll** | | |
| S_XMI_PRD | INTERFACE | XBP |
| **ABAP-Sicherheitsüberwachungsprotokoll – XAL** | | |
| Alle RFC | S_RFC | FUGR |
| S_ADMI_FCD | S_ADMI_FCD | AUDD |
| S_RFC | FUGR | SALX |
| S_USER_GRP | ACTVT | Anzeige |
| S_USER_GRP | CLASS | * |
| S_XMI_PRD | INTERFACE | XAL |
| **ABAP-Sicherheitsüberwachungsprotokoll – XAL, ABAP-Auftragsprotokoll, ABAP-Anwendungsprotokoll** | | |
| S_RFC | FUGR | SXMI |
| S_XMI_PRD | EXTCOMPANY | Microsoft |
| S_XMI_PRD | EXTPRODUCT | Microsoft Sentinel |
| **ABAP-Sicherheitsüberwachungsprotokoll – SAL** | | |
| S_RFC | FUGR | RSAU_LOG |
| S_RFC | FUGR | /MSFTSEN/_AUDITLOG |
| **ABAP-Spoolprotokoll, ABAP-Spoolausgabeprotokoll** | | |
| S_RFC | FUGR | /MSFTSEN/_SPOOL |
| **ABAP-Workflowprotokoll** | | |
| S_RFC | FUGR | SWRR |
| S_RFC | FUGR | /MSFTSEN/_WF |
| **Benutzerdaten** | | |
| S_RFC | FUNC | RFC_READ_TABLE |
| | |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Bereitstellen der Microsoft Sentinel-Lösung für SAP](sap-deploy-solution.md)
- [Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md)
- [Konfigurationsoptionen für Experten, lokale Bereitstellung und SAPControl-Protokollquellen](sap-solution-deploy-alternate.md)
- [Referenz zu Protokollen für die Microsoft Sentinel-Lösung für SAP](sap-solution-log-reference.md)
- [Microsoft Sentinel-Lösung für SAP: verfügbare Sicherheitsinhalte](sap-solution-security-content.md)
- [Problembehandlung bei der Bereitstellung der Microsoft Sentinel-Lösung für SAP](sap-deploy-troubleshoot.md)

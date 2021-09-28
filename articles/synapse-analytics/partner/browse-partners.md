---
title: Ermitteln von Drittanbieterlösungen von Azure Synapse Partnern über Synapse Studio
description: Erfahren Sie, wie Sie neue Lösungen von Drittanbietern ermitteln, die eng mit Azure Synapse Partner verzahnt sind.
services: synapse-analytics
manager: santoshb
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/14/2021
author: periclesrocha
ms.author: procha
ms.reviewer: omafnan, wiassaf
ms.custom: seo-lt-2019
ms.openlocfilehash: c765fcce0b95027ffa28915752bd3f370070a894
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481846"
---
# <a name="discover-partner-solutions-through-synapse-studio"></a>Ermitteln von Partnerlösungen über Synapse Studio

Synapse Studio ermöglicht die Ermittlung von Lösungspartnern, welche die Funktionen von Azure Synapse erweitern. Von Datenconnectors über Datenwranglingtools, Orchestrierungs-Engines und andere Workloads dient die Seite zum Durchsuchen von **Partnern** als Hub zum Ermitteln von ISV-Anwendungen und -Lösungen von Drittanbietern, die nachweislich mit Azure Synapse Analytics kompatibel sind. Synapse Studio vereinfacht die ersten Schritte mit diesen Partnern. In vielen Fällen erfolgt dies durch die automatisierte Einrichtung der ersten Verbindung mit der Partnerplattform.

## <a name="participating-partners"></a>Teilnehmende Partner
In der folgenden Tabelle sind die derzeit unterstützten Partnerlösungen aufgeführt. Rufen Sie diese Website regelmäßig auf, da die Liste stets mit neuen Partnern ergänzt wird. 

| Partner | Projektmappenname |
| ------- | ------------- |
| ![Incorta](./media/data-integration/incorta-logo.png) | Incorta Intelligent Ingest für Azure Synapse |
| ![Informatica](./media/data-integration/informatica_logo.png) | Informatica Intelligent Data Management Cloud |
| ![Qlik-Datenintegration (ehemals Attunity)](./media/business-intelligence/qlik_logo.png) | Qlik-Datenintegration (ehemals Attunity) |

## <a name="requirements"></a>Requirements (Anforderungen)
Wenn Sie eine Partneranwendung ausgewählt haben, stellt Azure Synapse Studio eine Sandboxumgebung bereit, die Sie für diese Testversion verwenden können, um sicherzustellen, dass Sie schnell mit Partnerlösungen experimentieren können, bevor Sie sich entscheiden, sie mit Ihren Produktionsdaten zu verwenden. Die folgenden Freigaben werden erstellt: 

|  Objekt  |    Details    |
| -------- | ------------- |
| Ein [dedizierter SQL-Pool](../overview-what-is.md) mit dem Namen **Partner_[PartnerName]_pool** | DW100c-Leistungsebene |
| Eine [SQL-Anmeldung](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) mit dem Namen **Partner_[PartnerName]_login** | Erstellt auf Ihrer `master`-Datenbank Das Kennwort für diese SQL-Anmeldung wird von Ihnen bei der Erstellung Ihrer Testversion angegeben.|
| [Datenbankbenutzer](../../azure-sql/database/logins-create-manage.md) | Ein neuer Datenbankbenutzer, der einer neuen SQL-Anmeldung zugeordnet ist. Dieser Benutzer wird der Rolle db_owner für die neu erstellte Datenbank hinzugefügt. |

In allen Fällen ist **[PartnerName]** der Name des Drittanbieter-ISV, der die Testversion anbietet. 

### <a name="security"></a>Sicherheit 
Nachdem die erforderlichen Objekte erstellt wurden, sendet Synapse Studio Informationen zu Ihrer neuen Sandboxumgebung an die Partneranwendung, sodass eine benutzerdefinierte Testumgebung ermöglicht wird. Die folgenden Informationen werden an unsere Partner gesendet: 
- Vorname
- Nachname
- E-Mail-Adresse
-  Details zur Synapse-Umgebung, die zum Herstellen einer Verbindung erforderlich ist:     
    - DNS-Name Ihres Synapse-Arbeitsbereichs (Servername)
    - Der Name des SQL-Pools (Datenbank)
    - SQL-Anmeldung (nur Benutzername)
    
Wir geben niemals Kennwörter für die Partneranwendung weiter, einschließlich des Kennworts der neu erstellten SQL-Anmeldung. Sie müssen Ihr Kennwort in der Partneranwendung eingeben.

### <a name="costs"></a>Kosten
Für den dedizierten SQL-Pool, der für Ihre Partner-Testversion erstellt wird, fallen laufende Kosten an, die auf der Anzahl von DWU-Blöcken und den Ausführungsstunden basieren. Halten Sie den SQL-Pool an, der für diese Partnertestversion erstellt wurde, wenn er nicht verwendet wird, um unnötige Gebühren zu vermeiden. 

## <a name="starting-a-new-partner-trial"></a>Starten einer neuen Partnertestversion 

1) Wählen Sie auf der Startseite von Synapse Studio unter **Mehr entdecken** > **browse partners** (Partner durchsuchen) aus.
2) Auf der Seite „Partner durchsuchen“ werden alle Partner angezeigt, die derzeit Testversionen anbieten, die direkte Verbindungen mit Azure Synapse ermöglichen. Wählen Sie eine Partnerlösung aus.
3) Auf der Seite mit den Partnerdetails finden Sie relevante Informationen zu dieser Anwendung und Links, um mehr über ihre Lösung zu erfahren. Wenn Sie bereit sind, eine Testversion zu starten, wählen Sie **Verbindung mit Partner**.
4) Notieren Sie sich auf der Lösungsseite **Verbinden mit Lösung von [PartnerName]** die Anforderungen dieser Partnerverbindung. Ändern Sie ggf. den Namen des SQL-Pools und die SQL-Anmeldeparameter (oder übernehmen Sie die Standardwerte), geben Sie das Kennwort Ihrer neuen SQL-Anmeldung ein, und wählen Sie **Verbinden** aus.

Die erforderlichen Objekte werden für Ihre Partnertestversion erstellt. Sie werden dann zu einer Partnerseite weitergeleitet, um (falls erforderlich) zusätzliche Informationen bereitzustellen und Ihre Testversion zu starten. 

> [!NOTE]
> Microsoft kontrolliert Partnertestversionen nicht. Partner bieten Produkttests nach ihren eigenen Bedingungen an, und die Bedienung, die Verfügbarkeit von Testversionen und die Features können je nach Partner variieren. Microsoft bietet keinen Support für Drittanbieteranwendungen, die in Synapse Studio angeboten werden. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu einigen anderen Partnern finden Sie unter [Partner für die Datenintegration](data-integration.md), [Partner für die Datenverwaltung](data-management.md) und [Partner für maschinelles Lernen und KI](machine-learning-ai.md).
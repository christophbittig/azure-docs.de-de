---
title: Konfigurieren von Advanced Threat Protection
titleSuffix: Azure SQL Managed Instance
description: Advanced Threat Protection identifiziert anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank in Azure SQL Managed Instance hinweisen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: b6f03129076ea4c87b7cbd176681cd1323d9d453
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345717"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Konfigurieren von Advanced Threat Protection in Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Advanced Threat Protection](../database/threat-detection-overview.md) für eine [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) identifiziert anomale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder sie missbräuchlich zu verwenden. Mithilfe von Advanced Threat Protection können folgende Aktivitäten identifiziert werden: **potenzielle SQL-Einschleusung**, **Zugriff von einem ungewöhnlichen Standort oder Rechenzentrum**, **Zugriff über einen unbekannten Prinzipal oder eine potenziell schädliche Anwendung** und **Brute-Force-SQL-Anmeldeinformationen**. Weitere Informationen finden Sie im Abschnitt zu den [Advanced Threat Protection-Warnungen](../database/threat-detection-overview.md#alerts).

Sie können sich über [E-Mail-Benachrichtigungen](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) oder im [Azure-Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal) über die erkannten Bedrohungen benachrichtigen lassen.

[Advanced Threat Protection](../database/threat-detection-overview.md) ist Teil des [Microsoft Defender für SQL](../database/azure-defender-for-sql.md)-Angebots. Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf Advanced Threat Protection und dessen Verwaltung sind über das zentrale Microsoft Defender für SQL-Portal möglich.

##  <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Navigieren Sie zur Konfigurationsseite der Instanz von SQL Managed Instance, die geschützt werden soll. Wählen Sie unter **Sicherheit** die Option **Defender für SQL** aus.
3. Gehen Sie auf der Konfigurationsseite für Microsoft Defender für SQL folgendermaßen vor:
   - **Aktivieren** Sie Microsoft Defender für SQL.
   - Konfigurieren Sie die E-Mail-Adresse für **Warnungen senden an**, die bei Erkennung anomaler Datenbankaktivitäten Sicherheitswarnungen erhalten sollen.
   - Wählen Sie das **Azure Storage-Konto** aus, in dem Überwachungsdatensätze zu anomalen Bedrohungen gespeichert werden.
   - Wählen Sie die **Advanced Threat Protection-Typen** aus, die Sie konfigurieren möchten. Erfahren Sie mehr über [Advanced Threat Protection-Warnungen](../database/threat-detection-overview.md).
4. Klicken Sie auf **Speichern**, um die neue oder aktualisierte Microsoft Defender für SQL-Richtlinie zu speichern.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="Einrichten von Advanced Threat Protection":::

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Advanced Threat Protection](../database/threat-detection-overview.md).
- Informationen zu verwalteten Instanzen finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- Erfahren Sie mehr über [Advanced Threat Protection für Azure SQL-Datenbank](../database/threat-detection-configure.md).
- Weitere Informationen zur [Überwachung von SQL Managed Instance](./auditing-configure.md).
- Erfahren Sie mehr zu [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md).

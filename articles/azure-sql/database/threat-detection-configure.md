---
title: Konfigurieren von Advanced Threat Protection
description: Advanced Threat Protection identifiziert anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsbedrohungen für die Datenbank in Azure SQL-Datenbank hinweisen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: f2402fe795141a00ac488cb163930cbde58141f6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325637"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Konfigurieren von Advanced Threat Protection für Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Advanced Threat Protection](threat-detection-overview.md) für Azure SQL-Datenbank erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese zu missbrauchen. Mithilfe von Advanced Threat Protection können folgende Aktivitäten identifiziert werden: **potenzielle SQL-Einschleusung**, **Zugriff von einem ungewöhnlichen Standort oder Rechenzentrum**, **Zugriff über einen unbekannten Prinzipal oder eine potenziell schädliche Anwendung** und **Brute-Force-SQL-Anmeldeinformationen**. Weitere Informationen finden Sie im Abschnitt zu den [Advanced Threat Protection-Warnungen](threat-detection-overview.md#alerts).

Sie können sich über [E-Mail-Benachrichtigungen](threat-detection-overview.md#explore-detection-of-a-suspicious-event) oder im [Azure-Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal) über die erkannten Bedrohungen benachrichtigen lassen.

[Advanced Threat Protection](threat-detection-overview.md) ist Teil des [Microsoft Defender für SQL](azure-defender-for-sql.md)-Angebots. Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf Advanced Threat Protection und dessen Verwaltung sind über das zentrale Microsoft Defender für SQL-Portal möglich.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Einrichten von Advanced Threat Protection im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zur Konfigurationsseite des Servers, den Sie schützen möchten. Wählen Sie in den Sicherheitseinstellungen die Option **Defender für Cloud** aus.
3. Gehen Sie auf der Konfigurationsseite für **Microsoft Defender für SQL** folgendermaßen vor:

   - Aktivieren Sie **Microsoft Defender für SQL** auf dem Server.
   - Geben Sie unter **Advanced Threat Protection Settings** (Advanced Threat Protection-Einstellungen) im Textfeld **Warnungen senden an** eine Liste von E-Mail-Adressen an, die bei der Erkennung von anomalen Datenbankaktivitäten Sicherheitswarnungen empfangen sollen.
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="Einrichten von Advanced Threat Protection":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Einrichten von Advanced Threat Protection mit PowerShell

Ein Skriptbeispiel finden Sie unter [Konfigurieren von Überwachung und Advanced Threat Protection mit PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Advanced Threat Protection](threat-detection-overview.md).
- Erfahren Sie mehr über [Advanced Threat Protection in SQL Managed Instance](../managed-instance/threat-detection-configure.md).  
- Erfahren Sie mehr zu [Microsoft Defender für SQL](azure-defender-for-sql.md).
- Weitere Informationen zur [Überwachung](../../azure-sql/database/auditing-overview.md).
- Erfahren Sie mehr zu [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md).
- Weitere Informationen zu den Preisen finden Sie unter [SQL-Datenbank: Preise](https://azure.microsoft.com/pricing/details/sql-database/).

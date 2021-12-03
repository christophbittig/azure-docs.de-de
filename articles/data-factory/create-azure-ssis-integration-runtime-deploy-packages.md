---
title: Bereitstellen von SSIS-Paketen
description: Erfahren Sie, wie Sie SSIS-Pakete in Azure Data Factory mit der integrierten Azure-SSIS-Laufzeit bereitstellen und ausführen können.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d32025ab757698448f9d8a36467c09e9b501f206
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842946"
---
# <a name="deploy-ssis-packages"></a>Bereitstellen von SSIS-Paketen

Nach der Konfiguration Ihrer Azure-SSIS-Integrationslaufzeit können Sie Pakete direkt in Azure bereitstellen und ausführen.

## <a name="using-ssisdb"></a>Verwenden von SSISDB

Wenn Sie SSISDB verwenden, können Sie Ihre Pakete darin bereitstellen und sie mithilfe der Azure-fähigen SSDT- oder SSMS-Tools in Ihrer Azure-SSIS IR ausführen. Mit diesen Tools wird über den zugehörigen Serverendpunkt eine Verbindung mit Ihrem Datenbankserver hergestellt: 

- Bei einem Azure SQL-Datenbankserver weist der Serverendpunkt das Format `<server name>.database.windows.net` auf.
- Bei einer verwalteten Instanz mit privatem Endpunkt weist der Serverendpunkt das Format `<server name>.<dns prefix>.database.windows.net` auf.
- Bei einer verwalteten Instanz mit öffentlichem Endpunkt weist der Serverendpunkt das Format `<server name>.public.<dns prefix>.database.windows.net,3342` auf. 

## <a name="using-file-system-azure-files-or-msdb"></a>Verwenden von Dateisystem, Azure Files oder MSDB

Wenn Sie SSISDB nicht verwenden, können Sie Ihre Pakete im Dateisystem, in Azure Files oder der MSDB bereitstellen, das bzw. die von Ihrer Azure SQL Managed Instance gehostet wird, und sie mit den Befehlszeilen-Hilfsprogrammen [dtutil](/sql/integration-services/dtutil-utility) und [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) in Ihrer Azure-SSIS IR ausführen. 

Weitere Informationen finden Sie unter [Bereitstellen von SQL Server Integration Services-Projekten und -Paketen (SSIS)](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

In beiden Fällen können Sie Ihre bereitgestellten Pakete auch in der Azure-SSIS IR-Instanz ausführen, indem Sie die Aktivität „SSIS-Paket ausführen“ in Data Factory-Pipelines verwenden. Weitere Informationen finden Sie unter [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe des Azure-Portals bereitstellen](create-azure-ssis-integration-runtime-portal.md).
- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe von Azure PowerShell bereitstellen](create-azure-ssis-integration-runtime-powershell.md).
- [Erfahren Sie, wie Sie eine Azure-SSIS IR mithilfe einer Azure Resource Manager-Vorlage bereitstellen](create-azure-ssis-integration-runtime-resource-manager-template.md).

Sehen Sie sich auch andere Themen zur Azure-SSIS IR in dieser Dokumentation an:

- [Azure SSIS-Integration Runtime:](concepts-integration-runtime.md#azure-ssis-integration-runtime) Dieser Artikel enthält allgemeine Informationen zu Integration Runtimes, einschließlich der Azure-SSIS IR.
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
- [Bereitstellen und Ausführen eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Herstellen einer Verbindung mit dem SSIS-Katalog (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Herstellen einer Verbindung mit lokalen Datenquellen mit Windows-Authentifizierung) 
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)

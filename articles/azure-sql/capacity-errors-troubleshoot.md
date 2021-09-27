---
title: Kapazitätsfehler mit Azure SQL-Ressourcen beheben
description: Erfahren Sie, wie Sie mögliche Kapazitätsfehler beheben können, wenn Sie versuchen, Azure SQL Datenbank- oder Azure SQL Managed Instance-Ressourcen bereitzustellen oder zu skalieren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 09/03/2021
ms.custom: references_regions
ms.openlocfilehash: 885be735055eaf65d67466694e65d6cf7fdf00da
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481843"
---
# <a name="resolve-capacity-errors-with-azure-sql-database-or-azure-sql-managed-instance"></a>Kapazitätsfehler mit Azure SQL Datenbank oder Azure SQL Managed Instance beheben
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

In diesem Artikel erfahren Sie, wie Sie Kapazitätsfehler bei der Bereitstellung von Azure SQL Datenbank- oder Azure SQL Managed Instance-Ressourcen gewusst haben. 

## <a name="exceeded-quota"></a>Überschrittene Quote 

Wenn Sie beim Versuch, Ihre Azure SQL-Ressource bereitzustellen, auf eine der folgenden Fehlermeldungen stoßen, stellen Sie bitte [die Anforderung, Ihr Kontingent zu erhöhen](database/quota-increase-request.md): 

- `Server quota limit has been reached for this location. Please select a different location with lower server count.`
- `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx.`
- Während eines Wiegevorgangs kann der folgende Fehler auftreten:    
  `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx. `. 

## <a name="subscription-access"></a>Abonnementzugriff

Ihr Abonnement hat möglicherweise keinen Zugriff auf die Erstellung eines Servers in der ausgewählten Region, wenn Ihr Abonnement nicht beim SQL Resource Provider (RP) registriert wurde.  

Wenn Sie die folgenden Fehler sehen, registrieren Sie bitte [ Ihr Abonnement beim SQL-RP](#register-with-sql-rp):
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`


## <a name="enable-region"></a>Aktivieren Sie die Region 

Ihr Abonnement hat möglicherweise keinen Zugriff auf die Erstellung eines Servers in der ausgewählten Region, wenn diese Region nicht aktiviert wurde. Um dies zu beheben, stellen Sie eine [Support-Anforderung zur Aktivierung einer bestimmten Region](database/quota-increase-request.md#region) für Ihr Abonnement. 

Wenn Sie die folgenden Fehler sehen, reichen Sie ein Support-Ticket ein, um eine bestimmte Region zu aktivieren: 
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`



## <a name="register-with-sql-rp"></a>Registrierung bei SQL RP

Um Azure SQL-Ressourcen bereitzustellen, registrieren Sie Ihr Abonnement beim SQL-Ressourcenanbieter (RP). 

Sie können Ihr Abonnement über das Azure-Portal, [die Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps) registrieren. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Gehen Sie folgendermaßen vor, um Ihr Abonnement im Azure-Portal zu registrieren: 

1. Öffnen Sie das Azure-Portal, und wechseln Sie zu **Alle Dienste**.
1. Wechseln Sie zu **Abonnements**, und wählen Sie das gewünschte Abonnement aus.
1. Wählen Sie auf der Seite **Abonnements** unter **Einstellungen** die Option **Ressourcenanbieter** aus.
1. Geben Sie im Filter die Zeichenfolge **sql** ein, um die SQL-bezogenen Erweiterungen aufzurufen.
1. Wählen Sie **Registrieren**, **Neuregistrieren** oder **Entregistrieren** für den Anbieter **Microsoft.Sql**, je nach der gewünschten Aktion.

   ![Ändern des Anbieters](./media/capacity-errors-troubleshoot/register-with-sql-rp.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/bash)

Um Ihr Abonnement mit [der Azure CLI](/cli/azure/install-azure-cli) zu registrieren, führen Sie dieses Cmdlet aus:

```azurecli-interactive
# Register the SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMac 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Um Ihr Abonnement mit [Azure PowerShell](/powershell/azure/install-az-ps) zu registrieren, führen Sie dieses Cmdlet aus: 

```powershell-interactive
# Register the SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.Sql

```

---

## <a name="additional-provisioning-issues"></a>Weitere Bereitstellungsprobleme

Wenn Sie immer noch Probleme mit der Bereitstellung haben, öffnen Sie bitte eine **Region**-Zugriffsanforderung unter dem Support-Thema von SQL Datenbank und geben Sie die DTU oder vCores an, die Sie auf Azure SQL Datenbank oder Azure SQL Managed Instance verbrauchen möchten. 

## <a name="azure-program-regions"></a>Azure Programm-Regionen 

Azure Programmangebote (Azure Pass, Imagine, Azure für Studenten, MPN, BizSpark, BizSpark Plus, Microsoft für Startups / Sponsorship Offers, Visual Studio / MSDN) haben Zugang zu einer begrenzten Anzahl von Regionen. 

Wenn Ihr Abonnement Teil eines Azure-Programmangebots ist und Sie eine Anforderung für den Zugriff auf eine der folgenden Regionen stellen möchten, ziehen Sie stattdessen bitte eine andere Region in Betracht: 

_Australien, Mitte, Australien, Mitte 2, Australien, Südost, Brasilien, Südost, Kanada, Ost, China, Ost, China, Norden, China, Norden 2, Frankreich, Süd, Deutschland, Japan, West, JIO Indien, Mitte, JIO Indien, West, Korea, Süden, Norwegen, West, Südafrika, West, Südindien, Schweiz, West, Vereinigte Arabische Emirate, Mitte, Vereinigtes Königreich, West, US DoD, Mitte, US DoD, Ost, US Gov Arizona, US Gov Texas, West, Zentral-USA, Westindien._ 

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Anfrage übermittelt haben, wird sie überprüft. Sie erhalten eine Antwort entsprechend den Informationen, die Sie im Formular angegeben haben.

Weitere Informationen zu anderen Azure-Grenzwerten finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).

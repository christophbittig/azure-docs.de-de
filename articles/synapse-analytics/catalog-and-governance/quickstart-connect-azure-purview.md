---
title: Herstellen einer Verbindung mit einem Azure Purview-Konto 
description: Herstellen einer Verbindung zwischen einem Azure Purview-Konto und einem Synapse-Arbeitsbereich
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: 827f1a4cd518d33ea67749482349cad9d4540c82
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117385"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Schnellstart: Herstellen einer Verbindung zwischen einem Azure Purview-Konto und einem Synapse-Arbeitsbereich 


In dieser Schnellstartanleitung registrieren Sie ein Azure Purview-Konto in einem Synapse-Arbeitsbereich. Dank dieser Verbindung können Sie Azure Purview-Ressourcen ermitteln und mit ihnen über Synapse-Funktionen interagieren. 

Sie können die folgenden Aufgaben in Synapse ausführen: 
- Suchen von Purview-Objekten anhand von Schlüsselwörtern mithilfe des Suchfelds im oberen Bereich 
- Verstehen der Daten basierend auf den Metadaten, der [Herkunft](../../purview/catalog-lineage-user-guide.md) und den Anmerkungen 
- Verbinden dieser Daten mit Ihrem Arbeitsbereich mit verknüpften Diensten oder Integrationsdatasets 
- Analysieren dieser Datasets mit Synapse Apache Spark, Synapse SQL und Datenflüssen 

## <a name="prerequisites"></a>Voraussetzungen 
- [Azure Purview-Konto](../../purview/create-catalog-portal.md) 
- [Synapse-Arbeitsbereich](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Anmelden bei einem Synapse-Arbeitsbereich 

Navigieren Sie zu  [https://web.azuresynapse.net](https://web.azuresynapse.net), und melden Sie sich bei Ihrem Arbeitsbereich an. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Berechtigungen zum Herstellen einer Verbindung mit einem Azure Purview-Konto 

- Zum Herstellen einer Verbindung zwischen einem Azure Purview-Konto und einem Synapse-Arbeitsbereich benötigen Sie die Rolle **Mitwirkender** im Synapse-Arbeitsbereich (über das IAM im Azure-Portal) sowie Zugriff auf das entsprechende Azure Purview-Konto. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](../../purview/catalog-permissions.md).

## <a name="connect-an-azure-purview-account"></a>Herstellen einer Verbindung mit einem Azure Purview-Konto  

- Navigieren Sie im Synapse-Arbeitsbereich zu **Verwalten** -> **Azure Purview**. Wählen Sie **Connect to a Purview account** (Mit einem Purview-Konto verbinden) aus. 
- Sie können **Aus Azure-Abonnement** oder **Manuell eingeben** auswählen. Unter **Aus Azure-Abonnement** können Sie das Konto auswählen, auf das Sie Zugriff haben. 
- Nachdem die Verbindung hergestellt wurde, sollte der Name des Purview-Kontos auf der Registerkarte **Azure Purview-Konto** angezeigt werden. 
- Sie können über die Suchleiste oben in der Mitte des Synapse-Arbeitsbereichs nach Daten suchen. 

## <a name="nextsteps"></a>Nächste Schritte 

[Registrieren und Überprüfen von Azure Synapse Analytics](../../purview/register-scan-azure-synapse-analytics.md)

[Entdecken, Verbinden und Untersuchen von Daten in Synapse mithilfe von Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   

[Verbinden von Azure Data Factory und Azure Purview](../../purview/how-to-link-azure-data-factory.md)

[Verbinden von Azure Data Share und Azure Purview](../../purview/how-to-link-azure-data-share.md)

[Die Herkunft von Power BI in Azure Purview übernehmen](../../purview/how-to-lineage-powerbi.md)

---
title: Verbinden von Azure Analysis Services mit Power BI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Power BI eine Verbindung mit einem Azure Analysis Services-Server herstellen. Nachdem die Verbindung hergestellt ist, können die Benutzer die Modelldaten untersuchen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/30/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a956d5e3be0a5f92e22dcd1536339ff5244e19dc
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2021
ms.locfileid: "113213349"
---
# <a name="connect-with-power-bi"></a>Herstellen einer Verbindung mit Power BI

Sobald Sie in Azure einen Server erstellt, und ein tabellarisches Modell bereitgestellt haben, können Benutzer in Ihrer Organisation beginnen, eine Verbindung herzustellen und Daten zu durchsuchen. 

> [!NOTE]
> Wenn Sie ein Power BI Desktop-Modell im Power BI-Dienst veröffentlichen, stellen Sie auf dem Azure Analysis Services-Server sicher, dass die Servereigenschaft „Beachtung der Groß-/Kleinschreibung bei Sortierung“ nicht ausgewählt ist (Standardeinstellung). Die Eigenschaft „Beachtung der Groß-/Kleinschreibung bei Sortierung“ kann mithilfe von SQL Server Management Studio festgelegt werden.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Herstellen einer Verbindung in Power BI Desktop

1. Klicken Sie in Power BI Desktop auf **Daten abrufen** > **Azure** > **Azure Analysis Services-Datenbank**.

2. Geben Sie unter **Server** den Namen des Servers ein. Geben Sie unbedingt die vollständige URL an, beispielsweise „asazure://westcentralus.asazure.windows.net/advworks“.

3. Wenn Sie den Namen der Datenbank für tabellarische Modelle oder die Perspektive kennen, mit der Sie sich verbinden wollen, fügen Sie ihn unter **Datenbanken** ein. Andernfalls können Sie dieses Feld leer lassen und später eine Datenbank oder Perspektive auswählen.

4. Wählen Sie eine Verbindungsoption aus, und klicken Sie dann auf **Verbinden**. 

    Sowohl **Live verbinden** als auch **Importieren** wird unterstützt. Es wird jedoch empfohlen, Liveverbindungen zu verwenden, da beim Importmodus einige Einschränkungen gelten. Insbesondere kann während des Imports die Serverleistung beeinträchtigt sein.
    
    Wenn Sie über ein Power BI-Modell im [gemischten Speichermodus](/power-bi/transform-model/desktop-composite-models) verfügen,wird die Option **Live verbinden** durch die Option **[DirectQuery](/power-bi/connect-data/desktop-directquery-datasets-azure-analysis-services)** ersetzt. Liveverbindungen werden auch automatisch auf DirectQuery aktualisiert, wenn das Modell vom Import- in den gemischten Speichermodus gewechselt wird.

5. Wenn Sie aufgefordert werden, Ihre Anmeldeinformationen einzugeben, klicken Sie auf **Microsoft-Konto** und dann auf **Anmelden**. 

    :::image type="content" source="media/analysis-services-connect-pbi/aas-sign-in.png" alt-text="Anmelden bei Azure AS":::

   > [!NOTE]
   > Die Windows-Authentifizierung und die Standardauthentifizierung werden nicht unterstützt. 

6. Erweitern Sie in **Navigator** den Server, wählen Sie das Modell oder die Perspektive aus, mit dem/der Sie eine Verbindung herstellen möchten, und klicken Sie dann auf **Verbinden**. Klicken Sie auf ein Modell oder eine Perspektive, um alle Objekte für diese Ansicht anzuzeigen.

    Das Modell wird in Power BI Desktop mit einem leeren Bericht in der Berichtsansicht geöffnet. In der Felderliste werden alle nicht ausgeblendeten Modellobjekte angezeigt. Der Verbindungsstatus wird in der unteren rechten Ecke angezeigt.

## <a name="connect-in-power-bi-service"></a>Herstellen einer Verbindung in Power BI (Dienst)

1. Erstellen Sie eine Power BI Desktop-Datei, die über eine Liveverbindung zu Ihrem Modell auf dem Server verfügt.
2. Klicken Sie in [Power BI](https://powerbi.microsoft.com) auf **Daten abrufen** > **Dateien**, suchen Sie Ihre PBIX-Datei, und wählen Sie sie dann aus.

## <a name="request-memory-limit"></a>Anforderungsspeicherlimit

Um die Leistung des Systems zu schützen, wird ein Arbeitsspeicherlimit für alle Abfragen erzwungen, die von Power BI-Berichten an Azure Analysis Services ausgegeben werden, unabhängig vom auf dem Azure Analysis Services-Server konfigurierten [Abfragespeicherlimit](/analysis-services/server-properties/memory-properties?view=azure-analysis-services-current&preserve-view=true). Bei zu speicherintensiven Abfragen sollten Benutzer in Erwägung ziehen, die Abfrage oder die zugehörigen Berechnungen zu vereinfachen.

|Abfragetyp| Anforderungsspeicherlimit |
|-----------------------------------------------------------|----------------------|
| Herstellen einer Liveverbindung von Power BI                            | 10 GB  |
| DirectQuery von Power BI-Bericht im freigegebenen Arbeitsbereich  | 1 GB   |
| DirectQuery von Power BI-Bericht im Premium-Arbeitsbereich | 10 GB  |
| Power BI Q&A | 100 MB |

## <a name="see-also"></a>Weitere Informationen
[Herstellen einer Verbindung mit Azure Analysis Services](analysis-services-connect.md)   
[Clientbibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

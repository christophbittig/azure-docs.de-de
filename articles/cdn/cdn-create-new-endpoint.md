---
title: 'Schnellstart: Erstellen eines Azure CDN-Profils und -Endpunkts'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure CDN durch Erstellen eines neuen CDN-Profils und CDN-Endpunkts aktivieren.
author: duongau
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: de24b2e9676082da6980c59e696781fc6939f29f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423506"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Schnellstart: Erstellen eines Azure CDN-Profils und -Endpunkts

In dieser Schnellstartanleitung aktivieren Sie Azure Content Delivery Network (CDN), indem Sie ein neues CDN-Profil erstellen. Dabei handelt es sich um eine Sammlung von mehreren CDN-Endpunkten. Nach der Erstellung eines Profils und eines Endpunkts können Sie mit der Bereitstellung von Inhalten für Ihre Kunden beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ein Azure Storage-Konto mit dem Namen *cdnstorageacct123*, das Sie für den Hostnamen des Ursprungs verwenden. Informationen zu dieser Anforderung finden Sie unter [Schnellstart: Integrieren eines Azure-Speicherkontos in Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Erstellen eines neuen CDN-Endpunkts

Nachdem Sie ein CDN-Profil erstellt haben, erstellen Sie damit einen Endpunkt.

1. Wählen Sie auf Ihrem Dashboard im Azure-Portal das erstellte CDN-Profil aus. Falls Sie es nicht finden, können Sie entweder die Ressourcengruppe öffnen, in der Sie es erstellt haben, oder die Suchleiste oben im Portal verwenden, den Profilnamen eingeben und das Profil aus den Ergebnissen auswählen.
   
1. Wählen Sie auf der Seite „CDN-Profil“ die Option **+ Endpunkt** aus.
   
    ![CDN-Profil](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Der Bereich **Endpunkt hinzufügen** wird angezeigt.

3. Geben Sie die folgenden Einstellungswerte ein:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Name** | Geben Sie *cdn-endpoint-123* als Endpunkthostnamen ein. Dieser Name muss in Azure global eindeutig sein. Sollte er bereits verwendet werden, geben Sie einen anderen Namen ein. Dieser Name wird für den Zugriff auf Ihre zwischengespeicherten Ressourcen in der Domäne „ _&lt;Endpunktname&gt;_ .azureedge.net“ verwendet.|
    | **Ursprungstyp** | Wählen Sie **Speicher**. | 
    | **Hostname des Ursprungs** | Wählen Sie in der Dropdownliste den Hostnamen des von Ihnen verwendeten Azure Storage-Kontos aus, etwa *cdnstorageacct123.blob.core.windows.net*. |
    | **Ursprungspfad** | Lassen Sie dieses Feld leer. |
    | **Header des Ursprungshosts** | Übernehmen Sie den Standardwert (dies ist der Hostname des Ursprungs). |  
    | **Protokoll** | Übernehmen Sie den ausgewählten Standardoptionen **HTTP** und **HTTPS**. |
    | **Ursprungsport** | Behalten Sie die Standardportwerte bei. | 
    | **Optimiert für** | Behalten Sie die Standardauswahl **Allgemeine Webbereitstellung** bei. |

    ![Bereich „Endpunkt hinzufügen“](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Wählen Sie **Hinzufügen**, um den neuen Endpunkt zu erstellen. Der erstellte Endpunkt wird in der Liste mit den Endpunkten für das Profil angezeigt.
    
   ![CDN-Endpunkt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Die Dauer für die Verteilung des Endpunkts hängt vom Tarif ab, den Sie bei der Erstellung des Profils ausgewählt haben. Bei **Akamai Standard** wird der Vorgang in der Regel innerhalb von einer Minute abgeschlossen, bei **Microsoft Standard** in zehn Minuten und bei **Verizon Standard** und **Verizon Premium** in bis zu 30 Minuten.

> [!NOTE]
> Bei *Verizon-CDN-Endpunkten* werden alle über das zusätzliche Verizon-Portal konfigurierten Ressourcen bereinigt, wenn ein Endpunkt aus irgendeinem Grund **deaktiviert** oder **angehalten** wird. Diese Konfigurationen können nicht durch das Neustarten des Endpunkts automatisch wiederhergestellt werden. Sie müssen diese Konfigurationsänderungen erneut vornehmen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie ein CDN-Profil und einen Endpunkt in einer Ressourcengruppe erstellt. Speichern Sie diese Ressourcen, falls Sie die [nächsten Schritte](#next-steps) ausführen und erfahren möchten, wie Sie Ihrem Endpunkt eine benutzerdefinierte Domäne hinzufügen. Sollten Sie die Ressourcen dagegen nicht mehr benötigen, können Sie die Ressourcengruppe mit den Ressourcen löschen, um weitere Kosten zu vermeiden:

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Ressourcengruppen** und dann **CDNQuickstart-rg** aus.

2. Wählen Sie auf der Seite **Ressourcengruppe** die Option **Ressourcengruppe löschen** aus, geben Sie *CDNQuickstart-rg* in das Textfeld ein, und wählen Sie anschließend **Löschen** aus. Dadurch werden die Ressourcengruppe, das Profil und der Endpunkt gelöscht, die Sie im Rahmen dieser Schnellstartanleitung erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen von Azure CDN zu einer Azure App Service-Web-App](cdn-add-to-web-app.md)

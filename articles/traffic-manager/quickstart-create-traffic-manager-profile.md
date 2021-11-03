---
title: 'Schnellstart: Erstellen eines Profils für die Hochverfügbarkeit von Anwendungen: Azure-Portal – Azure Traffic Manager'
description: In diesem Schnellstartartikel wird beschrieben, wie Sie mithilfe des Azure-Portals ein Traffic Manager-Profil erstellen, um eine hochverfügbare Webanwendung zu entwickeln.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mode-portal
ms.openlocfilehash: be0061fb42a391506adf7c944e60836e14d6cb8b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003464"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Schnellstart: Erstellen eines Traffic Manager-Profils im Azure-Portal

In dieser Schnellstartanleitung wird beschrieben, wie Sie ein Traffic Manager-Profil erstellen, mit dem die Hochverfügbarkeit für Ihre Webanwendung sichergestellt wird.

In dieser Schnellstartanleitung werden zwei Instanzen einer Webanwendung beschrieben. Jede Instanz wird in einer anderen Azure-Region ausgeführt. Sie erstellen ein Traffic Manager-Profil basierend auf der [Endpunktpriorität](traffic-manager-routing-methods.md#priority-traffic-routing-method). Das Profil leitet den Benutzerdatenverkehr an den primären Standort, an dem die Webanwendung ausgeführt wird. Die Webanwendung wird von Traffic Manager ständig überwacht. Wenn der primäre Standort nicht verfügbar ist, erfolgt automatisch ein Failover zum Sicherungsstandort.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagramm: Traffic Manager-Bereitstellungsumgebung" border="false":::

Wenn Sie kein Azure-Abonnement besitzen, können Sie jetzt ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie zwei Instanzen einer Webanwendung, die in zwei unterschiedlichen Azure-Regionen bereitgestellt werden (*USA, Osten* und *Europa, Westen*). Jede dient jeweils als primärer bzw. Failoverendpunkt für Traffic Manager.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie oben links auf dem Bildschirm die Option **Ressource erstellen** aus. Suchen Sie nach **Web-App**, und wählen Sie **Erstellen** aus.

1. Geben Sie unter **Web-App erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein (bzw. wählen Sie sie aus):

    | Einstellung                 | Wert |
    | ---                     | --- |
    | Subscription            | Wählen Sie Ihr Abonnement aus. |    
    | Resource group          | Wählen Sie **Neu erstellen** aus, und geben Sie *myResourceGroupTM1* in das Textfeld ein.|
    | Name                    | Geben Sie unter **Name** einen eindeutigen Namen für Ihre Web-App ein. In diesem Beispiel wird *myWebAppEastUS* verwendet. |
    | Veröffentlichen                 | Wählen Sie **Code** aus. |
    | Laufzeitstapel           | Wählen Sie **ASP.NET V4.7** aus. |
    | Betriebssystem        | Wählen Sie **Windows** aus. |
    | Region                  | Wählen Sie **USA, Osten** aus. |
    | Windows-Plan            | Wählen Sie **Neu erstellen** aus, und geben Sie *myAppServicePlanEastUS* in das Textfeld ein. |
    | SKU und Größe            | Wählen Sie **Standard S1, 100 ACU insgesamt, 1,75 GB Arbeitsspeicher** aus. |
   
1. Wählen Sie die Registerkarte **Überwachung** aus, oder wählen Sie **Weiter: Überwachung** aus.  Legen Sie unter **Überwachung****Application Insights** > **Application Insights aktivieren** auf **Nein** fest.

1. Klicken Sie auf **Überprüfen und erstellen**.

1. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.  Wenn die Bereitstellung der Web-App erfolgreich war, wird eine Standardwebsite erstellt.

1. Führen Sie die Schritte 1 bis 6 aus, um eine zweite Web-App namens *myWebAppWestEurope* zu erstellen. Der Name der **Ressourcengruppe** lautet *myResourceGroupTM2*, die **Region** ist *Europa, Westen* und der **App Service-Plan** hat den Namen **myAppServicePlanWestEurope**. Alle anderen Einstellungen sind identisch mit denen für *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Erstellen Sie ein Traffic Manager-Profil, das Benutzerdatenverkehr basierend auf der Endpunktpriorität weiterleitet.

1. Wählen Sie oben links auf dem Bildschirm die Option **Ressource erstellen** aus. Suchen Sie dann nach **Traffic Manager-Profil**, und wählen Sie **Erstellen** aus.
1. Geben Sie unter **Traffic Manager-Profil erstellen** die folgenden Einstellungen ein (bzw. wählen Sie sie aus):

    | Einstellung | Wert |
    | --------| ----- |
    | Name | Geben Sie einen eindeutigen Namen für Ihr Traffic Manager-Profil ein.|
    | Routingmethode | Wählen Sie **Priorität**.|
    | Abonnement | Wählen Sie das Abonnement aus, auf das das Traffic Manager-Profil angewendet werden soll. |
    | Ressourcengruppe | Wählen Sie *myResourceGroupTM1* aus.|
    | Standort |Diese Einstellung bezieht sich auf den Standort der Ressourcengruppe. Sie wirkt sich nicht auf das Traffic Manager-Profil aus, das global bereitgestellt wird.|

1. Wählen Sie **Erstellen** aus.

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten

Fügen Sie die Website in der Region *USA, Osten* als primären Endpunkt für das Routing des gesamten Benutzerdatenverkehrs hinzu. Fügen Sie die Website in *Europa, Westen* als Failoverendpunkt hinzu. Wenn der primäre Endpunkt nicht verfügbar ist, wird der Datenverkehr automatisch an den Failoverendpunkt weitergeleitet.

1. Geben Sie in der Suchleiste des Portals den Namen des Traffic Manager-Profils ein, das Sie im vorherigen Abschnitt erstellt haben.
1. Wählen Sie das Profil in den Suchergebnissen aus.
1. Wählen Sie im **Traffic Manager-Profil** im Abschnitt **Einstellungen** die Option **Endpunkte** und dann **Hinzufügen**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Screenshot: Endpunkteinstellungen im Traffic Manager-Profil":::

1. Geben Sie die folgenden Einstellungen ein (bzw. wählen Sie sie aus):

    | Einstellung | Wert |
    | ------- | ------|
    | type | Wählen Sie **Azure-Endpunkt**. |
    | Name | Geben Sie *myPrimaryEndpoint* ein. |
    | Zielressourcentyp | Wählen Sie **App Service**. |
    | Zielressource | Wählen Sie **App Service auswählen** > **USA, Osten**. |
    | Priorität | Wählen Sie **1**. Der gesamte Datenverkehr wird an diesen Endpunkt gesendet, wenn er fehlerfrei ist. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Screenshot: Ort zum Hinzufügen eines Endpunkts zu Ihrem Traffic Manager-Profil":::
    
1. Klicken Sie auf **OK**.
1. Wiederholen Sie die Schritte 3 und 4 mit den folgenden Einstellungen, um einen Failoverendpunkt für Ihre zweite Azure-Region zu erstellen:

    | Einstellung | Wert |
    | ------- | ------|
    | type | Wählen Sie **Azure-Endpunkt**. |
    | Name | Geben Sie *myFailoverEndpoint* ein. |
    | Zielressourcentyp | Wählen Sie **App Service**. |
    | Zielressource | Wählen Sie **App Service auswählen** > **Europa, Westen**. |
    | Priorität | Wählen Sie **2**. Der gesamte Datenverkehr wird an diesen Failoverendpunkt geleitet, wenn der primäre Endpunkt fehlerhaft ist. |

1. Klicken Sie auf **OK**.

Wenn Sie das Hinzufügen der beiden Endpunkte abgeschlossen haben, werden sie im **Traffic Manager-Profil** angezeigt. Beachten Sie, dass der Überwachungsstatus jetzt **Online** lautet.

## <a name="test-traffic-manager-profile"></a>Testen des Traffic Manager-Profils

In diesem Abschnitt überprüfen Sie den Domänennamen Ihres Traffic Manager-Profils. Außerdem konfigurieren Sie den primären Endpunkt so, dass er nicht verfügbar ist. Abschließend können Sie sehen, dass die Web-App weiterhin verfügbar ist. Dies liegt daran, dass Traffic Manager den Datenverkehr an den Failoverendpunkt sendet.

### <a name="check-the-dns-name"></a>Überprüfen des DNS-Namens

1. Suchen Sie in der Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie im vorhergehenden Abschnitt erstellt haben.
1. Wählen Sie das Traffic Manager-Profil aus. Die **Übersicht** wird angezeigt.
1. Unter **Traffic Manager-Profil** wird der DNS-Name Ihres neu erstellten Traffic Manager-Profils angezeigt.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Screenshot: Ihr Traffic Manager-DNS-Name":::

### <a name="view-traffic-manager-in-action"></a>Anzeigen von Traffic Manager in Aktion

1. Geben Sie in einem Webbrowser den DNS-Namen Ihres Traffic Manager-Profils an, um die Standardwebsite Ihrer Web-App anzuzeigen.

    > [!NOTE]
    > In diesem Schnellstartszenario werden alle Anforderungen an den primären Endpunkt weitergeleitet. Es ist **Priorität 1** festgelegt.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Screenshot: Webseite zum Bestätigen der Verfügbarkeit des Traffic Manager-Profils":::

1. Deaktivieren Sie Ihren primären Standort, wenn Sie das Traffic Manager-Failover in Aktion sehen möchten:
    1. Wählen Sie auf der Seite mit dem Traffic Manager-Profil im Abschnitt **Übersicht** die Option **myPrimaryEndpoint**.
    1. Wählen Sie unter *myPrimaryEndpoint* die Option **Deaktiviert** > **Speichern**.
    1. Schließen Sie **myPrimaryEndpoint**. Sie sehen, dass der Status jetzt **Deaktiviert** lautet.
1. Kopieren Sie den DNS-Namen des Traffic Manager-Profils aus dem vorherigen Schritt, um die Website in einer neuen Browsersitzung anzuzeigen.
1. Vergewissern Sie sich, dass die Web-App weiterhin verfügbar ist.

Da der primäre Endpunkt nicht verfügbar ist, wurden Sie an den Failoverendpunkt weitergeleitet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppen, Webanwendungen und alle dazugehörigen Ressourcen, wenn Sie fertig sind. Wählen Sie hierzu jedes einzelne Element in Ihrem Dashboard aus, und klicken Sie dann oben auf der Seite auf die Option **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Traffic Manager-Profil erstellt. Damit können Sie Benutzerdatenverkehr für Webanwendungen mit Hochverfügbarkeit weiterleiten. Weitere Informationen zum Weiterleiten des Datenverkehrs finden Sie in den Tutorials zu Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager-Tutorials](tutorial-traffic-manager-improve-website-response.md)

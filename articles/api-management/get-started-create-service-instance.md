---
title: 'Schnellstart: Erstellen einer Azure API Management-Instanz'
description: Hier erfahren Sie, wie Sie eine neue Azure API Management-Dienstinstanz über das Azure-Portal erstellen.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: danlep
ms.openlocfilehash: c2a822147b06cfdd950d12bd1fa2d5c1c583f22f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660783"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Schnellstart: Erstellen einer neuen Azure API Management-Dienstinstanz mithilfe des Azure-Portals

Azure API Management (APIM) unterstützt Organisationen beim Veröffentlichen von APIs für externe Entwickler, Partnerentwickler und interne Entwickler, um das volle Potenzial von Daten und Diensten ausschöpfen zu können. API Management bietet die Kernkompetenzen zur Sicherstellung eines erfolgreichen API-Programms in Form von Entwicklerengagement, geschäftlichen Erkenntnissen, Analysen, Sicherheit und Schutz. Mit APIM können Sie moderne API-Gateways für vorhandene Back-End-Dienste, die an einem beliebigen Ort gehostet werden, erstellen und verwalten. Weitere Informationen finden Sie in der [Übersicht](api-management-key-concepts.md).

In dieser Schnellstartanleitung werden die Schritte zum Erstellen einer neuen API Management-Instanz mit dem Azure-Portal beschrieben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

:::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="API Management-Instanz":::

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-new-service"></a>Erstellen eines neuen Diensts

1. Wählen Sie im Menü des Azure-Portals die Option **Ressource erstellen** aus. Die Option **Ressource erstellen** kann auch auf der **Startseite** von Azure ausgewählt werden. 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Auswählen von „Ressource erstellen“":::

   
1. Klicken Sie auf der Seite **Neu** auf **Integration** > **API Management**.

   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-02.png" alt-text="Neue Azure API Management-Instanz":::
   
1. Geben Sie auf der Seite **API Management-Dienst** Einstellungen ein.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Neue Instanz":::
   
   | Einstellung                 | Beschreibung   |                                                                     
   |-------------------------|-----------------------------------------------|
   | **Name**                | Ein eindeutiger Name für Ihren API Management-Dienst. Den Namen können Sie später nicht mehr ändern. Der Dienstname wird verwendet, um auf den Dienst und die entsprechende Azure-Ressource zu verweisen. <br/> Der Name des Diensts wird verwendet, um einen Standarddomänennamen in der Form *\<name\>.azure-api.net* zu generieren. Wenn Sie einen benutzerdefinierten Domänennamen verwenden möchten, lesen Sie [Configure a custom domain name](configure-custom-domain.md) (Konfigurieren eines benutzerdefinierten Domänennamens). |
   | **Abonnement**          | Das Abonnement, unter dem diese neue Dienstinstanz erstellt wird.   |
   | **Ressourcengruppe**      |  Wählen Sie eine neue oder vorhandene Ressourcengruppe aus. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. |
   | **Location**          | Wählen Sie aus den verfügbaren Standorten des API Management-Diensts eine geografische Region in Ihrer Nähe aus. | 
   | **Name der Organisation**   | Der Name Ihrer Organisation. Dieser Name wird an verschiedenen Stellen verwendet, u.a. im Titel des Entwicklerportals und im Absender von Benachrichtigungs-E-Mails. |                                                         
   | **Administrator-E-Mail** | Die E-Mail-Adresse, an die alle Benachrichtigungen von **API Management** gesendet werden.   |  
   | **Preisstufe**        | Legen Sie den Tarif **Entwickler** fest, um den Dienst zu bewerten. Dieser Tarif ist nicht für die Produktion bestimmt. Weitere Informationen zum Skalieren der API Management-Tarife finden Sie unter [Upgrade and scale an API Management instance](upgrade-and-scale.md) (Upgraden und Skalieren einer API Management-Instanz). |

3. Klicken Sie auf **Erstellen**.

    > [!TIP]
    > Es dauert in der Regel zwischen 30 und 40 Minuten, einen API Management-Dienst mit diesem Tarif zu erstellen und zu aktivieren. Durch Auswählen von **An Dashboard anheften** ist ein neu erstellter Dienst einfacher zu finden.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Sehen Sie sich auf der Seite **Übersicht** die Eigenschaften Ihres Diensts an.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="API Management-Instanz":::

Sobald Ihre API Management-Dienstinstanz online ist, können Sie sie verwenden. Beginnen Sie mit dem Tutorial [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit den folgenden Schritten entfernen:

1. Suchen Sie im Azure-Portal nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus. Die Option **Ressourcengruppen** kann auch auf der **Startseite** von Azure ausgewählt werden. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Ressourcengruppennavigation":::

1. Wählen Sie auf der Seite **Ressourcengruppen** Ihre Ressourcengruppe aus.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Auswählen der Ressourcengruppe":::

1. Wählen Sie auf der Ressourcengruppenseite die Option **Ressourcengruppe löschen** aus. 
   
1. Geben Sie den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-03.png" alt-text="Ressourcengruppe löschen":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md)

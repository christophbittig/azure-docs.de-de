---
title: 'Schnellstart: Erstellen eines Analysis Services-Servers im Azure-Portal | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals eine Azure Analysis Services-Serverinstanz erstellen.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 10/12/2021
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom: mode-portal
ms.openlocfilehash: 6a5c6f105b15d65cb13ef61e2416fa27f06fac34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060115"
---
# <a name="quickstart-create-a-server---portal"></a>Schnellstart: Erstellen eines Analysis Services-Servers im Azure-Portal

Diese Schnellstartanleitung erläutert, wie eine Analysis Services-Serverressource in Ihrem Azure-Abonnement mithilfe des Portals erstellt wird.

## <a name="prerequisites"></a>Voraussetzungen 

* **Azure-Abonnement:** Besuchen Sie die Webseite [Kostenlose Azure-Testversion](https://azure.microsoft.com/offers/ms-azr-0044p/), und erstellen Sie ein Konto.
* **Azure Active Directory**: Ihr Abonnement muss einem Azure Active Directory-Mandanten zugeordnet sein. Und Sie müssen bei Azure mit einem Konto in diesem Azure Active Directory angemeldet sein. Weitere Informationen finden Sie unter [Authentifizierung und Benutzerberechtigungen](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an. 

[Anmelden beim Portal](https://portal.azure.com)


## <a name="create-a-server"></a>Erstellen eines Servers

1. Klicken Sie auf **+ Ressource erstellen** > **Analysen** > **Analysis Services**.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. Füllen Sie in **Analysis Services** die erforderlichen Felder aus, und klicken Sie dann auf **Erstellen**.
   
   * **Servername:** Geben Sie einen eindeutigen Namen ein, mit dem auf den Server verwiesen wird. Der Servername muss mit einem Kleinbuchstaben beginnen und zwischen 3 und 128 Kleinbuchstaben und Ziffern enthalten. Leer- und Sonderzeichen sind nicht zulässig.
   * **Abonnement**: Wählen Sie das Abonnement aus, das diesem Server zugeordnet wird.
   * **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine bereits vorhandene Ressourcengruppe aus. Ressourcengruppen sind darauf ausgelegt, Sie beim Verwalten einer Sammlung von Azure-Ressourcen zu unterstützen. Weitere Informationen finden Sie unter [Ressourcengruppen](../azure-resource-manager/management/overview.md).
   * **Standort:** An diesem Standort des Azure-Rechenzentrums wird der Server gehostet. Wählen Sie einen Standort in der Nähe Ihrer größten Benutzerbasis aus.
   * **Tarif:** Wählen Sie einen Tarif aus. Wenn Sie Tests durchführen und die Beispielmodelldatenbank installieren möchten, wählen Sie den kostenlosen Tarif **D1** aus. Weitere Informationen finden Sie unter [Azure Analysis Services – Preise](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administrator**: Dies ist standardmäßig das Konto, mit dem Sie angemeldet werden. Sie können ein anderes Konto aus Ihrem Azure Active Directory auswählen.
   * **Einstellung „Sicherungsspeicher“** : Optional. Wenn Sie bereits über ein [Speicherkonto](../storage/common/storage-introduction.md), verfügen, können Sie es als Standardkonto für die Sicherung der Modelldatenbank angeben. Sie können später auch Einstellungen zum [Sichern und Wiederherstellen](analysis-services-backup.md) angeben.
   * **Speicherschlüssel-Ablaufdatum**: Optional. Geben Sie einen Ablaufzeitraum für den Speicherschlüssel an.

Das Erstellen des Servers dauert normalerweise weniger als eine Minute. Wenn Sie **Add to Portal** (Zu Portal hinzufügen) ausgewählt haben, navigieren Sie zu Ihrem Portal, um den neuen Server anzuzeigen. Oder navigieren Sie zu **Alle Dienste** > **Analysis Services**, um zu überprüfen, ob der Server bereit ist. Server unterstützen tabellarische Modelle mit dem Kompatibilitätsgrad 1200 oder höher. Der Modellkompatibilitätsgrad wird in Visual Studio oder SSMS angegeben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie Ihren Server, wenn Sie ihn nicht mehr benötigen. Klicken Sie auf der Seite **Übersicht** Ihres Servers auf **Löschen**. 

 ![Cleanup](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie gelernt, wie Sie einen Server in Ihrem Azure-Abonnement erstellen. Da Sie nun über einen Server verfügen, können Sie durch Konfigurieren einer (optionalen) Serverfirewall zur Sicherheit beitragen. Außerdem können Sie Ihrem Server direkt über das Portal ein einfaches Beispieldatenmodell hinzufügen. Ein Beispielmodell ist hilfreich, wenn Sie sich mit dem Konfigurieren von Modelldatenbankrollen und dem Testen von Clientverbindungen vertraut machen. Fahren Sie mit dem Tutorial zum Hinzufügen eines Beispielmodells fort, um mehr zu erfahren.

> [!div class="nextstepaction"]
> [Schnellstart: Konfigurieren der Serverfirewall – Portal](analysis-services-qs-firewall.md)   

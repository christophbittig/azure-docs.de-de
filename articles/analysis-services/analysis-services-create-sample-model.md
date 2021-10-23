---
title: 'Tutorial: Hinzufügen eines Beispielmodells – Azure Analysis Services | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie ein Beispielmodell in Azure Analysis Services hinzufügen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2949fe4e739e1afb7e41221675c90611d1ddb72e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000667"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Tutorial: Hinzufügen eines Beispielmodells über das Portal

In diesem Tutorial fügen Sie eine tabellarische Adventure Works-Modelldatenbank zu Ihrem Server hinzu. Das Beispielmodell ist eine vollständige Version des Beispieldatenmodells für Internetverkäufe in Adventure Works (1200). Ein Beispielmodell eignet sich zum Testen der Modellverwaltung, zum Herstellen einer Verbindung mit Tools und Clientanwendungen und zum Abfragen von Modelldaten. In diesem Tutorial werden das [Azure-Portal](https://portal.azure.com) und [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) für folgende Zwecke verwendet: 

> [!div class="checklist"]
> * Hinzufügen eines vollständigen tabellarischen Beispieldatenmodells zu einem Server 
> * Herstellen einer Verbindung mit dem Modell mit SSMS

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Einen Azure Analysis Services-Server. Weitere Informationen finden Sie unter [Erstellen eines Analysis Services-Servers im Azure-Portal](analysis-services-create-server.md).
- Serveradministratorberechtigungen
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Portal](https://portal.azure.com/) an.

## <a name="add-a-sample-model"></a>Hinzufügen eines Beispielmodells

1. Klicken Sie auf dem Server unter **Übersicht** auf **Neues Modell**.

    ![Erstellen einer Beispieltabelle](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. Vergewissern Sie sich unter **Neues Modell** > **Datenquelle auswählen**, dass **Beispieldaten** ausgewählt ist, und klicken Sie dann auf **Hinzufügen**.

    ![Auswählen des neuen Modells](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Vergewissern Sie sich unter **Übersicht**, dass das Beispielmodell `adventureworks` hinzugefügt wurde.

    ![Auswählen der Beispieldaten](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ihr Beispielmodell verwendet Cachespeicherressourcen. Wenn Sie Ihr Beispielmodell nicht zum Testen verwenden, sollten Sie es von Ihrem Server entfernen.

In diesen Schritten wird beschrieben, wie Sie ein Modell mithilfe von SSMS auf einem Server löschen.

1. Klicken Sie in SSMS im **Objekt-Explorer** auf **Verbinden** > **Analysis Services**.

2. Fügen Sie unter **Mit Server verbinden** den Servernamen ein, wählen Sie unter **Authentifizierung** die Option **Active Directory – universell mit MFA-Unterstützung** aus, geben Sie Ihren Benutzernamen ein, und klicken Sie dann auf **Verbinden**.

    ![Anmelden](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die Beispieldatenbank `adventureworks`, und klicken Sie dann auf **Löschen**.

    ![Löschen der Beispieldatenbank](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Nächste Schritte 

In diesem Tutorial haben Sie gelernt, wie Sie Ihrem Server ein einfaches Beispielmodell hinzufügen. Sie besitzen nun eine Modelldatenbank und können über SQL Server Management Studio eine Verbindung damit herstellen und Benutzerrollen hinzufügen. Weitere Informationen finden Sie im nächsten Tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren von Serveradministrator- und Benutzerrollen](tutorials/analysis-services-tutorial-roles.md)
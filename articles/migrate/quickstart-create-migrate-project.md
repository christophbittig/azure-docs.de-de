---
title: Schnellstartanleitung zum Erstellen eines Azure Migrate-Projekts mithilfe einer Azure Resource Manager-Vorlage
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) ein Azure Migrate-Projekt erstellen.
ms.date: 04/23/2021
author: rahulg1190
manager: bsiva
ms.author: rahugup
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: e128a9dda0cbf6bfbda4a4918ce9a4538e5fcbdb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084575"
---
# <a name="quickstart-create-an-azure-migrate-project-using-an-arm-template"></a>Schnellstartanleitung: Erstellen eines Azure Migrate-Projekts mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) ein Azure Migrate-Projekt erstellen. Azure Migrate verfügt über einen zentralisierten Hub für die Bewertung und die Migration von lokalen Servern sowie der lokalen Infrastruktur, Anwendungen und Daten zu Azure. Azure Migrate unterstützt die Bewertung und Migration von lokalen VMware-VMs, Hyper-V-VMs, physischen Servern, anderen virtualisierten VMs, Datenbanken, Web-Apps und virtuellen Desktops.

Mit dieser Vorlage wird ein Azure Migrate-Projekt erstellt, das weiter zum Bewerten und Migrieren Ihrer lokalen Server, Infrastruktur, Anwendungen und Daten in Azure verwendet wird.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie kein aktives Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/migrate-project-create/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.migrate/migrate-project-create/azuredeploy.json":::

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Zum Bereitstellen der Vorlage sind das **Abonnement**, die **Ressourcengruppe**, der **Projektname** und der **Standort** erforderlich.

1. Wählen Sie **Bereitstellung in Azure** aus, um sich bei Azure anzumelden und die Vorlage zu öffnen.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein:

   :::image type="content" source="media/quickstart-create-migrate-project-template/create-migrate-project.png" alt-text="Vorlage zum Erstellen eines Azure Migrate-Projekts":::

   - **Abonnement**: Wählen Sie Ihr Azure-Abonnement.
   - **Ressourcengruppe**: Wählen Sie eine vorhandene Gruppe aus, oder wählen Sie die Option **Neu erstellen** aus, um eine Gruppe hinzuzufügen.
   - **Region**: Für diese Option wird standardmäßig der Standort der Ressourcengruppe festgelegt, und die Option ist nach der Auswahl einer Ressourcengruppe nicht mehr verfügbar.
   - **Name des Migrationsprojekts**: Geben Sie einen Namen für den Tresor an.
   - **Standort**: Wählen Sie den Standort aus, an dem Sie das Azure Migrate-Projekt und seine Ressourcen bereitstellen möchten.

3. Klicken Sie auf die Schaltfläche **Überprüfen und erstellen**, um die Bereitstellung zu starten.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Verwenden Sie das Azure-Portal, um zu überprüfen, ob das Azure Migrate-Projekt erstellt wurde.


1. Navigieren Sie zu Azure Migrate, indem Sie im Azure-Portal über die Suchleiste nach **Azure Migrate** suchen.
2. Klicken Sie unter der Kachel „Server, Datenbanken und Web-Apps“ auf die Schaltfläche **Entdecken**, **Bewerten** und **Migrieren**.
3. Wählen Sie das **Azure-Abonnement** und das **Projekt** gemäß den in der Bereitstellung angegebenen Werten aus.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Azure Migrate-Projekt erstellt. Weitere Informationen zu Azure Migrate und den Funktionen finden Sie in der Übersicht über Azure Migrate.

> [!div class="nextstepaction"]
> [Übersicht über Azure Migrate](migrate-services-overview.md)
>

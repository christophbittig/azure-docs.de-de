---
title: Konfigurieren und Verwenden von öffentlichen Umgebungen
description: In diesem Artikel erfahren Sie, wie Sie öffentliche Umgebungen (Azure Resource Manager-Vorlagen in einem Git-Repository) in Azure DevTest Labs konfigurieren und verwenden.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 3570f88ae8fe88740721b04783a8689e22c7bb7d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286372"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurieren und Verwenden von Umgebungen in Azure DevTest Labs
Azure DevTest Labs verfügt über ein [öffentliches Repository von Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-devtestlab/tree/master/Environments), die Sie zum Erstellen von Umgebungen verwenden können. Sie müssen nicht selbst eine Verbindung mit einer externen GitHub Quelle-herstellen. Dieses Repository enthält häufig verwendete Vorlagen wie Azure-Web-Apps, Service-Fabric-Cluster und SharePoint-Farm-Entwicklungsumgebungen. Dieses Feature ähnelt dem öffentlichen Repository von Artefakten, das für jedes von Ihnen erstellte Lab enthalten ist. Das Umgebungs-Repository stellt vorab erstellte Umgebungsvorlagen mit minimalen Eingabeparametern bereit. Die Vorlagen bieten Ihnen einen reibungslosen Einstieg für PaaS-Ressourcen (Platform-as-a-Service) in Labs.
  
## <a name="configuring-public-environments"></a>Konfigurieren von öffentlichen Umgebungen
Als Lab-Besitzer können Sie das öffentliche Repository für Ihr Lab während der Erstellung des Labs aktivieren. Um öffentliche Umgebungen für Ihr Lab zu aktivieren, klicken Sie beim Erstellen eines Labs im Feld **Öffentliche Umgebungen** auf das Feld **Ein**. 

![Aktivieren Sie die öffentliche Umgebung für ein neues Lab.](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)

Für bereits vorhandene Labs ist das Repository für öffentliche Umgebungen nicht aktiviert. Aktivieren Sie das Repository manuell, um die Vorlagen zu verwenden. Für Labs, die mit Ressourcen-Verwaltungs-Vorlagen erstellt werden, ist das Repository ebenfalls standardmäßig deaktiviert.

Sie können öffentliche Umgebungen für Ihr Lab aktivieren oder deaktivieren und mit den folgenden Schritten auch nur bestimmte Umgebungen für Lab-Benutzer zur Verfügung stellen: 

1. Wählen Sie **Konfiguration und Richtlinien** für Ihr Lab aus. 
2. Wählen Sie im Abschnitt **Basen virtueller Computer** **Öffentliche Umgebungen** aus.
3. Klicken Sie auf **Ja**, um öffentliche Umgebungen für das Lab zu aktivieren. Klicken Sie andernfalls auf **Nein**. 
4. Wenn Sie öffentliche Umgebungen aktiviert haben, werden alle Umgebungen im Repository standardmäßig aktiviert. Sie können die Auswahl für eine Umgebung aufheben, um diese für Lab-Benutzer nicht verfügbar zu machen. 

![Screenshot: Seite "Öffentliche Umgebungen"](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Verwenden von Umgebungsvorlagen als Lab-Benutzer
Als Lab-Benutzer können Sie eine neue Umgebung aus der aktivierten Liste von Umgebungsvorlagen erstellen, indem Sie einfach auf **Hinzufügen** auf der Symbolleiste der Lab-Seite klicken. Die Liste von Basen enthält die Vorlagen von öffentlichen Umgebungen, die von Ihrem Lab-Administrator am oberen Rand der Liste aktiviert wurden.

![Screenshot: Vorlagen für öffentliche Umgebungen](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Nächste Schritte
Dieses Repository ist ein Open-Source-Repository, an dem Sie mitwirken können. Um Ihre eigenen häufig verwendeten und hilfreichen Ressourcen-Verwaltungs-Vorlagen hinzuzufügen, übermitteln Sie einen Pull Request an das Repository.

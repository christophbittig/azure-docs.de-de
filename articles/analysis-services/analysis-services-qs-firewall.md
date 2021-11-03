---
title: 'Schnellstart: Konfigurieren einer Firewall für einen Azure Analysis Services-Server | Microsoft-Dokumentation'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Firewall für einen Azure Analysis Services-Server über das Azure-Portal konfigurieren.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/12/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom: mode-portal
ms.openlocfilehash: fe774818a1f28d76d35d2771513f91aff785be41
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031839"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Schnellstart: Konfigurieren der Serverfirewall – Portal

In diesem Schnellstart wird beschrieben, wie Sie eine Firewall für Ihren Azure Analysis Services-Server konfigurieren. Das Aktivieren einer Firewall und das Konfigurieren von IP-Adressbereichen nur für jene Computer, die auf Ihren Server zugreifen, sind ein wichtiger Bestandteil der Sicherung Ihres Servers und Ihrer Daten.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Analysis Services-Server in Ihrem Abonnement. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Analysis Services-Servers im Azure-Portal](analysis-services-create-server.md) oder unter [Schnellstart: Erstellen eines Servers mit PowerShell](analysis-services-create-powershell.md).
- Mindestens ein IP-Adressbereich für Clientcomputer (falls erforderlich).

> [!NOTE]
> Verbindungen für den Datenimport (Aktualisierung) und für paginierte Berichte aus Power BI Premium in Microsoft Cloud Deutschland werden derzeit nicht unterstützt, wenn eine Firewall aktiviert ist. Dies gilt auch, wenn die Option zum Zulassen des Zugriffs in den Power BI-Einstellungen auf „Ein“ festgelegt ist.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an. 

[Anmelden beim Portal](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurieren einer Firewall

1. Klicken Sie auf Ihren Server, um die Seite „Übersicht“ zu öffnen. 
2. Wählen Sie unter **EINSTELLUNGEN** > **Firewall** > **Firewall aktivieren** die Option **Ein** aus.
3. Wählen Sie zum Aktivieren von Verbindungen über Power BI und Power BI Premium unter **Zugriff über Power BI zulassen** die Option **Ein** aus.  
4. (Optional) Geben Sie einen oder mehrere IP-Adressbereiche an. Geben Sie einen Namen sowie eine Start- und End-IP-Adresse für jeden Bereich ein. Der Name einer Firewallregel ist auf 128 Zeichen begrenzt und darf nur aus Großbuchstaben, Kleinbuchstaben, Zahlen, Unterstriche und Bindestriche bestehen. Leerzeichen und andere Sonderzeichen sind nicht zulässig.
5. Klicken Sie auf **Speichern**.

     ![Firewalleinstellungen](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie IP-Adressbereiche, oder deaktivieren Sie die Firewall, wenn diese nicht mehr benötigt werden.

## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie erfahren, wie eine Firewall für Ihren Server konfiguriert wird. Nachdem Sie nun über einen Server verfügen und ihn mit einer Firewall gesichert haben, können Sie ihm ein einfaches Beispieldatenmodell aus dem Portal hinzufügen. Ein Beispielmodell ist hilfreich, um sich mit dem Konfigurieren von Modelldatenbankrollen und dem Testen von Clientverbindungen vertraut zu machen. Fahren Sie mit dem Tutorial zum Hinzufügen eines Beispielmodells fort, um mehr zu erfahren.

> [!div class="nextstepaction"]
> [Tutorial: Tutorial: Hinzufügen eines Beispielmodells](analysis-services-create-sample-model.md)

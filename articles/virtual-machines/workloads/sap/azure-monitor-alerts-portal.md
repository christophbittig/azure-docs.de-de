---
title: Konfigurieren von Warnungen in Azure Monitor für SAP-Lösungen über das Azure-Portal
description: Erfahren Sie, wie Sie eine Browsermethode zur Konfiguration von Warnungen in Azure Monitor für SAP-Lösungen verwenden können.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/16/2021
ms.openlocfilehash: 3d75f08ea921e5a29709c697e7677f97891d9b3e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122343023"
---
# <a name="configure-alerts-in-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Konfigurieren von Warnungen in Azure Monitor für SAP-Lösungen über das Azure-Portal

In diesem Artikel werden wir die Schritte zur Konfiguration von Warnungen in Azure Monitor für SAP-Lösungen (AMS) über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal) erläutern. Über die browserbasierte Benutzeroberfläche des Portals konfigurieren wir Warnungen und Benachrichtigungen.

Voraussetzung: Sie müssen die AMS-Ressource mit mindestens einem Anbieter bereitstellen. Sie können Anbieter für SAP Application (NetWeaver), SAP HANA, Microsoft SQL Server oder Hochverfügbarkeitscluster (Pacemaker) konfigurieren. 

## <a name="sign-in-to-the-portal"></a>Anmelden beim Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel

1.  Navigieren Sie im Azure-Portal zu Ihrer AMS-Ressource (Azure Monitor für SAP-Lösungen), und wählen Sie sie aus. Stellen Sie sicher, dass mindestens ein Anbieter für diese AMS-Ressource konfiguriert ist. 
2.  Navigieren Sie zu Arbeitsmappen Ihrer Wahl, z. B. SAP HANA, und wählen Sie eine HANA-Instanz aus.

    :::image type="content" source="./media/ams-alerts/ams-alert-1.png" alt-text="Screenshot: Platzierung der Schaltfläche „Warnung“" lightbox="./media/ams-alerts/ams-alert-1.png":::
  
3.  Klicken Sie auf die Schaltfläche **Warnungen**, um verfügbare **Warnungsvorlagen** anzuzeigen.

    :::image type="content" source="./media/ams-alerts/ams-alert-2.png" alt-text="Screenshot: Liste der Warnungsvorlage" lightbox="./media/ams-alerts/ams-alert-2.png":::
    
4.  Klicken Sie auf **Regel erstellen**, um eine Warnung Ihrer Wahl zu konfigurieren.
5.  Geben Sie den **Warnungsschwellenwert** ein, wählen Sie **Anbieterinstanz** aus, wählen Sie eine **Aktionsgruppe** aus oder erstellen Sie eine, um die Benachrichtigungseinstellung zu konfigurieren. Sie können die Häufigkeits- und Schweregradinformationen gemäß Ihren Anforderungen bearbeiten.

    >[!Tip]
    > Weitere Informationen zu [Aktionsgruppen](../../../azure-monitor/alerts/action-groups.md). 
    
7.  Klicken Sie auf **Benachrichtigungsregel aktivieren**.

    :::image type="content" source="./media/ams-alerts/ams-alert-3.png" alt-text="Screenshot: Seite „Warnungskonfiguration“" lightbox="./media/ams-alerts/ams-alert-3.png":::
    
7.  Klicken Sie auf **Warnungsregel bereitstellen**, um die Konfiguration der Warnungsregel abzuschließen. Sie können die Vorlage für die Warnung anzeigen lassen, indem Sie auf **Vorlage anzeigen** klicken.

    :::image type="content" source="./media/ams-alerts/ams-alert-4.png" alt-text="Screenshot: Letzter Schritt der Warnungskonfiguration" lightbox="./media/ams-alerts/ams-alert-4.png":::
    
8.  Navigieren Sie zu **Warnungsregeln**, um die neu erstellte Warnungsregel anzuzeigen. Wann und ob Warnungen ausgelöst wurden, können Sie unter **Ausgelöste Warnungen** anzeigen.

    :::image type="content" source="./media/ams-alerts/ams-alert-5.png" alt-text="Screenshot: Ergebnis der Warnungskonfiguration" lightbox="./media/ams-alerts/ams-alert-5.png":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Monitor für SAP-Lösungen:

> [!div class="nextstepaction"]
> [Azure Monitor für SAP-Lösungen](azure-monitor-overview.md)
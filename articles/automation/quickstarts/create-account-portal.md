---
title: 'Schnellstart: Erstellen eines Azure Automation-Kontos über das Portal'
description: Dieser Schnellstart unterstützt Sie bei den ersten Schritten zum Erstellen eines Azure Automation-Kontos über das Portal.
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 5d73da041b5ecb5dd2b7089ee898b5bffbe4f512
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542996"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>Schnellstart: Erstellen eines Automation-Kontos über das Azure-Portal

Ein [Azure Automation-Konto](../automation-security-overview.md) kann mithilfe des Azure-Portals erstellt werden. Hierbei handelt es sich um eine browserbasierte Benutzeroberfläche für den Zugriff auf eine Reihe von Ressourcen. Von einem Automation-Konto können Ressourcen in allen Regionen und Abonnements für einen bestimmten Mandanten verwaltet werden. In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Automation-Konto erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-automation-account"></a>Erstellen eines Automation-Kontos

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie im oberen Menü auf **+ Ressource erstellen**.

1. Wählen Sie unter **Kategorien** die Option **IT- und Verwaltungstools** und dann **Automation** aus.

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="Navigieren zu den Automation-Konten im Portal":::

1. Geben Sie auf der Seite **Automation-Konto hinzufügen** die folgenden Informationen an:

   | Eigenschaft | BESCHREIBUNG |
   |---|---|
   |Name| Geben Sie einen eindeutigen Namen für den Standort und die Ressourcengruppe ein. Namen von gelöschten Automation-Konten sind möglicherweise nicht sofort verfügbar. Nach der Eingabe auf der Benutzeroberfläche kann der Kontoname nicht mehr geändert werden. |
   |Subscription| Wählen Sie in der Dropdownliste das Azure-Abonnement für das Konto aus.|
   |Resource group|Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
   |Standort| Wählen Sie in der Dropdownliste einen Standort für das Konto aus. Eine aktualisierte Liste mit Standorten, an denen Sie ein Automation-Konto bereitstellen können, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).|
   |Ausführendes Azure-Konto erstellen| Wählen Sie **Nein** aus.  Ein ausführendes Azure-Konto im Automation-Konto ist für die Authentifizierung bei Azure nützlich. Nun sind jedoch auch verwaltete Identitäten in Automation verfügbar. [Verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) stellen eine Identität bereit, die Anwendungen beim Herstellen einer Verbindung mit Ressourcen verwenden, die Azure Active Directory-Authentifizierung (Azure AD) unterstützen. |

   :::image type="content" source="./media/create-account-portal/add-automation-account-portal.png" alt-text="Erforderliche Felder zum Hinzufügen des Automation-Kontos":::

1. Wählen Sie **Erstellen** aus, um die Bereitstellung des Automation-Kontos zu starten. Die Erstellung wird in etwa einer Minute abgeschlossen.

1. Sie erhalten eine Benachrichtigung, wenn die Bereitstellung abgeschlossen wurde. Wählen Sie in der Benachrichtigung **Zu Ressource wechseln** aus, um die Seite **Automation-Konto** zu öffnen.

1. Überprüfen Sie Ihr neues Automation-Konto.

   :::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Übersichtsseite für das Automation-Konto":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Automation-Konto nicht mehr verwenden möchten, wählen Sie auf der Seite **Übersicht** die Option **Löschen** und dann bei entsprechender Aufforderung **Ja** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Automation-Konto erstellt. Fahren Sie mit der nächsten Schnellstartanleitung fort, um verwaltete Identitäten mit Ihrem Automation-Konto zu verwenden:

> [!div class="nextstepaction"]
> [Schnellstart: Aktivieren verwalteter Identitäten für Ihr Automation-Konto mithilfe des Azure-Portals](enable-managed-identity.md)


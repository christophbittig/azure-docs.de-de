---
title: Verwalten eines Servers – Azure-Portal – Azure Database for MySQL Flexible Server
description: Erfahren Sie, wie Sie Azure Database for MySQL Flexible Server aus dem Azure-Portal verwalten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 677301275beb561a583ffeea92861132a54b3e34
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445457"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-using-azure-portal"></a>Verwalten einer Azure-Datenbank für MySQL-flexible Server mit dem Azure-Portal
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


In diesem Abschnitt erfahren Sie, wie Sie Ihre Azure Datenbank für MySQL-flexible Server verwalten. Zu den Verwaltungsaufgaben gehören die Skalierung von Compute und Speicher, die Wiederherstellung des Kennworts des Serveradministrators und das Löschen Ihres Servers.

## <a name="sign-in"></a>Anmelden

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Navigieren Sie zu Ihrer flexiblen Serverressource im Azure-Portal.

## <a name="scale-compute-and-storage"></a>Skalieren von Compute und Speicher

Nach der Servererstellung können Sie zwischen verschiedenen [Tarifen](https://azure.microsoft.com/pricing/details/mysql/) skalieren, wenn sich Ihre Anforderungen ändern. Sie können auch Ihre Computeressourcen und den Arbeitsspeicher skalieren, indem Sie die Anzahl der virtuellen Kerne vergrößern oder verkleinern.

1. Wählen Sie Ihren Server im Azure-Portal aus. Klicken Sie im Abschnitt **Einstellungen** auf **Compute + Storage** (Compute + Speicher).

2. Sie können den **Computetarif** ändern, um den Server hochzuskalieren, oder die Anzahl der **virtuellen Kerne** oder den **Speicher** an den gewünschten Wert anpassen, um innerhalb des gleichen Computetarifs hochzuskalieren.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="Skalieren des Speichers, Flexible Server":::


> [!IMPORTANT]
   > - Der Speicher kann nicht herunterskaliert werden.
   > - Das Skalieren der virtuellen Kerne löst einen Neustart des Servers aus.

3. Wählen Sie **OK** aus, um die Änderungen zu speichern.

## <a name="reset-admin-password"></a>Zurücksetzen des Administratorkennworts

Sie können das Kennwort für die Administratorrolle über das Azure-Portal ändern.

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Fenster **Übersicht** die Option **Kennwort zurücksetzen** aus.

2. Geben Sie ein neues Kennwort ein, und bestätigen Sie es. Im Textfeld werden Sie nach den Anforderungen an die Kennwortkomplexität gefragt.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="Zurücksetzen Ihres Kennworts für Flexible Server":::

3. Wählen Sie **Speichern** aus, um das neue Kennwort zu speichern.

## <a name="delete-a-server"></a>Löschen eines Servers

Sie können Ihren Server löschen, wenn Sie ihn nicht mehr benötigen.

1. Wählen Sie Ihren Server im Azure-Portal aus. Wählen Sie im Fenster **Übersicht** die Option **Löschen** aus.

2. Geben Sie den Namen des Servers in das Eingabefeld ein, um zu bestätigen, dass Sie den Server löschen möchten.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="Löschen des flexiblen Servers":::

   > [!NOTE]
   > Das Löschen eines Servers kann nicht mehr rückgängig gemacht werden.

3. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte
- [Erfahren Sie mehr über das Starten oder Anhalten eines Servers](how-to-stop-start-server-portal.md)
- [Erfahren Sie mehr über das Wiederherstellen eines Servers](how-to-restore-server-portal.md)
- [Beheben von Verbindungsproblemen](how-to-troubleshoot-common-connection-issues.md)


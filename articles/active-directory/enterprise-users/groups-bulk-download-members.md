---
title: Massendownload der Gruppenmitgliedschaftsliste – Azure Active Directory-Portal | Microsoft-Dokumentation
description: Fügen Sie Benutzer in einem Massenvorgang im Azure Admin Center hinzu.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 09/01/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea46c1f419835948c27e72e1d1b77d2fd6a3592d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986737"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Massendownload von Mitgliedern einer Gruppe in Azure Active Directory

Im Azure Active Directory-Portal (Azure AD) können Sie die Mitglieder einer Gruppe in Ihrer Organisation per Massenvorgang in eine CSV-Datei (Comma-Separated Values, durch Trennzeichen getrennte Werte) herunterladen.

## <a name="to-bulk-download-group-membership"></a>So führen Sie einen Massendownload der Gruppenmitgliedschaft durch

1. Melden Sie sich mit dem Konto eines Benutzeradministrators in der Organisation beim [Azure-Portal](https://portal.azure.com) an. Gruppenbesitzer können Mitglieder von Gruppen, die ihnen gehören, ebenfalls in einem Massenvorgang herunterladen.
1. Wählen Sie in Azure AD **Gruppen** > **Alle Gruppen** aus.
1. Öffnen Sie die Gruppe, deren Mitgliedschaft Sie herunterladen möchten, und wählen Sie dann **Mitglieder** aus.
1. Wählen Sie auf der Seite **Mitglieder** die Option **Mitglieder herunterladen** aus, um eine CSV-Datei mit der Auflistung der Gruppenmitglieder herunterzuladen.

   ![Der Befehl „Mitglieder herunterladen“ ist auf der Profilseite für die Gruppe zu finden.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Überprüfen des Downloadstatus

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Grenzwerte für den Massendownloaddienst

Jede Massenaktivität zum Herunterladen einer Liste von Gruppenmitgliedern kann bis zu einer Stunde dauern. Auf diese Weise können Sie eine Liste von mindestens 500.000 Mitgliedern herunterladen.

## <a name="next-steps"></a>Nächste Schritte

- [Massenimport von Gruppenmitgliedern ausführen](groups-bulk-import-members.md)
- [Massenentfernung von Gruppenmitgliedern](groups-bulk-download-members.md)

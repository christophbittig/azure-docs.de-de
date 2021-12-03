---
title: Herunterladen einer Liste von Gruppen im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Laden Sie Gruppeneigenschaften in einem Massenvorgang im Azure Admin Center in Azure Active Directory herunter.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 298e961eff1736f32d2fa703dfb0079ea758e343
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401381"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Herunterladen einer Liste von Gruppen in einem Massenvorgang in Azure Active Directory

Im Azure Active Directory-Portal (Azure AD) können Sie die Liste aller Gruppen in Ihrer Organisation per Massenvorgang in eine CSV-Datei (Comma-Separated Values, durch Trennzeichen getrennte Werte) herunterladen. Alle Administratoren und Benutzer ohne Administratorrechte können Gruppenlisten herunterladen.

## <a name="to-download-a-list-of-groups"></a>So laden Sie eine Liste von Gruppen herunter

1. Melden Sie sich [beim Azure-Portal](https://portal.azure.com) mit einem Konto in der Organisation an.
1. Wählen Sie in Azure AD **Gruppen** > **Gruppen herunterladen** aus.
1. Wählen Sie auf der Seite **Gruppen herunterladen** die Option **Start** aus, um eine CSV-Datei mit der Auflistung Ihrer Gruppen herunterzuladen.

   ![Der Befehl „Gruppen herunterladen“ befindet sich auf der Seite „Alle Gruppen“.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Überprüfen des Downloadstatus

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Grenzwerte für den Massendownloaddienst

Jede Massenaktivität zum Herunterladen einer Gruppenliste kann bis zu einer Stunde dauern. Auf diese Weise können Sie eine Liste von mindestens 300.000 Gruppen herunterladen.

## <a name="next-steps"></a>Nächste Schritte

- [Massenentfernung von Gruppenmitgliedern](groups-bulk-remove-members.md)
- [Mitglieder einer Gruppe herunterladen](groups-bulk-download-members.md)

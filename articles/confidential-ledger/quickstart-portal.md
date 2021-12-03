---
title: 'Schnellstart: Microsoft Azure Confidential Ledger mit dem Azure-Portal'
description: Hier erfahren Sie, wie Sie Microsoft Azure Confidential Ledger über das Azure-Portal verwenden.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/18/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 265dfc023d0adf384eb483efc16a40380e395fb3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458150"
---
# <a name="quickstart-create-a-confidential-ledger-using-the-azure-portal"></a>Schnellstart: Erstellen einer Confidential Ledger-Instanz über das Azure-Portal

Azure Confidential Ledger ist ein Clouddienst, der einen Speicher mit hoher Integrität für vertrauliche Datenprotokolle und Datensätze bietet, für die Daten intakt bleiben müssen. Weitere Informationen zu Azure Confidential Ledger und Beispiele dafür, was in einer Confidential Ledger-Instanz gespeichert werden kann, finden Sie unter [Informationen zu Microsoft Azure Confidential Ledger](overview.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

In dieser Schnellstartanleitung erstellen Sie eine Confidential Ledger-Instanz über das [Azure-Portal](https://portal.azure.com). 

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-confidential-ledger"></a>Erstellen einer Confidential Ledger-Instanz

1. Wählen Sie im Menü des Azure-Portals oder auf der Startseite die Option **Ressource erstellen** aus.

1. Geben Sie im Suchfeld „Confidential Ledger“ ein.

1. Wählen Sie in der Ergebnisliste **Confidential Ledger** aus.

1. Wählen Sie im Abschnitt „Confidential Ledger“ die Option **Erstellen** aus.

1. Geben Sie im Abschnitt „Create confidential ledger“ (Confidential Ledger-Instanz erstellen) die folgenden Informationen ein:
    - **Name:** Geben Sie für die Confidential Ledger-Instanz einen eindeutigen Namen an.
    - **Abonnement**: Wählen Sie ein Abonnement aus.
    - **Ressourcengruppe:** Wählen Sie die Option **Neu erstellen*** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
    - **Standort:** Wählen Sie im Pulldownmenü einen Standort aus.
    - Behalten Sie bei den anderen Optionen die Standardeinstellungen bei.
   
1. Wählen Sie die Registerkarte **Sicherheit** .

1. Nun müssen Sie der Confidential Ledger-Instanz einen Azure AD-basierten oder zertifikatbasierten Benutzer mit der Rolle „Administrator“ hinzufügen. In dieser Schnellstartanleitung fügen Sie einen Azure AD-basierten Benutzer hinzu. Wählen Sie **+ Add AAD-Based User** (+ AAD-basierten Benutzer hinzufügen) aus.

1. Sie müssen einen Azure AD- oder zertifikatbasierten Benutzer hinzufügen. Suchen Sie im rechten Bereich nach Ihrer E-Mail-Adresse. Wählen Sie die entsprechende Zeile und anschließend unten im Bereich **Auswählen** aus.

1. Wählen Sie im Dropdownfeld **Ledger Role** (Ledger-Rolle) die Option **Administrator** aus.

1. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach erfolgreicher Überprüfung **Erstellen** aus.

Nach Abschluss der Bereitstellung wählen Sie **Zu Ressource wechseln** aus.

:::image type="content" source="./media/confidential-ledger-portal-quickstart.png" alt-text="ACL-Portal: Bildschirm zum Erstellen":::

Beachten Sie die beiden folgenden Eigenschaften:
- **Name des Confidential Ledger**: Im Beispiel wird „test-create-ledger-demo“ verwendet. Dieser Name wird noch für andere Schritte benötigt.
- **Ledger endpoint** (Ledger-Endpunkt): Im Beispiel lautet dieser Endpunkt `https://test-create-ledger-demo.confidential-ledger.azure.net/`. 

Sie benötigen diese Eigenschaftsnamen, um Transaktionen mit der Confidential Ledger-Instanz auf Datenebene auszuführen.
 
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Weitere Artikel zu Azure Confidential Ledger bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Artikeln fortfahren möchten, empfiehlt es sich ggf., diese Ressourcen nicht zu bereinigen. 

Wenn Sie die Ressourcengruppe nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Confidential Ledger-Instanz und die dazugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1.  Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Klicken Sie in den Suchergebnissen auf die Ressourcengruppe aus dieser Schnellstartanleitung.

1.  Wählen Sie die Option **Ressourcengruppe löschen**.

1.  Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Confidential Ledger-Instanz über das Azure-Portal erstellt. Weitere Informationen zu Azure Confidential Ledger und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Übersicht über Microsoft Azure Confidential Ledger](overview.md)

---
title: Konfigurieren der Aufgabentrennung für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung – Azure Active Directory
description: Hier erfahren Sie, wie Sie die Erzwingung der Aufgabentrennung für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung konfigurieren.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/2/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 171901de8f9037ee6e77f83138ec7a3065c2d71f
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730168"
---
# <a name="configure-separation-of-duties-checks-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Konfigurieren von Aufgabentrennungsprüfungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung (Vorschau)

In Azure AD Berechtigungsverwaltung können Sie mehrere Richtlinien mit verschiedenen Einstellungen für jede Benutzercommunity konfigurieren, die Zugriff über ein Zugriffspaket benötigt.  Beispielsweise benötigen Mitarbeiter möglicherweise nur eine Genehmigung des Managers, um Zugriff auf bestimmte Apps zu erhalten, während für Gäste aus anderen Organisationen eine Genehmigung eines Sponsors und des Abteilungsleiters des Ressourcenteams benötigen. In einer Richtlinie für Benutzer, die sich bereits im Verzeichnis befinden, können Sie eine bestimmte Gruppe von Benutzern angeben, für die ein Zugriff angefordert werden kann. Sie müssen jedoch u. U. verhindern, dass ein Benutzer übermäßigen Zugriff erhält.  Um diese Anforderung zu erfüllen, kann es wünschenswert sein, basierend auf dem Zugriff, über den der Anforderer bereits verfügt, weiter einzuschränken, wer Zugriff anfordern kann.

Mit den Aufgabentrennungseinstellungen für ein Zugriffspaket können Sie konfigurieren, dass ein Benutzer, der Mitglied einer Gruppe ist oder bereits über eine Zuweisung zu einem Zugriffspaket verfügt, kein weiteres Zugriffspaket anfordern kann.

![MyAccess-Benutzeroberfläche für den Versuch, inkompatiblen Zugriff anzufordern](./media/entitlement-management-access-package-incompatible/request-prevented.png)


## <a name="scenarios-for-separation-of-duties-checks"></a>Szenarien für Aufgabentrennungsprüfungen

Angenommen, Sie verfügen über ein Zugriffspaket, *Marketingkampagne*, auf das Personen in Ihrer Organisation und anderen Organisationen Zugriff anfordern können, um mit der Marketingabteilung Ihrer Organisation zusammenzuarbeiten, während die Kampagne läuft. Da Mitarbeiter in der Marketingabteilung bereits Zugriff auf das Material für diese Marketingkampagne haben sollten, ist es nicht wünschenswert, dass Mitarbeiter in der Marketingabteilung Zugriff auf dieses Zugriffspaket anfordern.  Oder Sie verfügen bereits über eine dynamische Gruppe, *Mitarbeiter der Marketingabteilung*, die alle Marketingmitarbeiter enthält. Sie können angeben, dass das Zugriffspaket nicht mit der Mitgliedschaft in dieser dynamischen Gruppe kompatibel ist. Wenn ein Mitarbeiter der Marketingabteilung dann nach einem anzufordernden Zugriffspaket sucht, kann er keinen Zugriff auf das Zugriffspaket *Marketingkampagne* anfordern.

Genauso kann es sein, dass Sie über eine Anwendung mit zwei Rollen verfügen, **Vertrieb West** sowie **Vertrieb Ost**, und sicherstellen möchten, dass ein Benutzer jeweils nur ein Vertriebsgebiet haben kann.  Wenn Sie über zwei Zugriffspakete verfügen, ein Zugriffspaket **Region West** für die Rolle **Vertrieb West** und ein Zugriffspaket **Region Ost** für die Rolle **Vertrieb Ost**, können Sie folgende Konfiguration vornehmen:
 - Das Zugriffspaket **Region West** weist das Paket **Region Ost** als inkompatibel aus.
 - Das Zugriffspaket **Region Ost** weist das Paket **Region West** als inkompatibel aus.

Wenn Sie Microsoft Identity Manager oder andere lokale Identitätsverwaltungssysteme zum Automatisieren des Zugriffs für lokale Apps verwendet haben, können Sie auch diese Systeme in die Azure AD-Berechtigungsverwaltung integrieren.  Wenn Sie den Zugriff auf in Azure AD integrierten Apps über die Berechtigungsverwaltung steuern und verhindern möchten, dass Benutzer inkompatiblen Zugriff haben, können Sie konfigurieren, dass ein Zugriffspaket mit einer Gruppe nicht kompatibel ist. Dies kann eine Gruppe sein, die Ihr lokales Identitätsverwaltungssystem über Azure AD Connect an Azure AD überträgt. Diese Überprüfung stellt sicher, dass ein Benutzer kein Zugriffspaket anfordern kann, wenn dieses Zugriffspaket Zugriff gewährt, der nicht mit dem Zugriff des Benutzers in lokalen Apps kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Berechtigungsverwaltung zu verwenden und Benutzer zu Zugriffspaketen zuzuweisen, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-Lizenz

## <a name="configure-another-access-package-or-group-membership-as-incompatible-for-requesting-access-to-an-access-package"></a>Konfigurieren eines anderen Zugriffspakets oder einer Gruppenmitgliedschaft als inkompatibel zum Anfordern des Zugriffs auf ein Zugriffspaket

**Erforderliche Rolle**: Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

Führen Sie die folgenden Schritte aus, um die Liste der inkompatiblen Gruppen oder anderen Zugriffspakete für ein vorhandenes Zugriffspaket zu ändern:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1.  Klicken Sie auf **Azure Active Directory** und dann auf **Identity Governance**.

1.  Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket, das Benutzer anfordern.

1.  Klicken Sie im linken Menü auf **Aufgabentrennung (Vorschau)** .

1.  Wenn Sie verhindern möchten, dass Benutzer, die bereits über eine andere Zugriffspaketzuweisung verfügen, dieses Zugriffspaket anfordern, klicken Sie auf **Zugriffspaket hinzufügen**, und wählen Sie das Zugriffspaket aus, dem der Benutzer bereits zugewiesen wäre.


    ![Konfiguration von inkompatiblen Zugriffspaketen](./media/entitlement-management-access-package-incompatible/select-incompatible-ap.png)


1.  Wenn Sie verhindern möchten, dass Benutzer mit einer vorhandenen Gruppenmitgliedschaft dieses Zugriffspaket anfordern, klicken Sie auf **Gruppe hinzufügen**, und wählen Sie die Gruppe aus, in der sich der Benutzer bereits befinden würde.

### <a name="configure-incompatible-access-packages-programmatically"></a>Programmgesteuertes Konfigurieren inkompatibler Zugriffspakete

Sie können die Gruppen und andere Zugriffspakete, die mit dem Zugriffspaket nicht kompatibel sind, auch mit Microsoft Graph konfigurieren.  Ein Benutzer in einer passenden Rolle mit einer Anwendung mit der delegierten Berechtigung `EntitlementManagement.ReadWrite.All` oder einer Anwendung mit dieser Anwendungsberechtigung kann die API aufrufen, um die inkompatiblen Gruppen und Zugriffspakete [eines Zugriffspakets](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true) hinzuzufügen, zu entfernen und aufzulisten.


## <a name="view-other-access-packages-that-are-configured-as-incompatible-with-this-one"></a>Anzeigen anderer Zugriffspakete, die als inkompatibel mit diesem konfiguriert sind

**Erforderliche Rolle**: Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

Führen Sie die folgenden Schritte aus, um die Liste der anderen Zugriffspakete anzuzeigen, die darauf hingewiesen haben, dass sie mit einem vorhandenen Zugriffspaket nicht kompatibel sind:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1.  Klicken Sie auf **Azure Active Directory** und dann auf **Identity Governance**.

1.  Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1.  Klicken Sie im linken Menü auf **Aufgabentrennung (Vorschau)** .

1. Klicken Sie auf **Inkompatibel mit**.

## <a name="monitor-and-report-on-access-assignments"></a>Überwachen und Melden von Zugriffszuweisungen

Sie können Azure Monitor-Arbeitsmappen verwenden, um Erkenntnisse darüber zu erhalten, wie Benutzer Zugriff erhalten haben.

1. Konfigurieren Sie Azure AD so, dass [Überwachungsereignisse an Azure Monitor gesendet werden](entitlement-management-logs-and-reporting.md).

1. Die Arbeitsmappe namens *Zugriffspaketaktivität* zeigt alle Ereignisse in Zusammenhang mit einem bestimmten Zugriffspaket an.

    ![Anzeigen von Zugriffspaketereignissen](./media/entitlement-management-logs-and-reporting/view-events-access-package.png)

1. Um festzustellen, ob Änderungen an Anwendungsrollenzuweisungen für eine Anwendung vorgenommen wurden, die nicht aufgrund von Zugriffspaketzuweisungen erstellt wurden, können Sie die Arbeitsmappe namens *Anwendungsrollenzuweisungsaktivität* auswählen.  Wenn Sie die Berechtigungsaktivität weglassen, werden nur Änderungen an Anwendungsrollen angezeigt, die nicht von der Berechtigungsverwaltung vorgenommen wurden. So wird beispielsweise eine Zeile angezeigt, wenn ein globaler Administrator einem Benutzer direkt eine Anwendungsrolle zugewiesen hat.

    ![Anzeige von App-Rollenzuweisungen](./media/entitlement-management-access-package-incompatible/workbook-ara.png)


## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen, Hinzufügen und Entfernen von Zuweisungen für ein Zugriffspaket](entitlement-management-access-package-assignments.md)
- [Anzeigen von Berichten und Protokollen](entitlement-management-reports.md)

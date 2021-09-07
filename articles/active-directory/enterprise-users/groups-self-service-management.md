---
title: 'Einrichten der Self-Service-Gruppenverwaltung: Azure Active Directory | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie Sicherheitsgruppen oder Microsoft 365-Gruppen in Azure Active Directory erstellen und verwalten sowie Mitgliedschaften in Sicherheitsgruppen oder Microsoft 365-Gruppen anfordern.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 07/27/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 724f63792c71e59b071e1851a013cbf94933008b
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721515"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Einrichten der Self-Service-Gruppenverwaltung in Azure Active Directory 

Sie können Benutzern die Erstellung und Verwaltung ihrer eigenen Sicherheitsgruppen oder Microsoft 365-Gruppen in Azure Active Directory (Azure AD) ermöglichen. Der Besitzer der Gruppe kann Mitgliedschaftsanforderungen genehmigen oder ablehnen sowie die Steuerung der Gruppenmitgliedschaft delegieren. Self-Service-Funktionen zur Gruppenverwaltung sind nicht für E-Mail-aktivierte Sicherheitsgruppen oder Verteilerlisten verfügbar.

## <a name="self-service-group-membership-defaults"></a>Standardwerte für die Self-Service-Gruppenmitgliedschaft

Wenn Sicherheitsgruppen im Azure-Portal oder über Azure AD PowerShell erstellt werden, können nur die Besitzer der Gruppen die Mitgliedschaft aktualisieren. Ein Beitritt zu im [Zugriffsbereich](https://account.activedirectory.windowsazure.com/r#/joinGroups) nach dem Self-Service-Prinzip erstellten Sicherheitsgruppen sowie zu allen Microsoft 365-Gruppen ist für alle vom Besitzer genehmigten oder automatisch genehmigten Benutzer möglich. Im Zugriffsbereich können Sie beim Erstellen einer Gruppe die Mitgliedschaftsoptionen ändern.

Erstellte Gruppen in | Standardverhalten von Sicherheitsgruppen | Standardverhalten von Microsoft 365-Gruppen
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Nur Besitzer können Mitglieder hinzufügen.<br>Sichtbar, jedoch kein Beitritt im Zugriffsbereich möglich. | Beitritt aller Benutzer ist möglich.
[Azure portal](https://portal.azure.com) | Nur Besitzer können Mitglieder hinzufügen.<br>Sichtbar, jedoch kein Beitritt im Zugriffsbereich möglich.<br>Besitzer wird bei der Gruppenerstellung nicht automatisch zugewiesen. | Beitritt aller Benutzer ist möglich.
[Zugriffsbereich](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Beitritt aller Benutzer ist möglich.<br>Mitgliedschaftsoptionen können bei der Erstellung der Gruppe geändert werden. | Beitritt aller Benutzer ist möglich.<br>Mitgliedschaftsoptionen können bei der Erstellung der Gruppe geändert werden.

## <a name="self-service-group-management-scenarios"></a>Szenarien der Self-Service-Gruppenverwaltung

* **Delegierte Gruppenverwaltung** – Beispielsweise ein Administrator, der den Zugriff auf eine SaaS-Anwendung verwaltet, die im Unternehmen verwendet wird. Die Verwaltung dieser Zugriffsrechte wird immer mühsamer, und der Administrator bittet den Geschäftsinhaber, eine neue Gruppe zu erstellen. Der Administrator weist den Zugriff auf die Anwendung der neuen Gruppe zu und fügt der Gruppe alle Personen zu, die bereits auf die Anwendung zugreifen. Der Geschäftsinhaber kann dann weitere Benutzer hinzufügen, und die Benutzer erhalten automatisch Zugriff auf die Anwendung. Der Geschäftsinhaber muss nicht warten, bis der Administrator die Verwaltungsschritte für den Zugriff der Benutzer durchgeführt hat. Wenn der Administrator einem Manager in einer anderen Geschäftseinheit die gleiche Berechtigung erteilt, kann diese Person ebenfalls den Zugriff für ihre eigenen Gruppenmitglieder verwalten. Weder der Geschäftsinhaber noch der Manager kann die Gruppenmitgliedschaften des jeweils anderen anzeigen oder verwalten. Der Administrator kann weiterhin alle Benutzer anzeigen, die auf die Anwendung zugreifen können, und kann die Zugriffsrechte bei Bedarf blockieren.
* **Self-Service-Gruppenverwaltung** Ein Beispiel für dieses Szenario sind zwei Benutzer, die beide über unabhängig voneinander eingerichtete SharePoint Online-Websites verfügen. Sie möchten dem anderen Team jeweils Zugriff auf ihre Websites gewähren. Hierzu können sie eine Gruppe in Azure AD erstellen, und in SharePoint Online wählt jeder der beiden Benutzer diese Gruppe dann aus, um Zugriff auf seine Websites bereitzustellen. Wenn ein weiterer Benutzer Zugriff benötigt, fordert er diesen einfach über den Zugriffsbereich an und erhält nach Genehmigung automatisch Zugriff auf beide SharePoint Online-Websites. Später entscheidet einer der Hauptbenutzer, dass alle Personen, die auf die Website zugreifen, auch Zugriff auf eine bestimmte SaaS-Anwendung erhalten sollen. Der Administrator der SaaS-Anwendung kann der SharePoint Online-Website Zugriffsrechte für die Anwendung hinzufügen. Anschließend können alle Personen, deren Zugriff genehmigt wird, auf die beiden SharePoint Online-Websites und diese SaaS-Anwendung zugreifen.

## <a name="make-a-group-available-for-user-self-service"></a>Einrichten einer Gruppe für Self-Service durch Benutzer

1. Melden Sie sich im [Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, dem die Rolle „Globaler Administrator“ oder „Administrator für privilegierte Rollen“ für das Verzeichnis zugewiesen wurde.

1. Klicken Sie auf **Gruppen** und anschließend auf **Allgemeine Einstellungen**.

    ![Allgemeine Einstellungen für Azure Active Directory-Gruppen](./media/groups-self-service-management/groups-settings-general.png)

1. Legen Sie **Besitzer können Anforderungen für eine Gruppenmitgliedschaft im Zugriffspanel verwalten** auf **Ja** fest.

1. Legen Sie **Benutzerfähigkeit zum Zugriff auf Gruppenfunktionen im Zugriffsbereich einschränken** auf **Nein** fest.

1. Legen Sie **Benutzer können Sicherheitsgruppen in Azure-Portalen, API oder PowerShell erstellen** auf **Ja** oder **Nein** fest.

    Weitere Informationen zu dieser Einstellung finden Sie im nächsten Abschnitt [Gruppeneinstellungen](#group-settings).

1. Legen Sie **Benutzer können Microsoft 365-Gruppen in Azure-Portalen, API oder PowerShell erstellen** auf **Ja** oder **Nein** fest.

    Weitere Informationen zu dieser Einstellung finden Sie im nächsten Abschnitt [Gruppeneinstellungen](#group-settings).

Sie können auch **Besitzer, die im Azure-Portal Mitglieder als Gruppenbesitzer zuweisen können**, verwenden, um eine detailliertere Zugriffssteuerung über die Gruppenverwaltung nach dem Self-Service-Prinzip für Ihre Benutzer zu erreichen.

Wenn Benutzer Gruppen erstellen können, dürfen alle Benutzer in Ihrer Organisation neue Gruppen erstellen und können dann als Standardbesitzer Mitglieder diesen Gruppen hinzufügen. Sie können keine Personen angeben, die ihre eigenen Gruppen erstellen können. Sie können lediglich Personen angeben, die ein anderes Gruppenmitglied als Gruppenbesitzer festlegen.

> [!NOTE]
> Eine Azure Active Directory Premium-Lizenz (P1 oder P2) ist erforderlich, damit Benutzer den Beitritt zu einer Sicherheitsgruppe oder Microsoft 365-Gruppe anfordern und Besitzer Mitgliedsanforderungen genehmigen oder ablehnen können. Ohne eine Azure Active Directory Premium-Lizenz können Benutzer ihre Gruppen weiterhin im Zugriffsbereich verwalten, sie können jedoch keine Gruppe erstellen, die im Zugriffsbereich eine Genehmigung durch den Besitzer erfordert, und sie können keinen Gruppenbeitritt anfordern.

## <a name="group-settings"></a>Gruppeneinstellungen

Mithilfe von Gruppeneinstellungen können Sie steuern, wer Sicherheits- und Microsoft 365-Gruppen erstellen kann.

![Ändern der Einstellung für Azure Active Directory-Sicherheitsgruppen](./media/groups-self-service-management/security-groups-setting.png)

> [!NOTE]
> Das Verhalten dieser Einstellungen wurde vor Kurzem geändert. Stellen Sie sicher, dass diese Einstellungen für Ihre Organisation konfiguriert sind. Weitere Informationen finden Sie unter [Warum wurden die Gruppeneinstellungen geändert?](#why-were-the-group-settings-changed).

 Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Werte Sie auswählen müssen.

| Einstellung | Wert | Auswirkung auf Ihren Mandanten |
| --- | :---: | --- |
| Benutzer können Sicherheitsgruppen in Azure-Portalen, API oder PowerShell erstellen | Ja | Alle Benutzer in Ihrer Azure AD-Organisation können in Azure-Portalen, in der API oder in PowerShell neue Sicherheitsgruppen erstellen und diesen Gruppen Mitglieder hinzufügen. Diese neuen Gruppen werden auch für alle Benutzer im Zugriffsbereich angezeigt. Wenn die Richtlinieneinstellung der Gruppe dies zulässt, können andere Benutzer Anforderungen in Bezug auf den Beitritt zu diesen Gruppen erstellen. |
|  | Nein | Benutzer können keine Sicherheitsgruppen erstellen und keine vorhandenen Gruppen ändern, deren Besitzer sie sind. Sie können aber trotzdem die Mitgliedschaften dieser Gruppen verwalten und Anforderungen anderer Benutzer zum Beitreten zu diesen Gruppen genehmigen. |
| Benutzer können Microsoft 365-Gruppen in Azure-Portalen, API oder PowerShell erstellen | Ja | Alle Benutzer in Ihrer Azure AD-Organisation können in Azure-Portalen, in der API oder in PowerShell neue Microsoft 365-Gruppen erstellen und diesen Gruppen Mitglieder hinzufügen. Diese neuen Gruppen werden auch für alle Benutzer im Zugriffsbereich angezeigt. Wenn die Richtlinieneinstellung der Gruppe dies zulässt, können andere Benutzer Anforderungen in Bezug auf den Beitritt zu diesen Gruppen erstellen. |
|  | Nein | Benutzer können keine Microsoft 365-Gruppen erstellen und keine vorhandenen Gruppen ändern, deren Besitzer sie sind. Sie können aber trotzdem die Mitgliedschaften dieser Gruppen verwalten und Anforderungen anderer Benutzer zum Beitreten zu diesen Gruppen genehmigen. |

Im Folgenden finden Sie einige zusätzliche Details zu diesen Gruppeneinstellungen.

- Es kann bis zu 15 Minuten dauern, bis die Einstellung wirksam wird.
- Wenn Sie einigen, aber nicht allen Benutzern das Erstellen von Gruppen ermöglichen möchten, können Sie diesen Benutzern eine Rolle mit der Berechtigung zum Erstellen von Gruppen zuweisen, beispielsweise [Gruppenadministrator](../roles/permissions-reference.md#groups-administrator).
- Diese Einstellungen gelten für Benutzer und wirken sich nicht auf Dienstprinzipale aus. Wenn Sie beispielsweise über einen Dienstprinzipal mit Berechtigungen zum Erstellen von Gruppen verfügen, kann der Dienstprinzipal auch dann weiterhin Gruppen erstellen, wenn Sie diese Einstellungen auf **Nein** festlegen. 

### <a name="why-were-the-group-settings-changed"></a>Warum wurden die Gruppeneinstellungen geändert?

Die vorherige Implementierung der Gruppeneinstellungen hieß **Benutzer können Sicherheitsgruppen in Azure-Portalen erstellen** und **Benutzer können Microsoft 365-Gruppen in Azure-Portalen erstellen**. Die vorherigen Einstellungen steuerten nur die Gruppenerstellung in Azure-Portalen und galten nicht für die API oder PowerShell. Die neuen Einstellungen steuern die Gruppenerstellung sowohl in Azure-Portalen als auch in der API und PowerShell. Die neuen Einstellungen bieten mehr Sicherheit.

Die Standardwerte für die neuen Einstellungen wurden auf Ihre vorherigen API- oder PowerShell-Werte festgelegt. Es besteht die Möglichkeit, dass sich die Standardwerte für die neuen Einstellungen von Ihren vorherigen Werten abweichen, mit denen nur das Verhalten für das Azure-Portal gesteuert wurde. Ab Mai 2021 konnten Sie in einer mehrwöchigen Übergangsphase Ihren bevorzugten Standardwert auswählen, bevor die neuen Einstellungen in Kraft traten. Nachdem jetzt die neuen Einstellungen wirksam geworden sind, müssen Sie überprüfen, ob die neuen Einstellungen für Ihre Organisation konfiguriert sind.

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../enterprise-users/groups-settings-cmdlets.md)
* [Anwendungsverwaltung in Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Was ist Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

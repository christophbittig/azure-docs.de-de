---
title: Planen Ihrer Azure Active Directory-Gerätebereitstellung
description: Wählen Sie Strategien für die Azure AD-Geräteintegration aus, die den Anforderungen Ihrer Organisation entsprechen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8198b7f4bbb7c710e899349766d0345655d7f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633429"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Planen Ihrer Azure Active Directory-Gerätebereitstellung

In diesem Artikel erfahren Sie, wie Sie die Methoden zur Integration Ihres Geräts in Azure AD auswerten, den Implementierungsplan auswählen und wichtige Links zu den unterstützten Geräteverwaltungstools bereitstellen.

Die Bandbreite an Geräten, über die sich Ihre Benutzer anmelden können, wird erweitert. Organisationen können Desktops, Laptops, Smartphones, Tablets und andere Geräte bereitstellen. Ihre Benutzer können ihre eigenen Arrays von Geräten einbringen und von unterschiedlichen Standorten aus auf Informationen zugreifen. In dieser Umgebung besteht Ihr Auftrag als Administrator darin, für die Sicherheit Ihrer Organisationsressourcen auf allen Geräten zu sorgen.

Azure Active Directory (Azure AD) ermöglicht es Ihrer Organisation, mit der Geräteidentitätsverwaltung diese Ziele zu erreichen. Sie können Ihre Geräte jetzt in Azure AD abrufen und sie von einem zentralen Ort im [Azure-Portal](https://portal.azure.com/) aus steuern. Dadurch erhalten Sie eine einheitliche Erfahrung und eine optimierte Sicherheit, und außerdem verkürzt sich die Zeit, die zum Konfigurieren eines neuen Geräts benötigt wird.

Es gibt mehrere Methoden, um Ihre Geräte in Azure AD zu integrieren:

* Manuelles [Registrieren von Geräten](concept-azure-ad-register.md) mit Azure AD

* [Einbinden von Geräten](concept-azure-ad-join.md) in Azure AD (nur Cloud)

* [Erstellen eines Hybrid Azure AD Join](concept-azure-ad-join-hybrid.md) zwischen Geräten in Ihrem lokalen Active Directory und Azure AD 

## <a name="learn"></a>Lernen

Bevor Sie beginnen, stellen Sie sicher, dass Sie mit der [Übersicht über die Geräteidentitätsverwaltung](overview.md) vertraut sind.

### <a name="benefits"></a>Vorteile

Die wichtigsten Vorteile, wenn Sie Ihren Geräten eine Azure AD Identität zuweisen:

* Steigern der Produktivität – mit Azure AD ermöglichen Sie Ihren Benutzern eine [nahtlose Anmeldung (SSO](./azuread-join-sso.md)) für Ihre lokalen und Cloudressourcen, sodass sie unabhängig von ihrem Standort produktiv arbeiten können.

* Optimieren der Sicherheit – mit Azure AD-Geräten können Sie [Richtlinien für den bedingten Zugriff](../conditional-access/require-managed-devices.md) auf Ressourcen basierend auf der Identität des Geräts oder des Benutzers anwenden. Richtlinien für bedingten Zugriff bieten mithilfe von [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) zusätzlichen Schutz. Das Einbinden eines Geräts in Azure AD ist eine Voraussetzung, um Ihre Sicherheit mit einer Strategie zur [kennwortlosen Authentifizierung](../authentication/concept-authentication-passwordless.md) zu optimieren.

* Verbessern der Benutzererfahrung – mit Geräteidentitäten in Azure AD ermöglichen Sie Ihren Benutzern einfachen Zugriff auf die cloudbasierten Ressourcen Ihrer Organisation sowohl über persönliche und als auch über unternehmenseigene Geräte. Administratoren können [Enterprise State Roaming](enterprise-state-roaming-overview.md) für eine einheitliche Darstellung auf allen Windows-Geräten aktivieren.

* Vereinfachen der Bereitstellung und der Verwaltung – die Geräteidentitätsverwaltung vereinfacht das Bereitstellen von Geräten in Azure AD mit [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot), [Massenbereitstellung](/mem/intune/enrollment/windows-bulk-enroll) und [Self-Service: Out of Box Experience (OOBE)](https://support.microsoft.com/account-billing/join-your-work-device-to-your-work-or-school-network-ef4d6adb-5095-4e51-829e-5457430f3973). Sie können diese Geräte mit MDM-Tools (Mobile Device Management, mobile Geräteverwaltung) wie [Microsoft Intune](/mem/intune/fundamentals/what-is-intune) und ihre Identitäten im [Azure-Portal](https://portal.azure.com/) verwalten.

### <a name="training-resources"></a>Schulungsressourcen

Video:  [Bedingter Zugriff mit Gerätesteuerungen](https://youtu.be/NcONUf-jeS4)

Häufig gestellte Fragen: [Häufig gestellte Fragen zur Azure AD-Geräteverwaltung](faq.yml) und [Häufig gestellte Fragen zu Einstellungen und Datenroaming](enterprise-state-roaming-faqs.yml) 

## <a name="plan-the-deployment-project"></a>Planen des Bereitstellungsprojekts

Berücksichtigen Sie die Anforderungen Ihrer Organisation, während Sie die Strategie für diese Bereitstellung in Ihrer Umgebung festlegen.

### <a name="engage-the-right-stakeholders"></a>Einbeziehen der richtigen Beteiligten

Fehler in Technologieprojekten sind in der Regel auf nicht erfüllte Erwartungen auf den Gebieten Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](../fundamentals/active-directory-deployment-plans.md) und dass die Rollen der Beteiligten im Projekt gut verstanden werden. 

Fügen Sie für diesen Plan die folgenden Projektbeteiligten zu Ihrer Liste hinzu:

| Role| BESCHREIBUNG |
| - | - |
| Geräteadministrator| Ein Vertreter des Geräteteams, der überprüfen kann, dass der Plan die Geräteanforderungen Ihrer Organisation erfüllt |
| Netzwerkadministrator| Ein Vertreter des Netzwerkteams, das sicherstellen kann, dass die Netzwerkanforderungen erfüllt sind |
| Geräteverwaltungsteam| Das Team, das das Geräteinventar verwaltet |
| Betriebssystemspezifische Administratorteams| Teams, die bestimmte Betriebssystemversionen unterstützen und verwalten. So kann es beispielsweise Teams mit Schwerpunkt Mac oder iOS geben. |

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Kommunizieren Sie proaktiv mit Ihren Benutzern darüber, wie sich die Nutzung ändern wird, wann sie sich ändert und wie sie Unterstützung erhalten, wenn sie auf Probleme stoßen.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Es wird empfohlen, dass die anfängliche Konfiguration Ihrer Integrationsmethode in einer Testumgebung oder einer kleinen Gruppe von Testgeräten durchgeführt wird. Lesen Sie hierzu [Bewährte Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md).

Die Azure AD Hybrid Join-Bereitstellung ist einfach, und sie ist zu 100 % Aufgabe eines Administrators, ohne die dass Endbenutzer irgendwelche Aktionen ausführen müssen. Sie können beispielsweise eine [kontrollierte Überprüfung von Azure AD Hybrid Join](hybrid-azuread-join-control.md) vornehmen, bevor Sie den Dienst in der gesamten Organisation aktivieren.

## <a name="choose-your-integration-methods"></a>Auswählen Ihrer Integrationsmethoden

Ihre Organisation kann mehrere Methoden zur Geräteintegration in einem einzelnen Azure AD-Mandanten verwenden. Ziel ist es, die Methoden auszuwählen, die für die sichere Verwaltung Ihrer Geräte in Azure AD geeignet sind. Es gibt viele Parameter, die diese Entscheidung steuern, einschließlich Besitz, Gerätetypen, primäre Zielgruppe und Infrastruktur Ihres Unternehmens.

Die folgenden Informationen können Ihnen bei der Entscheidung helfen, welche Integrationsmethoden Sie verwenden möchten.

### <a name="decision-tree-for-devices-integration"></a>Entscheidungsstruktur für die Geräteintegration

Verwenden Sie diese Struktur, um Optionen für Geräte zu bestimmen, die sich im Besitz der Organisation befinden. 

> [!NOTE]
> In diesem Diagramm sind keine persönlichen oder Bring Your Own Device-Szenarien (BYOD) abgebildet. Sie führen immer zu einer Azure AD-Registrierung.

 ![Entscheidungsstruktur](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Vergleichsmatrix

iOS- und Android-Geräte können nur in Azure AD registriert werden. In der folgenden Tabelle sind allgemeine Überlegungen zu Windows-Clientgeräten dargestellt. Verwenden Sie sie als Übersicht, und sehen Sie sich die verschiedenen Integrationsmethoden anschließend ausführlich an.

| Aspekt | Bei Azure AD registriert| Azure AD-Einbindung| Azure AD-Hybrideinbindung |
| - | - | - | - |
| **Clientbetriebssysteme**| | |  |
| Windows 10-Geräte| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| Kompatible Windows-Geräte (Windows 8.1 oder Windows 7)| | | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
|**Anmeldeoptionen**| | |  |
| Lokale Anmeldeinformationen von Endbenutzern| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| |  |
| Kennwort| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| Geräte-PIN| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello for Business| | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| FIDO 2.0-Sicherheitsschlüssel| | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator-App (kennwortlos)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
|**Wichtige Funktionen**| | |  |
| SSO für Cloudressourcen| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| Einmaliges Anmelden bei lokalen Ressourcen| | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| Bedingter Zugriff <br> (Markieren des Geräts als kompatibel erforderlich) <br> (Muss von MDM verwaltet werden)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)|![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
Bedingter Zugriff <br>(Über Azure AD Hybrid Join eingebundene Geräte erforderlich)| | | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)
| Self-Service-Kennwortzurücksetzung über den Windows-Anmeldebildschirm| | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| Windows Hello-PIN-Zurücksetzung| | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |
| Geräteübergreifendes Enterprise State Roaming| | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD-Registrierung 

Registrierte Geräte werden häufig mit [Microsoft Intune](/mem/intune/enrollment/device-enrollment) verwaltet. Geräte werden in Intune auf verschiedene Arten registriert, je nach Betriebssystem. 

In Azure AD registrierte Geräte bieten Unterstützung für Bring Your Own Device (BYOD) und unternehmenseigene Geräte für das einmalige Anmelden in Cloudressourcen. Der Zugriff auf Ressourcen basiert auf den Azure AD-[Richtlinien für bedingten Zugriff](../conditional-access/require-managed-devices.md), die auf das Gerät und den Benutzer angewandt werden.

### <a name="registering-devices"></a>Registrieren von Geräten

Registrierte Geräte werden häufig mit [Microsoft Intune](/mem/intune/enrollment/device-enrollment) verwaltet. Geräte werden in Intune auf verschiedene Arten registriert, je nach Betriebssystem. 

BYOD und unternehmenseigene mobile Geräte werden von Benutzern registriert, die die Unternehmensportal-App installieren.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

* [macOS](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)

Wenn die Registrierung Ihrer Geräte die beste Option für Ihre Organisation ist, sehen Sie sich die folgenden Ressourcen an:

* Die Übersicht über [bei Azure AD registrierte Geräte](concept-azure-ad-register.md)

* Die Dokumentation für Endbenutzer zum [Registrieren Ihres persönlichen Geräts im Netzwerk Ihrer Organisation](https://support.microsoft.com/account-billing/register-your-personal-device-on-your-work-or-school-network-8803dd61-a613-45e3-ae6c-bd1ab25bf8a8)

## <a name="azure-ad-join"></a>Azure AD-Einbindung

Azure AD Join ermöglicht es Ihnen, mit Windows zu einem Cloud-First-Modell zu wechseln. Dies ist eine gute Grundlage, wenn Sie Ihre Geräteverwaltung modernisieren und gerätebezogene IT-Kosten senken möchten. Azure AD Join funktioniert nur mit Windows 10-Geräten. Dies ist die erste Wahl für neue Geräte.

[In Azure AD eingebundene Geräte ermöglichen jedoch einmaliges Anmelden für lokale Ressourcen](azuread-join-sso.md), die sich im Netzwerk der Organisation befinden. Diese können sich bei lokalen Servern wie Datei-, Druck- oder anderen Anwendungen authentifizieren.

Wenn dies die beste Option für Ihre Organisation ist, sehen Sie sich die folgenden Ressourcen an:

* Die Übersicht über [in Azure AD eingebundene Geräte](concept-azure-ad-join.md)

* Machen Sie sich mit dem [Azure AD Join-Implementierungsplan](azureadjoin-plan.md) vertraut.

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Bereitstellung von Azure AD Join für Ihre Geräte

Zum Bereitstellen von Azure AD Join stehen Ihnen die folgenden Vorgehensweisen zur Verfügung:

* Self-Service: [Eindruck beim ersten Ausführen von Windows 10](azuread-joined-devices-frx.md)

Wenn Windows 10 Professional oder Windows 10 Enterprise auf einem Gerät installiert ist, wird standardmäßig der Setupprozess für firmeneigene Geräte übernommen.

* [Out of Box Experience (OOBE) von Windows oder über die Windows-Einstellungen](https://support.microsoft.com/account-billing/join-your-work-device-to-your-work-or-school-network-ef4d6adb-5095-4e51-829e-5457430f3973)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Massenregistrierung](/mem/intune/enrollment/windows-bulk-enroll)

Wählen Sie das Bereitstellungsverfahren aus, nachdem Sie [diese Vorgehensweisen sorgfältig verglichen haben](azureadjoin-plan.md).

Möglicherweise stellen Sie fest, dass Azure AD Join die beste Lösung für ein Gerät ist, das sich bereits in anderen Zuständen befindet. Im Folgenden finden Sie einige Überlegungen für ein Upgrade.

| Aktueller Gerätezustand| Gewünschter Gerätezustand| Vorgehensweise |
| - | - | - |
| Eingebundene lokale Domäne| Azure AD Join| Entfernen Sie die Einbindung des Geräts aus der lokalen Domäne vor dem Einbinden in Azure AD. |
| Azure AD Hybrid Join| Azure AD Join| Entfernen Sie die Einbindung des Geräts aus der lokalen Domäne und aus Azure AD vor dem Einbinden in Azure AD. |
| Bei Azure AD registriert| Azure AD Join| Entfernen Sie die Einbindung des Geräts vor dem Einbinden in Azure AD. |


## <a name="hybrid-azure-ad-join"></a>Azure AD-Hybrideinbindung

Wenn Sie in einer lokalen Active Directory-Umgebung Ihre in die Active Directory-Domäne eingebundenen Computer in Azure AD einbinden möchten, kann dies durch Vornehmen einer Einbindung in Azure AD Hybrid Join erfolgen. Dies unterstützt eine [breite Palette von Windows-Geräten](hybrid-azuread-join-plan.md), einschließlich aktueller und kompatibler Windows-Geräte.

Die meisten Organisationen verfügen bereits über in die Domäne eingebundene Geräte und verwalten sie über eine Gruppenrichtlinie oder System Center Configuration Manager (SCCM). In diesem Fall empfiehlt es sich, Azure AD Hybrid Join zu konfigurieren, um mit vorhandenen Investitionen Vorteile zu erzielen.

Wenn Azure AD Hybrid Join die beste Option für Ihre Organisation ist, sehen Sie sich die folgenden Ressourcen an:

* Die Übersicht über [Azure AD Hybrid Join-Geräte](concept-azure-ad-join-hybrid.md)

* Machen Sie sich mit dem [Implementierungsplan für Azure AD Hybrid Join](hybrid-azuread-join-plan.md) vertraut.

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Bereitstellen von Azure AD Hybrid Join für Ihre Geräte

[Überprüfen Sie Ihre Identitätsinfrastruktur](hybrid-azuread-join-plan.md). Azure AD Connect bietet einen Assistenten für die Konfiguration von Azure AD Hybrid Join für Folgendes:

* [Verbunddomänen](hybrid-azuread-join-federated-domains.md)

* [Verwaltete Domänen](hybrid-azuread-join-managed-domains.md)

Wenn die Installation der erforderlichen Version von Azure AD Connect keine Option für Sie ist, informieren Sie sich über die [manuelle Konfiguration von Azure AD Hybrid Join](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> Das lokale in die Domäne eingebundene Windows 10-Gerät versucht, automatisch mit Azure AD verbunden zu werden, damit es standardmäßig in Azure AD Hybrid eingebunden wird. Dies ist nur erfolgreich, wenn Sie die richtige Umgebung eingerichtet haben. 

Möglicherweise stellen Sie fest, dass Azure AD Hybrid Join die beste Lösung für ein Gerät ist, das sich ggf. bereits in einem anderen Zustand befindet. Im Folgenden finden Sie einige Überlegungen für ein Upgrade.

| Aktueller Gerätezustand| Gewünschter Gerätezustand| Vorgehensweise |
| - | - | - |
| Lokale Domäneneinbindung| Azure AD Hybrid Join| Verwenden Sie Azure AD Connect oder AD FS, um Azure beizutreten. |
| Lokale Arbeitsgruppe (beigetreten oder neu)| Azure AD Hybrid Join| Unterstützt mit [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) Andernfalls muss das Gerät vor Azure AD Hybrid Join in eine lokale Domäne eingebunden sein. |
| In Azure AD eingebunden| Azure AD Hybrid Join| Entfernen Sie die Einbindung in Azure AD, was es in eine lokale Arbeitsgruppe oder in einen neuen Zustand versetzt. |
| In Azure AD-registriert| Azure AD Hybrid Join| Hängt von der Windows-Version ab. [Beachten Sie diese Überlegungen](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Verwalten von Geräten

Nachdem Sie Ihre Geräte registriert oder in Azure AD eingebunden haben, verwenden Sie das [Azure-Portal](https://portal.azure.com/) als zentralen Ort zum Verwalten Ihrer Geräteidentitäten. Auf der Azure Active Directory-Geräteseite können Sie folgende Aktionen ausführen:

* [Konfigurieren Ihrer Geräteeinstellungen](device-management-azure-portal.md#configure-device-settings)
* Sie müssen ein lokaler Administrator sein, um Windows-Geräte zu verwalten. [Azure AD aktualisiert diese Mitgliedschaft für in Azure AD eingebundene Geräte](assign-local-admin.md) und fügt diese automatisch der Geräte-Manager-Rolle als Administratoren für alle eingebundenen Geräte hinzu.

Stellen Sie sicher, dass Sie die Umgebung bereinigen, indem Sie [veraltete Geräte verwalten](manage-stale-devices.md), und konzentrieren Sie sich auf Ihre Ressourcen zum Verwalten aktueller Geräte.

* [Überprüfen gerätebezogener Überwachungsprotokolle](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Unterstützte Geräteverwaltungstools

Administratoren können diese registrierten und eingebundenen Geräte mit zusätzlichen Geräteverwaltungstools sichern und steuern. Diese Tools bieten eine Möglichkeit zur Erzwingung der von einer Organisation geforderten Konfigurationen, z. B. Verschlüsselung des Speichers, Kennwortkomplexität, Softwareinstallationen und Softwareupdates. 

Überprüfen Sie die unterstützten und nicht unterstützten Plattformen für integrierte Geräte:

| Geräteverwaltungstools| Bei Azure AD registriert| Azure AD-Einbindung| Azure AD-Hybrideinbindung|
| - | - | - | - |
| [Verwaltung mobiler Geräte (Mobile Device Management, MDM)](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Beispiel: Microsoft Intune| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| 
| [Kombinierte Verwaltung mit Microsoft Intune und Microsoft Endpoint Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 und höher)| | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| 
| [Gruppenrichtlinie](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(nur Windows)| | | ![Häkchen für diese Werte.](./media/plan-device-deployment/check.png)| 



 Ziehen Sie die [mobile Anwendungsverwaltung von Microsoft Intune ](/mem/intune/apps/app-management) mit oder ohne Geräteverwaltung für registrierte iOS- oder Android-Geräte in Erwägung.

 Administratoren können auch [VDI-Plattformen (Virtual Desktop Infrastructure)](howto-device-identity-virtual-desktop-infrastructure.md) bereitstellen, die Windows-Betriebssysteme in ihren Unternehmen hosten, um die Verwaltung zu vereinfachen und die Kosten durch Konsolidierung und Zentralisierung von Ressourcen zu verringern. 

### <a name="troubleshoot-device-identities"></a>Problembehandlung für Geräteidentitäten

* [Problembehandlung von Geräten mit dem Befehl „dsregcmd“](troubleshoot-device-dsregcmd.md)

* [Problembehandlung bei Enterprise State Roaming-Einstellungen in Azure Active Directory](enterprise-state-roaming-troubleshooting.md)

Sollten bei der Azure AD-Hybrideinbindung für in Domänen eingebundene Windows-Geräte Probleme auftreten, finden Sie weitere Informationen unter:

* [Problembehandlung für in Azure AD eingebundene aktuelle Windows-Hybridgeräte](troubleshoot-hybrid-join-windows-current.md)

* [Problembehandlung für Azure AD Hybrid Join für kompatible Windows-Geräte](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nächste Schritte

* [Planen Ihrer Azure AD Join-Implementierung](azureadjoin-plan.md)
* [Planen Ihrer Azure AD Hybrid Join-Implementierung](hybrid-azuread-join-plan.md)
* [Verwalten von Geräteidentitäten](device-management-azure-portal.md)
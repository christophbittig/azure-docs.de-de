---
title: Sicherer Hybridzugriff mit Azure AD und Silverfort
description: In diesem Tutorial wird erläutert, wie Sie Silverfort mit Azure AD integrieren, um sicheren Hybridzugriff zu nutzen.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/13/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429b00a03aaf39fa5eb5657253ee6ee51190049
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274556"
---
# <a name="tutorial-configure-silverfort-with-azure-active-directory-for-secure-hybrid-access"></a>Tutorial: Konfigurieren von Silverfort mit Azure Active Directory für sicheren Hybridzugriff

In diesem Tutorial erfahren Sie, wie Sie Silverfort mit Azure Active Directory (Azure AD) integrieren. Mit [Silverfort](https://www.silverfort.com/) lassen sich mittels innovativer agent- und proxyloser Technologien alle Ihre Ressourcen lokal und in der Cloud mit Azure AD verbinden. Mit dieser Lösung können Organisationen Identitätsschutz, Sichtbarkeit und Benutzerfreundlichkeit auf alle Umgebungen in Azure AD anwenden. Sie ermöglicht die universelle, risikobasierte Überwachung und Bewertung von Authentifizierungsaktivitäten für lokale und Cloudumgebungen und verhindert Bedrohungen proaktiv.  

Mit Silverfort können Ressourcen jeder Art nahtlos in Azure AD eingebunden werden, als handle es sich um eine moderne Webanwendung. Beispiel:

- Legacy- und selbstentwickelte Anwendungen

- Remotedesktop und Secure Shell (SSH)

- Befehlszeilentools und anderer Administratorzugriff

- Dateifreigaben und Datenbanken

- Infrastruktur- und Industriesysteme

Diese **Bridgingressourcen** werden in Azure AD als reguläre Anwendungen angezeigt und können mit bedingtem Zugriff, einmaligem Anmelden (SSO), mehrstufiger Authentifizierung, Überwachung und mehr geschützt werden.

Diese Lösung kombiniert alle Unternehmensressourcen und IAM-Plattformen (Identity & Access Management) von Drittanbietern. Beispielsweise Active Directory, Active Directory-Verbunddienste (AD FS) und RADIUS (Remote Authentication Dial-In User Service) in Azure AD, einschließlich Hybrid- und Multi-Cloud-Umgebungen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Leitfaden konfigurieren und testen Sie das Bridging zwischen Silverfort und Azure AD in Ihrem Azure AD-Mandanten.

Nach der Konfiguration können Sie Silverfort-Authentifizierungsrichtlinien erstellen, die Authentifizierungsanforderungen aus verschiedenen Identitätsquellen zu Azure AD für SSO überbrücken. Nach der Überbrückung einer Anwendung kann diese in Azure AD verwaltet werden.

Im folgenden Diagramm sind die in der Lösung enthaltenen Komponenten und die in Silverfort orchestrierte Authentifizierungsabfolge dargestellt.

![Abbildung zeigt das Architekturdiagramm](./media/silverfort-azure-ad-integration/silverfort-architecture-diagram.png)

| Schritt | BESCHREIBUNG|
|:---------|:------------|
| 1. | Sie senden eine Authentifizierungsanforderung über Protokolle wie Kerberos, SAML, NTLM, OIDC und LDAP an den ursprünglichen Identitätsanbieter (Identity Provider, IdP).|
| 2. | Die Antwort wird unverändert zur Überprüfung des Authentifizierungsstatus an Silverfort weitergeleitet.|
| 3. | Silverfort ermöglicht Sichtbarkeit, Ermittlung und Bridging in Azure AD.|
| 4. | Wenn die Anwendung als **Überbrückung** konfiguriert ist, wird die Authentifizierungsentscheidung an Azure AD übergeben. Azure AD wertet Richtlinien für bedingten Zugriff aus und überprüft die Authentifizierung.|
| 5. | Die Antwort zum Authentifizierungsstatus wird dann freigegeben und von Silverfort unverändert an den Identitätsanbieter gesendet. |
| 6.| Der Identitätsanbieter gewährt oder verweigert den Zugriff auf die Ressource.|
| 7. | Sie werden benachrichtigt, wenn die Zugriffsanforderung gewährt oder verweigert wird. |

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um SSO für eine Anwendung einzurichten, die Sie Ihrem Azure AD-Mandanten hinzugefügt haben:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.

- Eine der folgenden Rollen in Ihrem Azure-Konto: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

- Eine Anwendung, die SSO unterstützt und bereits vorkonfiguriert und dem Azure AD-Katalog hinzugefügt wurde. Die Silverfort-Anwendung im Azure AD-Katalog ist bereits vorkonfiguriert. Sie müssen sie als Unternehmensanwendung aus dem Katalog hinzufügen.

## <a name="onboard-with-silverfort"></a>Onboarding mit Silverfort

[Wenden Sie sich an Silverfort](https://www.silverfort.com/), um Silverfort in Ihrem Mandanten oder Ihrer Infrastruktur bereitzustellen. Installieren Sie die Silverfort Desktop-App auf den relevanten Arbeitsstationen.

## <a name="configure-silverfort-and-create-a-policy"></a>Konfigurieren von Silverfort und Erstellen einer Richtlinie

1. Melden Sie sich in einem Browser bei der **Silverfort-Verwaltungskonsole** an.

2. Navigieren Sie im Hauptmenü zu **Settings** (Einstellungen), und scrollen Sie dann im Abschnitt „General“ (Allgemein) zu **Azure AD Bridge Connector**. Bestätigen Sie Ihre Mandanten-ID, und wählen Sie dann **Authorize** (Autorisieren) aus.

   ![Abbildung des Azure AD Bridge Connectors](./media/silverfort-azure-ad-integration/azure-ad-bridge-connector.png)

   ![Abbildung der Registrierungsbestätigung](./media/silverfort-azure-ad-integration/grant-permission.png)

3. Auf einer neuen Registerkarte wird eine Registrierungsbestätigung angezeigt. Schließen Sie diese Registerkarte.

   ![Abbildung der abgeschlossenen Registrierung](./media/silverfort-azure-ad-integration/registration-completed.png)

4. Wählen Sie auf der Seite **Settings** (Einstellungen) die Option **Save changes** (Änderungen speichern) aus.

   ![Abbildung des Azure AD-Adapters](./media/silverfort-azure-ad-integration/silverfort-azure-ad-adapter.png)

    Melden Sie sich bei Ihrer Azure AD-Konsole an. Sie können sehen, dass die Anwendung **Silverfort Azure AD Adapter** als Unternehmensanwendung registriert ist.

   ![Abbildung der Unternehmensanwendung](./media/silverfort-azure-ad-integration/enterprise-application.png)

5. Navigieren Sie in der Silverfort-Verwaltungskonsole zur Seite **Policies** (Richtlinien), und wählen Sie **Create Policy** (Richtlinie erstellen) aus.

6. Das Dialogfeld **New Policy** (Neue Richtlinie) wird angezeigt. Geben Sie einen **Richtliniennamen** ein, mit dem der in Azure erstellte Anwendungsname angegeben wird. Wenn Sie z. B. mehrere Server oder Anwendungen unter der Richtlinie hinzufügen, benennen Sie sie entsprechend den Ressourcen, die von der Richtlinie abgedeckt werden. In diesem Beispiel erstellen Sie eine Richtlinie für den Server *SL-APP1*.

   ![Abbildung des Definierens der Richtlinie](./media/silverfort-azure-ad-integration/define-policy.png)

7. Wählen Sie unter **Auth Type** (Authentifizierungstyp) und unter **Protocol** (Protokoll) einen geeigneten Wert aus.

8. Wählen Sie im Feld **Users and Groups** (Benutzer und Gruppen) das Bearbeitungssymbol aus, um Benutzer zu konfigurieren, auf die sich die Richtlinie auswirkt. Die Authentifizierung dieser Benutzer wird zu Azure AD überbrückt.

   ![Abbildung von „Users and Groups“ (Benutzer und Gruppen)](./media/silverfort-azure-ad-integration/user-groups.png)

9. Suchen Sie Benutzer (Users), Gruppen (Groups) oder Organisationseinheiten (Organization units, OUs), und wählen Sie sie aus.

   ![Abbildung der Suche von Benutzern](./media/silverfort-azure-ad-integration/search-users.png)

   Die ausgewählten Benutzer werden im Feld SELECTED (Ausgewählt) angezeigt.

   ![Abbildung des ausgewählten Benutzers](./media/silverfort-azure-ad-integration/select-user.png)

10. Wählen Sie die **Source** (Quelle) aus, für die die Richtlinie gilt. In diesem Beispiel ist *All Devices* (Alle Geräte) ausgewählt.

    ![Abbildung der Quelle](./media/silverfort-azure-ad-integration/source.png)

11. Legen Sie das Ziel (**Destination**) auf *SL-App1* fest. Sie können die Bearbeitungsschaltfläche auswählen, um weitere Ressourcen oder Ressourcengruppen zu ändern oder hinzuzufügen (optional).

    ![Abbildung des Ziels](./media/silverfort-azure-ad-integration/destination.png)

12. Wählen Sie die Aktion für **AZURE AD BRIDGE** aus.

    ![Abbildung des Speicherns von Azure AD Bridge](./media/silverfort-azure-ad-integration/save-azure-ad-bridge.png)

13. Wählen Sie **SAVE** (Speichern) aus, um die neue Richtlinie zu speichern. Sie werden aufgefordert, die Richtlinie zu aktivieren.

    ![Abbildung des geänderten Status](./media/silverfort-azure-ad-integration/change-status.png)

    Die Richtlinie wird auf der Seite „Policies“ (Richtlinien) im Abschnitt „Azure AD Bridge“ angezeigt:

    ![Abbildung des Hinzufügens der Richtlinie](./media/silverfort-azure-ad-integration/add-policy.png)

14. Kehren Sie zur Azure AD-Konsole zurück, und navigieren Sie zu **Unternehmensanwendungen**. Die neue Silverfort-Anwendung sollte nun angezeigt werden. Die Anwendung kann jetzt in [Richtlinien für bedingten Zugriff](../authentication/tutorial-enable-azure-mfa.md?bc=/azure/active-directory/conditional-access/breadcrumb/toc.json&toc=/azure/active-directory/conditional-access/toc.json%23create-a-conditional-access-policy) eingefügt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Silverfort Azure AD Adapter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.silverfortazureadadapter?tab=overview)

- [Silverfort-Ressourcen](https://www.silverfort.com/resources/)

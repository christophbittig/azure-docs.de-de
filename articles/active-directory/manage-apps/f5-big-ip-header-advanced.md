---
title: Integration von des einmaliges Anmelden (SSO) von Azure Active Directory mit F5 BIG-IP für eine headerbasierte Authentifizierung
description: Erfahren Sie, wie Sie den BIG-IP-Zugriffsrichtlinienverwalter (Access Policy Manager, APM) von F5 und das einmalige Anmelden (SSO) vom Azure Active Directory für eine headerbasierte Authentifizierung integrieren.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/10/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e66556e7233779d9fa7f9e2746839147cea42d54
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350333"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-with-f5-big-ip-for-header-based-authentication"></a>Tutorial: Integration von Azure Active Directory mit einmaligem Anmelden (Single sign-on, SSO) mit F5 BIG-IP für eine headerbasierte Authentifizierung

In diesem Tutorial erfahren Sie, wie Sie den BIG-IP-Zugriffsrichtlinienverwalter (Access Policy Manager, APM) von F5 und das Azure Active Directory (Azure AD) für einen sicheren Hybridzugriff auf headerbasierte Anwendungen integrieren.

Die Integration von veröffentlichten BIG-IP-Anwendungen in Azure AD bietet viele Vorteile, darunter:

- Verbesserte Zero Trust-Governance durch die Vorauthentifizierung und Autorisierung durch Azure AD

- Vollständiges einmaliges Anmelden (Single Sign-On, SSO) zwischen Azure AD und veröffentlichten BIG-IP-Diensten

- Verwalten der Identitäten und des Zugriffs über eine zentrale Steuerungsebene: das [Azure-Portal](https://azure.microsoft.com/features/azure-portal)

Informationen zu allen Vorteilen finden Sie im Abschnitt [F5-BIG-IP- und Azure-AD-Integration](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration) und [Was bedeutet ein anwendungsbasierter Zugriff und einmaliges Anmelden bei Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Szenario verfügen wir über eine interne Anwendung, deren Zugriff auf den Empfang von HTTP-Autorisierungsheadern von einem bestehenden Maklersystem basiert. Dadurch können Benutzer auf ihre jeweiligen Inhaltsbereiche weitergeleitet werden.

Das ideale Fallszenario ist, die Anwendung direkt über Azure AD verwalten und steuern zu lassen. Da es jedoch an jeglicher Form moderner Interoperabilität zwischen den Protokollen mangelt, wäre eine Modernisierung mit erheblichem Zeit- und Kostenaufwand verbunden und würde unvermeidliche Kosten und Risiken durch mögliche Ausfallzeiten mit sich bringen.

Stattdessen wird eine BIG-IP Virtual Edition (VE) verwendet, die zwischen dem öffentlichen Internet und dem internen Azure-VNET bereitgestellt wird, mit dem die Anwendung verbunden ist. Sie ermöglicht es, den eingehenden Zugriff mit Azure AD für dessen umfangreiche Auswahl an Authentifizierungs- und Autorisierungsfunktionen zu verknüpfen.

Eine BIG-IP-Adresse vor der Anwendung ermöglicht es, den Dienst mit einer Azure-AD-Vorab-Authentifizierung und headerbasiertem SSO zu überlagern. Dadurch wird der allgemeine Sicherheitsstatus der Anwendung erheblich verbessert, sodass das Unternehmen ohne Unterbrechung zügig weiterarbeiten kann.

Die sichere Hybridzugriffslösung für dieses Szenario besteht aus den folgenden Komponenten:

- **Anwendung**: Back-End-Dienst, der durch Azure-AD- und sicheren BIG-IP-Hybridzugriff geschützt wird

- **Azure AD**: Der SAML-Identitätsanbieter (IDP), der für die Überprüfung der Benutzeranmeldeinformationen, des bedingten Zugriffs (Conditional Access, CA) und des einmaligen Anmeldens bei big-IP-APM verantwortlich ist.

- **BIG-IP**: Reverse Proxy und SAML Service Provider (SP) für die Anwendung, wobei die Authentifizierung an den SAML IdP delegiert wird, bevor ein headerbasiertes einmaliges Anmelden für die Backend-Anwendung durchgeführt wird.

![Screenshot des Architekturflussdiagramms](./media/f5-big-ip-header-advanced/flow-diagram.png)

| Schritt | BESCHREIBUNG |
|:-------|:-----------|
| 1. | Der Benutzer stellt eine Verbindung mit dem SAML SP-Endpunkt der Anwendung (BIG-IP APM) her. |
| 2. | BIG-IP leitet den Benutzer zur Vorabauthentifizierung an den SAML-Identitätsanbieter (Azure AD) weiter.|
| 3. | SAML IdP authentifiziert den Benutzer und wendet alle geltenden Richtlinien für Zertifizierungsstellen an. |
| 4. | Azure AD leitet den Benutzer mit ausgestellten Token und Ansprüchen zurück an SAML SP. |
| 5. | BIG-IP APM gewährt Benutzerzugriff und fügt Header in die Anforderung an die Anwendung ein. |

Um die Sicherheit zu erhöhen, könnten Organisationen, die dieses Muster verwenden, auch erwägen, den direkten Zugriff auf die Anwendung zu blockieren, sodass ein strikter Pfad über die BIG-IP erzwungen wird.

## <a name="prerequisites"></a>Voraussetzungen

Vorherige Erfahrungen mit BIG-IP sind nicht erforderlich, aber Sie benötigen:

- Eine kostenloses Abonnement (oder höher) von Azure AD

- Eine vorhandene BIG-IP-Adresse oder das [Bereitstellen einer BIG-IP Virtual Edition (VE) in Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-bigip-deployment-guide)

- Eine der folgenden F5 BIG-IP-Lizenz-SKUs

  - F5 BIG-IP® Best Bundle

  - Eigenständige Lizenz für F5 BIG-IP Access Policy Manager™ (APM)

  - Add-On-Lizenz für F5 BIG-IP Access Policy Manager™ (APM) für eine bereits vorhandene Instanz von F5 BIG-IP® Local Traffic Manager™ (LTM)

  - 90-Tage-[Testlizenz](https://www.f5.com/trial/big-ip-trial.php) für sämtliche Features von BIG-IP

- Benutzeridentitäten, die aus dem lokalen Verzeichnis mit Azure AD [synchronisiert](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) wurden

- Ein Konto mit den [Berechtigungen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator) eines Azure AD-Anwendungsadministrators

- [SSL-Zertifikat](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-bigip-deployment-guide#ssl-profile) zum Veröffentlichen von Diensten über HTTPS oder Verwenden von Standardzertifikaten für Testzwecke

- Eine vorhandene headerbasierte Anwendung oder [Einrichten einer IIS-Head-App](https://docs.microsoft.com/previous-versions/iis/6.0-sdk/ms525396(v=vs.90)) für Testzwecke

## <a name="deployment-modes"></a>Bereitstellungsmodi

Es gibt mehrere Methoden zum Konfigurieren einer BIG-IP-Adresse für dieses Fallbeispiel, unter anderem mehrere assistentenbasierte Optionen oder eine erweiterte Konfiguration.

In diesem Tutorial wird der erweiterte Ansatz behandelt, der einen flexibleren Ansatz für die Implementierung eines sicheren Hybridzugriffs bietet, indem alle BIG-IP-Konfigurationsobjekte manuell erstellt werden. Sie würden diesen Ansatz auch in Fällen verwenden, die nicht von der geführten Konfiguration abgedeckt werden.

>[!NOTE]
>Alle Beispielzeichenfolgen und -werte in diesem Artikel sollten durch die tatsächlichen Werte für Ihre Umgebung ersetzt werden.

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Hinzufügen von F5 BIG-IP aus dem Azure AD-Anwendungskatalog

Das Einrichten einer SAML-Verbundvertrauensstellung zwischen BIG-IP APM und Azure AD ist einer der ersten Schritte bei der Implementierung des sicheren Hybridzugriffs. Es legt die Integration fest, die für BIG-IP erforderlich ist, um die Vorab-Authentifizierung und [bedingten Zugriff](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) an Azure AD zu übergeben, bevor Zugriff auf den veröffentlichten Dienst gewährt wird.

1. Melden Sie sich beim Azure-AD-Portal mit einem Konto mit Administratorrechten für die Anwendung an.

2. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.

3. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie auf dem oberen Menüband **+ Neue Anwendung** aus.

4. Suchen Sie im Katalog nach **F5**, und wählen Sie die **Azure AD-Integration für F5 BIG-IP APM** aus.

5. Geben Sie einen Namen für die Anwendung an, und wählen Sie dann **Hinzufügen/Erstellen** aus, um sie Ihrem Mandanten hinzuzufügen. Der Name sollte den jeweiligen Dienst widerspiegeln,

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD 

1. Navigieren Sie in der Ansicht der neuen **F5**-Anwendungseigenschaften zu **Verwalten** > **Einmaliges Anmelden** (Manage > Single sign-on).

2. Wählen Sie auf der Seite **Methode für einmaliges Anmelden auswählen** die Option **SAML** aus und überspringen Sie die Aufforderung zum Speichern der Einstellungen für einmaliges Anmelden, indem Sie **Nein, ich speichere es später** auswählen.

3. Wählen Sie im Menü **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die folgenden Informationen anzugeben:

   a. Ersetzen Sie die vordefinierte **Bezeichner-URL** durch die URL für Ihren veröffentlichten BIG-IP-Dienst. Zum Beispiel, `https://mytravel.contoso.com`

   b. Gehen Sie mit der **Antwort-URL** genauso vor, aber geben Sie den Pfad für den SAML-Endpunkt von APM an. Zum Beispiel, `https://mytravel.contoso.com/saml/sp/profile/post/acs`

   >[!NOTE]
   >In dieser Konfiguration würde der SAML-Fluss im IdP-initiierten Modus arbeiten, in dem Azure AD dem Benutzer eine SAML-Assertion ausstellt, bevor er an den BIG-IP-Serviceendpunkt für die Anwendung weitergeleitet wird. Big-IP APM unterstützt sowohl den IdP- als auch den SP-initiierten Modus.

   c. Geben Sie für `Logout URI` den SLO-Endpunkt (Single Logout, einmaliges Abmelden) des BIG-IP APM ein, der durch den Hostheader des veröffentlichten Diensts vorangestellt wird. Durch die Bereitstellung eines SLO-URI wird sichergestellt, dass die BIG-IP-APM-Sitzung des Benutzers beendet wurde, nachdem er sich von Azure AD abgemeldet hat. Zum Beispiel, `https://mytravel.contoso.com/saml/sp/profile/redirect/slr`

    ![Screenshot der grundlegenden SAML-Konfiguration](./media/f5-big-ip-header-advanced/basic-saml-configuration.png)

    >[!Note]
    >Von TMOS v16 wurde der SAML-SLO-Endpunkt in `/saml/sp/profile/redirect/slo` geändert.

4. Wählen Sie vor dem Beenden der SAML-Konfigurations-Karte **Speichern** aus und überspringen Sie die SSO-Testaufforderung.

5. Wählen Sie das Stiftsymbol aus, um die **Benutzerattribute & Ansprüche > + Neuen Anspruch hinzufügen** zu bearbeiten.

6. Legen Sie die Anspruchseigenschaften wie folgt fest, und wählen Sie dann **Speichern** aus.

   | Eigenschaft |Beschreibung|
   |:------|:---------|
   |Name | Employeeid |
   | Quellattribut | user.employeeid |

   ![Screenshot: Verwalten der Anspruchskonfiguration](./media/f5-big-ip-header-advanced/manage-claims.png)

7. Wählen Sie **+ Gruppenanspruch hinzufügen** und dann **der Anwendung zugewiesene Gruppen** > **Quellattribut** > **sAMAccountName** (sAMAKontoname) aus.

   ![Screenshot: Konfiguration von Gruppenansprüchen](./media/f5-big-ip-header-advanced/group-claims.png)

8. **Speichern** Sie die Konfiguration und schließen Sie die  Karte.

   Beachten Sie die Eigenschaften des Abschnitts **Benutzerattribute & Ansprüche.** Azure AD gibt Benutzern diese Eigenschaften für die BIG-IP-APM-Authentifizierung und das einmalige Anmelden für die Back-End-Anwendung aus:

   ![Screenshot: Benutzerattribute und Anspruchskonfiguration](./media/f5-big-ip-header-advanced/user-attributes-claims.png)

   Sie können auch andere spezifische Ansprüche hinzufügen, die Ihre veröffentlichte BIG-IP-Anwendung möglicherweise als Header erwartet. Ansprüche, die zusätzlich zum Standardsatz definiert werden, werden nur ausgegeben, wenn sie in Azure AD vorhanden sind. Ebenso müssen Mitgliedschaften von [Rollen oder Gruppen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) im Verzeichnis auch für ein Benutzerobjekt in Azure AD definiert werden, bevor sie als Anspruch ausgestellt werden können.

9. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** die Schaltfläche **Download** aus, um die Datei **Verbund-XML-Metadaten** auf Ihrem Computer zu speichern.

   ![Screenshot: Saml-Signaturzertifikat](./media/f5-big-ip-header-advanced/saml-signing-certificate.png)

Von Azure AD erstellte SAML-Signaturzertifikate haben eine Lebensdauer von 3 Jahren und sollten mithilfe des veröffentlichten [Leitfadens](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on) verwaltet werden.

### <a name="azure-ad-authorization"></a>Azure AD-Autorisierung

Standardmäßig gibt Azure AD nur Token für Benutzer aus, denen der Zugriff auf eine Anwendung gewährt wurde.

1. Wählen Sie in der Konfigurationsansicht der Anwendung die Option **Benutzer und Gruppen** aus.

2. Wählen Sie **+** **Benutzer hinzufügen** und anschließend auf der Karte **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

3. Fügen Sie im Dialogfeld **Benutzer und Gruppen** die Gruppen von Benutzern hinzu, die für den Zugriff auf die interne headerbasierte Anwendung autorisiert sind. Wählen Sie danach **Auswählen** > **Zuweisen** aus.

Damit ist der Teil der SAML-Verbundvertrauensstellung in Azure AD abgeschlossen. Der BIG-IP APM kann nun so eingerichtet werden, dass die interne Webanwendung veröffentlicht und mit den entsprechenden Eigenschaften konfiguriert wird, um die Vertrauensstellung für die SAML-Vorabauthentifizierung abzuschließen.

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

### <a name="saml-configuration"></a>SAML-Konfiguration

In den folgenden Schritten werden der BIG-IP-SAML-Dienstanbieter und die entsprechenden SAML-IdP-Objekte erstellt, die zum Abschließen des Verbunds der veröffentlichten Anwendung mit Azure AD erforderlich sind.

1. Wählen Sie **Access** > **Federation** > **SAML Service Provider** > **Local SP Services** > **Create** aus.

   ![Screenshot: Erstellen eines SAML-Dienstanbieters](./media/f5-big-ip-header-advanced/create-saml-sp.png)

2. **Geben Sie einen Namen** und genau dieselbe **Entitäts-ID** an, die in Azure AD zuvor definiert wurde.

   ![Screenshot des neuen SAML-Dienstanbieterdiensts ](./media/f5-big-ip-header-advanced/new-saml-sp-information.png)

   Die **Einstellungen für den SP-Namen** sind nur erforderlich, wenn die Entitäts-ID nicht exakt mit dem Teil des Hostnamens der veröffentlichten URL übereinstimmt oder wenn kein reguläres Format für die Hostnamen-URL verwendet wird. Geben Sie das externe Schema und den Hostnamen der zu veröffentlichenden Anwendung an, wenn die Entitäts-ID `urn:mytravel:contosoonline` ist.

3. Scrollen Sie nach unten zum neuen SAML-SP-Objekt und wählen Sie **IDP-Connectors binden/trennen** (Bind/UnBind IDP Connectors) aus.

   ![Screenshot: Neue SAML-Dienstanbieter-Objektconnectors](./media/f5-big-ip-header-advanced/idp-connectors.png)

4. Wählen Sie **Neuen IDP-Connector erstellen** (Create New IDP Connector) und dann im Dropdownmenü den Eintrag **Aus Metadaten** (From Metadata) aus.

   ![Screenshot: Bearbeiten der neuen SAML-Dienst-idp](./media/f5-big-ip-header-advanced/edit-saml-idp.png)

5. Navigieren Sie zur XML-Datei der Verbundmetadaten, die Sie zuvor heruntergeladen haben, und geben Sie für das APM-Objekt, das den externen SAML-IdP darstellt, einen **Identitätsanbieternamen** (Identity Provider Name) an. Zum Beispiel, `MyTravel_AzureAD`

   ![Screenshot: Neuer idp-Connector](./media/f5-big-ip-header-advanced/idp-name.png)

6. Wählen Sie **Neue Zeile hinzufügen** aus, um den neuen **SAML IdP-Connector** und dann **Update** auszuwählen.

   ![Screenshot: Aktualisieren des idp-Connectors](./media/f5-big-ip-header-advanced/update-idp-connector.png)

7. Wählen Sie **OK**, um die Einstellungen zu speichern.

   ![Screenshot: Speichern der Einstellungen](./media/f5-big-ip-header-advanced/save-settings.png)

### <a name="header-sso-configuration"></a>Konfiguration der Header-SSO

Erstellen Sie ein APM-SSO-Objekt zum Durchführen des einmaligen Anmeldens von Headern für die Back-End-Anwendung.

1. Wählen Sie **Zugriff** > **Zugriffsprofile/Richtlinien** > **pro Anforderungsrichtlinien** > **Erstellen** aus.

2. Geben Sie einen eindeutigen Profilnamen an, fügen Sie mindestens eine **Akzeptierte Sprache** hinzu und wählen Sie dann **Fertigstellen** aus. Beispiel: SSO_Headers

   ![Screenshot: Headerkonfiguration](./media/f5-big-ip-header-advanced/header-configuration.png)

3. Wählen Sie den Link **Bearbeiten** für die neue Richtlinie pro Anforderung aus, die Sie soeben erstellt haben.

    ![Screenshot: Richtlinie pro Anforderung bearbeiten](./media/f5-big-ip-header-advanced/header-configuration-edit.png)

4. Nachdem der Editor für visuelle Richtlinien gestartet wurde, wählen Sie das Symbol **+** neben dem Fallback aus.

    ![Screenshot des Editors für visuelle Richtlinien](./media/f5-big-ip-header-advanced/visual-policy-editor.png)

5. Wechseln Sie im Popup-Fenster zur Registerkarte **Universell**, um **HTTP-Header**  > **Element hinzufügen** auszuwählen.

    ![Screenshot: Hinzufügen eines Elements mit HTTP-Header](./media/f5-big-ip-header-advanced/add-item.png)

6. Wählen Sie **Neuen Eintrag hinzufügen** aus, um drei separate Einträge **HTTP** **-Header-Änderung** zu erstellen, indem Sie Folgendes verwenden:

   | Eigenschaft | Beschreibung |
   |:------|:-------|
   | Headername | upn |
   | Headerwert | %{session.saml.last.identity}|
   | Headername | employeeid |
   | Headerwert | %{session.saml.last.attr.name.employeeid} |
   | Headername | group\_authz |
   | Headerwert | %{session.saml.last.attr.name.`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`} |

   >[!Note]
   >Bei APM-Sitzungsvariablen, die in geschweiften Klammern definiert sind, wird zwischen Groß- und Kleinschreibung unterschieden. So führt die Eingabe „EmployeeID“, wenn der Azure AD-Attributs-Name als „employeeid“ gesendet wird, zu einem Attributs-Zuordnungsfehler. Sofern nicht erforderlich, wird empfohlen, alle Attribute in Kleinbuchstaben zu definieren.

   ![Screenshot: Änderung des HTTP-Headers](./media/f5-big-ip-header-advanced/http-header-modify.png)

7. Wählen Sie anschließend **Speichern** aus und schließen Sie den Editor für visuelle Richtlinien.

   ![Screenshot: Pro-Anforderungsrichtlinie abgeschlossen und gespeichert](./media/f5-big-ip-header-advanced/per-request-policy-done.png)

### <a name="access-profile-configuration"></a>Konfigurieren von Zugriffsprofilen

Ein Zugriffsprofil bindet viele APM-Elemente, die den Zugriff auf virtuelle BIG-IP-Server verwalten, einschließlich Zugriffsrichtlinien, SSO-Konfiguration und Benutzeroberflächeneinstellungen.

1. Navigieren Sie zu **Zugriff** > **Zugriffsprofile / Richtlinien** > **Zugriffsprofile (Pro-Sitzungs-Richtlinien)**  > **Erstellen** aus, um die folgenden Informationen anzugeben, und wählen Sie anschließend **Fertiggestellt** aus:

   | Eigenschaft | Beschreibung |
   |:--------|:----------|
   | Name | MyTravel |
   | Profiltyp | Alle |
   | Akzeptierte Sprache | Fügen Sie mindestens eine Sprache hinzu|

   ![Screenshot: Konfiguration des Zugriffsprofils](./media/f5-big-ip-header-advanced/access-profile-configuration.png)

2. Wählen Sie den Link **Bearbeiten** für das Pro-Sitzungs-Profil aus, das Sie gerade erstellt haben.

    ![Screenshot: Bearbeiten des Pro-Sitzungs-Profil](./media/f5-big-ip-header-advanced/edit-per-session-profile.png)

3. Nachdem der Editor für visuelle Richtlinien gestartet wurde, wählen Sie das Symbol **+** neben dem Fallback aus.

   ![Screenshot: Starten des Editors für visuelle Richtlinien](./media/f5-big-ip-header-advanced/visual-policy-editor-launch.png)

4. Wählen Sie im Popup **Authentifizierung** > **SAML-Authentifizierung** > **Element hinzufügen** (Add item) aus.

   ![Screenshot: Hinzufügen der SAML-Authentifizierung](./media/f5-big-ip-header-advanced/add-saml-auth.png)

5. Wählen Sie für die Konfiguration des **SAML-Authentifizierungs-SP** die Option **AAA-Server**, um das zuvor von Ihnen erstellte SAML-SP-Objekt zu verwenden, und wählen Sie dann **Speichern** aus.

   ![Screenshot: Verwenden des AAA-Servers für die SAML-Authentifizierungs-SP](./media/f5-big-ip-header-advanced/aaa-server.png)

### <a name="attribute-mapping"></a>Attributzuordnung

Das Hinzufügen einer LogonID_Mapping-Konfiguration ist zwar optional, ermöglicht aber, dass in der BIG-IP-Liste der aktiven Sitzungen der UPN des angemeldeten Benutzers anstelle einer Sitzungsnummer angezeigt wird Dies ist nützlich für die Analyse von Protokollen oder die Problembehandlung.

1. Wählen Sie das Symbol **+** für den Zweig (Branch) SAML-Authentifizierung **Erfolgreich** aus.

    ![Screenshot: Erstellen eines SAML-Authentifizierungszweigs](./media/f5-big-ip-header-advanced/create-saml-auth-branch.png)

2. Wählen Sie im Popupfenster **Zuweisung** > **Variable zuweisen** > **Element hinzufügen** aus.

   ![Screenshot: Zuweisen einer Variablen](./media/f5-big-ip-header-advanced/assign-variable.png)

3. Geben Sie einen beschreibenden Namen an und wählen Sie im Abschnitt **Variable zuweisen** die Option **Neue Eintrag hinzufügen** > **ändern** aus. Beispiel: LogonID_Mapping.

   ![Screenshot: Hinzufügen eines neuen Eintrags](./media/f5-big-ip-header-advanced/assign-variable-change.png)

4. Legen Sie die beiden Variablen so fest, dass Folgendes verwendet wird, dann **Fertiggestellt** >
    **Speichern** wählen:

   | Eigenschaft | BESCHREIBUNG |
   |:--------|:----------|
   | Benutzerdefinierte Variable | session.saml.last.identity |
   | Sitzungsvariable | session.logon.last.username |

5. Wählen Sie das Terminal **Verweigern** des Zugriffsrichtlinien-Zweigs **Erfolgreich** aus und ändern Sie ihn in **Zulassen** ab, anschließend **Speichern**.

6. Bestätigen Sie diese Einstellungen, indem Sie **Zugriffsrichtlinie anwenden** (Apply Access Policy) auswählen, und schließen Sie dann die Registerkarte des Editors für visuelle Richtlinien.

### <a name="backend-pool-configuration"></a>Konfiguration des Back-End-Pools

Damit BIG-IP weiß, wohin der Clientdatenverkehr weitergeleitet werden soll, müssen Sie ein APM-Knotenobjekt erstellen, das den Back-End-Server darstellt, der Ihre Anwendung hosten soll, und dann müssen Sie diesen Knoten in einem APM-Pool platzieren.

1. Wählen Sie **Lokalen Datenverkehr > Pools > Pool List > Erstellen** aus und geben Sie einen Namen für ein Server-Pool-Objekt an. Beispiel: MyApps_VMs

      ![Screenshot: Anwenden der Zugriffsrichtlinie](./media/f5-big-ip-header-advanced/apply-access-policy.png)

2. Fügen Sie ein Poolmitgliedsobjekt mit den folgenden Angaben hinzu:

    | Eigenschaft | BESCHREIBUNG |
    |:--------|:----------|
    | Node Name (Knotenname) | Optionaler Anzeigename für den Server, der die Back-End-Webanwendung hostet |
    | Adresse | IP-Adresse des Servers, der die Anwendung hostet|
    | Service Port (Dienstport) | Der HTTP/S-Port, den die Anwendung abhört |

    ![Screenshot: Hinzufügen eines Poolmitgliedsobjekts](./media/f5-big-ip-header-advanced/add-object.png)

>[!NOTE]
>Integritätsmonitore erfordern eine zusätzliche [Konfiguration](https://support.f5.com/csp/article/K13397), die in diesem Tutorial nicht behandelt wird.

## <a name="virtual-server-configuration"></a>Konfiguration des virtuellen Servers

Ein virtueller Server ist ein BIG-IP-Datenebenenobjekt, das durch eine virtuelle IP-Adresse repräsentiert wird und auf Client-Anfragen an die Anwendung wartet. Jeder empfangene Datenverkehr wird verarbeitet und anhand des APM-Zugriffsprofils ausgewertet, das dem virtuellen Server zugeordnet ist, bevor er gemäß den Richtlinienergebnissen und -einstellungen weitergeleitet wird.

1. Wählen Sie **Lokaler Datenverkehr** > **Virtuelle Server** > **Liste der virtuellen Server** > **Erstellen** (Local Traffic > Virtual Servers > Virtual Server List > Create) aus.

2. Geben Sie dem virtuellen Server einen **Namen**, eine nicht verwendete IP-Adresse IPv4 / IPv6, die der BIG-IP zugewiesen werden kann, um Clientdatenverkehr zu empfangen, und legen Sie den **Dienstport** auf 443 fest.

   ![Screenshot: Hinzufügen eines neuen virtuellen Servers](./media/f5-big-ip-header-advanced/new-virtual-server.png)

3. **HTTP-Profil**: Legen Sie dieses auf http fest.

4. **SSL-Profil (Client)** : Aktiviert die Transport Layer Security (TLS, Transportschichtsicherheit) und ermöglicht die Veröffentlichung von Diensten über HTTPS. Wählen Sie das Client-SSL-Profil aus, das Sie als Teil der Vorbedingungen erstellt haben, oder belassen Sie die Standardeinstellung, wenn Sie testen.

   ![Screenshot des SSL-Profilclients](./media/f5-big-ip-header-advanced/ssl-profile.png)

5. Ändern Sie die Option **Quelladressenübersetzung** in **Automatische Zuordnung**.

   ![Screenshot der Option "Automatische Zuordnung"](./media/f5-big-ip-header-advanced/change-source-address.png)

6. Legen Sie unter **Zugriffsrichtlinie** das zuvor erstellte **Zugriffsprofil** fest. Dadurch werden das Azure AD-SAML-Vorabauthentifizierungsprofil und die Header-SSO-Richtlinie an den virtuellen Server gebunden.

   ![Screenshot: Festlegen des Zugriffsprofils](./media/f5-big-ip-header-advanced/set-access-profile.png)

7. Legen Sie abschließend den **Standardpool** auf das Back-End-Poolobjekt fest, das sie im vorherigen Abschnitt erstellt haben, dann wählen Sie **Fertiggestellt** aus.

   ![Screenshot: Festlegen des Standardpools](./media/f5-big-ip-header-advanced/default-pool.png)

## <a name="session-management"></a>Sitzungsverwaltung

Eine BIG-IP-Sitzungsverwaltungseinstellung wird verwendet, um die Bedingungen zu definieren, unter denen Benutzersitzungen beendet oder fortgesetzt werden dürfen, sowie Grenzwerte für Benutzer und IP-Adressen sowie Fehlerseiten festzulegen. Sie können Ihre eigene Richtlinie erstellen, indem Sie auf **Zugriffsrichtlinie** > **Zugriffsprofile** klicken und Ihre Anwendung aus der Liste auswählen.

Im Hinblick auf die SLO-Funktionalität wird durch die Definition einer URI für einmaliges Abmelden (Single Log-Out) in Azure AD sichergestellt, dass eine vom IdP initiierte Abmeldung vom MyApps-Portal auch die Sitzung zwischen dem Client und dem BIG-IP-APM beendet. Nach dem Import der Federation-Metadaten der Anwendung stellt xml dem APM den Azure AD SAML-Logout-Endpunkt für SP-initiierte Sign-Outs zur Verfügung. Damit dies wirklich effektiv ist, muss der APM genau wissen, wann sich ein Benutzer abmeldet.

Nehmen wir ein Szenario, in dem ein BIG-IP-Webportal nicht verwendet wird und der Benutzer keine Möglichkeit hat, den APM anzuweisen, sich abzumelden. Selbst wenn sich der Benutzer von der Anwendung selbst abmeldet, ist dies für BIG-IP technisch nicht erkennbar, sodass die Anwendungssitzung problemlos über SSO wiederhergestellt werden kann. Aus diesem Grund muss die SP-initiierte Abmeldesitzung sorgfältig überdingt werden, um sicherzustellen, dass Sitzungen sicher beendet werden, wenn sie nicht mehr benötigt werden.

Eine Möglichkeit, dies zu erreichen, ist das Hinzufügen einer SLO-Funktion zu Ihrer Abmeldeschaltfläche für Anwendungen, damit ihr Client an den Azure AD SAML-Abmeldeendpunkt umgeleitet werden kann. Den SAML-Abmeldeendpunkt für Ihren Mandanten finden Sie unter **App Registrations** > **Endpoints**.

Wenn eine Änderung der Anwendung nicht möglich ist, sollte die BIG-IP auf den Abmeldeaufruf der Anwendung warten und bei Erkennung der Anforderung SLO auslösen. Weitere Informationen zur Verwendung von BIG-IP-iRules finden Sie im [Abschnitt K42052145](https://support.f5.com/csp/article/K42052145) und im [Abschnitt K12056](https://support.f5.com/csp/article/K12056).

## <a name="summary"></a>Zusammenfassung

In diesem letzten Schritt werden alle angewendeten Einstellungen vor Ihrer Bestätigung unterbrochen. Wählen Sie **Bereitstellen** aus, um alle Einstellungen zu bestätigen, und überprüfen Sie, ob die Anwendung in Ihrem Mandanten erschienen ist.

Ihre Anwendung sollte nun veröffentlicht und über einen sicheren hybriden Zugang zugänglich sein, entweder direkt über dessen URL oder über die Anwendungsportale von Microsoft.

Um die Sicherheit zu erhöhen, könnten Organisationen, die dieses Muster verwenden, auch erwägen, den direkten Zugriff auf die Anwendung zu blockieren, sodass ein strikter Pfad über BIG-IP erzwungen wird.

## <a name="next-steps"></a>Nächste Schritte

Starten Sie als Benutzer einen Browser und stellen Sie eine Verbindung mit der externen URL der Anwendung her oder wählen Sie im Microsoft MyApps-Portal das Symbol der Anwendung aus. Nach der Authentifizierung bei Azure AD werden Sie zum virtuellen BIG-IP-Server für die Anwendung weitergeleitet und durch SSO (einmaliges Anmelden) automatisch angemeldet.
Die Ausgabe der eingefügten Header, die von unserer headerbasierten Anwendung angezeigt werden, wird gezeigt.

![Screenshot: Ausgabe](./media/f5-big-ip-header-advanced/mytravel-example.png)

## <a name="troubleshooting"></a>Problembehandlung

Wenn der Zugriff auf die sichere durch hybriden Zugriff geschützte Anwendung nicht möglich ist, kann das an verschiedenen Faktoren liegen, z. B. an einer falschen Konfiguration.

- BIG-IP-Protokolle sind eine hervorragende Informationsquelle zum Filtern aller Authentifizierungs- und SSO-Probleme. Bei der Problembehandlung sollten Sie die Ausführlichkeitsstufe des Protokolls hochsetzen, indem Sie zu **Zugriffsrichtlinie** > **Übersicht** > **Ereignis-Protokolle**  > **Einstellungen** navigieren. Wählen Sie die Zeile für Ihre veröffentlichte Anwendung und dann **Edit** > **Access System Logs** (Bearbeiten > Auf Systemprotokolle zugreifen) aus. Wählen Sie **Debug** (Debuggen) in der SSO-Liste und dann **OK** aus. Sie können ihr Problem jetzt reproduzieren, bevor Sie sich die Protokolle ansehen. Denken Sie jedoch daran, dies nach Abschluss wieder rückgängig zu machen.

- Wenn unmittelbar nach der erfolgreichen Azure AD-Vorabauthentifizierung ein mit BIG-IP gekennzeichneter Fehler angezeigt wird, ist es möglich, dass sich das Problem auf das einmalige Anmelden aus Azure AD bei BIG-IP zurückzuführen ist. Navigieren Sie zu **Access** > **Overview** > **Access reports** (Zugriff, Übersicht, Zugriffsberichte) aus und führen Sie den Bericht für die letzte Stunde aus, um Protokolle mit hinweisenden Informationen zu sehen. Der Link **View session variables** (Sitzungsvariablen anzeigen) für Ihre Sitzung hilft auch zu verstehen, ob APM die erwarteten Ansprüche von Azure AD empfängt.

- Wenn keine BIG-IP-Fehlerseite angezeigt wird, hängt das Problem wahrscheinlich eher mit dem einmaligen Anmelden aus BIG-IP bei der Back-End-Anwendung zusammen. Navigieren Sie in diesem Fall zu **Zugriffslichtlinie** > **Übersicht** > **Aktive Sitzungen** (Access Policy > Overview > Active Sessions) und wählen Sie den Link für Ihre aktive Sitzung aus. Der Link **Variablen anzeigen** (View Variables) an dieser Stelle kann auch bei der Ermittlung der eigentlichen Ursache der Probleme beim einmaligen Anmelden helfen, insbesondere dann, wenn die BIG-IP-APM die richtige Benutzer- und Domänen-Kennung nicht abrufen kann.

Weitere Informationen finden Sie unter [Beispiele für Zuweisungen von BIG-IP-APM-Variablen](https://devcentral.f5.com/s/articles/apm-variable-assign-examples-1107) und unter [Empfehlung für F5-BIG-IP-Sitzungsvariablen](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Für weiterführende Informationen lesen Sie bitte diese Artikel:

- [Das Ende der Passwörter, verzichten Sie auf Passwörter](https://www.microsoft.com/security/business/identity/passwordless)

- [Was ist bedingter Zugriff?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [„Zero Trust“-Framework von Microsoft zum Ermöglichen von Remotearbeit](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

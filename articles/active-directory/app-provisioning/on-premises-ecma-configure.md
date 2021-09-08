---
title: 'Azure AD-ECMA-Connectorhost: Konfiguration'
description: In diesem Artikel wird das Konfigurieren des Azure AD-ECMA-Connectorhosts beschrieben.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 769b5a312572a2c10f55ee3efbb54191b4f13707
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449690"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>Konfigurieren des Azure AD-ECMA-Connectorhosts und des Bereitstellungs-Agents

>[!IMPORTANT]
> Die Vorschauversion zur lokalen Bereitstellung ist derzeit nur mit Einladung verfügbar. Um Zugriff auf die Funktion anzufordern, verwenden Sie das [Zugriffsanforderungsformular](https://aka.ms/onpremprovisioningpublicpreviewaccess). Wir öffnen die Vorschauversion im Rahmen der Vorbereitung auf die allgemeine Verfügbarkeit in den nächsten Monaten für weitere Kunden und Connectors.

In diesem Artikel finden Sie eine Anleitung zum Konfigurieren des Azure AD-ECMA-Connectorhosts (Azure Active Directory) und des Bereitstellungs-Agents nach der erfolgreichen Installation.

Dieser Flow führt Sie durch die Installation und Konfiguration des Azure AD ECMA-Connectorhosts. 

 ![Diagramm des Installationsflows.](./media/on-premises-ecma-configure/flow-1.png)

Weitere Installations- und Konfigurationsinformationen finden Sie unter:
   - [Voraussetzungen für den Azure AD-ECMA-Connectorhost](on-premises-ecma-prerequisites.md)
   - [Installation des Azure AD-ECMA-Connectorhosts](on-premises-ecma-install.md)
   - [Konfiguration des generischen SQL-Connectors mit Azure AD-ECMA-Connectorhost](on-premises-sql-connector-configure.md)
   
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Konfigurieren des Azure AD-ECMA-Connectorhosts
Die Konfiguration des Azure AD-ECMA-Connectorhosts erfolgt in zwei Teilen:
    
   - **Konfigurieren der Einstellungen:** Konfigurieren Sie den Port und das Zertifikat für den Azure AD-ECMA-Connectorhost. Dieser Schritt erfolgt nur beim ersten Start des ECMA-Connectorhosts.
   - **Erstellen eines Connectors:** Erstellen Sie einen Connector (z. B. SQL oder LDAP), damit der Azure AD-ECMA-Connectorhost Daten in eine Datenquelle exportieren oder daraus importieren kann.

### <a name="configure-the-settings"></a>Konfigurieren der Einstellungen
Wenn Sie den Azure AD ECMA-Connectorhost zum ersten Mal starten, wird eine Portnummer mit dem Standardwert **8585** angezeigt. 

 ![Screenshot der Konfiguration der Einstellungen.](.\media\on-premises-ecma-configure\configure-1.png)

Für die Vorschau müssen Sie ein neues selbstsigniertes Zertifikat generieren.

 >[!NOTE]
 >In dieser Vorschauversion wird ein zeitabhängiges Zertifikat verwendet. Das automatisch generierte Zertifikat wird selbstsigniert sein. Ein Teil des vertrauenswürdigen Stamms und das SAN stimmen mit dem Hostnamen überein.


### <a name="create-a-connector"></a>Erstellen eines Connectors
Nun müssen Sie einen Connector für den Azure AD-ECMA-Connectorhost erstellen. Durch diesen Connector wird dem ECMA-Connectorhost ermöglicht, Daten in die Datenquelle für den von Ihnen erstellten Connector zu exportieren. Sie können ihn auch zum Importieren von Daten verwenden.

Die Konfigurationsschritte für die einzelnen Connectors sind umfangreicher und werden in separaten Dokumenten bereitgestellt.

Verwenden Sie zum Erstellen und Konfigurieren eines Connectors den [generischen SQL Connector](on-premises-sql-connector-configure.md). Dieser Connector kann mit Microsoft SQL-Datenbanken wie Azure SQL-Datenbank oder Azure Database for MySQL verwendet werden.

## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>Herstellen einer Verbindung zwischen Azure AD und dem Azure AD-ECMA-Connectorhost
Die folgenden Abschnitte dienen als Leitfaden für das Herstellen einer Verbindung mit dem lokalen Azure AD-ECMA-Connectorhost und Azure AD.

### <a name="ensure-the-ecma2host-service-is-running"></a>Sicherstellen, dass der ECMA2Host-Dienst ausgeführt wird
1. Wählen Sie auf dem Server, auf dem der Azure AD-ECMA-Connectorhost ausgeführt wird, **Starten** aus.
1. Geben Sie **run** (Ausführen) ein, und geben Sie **services.msc** in das Feld ein.
1. Stellen Sie sicher, dass **Microsoft ECMA2Host** in der Liste **Dienste** enthalten ist und ausgeführt wird. Wählen Sie andernfalls **Starten** aus.

   ![Screenshot des ausgeführten Diensts.](.\media\on-premises-ecma-configure\configure-2.png)

### <a name="add-an-enterprise-application"></a>Hinzufügen einer Unternehmensanwendung
1. Melden Sie sich als Anwendungsadministrator beim Azure-Portal an.
1. Wechseln Sie im Portal zu **Azure Active Directory** > **Unternehmensanwendungen**.
1. Wählen Sie **Neue Anwendung** aus.

   ![Screenshot des Hinzufügens einer neuen Anwendung.](.\media\on-premises-ecma-configure\configure-4.png)
1. Suchen Sie im Katalog nach der Anwendung **Lokale Bereitstellung**, und klicken Sie auf **Erstellen**.

### <a name="configure-the-application-and-test"></a>Anwendungskonfiguration und Tests
  1. Wählen Sie nach der Erstellung der Anwendung die Seite **Bereitstellung** aus.
  1. Klicken Sie auf **Get started** (Los geht‘s).
  
     ![Screenshot der ersten Schritte.](.\media\on-premises-ecma-configure\configure-6.png)
  1. Ändern Sie auf der Seite **Bereitstellung** die Option **Bereitstellungsmodus** in **Automatisch**.

      ![Screenshot der Änderung des Modus.](.\media\on-premises-ecma-configure\configure-7.png)
  1. Wählen Sie im Abschnitt **Lokale Konnektivität** den Agent aus, den Sie bereitgestellt haben, und wählen Sie dann **Agent(s) zuweisen** aus.
  
      ![Screenshot der Zuweisung eines Agents.](.\media\on-premises-ecma-configure\configure-8.png)</br>

      >[!NOTE]
      >Warten Sie nach dem Hinzufügen des Agents 10 bis 20 Minuten, bis die Registrierung abgeschlossen ist. Der Konnektivitätstest funktioniert erst, wenn die Registrierung abgeschlossen ist.
      >
      >Alternativ können Sie erzwingen, dass die Agent-Registrierung abgeschlossen wird, indem Sie den Bereitstellungs-Agent auf Ihrem Server neu starten. Navigieren Sie zu Ihrem Server, suchen Sie in der Windows-Suchleiste nach **Dienste** und dem **Bereitstellungs-Agent-Dienst von Azure AD Connect**, klicken Sie mit der rechten Maustaste auf den Dienst, und starten Sie ihn neu.

  1. Geben Sie nach 10 Minuten im Abschnitt **Administratoranmeldeinformationen** die folgende URL ein. Ersetzen Sie den Teil `"connectorName"` durch den Namen des Connectors auf dem ECMA-Host.

      |Eigenschaft|Wert|
      |-----|-----|
      |Mandanten-URL|https://localhost:8585/ecma2host_connectorName/scim|

  1. Geben Sie den Wert des geheimen Tokens ein, das Sie beim Erstellen des Connectors definiert haben.
  1. Wählen Sie **Verbindung testen** aus, und warten Sie eine Minute.
  
     ![Screenshot „Verbindung testen“.](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >Warten Sie nach dem Zuweisen des Agents 10 bis 20 Minuten, bis Sie die Verbindung testen. Die Verbindung kann erst hergestellt werden, wenn die Registrierung abgeschlossen wurde.

  1. Wenn der Verbindungstest erfolgreich war, wählen Sie **Speichern** aus.</br>
  
     ![Screenshot des erfolgreichen Tests.](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-whos-in-scope-for-provisioning"></a>Konfigurieren der Zugehörigkeit zum Bereitstellungsbereich
Da der Azure AD-ECMA-Connectorhost und Azure AD nun miteinander kommunizieren, können Sie mit der Konfiguration des Bereitstellungsbereichs fortfahren. Die folgenden Abschnitte enthalten Informationen darüber, wie Sie Ihre Benutzer eingrenzen.

### <a name="assign-users-to-your-application"></a>Zuweisen von Benutzern zu Ihrer Anwendung
Mit Azure AD können Sie festlegen, welche Benutzer basierend auf der Zuweisung zu einer Anwendung und/oder durch Filtern nach einem bestimmten Attribut bereitgestellt werden sollen. Definieren Sie den Bereitstellungsbereich und Ihre Bereichsregeln nach Bedarf. Weitere Informationen finden Sie unter [Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory](../../active-directory/manage-apps/assign-user-or-group-access-portal.md).

### <a name="configure-your-attribute-mappings"></a>Konfigurieren Ihrer Attributzuordnungen
Sie müssen die Benutzerattribute in Azure AD den Attributen in der Zielanwendung zuordnen. Der Azure AD-Bereitstellungsdienst basiert bei der Bereitstellung auf dem SCIM-Standard. Daher verfügen die angezeigten Attribute über den SCIM-Namespace. Das folgende Beispiel zeigt, wie Sie die Attribute **mail** und **objectId** in Azure AD den Attributen **Email** und **InternalGUID** in einer Anwendung zuordnen können.

>[!NOTE]
>In der Standardzuordnung wird **userPrincipalName** dem Attributnamen *PLACEHOLDER* zugeordnet. Sie müssen das *PLACEHOLDER*-Attribut in ein Attribut ändern, das Bestandteil Ihrer Anwendung ist. Weitere Informationen finden Sie unter [Abgleichen von Benutzern im Quell- und Zielsystem](customize-application-attributes.md#matching-users-in-the-source-and-target--systems).

|Attributname in Azure AD|Attributname in SCIM|Attributname in der Zielanwendung|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|Email|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

### <a name="configure-attribute-mapping"></a>Konfigurieren von Attributzuordnungen
 1. Wählen Sie im Azure AD-Portal unter **Unternehmensanwendungen** die Seite **Bereitstellung** aus.
 2. Klicken Sie auf **Get started** (Los geht‘s).
 3. Erweitern Sie **Zuordnungen**, und wählen Sie **Azure Active Directory-Benutzer bereitstellen** aus.
 
    ![Screenshot „Azure Active Directory-Benutzer bereitstellen“.](.\media\on-premises-ecma-configure\configure-10.png)
 1. Wählen Sie **Neue Zuordnung hinzufügen** aus.
 
    ![Screenshot von „Neue Zuordnung hinzufügen“.](.\media\on-premises-ecma-configure\configure-11.png)
 1. Geben Sie die Quell- und Zielattribute an, und wählen Sie **OK**.aus.</br>
 
    ![Der Screenshot zeigt den Bereich „Attribut bearbeiten“.](.\media\on-premises-ecma-configure\configure-12.png)


Weitere Informationen über das Zuordnen von Benutzerattributen von Anwendungen zu Azure AD finden Sie im [Tutorial: Anpassen von Attributzuordnungen für die Benutzerbereitstellung für SaaS-Anwendungen in Azure Active Directory](customize-application-attributes.md).

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>Testen Ihrer Konfiguration durch die bedarfsorientierte Bereitstellung von Benutzern
Zum Testen Ihrer Konfiguration können Sie die bedarfsorientierte Bereitstellung von Benutzern verwenden. Weitere Informationen über die bedarfsorientierte Bereitstellung von Benutzern finden Sie unter [Bedarfsorientierte Bereitstellung](provision-on-demand.md).

 1. Wechseln Sie zum Bereich für einmaliges Anmelden und dann zurück zum Bereitstellungsbereich. Wählen Sie im neuen Bereich mit der Bereitstellungsübersicht **Bei Bedarf** aus.
 1. Testen Sie die Bedarfsbereitstellung für einige Benutzer, wie unter [On-Demand-Bereitstellung in Azure Active Directory](provision-on-demand.md) beschrieben.
 
    ![Screenshot eines Bereitstellungstests.](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>Benutzerbereitstellung starten
 1. Nachdem die bedarfsorientierte Bereitstellung erfolgreich war, wechseln Sie zurück zur Konfigurationsseite der Bereitstellung. Stellen Sie sicher, dass der Bereich nur auf zugewiesene Benutzer und Gruppen festgelegt ist, setzen Sie die Bereitstellung auf **Ein**, und wählen Sie **Speichern** aus.
 
    ![Screenshot des Starts der Bereitstellung.](.\media\on-premises-ecma-configure\configure-14.png)

1. Warten Sie einige Minuten, bis die Bereitstellung gestartet wurde. Dies kann bis zu 40 Minuten dauern. Nachdem der Bereitstellungsauftrag wie im nächsten Abschnitt beschrieben abgeschlossen wurde, können Sie den Bereitstellungsstatus wieder auf **Aus** setzen und **Speichern** auswählen. Mit diesem Schritt wird der Bereitstellungsdienst in Zukunft nicht mehr ausgeführt.

### <a name="verify-users-were-successfully-provisioned"></a>Überprüfen der erfolgreichen Bereitstellung von Benutzern
Kontrollieren Sie im Anschluss an die Wartezeit Ihre Datenquelle, um zu überprüfen, ob neue Benutzer bereitgestellt werden.

 ![Screenshot der Überprüfung der Bereitstellung von Benutzern.](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>Überwachen der Bereitstellung

1. Mithilfe der Bereitstellungsprotokolle können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
1. Erstellen Sie benutzerdefinierte Warnungen, Dashboards und Abfragen durch eine Integration von Azure Monitor.
1. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](https://github.com/MicrosoftDocs/azure-docs-pr/compare/application-provisioning-quarantine-status.md?expand=1).

## <a name="next-steps"></a>Nächste Schritte

- [Azure AD-ECMA-Connectorhost: Voraussetzungen](on-premises-ecma-prerequisites.md)
- [Azure AD-ECMA-Connectorhost: Installation](on-premises-ecma-install.md)
- [Generischer SQL-Connector](on-premises-sql-connector-configure.md)

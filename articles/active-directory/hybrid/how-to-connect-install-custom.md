---
title: Anpassen einer Installation von Azure Active Directory Connect
description: In diesem Artikel werden die Optionen für die benutzerdefinierte Installation für Azure AD Connect erläutert. Gehen Sie folgendermaßen vor, um Active Directory über Azure AD Connect zu installieren.
services: active-directory
keywords: Was ist Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29cc31121a4888c23ccbec1c549f2313d0c9e165
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439320"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Benutzerdefinierte Installation von Azure Active Directory Connect
Verwenden Sie *benutzerdefinierte Einstellungen* in Azure Active Directory (Azure AD) Connect, wenn Sie mehr Optionen für die Installation benötigen. Verwenden Sie diese Einstellungen z. B., wenn Sie über mehrere Gesamtstrukturen verfügen oder optionale Features konfigurieren möchten. Verwenden Sie benutzerdefinierte Einstellungen in allen Fällen, in denen die [Expressinstallation](how-to-connect-install-express.md) für Ihre Bereitstellung oder Topologie nicht ausreicht.

Voraussetzungen:
- [Azure AD Connect herunterladen](https://go.microsoft.com/fwlink/?LinkId=615771).
- Führen Sie die erforderlichen Schritte in [Azure AD Connect: Voraussetzungen und Hardware](how-to-connect-install-prerequisites.md) aus. 
- Stellen Sie sicher, dass Sie über die unter [Azure AD Connect-Konten und -Berechtigungen](reference-connect-accounts-permissions.md) beschriebenen Konten verfügen.

## <a name="custom-installation-settings"></a>Einstellungen für die benutzerdefinierte Installation 

Zum Einrichten einer benutzerdefinierten Installation für Azure AD Connect gehen Sie die in den folgenden Abschnitten beschriebenen Assistentenseiten durch.

### <a name="express-settings"></a>Express-Einstellungen
Wählen Sie auf der Seite **Express-Einstellungen** die Option **Anpassen** aus, um eine Installation mit benutzerdefinierten Einstellungen zu starten.  Im restlichen Artikel werden Sie durch den benutzerdefinierten Installationsvorgang geführt. Verwenden Sie die folgenden Links, um schnell zu den Informationen für eine bestimmte Seite zu wechseln:

- [Erforderliche Komponenten](#install-required-components)
- [Benutzeranmeldung](#user-sign-in)
- [Herstellen einer Verbindung mit Azure AD](#connect-to-azure-ad)
- [Sync](#sync-pages)

### <a name="install-required-components"></a>Installieren der erforderlichen Komponenten
Bei der Installation der Synchronisierungsdienste können Sie den optionalen Konfigurationsabschnitt deaktiviert lassen. Azure AD Connect richtet alles automatisch ein. Azure AD Connect richtet eine SQL Server 2019 Express LocalDB-Instanz ein, erstellt die geeigneten Gruppen und weist Berechtigungen zu. Wenn Sie die Standardwerte ändern möchten, deaktivieren Sie die entsprechenden Kontrollkästchen.  Die nachstehende Tabelle enthält eine Zusammenfassung dieser Optionen sowie Links zu weiteren Informationen. 

![Screenshot mit optionalen Auswahlmöglichkeiten für die erforderlichen Installationskomponenten in Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Optionale Konfiguration | BESCHREIBUNG |
| --- | --- |
|Benutzerdefinierten Speicherort für die Installation angeben| Hiermit können Sie den Standardinstallationspfad für Azure AD Connect ändern.|
| Verwenden eines vorhandenen SQL Servers |Ermöglicht Ihnen die Angabe des SQL Server-Namens und des Instanznamens. Wählen Sie diese Option aus, wenn Sie bereits über einen Datenbankserver verfügen, den Sie verwenden möchten. Geben Sie unter **Instanzname** den Instanznamen, ein Komma und die Portnummer ein, wenn für die SQL Server-Instanz kein Browsen aktiviert ist.  Geben Sie dann den Namen der Azure AD Connect-Datenbank an.  Ihre SQL-Berechtigungen bestimmen, ob eine neue Datenbank erstellt werden kann oder ob Ihr SQL-Administrator die Datenbank im Voraus erstellen muss.  Wenn Sie über Administratorberechtigungen für SQL Server (SA) verfügen, finden Sie weitere Informationen unter [Installieren von Azure AD Connect mithilfe einer vorhandenen Datenbank](how-to-connect-install-existing-database.md).  Ob Sie über delegierte Berechtigungen (DBO) verfügen, erfahren Sie unter [Installieren von Azure AD Connect mit delegierten SQL-Administratorrechten](how-to-connect-install-sql-delegation.md). |
| Verwenden eines vorhandenen Dienstkontos |Azure AD Connect bietet standardmäßig ein virtuelles Dienstkonto für die Synchronisierungsdienste. Wenn Sie eine SQL Server-Remoteinstanz oder einen Proxy mit Authentifizierungsanforderung verwenden, können Sie ein *verwaltetes Dienstkonto* oder ein kennwortgeschütztes Dienstkonto in der Domäne verwenden. Geben Sie in diesen Fällen das zu verwendende Konto ein. Um die Installation auszuführen, müssen Sie Systemadministrator in SQL sein, damit Sie Anmeldeinformationen für das Dienstkonto erstellen können. Weitere Informationen finden Sie unter [Azure AD Connect: Konten und Berechtigungen](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Durch die Verwendung des neuesten Builds kann der SQL-Administrator jetzt eine Out-of-Band-Bereitstellung für die Datenbank vornehmen. Anschließend kann sie vom Azure AD Connect-Administrator mit Datenbankbesitzerrechten installiert werden.  Weitere Informationen finden Sie unter [Installieren von Azure AD Connect mit Berechtigungen eines delegierten SQL-Administrators](how-to-connect-install-sql-delegation.md).|
| Angeben benutzerdefinierter Synchronisierungsgruppen |Von Azure AD Connect werden beim Installieren der Synchronisierungsdienste standardmäßig vier lokale Gruppen für den Server erstellt. Bei diesen Gruppen handelt es sich um „Administratoren“, „Operatoren“, „Durchsuchen“ und „Kennwortzurücksetzung“. Hier können Sie Ihre eigenen Gruppen angeben. Die Gruppen müssen sich lokal auf dem Server befinden. Sie können sich nicht in der Domäne befinden. |
|Synchronisierungseinstellungen importieren (Vorschau)|Hiermit können Sie Einstellungen aus anderen Versionen von Azure AD Connect importieren.  Weitere Informationen finden Sie unter [Importieren und Exportieren von Azure AD Connect-Konfigurationseinstellungen](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Benutzeranmeldung
Nach der Installation der erforderlichen Komponenten wählen Sie die SSO-Methode (Single Sign-on, einmaliges Anmelden) für Ihre Benutzer aus. In der folgenden Tabelle werden die verfügbaren Optionen kurz beschrieben. Eine vollständige Beschreibung der Anmeldemethoden finden Sie unter [Benutzeranmeldung](plan-connect-user-signin.md).

![Screenshot mit der Seite „Benutzeranmeldung“. Die Option „Kennworthashsynchronisierung“ ist ausgewählt.](./media/how-to-connect-install-custom/usersignin4.png)

| Option für das einmalige Anmelden | BESCHREIBUNG |
| --- | --- |
| Kennworthashsynchronisierung |Benutzer können sich bei Microsoft Cloud Services wie Microsoft 365 mit demselben Kennwort anmelden, das sie auch in ihrem lokalen Netzwerk verwenden. Benutzerkennwörter werden mit Azure AD als Kennworthash synchronisiert. Die Authentifizierung erfolgt in der Cloud. Weitere Informationen finden Sie unter [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md). |
|Passthrough-Authentifizierung|Benutzer können sich bei Microsoft Cloud Services wie Microsoft 365 mit demselben Kennwort anmelden, das sie auch in ihrem lokalen Netzwerk verwenden.  Benutzerkennwörter werden zur Überprüfung an den lokalen Active Directory-Domänencontroller übergeben.
| Verbund mit AD FS |Benutzer können sich bei Microsoft Cloud Services wie Microsoft 365 mit demselben Kennwort anmelden, das sie auch in ihrem lokalen Netzwerk verwenden.  Benutzer werden für die Anmeldung an ihre lokale Active Directory-Verbunddienste (AD FS)-Instanz umgeleitet. Die Authentifizierung erfolgt lokal. |
| Verbund mit PingFederate|Benutzer können sich bei Microsoft Cloud Services wie Microsoft 365 mit demselben Kennwort anmelden, das sie auch in ihrem lokalen Netzwerk verwenden.  Benutzer werden für die Anmeldung an ihre lokale PingFederate-Instanz umgeleitet. Die Authentifizierung erfolgt lokal. |
| Nicht konfigurieren |Es wird keine Funktion für die Benutzeranmeldung installiert oder konfiguriert. Wählen Sie diese Option aus, wenn Sie bereits über einen Verbundserver eines Drittanbieters verfügen oder eine andere Lösung eingesetzt wird. |
|Einmaliges Anmelden aktivieren|Diese Option ist sowohl bei der Kennworthashsynchronisierung als auch bei der Passthrough-Authentifizierung verfügbar. Sie bietet Desktopbenutzern in Unternehmensnetzwerken eine SSO-Benutzeroberfläche (Single Sign-on, einmaliges Anmelden). Weitere Informationen finden Sie unter [Einmaliges Anmelden](how-to-connect-sso.md). </br></br>**Hinweis:** Für AD FS-Kunden ist diese Option nicht verfügbar. AD FS bietet bereits dieselbe SSO-Ebene.</br>

### <a name="connect-to-azure-ad"></a>Stellen Sie eine Verbindung mit Azure AD her.
Geben Sie auf der Seite **Mit Azure AD verbinden** ein Konto und ein Kennwort für einen globalen Administrator ein. Wenn Sie auf der vorherigen Seite **Verbund mit AD FS** ausgewählt haben, melden Sie sich nicht mit einem Konto in einer Domäne an, die Sie für den Verbund aktivieren möchten. 

Sie könnten ein Konto in der Standarddomäne *onmicrosoft.com* verwenden, die zu Ihrem Azure AD-Mandanten gehört. Dieses Konto wird nur zum Erstellen eines Dienstkontos in Azure AD verwendet. Nach Abschluss der Installation wird es nicht mehr verwendet.
  
![Screenshot mit der Seite „Mit Azure AD verbinden“.](./media/how-to-connect-install-custom/connectaad.png)

Wenn für Ihr globales Administratorkonto die mehrstufige Authentifizierung aktiviert ist, geben Sie das Kennwort im Anmeldefenster erneut ein. Außerdem müssen Sie die Aufforderung für die mehrstufige Authentifizierung abschließen. Bei der Aufforderung kann es sich um einen Prüfcode oder um einen Anruf handeln.  

![Screenshot mit der Seite „Mit Azure AD verbinden“. In einem Feld für die mehrstufige Authentifizierung wird der Benutzer zur Eingabe eines Codes aufgefordert.](./media/how-to-connect-install-custom/connectaadmfa.png)

Für das globale Administratorkonto kann auch [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) aktiviert sein.

Falls ein Fehler oder Verbindungsprobleme auftreten, informieren Sie sich unter [Problembehandlung bei Konnektivitätsproblemen](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Synchronisierungsseiten

In den folgenden Abschnitten werden die Seiten im Abschnitt **Synchronisieren** beschrieben.

### <a name="connect-your-directories"></a>Verzeichnisse verbinden
Zum Herstellen einer Verbindung mit Active Directory Domain Services (Azure AD DS) benötigt Azure AD Connect den Namen der Gesamtstruktur und die Anmeldeinformationen für ein Konto, das über ausreichende Berechtigungen verfügt.

![Screenshot der Seite „Verzeichnisse verbinden“](./media/how-to-connect-install-custom/connectdir01.png)

Nachdem Sie den Namen der Gesamtstruktur eingegeben und **Verzeichnis hinzufügen** ausgewählt haben, wird ein Fenster eingeblendet. In der folgenden Tabelle werden die Optionen beschrieben.

| Option | BESCHREIBUNG |
| --- | --- |
| Erstellen eines neuen Kontos | Erstellen Sie das Azure AD DS-Konto, das von Azure AD Connect benötigt wird, um während der Verzeichnissynchronisierung eine Verbindung mit der Active Directory-Gesamtstruktur herzustellen. Nachdem Sie diese Option ausgewählt haben, geben Sie den Benutzernamen und das Kennwort für ein Unternehmensadministratorkonto ein.  Das bereitgestellte Unternehmensadministratorkonto wird von Azure AD Connect verwendet, um das erforderliche Azure AD DS-Konto zu erstellen. Sie können den Domänenteil entweder im NetBIOS-Format oder im FQDN-Format eingeben. Geben Sie also *FABRIKAM\administrator* oder *fabrikam.com\administrator* ein. |
| Vorhandenes Konto verwenden | Stellen Sie ein vorhandenes Azure AD DS-Konto bereit, das von Azure AD Connect verwendet werden kann, um während der Verzeichnissynchronisierung eine Verbindung mit der Active Directory-Gesamtstruktur herzustellen. Sie können den Domänenteil entweder im NetBIOS-Format oder im FQDN-Format eingeben. Geben Sie also *FABRIKAM\syncuser* oder *fabrikam.com\syncuser* ein. Dieses Konto kann ein normales Benutzerkonto sein, da nur die standardmäßigen Leseberechtigungen benötigt werden. Abhängig von Ihrem Szenario benötigen Sie jedoch möglicherweise weitere Berechtigungen. Weitere Informationen finden Sie unter [Azure AD Connect: Konten und Berechtigungen](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Screenshot mit der Seite „Verzeichnis verbinden“ und dem Fenster mit dem AD-Gesamtstrukturkonto, in dem Sie ein neues Konto erstellen oder ein vorhandenes Konto verwenden können.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> Ab Build 1.4.18.0. ist es nicht mehr möglich, ein Unternehmens- oder Domänenadministratorkonto als Azure AD DS-Connectorkonto zu verwenden. Wenn Sie bei Auswahl von **Vorhandenes Konto verwenden** versuchen, ein Unternehmens- oder Domänenadministratorkonto einzugeben, wird der folgende Fehler angezeigt: „Die Verwendung eines Unternehmens- oder Domänenadministratorkontos für Ihr AD-Gesamtstrukturkonto ist nicht zulässig. Lassen Sie das Konto von Azure AD Connect erstellen, oder geben Sie ein Synchronisierungskonto mit den richtigen Berechtigungen an.“
>

### <a name="azure-ad-sign-in-configuration"></a>Konfiguration der Azure AD-Anmeldung
Überprüfen Sie auf der Seite **Azure AD-Anmeldekonfiguration** die Benutzerprinzipalnamensdomänen (User Principal Name, UPN) in den lokalen Azure AD DS. Diese UPN-Domänen wurden in Azure AD überprüft. Auf dieser Seite konfigurieren Sie das Attribut, das für userPrincipalName verwendet werden soll.

![Screenshot mit nicht überprüften Domänen auf der Seite „Azure AD-Anmeldungskonfiguration“.](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Überprüfen Sie jede Domäne, die als **Nicht hinzugefügt** oder **Nicht überprüft** gekennzeichnet ist. Stellen Sie sicher, dass die verwendeten Domänen in Azure AD überprüft wurden. Nachdem Sie Ihre Domänen überprüft haben, wählen Sie das kreisförmige Aktualisierungssymbol aus. Weitere Informationen finden Sie unter [Hinzufügen und Überprüfen der Domäne](../fundamentals/add-custom-domain.md).

Benutzer verwenden das *userPrincipalName*-Attribut, wenn sie sich bei Azure AD und Microsoft 365 anmelden. Die Domänen, die auch als UPN-Suffix bezeichnet werden, sollten durch Azure AD überprüft werden, bevor Benutzer synchronisiert werden. Microsoft empfiehlt, das Standardattribut userPrincipalName beizubehalten. 

Wenn das userPrincipalName-Attribut nicht routingfähig ist und nicht überprüft werden kann, können Sie ein anderes Attribut auswählen. Sie können beispielsweise „email“ als Attribut auswählen, das die Anmelde-ID enthält. Wenn Sie ein anderes Attribut als userPrincipalName verwenden, wird dieses als *alternative ID* bezeichnet. 

Der Attributwert der alternativen ID muss dem RFC 822-Standard entsprechen. Eine alternative ID kann mit der Kennworthashsynchronisierung, Passthrough-Authentifizierung und Verbundlösungen verwendet werden. Das Attribut kann in Active Directory nicht als mehrwertiges Attribut definiert werden, auch wenn es nur einen einzelnen Wert hat. Weitere Informationen zur alternativen ID finden Sie unter [Passthrough-Authentifizierung: Häufig gestellte Fragen (FAQs)](./how-to-connect-pta-faq.yml#does-pass-through-authentication-support--alternate-id--as-the-username--instead-of--userprincipalname--).

>[!NOTE]
> Beim Aktivieren der Passthrough-Authentifizierung müssen Sie mindestens über eine verifizierte Domäne verfügen, um die benutzerdefinierte Installation fortzusetzen.

> [!WARNING]
> Alternative IDs sind nicht mit allen Microsoft 365-Workloads kompatibel. Weitere Informationen finden Sie unter [Konfigurieren alternativer Anmelde-IDs](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Filterung von Domänen und Organisationseinheiten
Standardmäßig werden alle Domänen und Organisationseinheiten (OEs) synchronisiert. Wenn Sie einige Domänen oder OEs nicht mit Azure AD synchronisieren möchten, können Sie die entsprechenden Optionen deaktivieren.  

![Screenshot mit der Seite für die Filterung von Domänen und Organisationseinheiten.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Auf dieser Seite wird die Filterung auf Grundlage von Domänen und OEs konfiguriert. Wenn Sie Änderungen vornehmen möchten, sollten Sie sich über die Filterung auf Grundlage von [Domänen](how-to-connect-sync-configure-filtering.md#domain-based-filtering) und [OEs](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) informieren. Da einige OEs für die Funktionalität von grundlegender Bedeutung sind, sollten sie aktiviert bleiben.

Bei Verwendung der OE-basierten Filterung mit einer Azure AD Connect-Version vor 1.1.524.0 werden neue OEs standardmäßig synchronisiert. Wenn keine neuen OEs synchronisiert werden sollen, können Sie das Standardverhalten nach dem Schritt für die [OE-basierte Filterung](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) anpassen. Ab Azure AD Connect 1.1.524.0 können Sie angeben, ob neue OEs synchronisiert werden sollen.

Wenn Sie die [gruppenbasierte Filterung](#sync-filtering-based-on-groups) verwenden möchten, sollten Sie sicherstellen, dass die OE mit der Gruppe eingebunden ist und dass dafür keine OE-Filterung verwendet wird. Die OE-Filterung wird vor der gruppenbasierten Filterung ausgewertet.

Es ist auch möglich, dass bestimmte Domänen aufgrund von Firewallbeschränkungen nicht erreichbar sind. Diese Domänen sind standardmäßig nicht ausgewählt und mit einer Warnung versehen.  

![Screenshot mit nicht erreichbaren Domänen.](./media/how-to-connect-install-custom/unreachable.png)  

Falls diese Warnung angezeigt wird, sollten Sie sich vergewissern, dass die entsprechenden Domänen tatsächlich nicht erreichbar sind und die Warnung zu erwarten ist.

### <a name="uniquely-identifying-your-users"></a>Eindeutige Identifizierung der Benutzer

Wählen Sie auf der Seite **Benutzer werden identifiziert** aus, wie Benutzer in Ihren lokalen Verzeichnissen identifiziert werden sollen. Geben Sie auch an, dass sie in Azure AD mit dem sourceAnchor-Attribut identifiziert werden sollen.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Auswählen, wie Benutzer in Ihren lokalen Verzeichnissen identifiziert werden sollen
Mit dem Feature *Abgleich über Gesamtstrukturen* können Sie definieren, wie Benutzer aus Ihren AD DS-Gesamtstrukturen in Azure AD dargestellt werden. Ein Benutzer kann nur einmal in allen Gesamtstrukturen dargestellt werden oder über eine Kombination aus aktivierten und deaktivierten Konten verfügen. In bestimmten Gesamtstrukturen wird der Benutzer unter Umständen auch als Kontakt dargestellt.

![Screenshot mit der Seite, auf der Sie Ihre Benutzer eindeutig identifizieren können.](./media/how-to-connect-install-custom/unique2.png)

| Einstellung | BESCHREIBUNG |
| --- | --- |
| [Benutzer werden nur einmal für alle Gesamtstrukturen dargestellt.](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Alle Benutzer werden in Azure AD jeweils als einzelne Objekte erstellt. Die Objekte werden nicht im Metaverse verknüpft. |
| [E-Mail-Attribut](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Diese Option verknüpft Benutzer und Kontakte, wenn dieses Attribut in verschiedenen Gesamtstrukturen denselben Wert aufweist. Verwenden Sie diese Option, wenn Ihre Kontakte mit GALSync erstellt wurden. Bei Verwendung dieser Option werden Benutzerobjekte, deren E-Mail-Attribut nicht aufgefüllt ist, nicht mit Azure AD synchronisiert. |
| [ObjectSID- und msExchangeMasterAccountSID/msRTCSIP-OriginatorSID-Attribute](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Mit dieser Option wird ein aktivierter Benutzer in einer Kontogesamtstruktur mit einem deaktivierten Benutzer in einer Ressourcengesamtstruktur verknüpft. In Exchange wird diese Konfiguration als verknüpftes Postfach bezeichnet. Sie können diese Option verwenden, wenn Sie nur Lync nutzen und Exchange nicht in der Ressourcengesamtstruktur enthalten ist. |
| SAMAccountName- und MailNickName-Attribute |Mit dieser Option werden Attribute mit der Stelle verknüpft, an der sich erwartungsgemäß die Anmelde-ID für den Benutzer befindet. |
| Bestimmtes Attribut auswählen |Mit dieser Option können Sie Ihr eigenes Attribut auswählen. Bei Auswahl dieser Option werden Benutzerobjekte, deren (ausgewähltes) Attribut nicht aufgefüllt ist, nicht mit Azure AD synchronisiert. **Einschränkung:** Nur Attribute, die bereits im Metaverse enthalten sind, sind für diese Option verfügbar. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Auswählen, wie Benutzer unter Verwendung eines Quellankers identifiziert werden sollen
Das *sourceAnchor*-Attribut ist während der Lebensdauer eines Benutzerobjekts unveränderlich. Das Attribut ist der Primärschlüssel, der den lokalen Benutzer mit dem Benutzer in Azure AD verknüpft.

| Einstellung | BESCHREIBUNG |
| --- | --- |
| Ich möchte den Quellanker durch Azure verwalten lassen | Wählen Sie diese Option aus, wenn Azure AD das Attribut für Sie auswählen soll. Wenn Sie diese Option auswählen, wendet Azure AD Connect die Auswahllogik für das sourceAnchor-Attribut an, die unter [Verwenden von ms-DS-ConsistencyGuid als sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) beschrieben wird. Nachdem die benutzerdefinierte Installation abgeschlossen ist, sehen Sie, welches Attribut als sourceAnchor-Attribut ausgewählt wurde. |
| Bestimmtes Attribut auswählen | Wählen Sie diese Option aus, wenn Sie ein vorhandenes AD-Attribut als sourceAnchor-Attribut angeben möchten. |

Da das sourceAnchor-Attribut nicht geändert werden kann, müssen Sie ein geeignetes Attribut auswählen. Hier empfiehlt sich "objectGUID". Dieses Attribut wird erst geändert, wenn das Benutzerkonto zwischen Gesamtstrukturen oder Domänen verschoben wird. Wählen Sie keine Attribute aus, die sich ändern können, wenn eine Person heiratet oder den Aufgabenbereich wechselt. 

Sie können keine Attribute verwenden, die ein @-Zeichen enthalten. Daher können Sie weder „email“ noch „userPrincipalName“ verwenden. Bei dem Attribut wird auch die Groß-/Kleinschreibung beachtet. Beim Verschieben von Objekten zwischen Gesamtstrukturen muss daher die Groß-/Kleinschreibung beibehalten werden. Binäre Attribute werden Base64-codiert, andere Attributtypen bleiben dagegen unverschlüsselt. 

In Verbundszenarien und bei einigen Azure AD-Schnittstellen wird das sourceAnchor-Attribut auch als *immutableID* bezeichnet. 

Weitere Informationen zum Quellanker finden Sie unter [Entwurfskonzepte](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Synchronisierungsfilterung anhand von Gruppen
Mit der Filterung nach Gruppen haben Sie die Möglichkeit, nur eine kleine Teilmenge von Objekten für einen Pilotversuch zu synchronisieren. Um dieses Feature zu verwenden, erstellen Sie in Ihrer lokalen Active Directory-Instanz eine Gruppe für diesen Zweck. Fügen Sie anschließend Benutzer und Gruppen hinzu, die als direkte Mitglieder mit Azure AD synchronisiert werden sollen. Später können Sie der Gruppe Benutzer hinzufügen oder daraus entfernen, um die Liste mit den Objekten zu verwalten, die in Azure AD vorhanden sein sollen. 

Alle Objekte, die Sie synchronisieren möchten, müssen direkte Mitglieder der Gruppe sein. Benutzer, Gruppen, Kontakte und Computer oder Geräte müssen alle direkte Mitglieder sein. Geschachtelte Gruppenmitgliedschaften werden nicht aufgelöst. Wenn Sie eine Gruppe als Mitglied hinzufügen, wird nur die Gruppe selbst hinzugefügt, nicht aber ihre Mitglieder.

![Screenshot mit der Seite, auf der Sie auswählen können, wie Benutzer und Geräte gefiltert werden sollen.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Dieses Feature dient nur zur Unterstützung von Pilotbereitstellungen. Verwenden Sie sie nicht in einer vollständigen Produktionsbereitstellung.
>

In einer vollständigen Produktionsbereitstellung wird es schwer, eine einzelne Gruppe mit allen zu synchronisierenden Objekten zu verwalten. Verwenden Sie anstelle des Features für das Filtern nach Gruppen eine der unter [Konfigurieren der Filterung](how-to-connect-sync-configure-filtering.md) beschriebenen Methoden.

### <a name="optional-features"></a>Optionale Features
Auf der nächsten Seite können Sie optionale Features für Ihr Szenario auswählen.

>[!WARNING]
>Für Azure AD Connect bis Version 1.0.8641.0 wird der Azure Access Control Service für das Kennwortrückschreiben verwendet.  Dieser Dienst wurde am 7. November 2018 außer Betrieb genommen.  Wenn Sie eine dieser Versionen von Azure AD Connect nutzen und das Kennwortrückschreiben aktiviert haben, können Benutzer u. U. ihre Kennwörter nicht mehr ändern oder zurücksetzen, nachdem der Dienst außer Betrieb genommen wurde. Das Kennwortrückschreiben wird von diesen Versionen von Azure AD Connect nicht unterstützt.
>
>Weitere Informationen finden Sie unter [Migrieren aus dem Azure Access Control Service](../azuread-dev/active-directory-acs-migration.md).
>
>Wenn Sie das Kennwortrückschreiben verwenden möchten, laden Sie die [neueste Version von Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) herunter.

![Screenshot mit der Seite „Optionale Features“.](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Wenn Azure AD Sync oder die direkte Synchronisierung (DirSync) aktiv ist, sollten Sie keine Rückschreibefunktionen in Azure AD Connect aktivieren.



| Optionale Features | BESCHREIBUNG |
| --- | --- |
| Exchange-Hybridbereitstellung |Das Feature „Exchange-Hybridbereitstellung“ unterstützt die Koexistenz lokaler und Microsoft 365-basierter Exchange-Postfächer. Azure AD Connect synchronisiert eine bestimmte Gruppe von [Attributen](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) aus Azure AD mit Ihrem lokalen Verzeichnis. |
| Öffentliche Exchange-E-Mail-Ordner | Mit dem Feature „Öffentliche Exchange-E-Mail-Ordner“ können Sie Objekte für E-Mail-aktivierte öffentliche Ordner von Ihrer lokalen Active Directory-Instanz nach Azure AD synchronisieren. |
| Azure AD-App- und Attributfilterung |Mithilfe der App- und Attributfilterung von Azure AD kann die Gruppe synchronisierter Attribute individuell angepasst werden. Durch diese Option wird der Assistent um zwei weitere Konfigurationsseiten erweitert. Weitere Informationen finden Sie unter [Azure AD-App- und Attributfilterung](#azure-ad-app-and-attribute-filtering). |
| Kennworthashsynchronisierung |Wenn Sie als Anmeldelösung die Verbundoption ausgewählt haben, können Sie die Kennworthashsynchronisierung aktivieren. Anschließend können Sie diese als Sicherungsoption verwenden.  </br></br>Wenn Sie die Passthrough-Authentifizierung ausgewählt haben, kann diese Option aktiviert werden, um sicherzustellen, dass Legacyclients unterstützt werden, und um eine Sicherungslösung bereitzustellen.</br></br> Weitere Informationen finden Sie unter [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md).|
| Kennwortrückschreiben |Verwenden Sie diese Option, um sicherzustellen, dass Kennwortänderungen aus Azure AD in Ihr lokales Verzeichnis zurückgeschrieben werden. Weitere Informationen finden Sie unter [Erste Schritte mit der Kennwortverwaltung](../authentication/tutorial-enable-sspr.md). |
| Gruppenrückschreiben |Wenn Sie Microsoft 365-Gruppen verwenden, können Sie Gruppen in der lokalen Active Directory-Instanz darstellen. Diese Option ist nur verfügbar, wenn Exchange in Ihrer lokalen Active Directory-Instanz enthalten ist. Weitere Informationen finden Sie unter [Gruppenrückschreiben in Azure AD Connect](how-to-connect-group-writeback.md).|
| Geräterückschreiben |Verwenden Sie diese Option für Szenarien mit bedingtem Zugriff zum Rückschreiben von Geräteobjekten in Azure AD auf Ihre lokale Active Directory-Instanz. Weitere Informationen finden Sie unter [Aktivieren des Geräterückschreibens in Azure AD Connect](how-to-connect-device-writeback.md). |
| Verzeichniserweiterungen-Attributsynchronisierung |Wählen Sie diese Option aus, um bestimmte Attribute mit Azure AD zu synchronisieren. Weitere Informationen finden Sie unter [Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD-App- und Attributfilterung
Wenn Sie einschränken möchten, welche Attribute mit Azure AD synchronisiert werden, wählen Sie zunächst die verwendeten Dienste aus. Wenn Sie die Auswahlen auf dieser Seite ändern, müssen Sie explizit einen neuen Dienst auswählen, indem Sie den Installations-Assistenten erneut ausführen.

![Screenshot der optionalen Features von Azure AD-Apps.](./media/how-to-connect-install-custom/azureadapps2.png)

Auf der Grundlage der im vorherigen Schritt ausgewählten Dienste werden auf dieser Seite alle synchronisierten Attribute angezeigt. Diese Liste ist eine Kombination aller Objekttypen, die synchronisiert werden. Wenn einige Attribute nicht synchronisiert werden sollen, können Sie die Auswahl dieser Attribute deaktivieren.

![Screenshot der optionalen Features von Azure AD-Attributen.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Das Entfernen von Attributen kann sich auf die Funktionalität auswirken. Bewährte Methoden und Empfehlungen finden Sie im Thema zu [synchronisierten Attributen](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Verzeichniserweiterungen-Attributsynchronisierung
Sie können das Schema in Azure AD durch benutzerdefinierte Attribute erweitern, die von Ihrem Unternehmen hinzugefügt wurden, oder indem Sie andere Attribute in Active Directory verwenden. Wählen Sie auf der Seite **Optionale Features** die Option **Verzeichniserweiterungen-Attributsynchronisierung** aus, um dieses Feature zu verwenden. Auf der Seite **Verzeichniserweiterungen** können Sie weitere Attribute für die Synchronisierung auswählen.

>[!NOTE]
>Im Feld **Verfügbare Attribute** muss die Groß-/Kleinschreibung beachtet werden.

![Screenshot mit der Seite „Verzeichniserweiterungen“.](./media/how-to-connect-install-custom/extension2.png)

Weitere Informationen finden Sie unter [Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Aktivieren des einmaligen Anmeldens
Auf der Seite **Einmaliges Anmelden** konfigurieren Sie das einmalige Anmelden (Single Sign-on, SSO), um die Funktion mit der Kennwortsynchronisierung oder Passthrough-Authentifizierung zu verwenden. Sie führen diesen Schritt einmal für jede Gesamtstruktur aus, die mit Azure AD synchronisiert wird. Die Konfiguration umfasst zwei Schritte:

1.  Erstellen des erforderlichen Computerkontos in Ihrer lokalen Active Directory-Instanz
2.  Konfigurieren der Intranetzone der Clientcomputer zur Unterstützung des einmaligen Anmeldens

#### <a name="create-the-computer-account-in-active-directory"></a>Erstellen des Computerkontos in Active Directory
Für jede Gesamtstruktur, die in Azure AD Connect hinzugefügt wurde, müssen Sie Domänenadministrator-Anmeldeinformationen angeben, damit das Computerkonto in jeder Gesamtstruktur erstellt werden kann. Die Anmeldeinformationen werden nur zum Erstellen des Kontos verwendet. Sie werden nicht für andere Vorgänge gespeichert oder verwendet. Fügen Sie die Anmeldeinformationen auf der Seite **Einmaliges Anmelden aktivieren** hinzu, wie in der folgenden Abbildung dargestellt.

![Screenshot mit der Seite „Einmaliges Anmelden aktivieren“. Anmeldeinformationen für die Gesamtstruktur werden hinzugefügt.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Sie können die Gesamtstrukturen überspringen, in denen Sie kein einmaliges Anmelden verwenden möchten.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Konfigurieren der Intranetzone für Clientcomputer
Damit der Client automatisch bei der Intranetzone angemeldet wird, müssen Sie sicherstellen, dass die URL Teil der Intranetzone ist. So wird sichergestellt, dass der in die Domäne eingebundene Computer automatisch ein Kerberos-Ticket an Azure AD sendet, sobald eine Verbindung mit dem Unternehmensnetzwerk hergestellt wurde.

Auf einem Computer mit Gruppenrichtlinien-Verwaltungstools:

1.  Öffnen Sie die Gruppenrichtlinien-Verwaltungstools.
2.  Bearbeiten Sie die Gruppenrichtlinie, die auf alle Benutzer angewendet wird. Beispiel: Standardrichtlinie der Domäne.
3.  Wechseln Sie zu **Benutzerkonfiguration** > **Administrative Vorlagen** > **Windows-Komponenten** > **Internet Explorer** > **Internetsystemsteuerung** > **Seite „Sicherheit“** . Wählen Sie dann **Liste der Site zu Zonenzuweisungen**.
4.  Aktivieren Sie die Richtlinie. Geben Sie dann im Dialogfeld den Wertnamen `https://autologon.microsoftazuread-sso.com` und den Wert `1` ein. Das Setup sollte nun wie in der folgenden Abbildung aussehen.
  
    ![Screenshot mit Intranetzonen.](./media/how-to-connect-install-custom/sitezone.png)

6.  Wählen Sie zweimal **OK**.

## <a name="configuring-federation-with-ad-fs"></a>Konfigurieren des Verbunds mit AD FS
Sie können AD FS mit wenigen Klicks mit Azure AD Connect konfigurieren. Bevor Sie beginnen, benötigen Sie:

* Windows Server 2012 R2 oder höher für den Verbundserver. Die Remoteverwaltung sollte aktiviert sein.
* Windows Server 2012 R2 oder höher für den Webanwendungsproxy-Server. Die Remoteverwaltung sollte aktiviert sein.
* Ein TLS/SSL-Zertifikat für den Verbunddienstnamen, den Sie verwenden möchten (z. B. „sts.contoso.com“)

>[!NOTE]
>Sie können ein TLS/SSL-Zertifikat für Ihre AD FS-Farm mit Azure AD Connect aktualisieren, auch wenn Sie es nicht für die Verwaltung Ihrer Verbundvertrauensstellung verwenden.

### <a name="ad-fs-configuration-prerequisites"></a>Voraussetzungen für die AD FS-Konfiguration
Vergewissern Sie sich, dass WinRM auf den Remoteservern aktiviert ist, damit Sie Ihre AD FS-Farm mithilfe von Azure AD Connect konfigurieren können. Vergewissern Sie sich, dass Sie die anderen Aufgaben unter [Voraussetzungen für die Verbundinstallation und -konfiguration](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration) abgeschlossen haben. Stellen Sie außerdem sicher, dass Sie die in der Tabelle [Azure AD Connect und Verbund-/WAP-Server](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) aufgeführten Portanforderungen befolgen.

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Erstellen einer neuen AD FS-Farm oder Verwenden einer vorhandenen AD FS-Farm
Sie können eine vorhandene AD FS-Farm verwenden oder eine neue Farm erstellen. Wenn Sie eine neue Farm erstellen möchten, müssen Sie das TLS/SSL-Zertifikat bereitstellen. Wenn das TLS/SSL-Zertifikat kennwortgeschützt ist, werden Sie zur Eingabe des Kennworts aufgefordert.

![Screenshot mit der Seite „AD FS-Farm“](./media/how-to-connect-install-custom/adfs1.png)

Wenn Sie sich für die Verwendung einer bereits vorhandenen AD FS-Farm entscheiden, wird die Seite angezeigt, auf der Sie die Vertrauensstellung zwischen AD FS und Azure AD konfigurieren können.

>[!NOTE]
>Mit Azure AD Connect kann nur eine einzelne AD FS-Farm verwaltet werden. Wenn Sie über eine vorhandene Verbundvertrauensstellung verfügen, bei der Azure AD in der ausgewählten AD FS-Farm konfiguriert ist, wird die Vertrauensstellung von Azure AD Connect von Grund auf neu erstellt.

### <a name="specify-the-ad-fs-servers"></a>Angeben der AD FS-Server
Geben Sie die Server an, auf denen Sie AD FS installieren möchten. Sie können je nach Ihren Kapazitätsanforderungen einen oder mehrere Server hinzufügen. Verknüpfen Sie vor dieser Konfiguration alle AD FS-Server mit Active Directory. Dieser Schritt ist für Webanwendungsproxy-Server nicht erforderlich. 

Microsoft empfiehlt, einen einzelnen AD FS-Server für Test- und Pilotbereitstellungen zu installieren. Nach der Erstkonfiguration können Sie Azure AD Connect erneut ausführen und weitere Server hinzufügen und bereitstellen, um Ihren Skalierungsanforderungen gerecht zu werden.

> [!NOTE]
> Vergewissern Sie sich vor dieser Konfiguration, dass alle Ihre Server mit einer Azure AD-Domäne verknüpft sind.
>


![Screenshot mit der Seite „Verbundserver“.](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Angeben von Webanwendungsproxy-Servern
Geben Sie Ihre Webanwendungsproxy-Server an. Der Webanwendungsproxy-Server wird in Ihrem Umkreisnetzwerk bereitgestellt und ist auf das Extranet ausgerichtet. Er unterstützt Authentifizierungsanforderungen aus dem Extranet. Sie können je nach Ihren Kapazitätsanforderungen einen oder mehrere Server hinzufügen. 

Microsoft empfiehlt, einen einzelnen Webanwendungsproxy-Server für Test- und Pilotbereitstellungen zu installieren. Nach der Erstkonfiguration können Sie Azure AD Connect erneut ausführen und weitere Server hinzufügen und bereitstellen, um Ihren Skalierungsanforderungen gerecht zu werden. Es empfiehlt sich, eine entsprechende Anzahl von Proxyservern bereitzustellen, um die Authentifizierung über das Intranet zu ermöglichen.

> [!NOTE]
> - Wenn es sich bei dem verwendeten Konto nicht um ein lokales Administratorkonto auf den Webanwendungsproxy-Servern handelt, werden Sie zur Eingabe der Administratoranmeldeinformationen aufgefordert.
> - Vergewissern Sie sich vor der Angabe der Webanwendungsproxy-Server, dass zwischen dem Azure AD Connect-Server und dem Webanwendungsproxy-Server eine HTTP/HTTPS-Verbindung besteht.
> - Vergewissern Sie sich zudem, dass zwischen dem Webanwendungsserver und dem AD FS-Server eine HTTP/HTTPS-Verbindung besteht, um die Durchleitung von Authentifizierungsanforderungen zu ermöglichen.
>


![Screenshot mit der Seite „Webanwendungsproxy-Server“.](./media/how-to-connect-install-custom/adfs3.png)

Sie werden zur Eingabe von Anmeldeinformationen aufgefordert, damit der Webanwendungsserver eine sichere Verbindung mit dem AD FS-Server herstellen kann. Dabei muss es sich um Anmeldeinformationen für ein lokales Administratorkonto auf dem AD FS-Server handeln.

![Screenshot mit der Seite „Anmeldeinformationen“. Die Administratoranmeldeinformationen werden im Feld „Benutzername“ und „Kennwort“ eingegeben.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Angeben eines Dienstkontos für den AD FS-Dienst
Der AD FS-Dienst erfordert ein Domänendienstkonto zur Authentifizierung von Benutzern und zur Suche nach Benutzerinformationen in Active Directory. Zwei Dienstkontotypen werden unterstützt:

* **Gruppenverwaltetes Dienstkonto**: Dieser Kontotyp wurde von Windows Server 2012 in AD DS eingeführt. Dieser Kontotyp bietet Dienste wie AD FS. Dabei handelt es sich um ein einzelnes Konto, bei dem Sie das Kennwort nicht regelmäßig aktualisieren müssen. Verwenden Sie diese Option, wenn Sie in der Domäne, der die AD FS-Server angehören, bereits über Windows Server 2012-Domänencontroller verfügen.
* **Domänenbenutzerkonto**: Für diesen Kontotyp müssen Sie ein Kennwort angeben und dieses regelmäßig aktualisieren, wenn es abläuft. Verwenden Sie diese Option nur, wenn Sie in der Domäne, der die AD FS-Server angehören, über keine Windows Server 2012-Domänencontroller verfügen.

Wenn Sie **Gruppenverwaltetes Dienstkonto erstellen** ausgewählt haben und dieses Feature in Active Directory noch nie verwendet wurde, geben Sie Ihre Anmeldeinformationen für den Unternehmensadministrator ein. Diese werden zum Initiieren des Schlüsselspeichers und zum Aktivieren des Features in Active Directory verwendet.

> [!NOTE]
> Azure AD Connect überprüft, ob der AD FS-Dienst bereits als Dienstprinzipalname (Service Principal Name, SPN) in der Domäne registriert ist.  Die gleichzeitige Registrierung doppelter SPNs wird von Azure AD DS nicht unterstützt.  Wenn ein doppelter SPN gefunden wird, können Sie den Vorgang erst fortsetzen, nachdem der SPN entfernt wurde.

![Screenshot mit der Seite „AD FS-Dienstkonto“.](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Auswählen der Azure AD-Domäne für den Verbund
Verwenden Sie die Seite **Azure AD-Domäne**, um die Verbundbeziehung zwischen AD FS und Azure AD einzurichten. Hier konfigurieren Sie AD FS für die Bereitstellung von Sicherheitstoken für Azure AD. Außerdem konfigurieren Sie Azure AD so, dass Token von dieser AD FS-Instanz als vertrauenswürdig behandelt werden. 

Auf dieser Seite kann bei der Erstinstallation nur eine einzelne Domäne konfiguriert werden. Weitere Domänen können Sie später durch erneutes Ausführen von Azure AD Connect konfigurieren.

![Screenshot mit der Seite „Azure AD-Domäne“.](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Überprüfen der für den Verbund ausgewählten Azure AD-Domäne
Wenn Sie die Domäne auswählen, die Sie in einem Verbund verwenden möchten, stellt Azure AD Connect Informationen bereit, mit denen Sie eine nicht überprüfte Domäne überprüfen können. Weitere Informationen finden Sie unter [Hinzufügen und Überprüfen der Domäne](../fundamentals/add-custom-domain.md).

![Screenshot mit der Seite „Azure AD-Domäne“ und Informationen, die Sie zum Überprüfen der Domäne verwenden können.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect versucht in der Konfigurationsphase, die Domäne zu überprüfen. Wenn Sie nicht die erforderlichen DNS (Domain Name System)-Einträge hinzufügen, kann die Konfiguration nicht abgeschlossen werden.
>


## <a name="configuring-federation-with-pingfederate"></a>Konfigurieren des Verbunds mit PingFederate
Sie können PingFederate mit wenigen Klicks mit Azure AD Connect konfigurieren. Die folgenden Voraussetzungen müssen erfüllt sein:
- PingFederate 8.4 oder höher.  Weitere Informationen finden Sie unter [PingFederate Integration with Azure Active Directory and Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html) (PingFederate-Integration in Azure Active Directory und Microsoft 365).
- Ein TLS/SSL-Zertifikat für den Verbunddienstnamen, den Sie verwenden möchten (z. B. „sts.contoso.com“)

### <a name="verify-the-domain"></a>Überprüfen der Domäne
Nachdem Sie sich entschieden haben, den Verbund mit PingFederate einzurichten, werden Sie aufgefordert, die Domäne zu überprüfen, mit der ein Verbund hergestellt werden soll.  Wählen Sie im Dropdownmenü die Domäne aus.

![Screenshot mit der Seite „Azure AD-Domäne“. Die Beispieldomäne „contoso.com“ ist ausgewählt.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Exportieren der PingFederate-Einstellungen


Konfigurieren Sie PingFederate als Verbundserver für jede Azure-Verbunddomäne.  Wählen Sie **Einstellungen exportieren** aus, um diese Informationen an Ihren PingFederate-Administrator weiterzugeben.  Der Verbundserveradministrator aktualisiert die Konfiguration und gibt dann die PingFederate-Server-URL und die Portnummer an, damit Azure AD Connect die Metadateneinstellungen überprüfen kann.  

![Screenshot mit der Seite „PingFederate-Einstellungen“. Die Schaltfläche „Einstellungen exportieren“ wird oben auf der Seite angezeigt.](./media/how-to-connect-install-custom/ping2.png)

Wenden Sie sich an den PingFederate-Administrator, um alle Überprüfungsprobleme zu beheben.  Auf der folgenden Abbildung sind Informationen zu einem PingFederate-Server zu sehen, der keine gültige Vertrauensstellung mit Azure aufweist.

![Screenshot mit Serverinformationen: Der PingFederate-Server wurde gefunden, aber die Dienstanbieterverbindung für Azure fehlt oder ist deaktiviert.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Überprüfen der Verbundkonnektivität
Azure AD Connect überprüft die Authentifizierungsendpunkte, die aus den PingFederate-Metadaten im vorherigen Schritt abgerufen werden.  Azure AD Connect versucht zunächst, die Endpunkte mithilfe Ihrer lokalen DNS-Server aufzulösen.  Als Nächstes wird versucht, die Endpunkte mithilfe eines externen DNS-Anbieters aufzulösen.  Wenden Sie sich an den PingFederate-Administrator, um alle Überprüfungsprobleme zu beheben.  

![Screenshot mit der Seite „Konnektivität überprüfen“.](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Überprüfen der Verbundanmeldung
Zu guter Letzt können Sie den neu konfigurierten Verbundanmeldevorgang überprüfen, indem Sie sich bei der Verbunddomäne anmelden. Wenn die Anmeldung erfolgreich ist, wird der Verbund mit PingFederate erfolgreich konfiguriert.

![Screenshot mit der Seite „Verbundanmeldung überprüfen“. Eine Meldung im unteren Bereich weist auf eine erfolgreiche Anmeldung hin.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Konfigurieren und Überprüfen von Seiten
Die Konfiguration wird auf der Seite **Konfigurieren** vorgenommen.

> [!NOTE]
> Wenn Sie einen Verbund konfiguriert haben, sollten Sie sich vergewissern, dass Sie auch die [Namensauflösung für Verbundserver](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) konfiguriert haben, bevor Sie mit der Installation fortfahren.
>



![Screenshot mit der Seite „Bereit zur Konfiguration“.](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Verwenden des Stagingmodus
Mit dem Stagingmodus können Sie parallel einen neuen Synchronisierungsserver einrichten. Wenn Sie dieses Setup verwenden möchten, kann nur ein Synchronisierungsserver Exporte in ein einzelnes Verzeichnis in der Cloud ausführen. Wenn Sie jedoch eine Verschiebung von einem anderen Server durchführen möchten, z. B. einem Server, auf dem DirSync ausgeführt wird, kann Azure AD Connect im Stagingmodus aktiviert werden. 

Wenn Sie das Stagingsetup aktivieren, importiert und synchronisiert das Synchronisierungsmodul Daten wie üblich. Es werden jedoch keine Daten nach Azure AD oder Active Directory exportiert. Im Stagingmodus sind die Kennwortsynchronisierung und das Kennwortrückschreiben deaktiviert.

![Screenshot mit der Option „Stagingmodus aktivieren“.](./media/how-to-connect-install-custom/stagingmode.png)

Im Stagingmodus können Sie die erforderlichen Änderungen am Synchronisierungsmodul vornehmen und die Exporte überprüfen. Wenn die Konfiguration Ihren Vorstellungen entspricht, führen Sie erneut den Installations-Assistenten aus, und deaktivieren Sie den Stagingmodus. 

Daraufhin werden Daten vom Server nach Azure AD exportiert. Stellen Sie sicher, das der andere Server währenddessen deaktiviert ist, sodass nur ein Server aktiv exportieren kann.

Weitere Informationen finden Sie unter [Stagingmodus](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Überprüfen der Verbundkonfiguration
Wenn Sie die Schaltfläche **Überprüfen** auswählen, werden die DNS-Einstellungen von Azure AD Connect überprüft. Die folgenden Einstellungen werden überprüft:

* **Intranetkonnektivität**
    * Verbund-FQDN auflösen: Azure AD Connect überprüft, ob der Verbund-FQDN vom DNS aufgelöst werden kann, um Konnektivität zu gewährleisten. Falls der FQDN von Azure AD Connect nicht aufgelöst werden kann, ist die Überprüfung nicht erfolgreich. Vergewissern Sie sich, dass der DNS-Eintrag für den Verbunddienst-FQDN vorhanden ist, um die Überprüfung abzuschließen.
    * DNS-A-Eintrag: Azure AD Connect überprüft, ob für Ihren Verbunddienst ein A-Eintrag vorhanden ist. Sollte kein A-Eintrag vorhanden sein, ist die Überprüfung nicht erfolgreich. Erstellen Sie einen A-Eintrag (und keinen CNAME-Eintrag) für Ihren Verbund-FQDN, um die Überprüfung abzuschließen.
* **Extranetkonnektivität**
    * Verbund-FQDN auflösen: Azure AD Connect überprüft, ob der Verbund-FQDN vom DNS aufgelöst werden kann, um Konnektivität zu gewährleisten.

      ![Screenshot mit der Seite „Installation abgeschlossen“.](./media/how-to-connect-install-custom/completed.png)

      ![Screenshot mit der Seite „Installation abgeschlossen“. Eine Meldung gibt an, dass die Intranetkonfiguration überprüft wurde.](./media/how-to-connect-install-custom/adfs7.png)

Um die End-to-End-Authentifizierung zu überprüfen, führen Sie manuell einen oder mehrere der folgenden Tests durch:

* Sobald die Synchronisierung abgeschlossen ist, verwenden Sie in Azure AD Connect die zusätzliche Aufgabe **Verbundanmeldung überprüfen**, um die Authentifizierung für ein lokales Benutzerkonto Ihrer Wahl zu überprüfen.
* Vergewissern Sie sich auf einem in die Domäne eingebundenen Computer im Intranet, dass Sie sich über einen Browser anmelden können. Stellen Sie eine Verbindung mit https://myapps.microsoft.comher. Verwenden Sie dann das angemeldete Konto, um die Anmeldung zu überprüfen. Das integrierte Azure AD DS-Administratorkonto wird nicht synchronisiert und kann nicht für die Überprüfung verwendet werden.
* Vergewissern Sie sich, dass Sie sich auf einem Gerät im Extranet anmelden können. Stellen Sie auf einem privaten Computer oder auf einem mobilen Gerät eine Verbindung mit https://myapps.microsoft.com her. Geben Sie dann Ihre Anmeldeinformationen an.
* Überprüfen Sie die Anmeldung per Rich Client. Stellen Sie eine Verbindung mit https://testconnectivity.microsoft.comher. Wählen Sie dann **Office 365** > **Office 365-Test für einmaliges Anmelden** aus.

## <a name="troubleshoot"></a>Problembehandlung
Dieser Abschnitt enthält Informationen zur Problembehandlung, die hilfreich sind, wenn bei der Installation von Azure AD Connect ein Problem auftritt.

Wenn Sie eine Azure AD Connect-Installation anpassen, können Sie auf der Seite **Erforderliche Komponenten installieren** die Option **Vorhandenen SQL Server-Computer verwenden** auswählen. Unter Umständen tritt der folgende Fehler auf: „The ADSync database already contains data and cannot be overwritten. Please remove the existing database and try again.“ (Die ADSync-Datenbank enthält bereits Daten und kann nicht überschrieben werden. Entfernen Sie die vorhandene Datenbank, und versuchen Sie es noch einmal.)

![Screenshot der Seite „Installieren der erforderlichen Komponenten“ Unten auf der Seite wird ein Fehler angezeigt.](./media/how-to-connect-install-custom/error1.png)

Dieser Fehler wird angezeigt, weil eine Datenbank mit dem Namen *ADSync* bereits in der von Ihnen angegebenen SQL-Instanz von SQL Server vorhanden ist.

Der Fehler tritt in der Regel nach der Deinstallation von Azure AD Connect auf.  Die Datenbank wird nicht von dem Computer gelöscht, auf dem SQL Server ausgeführt wird, wenn Sie Azure AD Connect deinstallieren.

So beheben Sie dieses Problem:

1. Überprüfen Sie die ADSync-Datenbank, die Azure AD Connect vor der Deinstallation verwendet hat. Stellen Sie sicher, dass die Datenbank nicht mehr verwendet wird.

2. Sichern Sie die Datenbank.

3. Löschen Sie die Datenbank:
    1. Stellen Sie über **Microsoft SQL Server Management Studio** eine Verbindung mit der SQL-Instanz her. 
    1. Suchen Sie die **ADSync**-Datenbank, und klicken Sie mit der rechten Maustaste darauf.
    1. Wählen Sie im Kontextmenü **Löschen** aus.
    1. Wählen Sie **OK** aus, um die Datenbank zu löschen.

![Screenshot mit Microsoft SQL Server Management Studio. Die AD-Synchronisierung ist ausgewählt.](./media/how-to-connect-install-custom/error2.png)

Nach dem Löschen der ADSync-Datenbank wählen Sie **Installieren** aus, um die Installation zu wiederholen.

## <a name="next-steps"></a>Nächste Schritte
Nachdem die Installation abgeschlossen ist, melden Sie sich von Windows ab. Melden Sie sich dann wieder an, bevor Sie den Synchronization Service Manager oder Synchronisierungsregel-Editor verwenden.

Nachdem Sie Azure AD Connect installiert haben, können Sie [die Installation überprüfen und Lizenzen zuweisen](how-to-connect-post-installation.md).

Weitere Informationen zu den Features, die Sie während der Installation aktiviert haben, finden Sie unter [Verhindern von versehentlichen Löschungsvorgängen](how-to-connect-sync-feature-prevent-accidental-deletes.md) und [Azure AD Connect Health](how-to-connect-health-sync.md).

Weitere Informationen zu anderen gängigen Themen finden Sie unter [Azure AD Connect Sync: Scheduler](how-to-connect-sync-feature-scheduler.md) und [Integrieren lokaler Identitäten in Azure AD](whatis-hybrid-identity.md).

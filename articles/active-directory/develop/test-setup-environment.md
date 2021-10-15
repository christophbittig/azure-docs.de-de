---
title: Einrichten einer Testumgebung für Ihre Anwendung
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Azure Active Directory-Testumgebung einrichten, damit Sie Ihre in die Microsoft Identitätsplattform integrierte Anwendung testen können.  Beurteilen Sie, ob Sie einen separaten Tenant für Tests benötigen oder ob Sie Ihren Produktions-Tenant verwenden können.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2021
ms.author: arcrowe
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: e695a7028432b1044c0c1896447d474b2716097f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368074"
---
# <a name="set-up-your-applications-azure-ad-test-environment"></a>Einrichten der Azure AD-Testumgebung für Ihre Anwendung

Als Entwickler nutzen Sie den Lebenszyklus der Softwareentwicklung und verschieben Ihre Anwendung zwischen Entwicklungs-, Test- und Produktionsumgebung.  Wenn dieser Prozess für Anwendungen verwendet wird, die durch die Microsoft-Identitätsplattform geschützt sind, sollten Sie eine Azure Active Directory (Azure AD)-Umgebung einrichten, die für Tests verwendet wird.  Diese Umgebung kann sowohl in den frühen Testphasen des Entwicklungslebenszyklus als auch als langfristige, permanente Testumgebung verwendet werden.  Abhängig von Ihren Anforderungen an die Ressourcenisolierung können Sie den Azure AD-Produktions-Tenant Ihrer Organisation oder einen völlig separaten Tenant für die Tests verwenden.  

In diesem Artikel erfahren Sie, wie Sie eine Azure AD-Testumgebung einrichten, damit Sie Ihre in die Microsoft Identitätsplattform integrierte Anwendung testen können.  Beurteilen Sie den Grad der benötigten Isolierung und ob Sie einen separaten Tenant für Tests benötigen oder ob Sie Ihren Produktions-Tenant verwenden können.

## <a name="decide-the-level-of-isolation-needed"></a>Bestimmen Sie den erforderlichen Isolierungsgrad
Im Allgemeinen ist es einfacher und weniger aufwändig, Ihren Produktions-Tenant als Testumgebung zu verwenden. Dies ist jedoch nur dann eine praktikable Option, wenn Sie das richtige Maß an Isolierung zwischen Test- und Produktionsressourcen erreichen können.  Die Isolierung ist besonders wichtig für Szenarien mit hohen Berechtigungen.

Richten Sie Ihre Testumgebung in einem separaten Tenant ein (nicht im Produktions-Tenant Ihres Unternehmens), wenn:
- Sie eine Reihe von Ressourcen haben, die eindeutige, Tenant-weite Einstellungen erfordern. So kann es beispielsweise sein, dass Ihre Anwendung selbst auf Tenant-Ressourcen zugreifen muss und nicht im Namen eines Benutzers (mit "App-only"-Rechten).  Für den reinen App-Zugriff ist eine Admin-Zustimmung erforderlich, die für den gesamten Tenant gilt, und diese Berechtigungen lassen sich innerhalb einer Tenant-Grenze nur schwer sicher einschränken.
- Sie haben eine minimale Risikotoleranz für den unbefugten Zugriff von Tenant-Mitgliedern auf Ihre Testressourcen.
- Konfigurationsänderungen können kritische Auswirkungen auf Ihre Produktionsumgebung haben.
- Sie sind nicht in der Lage, Testbenutzer und zugehörige Testdaten in Ihrem Tenant zu erstellen.
- Sie planen, automatische Anmeldungen bei Ihrer Anwendung zu Testzwecken durchzuführen, und Ihr Produktions-Tenant hat Richtlinien für Authentifizierungsmethoden konfiguriert, die eine gewisse Benutzerinteraktion erfordern.  Wenn beispielsweise die Multifaktor-Authentifizierung für alle Benutzer erforderlich ist, können Sie keine automatisierten Anmeldungen für Integrationstests durchführen.
- Sie müssen sicherstellen, dass globale Administratoren keine spezifischen Testressourcen verwalten oder darauf zugreifen können. Sie müssen diese Ressourcen in einem separaten Tenant mit separaten globalen Administratoren isolieren.
- Das Hinzufügen von Nicht-Produktionsressourcen und/oder Arbeitslast zu Ihrem Produktions-Tenant würde [ die Service- oder Drosselungsgrenzen](test-throttle-service-limits.md) für diesen Tenant überschreiten.

Wenn keine dieser Bedingungen auf Sie zutrifft, folgen Sie diesen Schritten, um [ Ihre Testumgebung in Ihrem Produktions-Tenant einzurichten](#set-up-a-test-environment-in-your-production-tenant).  Wenn jedoch eine dieser Bedingungen zutrifft, sollten Sie [ eine Testumgebung in einem separaten Tenant](#set-up-a-test-environment-in-a-separate-tenant) einrichten.

## <a name="set-up-a-test-environment-in-a-separate-tenant"></a>Einrichten einer Testumgebung in einem separaten Tenant
Wenn Sie Ihre Testanwendung nicht sicher in Ihrem Produktions-Tenant einschränken können, können Sie einen separaten Tenant für Entwicklungs- und Testzwecke erstellen. 

### <a name="get-a-test-tenant"></a>Besorgen Sie sich einen Test-Tenant
Wenn Sie noch keinen dedizierten Test-Tenant haben, können Sie mit dem Microsoft 365 Developer Program kostenlos einen erstellen oder selbst einen erstellen. 

#### <a name="join-the-microsoft-365-developer-program-recommended"></a>Nehmen Sie am Microsoft 365-Entwicklerprogramm teil (empfohlen) 
Das [Microsoft 365-Entwicklerprogramm](https://developer.microsoft.com/microsoft-365/dev-program) ist kostenlos und ermöglicht das automatische Hinzufügen von Testbenutzerkonten und Beispieldatenpaketen zum Tenant.
1. Klicken Sie auf die Schaltfläche **Jetzt teilnehmen** auf dem Bildschirm.
2. Melden Sie sich mit einem neuen Microsoft-Konto an, oder verwenden Sie ein bereits vorhandenes (Geschäfts-)Konto.
3. Wählen Sie auf der Registrierungsseite Ihre Region aus, geben Sie einen Firmennamen ein, und akzeptieren Sie die Bestimmungen des Programms, bevor Sie auf **Weiter** klicken.
4. Klicken Sie auf **Abonnement einrichten**. Geben Sie die Region an, in der Sie Ihren neuen Mandanten erstellen möchten, erstellen Sie einen Benutzernamen und eine Domäne, und geben Sie ein Kennwort ein. Daraufhin werden ein neuer Mandant und der erste Administrator des Mandanten erstellt.
5. Geben Sie die Sicherheitsinformationen ein, die zum Schutz des Administratorkontos Ihres neuen Mandanten erforderlich sind. Dadurch wird die mehrstufige Authentifizierung für das Konto eingerichtet

#### <a name="manually-create-a-tenant"></a>Manuelles Erstellen eines Tenants  
Sie können [manuell einen Tenant](quickstart-create-new-tenant.md) erstellen, der bei der Erstellung leer ist und mit Testdaten konfiguriert werden muss.

### <a name="populate-your-tenant-with-users"></a>Bevölkern Sie Ihren Tenant mit Benutzern  
Der Einfachheit halber können Sie sich selbst und andere Mitglieder Ihres Entwicklungsteams als Gastbenutzer in den Tenant einladen.  Dadurch werden separate Gastobjekte im Test-Tenant erstellt, aber Sie müssen nur einen Satz Anmeldeinformationen für Ihr Unternehmenskonto und Ihr Testkonto verwalten.
1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Azure Active Directory**.
2. Wechseln Sie zu **Benutzer**.
3. Klicken Sie auf **Neuer Gastbenutzer** und laden Sie die E-Mail-Adresse Ihres Arbeitskontos ein.
4. Wiederholen Sie diesen Vorgang für andere Mitglieder des Entwicklungs- und/oder Testteams für Ihre Anwendung.

Sie können auch Testbenutzer in Ihrem Testmandanten erstellen.  Wenn Sie eines der Microsoft 365-Beispielpakete verwendet haben, verfügen Sie möglicherweise bereits über einige Testbenutzer in Ihrem Tenant.  Falls nicht, sollten Sie als Tenant-Administrator in der Lage sein, selbst welche anzulegen.
1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Azure Active Directory**.
2. Wechseln Sie zu **Benutzer**.
3. Klicken Sie auf **Neuer Benutzer** und erstellen Sie einige neue Testbenutzer in Ihrem Verzeichnis.

### <a name="get-an-azure-ad-subscription-optional"></a>Erhalten Sie ein Azure AD-Abonnement (optional)
Wenn Sie die Premiumfunktionen von Azure AD in Ihrer Anwendung vollständig testen möchten, müssen Sie Ihren Tenant für eine [Premium P1- oder Premium P2-Lizenz](https://azure.microsoft.com/pricing/details/active-directory/) anmelden.

Wenn Sie sich über das Microsoft 365-Entwicklerprogramm angemeldet haben, wird Ihr Test-Tenant mit Azure AD P2-Lizenzen geliefert.  Falls nicht, können Sie trotzdem eine einmonatige [kostenlose Testversion von Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/) aktivieren.

### <a name="create-and-configure-an-app-registration"></a>Erstellen und konfigurieren Sie eine App-Registrierung
Sie müssen eine App-Registrierung erstellen, die Sie in Ihrer Testumgebung verwenden möchten.  Dies sollte eine von der späteren Produktionsregistrierung getrennte Registrierung sein, um die Sicherheitsisolierung zwischen Ihrer Testumgebung und Ihrer Produktionsumgebung aufrechtzuerhalten.  Wie Sie Ihre Anwendung konfigurieren, hängt von der Art der Anwendung ab, die Sie erstellen.  Weitere Informationen finden Sie in den Schritten zur Anwendungsregistrierung für Ihr Anwendungsszenario im linken Navigationsbereich, z. B. in diesem Artikel zur Registrierung von [Webanwendungen](scenario-web-app-sign-user-app-registration.md).
   
### <a name="populate-your-tenant-with-policies"></a>Befüllen Sie Ihren Tenant mit Richtlinien
Wenn Ihre Anwendung in erster Linie von einer einzigen Organisation genutzt wird (allgemein als Single Tenant bezeichnet) und Sie Zugang zu diesem Produktions-Tenant haben, sollten Sie versuchen, die Einstellungen Ihres Produktions-Tenant zu replizieren, die das Verhalten Ihrer Anwendung beeinflussen können.  Dadurch wird die Wahrscheinlichkeit unerwarteter Fehler beim Betrieb in der Produktion verringert.

#### <a name="conditional-access-policies"></a>Bedingte Zugriffsrichtlinien 
Durch die Replizierung der Richtlinien für den bedingten Zugriff wird sichergestellt, dass bei der Umstellung auf die Produktion keine unerwarteten Zugriffsblockaden auftreten und Ihre Anwendung die zu erwartenden Fehler angemessen behandeln kann.

Die Anzeige der Zugriffsrichtlinien Ihres Produktions-Tenants muss möglicherweise von einem Unternehmensadministrator durchgeführt werden.  
1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) mit Ihrem Produktions-Tenant-Konto an
1. Gehen Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Bedingter Zugriff**.
1. Zeigen Sie die Liste der Richtlinien in Ihrem Tenant an.  Klicken Sie auf die erste Richtlinie.
1. Navigieren Sie zu **Cloud-Anwendungen oder -Aktionen**.  
1. Wenn die Richtlinie nur für eine ausgewählte Gruppe von Anwendungen gilt, fahren Sie mit der nächsten Richtlinie fort.  Wenn nicht, wird sie wahrscheinlich auch für Ihre Anwendung gelten, wenn Sie in die Produktion wechseln.  Kopieren Sie die Richtlinie in Ihren Test-Tenant.

Navigieren Sie in einer neuen Registerkarte oder Browsersitzung zum [Azure-Portal](https://portal.azure.com) und melden Sie sich bei Ihrem Test-Tenant an.
1. Gehen Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Bedingter Zugriff**.
1. Klicken Sie auf **Neue Richtlinie**
1. Kopieren Sie die Einstellungen aus der Richtlinie des Produktionsmieters, die Sie in den vorherigen Schritten ermittelt haben.

#### <a name="permission-grant-policies"></a>Richtlinien für die Erteilung von Berechtigungen
Durch die Replizierung von Berechtigungsrichtlinien wird sichergestellt, dass Sie bei der Umstellung auf die Produktion keine unerwarteten Aufforderungen zur Erteilung der Administratorberechtigung erhalten. 

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) mit Ihrem Produktions-Tenant-Konto an
1. Klicken Sie auf **Azure Active Directory**.
1. Navigieren Sie zu **Unternehmensanwendungen ausführen**. 
1. Gehen Sie in Ihrem Produktions-Tenant zu **Azure Active Directory** > **Unternehmensanwendungen** > **Zustimmung und Berechtigungen** > **Benutzerzustimmung** Einstellungen.  Kopieren Sie die dortigen Einstellungen in Ihren Testmandanten.  
    
#### <a name="token-lifetime-policies"></a>Richtlinien für die Token-Lebensdauer
Die Replikation von Richtlinien für die Token-Lebensdauer stellt sicher, dass die für Ihre Anwendung ausgestellten Token in der Produktion nicht unerwartet ablaufen.  
 
Token-Lebensdauerrichtlinien können derzeit nur über PowerShell verwaltet werden. Lesen Sie über [konfigurierbare Token-Lebensdauern](active-directory-configurable-token-lifetimes.md), um zu erfahren, wie Sie Richtlinien für die Token-Lebensdauer identifizieren, die für Ihre gesamte Produktionsorganisation gelten.  Kopieren Sie diese Richtlinien in Ihren Test-Tenant.
 
## <a name="set-up-a-test-environment-in-your-production-tenant"></a>Richten Sie eine Testumgebung in Ihrem Produktions-Tenant ein
Wenn Sie Ihre Testanwendung sicher in Ihrem Produktions-Tenant einschränken können, richten Sie Ihren Tenant für Testzwecke ein.

### <a name="create-and-configure-an-app-registration"></a>Erstellen und konfigurieren Sie eine App-Registrierung
Sie müssen eine App-Registrierung erstellen, die Sie in Ihrer Testumgebung verwenden möchten.  Dies sollte eine von der späteren Produktionsregistrierung getrennte Registrierung sein, um die Sicherheitsisolierung zwischen Ihrer Testumgebung und Ihrer Produktionsumgebung aufrechtzuerhalten.  Wie Sie Ihre Anwendung konfigurieren, hängt von der Art der Anwendung ab, die Sie erstellen.  Weitere Informationen finden Sie in den [Schritten zur App-Registrierung für Ihr App-Szenario](scenario-web-app-sign-user-app-registration.md) im linken Navigationsbereich.

### <a name="create-some-test-users"></a>Erstellen Sie einige Testbenutzer
Sie müssen einige Testbenutzer mit zugehörigen Testdaten erstellen, die Sie beim Testen Ihrer Szenarien verwenden können.  Dieser Schritt muss eventuell von einem Administrator durchgeführt werden
1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Azure Active Directory**.
2. Wechseln Sie zu **Benutzer**.
3. Klicken Sie auf **Neuer Benutzer** und erstellen Sie einige neue Testbenutzer in Ihrem Verzeichnis.

### <a name="add-the-test-users-to-a-group-optional"></a>Hinzufügen der Testbenutzer zu einer Gruppe (optional)
Der Einfachheit halber können Sie alle diese Benutzer einer Gruppe zuordnen, was weitere Zuordnungsvorgänge erleichtert.  
1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Azure Active Directory**.
2. Gehen Sie zu **Gruppen**.
3. Klicken Sie auf **Neue Gruppe**.
4. Wählen Sie entweder **Sicherheit** oder **Microsoft 365** als Gruppentyp.
5. Benennen Sie die Gruppe.
6. Fügen Sie die im vorherigen Schritt erstellten Testbenutzer hinzu.

### <a name="restrict-your-test-application-to-specific-users"></a>Beschränken Sie Ihre Testanwendung auf bestimmte Benutzer
Sie können die Benutzer in Ihrem Mandanten, die Ihre Testanwendung verwenden dürfen, durch Benutzerzuweisung auf bestimmte Benutzer oder Gruppen beschränken.  Als Sie [eine App über App-Registrierungen](#create-and-configure-an-app-registration) erstellten, wurde auch eine Darstellung Ihrer App in **Unternehmensanwendungen** erstellt.  Verwenden Sie die Einstellungen in **Unternehmensanwendungen**, um einzuschränken, wer die Anwendung in Ihrem Mandanten verwenden kann.

> [!IMPORTANT]
> Wenn es sich bei Ihrer Anwendung um eine [Multi-Tenant-Anwendung](v2-supported-account-types.md) handelt, schränkt dieser Vorgang Benutzer in anderen Tenants nicht davon ab, sich bei Ihrer Anwendung anzumelden und sie zu nutzen.  Er schränkt nur Benutzer in dem Tenant ein, in dem die Benutzerzuweisung konfiguriert ist. 

Detaillierte Anweisungen zur Beschränkung einer App auf bestimmte Benutzer in einem Tenant finden Sie unter [Beschränkung Ihrer App auf eine Gruppe von Benutzern](howto-restrict-your-app-to-a-set-of-users.md).

## <a name="next-steps"></a>Nächste Schritte
 
Erfahren Sie mehr über [Drosselung und Service-Limits](test-throttle-service-limits.md), auf die Sie beim Einrichten einer Testumgebung stoßen könnten.

Ausführlichere Informationen zu Testumgebungen finden Sie unter [Sicherung von Azure-Umgebungen mit Azure Active Directory](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/).
  

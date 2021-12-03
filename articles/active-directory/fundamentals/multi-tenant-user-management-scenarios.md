---
title: Häufige Szenarien für die Verwendung der mehrinstanzenfähigen Benutzerverwaltung in Azure Active Directory
description: Erfahren Sie mehr über häufige Szenarien, in denen Gastkonten zum Konfigurieren des Benutzerzugriffs über Azure Active Directory-Mandanten hinweg verwendet werden können.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fde715f688d7e19477894f3239d174d7925edb78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059393"
---
# <a name="multi-tenant-user-management-scenarios"></a>Szenarien für die mehrinstanzenfähige Benutzerverwaltung

## <a name="end-user-initiated-scenario"></a>Vom Endbenutzer initiiertes Szenario
Für das vom Endbenutzer initiierte Szenario delegieren Ressourcen-Mandantenadministratoren bestimmte Funktionen an Benutzer im Mandanten. Administratoren ermöglichen Endbenutzern, Gastbenutzer in den Mandanten, eine App oder eine Ressource einzuladen. Benutzer aus dem Home-Mandanten werden einzeln eingeladen oder registriert.

Ein Beispiel für einen Anwendungsfall wäre für ein globales Dienstleistungsunternehmen, das mit Beratern an einem Projekt arbeitet. Benutzer von Subunternehmern benötigen Zugriff auf die Anwendungen und Dokumente des Unternehmens. Administratoren des Unternehmens können die Möglichkeit, Subunternehmer einzuladen oder Self-Service für den Ressourcenzugriff zu konfigurieren, an die Endbenutzer delegieren.

### <a name="provision-accounts"></a>Bereitstellen von Konten

Es gibt viele Möglichkeiten, wie Endbenutzer eingeladen werden können, auf Ressourcen-Mandantenressourcen zuzugreifen. Hier sind fünf der am häufigsten verwendeten: 

* [Anwendungsbasierte Einladungen](../external-identities/o365-external-user.md). Microsoft-Anwendungen können die Einladung von Gastbenutzern aktivieren. B2B-Einladungseinstellungen müssen sowohl in Azure AD B2B als auch in der bzw. den relevanten Anwendungen konfiguriert werden.

* [MyApps]../user-help/my-apps-portal-end-user-access). Benutzer laden einen Gastbenutzer mithilfe von MyApps zu einer Anwendung ein und weisen diesen zu. Das Benutzerkonto muss über Berechtigungen von genehmigenden Personen für [Anwendungs-Self-Service-Registrierung](../manage-apps/manage-self-service-access.md) verfügen. Sie können Gastbenutzer zu einer Gruppe einladen, wenn sie Gruppenbesitzer sind.

* [Berechtigungsverwaltung](../governance/entitlement-management-overview.md): Ermöglicht es Administratoren oder Ressourcenbesitzern, Ressourcen, zulässige externe Organisationen, den Ablauf von Gastbenutzern und Zugriffsrichtlinien in Zugriffspaketen zu verknüpfen. Zugriffspakete können veröffentlicht werden, um die Self-Service-Registrierung für den Ressourcenzugriff durch Gastbenutzer zu ermöglichen.

* [Azure-Portal](../external-identities/add-users-administrator.md) Endbenutzer mit der [Rolle „Gast-Einladender“](../external-identities/delegate-invitations.md) können sich beim Azure-Portal anmelden und Gastbenutzer über das Benutzermenü in Azure Active Directory einladen.

* [Programmgesteuerte (PowerShell, Graph-API)](../external-identities/customize-invitation-api.md) Endbenutzer mit der [Rolle „Gast-Einladender“](../external-identities/delegate-invitations.md) können Gastbenutzer über PowerShell oder die Graph API einladen. 

### <a name="redeem-invitations"></a>Einlösen von Einladungen

Im Rahmen der Bereitstellung von Konten für den Zugriff auf eine Ressource werden E-Mail-Einladungen an die E-Mail-Adresse der eingeladenen Benutzer gesendet. Wenn ein eingeladener Benutzer eine Einladung empfängt, kann er:
* Dem in der E-Mail enthaltenen Link zur Einlösungs-URL folgen.
* Versuchen, direkt auf die Ressource zuzugreifen. 

Wenn der Benutzer versucht, direkt auf die Ressource zuzugreifen, wird das als Just-in-Time-Einlösung (JIT-Einlösung) bezeichnet. Im Folgenden finden Sie die Benutzeroberflächen für jede Einlösungsmethode. 

#### <a name="redemption-url"></a>Einlösungs-URL

Durch Zugriff auf die [Einlösungs-URL](../external-identities/redemption-experience.md) in der E-Mail kann der eingeladene Benutzer die Einladung genehmigen oder ablehnen (bei Bedarf wird ein Gastbenutzerkonto erstellt). 

#### <a name="just-in-time-redemption"></a>Just-in-Time-Einlösung

Der Benutzer kann direkt auf die Ressourcen-URL für die Just-in-Time-Einlösung zugreifen, wenn Folgendes gilt:

* Der eingeladene Benutzer verfügt bereits über eine Azure AD oder ein Microsoft-Konto  
‎-or-

* Wenn [einmalige E-Mail-Passcodes](../external-identities/one-time-passcode.md) aktiviert sind

Einige Punkte während der JIT-Einlösung:

* Wenn Administratoren die Annahme von Datenschutzbestimmungen nicht unterdrückt haben, muss der Benutzer die Seite mit den Datenschutzbestimmungen akzeptieren, bevor er auf die Ressource zugreift.

* PowerShell ermöglicht die Kontrolle darüber, ob beim Einladen [über PowerShell](/powershell/module/azuread/new-azureadmsinvitation?view=azureadps-2.0&preserve-view=true) eine E-Mail gesendet wird.

* Sie können Einladungen für Gastbenutzer aus bestimmten Organisationen zulassen oder blockieren, indem Sie eine [Positivliste oder eine Sperrliste](../external-identities/allow-deny-list.md) verwenden.

Weitere Informationen finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](../external-identities/redemption-experience.md).

#### <a name="important--enable-one-time-passcode-authentication"></a>Wichtig: Einmalige Passcode-Authentifizierung aktivieren 

Es wird dringend empfohlen, die [einmalige Passcode-Authentifizierung per E-Mail](../external-identities/one-time-passcode.md) zu aktivieren. Dieses Feature authentifiziert Gastbenutzer, wenn sie nicht auf andere Weise authentifiziert werden können, z. B. per:

* Azure AD

* Microsoft-Konto (MSA)

* Gmail-Konto über den Google-Verbund

* Konto aus einem SAML/WS-Fed-IDP über direkten Verbund

Bei der Authentifizierung mit Einmalkennung ist es nicht erforderlich, ein Microsoft-Konto zu erstellen. Wenn der Gastbenutzer eine Einladung einlöst oder auf eine freigegebene Ressource zugreift, erhält er einen temporären Code. Der Code wird an seine E-Mail-Adresse gesendet, und dann wird der Code eingegeben, um die Anmeldung fortzusetzen. 

Ohne aktivierte Authentifizierung per einmaligem Passcode per E-Mail kann ein Microsoft-Konto oder ein nicht verwalteter Just-in-Time-Azure AD-Mandant erstellt werden.

>**Wichtig**: Microsoft setzt die Erstellung von nicht verwalteten Mandanten und deren Benutzern als veraltet ein, da dieses Feature in jeder Cloudumgebung allgemein verfügbar wird. 

### <a name="manage-accounts"></a>Konten verwalten

Der Ressourcen-Mandantenadministrator verwaltet Gastbenutzerkonten im Ressourcenmandanten. Gastbenutzerkonten werden nicht basierend auf den aktualisierten Werten im Home-Mandanten aktualisiert. Die einzigen sichtbaren Attribute, die empfangen werden, sind die E-Mail-Adresse und der Anzeigename.

Sie können weitere Attribute für Gastbenutzerobjekte konfigurieren, um Szenarien zu unterstützen. Beispielsweise können Sie das Auffüllen des Adressbuchs mit Kontaktdetails oder in Berechtigungsszenarien einschließen. Ziehen Sie z. B. Folgendes in Betracht:

* HiddenFromAddressListsEnabled

* GivenName

* Surname

* Titel

* Department

* TelephoneNumber

Diese Attribute können festgelegt werden, um [Gäste zur globalen Adressliste hinzuzufügen](/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide&preserve-view=true). Andere Szenarien erfordern möglicherweise unterschiedliche Attribute, z. B. zum Festlegen von Berechtigungen sowie Berechtigungen für Zugriffspakete, dynamische Gruppenmitgliedschaft, SAML-Ansprüche usw.

Hinweis: Eingeladene Gastbenutzer werden standardmäßig in der globalen Adressliste (GAL) ausgeblendet. Legen Sie fest, dass Gastbenutzerattribute nicht mehr eingebunden werden, damit sie in die einheitliche GAL aufgenommen werden. Weitere Informationen finden Sie in der Dokumentation [Microsoft Exchange Online ](multi-tenant-common-considerations.md#microsoft-exchange-online).

### <a name="deprovision-accounts"></a>Bereitstellung von Konten aufheben

Von Endbenutzern initiierte Szenarien dezentralisieren Zugriffsentscheidungen. Dezentralisierte Zugriffsentscheidungen stellen jedoch die Herausforderung dar, zu entscheiden, wann ein Gastbenutzer und der zugehörige Zugriff entfernt werden sollen. [Berechtigungsverwaltung](../governance/entitlement-management-overview.md) und [Zugriffsüberprüfungen](../governance/manage-guest-access-with-access-reviews.md) bieten eine Möglichkeit, vorhandene Gastbenutzer und deren Zugriff auf Ressourcen zu überprüfen und zu entfernen. 

Hinweis: Wenn Benutzer außerhalb der Berechtigungsverwaltung eingeladen werden, müssen Sie einen separaten Prozess erstellen, um den Zugriff dieser Gastbenutzer zu überprüfen und zu verwalten. Wenn der Gastbenutzer beispielsweise direkt über SharePoint Online eingeladen wird, ist er nicht in Ihrem Prozess der Berechtigungsverwaltung enthalten.

## <a name="scripted-scenario"></a>Skriptszenario

Für das Skriptszenario stellen Ressourcen-Mandantenadministratoren einen Skript-Pullprozess bereit, um die Ermittlung und Bereitstellung von Gastbenutzern zu automatisieren. Dieser Ansatz ist für Kunden üblich, die einen Skriptmechanismus verwenden.

Ein Beispiel für einen Anwendungsfall wäre ein globales Versandunternehmen, das einen Mitbewerber erworben hat. Jedes Unternehmen verfügt über einen einzelnen Azure AD-Mandanten. Sie möchten, dass die folgenden Szenarien für den ersten Tag funktionieren, ohne dass Benutzer Einladungs- oder Einlösungsschritte ausführen müssen. Alle Benutzer müssen zu Folgendem in der Lage sein: 

* Verwenden des einmaligen Anmeldens für alle Ressourcen, für die sie bereitgestellt werden

* Sich gegenseitig suchen, und auch andere Ressourcen in einer einheitlichen GAL suchen

* Ermitteln der Präsenz des jeweils anderen und Initiieren von Sofortnachrichten

* Zugreifen auf eine Anwendung basierend auf der dynamischen Gruppenmitgliedschaft

In diesem Fall ist der Mandant jeder Organisation der Home-Mandant für seine vorhandenen Mitarbeiter und der Ressourcenmandant für die Mitarbeiter der anderen Organisation.

### <a name="provision-accounts"></a>Bereitstellen von Konten

Mit [Delta Query](/graph/delta-query-overview) können Mandantenadministratoren einen Skript-Pullprozess bereitstellen, um die Ermittlung und Bereitstellung von Identitäten zur Unterstützung des Ressourcenzugriffs zu automatisieren. Dieser Prozess überprüft den Home-Mandanten auf neue Benutzer und verwendet die B2B-Graph-APIs, um diese Benutzer als eingeladene Benutzer im Ressourcenmandanten bereitstellen zu können. Im folgenden Diagramm werden die Komponenten veranschaulicht. 
### <a name="multi-tenant-topology"></a>Mehrinstanzenfähige Topologie

![Mehrinstanzenfähige Szenarien](media\multi-tenant-user-management-scenarios\multi-tenant-scripted-scenario.png)

* Administratoren der einzelnen Mandanten ordnen Anmeldeinformationen vorab an und stimmen dem Lesen der einzelnen Mandanten zu.

* Ermöglicht Mandantenadministratoren das Automatisieren der Enumeration und das Ausführen eines Pulls bereichsabhängiger Benutzer an den Ressourcenmandanten.

* Verwenden Sie die MS Graph-API mit eingewilligten Berechtigungen zum Lesen und Bereitstellen von Benutzern über die Einladungs-API.

* Bei der ersten Bereitstellung können Quellattribute gelesen und auf das Zielbenutzerobjekt angewendet werden.

### <a name="manage-accounts"></a>Konten verwalten

Die Ressourcenorganisation kann die Profildaten erweitern, um Freigabeszenarien zu unterstützen, indem die Metadatenattribute des Benutzers im Ressourcenmandanten aktualisiert werden. Wenn jedoch eine fortlaufende Synchronisierung erforderlich ist, ist eine synchronisierte Lösung möglicherweise eine bessere Option.

### <a name="deprovision-accounts"></a>Bereitstellung von Konten aufheben

[Delta Query](/graph/delta-query-overview) kann signalisieren, wann die Bereitstellung eines Gastbenutzers aufgehoben werden muss. [Berechtigungsverwaltung](../governance/entitlement-management-overview.md) und [Zugriffsüberprüfungen](../governance/manage-guest-access-with-access-reviews.md) können auch eine Möglichkeit bieten, vorhandene Gastbenutzer und deren Zugriff auf Ressourcen zu überprüfen und zu entfernen. 

Hinweis: Wenn Benutzer außerhalb der Berechtigungsverwaltung eingeladen werden, müssen Sie einen separaten Prozess erstellen, um den Zugriff dieser Gastbenutzer zu überprüfen und zu verwalten. Wenn der Gastbenutzer beispielsweise direkt über SharePoint Online eingeladen wird, ist er nicht in Ihrem Prozess der Berechtigungsverwaltung enthalten.

## <a name="automated-scenario"></a>Automatisiertes Szenario

Das bei Weitem komplexeste Muster ist die mandantenübergreifende Synchronisierung der Freigabe. Dieses Muster ermöglicht mehr automatisierte Szenarien für die Verwaltung und Aufhebung der Bereitstellung als vom Benutzer initiierte oder skriptbasierte Szenarien. Für automatisierte Szenarien verwenden Ressourcen-Mandantenadministratoren ein Identitätsbereitstellungssystem, um die Prozesse für die Bereitstellung und Aufhebung der Bereitstellung zu automatisieren.

Ein Beispiel für einen Anwendungsfall wäre ein multinationales Unternehmen mit mehreren Niederlassungen. Jede verfügt über einen eigenen Azure AD-Mandanten, muss aber zusammenarbeiten. Zusätzlich zum Synchronisieren neuer Benutzer zwischen Mandanten müssen Attribut-Updates automatisch synchronisiert werden. Die Aufhebung der Bereitstellung muss automatisiert werden. Wenn sich ein Mitarbeiter beispielsweise nicht mehr in einer Niederlassung befindet, sollte sein Konto während der nächsten Synchronisierung aus allen anderen Mandanten entfernt werden.

Oder betrachten Sie das folgende erweiterte Szenario. Ein Mitarbeiter der Verteidigungsindustrie (DIB-Mitarbeiter) verfügt über eine militärbasierte und kommerzielle Niederlassung. Diese haben konkurrierende Regulierungsanforderungen:

* Das US-Verteidigungsgeschäft befindet sich in einem US Sovereign Cloud-Mandanten. Beispiel: Microsoft 365 US Government GCC High.

* Das kommerzielle Geschäft befindet sich in einem separaten Azure AD-Mandanten in der Öffentlichkeit. Beispielsweise eine Azure AD-Umgebung, die in der globalen Azure-Cloud ausgeführt wird. 

 Um wie ein einzelnes Unternehmen zu agieren, das in einer „Cross-Sovereign Cloud“-Architektur bereitgestellt wird, werden alle Benutzer mit beiden Mandanten synchronisiert. Dies ermöglicht eine einheitliche GAL, die für beide Mandanten verfügbar ist. Außerdem kann sichergestellt werden, dass Benutzer, die automatisch mit beiden Mandanten synchronisiert werden, Berechtigungen und Einschränkungen für Anwendungen und Inhalte enthalten. Beispiel:

* Mitarbeiter in den USA haben möglicherweise einen uneingeschränkten Zugriff auf beide Mandanten.

* Mitarbeiter außerhalb der USA werden in der vereinheitlichten GAL beider Mandanten angezeigt, haben aber keinen Zugriff auf geschützte Inhalte im GCC High-Mandanten. 

Dies erfordert die automatische Synchronisierung und Identitätsverwaltung, um Benutzer in beiden Mandanten zu konfigurieren, während sie den richtigen Berechtigungs- und Datenschutzrichtlinien zugeordnet werden.

### <a name="provision-accounts"></a>Bereitstellen von Konten

Diese erweiterte Bereitstellung verwendet [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) (MIM) als Synchronisierungsmodul. MIM ruft die [MS Graph-API](/developer.microsoft.com/graph/) und [Exchange Online PowerShell](/powershell/exchange/exchange-online/exchange-online-powershell?view=exchange-ps&preserve-view=true) auf. Alternative Implementierungen können das in der Cloud gehostete verwaltete Dienstangebot für [Active Directory-Synchronisierungsdienste](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (ADSS) [von Microsoft Consulting Services ](/industry/services/consulting) umfassen. Es gibt auch Nicht-Microsoft-Angebote, die mit anderen Identitätsverwaltungsangeboten von Grund auf neu erstellt werden können.

Dies sind komplexe Szenarien, und es wird empfohlen, mit Ihren Partnern, Microsoft-Konto-Team und allen anderen verfügbaren Ressourcen während der Planung und Ausführung zusammenzuarbeiten. 

Hinweis: Es gibt Überlegungen, die außerhalb des Rahmens dieses Dokuments liegen. Beispielsweise die [Integration von lokalen Anwendungen](../app-proxy/what-is-application-proxy.md). 

### <a name="choose-the-right-topology"></a>Auswählen der richtigen Topologie

Die meisten Kunden verwenden eine von zwei Topologien in automatisierten Szenarien. 

* Eine Maschentopologie ermöglicht die gemeinsame Nutzung aller Ressourcen in allen Mandanten. Benutzer aus anderen Mandanten werden in jedem Ressourcenmandanten als Gastbenutzer erstellt.

* Eine Topologie mit einem einzelnen Ressourcenmandanten verwendet einen einzelnen Mandanten (den Ressourcenmandanten), in dem Benutzer aus anderen Unternehmen externe Gastbenutzer sind. 

Die folgende Tabelle kann als Entscheidungsstruktur verwendet werden, während Sie Ihre Lösung entwerfen. Wir veranschaulichen beide Topologien, die der Tabelle folgen. Anhand der Informationen in der folgenden Tabelle können Sie entscheiden, welche Lösung für Ihre Organisation geeignet ist. 

Vergleich von Maschen- und Einzel-Ressourcenmandanten-Topologien

| Aspekt| Maschentopologie| Einzelner Ressourcenmandant |
| - | - |-|
| Jedes Unternehmen verfügt über einen separaten Azure AD-Mandanten mit Benutzern und Ressourcen| Ja| Ja |
| **Ressourcenstandort und Zusammenarbeit**| |  |
| Freigegebene Apps und andere Ressourcen verbleiben im aktuellen Home-Mandanten.| Ja| Nein: Nur Ressourcen im Ressourcenmandanten werden freigegeben. |
| Alle anzeigebar in den GALs einzelner Unternehmen (Unified GAL)| Ja| Nein |
| **Ressourcenzugriff und -verwaltung**| |  |
| Alle Anwendungen, die mit Azure AD verbunden sind, können von allen Unternehmen gemeinsam genutzt werden.| Ja| Nein: Nur Ressourcen im Ressourcenmandanten werden freigegeben. Die verbleibenden in anderen Mandanten sind es nicht. |
| Globale Ressourcenverwaltung ‎| Fortsetzen auf Mandantenebene| Konsolidiert im Ressourcenmandanten |
| Lizenzierung – Office 365 <br>SharePoint Online, einheitliche GAL, Team-Zugriff auf alle unterstützten Gäste; andere Exchange Online-Szenarien jedoch nicht| Wird auf Mandantenebene fortgesetzt| Wird auf Mandantenebene fortgesetzt |
| Lizenzierung – [Azure AD Premium](../external-identities/external-identities-pricing.md)-Lizenz| Die ersten 50.000 aktiven Benutzer pro Monat sind kostenlos (pro Mandant).| Die ersten 50.000 aktiven Benutzer pro Monat sind kostenlos. |
| Lizenzierung – SaaS-Apps| Verbleiben in einzelnen Mandanten, erfordert möglicherweise Lizenzen pro Benutzer und Mandant| Alle freigegebenen Ressourcen befinden sich im einzelnen Ressourcenmandanten. Sie können ggf. die Konsolidierung von Lizenzen für den einzelnen Mandanten untersuchen. |

#### <a name="mesh-topology"></a>Maschentopologie

![Maschentopologie](media/multi-tenant-user-management-scenarios/mesh.png)

In einer Maschentopologie wird jeder Benutzer in jedem Home-Mandanten mit jedem anderen Mandanten synchronisiert, der zu Ressourcenmandanten wird. 

* Dadurch kann jede Ressource innerhalb eines Mandanten für Gastbenutzer freigegeben werden. 

* Dadurch kann jede Organisation alle Benutzer im Konglomarat anzeigen. In der obigen Abbildung gibt es vier einheitliche GALs, von denen jede die Home-Benutzer und die Gastbenutzer der anderen drei Mandanten enthält.

Weitere Informationen zur Bereitstellung, Verwaltung und Aufhebung der Bereitstellung von Benutzern in diesem Szenario finden Sie im Abschnitt [allgemeine Überlegungen](multi-tenant-common-considerations.md#directory-object-considerations) dieses Dokuments.

#### <a name="mesh-topology-for-cross-sovereign-cloud"></a>Maschentopologie für Cross-Sovereign Clouds

Die Maschentopologie kann in nur zwei Mandanten verwendet werden, z. B. im Szenario für den DIB-Verteidigungsauftragsnehmer, der sich an einer unabhängigen Cloudlösung beteiligt. Wie bei der Maschentopologie wird jeder Benutzer in jedem Home-Mandanten mit dem anderen Mandanten synchronisiert, der effektiv zu einem Ressourcenmandanten wird. In der obigen Abbildung wird der Benutzer des öffentlichen kommerziellen Mandantenmembers als Gastbenutzerkonto mit dem GCC High-Mandanten synchronisiert. Gleichzeitig wird der GCC High-Memberbenutzer mit dem kommerziellen als Gastbenutzerkonto synchronisiert.

>**Hinweis:** In der Abbildung wird auch beschrieben, wo die Daten gespeichert werden. Die Kategorisierung und Konformität von Daten liegt außerhalb des Rahmens dieses Whitepapers, zeigt jedoch, dass Sie Berechtigungen und Einschränkungen für Anwendungen und Inhalte einschließen können. Zu den Inhalten kann gehören, wo sich die „personenbezogenen Daten“ eines Memberbenutzers befinden. Beispielsweise Daten, die im Exchange Online-Postfach oder in OneDrive for Business gespeichert sind. Der Inhalt kann sich nur im Home-Mandanten und nicht im Ressourcenmandanten enthalten. Freigegebene Daten können sich in beiden Mandanten befinden. Sie können den Zugriff auf den Inhalt durch Zugriffssteuerungsrichtlinien und Richtlinien für bedingten Zugriff einschränken.

#### <a name="single-resource-tenant-topology"></a>Topologie eines einzelnen Ressourcenmandanten

![Einzelner Ressourcenmandant](media/multi-tenant-user-management-scenarios/single-resource-tenant.png)

In einer Topologie eines einzelnen Ressourcenmandanten werden Benutzer und ihre Attribute mit dem Ressourcenmandanten synchronisiert (Unternehmen A in der obigen Abbildung). 

* Alle Ressourcen, die von den Mitgliedsorganisationen gemeinsam genutzt werden, müssen sich im einzelnen Ressourcenmandanten befinden.
  * Wenn mehrere Niederlassungen über Abonnements für dieselben SaaS-Apps verfügen, kann dies eine Gelegenheit sein, diese Abonnements zu konsolidieren.

* Nur die GAL im Ressourcenmandanten zeigt Benutzer aus allen Unternehmen an.

### <a name="manage-accounts"></a>Konten verwalten

Diese Lösung erkennt Attributänderungen von Ressourcenmandantenbenutzern und synchronisiert sie mit Gastbenutzern des Ressourcenmandanten. Diese Attribute können verwendet werden, um Autorisierungsentscheidungen zu treffen. Beispielsweise bei Verwendung dynamischer Gruppen.

### <a name="deprovision-accounts"></a>Bereitstellung von Konten aufheben

Die Automatisierung erkennt das Löschen des Objekts in der Quellumgebung und löscht das zugeordnete Gastbenutzerobjekt in der Zielumgebung.

Weitere Informationen zur Bereitstellung, Verwaltung und Aufhebung der Bereitstellung von Benutzern in diesem Szenario finden Sie im Abschnitt [allgemeine Überlegungen](multi-tenant-common-considerations.md) dieses Dokuments.

## <a name="next-steps"></a>Nächste Schritte
[Einführung in die mehrstufige Benutzerverwaltung](multi-tenant-user-management-introduction.md)

[Allgemeine Überlegungen zu mehreren Mandanten](multi-tenant-common-considerations.md)

[Allgemeine Lösungen für mehrere Mandanten](multi-tenant-common-solutions.md)

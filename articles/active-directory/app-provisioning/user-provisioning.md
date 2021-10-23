---
title: Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure Active Directory?
description: Einführung in die Verwendung von Azure Active Directory für die automatisierte Bereitstellung, Bereitstellungsaufhebung und fortlaufende Aktualisierung von Benutzerkonten für verschiedene SaaS-Drittanbieteranwendungen.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 05/28/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 78533b519c2410744b3ec8adc7dd3269e018715f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990556"
---
# <a name="what-is-app-provisioning-in-azure-active-directory"></a>Worum handelt es sich bei der App-Bereitstellung in Azure Active Directory?

In Azure Active Directory (Azure AD) bezieht sich der Ausdruck *App-Bereitstellung* auf die automatische Erstellung von Benutzeridentitäten und Rollen für Anwendungen.
    
![Diagramm mit Bereitstellungsszenarien](../governance/media/what-is-provisioning/provisioning.png)

Azure AD zu SaaS-Anwendungsbereitstellung (Software-as-a-Service) bezieht sich auf die automatische Erstellung von Benutzeridentitäten und Rollen in den Cloudanwendungen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), auf die Benutzer zugreifen müssen. Zusätzlich zur Erstellung von Benutzeridentitäten umfasst die automatische Bereitstellung auch die Wartung und Entfernung von Benutzeridentitäten, wenn sich der Status oder die Rollen ändern. Gängige Szenarien sind die Bereitstellung eines Azure AD-Benutzers in Anwendungen wie [Dropbox](../../active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../../active-directory/saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../../active-directory/saas-apps/servicenow-provisioning-tutorial.md) und anderen.

Azure AD unterstützt die Bereitstellung von Benutzern in SaaS-Anwendungen sowie lokal gehosteten Anwendungen oder einer IaaS-Lösung (Infrastructure-as-a-Service) wie einer VM. Möglicherweise verfügen Sie über eine Legacyanwendung, die auf einem LDAP-Benutzerspeicher oder einer SQL-Datenbank basiert. Mit dem Azure AD-Bereitstellungsdienst können Sie Benutzer in lokalen Anwendungen erstellen, aktualisieren und löschen, ohne Firewalls öffnen oder TCP-Ports verwalten zu müssen. 

Mithilfe schlanker Agents können Sie Benutzer in lokalen Anwendungen bereitstellen und den Zugriff steuern. In Verbindung mit dem Anwendungsproxy können Sie mit Azure AD den Zugriff auf Ihre lokale Anwendung verwalten und eine automatische Benutzerbereitstellung (mit dem Bereitstellungsdienst) sowie einmaliges Anmelden (mit App-Proxy) zur Verfügung stellen. 

Die App-Bereitstellung ermöglicht Folgendes:

- **Automatisieren der Bereitstellung**: Automatisches Erstellen neuer Konten in den richtigen Systemen für neue Benutzer, wenn diese Ihr Team oder Ihre Organisation verstärken
- **Automatisieren der Aufhebung der Bereitstellung:** automatisches Deaktivieren von Konten in den richtigen Systemen, wenn Benutzer das Team oder die Organisation verlassen
- **Synchronisieren von Daten zwischen Systemen:** Sicherstellen, dass die Identitäten in Ihren Apps und Systemen bei Änderungen im Verzeichnis oder in Ihrem Personalsystem auf den neuesten Stand gebracht werden
- **Bereitstellen von Gruppen**: Bereitstellen von Gruppen für Anwendungen, die diese unterstützen
- **Steuern des Zugriffs:** Überwachen und Überprüfen, wer Bereitstellungen für Ihre Anwendungen durchgeführt hat
- **Nahtloses Bereitstellen bei Brownfield-Szenarien**: Abgleichen vorhandener Identitäten zwischen Systemen und Ermöglichen einer einfachen Integration, auch wenn Benutzer im Zielsystem bereits vorhanden sind
- **Nutzen der umfassenden Anpassung**: Nutzen anpassbarer Attributzuordnungen zur Definition der Benutzerdaten, die vom Quellsystem an das Zielsystem fließen sollen
- **Erhalten von Warnungen zu kritischen Ereignissen**: Senden von Warnungen zu kritischen Ereignissen durch den Bereitstellungsdienst und Ermöglichen der Log Analytics-Integration, damit Sie benutzerdefinierte Warnungen zur Erfüllung Ihrer geschäftlichen Anforderungen definieren können

## <a name="what-is-scim"></a>Was ist SCIM?

Als Hilfe bei der Automatisierung der Bereitstellung und Bereitstellungsaufhebung werden von Apps proprietäre Benutzer- und Gruppen-APIs verfügbar gemacht. Wenn Sie schon einmal Benutzer in mehreren Apps verwaltet haben, wissen Sie aber, dass in jeder App versucht wird, die gleichen Aktionen durchzuführen (z. B. Erstellen oder Aktualisieren von Benutzern, Hinzufügen von Benutzern zu Gruppen oder Aufheben der Bereitstellung von Benutzern). Die Implementierung all dieser Aktionen unterscheidet sich aber jeweils nur leicht, indem andere Endpunktpfade, unterschiedliche Methoden zum Angeben von Benutzerinformationen und ein anderes Schema zum Darstellen der einzelnen Informationselemente genutzt werden.

Zur Bewältigung dieser Aufgaben verfügt die SCIM-Spezifikation (System for Cross-Domain Identity Management, System für die domänenübergreifende Identitätsverwaltung) über ein gemeinsames Benutzerschema, damit Benutzer Apps zugeordnet werden können und diese verwenden können. SCIM wird immer mehr zum De-facto-Standard für die Bereitstellung. Bei Verwendung zusammen mit Verbundstandards wie SAML (Security Assertion Markup Language) oder OIDC (OpenID Connect) verfügen Administratoren über eine umfassende, auf Standards basierende Lösung für die Zugriffsverwaltung.

Eine ausführliche Anleitung zum Entwickeln eines SCIM-Endpunkts zum Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern und Gruppen für eine Anwendung finden Sie unter [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](use-scim-to-provision-users-and-groups.md). Für vorab integrierte Anwendungen im Katalog, z. B. Slack, Azure Databricks und Snowflake, können Sie die Entwicklerdokumentation überspringen und die Tutorials in [Tutorials zur Integration von SaaS-Anwendungen mit Azure Active Directory](../../active-directory/saas-apps/tutorial-list.md) verwenden.

## <a name="manual-vs-automatic-provisioning"></a>Manuelle und automatische Bereitstellung

Die im Azure AD-Katalog enthaltenen Anwendungen unterstützen einen der zwei folgenden Bereitstellungsmodi:

* **Manuelle** Bereitstellung bedeutet, dass noch kein automatischer Azure AD-Bereitstellungsconnector für die App vorhanden ist. Benutzerkonten müssen manuell erstellt werden. Dazu können beispielsweise Benutzer direkt dem Verwaltungsportal der App hinzugefügt werden, oder es wird eine Tabelle mit Benutzerkontodetails hochgeladen. Die verfügbaren Mechanismen können Sie über die von der App bereitgestellte Dokumentation ermitteln, oder wenden Sie sich an den Entwickler der App.
* **Automatisch** bedeutet, dass für diese Anwendung ein Azure AD-Bereitstellungsconnector entwickelt wurde. Befolgen Sie zum Einrichten der Bereitstellung das für die jeweilige Anwendung spezifische Setuptutorial. App-Tutorials finden Sie unter [Tutorials zur Integration von SaaS-Anwendungen mit Azure Active Directory](../../active-directory/saas-apps/tutorial-list.md).

Der von einer Anwendung unterstützte Bereitstellungsmodus wird auch auf der Registerkarte **Bereitstellung** angezeigt, nachdem Sie Ihren Unternehmens-Apps die Anwendung hinzugefügt haben.

## <a name="benefits-of-automatic-provisioning"></a>Vorteile der automatischen Bereitstellung

Da die Anzahl der in modernen Organisationen genutzten Anwendungen weiter zunimmt, müssen IT-Administratoren eine bedarfsabhängige Zugriffsverwaltung durchführen können. Mit Standards wie SAML oder OIDC können Administratoren schnell einmaliges Anmelden (Single Sign-On, SSO) einrichten. Für den Zugriff müssen jedoch in der App auch Benutzer bereitgestellt werden. Für viele Administratoren ist die Bereitstellung mit der wöchentlichen manuellen Erstellung jedes einzelnen Benutzerkontos bzw. dem Hochladen von CSV-Dateien verbunden. Diese Prozesse sind aber zeitaufwendig, teuer und fehleranfällig. Lösungen wie SAML JIT (Just in Time) wurden eingeführt, um die Bereitstellung zu automatisieren. Unternehmen benötigen aber auch eine Lösung zum Aufheben der Bereitstellung von Benutzern, wenn diese die Organisation verlassen oder aufgrund einer Rollenänderung keinen Zugriff auf bestimmte Apps mehr benötigen.

Hier sind einige häufige Gründe für die Nutzung der automatischen Bereitstellung aufgeführt:

- Steigern der Effizienz und Genauigkeit von Bereitstellungsprozessen.
- Einsparen von Kosten im Zusammenhang mit dem Hosten und Verwalten von benutzerdefiniert entwickelten Bereitstellungslösungen und Skripts.
- Schützen Ihrer Organisation, indem Benutzeridentitäten sofort aus wichtigen SaaS-Apps entfernt werden, wenn Benutzer die Organisation verlassen.
- Einfaches Importieren einer großen Anzahl von Benutzern in eine bestimmte SaaS-Anwendung oder in ein bestimmtes System.
- Vorteil eines einzelnen Richtliniensatzes für die Ermittlung, wer bereitgestellt wird und wer sich bei einer App anmelden kann.

Die Azure AD-Benutzerbereitstellung kann zur Bewältigung dieser Aufgaben beitragen. Weitere Informationen zur Verwendung der Azure AD-Benutzerbereitstellung durch Kunden finden Sie in der [Fallstudie von ASOS](https://aka.ms/asoscasestudy). Das folgende Video enthält eine Übersicht über die Benutzerbereitstellung in Azure AD.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Welche Anwendungen und Systeme kann ich mit der automatischen Benutzerbereitstellung von Azure AD verwenden?

Azure AD umfasst eine vorab integrierte Unterstützung für viele beliebte SaaS-Apps und Personalsysteme und bietet allgemeine Unterstützung für Apps, die bestimmte Teile des [SCIM 2.0-Standards](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010) implementieren.

* **Vorintegrierte Anwendungen (SaaS-Katalog-Apps):** Sie finden alle Anwendungen, für die Azure AD einen vorab integrierten Bereitstellungsconnector unterstützt, unter [Tutorials zur Integration von SaaS-Anwendungen mit Azure Active Directory](../saas-apps/tutorial-list.md). Für die vorab integrierten Anwendungen, die im Katalog aufgeführt sind, werden normalerweise SCIM 2.0-basierte Benutzerverwaltungs-APIs für die Bereitstellung verwendet. 

   ![Abbildung der Logos von Dropbox, Salesforce und anderen](./media/user-provisioning/gallery-app-logos.png)

   Wenn Sie eine neue Anwendung für die Bereitstellung anfordern möchten, können Sie [anfordern, dass Ihre Anwendung in unseren App-Katalog integriert wird](../develop/v2-howto-app-gallery-listing.md). Für die Anforderung der Benutzerbereitstellung muss die Anwendung über einen SCIM-konformen Endpunkt verfügen. Bitten Sie den Anwendungsanbieter darum, den SCIM-Standard einzuhalten, damit wir das Onboarding der App für unsere Plattform schnell durchführen können.

* **Anwendungen, die SCIM 2.0 unterstützen:** Informationen zum generischen Verbinden von Anwendungen, die SCIM 2.0-basierte Benutzerverwaltungs-APIs implementieren, finden Sie unter [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Wie richte ich die automatische Bereitstellung für eine Anwendung ein?

Für vorab integrierte Anwendungen, die im Katalog aufgeführt sind, ist eine Schritt-für-Schritt-Anleitung zum Einrichten der automatischen Bereitstellung verfügbar. Weitere Informationen finden Sie unter [Tutorials zur Integration von SaaS-Anwendungen mit Azure Active Directory](../saas-apps/tutorial-list.md). Im folgenden Video wird veranschaulicht, wie Sie die automatische Benutzerbereitstellung für Salesforce einrichten.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Führen Sie für andere Anwendungen, die SCIM 2.0 unterstützen, die unter [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](use-scim-to-provision-users-and-groups.md) aufgeführten Schritte aus.


## <a name="next-steps"></a>Nächste Schritte

- [Liste der Tutorials zur Integration von SaaS-Apps](../saas-apps/tutorial-list.md)
- [Anpassen von Attributzuordnungen für die Benutzerbereitstellung](customize-application-attributes.md)
- [Bereichsfilter für die Benutzerbereitstellung](define-conditional-rules-for-provisioning-user-accounts.md)

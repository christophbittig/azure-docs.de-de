---
title: Sicherheit, Governance und Azure Dev/Test-Abonnements
description: Hier erfahren Sie, wie Sie die Sicherheit und Governance innerhalb der Dev/Test-Abonnements Ihrer Organisation verwalten.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 6f3d9bae37e49c23dacc9a44ed4e0006df95ec29
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305881"
---
# <a name="security-within-azure-devtest-subscription"></a>Sicherheit innerhalb eines Azure Dev/Test-Abonnements

Der Schutz Ihrer Ressourcen ist eine gemeinsame Aufgabe, die von Ihrem Cloudanbieter, von Azure und von Ihnen selbst wahrgenommen wird. Mit Azure Dev/Test-Abonnements und [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md) erhalten Sie die Tools, die Sie zum Sichern Ihres Netzwerks, zum Schützen Ihrer Dienste und zum Gewährleisten eines optimalen Sicherheitsstatus benötigen.  

Wichtige Tools in Azure Dev/Test-Abonnements helfen Ihnen dabei, den Zugriff auf Ihre Ressourcen sicher zu machen:  

- Azure-Verwaltungsgruppen  
- Azure Lighthouse  
- Überwachung von Gutschriften  
- Azure Active Directory  

## <a name="azure-management-groups"></a>Azure-Verwaltungsgruppen  

Wenn Sie Ihre Azure Dev/Test-Abonnements aktivieren und einrichten, stellt Azure eine Standardressourcenhierarchie bereit, um Identitäten und den Zugriff auf Ressourcen in einer einzelnen Azure Active Directory-Domäne zu verwalten. Mit der Ressourcenhierarchie kann Ihre Organisation starke Sicherheitsperimeter für Ihre Ressourcen und Benutzer einrichten.  

![Screenshot: Azure-Verwaltungsgruppen](media/concepts-security-governance-devtest/access-management-groups.png "Standardressourcenhierarchie von Azure")  

Ihre Ressourcen, Ressourcengruppen, Abonnements, Verwaltungsgruppen und Mandanten bilden zusammen Ihre Ressourcenhierarchie. Aktualisierungen und Änderungen dieser Einstellungen in benutzerdefinierten Azure-Rollen oder Azure-Richtlinienzuweisungen können sich auf jede Ressource in Ihrer Ressourcenhierarchie auswirken. Es ist wichtig, die Ressourcenhierarchie vor Änderungen zu schützen, die sich negativ auf alle Ressourcen auswirken könnten.  

[Azure Verwaltungsgruppen](../../governance/management-groups/overview.md) sind ein wichtiger Aspekt der Zugriffssteuerung und des Ressourcenschutzes in einem einzelnen Mandanten. Mit Azure-Verwaltungsgruppen können Sie Kontingente, Azure-Richtlinien und Sicherheitsoptionen für verschiedene Arten von Abonnements festlegen. Diese Gruppen sind eine wichtige Komponente der Sicherheitsentwicklung für die Dev/Test-Abonnements Ihrer Organisation.  

![Screenshot: Organisations- und Governancegruppen von Azure](media/concepts-security-governance-devtest/orgs-and-governance.png "Einordnung von Azure-Verwaltungsgruppen in die allgemeine Governance")

Wie in der obigen Abbildung zu sehen, verändert die Verwendung von Verwaltungsgruppen die Standardhierarchie und fügt eine Ebene für die Verwaltungsgruppen hinzu. Dieses Verhalten kann zu unvorhergesehenen Umständen und Sicherheitslücken führen, wenn Sie sich nicht an den [geeigneten Prozess zum Schutz Ihrer Ressourcenhierarchie](../../governance/management-groups/how-to/protect-resource-hierarchy.md) halten.  

## <a name="why-are-azure-management-groups-useful"></a>Warum sind Azure-Verwaltungsgruppen hilfreich?  

Bei der Entwicklung von Sicherheitsrichtlinien für die Dev/Test-Abonnements Ihrer Organisation können pro Organisationseinheit oder Branchen mehrere Dev/Test-Abonnements verwendet werden. Diese Verwaltungsgruppierung wird in der folgenden Abbildung veranschaulicht:  

![Diagramm: Abonnementverwaltungsgruppierungen für mehrere Abonnements innerhalb einer Organisation](media/concepts-security-governance-devtest/access-management-groups.png "Diagramm: Verwaltungsgruppierungen für mehrere Abonnements innerhalb einer Organisation")  

Sie können auch ein einzelnes Dev/Test-Abonnement für alle Ihre verschiedenen Einheiten verwenden.  

Ihre Azure Verwaltungsgruppen und Dev/Test-Abonnements fungieren innerhalb Ihrer Organisationsstruktur als Sicherheitsbarriere.  

Diese Sicherheitsbarriere umfasst zwei Komponenten:  

- Identität und Zugriff: Der Zugriff muss unter Umständen auf bestimmte Ressourcen aufgeteilt werden.  
- Daten: Verschiedene Abonnements für Ressourcen, die auf personenbezogene Informationen zugreifen  

## <a name="using-azure-active-directory-tenants"></a>Verwenden von Azure Active Directory-Mandanten  

Ein [Mandant](../../active-directory/develop/quickstart-create-new-tenant.md) ist eine dedizierte Instanz von Azure AD, die ein Unternehmen oder ein App-Entwickler erhält, wenn das Unternehmen oder der App-Entwickler eine Beziehung mit Microsoft eingeht (etwa durch eine Registrierung für Azure, Microsoft Intune oder Microsoft 365).  

Jeder Azure AD-Mandant ist von anderen Azure AD-Mandanten getrennt. Alle Azure AD-Mandaten verfügen jeweils über eine eigene Darstellung von Geschäfts-, Schul- oder Uniidentitäten, Consumeridentitäten (wenn es sich um einen Azure AD B2C-Mandanten handelt) und App-Registrierungen. Eine Anwendungsregistrierung innerhalb Ihres Mandanten kann nur Authentifizierungen von Konten innerhalb Ihres Mandanten oder von allen Mandanten zulassen.  

Wenn Sie für die Identitätsinfrastruktur Ihrer Organisation eine Trennung benötigen, die über Verwaltungsgruppen in einem einzelnen Mandanten hinausgeht, können Sie auch weitere Mandanten mit einer eigenen Ressourcenhierarchie erstellen.  

Eine einfache Möglichkeit zur Trennung von Ressourcen und Benutzern ist das Erstellen eines neuen Azure AD-Mandanten.  

### <a name="create-a-new-azure-ad-tenant"></a>Erstellen eines neuen Azure AD-Mandanten  

Falls Sie über keinen Azure AD-Mandanten verfügen oder einen neuen Mandanten für die Entwicklung erstellen möchten, sehen Sie sich die [Schnellstartanleitung](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) an, oder verwenden Sie die  [Benutzeroberfläche für die Verzeichniserstellung](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Sie müssen die folgende Informationen zum Erstellen Ihres neuen Mandanten angeben:  

- **Name der Organisation**  
- **Anfangsdomäne** (Teil von „/*.onmicrosoft.com“). Sie können die Domäne später noch anpassen.  
- **Land oder Region**  

 Weitere Informationen zum Erstellen und Einrichten von Azure AD-Mandanten finden Sie [hier](../../active-directory/develop/quickstart-create-new-tenant.md).  

### <a name="using-azure-lighthouse-to-manage-multiple-tenants"></a>Verwalten mehrerer Mandanten mithilfe von Azure Lighthouse  

Azure Lighthouse ermöglicht die mandantenübergreifende Verwaltung sowie die Verwaltung mehrerer Mandanten mit stärkerer Automatisierung, besserer Skalierbarkeit und verbesserter Governance für alle Ressourcen und Mandanten. Dienstanbieter können verwaltete Dienste mithilfe umfassender und stabiler Verwaltungstools bereitstellen, die in die Azure-Plattform integriert sind. Kunden behalten die Kontrolle darüber, wer auf ihre Mandanten zugreift, auf welche Ressourcen zugegriffen wird und welche Aktionen durchgeführt werden können.  

Ein gängiges Szenario für Azure Lighthouse ist die Verwaltung von Ressourcen in den Azure Active Directory-Mandanten der Kunden. Die Funktionen von Azure Lighthouse können aber auch zum Vereinfachen der mandantenübergreifenden Verwaltung in einem Unternehmen, in dem mehrere Azure AD-Mandanten vorhanden sind, verwendet werden.  

Für die meisten Organisationen ist die Verwaltung mit nur einem Azure AD-Mandanten einfacher. Wenn sich alle Ressourcen unter einem Mandanten befinden, können die Verwaltungsaufgaben nach den jeweiligen Benutzern, Benutzergruppen oder Dienstprinzipalen des Mandanten zentralisiert werden.  

Falls eine Architektur mit mehreren Mandanten benötigt wird, trägt Azure Lighthouse zur Zentralisierung und Optimierung von Verwaltungsvorgängen bei. Durch Verwendung der delegierten Azure-Ressourcenverwaltung können Benutzer in einem verwalteten Mandanten mandantenübergreifende Verwaltungsfunktionen zentral und skalierbar ausführen.  

[Weitere Sicherheitsressourcen](../../security-center/security-center-introduction.md)

---
title: Planen der Bereitstellung von Azure Active Directory-Zugriffsüberprüfungen
description: Planhinweisliste für eine erfolgreiche Bereitstellung von Zugriffsüberprüfungen
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 04/16/2021
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5974f8d3f94d16a27c77c81837d7c430ee48b26
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817912"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Planen der Bereitstellung von Azure Active Directory-Zugriffsüberprüfungen

[Zugriffsüberprüfungen von Azure Active Directory (Azure AD)](access-reviews-overview.md) unterstützen Ihre Organisation dabei, einen besseren Schutz des Netzwerks zu erreichen, indem der [Lebenszyklus des Ressourcenzugriffs](identity-governance-overview.md) verwaltet wird. Zugriffsüberprüfungen bieten folgende Möglichkeiten:

* Planen regelmäßiger Überprüfungen oder Durchführen von Ad-hoc-Überprüfungen, um zu ermitteln, wer Zugriff auf bestimmte Ressourcen wie Anwendungen und Gruppen hat

* Nachverfolgen von Überprüfungen, um Erkenntnisse zu gewinnen, sowie aus Gründen der Compliance oder der Einhaltung von Richtlinien

* Delegieren von Überprüfungen an bestimmte Administratoren, Geschäftsinhaber oder Endbenutzer, die eine Notwendigkeit des fortwährenden Zugriffs selbst nachweisen können

* Verwenden der Erkenntnisse, um effizient zu ermitteln, ob Benutzer weiterhin Zugriff haben sollen

* Automatisieren der Überprüfungsergebnisse, z. B. Entfernen des Benutzerzugriffs auf Ressourcen

  ![Diagramm des Flows für Zugriffsüberprüfungen.](./media/deploy-access-review/1-planning-review.png)

Zugriffsüberprüfungen sind eine Funktion der [Azure AD Identity Governance](identity-governance-overview.md). Die weiteren Funktionen sind [Berechtigungsverwaltung](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) und [Nutzungsbedingungen](../conditional-access/terms-of-use.md). Sie alle helfen Organisationen dabei, die folgenden vier Fragen zu beantworten:

* Welche Benutzer sollen Zugriff auf welche Ressourcen haben?

* Wozu nutzen diese Benutzer den Zugriff?

* Gibt es eine effektive organisatorische Kontrolle zum Verwalten des Zugriffs?

* Können Prüfer feststellen, ob die Kontrollen funktionieren?

Die Planung der Bereitstellung von Zugriffsüberprüfungen ist von entscheidender Bedeutung, um sicherzustellen, dass Sie die gewünschte Governancestrategie für Benutzer in Ihrer Organisation erfolgreich umsetzen.

### <a name="key-benefits"></a>Hauptvorteile

Die Aktivierung von Zugriffsüberprüfungen bietet die folgenden Hauptvorteile:

* **Zusammenarbeit bei der Kontrolle**. Mithilfe von Zugriffsüberprüfungen können Organisationen den Zugriff auf alle Ressourcen verwalten, welche die Benutzer benötigen. Wenn ihre Benutzer Dateien freigeben und zusammenarbeiten, können Organisationen sicher sein, dass nur autorisierte Benutzer Zugriff auf die Informationen haben.

* **Risikomanagement:** Mit Zugriffsüberprüfungen können Organisationen den Zugriff auf Daten und Anwendungen überprüfen und so das Risiko von Datenlecks und Datenaustritt verringern. Dies schließt Funktionen ein, um regelmäßig den Zugriff externer Partner auf Unternehmensressourcen zu überprüfen. 

* **Erfüllung von Compliance und Governance**: Mit Zugriffsüberprüfungen können Sie den Lebenszyklus des Zugriffs auf Gruppen, Apps und Websites steuern und erneut bestätigen. Sie können nachverfolgte Überprüfungen von in Ihrer Organisation verwendeten compliance- oder risikokritischen Anwendungen kontrollieren. 

* **Kostensenkung:** Zugriffsüberprüfungen werden in der Cloud erstellt und funktionieren nativ mit Cloudressourcen wie Gruppen, Anwendungen und Zugriffspaketen. Die Verwendung von Zugriffsüberprüfungen ist kostengünstiger als das Entwickeln eigener Tools oder ein anderweitiges Upgrade Ihres lokalen Toolsets.

### <a name="training-resources"></a>Schulungsressourcen

Die folgenden Videos können nützlich sein, während Sie sich über Zugriffsüberprüfungen informieren:

* [Was sind Azure AD-Zugriffsüberprüfungen?](https://youtu.be/kDRjQQ22Wkk)

* [Erstellen von Azure AD-Zugriffsüberprüfungen](https://youtu.be/6KB3TZ8Wi40)

* [So erstellen Sie automatische Zugriffsüberprüfungen für alle Gastbenutzer mit Zugriff auf Microsoft 365-Gruppen in Azure AD](https://www.youtube.com/watch?v=3D2_YW2DwQ8)

* [Aktivieren von Azure AD-Zugriffsüberprüfungen](https://youtu.be/X1SL2uubx9M)

* [Überprüfen des Zugriffs mit „Mein Zugriff“](https://youtu.be/tIKdQhdHLXU)


### <a name="licenses"></a>Lizenzen

Sie benötigen eine gültige Azure AD Premium-Lizenz (P2) für jede Person, die kein globaler Administrator oder Benutzeradministrator ist und Zugriffsüberprüfungen erstellen oder ausführen soll. Weitere Informationen finden Sie unter [Lizenzanforderungen für Zugriffsüberprüfungen](access-reviews-overview.md).

Möglicherweise benötigen Sie auch andere Identity Governance-Features, wie [Entitlement Lifecycle Management](entitlement-management-overview.md) oder Privileged Identity Managements. In diesem Fall benötigen Sie möglicherweise auch zugehörige Lizenzen. Weitere Informationen finden Sie unter [Azure Active Directory – Preise](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).

## <a name="plan-the-access-reviews-deployment-project"></a>Planen des Bereitstellungsprojekts für Zugriffsüberprüfungen

Berücksichtigen Sie die Anforderungen Ihrer Organisation, um die Strategie für die Bereitstellung von Zugriffsüberprüfungen in Ihrer Umgebung festzulegen.

### <a name="engage-the-right-stakeholders"></a>Einbeziehen der richtigen Beteiligten

Fehler in Technologieprojekten sind in der Regel auf nicht erfüllte Erwartungen auf den Gebieten Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](../fundamentals/active-directory-deployment-plans.md) und dass die Projektrollen klar sind.

An Zugriffsüberprüfungen können Mitglieder der folgenden Teams aus Ihrer Organisation beteiligt sein:

* Die **IT-Administration** verwaltet Ihre IT-Infrastruktur, Cloudinvestitionen und SaaS-Apps (Software as a Service). Aufgaben dieses Teams:

   * Überprüfen des privilegierten Zugriffs auf Infrastruktur und Apps, einschließlich Microsoft 365 und Azure AD.

   * Planen und Ausführen von Zugriffsüberprüfungen für Gruppen, die zum Verwalten von Ausnahmelisten oder IT-Pilotprojekten verwendet werden, um Zugriffslisten auf dem aktuellen Stand zu halten.

   * Sicherstellen, dass der programmgesteuerte (skriptbasierte) Zugriff auf Ressourcen über Dienstprinzipale geregelt ist und überprüft wird.

* **Entwicklungsteams** erstellen und verwalten Anwendungen für Ihre Organisation. Aufgaben dieses Teams:

   * Steuern, wer auf Komponenten in SaaS-, PaaS- und IaaS-Ressourcen zugreifen und diese verwalten kann, welche die entwickelten Lösungen bilden.

   * Verwalten von Gruppen, die auf Anwendungen und Tools für die interne Anwendungsentwicklung zugreifen können.

   * Benötigen privilegierte Identitäten, die Zugriff auf Produktionssoftware oder Lösungen haben, die für Ihre Kunden gehostet werden.

* **Geschäftseinheiten** verwalten Projekte und eigenen Anwendungen. Aufgaben dieses Teams: 

   * Überprüfen und Genehmigen bzw. Verweigern des Zugriffs von internen und externen Benutzern auf Gruppen und Anwendungen.

   * Planen und Durchführen von Überprüfungen, um den Fortbestand des Zugriffs von Mitarbeitern und externen Identitäten wie Geschäftspartnern zu bestätigen.

* Die **Unternehmensgovernance** stellt sicher, dass die Organisation die internen Richtlinien und Vorschriften einhält. Aufgaben dieses Teams:

   * Anfordern oder Planen neuer Zugriffsüberprüfungen.

   * Bewerten der Prozesse und Verfahren zum Überprüfen des Zugriffs, einschließlich Dokumentation und Aufzeichnung aus Gründen der Compliance.

   * Überprüfen der Ergebnisse früherer Überprüfungen für die wichtigsten Ressourcen.

> [!NOTE]
> Bei Überprüfungen, die manuell ausgewertet werden müssen, planen Sie geeignete Prüfer und Überprüfungszyklen ein, die Ihren Richtlinien und den Anforderungen an die Compliance entsprechen. Wenn Überprüfungszyklen zu häufig oder zu wenig Prüfer verfügbar sind, kann dies zu Qualitätseinbußen führen, und es haben möglicherweise zu viele oder zu wenige Personen Zugriff. 

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein entscheidender Faktor für den Erfolg jedes Geschäftsprozesses. Vermitteln Sie Benutzern proaktiv, wie sich ihre Erfahrung ändern wird, wann sie sich ändert und wie sie Unterstützung erhalten, wenn Probleme auftreten. 

#### <a name="communicate-changes-in-accountability"></a>Kommunizieren von Änderungen bei der Zuständigkeit

Zugriffsüberprüfungen unterstützen die Übertragung der Zuständigkeit für die Überprüfung und Maßnahmen bezüglich des fortwährenden Zugriffs auf Geschäftsinhaber. Die Entbindung der IT von Zugriffsentscheidungen führt zu präziseren Zugriffsentscheidungen. Dies stellt eine kulturelle Veränderung bei der Verantwortlichkeit und Zuständigkeit des Ressourcenbesitzers dar. Kommunizieren Sie eine solche Änderung proaktiv, und stellen Sie sicher, dass die Ressourcenbesitzer geschult werden und die Erkenntnisse umsetzen können, um gute Entscheidungen zu treffen.

Die IT ist offensichtlich bestrebt, die Kontrolle über alle Zugriffsentscheidungen für die Infrastruktur und die Zuweisung privilegierter Rollen zu behalten. 

#### <a name="customize-email-communication"></a>Anpassen der E-Mail-Kommunikation

Wenn Sie eine Überprüfung planen, schlagen Sie Benutzer vor, die diese Überprüfung durchführen werden. Diese Prüfer erhalten dann eine E-Mail-Benachrichtigung zu neuen Überprüfungen, die ihnen zugewiesen sind, sowie Erinnerungen, bevor eine ihnen zugewiesene Überprüfung abläuft. 

Die an Prüfer gesendete E-Mail kann so angepasst werden, dass sie eine benutzerdefinierte kurze Nachricht enthalten, mit der sie aufgefordert werden, die Überprüfung zu bearbeiten. Es wird empfohlen, Folgendes als zusätzlichen Text anzugeben:

* Eine persönliche Mitteilung an Prüfer, damit sie erkennen, dass die Nachricht von der Compliance- oder IT-Abteilung stammt.

* Schliessen Sie einen Verweis auf interne Informationen darüber ein, was die Erwartungen an die Überprüfung sind sowie zusätzliche Referenz- oder Schulungsmaterialien.

  ![E-Mail an Prüfer](./media/deploy-access-review/2-plan-reviewer-email.png)

Wenn Sie die Option „Review starten“ auswählen, werden Prüfer für Zugriffsüberprüfungen für Gruppen und Anwendungen an das [MyAccess-Portal](https://myapplications.microsoft.com/) weitergeleitet. Im Portal finden sie eine Übersicht über alle Benutzer, die Zugriff auf die Ressource haben, die Sie überprüfen, sowie Systemempfehlungen, die auf Informationen zur letzten Anmeldung und zum letzten Zugriff beruhen.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Kunden wird empfohlen, Zugriffsüberprüfungen zunächst in einem Pilotversuch mit einer kleinen Gruppe und für nicht kritische Ressourcen durchzuführen. Ein Pilotversuch ermöglicht es, Prozesse und Kommunikation nach Bedarf anzupassen, und erhöht die Chancen, dass Benutzer und Prüfer die Sicherheits- und Complianceanforderungen erfüllen.

Empfehlungen für den Pilotversuch:

* Beginnen Sie mit Überprüfungen, bei denen die Ergebnisse nicht automatisch angewendet werden und Sie die Konsequenzen steuern können.

* Stellen Sie sicher, dass alle Benutzer über gültige E-Mail-Adressen verfügen, die in Azure AD aufgeführt sind, und E-Mail-Kommunikation erhalten, um die entsprechende Maßnahme ergreifen zu können. 

* Dokumentieren Sie alle Zugriffsberechtigungen, die im Rahmen des Pilotversuchs entfernt wurden, falls Sie sie schnell wiederherstellen müssen.

* Prüfen Sie Überwachungsprotokolle, um sicherzustellen, dass alle Ereignisse ordnungsgemäß überwacht werden.

Weitere Informationen finden Sie unter [Bewährte Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md).

## <a name="introduction-to-access-reviews"></a>Einführung in Zugriffsüberprüfungen

In diesem Abschnitt werden die Konzepte der Zugriffsüberprüfung erläutert, die Ihnen vor dem Planen der Überprüfungen vertraut sein sollten.

### <a name="what-resource-types-can-be-reviewed"></a>Welche Ressourcentypen können überprüft werden?

Sobald Sie die Ressourcen Ihrer Organisation in Azure AD integriert haben (z. B. Benutzer, Anwendungen und Gruppen), können sie verwaltet und überprüft werden. 

Typische Ziele für die Überprüfung:

* [Anwendungen, die für das einmalige Anmelden in Azure AD integriert sind](../manage-apps/what-is-application-management.md) (z. B. SaaS, Branchenanwendungen).

* [Mitgliedschaft](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) in Gruppen (mit Azure AD synchronisiert oder in Azure AD oder Microsoft 365 erstellt, einschließlich Microsoft Teams).

* [Zugriffspaket](./entitlement-management-overview.md) zum Gruppieren von Ressourcen (Gruppen, Apps und Websites) in einem Paket für die Zugriffsverwaltung.

* [Azure AD- und Azure-Ressourcenrollen](../privileged-identity-management/pim-resource-roles-assign-roles.md) gemäß ihrer Definition in Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Von wem werden Zugriffsüberprüfungen erstellt und verwaltet?

Die zum Erstellen, Verwalten oder Lesen einer Zugriffsüberprüfung erforderliche Verwaltungsrolle hängt vom Typ der überprüften Ressource ab. In der folgenden Tabelle sind die für jeden Ressourcentyp erforderlichen Rollen aufgeführt:

| Ressourcentyp| Zugriffsüberprüfungen erstellen und verwalten (Ersteller)| Ergebnisse der Zugriffsüberprüfung lesen |
| - | - | -|
| Gruppe oder Anwendung| Globaler Administrator <p>Benutzeradministrator| Ersteller oder Sicherheitsadministrator |
| Privilegierte Rollen in Azure AD| Globaler Administrator <p>Administrator für privilegierte Rollen| Ersteller <p>Sicherheitsleseberechtigter<p>Sicherheitsadministrator |
| Privilegierte Rollen in Azure (Ressourcen)| Globaler Administrator<p>Benutzeradministrator<p>Ressourcenbesitzer| Ersteller |
| Zugriffspaket| Globaler Administrator<p>Ersteller des Zugriffspakets| Nur globaler Administrator |

Weitere Informationen finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Wer überprüft den Zugriff auf die Ressource?

Der Ersteller der Zugriffsüberprüfung legt zum Zeitpunkt der Erstellung fest, wer die Überprüfung durchführt. Diese Einstellung kann nicht mehr geändert werden, nachdem die Überprüfung gestartet wurde. Prüfer werden durch drei Personas dargestellt:

* Ressourcenbesitzer, die der Geschäftsinhaber der Ressource sind.

* Eine Reihe von einzeln vom Administrator für Zugriffsüberprüfungen ausgewählten Delegaten.

* Endbenutzer, die eine Notwendigkeit des Fortbestands Ihres Zugriffs selbst nachweisen.

* Manager überprüfen den Zugriff ihrer direkten Berichte auf die Ressource. 

Beim Erstellen einer Zugriffsüberprüfung können Administratoren einen oder mehrere Prüfer auswählen. Alle Prüfer können eine Überprüfung starten und ausführen, wobei sie Benutzer für den fortwährenden Zugriff auf eine Ressource auswählen oder entfernen. 

### <a name="components-of-an-access-review"></a>Komponenten einer Zugriffsüberprüfung

Vor der Implementierung Ihrer Zugriffsüberprüfungen sollten Sie die für Ihre Organisation relevanten Überprüfungstypen planen. Hierzu müssen Sie Geschäftsentscheidungen darüber treffen, was Sie überprüfen möchten und welche Maßnahmen auf Grundlage dieser Überprüfungen durchgeführt werden sollen.

Zum Erstellen einer Zugriffsüberprüfungsrichtlinie benötigen Sie die folgenden Informationen:

* Welche Ressourcen sollen überprüft werden?

* Wessen Zugriff soll überprüft werden?

* Wie oft soll die Überprüfung durchgeführt werden?

* Wer soll die Überprüfung durchführen?

   * Wie werden sie von der Überprüfung in Kenntnis gesetzt?

   * Welche Fristen sollen für Überprüfung gelten?

* Welche automatischen Aktionen sollten auf Grundlage der Überprüfung erzwungen werden?

   * Was geschieht, wenn der Prüfer nicht rechtzeitig reagiert?

* Welche manuellen Aktionen werden in Folge der Überprüfung ausgeführt?

* Welche Mitteilungen sollten auf Grundlage der ergriffenen Maßnahmen gesendet werden?

**Beispiel für einen Zugriffsüberprüfungsplan**

| Komponente| Wert |
| - | - |
| **Zu überprüfende Ressourcen**| Zugriff auf Microsoft Dynamics |
| **Häufigkeit der Überprüfung**| Monatlich |
| **Zuständiger für die Überprüfung**| Programmmanager der Dynamics-Unternehmensgruppe |
| **Benachrichtigung**| E-Mail, die zu Beginn der Überprüfung an den Alias Dynamics-Pms gesendet wird<p>Einschließen einer benutzerdefinierten Nachricht mit einer Handlungsaufforderung an die Prüfer |
| **Zeitachse**| 48 Stunden ab Zeitpunkt der Benachrichtigung |
|**Automatische Aktionen**| Entfernen des Zugriffs aller Konten, die sich seit 90 Tagen nicht interaktiv angemeldet haben, indem Benutzer aus der Sicherheitsgruppe „dynamics-access“ entfernt werden. <p>*Aktionen ausführen, wenn die Überprüfung nicht fristgerecht erfolgt* |
| **Manuelle Aktionen**| Prüfer können Entfernungen bei Bedarf genehmigen, bevor die automatisierte Aktion ausgeführt wird. |

### <a name="automate-actions-based-on-access-reviews"></a>Automatisieren von Aktionen auf Grundlage von Zugriffsüberprüfungen

Sie können den Zugriff automatisch entfernen lassen, indem Sie die Option „Ergebnisse automatisch auf Ressource anwenden“ auf „Aktivieren“ festlegen.

  ![Planen von Zugriffsüberprüfungen](./media/deploy-access-review/3-automate-actions-settings.png)

Nachdem die Überprüfung abgeschlossen und beendet wurde, werden Benutzer, die vom Prüfer nicht genehmigt wurden, automatisch aus der Ressource entfernt – oder sie werden beibehalten und haben weiterhin Zugriff. Dies kann bedeuten, dass deren Gruppenmitgliedschaft, deren Anwendungszuweisung oder deren Recht zum Heraufstufen auf eine privilegierte Rolle aufgehoben wird.

Annehmen der Empfehlungen

Die Empfehlungen werden den Prüfern auf der Benutzeroberfläche für Prüfer angezeigt, und nennen den Zeitpunkt der letzten Anmeldung einer Person beim Mandanten oder des letzten Zugriffs auf eine Anwendung. Anhand dieser Informationen können Prüfer die richtige Entscheidung bezüglich des Zugriffs treffen. Wenn Sie „Empfehlungen annehmen“ auswählen, werden die Empfehlungen aus der Zugriffsüberprüfung umgesetzt. Am Ende einer Zugriffsüberprüfung wendet das System diese Empfehlungen automatisch auf Benutzer an, für die Prüfer keine Aktion ausgeführt haben.

Empfehlungen basieren auf den Kriterien in der Zugriffsüberprüfung. Wenn Sie z. B. die Überprüfung so konfigurieren, dass der Zugriff nach 90 Tagen ohne interaktive Anmeldung entfernt wird, lautet die Empfehlung, alle Benutzer zu entfernen, die diese Kriterien erfüllen. Microsoft arbeitet kontinuierlich an der Verbesserung der Empfehlungen. 

### <a name="review-guest-user-access"></a>Überprüfen des Gastbenutzerzugriffs

Mithilfe von Zugriffsüberprüfungen können Sie die Identitäten von Kooperationspartnern aus externen Organisationen überprüfen und bereinigen. Die Konfiguration einer Überprüfung aller einzelnen Partner kann die Anforderungen der Compliance erfüllen.

Externen Identitäten kann über eine der folgenden Aktionen Zugriff auf Unternehmensressourcen gewährt werden:

* Hinzufügen zu einer Gruppe 

* Einladen zu Teams 

* Zuweisen zu einer Unternehmensanwendung oder einem Zugriffspaket

* Zuweisen einer privilegierten Rolle in Azure AD oder in einem Azure-Abonnement

Siehe das [Beispielskript](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Das Skript zeigt, wo externe Identitäten, die zum Mandanten eingeladen wurden, verwendet werden. Sie können die Gruppenmitgliedschaft, die Rollen- und Anwendungszuweisungen externer Benutzer in Azure AD ablesen. Das Skript zeigt keine Zuweisungen außerhalb von Azure AD an, z. B. direkt zugewiesene Rechte für SharePoint-Ressourcen, wenn keine Gruppen verwendet werden.

Wenn Sie eine Zugriffsüberprüfung für Gruppen oder Anwendungen erstellen, können Sie festlegen, dass sich der Prüfer auf alle Benutzer mit Zugriff oder nur auf Gastbenutzer konzentrieren soll. Wenn nur Gastbenutzer ausgewählt werden, wird den Prüfern eine gefilterte Liste externer Identitäten aus Azure AD B2B zugewiesen, die Zugriff auf die Ressource haben.

 ![Überprüfen von Gastbenutzern](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Dies schließt KEINE externen Mitglieder mit dem userType „Mitglied“ ein. Dies umfasst auch keine Benutzer, die außerhalb der Azure AD B2B-Zusammenarbeit eingeladen wurden, z. B. solche, die direkt über SharePoint auf freigegebene Inhalte zugreifen können.

## <a name="plan-access-reviews-for-access-packages"></a>Planen von Zugriffsüberprüfungen für Zugriffspakete

[Zugriffspakete](entitlement-management-overview.md) können Ihre Governance- und Zugriffsüberprüfungsstrategie erheblich vereinfachen. Ein Zugriffspaket ist ein Bündel aller Ressourcen mit den Zugriffsrechten, die ein Benutzer benötigt, um an einem Projekt zu arbeiten oder seine Aufgaben auszuführen. Beispielsweise können Sie ein Zugriffspaket erstellen, das alle Anwendungen enthält, die Entwickler in Ihrer Organisation benötigen, oder alle Anwendungen, auf die externe Benutzer Zugriff haben sollen. Ein Administrator oder delegierter Zugriffspaket-Manager fasst die Ressourcen (Gruppen oder Apps) sowie die Rollen, die die Benutzer für diese Ressourcen benötigen, in Gruppen zusammen.

Beim [Erstellen eines Zugriffspakets](entitlement-management-access-package-create.md) können Sie eine oder mehrere Zugriffsrichtlinien erstellen, die definieren, unter welchen Bedingungen die Benutzer ein Zugriffspaket anfordern können, wie der Genehmigungsprozess aussieht und wie oft eine Person den Zugriff erneut anfordern muss. Zugriffsüberprüfungen werden beim Erstellen oder Bearbeiten einer Zugriffspaketrichtlinie konfiguriert.

Öffnen Sie die Registerkarte „Lebenszyklus“, und scrollen Sie nach unten zu „Zugriffsüberprüfungen“.

 ![Screenshot der Option „Richtlinie bearbeiten“ auf der Registerkarte „Lebenszyklus“.](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Planen von Zugriffsüberprüfungen für Gruppen

Neben Zugriffspaketen stellt das Überprüfen der Gruppenmitgliedschaft die effektivste Methode zum Steuern des Zugriffs dar. Wir empfehlen, den Zugriff auf Ressourcen über [Sicherheitsgruppen oder Microsoft 365-Gruppen](../fundamentals/active-directory-manage-groups.md) zuzuweisen und diesen Gruppen Benutzern hinzuzufügen, die Zugriff erhalten.

Einer einzelnen Gruppe kann der Zugriff auf alle entsprechenden Ressourcen gewährt werden. Sie können der Gruppe Zugriff auf einzelne Ressourcen oder ein Zugriffspaket zuweisen, in dem Anwendungen und andere Ressourcen gruppiert sind. Mit dieser Methode können Sie den Zugriff auf die Gruppe statt den eines einzelnen Benutzers auf jede einzelne Anwendung überprüfen. 

Die Gruppenmitgliedschaft kann von folgenden Benutzern überprüft werden: 

* Administratoren

* Gruppenbesitzer

* Ausgewählte Benutzer, an die die Überprüfungsfunktion beim Erstellen der Überprüfung delegiert wurde

* Mitglieder der Gruppe, die eine Bestätigung für sich selbst durchführen

* Manager überprüfen den Zugang ihrer direkten Berichte 

### <a name="group-ownership"></a>Gruppenbesitz

Es wird empfohlen, dass Gruppenbesitzer die Mitgliedschaft überprüfen, da sie am besten wissen, welche Benutzer den Zugriff benötigen. Der Besitz von Gruppen unterscheidet sich je nach Gruppentyp:

Gruppen, die in Microsoft 365 und Azure AD erstellt werden, verfügen über mindestens einen klar definierten Besitzer. In den meisten Fällen sind diese Besitzer ideal als Prüfer für ihre eigenen Gruppen geeignet, da Sie wissen, wer Zugriff haben sollte. 

Beispielsweise verwendet Microsoft Teams Microsoft 365-Gruppen als zugrunde liegendes Autorisierungsmodell, um Benutzern den Zugriff auf Ressourcen zu gewähren, die sich in SharePoint, Exchange, OneNote oder anderen Microsoft 365-Diensten befinden. Der Ersteller des Teams wird automatisch Besitzer und sollte für die Überprüfung der Mitgliedschaft in dieser Gruppe verantwortlich sein. 

Für Gruppen, die manuell im Azure AD-Portal oder über mithilfe eines Skripts über Microsoft Graph erstellt werden, sind möglicherweise keine Besitzer definiert. Es wird empfohlen, dass Sie diese entweder über das Azure AD-Portal im Abschnitt „Besitzer“ der Gruppe oder über Graph definieren.

Gruppen, die von lokalem Active Directory synchronisiert werden, können in Azure AD nicht über einen Besitzer verfügen. Wenn Sie eine Zugriffsüberprüfung für diese erstellen, sollten Sie Personen auswählen, die Entscheidungen zur Mitgliedschaft am besten treffen können.

> [!NOTE]
> Es wird empfohlen, Geschäftsrichtlinien zu definieren, die die Erstellung von Gruppen regeln, um den Gruppenbesitz und die Zuständigkeit für die regelmäßige Überprüfung der Mitgliedschaft eindeutig festzulegen. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Überprüfen der Mitgliedschaft von Ausschlussgruppen in Richtlinien für bedingten Zugriff 

Gehen Sie zu [Azure AD-Zugriffsüberprüfungen verwenden, um Benutzer zu verwalten, die von den Richtlinien für den bedingten Zugriff ausgeschlossen sind](conditional-access-exclusion.md), um zu erfahren, wie Sie die Mitgliedschaft von Ausschlussgruppen überprüfen.

### <a name="review-guest-users-group-memberships"></a>Überprüfen Sie die Gruppenmitgliedschaften von Gastbenutzern

Unter [Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen](./manage-guest-access-with-access-reviews.md) erfahren Sie, wie Sie den Zugriff von Gastbenutzern auf Gruppenmitgliedschaften überprüfen.

### <a name="review-access-to-on-premises-groups"></a>Überprüfen des Zugriffs auf lokale Gruppen

Mit Zugriffsüberprüfungen kann die Gruppenmitgliedschaft für Gruppen, die mit [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) mit lokalen Gruppen synchronisiert werden, nicht geändert werden. Dies liegt daran, dass die Autoritätsquelle lokal ist.

Sie können trotzdem Zugriffsüberprüfungen verwenden, um regelmäßige Überprüfungen von lokalen Gruppen zu planen und zu verwalten. Prüfer führen Aktionen dann in der lokalen Gruppe aus. Bei dieser Strategie werden alle Überprüfungen ausschließlich mit Zugriffsüberprüfungen durchgeführt.

Sie können die Ergebnisse einer Zugriffsüberprüfung für lokale Gruppen verwenden und auf eine der folgenden Weisen weiterverarbeiten:

* Herunterladen des CSV-Berichts aus der Zugriffsüberprüfung und manuelles Ausführen von Aktionen.

* Verwenden von Microsoft Graph zum programmgesteuerten Zugriff auf Ergebnisse und Entscheidungen in abgeschlossenen Zugriffsüberprüfungen.

Wenn Sie z. B. auf Ergebnisse für eine durch Windows AD verwaltete Gruppe zugreifen möchten, verwenden Sie dieses [PowerShell-Beispielskript](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Das Skript umreißt die erforderlichen Graph-Aufrufe und exportiert die Windows AD-PowerShell-Befehle, um die Änderungen auszuführen.

## <a name="plan-access-reviews-for-applications"></a>Planen von Zugriffsüberprüfungen für Anwendungen 

Wenn Sie den Zugriff auf eine Anwendung überprüfen, überprüfen Sie den Zugriff von Mitarbeitern und externen Identitäten auf die Informationen und Daten in der Anwendung. Überprüfen Sie eine Anwendung, wenn Sie wissen möchten, wer Zugriff auf eine bestimmte Anwendung statt auf ein Zugriffspaket oder eine Gruppe hat. 

Es wird empfohlen, in den folgenden Szenarien Überprüfungen für Anwendungen zu planen:

* Benutzern wird der direkte Zugriff auf die Anwendung gewährt (unabhängig von einer Gruppe oder einem Zugriffspaket).

* Die Anwendung macht kritische oder vertrauliche Informationen verfügbar.

* Die Anwendung verfügt über bestimmte Complianceanforderungen, die nachgewiesen werden müssen.

* Sie vermuten einen nicht zulässigen Zugriff.

Wenn Sie Zugriffsüberprüfungen für eine Anwendung erstellen möchten, legen Sie die Eigenschaft „Benutzerzuweisung erforderlich?“ auf „Ja“ fest. Wenn diese Einstellung auf „Nein“ festgelegt ist, können alle Benutzer in Ihrem Verzeichnis, einschließlich externer Identitäten, auf die Anwendung zugreifen, und Sie können den Zugriff auf die Anwendung nicht überprüfen. 

 ![Planen von App-Zuweisungen](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Anschließend müssen Sie die [Benutzer und Gruppen zuweisen](../manage-apps/assign-user-or-group-access-portal.md), auf die Sie zugreifen möchten. 

### <a name="reviewers-for-an-application"></a>Prüfer für eine Anwendung

Zugriffsüberprüfungen können für die Mitglieder einer Gruppe oder für Benutzer erfolgen, die einer Anwendung zugewiesen wurden. Anwendungen in Azure AD verfügen nicht unbedingt über einen Besitzer. Aus diesem Grund ist die Option zum Auswählen des Anwendungsbesitzers als Prüfer nicht verfügbar. Sie können eine Überprüfung weiter einschränken, sodass anstelle sämtlicher Zugriffe nur Gastbenutzer überprüft werden, die der Anwendung zugewiesen sind.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Planen der Überprüfung von Azure AD- und Azure-Ressourcenrollen

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) vereinfacht für Unternehmen die Verwaltung des privilegierten Zugriffs auf Ressourcen in Azure AD. Dadurch wird die Liste der privilegierten Rollen in [Azure AD](../roles/permissions-reference.md) und [Azure-Ressourcen](../../role-based-access-control/built-in-roles.md) erheblich kleiner, und die Gesamtsicherheit des Verzeichnisses wird erhöht.

Anhand von Zugriffsüberprüfungen können Prüfer feststellen, ob Benutzer eine Rolle weiterhin benötigen. Ebenso wie Zugriffsüberprüfungen für Zugriffspakete sind die Überprüfungen für Azure AD-Rollen und Azure-Ressourcen in die PIM-Administratoroberfläche integriert. Es wird empfohlen, die folgenden Rollenzuweisungen regelmäßig zu überprüfen:

* Globaler Administrator

* Benutzeradministrator

* Privilegierter Authentifizierungsadministrator

* Administrator für den bedingten Zugriff

* Sicherheitsadministrator

* Alle Rollen für Microsoft 365 und die Dynamics-Dienstverwaltung

Zu den überprüften Rollen gehören permanente und wählbare Zuweisungen. 

Wählen Sie im Abschnitt Prüfer mindestens eine Person für die Überprüfung aller Benutzer aus. Alternativ können Sie auswählen, dass die Mitglieder ihren eigenen Zugriff überprüfen.

 ![Richtlinie bearbeiten](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Bereitstellen von Zugriffsüberprüfungen

Nachdem Sie eine Strategie und einen Plan zum Überprüfen des Zugriffs auf Ressourcen, die in Azure AD integriert sind, ausgearbeitet haben, können Sie Überprüfungen mithilfe der unten aufgeführten Ressourcen bereitstellen und verwalten.

### <a name="review-access-packages"></a>Überprüfen von Zugriffspaketen

Um das Risiko eines veralteten Zugriffs zu verringern, können Administratoren regelmäßige Überprüfungen der Benutzer aktivieren, die über aktive Zuweisungen zu einem Zugriffspaket verfügen. Folgen Sie den Anweisungen unter dem unten angegebenen Link:

| Anleitungen| BESCHREIBUNG |
| - | - |
| [Erstellen von Zugriffsüberprüfungen](entitlement-management-access-reviews-create.md)| Aktivieren Sie Zugriffspaketüberprüfungen. |
| [Ausführen von Zugriffsüberprüfungen](entitlement-management-access-reviews-review-access.md)| Führen Sie Zugriffsüberprüfungen für andere Benutzer aus, die einem Zugriffspaket zugewiesen sind. |
| [Selbstüberprüfung zugewiesener Zugriffspakete](entitlement-management-access-reviews-self-review.md)| Selbstüberprüfung zugewiesener Zugriffspakete |

> [!NOTE]
> Endbenutzer, die eine Selbstüberprüfung ausführen und angeben, dass sie keinen Zugriff mehr benötigen, werden nicht sofort aus dem Zugriffspaket entfernt. Sie werden aus dem Zugriffspaket entfernt, wenn die Überprüfung abgeschlossen ist oder ein Administrator die Überprüfung beendet.

### <a name="review-groups-and-apps"></a>Überprüfen von Gruppen und Apps

Die Notwendigkeit des Zugriffs von Mitarbeitern und Gästen auf Gruppen und Anwendungen ändert sich wahrscheinlich mit der Zeit. Zur Senkung der Risiken im Zusammenhang mit veralteten Zugriffszuweisungen können Administratoren Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff erstellen. Folgen Sie den Anweisungen unter dem unten angegebenen Link:

| Anleitungen| BESCHREIBUNG |
| - | - |
| [Erstellen von Zugriffsüberprüfungen](create-access-review.md)| Erstellen Sie eine oder mehrere Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff. |
| [Ausführen von Zugriffsüberprüfungen](perform-access-review.md)| Führen Sie eine Zugriffsüberprüfung für Mitglieder einer Gruppe oder Benutzer mit Zugriff auf eine Anwendung aus. |
| [Selbstüberprüfung des Zugriffs](review-your-access.md)| Mitglieder überprüfen den eigenen Zugriff auf eine Gruppe oder eine Anwendung. |
| [Abschließen einer Zugriffsüberprüfung](complete-access-review.md)| Öffnen Sie eine Zugriffsüberprüfung, und wenden Sie die Ergebnisse an. |
| [Ausführen von Aktionen für lokale Gruppen](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| PowerShell-Beispielskript, um die Ergebnisse von Zugriffsüberprüfungen für lokale Gruppen umzusetzen. |

### <a name="review-azure-ad-roles"></a>Überprüfen von Azure AD-Rollen

Sie sollten den Zugriff von privilegierten Azure AD-Rollen in regelmäßigen Abständen überprüfen, um das mit veralteten Rollenzuweisungen verbundene Risiko zu verringern.

![Screenshot der Liste „Mitgliedschaft überprüfen“ der Azure AD-Rollen.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Folgen Sie den Anweisungen unter den nachstehenden Links:

| Anleitungen | BESCHREIBUNG |
| - | - |
 [Erstellen von Zugriffsüberprüfungen](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Erstellen Sie Zugriffsüberprüfungen für privilegierte Azure AD-Rollen in PIM. |
| [Selbstüberprüfung des Zugriffs](../privileged-identity-management/pim-perform-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Wenn Sie einer Administratorrolle zugewiesen sind, genehmigen oder verweigern Sie den Zugriff Ihrer Rolle. |
| [Abschließen einer Zugriffsüberprüfung](../privileged-identity-management/pim-complete-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Öffnen Sie eine Zugriffsüberprüfung, und wenden Sie die Ergebnisse an. |


### <a name="review-azure-resource-roles"></a>Überprüfen von Azure-Ressourcenrollen

Sie sollten den Zugriff von privilegierten Azure-Ressourcenrollen in regelmäßigen Abständen überprüfen, um das mit veralteten Rollenzuweisungen verbundene Risiko zu verringern. 

![Überprüfen von Azure AD-Rollen](./media/deploy-access-review/9-review-azure-roles-picker.png)

Folgen Sie den Anweisungen unter den nachstehenden Links:

| Anleitungen| BESCHREIBUNG |
| - | -|
| [Erstellen von Zugriffsüberprüfungen](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Erstellen Sie Zugriffsüberprüfungen für privilegierte Azure-Ressourcenrollen in PIM. |
| [Selbstüberprüfung des Zugriffs](../privileged-identity-management/pim-perform-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Wenn Sie einer Administratorrolle zugewiesen sind, genehmigen oder verweigern Sie den Zugriff Ihrer Rolle. |
| [Abschließen einer Zugriffsüberprüfung](../privileged-identity-management/pim-complete-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Öffnen Sie eine Zugriffsüberprüfung, und wenden Sie die Ergebnisse an. |


## <a name="use-the-access-reviews-api"></a>Verwenden der Zugriffsüberprüfungs-API

Weitere Informationen zur Verwendung und Verwaltung von überprüfbaren Ressourcen finden Sie unter [Graph-API-Methoden](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) und [Autorisierungsüberprüfungen für Rollen- und Anwendungsberechtigungen](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true). Die Zugriffsüberprüfungsmethoden in der Microsoft Graph-API stehen im Anwendungs- und Benutzerkontext zur Verfügung. Beim Ausführen von Skripts im Anwendungskontext muss dem Konto, das zum Ausführen der API verwendet wird (d. h. dem Dienstprinzipal), die Berechtigung „AccessReview.Read.All“ erteilt werden, um Informationen über Zugriffsüberprüfungen abfragen zu können.

Gängige Aufgaben bei Zugriffsüberprüfungen, die mit der Graph-API für Zugriffsüberprüfungen automatisiert werden können:

* Erstellen und Starten einer Zugriffsüberprüfung

* Manuelles Beenden einer Zugriffsüberprüfung vor dem planmäßigen Ende

* Auflisten der Zugriffsüberprüfungen mit dem jeweiligen Status

* Sehen Sie sich den Verlauf einer Überprüfungsreihe und die Entscheidungen und Aktionen zu den einzelnen Prüfungen an.

* Sammeln von Entscheidungen aus einer Zugriffsüberprüfung

* Sammeln Sie Entscheidungen aus abgeschlossenen Überprüfungen, bei denen der Prüfer eine andere Entscheidung getroffen hat, als vom System empfohlen wurde.

Wenn Sie neue Graph-API-Abfragen für die Automatisierung erstellen, empfiehlt es sich, den [Graph-Tester](https://developer.microsoft.com/en-us/graph/graph-explorer) zu verwenden. Sie können Ihre Graph-Abfragen erstellen und testen, bevor Sie sie in Skripts und Code einfügen. Dies kann dabei helfen, die Abfrage schnell zu überarbeiten, sodass Sie genau die gewünschten Ergebnisse erhalten, ohne den Code des Skripts ändern zu müssen.

## <a name="monitor-access-reviews"></a>Überwachen von Zugriffsüberprüfungen

Zugriffsüberprüfungsaktivitäten werden aufgezeichnet und stehen in den [Überwachungsprotokollen von Azure AD](../reports-monitoring/concept-audit-logs.md) zur Verfügung. Sie können die Überwachungsdaten nach Kategorie, Aktivitätstyp und Datumsbereich filtern. Hier sehen Sie eine Beispielabfrage:

| Category| Richtlinie |
| - | - |
| Aktivitätstyp| Zugriffsüberprüfung erstellen |
| | Zugriffsüberprüfung aktualisieren |
| | Zugriffsüberprüfung beendet |
| | Zugriffsüberprüfung löschen |
| | Entscheidung genehmigen |
| | Entscheidung ablehnen |
| | Entscheidung zurücksetzen |
| | Entscheidung anwenden |
| Datumsbereich| sieben Tage |


Wenn Sie erweiterte Abfragen und Analysen von Zugriffsüberprüfungen durchführen und Änderungen und den Abschluss von Überprüfungen nachverfolgen möchten, sollten Sie Ihre Azure AD-Überwachungsprotokolle in [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) oder Azure Event Hub exportieren. Wenn sie in Azure Log Analytics gespeichert werden, können Sie die [leistungsfähige Analysesprache](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) verwenden und eigene Dashboards erstellen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die im folgenden aufgeführten verwandten Technologien.

* [Was ist die Azure AD-Berechtigungsverwaltung?](entitlement-management-overview.md)

* [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
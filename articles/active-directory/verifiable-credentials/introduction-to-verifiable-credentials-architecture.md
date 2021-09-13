---
title: Übersicht über die Azure Active Directory-Architektur (Vorschau)
description: Grundlegende Informationen zum Planen und Entwerfen Ihrer Lösung
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.openlocfilehash: 45dd0be664474b22a24354903233e5729479f095
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345816"
---
# <a name="azure-ad-verifiable-credentials-architecture-overview-preview"></a>Übersicht über die Architektur von Azure AD-Nachweisen (Vorschau)

> [!IMPORTANT]
> Überprüfbare Anmeldeinformationen für Azure Active Directory-befinden sich derzeit in der öffentlichen Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie in den [**zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie sollten Ihre Lösung für Nachweise so planen, dass Sie nicht nur Anmeldeinformationen ausstellen und überprüfen, sondern auch einen umfassenden Überblick über die Architektur und die geschäftlichen Auswirkungen Ihrer Lösung haben. Wenn Sie die [Einführung in Nachweise für Azure Active Directory](decentralized-identifier-overview.md) und die [Häufig gestellten Fragen](verifiable-credentials-faq.md) nicht bereits kennen, sollten Sie die beiden Dokumente lesen und anschließend das Tutorial [Erste Schritte](get-started-verifiable-credentials.md) absolvieren. 

In dieser Übersicht über die Architektur werden die Funktionen und Komponenten des Diensts Azure Active Directory-Nachweise vorgestellt. Ausführlichere Informationen zur Ausstellung und Überprüfung finden Sie unter 

* [Planen Ihrer Ausstellungslösung](plan-issuance-solution.md)

* [Planen Ihrer Überprüfungslösung](plan-verification-solution.md)

## <a name="approaches-to-identity"></a>Konzepte rund um die Identität

Heutzutage werden in den meisten Organisationen für die Bereitstellung von Anmeldeinformationen für Mitarbeiter Systeme zentralisierter Identitäten verwendet. Darüber hinaus werden verschiedene Methoden verwendet, um Kunden, Partner, Anbieter und vertrauende Seiten in die Vertrauensstellungsgrenzen der Organisation einzubinden. Zu diesen Methoden gehören der Verbund, das Erstellen und Verwalten von Gastkonten mit Systemen wie Azure AD B2B sowie das Erstellen von expliziten Vertrauensstellungen mit vertrauenden Seiten. Die meisten Geschäftsbeziehungen weisen eine digitale Komponente auf, sodass das Ermöglichen einer Form von Vertrauensstellung zwischen Organisationen einen erheblichen Aufwand erfordert. 

### <a name="centralized-identity-systems"></a>Systeme zentralisierter Identitäten

Zentrale Konzepte funktionieren in vielen Fällen weiterhin gut, z. B. wenn Anwendungen, Dienste und Geräte auf den Mechanismen zur Herstellung einer Vertrauensstellung basieren, die innerhalb einer Domäne oder Vertrauensstellungsgrenze verwendet werden. 

Bei Systemen zentralisierter Identitäten bestimmt der Identitätsanbieter (IDP) den Lebenszyklus und die Nutzung von Anmeldeinformationen. 

![Beispiel für ein System zentralisierter Identitäten](./media/introduction-to-verifiable-credentials-architecture/centralized-identity-architecture.png)


Es gibt jedoch Szenarios, in denen eine dezentrale Architektur mit Nachweisen durch die Verbesserung wichtiger Szenarios wie den folgenden von Vorteil sein kann:

* Sicheres Onboarding der Identitäten von Mitarbeitern und anderen Personen auch bei Remoteszenarios

* Zugriff auf Ressourcen innerhalb der Vertrauensstellungsgrenze der Organisation anhand bestimmter Kriterien

* Zugriff auf Ressourcen außerhalb der Vertrauensstellungsgrenze, etwa auf Ressourcen eines Partners, mit von der Organisation ausgestellten übertragbaren Anmeldeinformationen.

 

### <a name="decentralized-identity-systems"></a>Systeme dezentralisierter Identitäten

In Systemen dezentralisierter Identitäten teilen sich der Aussteller, der Inhaber und die vertrauende Seite, die die Anmeldeinformationen nutzt, die Kontrolle über Lebenszyklus und Nutzung der Anmeldeinformationen.

Betrachten Sie das Szenario in der folgenden Abbildung: Hier soll Mitarbeitern von Woodgrove über die E-Commerce-Website Proseware ein Unternehmensrabatt angeboten werden. 

 ![Beispiel für ein System dezentralisierter Identitäten](media/introduction-to-verifiable-credentials-architecture/decentralized-architecture.png)


Wenn Sie mit Nachweisen nicht vertraut sind, kann die entsprechende Terminologie verwirrend sein. Die folgenden Definitionen stammen aus dem Abschnitt „Terminology“ (Terminologie) unter [Verifiable Credentials Data Model 1.0 (Datenmodell 1.0 für Nachweise)](https://www.w3.org/TR/vc-data-model/). Die einzelnen Definitionen sind Entitäten in der obigen Abbildung zugeordnet.

 „Ein ***Aussteller*** ist eine Rolle, die eine Entität ausüben kann, indem sie Ansprüche mindestens eines Antragstellers bestätigt und aus diesen Ansprüchen einen Nachweis erstellt und an einen Inhaber überträgt.“

* In der obigen Abbildung ist Woodgrove der Aussteller von Nachweisen für seine Mitarbeiter. 

 „Ein ***Inhaber*** ist eine Rolle, die eine Entität ausüben kann, indem sie mindestens einen Nachweis besitzt und daraus Präsentationen erstellt. Ein Inhaber ist meist, aber nicht immer, der Antragsteller der Nachweise, die er besitzt. Inhaber speichern ihre Anmeldeinformationen in Nachweisrepositorys.“ 

* In der obigen Abbildung ist Alice eine Woodgrove-Mitarbeiterin. Sie hat vom Woodgrove-Aussteller einen Nachweis erhalten und ist Inhaber dieser Anmeldeinformation.

 „Ein ***Überprüfer*** ist eine Rolle, die eine Entität ausüben kann, indem sie optional innerhalb einer überprüfbaren Präsentation mindestens einen Nachweis zur Verarbeitung erhält. Bei anderen Spezifikationen wird dieses Konzept als vertrauende Seite bezeichnet.“

* In der obigen Abbildung ist Proseware ein Überprüfer von Anmeldeinformationen, die von Woodgrove ausgestellt werden. 

„***Anmeldeinformationen*** sind Ansprüche, die von einem Aussteller gestellt werden. Bei einem Nachweis handelt es sich um manipulationssichere Anmeldeinformationen deren Urheberschaft kryptografisch überprüft werden kann. Mit Nachweisen können überprüfbare Präsentationen erstellt werden, die ebenfalls kryptografisch überprüft werden können. Die in Anmeldeinformationen enthaltene Ansprüche können sich auf verschiedene Antragsteller beziehen.“

 „Ein ***dezentraler Bezeichner*** ist ein portierbarer URI-basierter Bezeichner, der auch als DID (Decentralized IDentifier) bezeichnet wird und einer Entität zugeordnet ist. Diese Bezeichner werden häufig in Nachweisen verwendet und sind Antragstellern, Ausstellern und Überprüfern zugeordnet.“

* In der obigen Abbildung sind die dargestellten öffentlichen Schlüssel der DIDs der Akteure im dezentralen Ledger (ION) gespeichert, und zwar im dezentralen Bezeichnerdokument.

 „Ein ***Dokument mit dezentralen Bezeichnern** _, das auch als _*_DID-Dokument_** bezeichnet wird, ist ein Dokument, auf das über eine überprüfbare Datenregistrierung zugegriffen werden kann und das Informationen zu einem bestimmten dezentralen Bezeichner wie etwa zum jeweiligen Repository und Informationen zu öffentlichen Schlüsseln enthält.“ 

* Im obigen Szenario verfügen sowohl der Aussteller als auch der Überprüfer über einen dezentralen Bezeichner (DID) und ein DID-Dokument. Das DID-Dokument enthält den öffentlichen Schlüssel und die Liste mit den DNS-Webdomänen, die dem dezentralen Bezeichner (DID) zugewiesen sind (und auch als verknüpfte Domänen bezeichnet werden).

* Woodgrove (Aussteller) signiert die Nachweise seiner Mitarbeiter mit seinem öffentlichen Schlüssel. Entsprechend signiert Proseware (Überprüfer) Anforderungen zur Vorlage eines Nachweises mit seinem Schlüssel, der auch mit dem entsprechenden dezentralen Bezeichner (DID) verknüpft ist.

 „Ein ***Distributed Ledger*** ist ein nicht zentrales System zum Aufzeichnen von Ereignissen. Diese Systeme schaffen so viel Vertrauen, dass sich Teilnehmer bei operativen Entscheidungen auf die von anderen aufgezeichneten Daten verlassen können. Bei diesen Systemen werden in der Regel verteilte Datenbanken verwendet, bei denen verschiedene Knoten ein Konsensprotokoll verwenden, um die Reihenfolge von kryptografisch signierten Transaktionen zu bestätigen. Durch die Verknüpfung von digital signierten Transaktionen kann der Verlauf des Ledgers im Laufe der Zeit häufig nicht mehr verändert werden.“

* Bei der Lösung von Microsoft wird das ***Identity Overlay Network (ION)*** verwendet, um eine dezentrale Public Key-Infrastruktur (PKI) bereitzustellen.

 

### <a name="combining-centralized-and-decentralized-identity-architectures"></a>Kombinierte Architektur zentralisierter und dezentralisierter Identitäten

Bei der Untersuchung einer Lösung für Nachweise ist es hilfreich zu wissen, wie Architekturen dezentralisierter Identitäten mit Architekturen zentralisierter Identitäten kombiniert werden können, um eine Lösung bereitstellen zu können, mit der sich Risiken reduzieren lassen, und die erhebliche betriebliche Vorteile bietet.

## <a name="the-user-journey"></a>Die User Journey

Bei dieser Übersicht über die Architektur wird der Weg eines Bewerbers und Mitarbeiters verfolgt, der sich bei einer Organisation um eine Stelle bewirbt und diese annimmt. Dabei werden Mitarbeiter und Organisation bei Veränderungen nachverfolgt, bei denen zentrale Prozesse durch Nachweise verbessert werden können.

 

### <a name="actors-in-these-use-cases"></a>Akteure in diesen Anwendungsfällen

* **Alice**, eine Person, die sich bei Woodgrove, Inc. um eine Stelle bewirbt und diese annimmt

* **Woodgrove**, Inc, ein fiktives Unternehmen

* **Adatum**, der fiktive Partner von Woodgrove für die Identitätsüberprüfung

* **Proseware**, die fiktive Partnerorganisation von Woodgrove

Bei Woodgrove werden sowohl Architekturen zentralisierter Identitäten als auch Architekturen dezentralisierter Identitäten verwendet.

### <a name="steps-in-the-user-journey"></a>Schritte der User Journey

* Alice bewirbt sich bei Woodgrove, Inc. um eine Stelle, nimmt diese an und wird eingearbeitet.

* Sie greift auf digitale Ressourcen innerhalb der Vertrauensstellungsgrenze von Woodgrove zu.

* Sie greift auf digitale Ressourcen außerhalb der Vertrauensstellungsgrenze von Woodgrove zu, ohne die Vertrauensstellungsgrenzen von Woodgrove oder die der Partner zu erweitern.

Im Rahmen der Geschäftstätigkeiten von Woodgrove müssen fortlaufend Identitäten verwaltet werden. Anhand der Anwendungsfälle in diesem Leitfaden wird beschrieben, wie Alice mithilfe von Self-Service-Funktionen Bezeichner abruft und verwaltet, und wie bei Woodgrove Business-to-Business-Beziehungen mit unterschiedlichen Anforderungen hinsichtlich der Vertrauensstellung hinzugefügt, geändert und beendet werden. 

Anhand der Anwendungsfälle wird deutlich, wie zentrale und dezentrale Identitäten so kombiniert werden können, dass damit eine robustere und effizientere Identitäts- und Vertrauensstellungsstrategie und ein entsprechender Lebenszyklus bereitgestellt werden können. 


## <a name="user-journey-onboarding-to-woodgrove"></a>User Journey: Onboarding bei Woodgrove

![Onboarding-Journey des Benutzers bei Woodgrove](media/introduction-to-verifiable-credentials-architecture/onboarding-journey.png)

 **Wahrnehmung**: Alice interessiert sich für die Arbeit bei Woodgrove, Inc. und besucht die Karrierewebsite von Woodgrove. 

**Aktivierung**: Auf der Woodgrove-Website findet Alice eine Methode zum Nachweis ihrer Identität vor, bei der sie über einen QR-Code oder einen Deep-Link auf eine Website von Adatum, dem vertrauenswürdigen Partner von Woodgrove zur Überprüfung von Identitäten, gelangt.

**Anforderung und Upload**: Adatum fordert einen Identitätsnachweis von Alice an. Alice macht ein Selfie und ein Foto von ihrem Führerschein und lädt beides auf die Website von Adatum hoch. 

**Ausstellung**: Nachdem bei Adatum die Identität von Alice überprüft wurde, wird Alice von Adatum ein Nachweis zur Bestätigung ihrer Identität ausgestellt.

**Präsentation**: Alice (Inhaberin und Antragstellerin der Anmeldeinformationen) kann dann auf das Karriereportal von Woodgrove zugreifen, um den Bewerbungsprozess zu durchlaufen. Wenn Alice mit dem Nachweis auf das Portal zugreift, übernimmt Woodgrove die Rolle des Überprüfers und der vertrauenden Seite und vertraut dem Nachweis von Adatum.


### <a name="distributing-initial-credentials"></a>Verteilen anfänglicher Anmeldeinformationen

Alice nimmt die Stelle bei Woodgrove an. Im Rahmen des Onboardingprozesses wird ein Azure Active Directory-Konto (AD) erstellt, das Alice innerhalb der Vertrauensstellungsgrenze von Woodgrove verwenden kann. Alices Vorgesetzter muss herausfinden, wie er dafür sorgen kann, dass Alice, die remote arbeitet, erste Anmeldeinformationen auf sichere Weise erhält. In der Vergangenheit hätte die IT-Abteilung diese Anmeldeinformationen an den Vorgesetzten weitergeben können, der sie dann ausgedruckt und Alice ausgehändigt hätte. Bei Remotemitarbeitern funktioniert das jedoch nicht.

Nachweise sind für zentrale Systeme nützlich, da durch sie der Prozess der Verteilung von Anmeldeinformationen verbessert wird. So muss nicht der Vorgesetzte Anmeldeinformationen bereitstellen, sondern Alice kann ihren Nachweis als Identitätsnachweis verwenden, um ihren anfänglichen Benutzernamen und Anmeldeinformationen für den Zugriff auf zentrale Systeme zu erhalten. Alice legt den Identitätsnachweis vor, den sie im Rahmen des Onboardingprozesses ihrem Wallet hinzugefügt hat. 

 

Im Anwendungsfall Onboarding werden die Vertrauensstellungsrollen zwischen dem Aussteller, dem Überprüfer und dem Inhaber verteilt. 

* Der Aussteller ist für die Überprüfung der Ansprüche verantwortlich, die Teil des ausgestellten Nachweises sind. Adatum überprüft zur Ausstellung des Nachweises Alices Identität. In diesen Fall werden die Nachweise ohne Einbeziehen eines Überprüfers oder einer vertrauenden Seite ausgestellt.

* Der Inhaber ist im Besitz des Nachweises und veranlasst die Vorlage des Nachweises zur Überprüfung. Nur Alice kann die Nachweise vorlegen, die sie besitzt.

* Der Überprüfer akzeptiert die Ansprüche in Nachweisen von Ausstellern, denen er vertraut, und überprüft die Nachweise mithilfe der Funktion des dezentralen Ledgers, die im Datenmodell der Nachweise beschrieben wurde. Woodgrove vertraut den Ansprüchen von Adatum zu Alices Identität.

 

Dank der Kombination von Architekturen zentralisierter Identitäten mit Architekturen dezentralisierter Identitäten für das Onboarding müssen für die Identitätsüberprüfung erforderliche privilegierte Informationen über Alice wie etwa staatliche ID-Nummern von Woodgrove nicht gespeichert werden, da darauf vertraut wird, dass Alices Identität durch den vom Identitätsüberprüfungspartner Adatum ausgestellten Nachweis bestätigt wird. Dadurch entsteht weniger doppelte Arbeit, und es kann ein programmatischer und vorhersehbarer Ansatz für die anfänglichen Onboardingaufgaben umgesetzt werden. 

 

## <a name="user-journey-accessing-resources-inside-the-trust-boundary"></a>User Journey: Zugreifen auf Ressourcen innerhalb der Vertrauensstellunggrenze

![Zugreifen auf Ressourcen innerhalb der Vertrauensstellunggrenze](media/introduction-to-verifiable-credentials-architecture/inside-trust-boundary.png)

Als Mitarbeiterin ist Alice innerhalb der Vertrauensstellunggrenze von Woodgrove tätig. Woodgrove fungiert als Identitätsanbieter (Identity Provider, IDP) und behält die vollständige Kontrolle über die Identität und Konfiguration der Apps, die Alice für die Interaktion innerhalb der Vertrauensstellungsgrenze von Woodgrove verwendet. Wenn Alice Ressourcen innerhalb der Azure AD-Vertrauensstellungsgrenze verwenden möchte, stellt sie potenziell mehrere Formen des Identifikationsnachweises zur Verfügung, um sich an der Vertrauensstellungsgrenze von Woodgrove anzumelden und auf die Ressourcen in der Technologieumgebung von Woodgrove zugreifen zu können. Dies ist ein typisches Szenario, für das eine Architektur zentralisierter Identitäten gut geeignet ist. 

* Woodgrove verwaltet die Vertrauensstellunggrenze und gewährt Alice je nach Aufgabe und unter Anwendung bewährter Sicherheitsmaßnahmen die Zugriffsebene mit den wenigsten Rechten. Zur Gewährleistung eines hohen Sicherheitsstandards und möglicherweise aus Compliancegründen muss Woodgrove auch in der Lage sein, die Berechtigungen und den Zugriff auf Ressourcen durch Mitarbeiter nachverfolgen und Berechtigungen widerrufen zu können, wenn das Arbeitsverhältnis beendet wird.

* Alice verwendet nur die Anmeldeinformationen, die von Woodgrove für den Zugriff auf Woodgrove-Ressourcen verwendet werden. Alice muss nicht nachverfolgen, wann die Anmeldeinformationen verwendet werden, da die Anmeldeinformationen von Woodgrove verwaltet und nur mit Woodgrove-Ressourcen verwendet werden. Die Identität ist innerhalb der Woodgrove-Vertrauensstellunggrenze nur gültig, wenn Zugriff auf Woodgrove-Ressourcen erforderlich ist, sodass Alice die Anmeldeinformationen nicht besitzen muss. 

### <a name="using-vcs-inside-the-trust-boundary"></a>Verwenden von Nachweisen innerhalb der Vertrauensstellungsgrenze

Einzelne Mitarbeiter haben sich ändernde Identitätsanforderungen. Für den Umgang mit diesen Änderungen können zentrale Systeme durch Nachweise verbessert werden. 

* Während ihrer Tätigkeit bei Woodgrove benötigt Alice je nach den Anforderungen möglicherweise zusätzlichen Zugriff auf Ressourcen. Wenn Alice beispielsweise eine Datenschutzschulung absolviert, kann ihr ein neue Mitarbeiternachweis mit diesem Anspruch ausgestellt werden, den für den Zugriff auf geschützte Ressourcen verwenden kann.

* Nachweise können innerhalb der Vertrauensstellungsgrenze für die Wiederherstellung von Konten verwendet werden. Wenn ein Mitarbeiter beispielsweise sein Telefon oder seinen Computer verloren hat, kann er den Zugriff wiedererlangen, indem er von dem Identitätsüberprüfungsdienst, der von Woodgrove als vertrauenswürdig eingestuft wird, einen neuen Nachweis und mit dessen Hilfe dann neue Anmeldeinformationen erhält. 

 ## <a name="user-journey-accessing-external-resources"></a>User Journey: Zugreifen auf externe Ressourcen

Woodgrove handelt mit Proseware einen Rabatt beim Kauf von Produkten aus. Alle Woodgrove-Mitarbeiter haben Anspruch auf den Rabatt. Woodgrove möchte Alice eine Möglichkeit bieten, auf die Website von Proseware zuzugreifen und den Rabatt auf erworbene Produkte zu erhalten. Wenn bei Woodgrove eine Architektur zentralisierter Identitäten verwendet wird, gibt es zwei Möglichkeiten, Alice den Rabatt zu gewähren: 

* Alice könnte persönliche Informationen angeben, um ein Konto bei Proseware zu erstellen, und Proseware müsste dann überprüfen, ob Alice bei Woodgrove tätig ist.

* Woodgrove könnte die Vertrauensstellungsgrenze so erweitern, dass Proseware als vertrauende Seite enthalten ist, und Alice könnte die erweiterte Vertrauensstellungsgrenze verwenden, um den Rabatt zu erhalten. 

Mit dezentralen Bezeichnern kann Woodgrove Alice einen Nachweis bereitstellen, mit dem Alice auf die Website von Proseware und andere externe Ressourcen zugreifen kann. 

![Zugreifen auf Ressourcen außerhalb der Vertrauensstellungsgrenze](media/introduction-to-verifiable-credentials-architecture/external-resources.png)

 

Dadurch, dass Woodgrove Alice den Nachweis bereitstellt, wird bestätigt, dass Alice eine Mitarbeiterin ist. Woodgrove gilt in der Überprüfungslösung von Proseware als vertrauenswürdiger Aussteller von Nachweisen. Durch dieses Vertrauen in den Ausstellungsprozess von Woodgrove ist es Proseware möglich, den Nachweis als Beleg dafür, dass Alice Mitarbeiterin bei Woodgrove ist, elektronisch zu akzeptieren und Alice den Rabatt zu gewähren. Im Rahmen der Überprüfung des von Alice vorgelegten Nachweises überprüft Proseware die Gültigkeit des Nachweises mithilfe des Distributed Ledgers. Der Inhalt dieser Lösung:

* Mit Woodgrove kann Alice gegenüber Proseware nachweisen, dass sie bei Woodgrove tätig ist, ohne dass Woodgrove die Vertrauensstellungsgrenze erweitern muss. 

* Proseware muss die Vertrauensstellungsgrenze nicht erweitern, um zu überprüfen, ob Alice bei Woodgrove tätig ist. Proseware kann stattdessen den von Woodgrove bereitgestellten Nachweis verwenden. Da die Vertrauensstellungsgrenze nicht erweitert wird, ist die Verwaltung der Vertrauensstellung einfacher, und Proseware kann die Beziehung problemlos beenden, indem die Nachweise nicht mehr akzeptiert werden.

* Alice muss Proseware keine persönlichen Informationen wie etwa eine E-Mail-Adresse bereitstellen. Alice speichert den Nachweis in einer Wallet-Anwendung auf einem persönlichen Gerät. Die einzige Person, die den Nachweis verwenden kann, ist Alice, und Alice muss die Verwendung der Anmeldeinformationen initiieren. Jede Nutzung des Nachweises wird von der Wallet-Anwendung aufgezeichnet, sodass Alice eine Aufzeichnung darüber hat, wann und wo der Nachweis genutzt wurde. 

 

Durch die Kombination von Architekturen zentralisierter Identitäten mit Architekturen dezentralisierter Identitäten für den Einsatz innerhalb und außerhalb von Vertrauensstellungsgrenzen lassen sich Komplexität und Risiko reduzieren und befristete Beziehungen leichter verwalten. 

### <a name="changes-over-time"></a>Änderungen im Laufe der Zeit

Woodgrove knüpft und beendet Geschäftsbeziehungen mit anderen Organisationen und muss festlegen, wann Architekturen zentralisierter Identitäten und Architekturen dezentralisierter Identitäten verwendet werden.

Wenn Architekturen zentralisierter Identitäten mit Architekturen dezentralisierter Identitäten kombiniert werden, werden die Verantwortung und der Aufwand, die mit Identitäten und Identitätsnachweisen verbunden sind, verteilt, Risiken werden minimiert und der Benutzer muss seine privaten Informationen nicht so häufig und nicht so vielen unbekannten Überprüfern bekanntgeben. Dies gilt insbesondere in folgenden Fällen:

* In Architekturen zentralisierter Identitäten stellt der Identitätsanbieter Anmeldeinformationen aus und führt die Überprüfung dieser ausgestellten Anmeldeinformationen durch. Informationen zu allen Identitäten werden vom Identitätsanbieter verarbeitet, indem sie in einem Verzeichnis gespeichert oder abgerufen werden. Identitätsanbieter können Sicherheitstoken von anderen Identitätsanbietersystemen wie etwa von Systemen für die Anmeldungen bei sozialen Netzwerken oder von Systemen von Geschäftspartnern auch dynamisch akzeptieren. Damit eine vertrauende Seite Identitäten in der Vertrauensstellungsgrenze des Identitätsanbieters verwenden kann, muss sie so konfiguriert sein, dass sie die vom Identitätsanbieter ausgestellten Token akzeptiert.

## <a name="how-decentralized-identity-systems-work"></a>Funktionsweise von Systemen dezentralisierter Identitäten

In Architekturen dezentralisierter Identitäten übernehmen Aussteller, Benutzer und vertrauende Seite jeweils eine Rolle bei der Einrichtung und Sicherstellung des eines kontinuierlichen vertrauenswürdigen Austauschs der gegenseitigen Anmeldeinformationen. Die öffentlichen Schlüssel der dezentralen Bezeichner der Akteure sind im Identity Overlay Network (ION) auflösbar. Dadurch ist es möglich, die Signatur zu überprüfen und ein Artefakt wie etwa einen Nachweis als vertrauenswürdig einzustufen. Vertrauende Seiten können Nachweise nutzen, ohne Vertrauensstellungen mit dem Aussteller einzurichten. Stattdessen stellt der Aussteller dem Antragsteller Anmeldeinformationen bereit, die er den vertrauenden Seiten vorlegen kann. Alle Nachrichten zwischen den Akteuren werden mit dem dezentralen Bezeichner des Akteurs signiert. Dezentrale Bezeichner von Ausstellern und Überprüfern müssen auch die DNS-Domänen besitzen, von denen die Anforderungen generiert wurden. 

Beispiel: Wenn der Inhaber eines Nachweises auf eine Ressource zugreifen möchte, muss er der vertrauenden Seite den Nachweis vorlegen. Dazu verwendet er eine Wallet-Anwendung, damit die Aufforderung der vertrauenden Seite zur Vorlage eines Nachweises gelesen wird. Beim Lesen der Anforderung verwendet die Wallet-Anwendung den dezentralen Bezeichner der vertrauenden Seite, um mithilfe des Identity Overlay Networks die öffentlichen Schlüssel der vertrauenden Seite zu suchen und zu überprüfen, ob die Anforderung zur Vorlage des Nachweises manipuliert wurde. Zum Nachweis des Domänenbesitzes wird mit der Wallet-Anwendung auch überprüft, ob in einem in der DNS-Domäne der vertrauenden Seite gehosteten Metadatendokument auf den dezentralen Bezeichner verwiesen wird. 

 

![Funktionsweise eines Systems dezentralisierter Identitäten](media/introduction-to-verifiable-credentials-architecture/how-decentralized-works.png)

### <a name="flow-1-verifiable-credential-issuance"></a>Flow 1: Ausstellung von Nachweisen

In diesem Flow interagiert der Inhaber der Anmeldeinformationen mit dem Aussteller, um einen Nachweis anzufordern, wie in der folgenden Abbildung dargestellt.

![Ausstellung von Nachweisen](media/introduction-to-verifiable-credentials-architecture/issuance.png)

1. Der Inhaber startet den Flow, indem er über einen Browser oder eine native Anwendung auf das Web-Front-End des Ausstellers zugreift. Dort wird der Benutzer von der Website des Ausstellers aufgefordert, Daten zu hinterlegen. Auf der Website wird zudem eine ausstellerspezifische Logik ausgeführt, um festzustellen, ob die Anmeldeinformationen ausgestellt werden können, und um den Inhalt zu überprüfen. 

1. Vom Web-Front-End des Ausstellers wird der Azure AD-Nachweisdienst aufgerufen, um eine Anforderung zum Ausstellen eines Nachweises zu generieren. 

1. Vom Web-Front-End wird ein Link zur Anforderung je nach Gerät in Form eines QR-Codes oder in Form eines gerätespezifischen Deep Links angezeigt.

1. Der QR-Code oder Deep Link aus Schritt 3 wird vom Inhaber mithilfe einer Wallet-App wie Microsoft Authenticator gescannt.

1. Die Anforderung wird von der Wallet-App über den Link heruntergeladen. Anforderung enthält Folgendes:

   * Den dezentralen Bezeichner des Ausstellers. Dieser wird von der Wallet-App zur Auflösung im Identity Overlay Network für die Suche nach den öffentlichen Schlüsseln und verknüpften Domänen verwendet.

   * Die URL mit den Nachweismanifest, das die Vertragsanforderungen für die Ausstellung des Nachweises angibt. Dazu können „id_token“, selbst bestätigte Attribute, die bereitgestellt werden müssen, oder die Vorlage anderer Nachweise gehören. 

   * Das Erscheinungsbild des Nachweises (URL der Logodatei, Farben usw.).

1. Mit der Wallet-Anwendung werden die Ausstellungsanforderungen überprüft und die Vertragsanforderungen verarbeitet:

   1. Damit wird überprüft, ob die Nachricht mit der Ausstellungsanforderung mit den Schlüsseln des Ausstellers signiert ist, die im DID-Dokument gefunden wurden, das im Identity Overlay Network aufgelöst wurde. Dadurch wird sichergestellt, dass die Nachricht nicht manipuliert wurde.

   1. Damit wird überprüft, ob die DNS-Domäne, auf die im DID-Dokument des Ausstellers verwiesen wird, im Besitz des Ausstellers ist. 

   1. Je nach den Anforderungen des Vertrags über den Nachweis kann die Wallet-Anwendung zur Ausstellung eines „id_token“ vom Inhaber verlangen, dass er zusätzliche Informationen bereitstellt und beispielsweise selbst ausgestellte Attribute anfordern oder die Navigation durch einen OIDC-Flow verlangen.

1. Übermittelt die für den Vertrag erforderlichen Artefakte an den Azure AD-Nachweisdienst. Der Azure AD-Nachweisdienst gibt den Nachweis zurück, der mit dem DID-Schlüssel des Ausstellers signiert wurde, und die Wallet-Anwendung speichert den Nachweis sicher.

Ausführlichere Informationen zum Erstellen einer Ausstellungslösung sowie zu Aspekten der Architektur finden Sie unter [Planen einer Azure Active Directory-Lösung für die Ausstellung von Nachweisen](plan-issuance-solution.md).

### <a name="flow-2-verifiable-credential-presentation"></a>Flow 2: Vorlage von Nachweisen

![Vorlage von Nachweisen](media/introduction-to-verifiable-credentials-architecture/presentation.png)

In diesem Flow interagiert ein Inhaber mit einer vertrauenden Seite, um einen Nachweis als Teil der Autorisierungsanforderungen vorzulegen.

1. Der Inhaber startet den Flow, indem er über einen Browser oder eine native Anwendung auf das Web-Front-End der vertrauenden Seite zugreift.

1. Vom Web-Front-End wird der Azure AD-Nachweisdienst aufgerufen, um eine Anforderung zum Vorlegen eines Nachweises zu generieren.

1. Vom Web-Front-End wird ein Link zur Anforderung je nach Gerät in Form eines QR-Codes oder in Form eines gerätespezifischen Deep Links angezeigt.

1. Der QR-Code oder Deep Link aus Schritt 3 wird vom Inhaber mithilfe einer Wallet-App wie Microsoft Authenticator gescannt.

1. Die Anforderung wird von der Wallet-App über den Link heruntergeladen. Anforderung enthält Folgendes:

   * eine [auf Standards basierende Anforderung von Anmeldeinformationen](https://identity.foundation/presentation-exchange/) eines Schemas oder eines Anmeldeinformationstyps 

   * den dezentralen Bezeichner der vertrauenden Seite, nach dem von der Wallet-Anwendung im Identity Overlay Network gesucht wird


1. Mit der Wallet-Anwendung wird die Vorlageanforderung überprüft und nach gespeicherten Nachweisen gesucht, die der Anforderung entsprechen. Je nach den erforderlichen Nachweisen wird der Antragsteller von der Wallet-Anwendung aufgefordert, Nachweise auszuwählen und der Verwendung von Nachweisen zuzustimmen.

   * Nachdem der Antragsteller der Verwendung von Nachweisen zugestimmt hat, wird von der Wallet-Anwendung ein eindeutiger paarweiser dezentraler Bezeichner zwischen dem Antragsteller und der vertrauenden Seite erstellt. 

   Anschließend wird von der Wallet-Anwendung eine vom Antragsteller signierte Vorlageantwort-Payload an den Azure AD-Nachweisdienst gesendet. Sie enthält folgende Elemente: 

   * Die Nachweise, denen der Antragsteller zugestimmt hat

   * Den paarweisen dezentralen Bezeichner, der als „Antragsteller“ der Payload generiert wurde

   * Den dezentralen Bezeichner der vertrauenden Seite als „Zielgruppe“ der Payload

1. Die von der Wallet-Anwendung gesendete Antwort wird vom Azure AD-Nachweisdienst überprüft. Je nachdem, wie die ursprüngliche Vorlageanforderung in Schritt 2 erstellt wurde, kann diese Überprüfung beinhalten, dass beispielsweise bei einer Sperrung der Status des beim Nachweisaussteller vorgelegten Nachweises überprüft wird.

1. Nach der Überprüfung wird die vertrauende Seite vom Azure AD-Nachweisdienst mit dem Ergebnis zurückgerufen. 

Ausführlichere Informationen zum Erstellen einer Überprüfungslösung sowie zu Aspekten der Architektur finden Sie unter [Planen einer Azure Active Directory-Lösung für die Überprüfung von Nachweisen](plan-verification-solution.md).

## <a name="key-takeaways"></a>Wesentliche Punkte

Mithilfe dezentraler Architekturen lassen sich bestehende Lösungen verbessern und neue Funktionen bereitstellen. 

Zur Unterstützung der Bestrebungen der [Decentralized Identity Foundation](https://identity.foundation/) (DIF) und der W3C-[Designziele](https://www.w3.org/TR/did-core/) sollten beim Erstellen einer Lösung für Nachweise folgende Aspekte berücksichtigt werden:

* Zwischen Akteuren im System gibt es keine zentralen Vertrauensstellungspunkte. Das bedeutet, dass die Vertrauensstellungsgrenzen durch einen Verbund nicht erweitert werden, da die Akteure nur bestimmten Nachweisen vertrauen.

   * Über das Identity Overlay Network kann der dezentrale Bezeichner aller Akteure ermittelt werden.

   * Mit der Lösung können Überprüfer alle Nachweise aller Aussteller überprüfen.

   * Die Autorisierung des Antragstellers oder des Überprüfers (der vertrauenden Seite) kann der Aussteller mit der Lösung jedoch nicht steuern.

* Die Akteure arbeiten entkoppelt und sind jeweils in der Lage, die Aufgaben ihrer Rollen zu erfüllen.

   * Aussteller sind für alle Nachweisanforderungen verantwortlich und machen keinen Unterschied bei den Anforderungen.

   * Antragsteller besitzen den Nachweis, sobald dieser ausgestellt wurde, und können diesen jedem Überprüfer vorlegen.

   * Überprüfer können alle Nachweise von allen Antragstellern oder Ausstellern überprüfen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Architektur von Nachweisen

* [Planen Ihrer Ausstellungslösung](plan-issuance-solution.md)

* [Planen Ihrer Überprüfungslösung](plan-verification-solution.md)

* [Erste Schritte mit Azure Active Directory-Nachweisen](get-started-verifiable-credentials.md)

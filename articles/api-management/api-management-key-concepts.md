---
title: Azure API Management-Übersicht und zentrale Konzepte | Microsoft-Dokumentation
description: Erfahren Sie mehr über APIs, Produkte, Rollen, Gruppen und andere API Management-Schlüsselkonzepte.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 9c0cef1c451183c2f5d31f086d2612bf47ab3f7b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658980"
---
# <a name="about-api-management"></a>Informationen zu API Management

Mit API Management (APIM) können Sie konsistente und moderne API-Gateways für vorhandene Back-End-Dienste erstellen.

API Management unterstützt Organisationen beim Veröffentlichen von APIs für externe Entwickler, Partnerentwickler und interne Entwickler, um das volle Potenzial von Daten und Diensten ausschöpfen zu können. Unternehmen sind weltweit dabei, ihren Betrieb als digitale Plattform zu erweitern, neue Kanäle zu erschließen, neue Kunden zu finden und die Bindung zu existierenden Kunden zu vertiefen. API Management bietet die Kernkompetenzen zur Sicherstellung eines erfolgreichen API-Programms in Form von Entwicklerengagement, geschäftlichen Erkenntnissen, Analysen, Sicherheit und Schutz. Mithilfe von Azure API Management können Sie ein API-Programm mit vollem Funktionsumfang auf einem beliebigen Back-End starten.

Dieser Artikel enthält eine Übersicht über allgemeine Szenarien mit APIM.  Außerdem finden Sie hier eine Kurzübersicht über die Hauptkomponenten des APIM-Systems. Im Anschluss werden die einzelnen Komponenten ausführlicher behandelt.

## <a name="overview"></a>Übersicht

Administratoren erstellen APIs, um API Management zu nutzen. Jede API besteht aus einer oder mehreren Operationen, und jede API kann zu einem oder mehreren Produkten hinzugefügt werden. Um eine API zu nutzen, abonnieren Entwickler ein Produkt, das diese API enthält. Anschließend können sie die Operationen der API aufrufen und unterliegen dabei allen geltenden Nutzungsrichtlinien. Zu den häufigen Szenarios gehören:

* **Schutz einer mobilen Infrastruktur** durch Einschränkung des Zugriffs mit API-Schlüsseln, das Verhindern von DOS-Angriffen über eine Drosselung oder das Verwenden erweiterter Sicherheitsrichtlinien wie z. B. der JWT-Tokenüberprüfung.
* **Bereitstellung eines ISV-Partnerökosystems** durch eine schnelle Partnerintegration über das Entwicklerportal und das Erstellen einer API-Fassade zum Entkoppeln interner Implementierungen, die noch nicht für die Partnernutzung geeignet sind.
* **Ausführung eines internen API-Programms** durch Bereitstellung einer zentralen Stelle innerhalb der Organisation für den Austausch über Verfügbarkeit und neueste Änderungen an APIs, Einschränkung des Zugriffs basierend auf Organisationskonten – all dies basierend auf einem sicheren Kanal zwischen API-Gateway und Back-End.

Das System setzt sich aus den folgenden Komponenten zusammen:

* Das **API-Gateway** ist der Endpunkt, der folgende Aufgaben übernimmt:
  
  * Akzeptieren von API-Aufrufen und Weiterleiten dieser Aufrufe an Ihre Back-Ends
  * Überprüfen von API-Schlüsseln, JWT-Tokens, Zertifikaten und anderen Anmeldeinformationen
  * Erzwingen von Nutzungskontingenten und Übertragungsratenlimits
  * Dynamisches Transformieren Ihrer API ohne Codeänderungen
  * Zwischenspeichern von Back-End-Antworten, falls eingerichtet
  * Protokollieren von Aufrufmetadaten zu Analysezwecken
* Das **Azure-Portal** ist die Verwaltungsoberfläche, über die Sie Ihr API-Programm einrichten. Verwenden Sie es zu folgenden Zwecken:
  
  * Definieren oder Importieren des API-Schemas
  * Paketieren von APIs in Produkten
  * Konfigurieren von Richtlinien wie z. B. Kontingenten oder Transformationen für die APIs
  * Gewinnen von Erkenntnissen mithilfe von Analysen
  * Verwalten von Benutzern
* Das **Entwicklerportal** dient als wichtigste Webpräsenz für Entwickler und ermöglicht Folgendes:
  
  * Lesen der API-Dokumentation
  * Ausprobieren einer API mithilfe der interaktiven Konsole
  * Erstellen eines Kontos und Anfordern von API-Schlüsseln
  * Zugriff auf Nutzungsanalysen

Weitere Informationen finden Sie im Whitepaper [Cloud-based API Management: Harnessing the Power of APIs](https://j.mp/ms-apim-whitepaper) (Cloudbasierte API-Verwaltung: Nutzung der Leistung von APIs). In diesem Whitepaper zu API Management von CITO Research werden folgende Themen behandelt: 
 
 * Allgemeine API-Anforderungen und -Herausforderungen
 * Entkoppeln von APIs und Präsentieren von Fassaden
 * Schnelle Einrichtung und Inbetriebnahme durch Entwickler
 * Sichern des Zugriffs
 * Analysen und Metriken
 * Kontrolle und Einblick mit einer API Management-Plattform
 * Verwenden von Cloudlösungen im Vergleich zu lokalen Lösungen
 * Azure API Management
 
## <a name="apis-and-operations"></a><a name="apis"> </a>APIs und Operationen
APIs sind die Grundlage einer API Management-Dienstinstanz. Jede API stellt Entwicklern einen Satz von Operationen zur Verfügung. Jede API enthält einen Verweis auf den Back-End-Dienst, der die API implementiert, und die Operationen der API sind den Operationen des Back-End-Diensts zugeordnet. Für Operationen in API Management ist eine umfangreiche Konfiguration möglich. Sie können die URL-Zuordnung, Abfrage- und Pfadparameter, Anforderungs- und Antwortinhalte sowie das Zwischenspeichern von Operationsantworten steuern. Durchsatzgrenzen, Kontingente und IP-Einschränkungen können ebenfalls auf API-Ebene oder für einzelne Operationen konfiguriert werden.

Weitere Informationen finden Sie unter [Erstellen von APIs][How to create APIs] und [Hinzufügen von Operationen zu einer API][How to add operations to an API].

## <a name="products"></a><a name="products"> </a> Produkte
APIs werden in Form von Produkten an Entwickler bereitgestellt. Produkte in API Management enthalten eine oder mehrere APIs und werden mit einem Titel, einer Beschreibung und Nutzungsbedingungen konfiguriert. Produkte können **Offen** oder **Geschützt** sein. Geschützte Produkte müssen abonniert werden, bevor Sie verwendet werden können, während offene Produkte ohne Abonnement genutzt werden können. Wenn ein Produkt bereit für die Nutzung durch Entwickler ist, kann es veröffentlicht werden. Nach der Veröffentlichung kann das Produkt (bei geschützten Produkten nach dem Abonnieren) durch die Entwickler angezeigt werden. Die Genehmigung von Abonnements wird auf der Produktebene konfiguriert. Abonnements können entweder eine Genehmigung eines Administrators erfordern oder automatisch genehmigt werden.

Gruppen dienen zur Verwaltung der Sichtbarkeit von Produkten für Entwickler. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind. 

## <a name="groups"></a><a name="groups"> </a> Gruppen
Gruppen dienen zur Verwaltung der Sichtbarkeit von Produkten für Entwickler. API Management umfasst folgende unveränderliche Systemgruppen:

* **Administratoren** – Azure-Abonnementadministratoren sind Mitglieder dieser Gruppe. Administratoren verwalten API Management-Dienstinstanzen und erstellen die APIs, Operationen und Produkte, die von den Entwicklern verwendet werden.
* **Entwickler** – Zu dieser Gruppe gehören authentifizierte Benutzer des Entwicklerportals. Entwickler sind die Kunden, die Anwendungen unter Verwendung Ihrer APIs erstellen. Entwickler erhalten Zugriff zum Entwicklerportal und erstellen Anwendungen, die die Operationen einer API aufrufen.
* **Gäste** – Nicht authentifizierte Benutzer wie z. B. potenzielle Kunden, die das Entwicklerportal einer API Management-Instanz besuchen, fallen in diese Gruppe. Sie können diesen Benutzern schreibgeschützten Zugriff gewähren, z.B. um die APIs anzuzeigen, jedoch nicht aufrufen zu können.

Zusätzlich zu diesen Systemgruppen können Administratoren benutzerdefinierte Gruppen erstellen oder [externe Gruppen in zugeordneten Azure Active Directory-Mandanten verwenden](api-management-howto-aad.md). Benutzerdefinierte und externe Gruppen können gemeinsam mit Systemgruppen verwendet werden, um API-Produkte für Entwickler sichtbar zu machen und ihnen den Zugriff auf die API-Produkte zu ermöglichen. Beispielsweise können Sie eine benutzerdefinierte Gruppe für Entwickler eines spezifischen Partnerunternehmens erstellen und diesen Entwicklern Zugriff auf die APIs über ein Produkt erteilen, das nur die relevanten APIs enthält. Ein Benutzer kann Mitglied von mehr als einer Gruppe sein.

Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen][How to create and use groups].

## <a name="developers"></a><a name="developers"> </a> Entwickler
Entwickler stellen die Benutzerkonten in einer API Management-Dienstinstanz dar. Entwickler können von Administratoren erstellt oder eingeladen werden, oder sie können sich im [Entwicklerportal][Developer portal] anmelden. Jeder Entwickler ist Mitglied in einer oder mehreren Gruppen und kann die Produkte abonnieren, die für die entsprechenden Gruppen sichtbar sind.

Wenn Entwickler ein Produkt abonnieren, erhalten sie den primären und den sekundären Schlüssel für das Produkt. Diese Schlüssel kommen bei den Aufrufen an die APIs des Produkts zum Einsatz.

Weitere Informationen finden Sie unter [Erstellen und Einladen von Entwicklern][How to create or invite developers] und [Zuordnen von Entwicklern zu Gruppen][How to associate groups with developers].

## <a name="policies"></a><a name="policies"> </a> Richtlinien
Richtlinien sind ein praktisches Werkzeug in API Management, mit dem das Azure-Portal das Verhalten der API mittels Konfiguration verändern kann. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind etwa Formatumwandlungen von XML nach JSON und Aufrufratenlimits, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Darüber hinaus stehen noch viele weitere Richtlinien zur Verfügung.

Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung](./api-management-advanced-policies.md#choose) und [Variable festlegen](./api-management-advanced-policies.md#set-variable), basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien](./api-management-advanced-policies.md#AdvancedPolicies) und [Richtlinienausdrücke](./api-management-policy-expressions.md).


Eine vollständige Liste der Richtlinien für API Management finden Sie unter [Gruppenrichtlinienreferenz][Policy reference]. Weitere Informationen zur Verwendung und Konfiguration von Richtlinien finden Sie unter [Richtlinien für API Management][API Management policies]. Ein Tutorial zum Erstellen eines Produkts mit Richtlinien für Durchsatzgrenzen und Kontingente finden Sie unter [Erstellen und Konfigurieren erweiterter Produkteinstellungen][How to create and configure advanced product settings].


## <a name="developer-portal"></a><a name="developer-portal"> </a> Entwicklerportal
Im Entwicklerportal können Entwickler auf Ihre APIs zugreifen, Operationen anzeigen und aufrufen und Produkte abonnieren. Potenzielle Kunden können das Entwicklerportal besuchen, APIs und Operationen anzeigen und sich anmelden. Sie finden die URL Ihres Entwicklerportals im Dashboard des Azure-Portals für Ihre API Management-Dienstinstanz.

Sie können das Erscheinungsbild Ihres Entwicklerportals konfigurieren, indem Sie eigene Inhalte hinzufügen, Stilregeln anpassen und Ihr Markenbild einfügen.

## <a name="api-management-and-the-api-economy"></a>API Management und API-Ökonomie

Weitere Informationen zu API Management finden Sie in der folgenden Präsentation von der Microsoft Ignite 2017-Konferenz.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Nächste Schritte

Absolvieren Sie das folgende Schnellstarttutorial, und beginnen Sie mit der Verwendung von Azure API Management:

> [!div class="nextstepaction"]
> [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: ./import-and-publish.md
[How to add operations to an API]: ./mock-api-responses.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How to create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: ./api-management-policies.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md

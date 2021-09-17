---
title: Azure API Management Support für Monetarisierung
description: Lernen Sie, wie Azure API Management Monetarisierungsstrategien für Ihre API-Produkte unterstützt.
author: v-hhunter
ms.author: v-hhunter
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 88323145b9b6bb66babe77b19d6b834afc2831ab
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825267"
---
# <a name="how-api-management-supports-monetization"></a>Unterstützung der Monetarisierung durch API Management

Mit der [Azure API Management](./api-management-key-concepts.md) Service-Plattform können Sie:
* APIs veröffentlichen, die Ihre Kunden abonnieren.
* Das Risiko der Implementierung verringern. 
* Projektzeitpläne beschleunigen.
* Ihre APIs mit Vertrauen skalieren.

In diesem Dokument fokussieren wir uns auf API Management-Funktionen, die die Umsetzung Ihrer Monetarisierungsstrategie ermöglichen, wie z. B. die Bereitstellung einer reibungslosen Erfahrung beim:
* Entdecken Ihrer öffentlichen APIs.
* Eingeben von Zahlungsdetails.
* Aktivieren Ihres Abonnements.
* Nutzen der API.
* Überwachen der Nutzung.
* Automatischen Bezahlen für die Nutzung der API.

Im folgenden Diagramm werden diese wichtigen API Management Funktionen vorgestellt:

:::image type="content" source="media/monetization-support/architecture-overview.png" alt-text="Diagramm der wichtigsten Monetarisierungsfunktionen des API-Management":::

## <a name="api-discovery"></a>API-Entdeckung

Starten Sie Ihre API und binden Sie API-Kunden über das integrierte Entwicklerportal von API Management ein. Stellen Sie qualitativ hochwertige Entwicklungsinhalte für das Entwicklerportal heraus, damit API-Kunden Ihre APIs nahtlos erkunden und nutzen können. Testen Sie die bereitgestellten Inhalte und Informationen auf Zugänglichkeit, Gründlichkeit und Benutzerfreundlichkeit.

Einzelheiten zum Hinzufügen von Inhalten und zur Steuerung des Brandings des Entwicklerportals finden Sie in der H Übersicht über das [Entwicklerportal](./api-management-howto-developer-portal.md).

## <a name="api-packaging"></a>API-Verpackung

Das API-Management verwaltet, wie Ihre APIs verpackt und präsentiert werden, indem sie das Konzept der *Produkte* und *Richtlinien* verwendet.

### <a name="products"></a>Produkte

APIs werden veröffentlicht [via-Produkte](./api-management-howto-add-products.md). Mit Produkten können Sie definieren:
* Welche APIs ein Abonnent nutzen kann.
* Spezifische Einschränkungs[richtlinien](./api-management-howto-policies.md) wie die Begrenzung eines bestimmten Abonnements auf ein Kontingent von Anrufen pro Monat.

Wenn ein API-Kunde ein Produkt abonniert, erhält er einen API-Schlüssel, mit dem er Anrufe tätigen kann. Anfänglich ist das Abonnement auf den Zustand `submitted` eingestellt. Aktivieren Sie das Abonnement, damit die Abonnenten die APIs nutzen können.

Konfigurieren Sie die API-Managementprodukte so, dass sie die zugrunde liegende API so verpacken, dass sie Ihr Ertragsmodell widerspiegelt mit:
* Einer Eins-zu-Eins-Beziehung zwischen jeder Ebene in Ihrem Ertragsmodell.
* Ein entsprechendes API-Managementprodukt.

In Beispielprojekten werden API-Managementprodukte als Top-Level-Mittel zur Kodifizierung der Monetarisierungsstrategie verwendet. Die API Management Produkte spiegeln die Ertragsmodelltarife wider und indizieren das jeweilige Preismodell für jeden Tarif. Dieses Setup bietet ein flexibles, konfigurationsangetriebenes Vorgehen zur Vorbereitung der Monetarisierungsstrategie.

### <a name="policies"></a>Richtlinien

Wenden Sie API Management Richtlinien an, um die Dienstqualität für jedes Produkt zu steuern. Beispielprojekte verwenden zwei spezifische Richtlinienfunktionen, um die Dienstqualität entsprechend dem Ertragsmodell zu steuern:

| Richtlinienfunktion | BESCHREIBUNG |
| ----- | ----- |
| **Kontingent** | Definiert die Gesamtzahl der Aufrufe, die der Benutzer über einen bestimmten Zeitraum an die API vornehmen kann. Zum Beispiel: „100 Aufrufe pro Monat“. .Sobald der Benutzer das Kontingent erreicht hat, versagen die Aufrufe an die API, und der Aufrufer erhält einen `403 Forbidden`-Antwortstatuscode. |
| **Rate Limit** | Definiert die Anzahl der Aufrufe über ein gleitendes Zeitfenster, die an die API erfolgen können, über ein gleitendes Zeitfenster. Zum Beispiel: "200 Aufrufe pro Monat". Entwickelt, um Spitzen in der API-Nutzung zu verhindern, die über die bezahlte Dienstqualität des gewählten Produkts hinausgehen. Wenn die Aufrufrate überschritten wird, empfängt der Aufrufer einen `429 Too Many Requests`-Antwortstatuscode. |

Weitere Einzelheiten zu Richtlinien finden Sie in der Dokumentation [Policies in Azure API Management](./api-management-howto-policies.md).

## <a name="api-consumption"></a>API-Verbrauch

Gewähren Sie API-Consumern Zugang zu Ihren APIs über Produkte mit API-Abonnements.

1. API-Consumer richten API-Abonnements ein, wenn sie sich für ein bestimmtes API-Management-Produkt anmelden. 
1. Integrieren Sie den Abonnementprozess mit dem Zahlungsanbieter mithilfe der API-Management-Delegierung. 
1. Nach erfolgreicher Übermittlung der Zahlungsdetails erhalten die Benutzer Zugriff auf die API mit einem generierten, eindeutigen Sicherheitsschlüssel für das Abonnement.

Weitere Informationen zu Abonnements finden Sie in der Dokumentation [Abonnements in Azure API Management](./api-management-subscriptions.md).

## <a name="api-usage-monitoring"></a>Überwachung der API-Nutzung

Gewinnen Sie Einblicke in Ihre API-Nutzung und -Leistung mit den integrierten Analysen von API Management. Diese Analysen liefern Berichte nach:
* API
* Geografie
* API-Vorgänge
* Produkt
* Anforderung
* Subscription
* Time 
* Benutzer

Überprüfen Sie die Analyseberichte regelmäßig, um zu verstehen, wie Ihre Monetarisierungsstrategie von API-Consumern übernommen wird.

Weitere Informationen finden Sie unter [Erhalten von API Analysen in Azure API Management](./howto-use-analytics.md).

## <a name="security"></a>Sicherheit

Kontrollieren Sie die Zugriffsebene für jeden Benutzer für jedes Produkt mithilfe der Produkte von API Management, der API-Richtlinien und der Abonnements. Verhindern Sie Falschnutzung und Missbrauch, indem Sie API-Zugang auf Abonnementebene gewähren, wenn sich der Nutzer erfolgreich beim Zahlungsanbieter authentifiziert hat, auch wenn das jeweilige API-Produkt kostenlos ist.

## <a name="integration"></a>Integration

Erstellen Sie eine nahtlose Monetarisierungserfahrung durch Front-End- und Back-End-Integration zwischen API Management und dem von Ihnen gewählten Zahlungsanbieter.  Verwenden Sie API Management Delegierung für die Front-End-Integration und die REST-API für die Back-End-Integration.

### <a name="delegation"></a>Delegierung

In den Beispielprojekten können Sie [API Management Delegierung](./api-management-howto-setup-delegation.md) verwenden, um benutzerdefinierte Integrationen mit den Zahlungsanbietern von Drittanbietern vorzunehmen. Die Demo setzt die Delegierung sowohl für die Registrierung/Anmeldung als auch für das Produktabonnement ein.

#### <a name="sign-upsign-in-workflow"></a>Workflow von Registrierung/Anmeldung

1. Ein Entwickler klickt auf den Anmeldungs- oder Registrierungslink im API Management-Entwicklerportal.
1. Der Browser leitet an den Delegierungsendpunkt um (konfiguriert auf einer Seite in der benutzerdefinierten Abrechnungsportal-App).
1. Die App für das benutzerdefinierte Abrechnungsportal zeigt eine Benutzeroberfläche zur Anmeldung/Registrierung an.
1. Nach erfolgreicher Anmeldung/Registrierung wird der Benutzer authentifiziert und zurück zur Startseite vom API Management Entwicklerportals umgeleitet.

#### <a name="product-subscription-workflow"></a>Workflow von Produktabonnements

1. Ein Entwickler wählt ein Produkt im API Management-Entwicklerportal aus und klickt auf die Schaltfläche **Abonnieren**
1. Der Browser leitet an den Delegierungsendpunkt um (konfiguriert auf einer Seite in der benutzerdefinierten Abrechnungsportal-App).
1. Benutzerdefinierte Abrechnungsportal-App:
    * Zeigt eine Benutzeroberfläche an, die auf der Grundlage des Zahlungsanbieters (Stripe oder Adyen) konfiguriert ist.
    * Führt den Benutzer durch den relevanten Check-Out-Vorgang.
1. Der Benutzer wird zurück zur Startseite „API Management Produkt“ umgeleitet. 
    * Das Produkt ist aktiv, und die API-Schlüssel sind verfügbar.

### <a name="rest-api"></a>REST-API

Verwenden Sie die REST-API für API Management, um den Betrieb Ihrer Monetarisierungsstrategie zu automatisieren.

Die Beispielprojekte verwenden die API zur Programmsteuerung von:

- Abrufen von API Management Produkten und Richtlinien, um eine synchronisierte Konfiguration ähnlicher Konzepte bei Zahlungsanbietern wie Stripe zu ermöglichen.
- Regelmäßiges Befragen von API Management, um API-Nutzungsmetriken für jedes Abonnement abzurufen und den Abrechnungsprozess zu betreiben.

Weitere Informationen finden Sie unter der[REST API Azure API Management](https://docs.microsoft.com/rest/api/apimanagement/#rest-operation-groups) Übersicht.

## <a name="devops"></a>DevOps

Versionskontrolle und Automatisieren von Bereitstellungsänderungen an API Management mithilfe von Azure Resource Manager, einschließlich der Konfiguration von Funktionen, die Ihre Monetarisierungsstrategie implementieren, z. B.:
* Produkte
* Richtlinien
* Entwicklerportal

In Beispielprojekten werden die Azure Resource Manager Skripts mit einer JSON-Datei erweitert, die das Preismodell jedes API Management Produkts definiert. Mit dieser Erweiterung können Sie die Konfiguration zwischen API Management und dem von Ihnen gewählten Zahlungsanbieter synchronisieren. Die gesamte Lösung wird in einem einzigen Quellcodeverwaltungs-Repository verwaltet, zum:
* Koordinieren von allen Änderungen im Zusammenhang mit der laufenden Weiterentwicklung der Monetarisierungsstrategie als einzelnes Release.
* Ausführen von Änderungen unter Befolgung von Anforderungen von Governance und Überprüfung.

## <a name="initialization-and-deployment"></a>Initialisierung und Bereitstellung

API Management kann bereitgestellt werden entweder über:
* Die Azure-Portal Benutzeroberfläche oder
* Ein "Infrastruktur als Code"-Vorgehen mit [Azure Resource Manager-Vorlagen](https://azure.microsoft.com/services/arm-templates). 

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über API Management Monetarisierungsstrategien](monetization-overview.md).
* Stellen Sie eine Demo-Integration von Adyen oder Stripe über das zugehörige [Git-Repository](https://github.com/microsoft/azure-api-management-monetization) bereit.

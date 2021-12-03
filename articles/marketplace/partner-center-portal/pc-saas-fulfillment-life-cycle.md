---
title: Verwalten des Lebenszyklus von SaaS-Abonnements | Kommerzieller Microsoft-Marketplace
description: Erfahren Sie, wie Sie den Lebenszyklus von SaaS-Abonnements mithilfe der Fulfillment-APIs der Version 2 verwalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: dfe47c220cb0dc427d9ff00bdfbaa85da949f520
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063968"
---
# <a name="managing-the-saas-subscription-life-cycle"></a>Verwalten des Lebenszyklus von SaaS-Abonnements

Im kommerziellen Marketplace wird der gesamte Lebenszyklus eines SaaS-Abonnements nach Erwerb durch den Endkunden verwaltet. Die Angebotsseite, die Fulfillment- und Vorgangs-APIs sowie der Webhook dienen als Mechanismen zur tatsächlichen Aktivierung, Nutzung, Aktualisierung und Kündigung des SaaS-Abonnements. Die Rechnung des Endbenutzers basiert auf dem Zustand des von Microsoft verwalteten SaaS-Abonnements.

## <a name="states-of-a-saas-subscription"></a>Zustände eines SaaS-Abonnements

Das folgende Diagramm zeigt die Zustände eines SaaS-Abonnements und die zugehörigen Aktionen.

[ ![Diagramm des Lebenszyklus eines Software-as-a-Service-Abonnements im Marketplace](./media/saas-subscription-lifecycle-api-v2.png) ](./media/saas-subscription-lifecycle-api-v2.png#lightbox)

### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Gekauft, aber noch nicht aktiviert (*PendingFulfillmentStart*)

Nachdem ein Endbenutzer oder CSP (Cloud Solution Provider) ein SaaS-Angebot im kommerziellen Marketplace gekauft hat, wird der Herausgeber über den Kauf benachrichtigt. Der Herausgeber kann dann auf seiner Seite ein neues SaaS-Konto für den Endbenutzer erstellen und konfigurieren.

Ablauf für die Kontoerstellung:

1. Der Kunde wählt die Schaltfläche **Konto jetzt konfigurieren** aus, die nach erfolgreichem Kauf in Microsoft AppSource oder im Azure-Portal für ein SaaS-Angebot gezeigt wird. Alternativ kann der Kunde die Schaltfläche **Jetzt konfigurieren** in der E-Mail verwenden, die er kurz nach dem Kauf erhält.
2. Microsoft benachrichtigt dann den Partner über den Kauf, indem die URL der Angebotsseite mit dem Tokenparameter (dem Identifizierungstoken für den Kauf im kommerziellen Marketplace) auf der neuen Browserregisterkarte geöffnet wird.

Ein Beispiel eines solchen Aufrufs ist `https://contoso.com/signup?token=<blob>`, doch ist die URL der Angebotsseite für dieses SaaS-Angebot im Partner Center als `https://contoso.com/signup` konfiguriert. Dieses Token stellt dem Herausgeber eine ID bereit, die den SaaS-Kauf und den Kunden eindeutig identifiziert.

[!INCLUDE [pound-sign-note](../includes/pound-sign-note.md)]

> [!IMPORTANT]
> Die URL der Angebotsseite muss rund um die Uhr aktiv sein und jederzeit neue Aufrufe von Microsoft empfangen können. Wenn die Angebotsseite nicht mehr verfügbar ist, können sich Kunden nicht mehr für den SaaS-Dienst registrieren und mit dessen Nutzung beginnen.

Als Nächstes muss der Herausgeber das *-Token* an Microsoft zurückgeben, indem er die [SaaS-Auflösungs-API](pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription) aufruft und das Token als Wert des Headerparameters `x-ms-marketplace-token header` eingibt. Durch den Aufruf der Auflösungs-API wird das Token durch die Details des SaaS-Kaufs ersetzt, z. B. die eindeutige ID des Kaufs, die ID des erworbenen Angebots und die ID des erworbenen Plans.

Auf der Angebotsseite sollte der Kunde über einmaliges Anmelden (Single Sign-On, SSO) bei Azure Active Directory (Azure AD) beim neuen oder vorhandenen SaaS-Konto angemeldet sein.

>[!NOTE]
>Der Herausgeber wird erst dann über den SaaS-Kauf benachrichtigt, nachdem der Kunde den Konfigurationsprozess auf Microsoft-Seite eingeleitet hat.

Der Herausgeber muss SSO implementieren, um die von Microsoft für diesen Flow benötigte Benutzeroberfläche bereitzustellen. Stellen Sie sicher, dass Sie beim Konfigurieren von SSO eine mehrinstanzenfähige Azure AD-Anwendung verwenden und sowohl Geschäfts-, Schul- oder Unikonten als auch persönliche Microsoft-Konten zulassen. Diese Anforderung gilt nur für die Angebotsseite und Benutzer, die zum SaaS-Dienst umgeleitet werden, wenn sie bereits mit Microsoft-Anmeldeinformationen angemeldet sind. SSO ist nicht für alle Anmeldungen beim SaaS-Dienst erforderlich.

> [!NOTE]
>Wenn SSO anfordert, dass ein Administrator die Berechtigung für eine App erteilt, muss in der Beschreibung des Angebots im Partner Center angegeben sein, dass Zugriff auf Administratorebene erforderlich ist. Dadurch werden die [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#10003-authentication-options) eingehalten.

Nach der Anmeldung muss der Kunde die SaaS-Konfiguration auf Herausgeberseite vervollständigen. Anschließend muss der Herausgeber die [API zum Aktivieren des Abonnements](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) aufrufen, um ein Signal an Azure Marketplace zu senden, dass die Bereitstellung des SaaS-Kontos abgeschlossen ist.
Dadurch wird der Abrechnungszeitraum des Kunden gestartet. Wird die API zum Aktivieren des Abonnements nicht erfolgreich aufgerufen, wird dem Kunden der Kauf nicht in Rechnung gestellt.

![Diagramm mit den API-Aufrufen für ein Bereitstellungsszenario.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

### <a name="active-subscribed"></a>Aktiv (*Abonniert*)

*Aktiv (Abonniert)* ist der stabile Zustand eines bereitgestellten SaaS-Abonnements. Nachdem der Aufruf der [API zum Aktivieren des Abonnements](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) auf Microsoft-Seite verarbeitet wurde, wird das SaaS-Abonnement als *Abonniert* gekennzeichnet. Der Kunde kann nun den SaaS-Dienst auf Herausgeberseite verwenden, der ihm in Rechnung gestellt wird.

Wenn ein SaaS-Abonnement bereits aktiv ist, kann der Kunde im Azure-Portal oder Microsoft 365 Admin Center die Option zum **Verwalten der SaaS-Umgebung** auswählen. Diese Aktion bewirkt auch, dass Microsoft die **URL der Angebotsseite** mit dem *Tokenparameter* wie beim Flow „Aktivieren“ aufruft. Der Herausgeber muss zwischen neuen Käufen und der Verwaltung vorhandener SaaS-Konten unterscheiden und diesen Aufruf der URL der Angebotsseite entsprechend handhaben.

### <a name="being-updated-subscribed"></a>Wird aktualisiert (*Abonniert*)

Diese Aktion bedeutet, dass eine Aktualisierung eines vorhandenen aktiven SaaS-Abonnements sowohl von Microsoft als auch vom Herausgeber verarbeitet wird. Eine solche Aktualisierung kann auf folgende Arten initiiert werden:

- Vom Kunden über den kommerziellen Marketplace
- Vom Cloud-Dienstanbieter über den kommerziellen Marketplace
- Vom Kunden über die SaaS-Website des Herausgebers (gilt nicht für Käufe des Cloud-Dienstanbieters)

Für ein SaaS-Abonnement sind zwei Arten von Aktualisierungen verfügbar:

- Aktualisierung des Plans, wenn der Kunde einen anderen Plan für das Abonnement auswählt
- Aktualisierung der Menge, wenn der Kunde die Anzahl erworbener Arbeitsplätze für das Abonnement ändert

Nur ein aktives Abonnement kann aktualisiert werden. Während das Abonnement aktualisiert wird, lautet der Status auf Microsoft-Seite weiterhin „Aktiv“.

#### <a name="update-initiated-from-the-commercial-marketplace"></a>Über den kommerziellen Marketplace eingeleitetes Update

Bei diesem Flow ändert der Kunde den Abonnementplan oder die Anzahl von Arbeitsplätzen über das Azure-Portal oder das Microsoft 365 Admin Center.

1. Nachdem eine Aktualisierung eingegeben wurde, ruft Microsoft die Webhook-URL des Herausgebers, die im Feld **Verbindungswebhook** auf der Seite _Technische Konfiguration_ im Partner Center konfiguriert ist, mit einem geeigneten Wert für *action* und anderen relevanten Parametern auf.
1. Auf Herausgeberseite müssen die erforderlichen Änderungen am SaaS-Dienst vorgenommen werden. Microsoft muss nach Abschluss des Vorgangs durch Aufrufen der [API zum Aktualisieren des Vorgangsstatus](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) benachrichtigt werden.
1. Wenn der Patch mit dem Status *Fehler* gesendet wird, wird der Aktualisierungsvorgang auf Microsoft-Seite nicht abgeschlossen. Das SaaS-Abonnement weist weiterhin den vorhandenen Plan und die vorhandene Menge von Arbeitsplätzen auf.

> [!NOTE]
> Der Herausgeber muss *innerhalb eines zehnsekündigen Zeitfensters* nach Empfang der Webhookbenachrichtigung PATCH für die [API zum Aktualisieren des Vorgangsstatus](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) mit der Antwort „Fehler/Erfolg“ aufrufen. Wird „PATCH“ für den Vorgangsstatus nicht innerhalb von zehn Sekunden empfangen, wird der Änderungsplan *automatisch als Erfolg gepatcht*.

Die Abfolge der API-Aufrufe für ein Aktualisierungsszenario, das über den kommerziellen Marketplace eingeleitet wird, ist in der folgenden Abbildung dargestellt.

![Diagramm der API-Aufrufe für eine über den Marketplace eingeleitete Aktualisierung.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

#### <a name="update-initiated-from-the-publisher"></a>Über den Herausgeber initiierte Aktualisierung

Bei diesem Ablauf ändert der Kunde den Abonnementplan oder die Menge der erworbenen Arbeitsplätze über den SaaS-Dienst selbst. 

1. Bevor die angeforderte Änderung auf Herausgeberseite vorgenommen wird, muss der Herausgebercode die [Planänderungs-API](pc-saas-fulfillment-subscription-api.md#change-the-plan-on-the-subscription) oder die [Mengenänderungs-API](pc-saas-fulfillment-subscription-api.md#change-the-quantity-of-seats-on-the-saas-subscription) oder beide aufrufen.

1. Microsoft wendet die Änderung auf das Abonnement an und fordert dann den Herausgeber über den **Verbindungswebhook** auf, die gleiche Änderung vorzunehmen.

1. Erst dann muss der Herausgeber die erforderliche Änderung am SaaS-Abonnement durchführen und Microsoft durch Aufrufen der [API zum Aktualisieren des Vorgangsstatus](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) benachrichtigen, sobald die Änderung erfolgt ist.

Die Abfolge der API-Aufrufe für ein Aktualisierungsszenario, das auf Herausgeberseite eingeleitet wird, ist in der folgenden Abbildung dargestellt.

![Diagramm der API-Aufrufe für eine auf Herausgeberseite eingeleitete Aktualisierung.](./media/saas-update-status-api-v2-calls-publisher-side.png)

### <a name="suspended-suspended"></a>Ausgesetzt (*Suspended*)

Dieser Status gibt an, dass die Zahlung eines Kunden für den SaaS-Dienst nicht eingegangen ist. Microsoft benachrichtigt den Herausgeber über diese Änderung im Status des SaaS-Abonnements. Die Benachrichtigung erfolgt über einen Aufruf des Webhooks, wobei der Parameter *action* auf *Suspended* festgelegt ist.

Der Herausgeber kann auf Wunsch Änderungen am SaaS-Dienst auf Herausgeberseite vornehmen. Es wird empfohlen, dass der Herausgeber diese Informationen dem jeweiligen Kunden bereitstellt und den Zugriff des Kunden auf den SaaS-Dienst einschränkt oder blockiert. Es besteht die Möglichkeit, dass die Zahlung niemals eingeht.

> [!NOTE]
> Microsoft gewährt dem Kunden eine Karenzzeit von 30 Tagen, bevor das Abonnement automatisch gekündigt wird. Nach Ablauf der 30-tägigen Toleranzperiode erhält der Webhook eine Aktion vom Typ `Unsubscribe`.

Wenn sich ein Abonnement im Status *Suspended* (Ausgesetzt) befindet, ist Folgendes gegeben:

* Der Partner oder ISV muss das SaaS-Konto in einem wiederherstellbaren Zustand halten, damit die vollständige Funktionalität ohne Verlust von Daten oder Einstellungen wiederhergestellt werden kann.
* Der Partner oder ISV muss mit einer Anforderung zur Reaktivierung des Abonnements rechnen, wenn die Zahlung während der Toleranzperiode eingeht, oder mit einer Anforderung zur Aufhebung der Bereitstellung des Abonnements nach Ablauf der Toleranzperiode. Beide Anforderungen werden über den Webhookmechanismus gesendet.

Der Abonnementstatus wird auf Microsoft-Seite in „Ausgesetzt“ geändert, bevor der Herausgeber eine Aktion ausführt. Nur aktive Abonnements können ausgesetzt werden.

### <a name="reinstated-suspended"></a>Reaktiviert (*Suspended*)

Diese Aktion gibt an, dass das Zahlungsinstrument des Kunden wieder gültig geworden ist, eine Zahlung für das SaaS-Abonnement erfolgt ist und das Abonnement reaktiviert wurde. In diesem Fall: 

1. Microsoft ruft den Webhook mit einem *action*-Parameter auf, der auf den Wert *Reinstate* festgelegt ist.
1. Der Herausgeber stellt sicher, dass das Abonnement auf Herausgeberseite wieder voll funktionsfähig ist.
1. Der Herausgeber ruft die [Patchvorgang-API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) mit dem Status „Erfolgreich“ auf.
1. Der Reaktivierungsprozess ist erfolgreich, und dem Kunden wird das SaaS-Abonnement wieder in Rechnung gestellt. 

Wenn der Patch mit dem Status *Fail* (Fehler) gesendet wird, wird der Reaktivierungsprozess auf Microsoft-Seite nicht beendet, sodass das Abonnement *Suspended* (Ausgesetzt) bleibt.

Nur ein ausgesetztes Abonnement kann reaktiviert werden. Das ausgesetzte SaaS-Abonnement verbleibt im Zustand *Suspended* (Ausgesetzt), während der Dienst reaktiviert wird. Sobald dieser Vorgang abgeschlossen ist, lautet der Status des Abonnements wieder *Active* (Aktiv).

### <a name="renewed-subscribed"></a>Verlängert (*Subscribed*)

Am Ende der Abonnementlaufzeit von einem Monat oder einem Jahr wird das SaaS-Abonnement automatisch von Microsoft verlängert. Die Standardeinstellung für die automatische Verlängerung lautet für alle SaaS-Abonnements *TRUE*. Aktive SaaS-Abonnements werden weiter in regelmäßigen Abständen verlängert. Microsoft stellt rein informelle Webhookbenachrichtigungen für Erneuerungsereignisse bereit. Ein Kunde kann die automatische Verlängerung eines SaaS-Abonnements über das Microsoft 365-Verwaltungsportal deaktivieren. In diesem Fall wird das SaaS-Abonnement am Ende des aktuellen Abrechnungszeitraums automatisch gekündigt. Kunden können das SaaS-Abonnement auch jederzeit kündigen.

Nur aktive Abonnements werden automatisch verlängert. Abonnements bleiben während des Verlängerungsvorgangs und bei erfolgreicher automatischer Verlängerung aktiv. Nach der Verlängerung werden das Start- und Enddatum der Abonnementlaufzeit in die Datumsangaben der neuen Laufzeit geändert.

Wenn bei einer automatischen Verlängerung aufgrund eines Zahlungsproblems ein Fehler auftritt, wechselst das Abonnement in den Zustand *Suspended* (Ausgesetzt), worüber der Herausgeber informiert wird.

### <a name="canceled-unsubscribed"></a>Gekündigt (*Unsubscribed*)

Abonnements erreichen diesen Zustand entweder als Reaktion auf eine explizite Aktion des Kunden oder Cloud-Dienstanbieters oder durch Kündigung eines Abonnements über die Website des Herausgebers, das Azure-Portal oder das Microsoft 365 Admin Center. Ein Abonnement kann auch bei Nichtzahlung der fälligen Beträge implizit gekündigt werden, nachdem es 30 Tage den Status *Suspended* (Ausgesetzt) aufwies.

Nachdem der Herausgeber einen Webhookaufruf zur Kündigung empfangen hat, muss er die Kundendaten für die Wiederherstellung auf Anforderung mindestens sieben Tage aufbewahren. Erst dann können Kundendaten gelöscht werden.

Ein SaaS-Abonnement kann jederzeit während des Lebenszyklus gekündigt werden. Ein einmal gekündigtes Abonnement kann nicht reaktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

- [SaaS-Fulfillment-Abonnement-APIs der Version 2](pc-saas-fulfillment-subscription-api.md)
- [SaaS-Fulfillment-Vorgangs-APIs der Version 2](pc-saas-fulfillment-operations-api.md)

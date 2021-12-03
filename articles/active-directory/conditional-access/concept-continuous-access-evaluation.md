---
title: Fortlaufende Zugriffsevaluierung in Azure AD
description: Schnellere Reaktion auf Benutzerstatusänderungen mit der fortlaufenden Zugriffsevaluierung in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de2ed185b3a4421a06ac9b3c88df68821b61adf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307989"
---
# <a name="continuous-access-evaluation"></a>Fortlaufende Zugriffsevaluierung

Tokenablauf und -aktualisierung sind standardmäßige Mechanismen der Branche. Wenn eine Clientanwendung wie Outlook eine Verbindung mit einem Dienst wie Exchange Online herstellt, werden die API-Anforderungen mithilfe von OAuth 2.0-Zugriffstoken autorisiert. Standardmäßig sind diese Zugriffstoken eine Stunde lang gültig. Nach Ablauf dieser Zeit wird der Client zur Aktualisierung zurück an Azure AD geleitet. Die Aktualisierung bietet eine Möglichkeit, Richtlinien für den Benutzerzugriff neu auszuwerten. Möglicherweise soll das Token nicht aktualisiert werden, zum Beispiel aufgrund einer Richtlinie für bedingten Zugriff oder da der Benutzer im Verzeichnis deaktiviert wurde. 

Kunden haben Bedenken ausgedrückt hinsichtlich der Verzögerung zwischen der Änderung der Bedingungen für einen Benutzer und der Durchsetzung von Richtlinienänderungen. Azure AD hat mit dem "stumpfen Objekt"-Ansatz verkürzter Token-Lebensdauern experimentiert, aber festgestellt, dass sie die Benutzererfahrung und Zuverlässigkeit beeinträchtigen können, ohne Risiken zu beseitigen.

Eine rechtzeitige Reaktion auf Richtlinienverletzungen oder Sicherheitsprobleme erfordert eine "Konversation" zwischen dem Token-Aussteller (Azure AD) und der vertrauenden Seite (aufgeklärte Anwendung). Dieser bidirektionale Austausch bietet zwei wichtige Funktionen. Die vertrauende Partei kann sehen, wenn sich Eigenschaften ändern, wie z.B. der Standort im Netz, und dies dem Token-Aussteller mitteilen. Außerdem kann der Tokenaussteller der vertrauenden Seite veranlassen aufzuhören, die Token für einen bestimmten Benutzer zu respektieren, weil das Konto kompromittiert oder deaktiviert wurde oder andere Bedenken bestehen. Der Mechanismus für diesen Austausch ist die fortlaufende Zugriffsevaluierung (Continuous Access Evaluation, CAE). Ziel ist es, nahezu in Echtzeit zu reagieren, aber aufgrund der Ausbreitungszeit von Ereignissen kann eine Wartezeit von bis zu 15 Minuten beobachtet werden.

Die anfängliche Implementierung der fortlaufenden Zugriffsevaluierung konzentriert sich auf Exchange, Teams und SharePoint Online.

Informationen zum Vorbereiten Ihrer Anwendungen auf die Verwendung der fortlaufenden Zugriffsevaluierung finden Sie unter [Verwenden von APIs mit aktivierter fortlaufender Zugriffsevaluierung in Ihren Anwendungen](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Hauptvorteile

- Benutzerkündigung oder Kennwortänderung oder -zurücksetzung: Die Sperrung der Benutzersitzung wird nahezu in Echtzeit erzwungen.
- Änderung der Netzwerkadresse: Standortrichtlinien für den bedingten Zugriff werden nahezu in Echtzeit erzwungen.
- Der Export von Token auf einen Computer außerhalb eines vertrauenswürdigen Netzwerks kann mit Standortrichtlinien für den bedingten Zugriff verhindert werden.

## <a name="scenarios"></a>Szenarien 

Zwei Szenarien ermöglichen die fortlaufende Zugriffsevaluierung: die Auswertung kritischer Ereignisse und die Auswertung von Richtlinien für bedingten Zugriff.

### <a name="critical-event-evaluation"></a>Auswertung kritischer Ereignisse

Eine fortlaufende Zugriffsbewertung wird implementiert, indem Dienste wie Exchange Online, SharePoint Online und Teams in die Lage versetzt werden, kritische Azure AD-Ereignisse zu abonnieren. Diese Ereignisse können dann ausgewertet und nahezu in Echtzeit durchgesetzt werden. Die Auswertung kritischer Ereignisse ist nicht auf Richtlinien für bedingten Zugriff angewiesen und kann daher in jedem Mandanten genutzt werden. Derzeit werden folgende Ereignisse ausgewertet:

- Benutzerkonto wird gelöscht oder deaktiviert
- Kennwort für einen Benutzer wird geändert oder zurückgesetzt
- Mehrstufige Authentifizierung wird für den Benutzer aktiviert.
- Administrator sperrt explizit alle Aktualisierungstoken für einen Benutzer.
- Azure AD Identity Protection hat ein hohes Benutzerrisiko erkannt.

Durch diesen Prozess wird ein Szenario ermöglicht, bei dem Benutzer innerhalb von wenigen Minuten nach einem dieser kritischen Ereignisse den Zugriff auf SharePoint Online-Dateien, E-Mails, Kalender, Aufgaben und Teams über Microsoft 365-Client-Apps verlieren. 

> [!NOTE] 
> Benutzerrisikoereignisse werden von Teams und SharePoint Online noch nicht unterstützt.

### <a name="conditional-access-policy-evaluation-preview"></a>Auswertung von Richtlinien für bedingten Zugriff (Vorschau)

Exchange Online, SharePoint Online, Teams und MS Graph können wichtige Richtlinien für den bedingten Zugriff zur Bewertung innerhalb des Dienstes selbst synchronisieren.

Dies ermöglicht ein Szenario, bei dem Benutzer unmittelbar nach Änderungen der Netzwerkadresse den Zugriff auf Dateien, E-Mails, Kalender oder Aufgaben der Organisation aus Microsoft 365-Client-Apps oder SharePoint Online verlieren.

> [!NOTE]
> Dabei werden nicht alle Kombinationen aus App- und Ressourcenanbietern unterstützt. Siehe dazu die folgende Tabelle. Office bezieht sich auf Word, Excel und PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **Exchange Online** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |

| | Office-Web-Apps | Office Win32-Apps | Office für iOS | Office für Android | Office für Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Nicht unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **Exchange Online** | Nicht unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |

| | OneDrive Web | OneDrive Win32 | OneDrive iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Unterstützt | Nicht unterstützt | Unterstützt | Unterstützt | Nicht unterstützt |

| | Teams (Webversion) | Teams (Win32) | Teams iOS | Teams Android | Teams Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Teams-Dienst** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **SharePoint Online** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **Exchange Online** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |

## <a name="client-capabilities"></a>Clientfunktionen

### <a name="client-side-claim-challenge"></a>Clientseitige Anspruchsaufforderung

Vor der fortlaufenden Zugriffsevaluierung haben die Clients das Zugriffstoken aus ihrem Cache wiedergegeben, solange es noch nicht abgelaufen war. Mit CAE führen wir einen neuen Fall ein, in dem ein Ressourcenanbieter ein Token zurückweisen kann, wenn es noch nicht abgelaufen ist. Um Clients darüber zu informieren, dass sie ihren Cache umgehen sollen, obwohl die zwischengespeicherten Token noch nicht abgelaufen sind, führen wir einen Mechanismus namens **Anspruchsüberprüfung** ein, um anzuzeigen, dass das Token abgelehnt wurde und ein neues Zugriffstoken von Azure AD ausgestellt werden muss. Für die fortlaufende Zugriffsevaluierung ist ein Client-Update erforderlich, damit der Client die Anspruchsaufforderung verstehen kann. Die aktuelle Version der folgenden Anwendungen unterstützt die Anspruchsaufforderung:

| | Web | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **Teams** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **Office** | Nicht unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |
| **OneDrive** | Unterstützt | Unterstützt | Unterstützt | Unterstützt | Unterstützt |

### <a name="token-lifetime"></a>Lebensdauer von Token

Weil Risiko und Richtlinie in Echtzeit bewertet werden, sind Clients, die Sitzungen mit kontinuierlicher Zugriffsevaluierung aushandeln, nicht mehr auf statische Richtlinien zur Token-Lebensdauer angewiesen. Diese Änderung bedeutet, dass die konfigurierbare Richtlinie für die Gültigkeitsdauer von Token für Clients, die CAE-Sitzungen aushandeln, nicht beachtet wird.

Die Gültigkeitsdauer von Token wird in CAE-Sitzungen auf bis zu 28 Stunden erhöht. Sperrungen werden durch kritische Ereignisse und Richtlinienauswertung, nicht einfach durch einen willkürlichen Zeitraum gesteuert. Diese Änderung erhöht die Stabilität von Anwendungen, ohne den Sicherheitsstatus zu beeinträchtigen. 

Wenn Sie keine CAE-fähigen Clients verwenden, bleibt die standardmäßige Gültigkeitsdauer des Zugriffstokens bei 1 Stunde. Die standardmäßige Gültigkeitsdauer ändert sich nur, wenn Sie die Gültigkeitsdauer Ihrer Zugriffstoken mit der Previewfunktion [Konfigurierbare Tokengültigkeitsdauer (CTL)](../develop/active-directory-configurable-token-lifetimes.md) konfiguriert haben.

## <a name="example-flow-diagrams"></a>Beispiele von Flowdiagrammen

### <a name="user-revocation-event-flow"></a>Ablauf bei Benutzersperrereignis

![Ablauf bei Benutzersperrereignis](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Ein CAE-fähiger Client stellt Anmeldeinformationen oder ein Aktualisierungstoken für Azure AD bereit und fordert ein Zugriffstoken für eine Ressource an.
1. Ein Zugriffstoken wird zusammen mit anderen Artefakten an den Client zurückgegeben.
1. Ein Administrator [sperrt explizit alle Aktualisierungstoken für den Benutzer](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Von Azure AD wird ein Sperrereignis an den Ressourcenanbieter gesendet.
1. Dem Ressourcenanbieter wird ein Zugriffstoken präsentiert. Der Ressourcenanbieter wertet die Gültigkeit des Tokens aus und überprüft, ob für den Benutzer ein Sperrungsereignis vorliegt. Der Ressourcenanbieter verwendet diese Informationen, um den Zugriff auf die Ressource zu gewähren oder zu untersagen.
1. In diesem Fall verweigert der Ressourcenanbieter den Zugriff und sendet die Anspruchsaufforderung „401+“ an den Client zurück.
1. Der CAE-fähige Client versteht die Anspruchsaufforderung „401+“. Er umgeht die Caches, geht zurück zu Schritt 1 und sendet das Aktualisierungstoken zusammen mit der Anspruchsaufforderung zurück an Azure AD. Azure AD wertet dann alle Bedingungen erneut aus und fordert in diesem Fall den Benutzer auf, sich erneut zu authentifizieren.

### <a name="user-condition-change-flow-preview"></a>Ablauf der Benutzerkonditionsänderung (Vorschauversion)

Im folgenden Beispiel hat ein Administrator für den bedingten Zugriff eine standortbasierte Richtlinie für bedingten Zugriff so konfiguriert, dass der Zugriff nur über bestimmte IP-Adressbereiche zulässig ist:

![Ablauf der Ereignisse für Benutzerbedingungen](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Ein CAE-fähiger Client stellt Anmeldeinformationen oder ein Aktualisierungstoken für Azure AD bereit und fordert ein Zugriffstoken für eine Ressource an.
1. Azure AD wertet alle Richtlinien für bedingten Zugriff aus, um zu überprüfen, ob der Benutzer und der Client die Bedingungen erfüllen.
1. Ein Zugriffstoken wird zusammen mit anderen Artefakten an den Client zurückgegeben.
1. Der Benutzer wechselt zu einer IP-Adresse außerhalb eines zulässigen IP-Adressbereichs.
1. Der Client stellt ein Zugriffstoken für den Ressourcenanbieter außerhalb eines zulässigen IP-Adressbereichs bereit.
1. Der Ressourcenanbieter wertet die Gültigkeit des Tokens aus und überprüft die über Azure AD synchronisierte Standortrichtlinie.
1. In diesem Fall verweigert der Ressourcenanbieter den Zugriff und sendet die Anspruchsaufforderung „401+“ an den Client zurück. Der Client wird herausgefordert, da er nicht aus einem zulässigen IP-Adressbereich stammt.
1. Der CAE-fähige Client versteht die Anspruchsaufforderung „401+“. Er umgeht die Caches, geht zurück zu Schritt 1 und sendet das Aktualisierungstoken zusammen mit der Anspruchsaufforderung zurück an Azure AD. Azure AD wertet alle Bedingungen erneut aus, und der Zugriff wird in diesem Fall verweigert.

## <a name="enable-or-disable-cae-preview"></a>Aktivieren oder Deaktivieren der fortlaufenden Zugriffsevaluierung (Vorschau)

Die CAE-Einstellung wurde in den Bereich „Bedingter Zugriff“ verschoben. Neue CAE-Kunden können beim Erstellen von Richtlinien für bedingten Zugriff direkt auf CAE zugreifen und diese umschalten. Einige Bestandskunden müssen jedoch die Migration durchlaufen, bevor sie mit dem Zugriff auf CAE über bedingten Zugriff beginnen können.

#### <a name="migration"></a>Migration

Kunden, die zuvor CAE-Einstellungen unter Sicherheit konfiguriert haben, müssen diese Einstellungen in eine neue Richtlinie für bedingten Zugriff migrieren. Führen Sie die folgenden Schritte aus, um Ihre CAE-Einstellungen in eine Richtlinie für bedingten Zugriff zu migrieren.

:::image type="content" source="media/concept-continuous-access-evaluation/migrate-continuous-access-evaluation.png" alt-text="Portalansicht mit der Option zum Migrieren der fortlaufenden Zugriffsauswertung zu einer Richtlinie für bedingten Zugriff." lightbox="media/concept-continuous-access-evaluation/migrate-continuous-access-evaluation.png":::

1. Melden Sie sich als Administrator für bedingten Zugriff, Sicherheitsadministrator oder globaler Administrator beim **Azure-Portal** an. 
1.  Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Fortlaufende Zugriffsevaluierung (Vorschauversion)** . 
1.  Es wird die Option **Migrieren** der Richtlinie angezeigt. Diese Aktion ist die einzige Aktion, auf die Sie zu diesem Zeitpunkt zugreifen können.
1. Navigieren Sie zu **Bedingter Zugriff**. Sie sehen dort eine neue Richtlinie namens **ZS-Richtlinie aus CAE-Einstellungen**, die mit Ihren Einstellungen konfiguriert ist. Administratoren können diese Richtlinie anpassen oder eine eigene Richtlinie erstellen, um sie zu ersetzen.

Weitere Informationen zur fortlaufenden Zugriffsauswertung als Sitzungssteuerung finden Sie im Abschnitt [Anpassen der fortlaufenden Zugriffsauswertung](concept-conditional-access-session.md#customize-continuous-access-evaluation).

### <a name="strict-enforcement"></a>Strict Enforcement (Strenge Erzwingung) 

Bei der aktuellsten CAE-Einstellung unter „Bedingter Zugriff“ ist Strict Enforcement (Strenge Erzwingung) ein neues Feature, das eine erhöhte Sicherheit auf der Grundlage von zwei Faktoren ermöglicht: IP-Adressvariation und Clientfunktion. Diese Funktion kann beim Anpassen von CAE-Optionen für eine bestimmte Richtlinie aktiviert werden. Durch Aktivieren von Strict Enforcement (Strenge Erzwingung) widerruft CAE den Zugriff, wenn Instanzen von [IP-Adressvariationen](#ip-address-variation) oder fehlende [CAE-Clientfunktionen](#client-capabilities)erkannt werden.

> [!NOTE] 
> Sie sollten die strikte Durchsetzung erst aktivieren, nachdem Sie sichergestellt haben, dass alle Clientanwendungen CAE unterstützen und Sie alle IP-Adressen, die von Azure AD und den Ressourcenanbietern wie Exchange online und Azure Resource Mananger gesehen werden, in Ihre Standortrichtlinie unter Conditional Access aufgenommen haben. Andernfalls könnten Sie gesperrt werden.

## <a name="limitations"></a>Einschränkungen

### <a name="group-membership-and-policy-update-effective-time"></a>Zeitraum bis zur Wirksamkeit einer Aktualisierung von Gruppenmitgliedschaft und Richtlinien

Es kann bis zu einem Tag dauern, bis von Administratoren vorgenommene Änderungen an Richtlinien für bedingten Zugriff und Gruppenmitgliedschaften wirksam werden. Die Verzögerung ist auf die Replikation zwischen Azure AD und Ressourcenanbietern wie Exchange Online und SharePoint Online zurückzuführen. Es wurden einige Optimierungen für Richtlinienaktualisierungen vorgenommen, die die Verzögerung auf zwei Stunden reduzieren. Allerdings sind damit noch nicht alle Szenarien abgedeckt.  

Wenn Änderungen an der Richtlinie für bedingten Zugriff oder der Gruppenmitgliedschaft sofort auf bestimmte Benutzer angewendet werden müssen, haben Sie zwei Möglichkeiten. 

- Führen Sie den [PowerShell-Befehl revoke-azureaduserallrefreshtoken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) aus, um alle Aktualisierungs-Tokens eines bestimmten Benutzers zu widerrufen.
- Wählen Sie "Sitzung widerrufen" auf der Benutzerprofilseite imr Azure-Portal aus, um die Sitzung des Benutzers zu widerrufen, um sicherzustellen, dass die aktualisierten Richtlinien sofort angewendet werden.

### <a name="ip-address-variation"></a>Variation der IP-Adresse

Im Identitäts- und Ressourcenanbieter werden möglicherweise andere IP-Adressen angezeigt. Dieser Konflikt kann aus folgenden Gründen auftreten:

- Netz-Proxy-Implementierungen in Ihrer Organisation
- Falsche IPv4/IPv6-Konfigurationen zwischen Ihrem Identitätsanbieter und Ihrem Ressourcenanbieter
 
Beispiele:

- Im Identitätsanbieter wird eine IP-Adresse vom Client angezeigt.
- Im Ressourcenanbieter wird nach der Weiterleitung über einen Proxy eine andere IP-Adresse vom Client angezeigt.
- Die IP-Adresse, die Ihr Identitätsanbieter sieht, gehört zu einem in der Richtlinie erlaubten IP-Bereich, die IP-Adresse des Ressourcenanbieters jedoch nicht.

Um Endlosschleifen aufgrund dieser Szenarien zu vermeiden, gibt Azure AD ein einstündiges CAE-Token aus und wird keine Änderung des Clientorts durchsetzen. In diesem Fall wird die Sicherheit im Vergleich zu traditionellen einstündigen Token verbessert, da wir neben den Ereignissen zur Änderung des Clientorts auch [andere-Ereignisse](#critical-event-evaluation) auswerten.

### <a name="supported-location-policies"></a>Unterstützte Standortrichtlinien

CAE hat nur Erkenntnis von [DlP-basiert benannten Orten](../conditional-access/location-condition.md#ip-address-ranges). CAE hat keine Erkenntnis von anderen Ortsbedingungen wie [MFA vertrauenswürdige IPs](../authentication/howto-mfa-mfasettings.md#trusted-ips) oder länderbasierte Orte. Wenn ein Benutzer von einer MFA-vertrauenswürdigen IP, einem vertrauenswürdigen Ort, der MFA-vertrauenswürdige IPs enthält, oder einem länderbasierten Ort kommt, wird CAE nicht durchgesetzt, wenn der Benutzer an einen anderen Ort wechselt. In diesen Fällen wird Azure AD ein einstündiges Zugriffstoken ohne sofortige IP-Durchsetzungsprüfung ausstellen. 

> [!IMPORTANT]
> Verwenden Sie bei der Konfiguration von Standorten für die fortlaufende Zugriffsevaluierung nur die [Standortbedingung für den auf IP-Adressen basierenden bedingten Zugriff](../conditional-access/location-condition.md), und konfigurieren Sie alle IP-Adressen, **einschließlich IPv4- und IPv6-Adressbereichen**, die vom Identitätsanbieter und Ressourcenanbieter angezeigt werden können. Verwenden Sie keine länderspezifischen Standortbedingungen und auch nicht die Funktion für vertrauenswürdige IP-Adressen, die auf der Seite der Diensteinstellungen für Azure AD Multi-Factor Authentication verfügbar ist.

### <a name="office-and-web-account-manager-settings"></a>Einstellungen für Office und Web Account Manager

| Office-Updatekanal | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Halbjährlicher Enterprise-Kanal | Wenn auf aktiviert oder 1 gesetzt, wird CAE nicht unterstützt. | Wenn auf aktiviert oder 1 gesetzt, wird CAE nicht unterstützt. |
| Aktueller Kanal <br> oder <br> Monatlicher Enterprise-Kanal | CAE wird unabhängig von der Einstellung unterstützt | CAE wird unabhängig von der Einstellung unterstützt |

Eine Erläuterung der Office-Updatekanäle finden Sie unter [Übersicht über die Updatekanäle von Microsoft 365 Apps](/deployoffice/overview-update-channels). Es wird empfohlen, dass Organisationen den Web Account Manager (WAM) nicht deaktivieren.

### <a name="coauthoring-in-office-apps"></a>Gemeinsame Dokumenterstellung in Office-Apps

Wenn mehrere Benutzer gleichzeitig an einem Dokument zusammenarbeiten, wird ihr Zugriff auf das Dokument möglicherweise nicht sofort von CAE aufgrund von Benutzersperrung oder Richtlinienänderungsereignissen entzogen widerrufen. In diesem Fall verliert der Benutzer den Zugriff vollständig nach: 

- Schließen des Dokuments
- Schließen der Office-App
- Nach einem Zeitraum von 10 Stunden

Um diese Zeit zu verkürzen, kann ein SharePoint-Administrator die maximale Gültigkeitsdauer von Co-Authoring-Sitzungen für Dokumente, die in SharePoint Online und OneDrive for Business gespeichert sind, durch [Konfiguration einer Netzstandortrichtlinie in SharePoint Online](/sharepoint/control-access-based-on-network-location) reduzieren. Wenn diese Konfiguration geändert ist, wird die maximale Gültigkeitsdauer von Koautorensitzungen auf 15 Minuten reduziert und kann mit dem SharePoint Online PowerShell-Befehl „[Set-SPOTenant-IPAddressWACTokenLifetime](/powershell/module/sharepoint-online/set-spotenant)“ weiter angepasst werden.

### <a name="enable-after-a-user-is-disabled"></a>Aktivieren eines Benutzers nach seiner Deaktivierung

Wenn Sie einen Benutzer direkt nach der Deaktivierung aktivieren, gibt es eine gewisse Wartezeit, bevor das Konto in den nachgelagerten Microsoft-Diensten als aktiviert erkannt wird.

- SharePoint Online und Teams haben in der Regel eine 15-minütige Verzögerung. 
- Exchange Online hat in der Regel eine Verzögerung von 35-40 Minuten. 

### <a name="push-notifications"></a>Pushbenachrichtigungen

Eine IP-Adressrichtlinie wird nicht ausgewertet, bevor Pushbenachrichtigungen freigegeben werden. Dieses Szenario besteht, weil Pushbenachrichtigungen nach auswärts erfolgen und keine zugehörige IP-Adresse haben, gegen die sie ausgewertet werden können. Wenn ein Benutzer auf die Push-Benachrichtigung klickt, z.B. auf eine E-Mail in Outlook, werden die IP-Adressrichtlinien des CAE immer noch durchgesetzt, bevor die E-Mail angezeigt werden kann. Pushbenachrichtigungen zeigen eine Nachrichtenvorschau an, die nicht durch eine IP-Adressrichtlinie geschützt ist. Alle anderen CAE-Prüfungen werden durchgeführt, bevor die Pushbenachrichtigung gesendet wird. Wenn einem Benutzer oder Gerät der Zugriff entzogen wird, erfolgt die Durchsetzung innerhalb des dokumentierten Zeitraums. 

## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Wie funktioniert die fortlaufende Zugriffsevaluierung (CAE) mit der Anmeldehäufigkeit?

Die Anmeldehäufigkeit wird mit oder ohne CAE berücksichtigt.

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von CAE-fähigen APIs in Ihren Anwendungen](../develop/app-resilience-continuous-access-evaluation.md)
- [Anspruchsherausforderungen, Anspruchsanforderungen und Clientfunktionen](../develop/claims-challenge.md)
- [Bedingter Zugriff: Sitzung](concept-conditional-access-session.md)
- [Überwachung und Problembehandlung von fortlaufender Zugriffsevaluierung](howto-continuous-access-evaluation-troubleshoot.md)

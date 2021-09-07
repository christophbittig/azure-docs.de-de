---
title: Referenz zu Fehlercodes
titleSuffix: Azure AD B2C
description: Eine Liste der Fehlercodes, die vom Azure Active Directory B2C-Dienst zurückgegeben werden können.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/16/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c24bcf45b2440aada43af62035f0b988128c8f8
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284543"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Fehlercodes: Azure Active Directory B2C

Die folgenden Fehler können vom Azure Active Directory B2C-Dienst zurückgegeben werden.

| Fehlercode | `Message` |
| ---------- | ------- |
| `AADB2C90002` | Die CORS-Ressource „{0}“ hat einen 404-Fehler (Nicht gefunden) zurückgegeben. |
| `AADB2C90006` | Der in der Anforderung angegebene Umleitungs-URI „{0}“ ist nicht für die Client-ID „{1}“ registriert. |
| `AADB2C90007` | Die der Client-ID „{0}“ zugeordnete Anwendung weist keine registrierten Umleitungs-URIs auf. |
| `AADB2C90008` | Die Anforderung enthält keinen Client-ID-Parameter. |
| `AADB2C90010` | Die Anforderung enthält keinen Bereichsparameter. |
| `AADB2C90011` | Die in der Anforderung angegebene Client-ID „{0}“ stimmt nicht mit der Client-ID „{1}“ überein, die in der Richtlinie registriert ist. |
| `AADB2C90012` | Der in der Anforderung angegebene Bereich „{0}“ wird nicht unterstützt. |
| `AADB2C90013` | Der in der Anforderung angegebene Antworttyp „{0}“ wird nicht unterstützt. |
| `AADB2C90014` | Der in der Anforderung angegebene Antwortmodus „{0}“ wird nicht unterstützt. |
| `AADB2C90016` | Die angeforderte Clientassertion vom Typ „{0}“ stimmt nicht mit dem erwarteten Typ „{1}“ überein. |
| `AADB2C90017` | Die in der Anforderung angegebene Clientassertion ist ungültig: {0} |
| `AADB2C90018` | Die in der Anforderung angegebene Client-ID „{0}“ ist nicht im Mandanten „{1}“ registriert. |
| `AADB2C90019` | Der Schlüsselcontainer mit der ID „{0}“ im Mandanten „{1}“ weist keinen gültigen Schlüssel auf. Grund: {2}. |
| `AADB2C90021` | Das technische Profil „{0}“ ist nicht in der Richtlinie „{1}“ von Mandant „{2}“ enthalten. |
| `AADB2C90022` | Für die Richtlinie „{0}“ in Mandant „{1}“ können keine Metadaten zurückgegeben werden. |
| `AADB2C90023` | Das Profil „{0}“ enthält nicht den erforderlichen Metadatenschlüssel „{1}“. |
| `AADB2C90025` | Das Profil „{0}“ in Richtlinie „{1}“ in Mandant „{2}“ enthält nicht den erforderlichen Kryptografieschlüssel „{3}“. |
| `AADB2C90027` | Die für „{0}“ angegebenen Standardanmeldeinformationen sind ungültig. Überprüfen Sie, ob die Anmeldeinformationen korrekt sind und Zugriff von der Ressource erteilt wurde. |
| `AADB2C90028` | Das für „{0}“ angegebene Clientzertifikat ist ungültig. Überprüfen Sie, ob das Zertifikat korrekt ist, einen privaten Schlüssel enthält und ob Zugriff von der Ressource erteilt wurde. |
| `AADB2C90031` | Die Richtlinie „{0}“ gibt keine standardmäßige User Journey an. Stellen Sie sicher, dass die Richtlinie oder die übergeordnete Richtlinie im Abschnitt der vertrauenden Seite eine standardmäßige User Journey angibt. |
| `AADB2C90035` | Der Dienst ist vorübergehend nicht verfügbar. Versuchen Sie es nach ein paar Minuten erneut. |
| `AADB2C90036` | Die Anforderung enthält keinen URI zur Umleitung des Benutzers nach der Abmeldung. Geben Sie im Parameterfeld „post_logout_redirect_uri“ einen URI an. |
| `AADB2C90037` | Bei der Verarbeitung der Anforderung ist ein Fehler aufgetreten. Wenden Sie sich an den Administrator der Website, auf die Sie zugreifen möchten. |
| `AADB2C90039` | Die Anforderung enthält eine Clientassertion, aber in der angegebenen Richtlinie „{0}“ in Mandant „{1}“ fehlt „client_secret“ in „RelyingPartyPolicy“. |
| `AADB2C90040` | Die User Journey „{0}“ enthält keinen SendClaims-Schritt. |
| `AADB2C90043` | Die in der Anforderung enthaltene Eingabeaufforderung enthält ungültige Werte. Erwartet wird „none“, „login“, „consent“ oder „select_account“. |
| `AADB2C90044` | Der Anspruch „{0}“ wird vom Anspruchskonfliktlöser „{1}“ nicht unterstützt. |
| `AADB2C90046` | Ihr aktueller Status kann nicht geladen werden. Starten Sie Ihre Sitzung komplett neu. |
| `AADB2C90047` | Die Ressource „{0}“ enthält Skriptfehler, die das Laden der Ressource verhindern. |
| `AADB2C90048` | Ausnahmefehler auf dem Server. |
| `AADB2C90051` | Es wurden keine geeigneten Anspruchsanbieter gefunden. |
| `AADB2C90052` | Ungültiger Benutzername oder ungültiges Kennwort. |
| `AADB2C90053` | Es wurde kein Benutzer mit den angegebenen Anmeldeinformationen gefunden. |
| `AADB2C90054` | Ungültiger Benutzername oder ungültiges Kennwort. |
| `AADB2C90055` | Der in der Anforderung angegebene Bereich „{0}“ muss eine Ressource wie „https://example.com/calendar.read“ angeben. |
| `AADB2C90057` | Die angegebene Anwendung ist nicht zum Zulassen des impliziten OAuth-Flows konfiguriert. |
| `AADB2C90058` | Die angegebene Anwendung ist nicht zum Zulassen öffentlicher Clients konfiguriert. |
| `AADB2C99059` | Die angegebene Anforderung muss eine „code_challenge“ darstellen. Erforderlich für Single-Page-Webanwendungen, die den Autorisierungscodeflow verwenden.| 
| `AADB2C90067` | Der URI „{0}“ zur Umleitung nach der Abmeldung weist ein ungültiges Format auf. Geben Sie einen HTTPS-basierten URL wie beispielsweise „https://example.com/return“ an, oder verwenden Sie für native Clients den IETF-URI „urn:ietf:wg:oauth:2.0:oob“ für native Clients. |
| `AADB2C90068` | Die angegebene Anwendung mit der ID „{0}“ ist für diesen Dienst ungültig. Verwenden Sie eine Anwendung, die über das B2C-Portal erstellt wurde, und versuchen Sie es noch mal. |
| `AADB2C90075` | Der in Schritt „{1}“ angegebene Anspruchsaustausch „{0}“ führte zu einer HTTP-Fehlerantwort mit Code „{2}“ und Ursache „{3}“. |
| `AADB2C90077` | Der Benutzer verfügt über keine vorhandene Sitzung, und der Parameter für die Eingabeaufforderung in der Anforderung weist den Wert „{0}“ auf. |
| `AADB2C90079` | Clients müssen bei der Inanspruchnahme der Gewährung eines vertraulichen Zugriffs einen Wert für „client_secret“ senden. |
| `AADB2C90080` | Die angegebene Zugriffsgewährung ist abgelaufen. Authentifizieren Sie sich erneut, und versuchen Sie es noch mal. Aktuelle Uhrzeit: „{0}“, Uhrzeit der Zugriffsgewährung: {1}, Ablauf des gleitenden Fensters für die Zugriffsgewährung: {2}. |
| `AADB2C90081` | Der angegebene Wert für „client_secret“ entspricht nicht dem erwarteten Wert für diesen Client. Korrigieren Sie „client_secret“, und versuchen Sie es noch mal. |
| `AADB2C90083` | In der Anforderung fehlt ein erforderlicher Parameter: {0}. |
| `AADB2C90084` | Öffentliche Clients sollten bei der Inanspruchnahme einer öffentlich erworbenen Gewährung eines vertraulichen Zugriffs keinen Wert für „client_secret“ senden. |
| `AADB2C90085` | Interner Fehler beim Dienst. Authentifizieren Sie sich erneut, und versuchen Sie es noch mal. |
| `AADB2C90086` | Der angegebene „grant_type“ [{0}] wird nicht unterstützt. |
| `AADB2C90087` | Die angegebene Zugriffsgewährung wurde nicht für diese Version des Protokollendpunkts erteilt. |
| `AADB2C90088` | Die angegebene Zugriffsgewährung wurde nicht für diesen Endpunkt erteilt. Istwert: {0}, Erwarteter Wert: {1} |
| `AADB2C90091` | Benutzerabbruch. |
| `AADB2C90092` | Die angegebene Anwendung mit der ID „{0}“ ist für den Mandanten „{1}“ deaktiviert. Aktivieren Sie die Anwendung, und versuchen Sie es noch mal. |
| `AADB2C90107` | Die Anwendung mit der ID „{0}“ kann kein ID-Token abrufen. Entweder wurde der OpenID-Bereich nicht in der Anforderung angegeben, oder die Anwendung ist nicht zum Abruf eines ID-Tokens autorisiert. |
| `AADB2C90108` | Der Orchestrierungsschritt „{0}“ gibt keine CpimIssuerTechnicalProfileReferenceId an, diese wurde erwartet. |
| `AADB2C90110` | Der Bereichsparameter muss „openid“ enthalten, wenn ein „response_type“ angefordert wird, der „id_token“ umfasst. |
| `AADB2C90111` | Ihr Konto wurde gesperrt. Wenden Sie sich an einen Supportmitarbeiter, um die Sperre aufheben zu lassen, und wiederholen Sie den Vorgang. |
| `AADB2C90114` | Ihr Konto wurde vorübergehend gesperrt, um eine unbefugte Nutzung zu verhindern. Versuchen Sie es später noch einmal. |
| `AADB2C90115` | Beim Anfordern des „response_type“-Werts „code“ muss der Bereichsparameter eine Ressourcen- oder Client-ID für Zugriffstoken und „openid“ für ID-Token umfassen. Schließen Sie für Aktualisierungstoken zusätzlich „offline_access“ ein. |
| `AADB2C90117` | Der in der Anforderung angegebene Bereich „{0}“ wird nicht unterstützt. |
| `AADB2C90118` | Der Benutzer hat sein Kennwort vergessen. |
| `AADB2C90120` | Der in der Anforderung angegebene Parameter „{0}“ für das maximale Alter ist ungültig. Der Wert für das maximale Alter muss eine ganze Zahl zwischen {1} und {2} (einschließlich) sein. |
| `AADB2C90122` | Fehler bei der HTTP-Anforderungsüberprüfung für die in der Anforderung empfangene Eingabe für „{0}“. Stellen Sie sicher, dass die Eingabe keine Zeichen wie < oder & enthält. |
| `AADB2C90128` | Das dieser Zugriffserlaubnis zugeordnete Konto ist nicht mehr vorhanden. Authentifizieren Sie sich erneut, und versuchen Sie es noch mal. |
| `AADB2C90129` | Die bereitgestellte Zuweisung wurde widerrufen. Authentifizieren Sie sich erneut, und versuchen Sie es noch mal. |
| `AADB2C90145` | Es wurden keine nicht überprüften Telefonnummern gefunden, und die Richtlinie lässt keine vom Benutzer eingegebene Nummer zu. |
| `AADB2C90146` | Der in der Anforderung angegebene Bereich „{0}“ gibt für ein Zugriffstoken mehrere Ressourcen an. Dies wird nicht unterstützt. |
| `AADB2C90149` | Fehler beim Laden des Skripts „{0}“. |
| `AADB2C90151` | Der Benutzer hat die maximale Anzahl von Wiederholungsversuchen für die mehrstufige Authentifizierung überschritten. |
| `AADB2C90152` | Bei einer mehrstufigen Abrufanforderung konnte keine Antwort vom Dienst abgerufen werden. |
| `AADB2C90154` | Eine mehrstufige Überprüfungsanforderung konnte keine Sitzungs-ID vom Dienst erhalten. |
| `AADB2C90155` | Fehler bei einer mehrstufigen Überprüfungsanforderung. Grund: {0}. |
| `AADB2C90156` | Fehler bei einer mehrstufigen Validierungsanforderung. Grund: {0}. |
| `AADB2C90157` | Der Benutzer hat die maximale Anzahl von Wiederholungsversuchen für einen selbstbestätigten Schritt überschritten. |
| `AADB2C90158` | Fehler bei einer selbstbestätigten Validierungsanforderung. Grund: {0}. |
| `AADB2C90159` | Fehler bei einer selbstbestätigten Überprüfungsanforderung. Grund: {0}. |
| `AADB2C90161` | Fehler bei einer selbstbestätigten gesendeten Antwort. Grund: {0}. |
| `AADB2C90165` | Die SAML-Initialisierungsmeldung mit der ID „{0}“ wurde nicht im Status gefunden. |
| `AADB2C90168` | Die Anforderung zur HTTP-Umleitung enthält nicht den erforderlichen Parameter „{0}“ für eine signierte Anforderung. |
| `AADB2C90178` | Das Signaturzertifikat „{0}“ weist keinen privaten Schlüssel auf. |
| `AADB2C90182` | Der angegebene Wert für „code_verifier“ entspricht nicht dem zugeordneten Wert für „code_challenge“ |
| `AADB2C90183` | Der angegebene Wert für „code_verifier“ ist ungültig |
| `AADB2C90184` | Der angegebene Wert für „code_challenge_method“ wird nicht unterstützt. Unterstützt werden die Werte „plain“ oder „S256“ |
| `AADB2C90188` | Das technische SAML-Profil „{0}“ gibt die PartnerEntitity-URL „{1}“ an, aber die Metadaten konnten nicht abgerufen werden. Ursache: {2}. |
| `AADB2C90194` | Der für das Bearertoken angegebene Anspruch „{0}“ ist in den verfügbaren Ansprüchen nicht vorhanden. Verfügbare Ansprüche: {1}. |
| `AADB2C90205` | Diese Anwendung besitzt keine ausreichenden Berechtigungen für diese Webressource, um den Vorgang durchzuführen. |
| `AADB2C90206` | Timeout beim Initialisieren des Clients. |
| `AADB2C90208` | Der angegebene Parameter „id_token_hint“ ist abgelaufen. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90209` | Der angegebene Parameter „id_token_hint“ enthält keine akzeptierte Zielgruppe. Gültige Zielgruppenwerte: {0}. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90210` | Der angegebene Parameter „id_token_hint“ konnte nicht überprüft werden. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90211` | Die Anforderung enthielt ein unvollständiges Zustandscookie. |
| `AADB2C90212` | Die Anforderung enthielt ein ungültiges Zustandscookie. |
| `AADB2C90220` | Der Schlüsselcontainer im Mandanten „{0}“ mit dem Speicherbezeichner „{1}“ ist vorhanden, enthält aber kein gültiges Zertifikat. Das Zertifikat ist möglicherweise abgelaufen, oder das Aktivierungsdatum für Ihr Zertifikat liegt in der Zukunft (nbf). |
| `AADB2C90223` | Fehler beim Bereinigen der CORS-Ressource. |
| `AADB2C90224` | Der Ressourcenbesitzerflow wurde für die Anwendung nicht aktiviert. |
| `AADB2C90225` | Der in der Anforderung angegebene Benutzername oder das Kennwort ist ungültig. |
| `AADB2C90226` | Der angegebene Tokenaustausch wird nur über HTTP POST unterstützt. |
| `AADB2C90232` | Der angegebene Parameter „id_token_hint“ enthält keinen akzeptierten Aussteller. Gültige Aussteller: „{0}“. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90233` | Fehler bei der Signaturüberprüfung für den angegebenen Parameter „id_token_hint“. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90235` | Das angegebene „id_token“ ist abgelaufen. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90237` | Das angegebene „id_token“ enthält keine gültige Zielgruppe. Gültige Zielgruppenwerte: {0}. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90238` | Das angegebene „id_token“ enthält keinen gültigen Aussteller. Gültige Ausstellerwerte: „{0}“. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90239` | Fehler bei der Signaturüberprüfung für das angegebene „id_token“. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90240` | Das angegebene „id_token“ ist falsch formatiert und konnte nicht analysiert werden. Geben Sie ein anderes Token an, und versuchen Sie es noch mal. |
| `AADB2C90242` | Das technische SAML-Profil „{0}“ gibt einen PartnerEntity-CDATA-Abschnitt an, der nicht geladen werden kann. Ursache: {1}. |
| `AADB2C90243` | Der Clientschlüssel/das Geheimnis für den IDP wurde nicht ordnungsgemäß konfiguriert. |
| `AADB2C90244` | Derzeit sind zu viele Anforderungen vorhanden. Warten Sie einige Zeit, und versuchen Sie es erneut. |
| `AADB2C90248` | Der Ressourcenbesitzerflow kann nur von Anwendungen verwendet werden, die über das B2C-Verwaltungsportal erstellt wurden. |
| `AADB2C90250` | Der generische Endpunkt für die Anmeldung wird nicht unterstützt. |
| `AADB2C90255` | Der im technischen Profil „{0}“ angegebene Austausch von Ansprüchen wurde nicht erwartungsgemäß abgeschlossen. Möglicherweise empfiehlt es sich, die Sitzung noch einmal neu zu starten. |
| `AADB2C90261` | Der in Schritt „{1}“ angegebene Anspruchsaustausch „{0}“ hat eine HTTP-Fehlerantwort zurückgegeben, die nicht analysiert werden konnte. |
| `AADB2C90272` | Der „id_token_hint“-Parameter wurde in der Anforderung nicht angegeben. Geben Sie das Token an, und versuchen Sie es noch mal. |
| `AADB2C90273` | Es wurde eine ungültige Antwort empfangen: {0} |
| `AADB2C90274` | In den Anbietermetadaten wird kein Dienst für einmaliges Abmelden angegeben, oder die Endpunktbindung lautet weder „urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect“ noch „urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST“. |
| `AADB2C90276` | Die Anforderung ist mit der Steuerelementeinstellung „{0}“ nicht konsistent: „{1}“ in technicalProfile „{2}“ für Richtlinie „{3}“, Mandant „{4}“. |
| `AADB2C90277` | Der Orchestrierungsschritt „{0}“ der User Journey „{1}“ der Richtlinie „{2}“ enthält keinen Verweis auf die Inhaltsdefinition. |
| `AADB2C90279` | Die angegebene Client-ID „{0}“ entspricht nicht der Client-ID, die die Berechtigung erteilt hat. |
| `AADB2C90284` | Für die Anwendung mit dem Bezeichner „{0}“ wurde keine Einwilligung erteilt. Sie kann daher nicht für lokale Konten verwendet werden. |
| `AADB2C90285` | Die Anwendung mit dem Bezeichner „{0}“ wurde nicht gefunden. |
| `AADB2C90288` | Die im TechnicalProfile „{1}“ für die Einlösung des Aktualisierungstokens für den für Mandant „{2}“ referenzierte UserJourney mit der ID „{0}“ ist nicht in der Richtlinie „{3}“ oder einer der zugehörigen Basisrichtlinien enthalten. |
| `AADB2C90287` | Die Anforderung enthält einen ungültigen Umleitungs-URI {0}.|
| `AADB2C90289` | Fehler beim Herstellen einer Verbindung mit dem Identitätsanbieter. Versuchen Sie es später noch mal. |
| `AADB2C90296` | Die Anwendung wurde nicht richtig konfiguriert. Wenden Sie sich an den Administrator der Website, auf die Sie zugreifen möchten. |
| `AADB2C99005` | Die Anforderung enthält einen ungültigen Bereichsparameter, der das unzulässige Zeichen {0} enthält. |
| `AADB2C99006` | Azure AD B2C kann die Erweiterungs-App mit der App-ID „{0}“ nicht finden. Weitere Informationen erhalten Sie unter https://go.microsoft.com/fwlink/?linkid=851224. |
| `AADB2C99011` | Der Metadatenwert „{0}“ wurde im TechnicalProfile „{1}“ in der Richtlinie „{2}“ nicht angegeben. |
| `AADB2C99013` | Die angegebene Kombination aus „grant_type“ [{0}] und „token_type“ [{1}] wird nicht unterstützt. |
| `AADB2C99015` | Im Profil „{0}“ in Richtlinie „{1}“ und Mandant „{2}“ fehlen alle für den Anmeldeinformationsfluss des Ressourcenbesitzerkennworts erforderlichen InputClaim-Werte. |

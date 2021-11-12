---
title: Standortbedingung beim bedingten Zugriff in Azure Active Directory
description: Verwenden Sie die Standortbedingung, um den Zugriff basierend auf dem Benutzerstandort zu steuern.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, mewal
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: ef2a7671b1db13e1db9fae8339ae33726a2eb3b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444275"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Verwenden der Standortbedingung in einer Richtlinie für bedingten Zugriff 

Wie im [Übersichtsartikel](overview.md) erläutert wurde, sind Richtlinien für bedingten Zugang in ihrer grundlegendsten Form eine If-Then-Anweisung, die Signale kombiniert, um Entscheidungen zu treffen und Organisationsrichtlinien zu erzwingen. Eines dieser Signale, das in den Entscheidungsprozess integriert werden kann, ist der Standort.

![Konzeptionelles bedingtes Signal plus Entscheidung zum Erzwingen](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organisationen können diesen Standort für gängige Aufgaben wie die folgenden nutzen: 

- Das Vorschreiben von mehrstufiger Authentifizierung für Benutzer beim Zugriff auf einen Dienst, wenn sie sich außerhalb des Unternehmensnetzwerks befinden
- Das Blockieren des Zugriffs auf einen Dienst für Benutzer, die sich in bestimmten Ländern oder Regionen aufhalten.

Der Standort wird durch die öffentliche IP-Adresse bestimmt, die ein Client für Azure Active Directory bereitstellt, oder durch die von der Microsoft Authenticator-App gelieferten GPS-Koordinaten. Richtlinien für bedingten Zugriff werden standardmäßig auf alle IPv4- und IPv6-Adressen angewendet. 

## <a name="named-locations"></a>Benannte Orte

Standorte werden im Azure-Portal unter **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff** > **Benannte Standorte** angegeben. Diese benannten Netzwerkstandorte können Standorte wie z. B. die Netzwerkbereiche des Hauptsitzes einer Organisation, VPN-Netzwerkbereiche oder Bereiche umfassen, die Sie blockieren möchten. Benannte Standorte können nach IPv4/IPv6-Adressbereichen oder Länder/Regionen definiert werden. 

![Benannte Standorte im Azure-Portal](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>IP-Adressbereiche

Zum Definieren eines benannten Standorts nach IPv4-/IPv6-Adressbereichen müssen Sie Folgendes angeben: 

- **Name** für den Standort
- Mindestens ein IP-Adressbereich
- **Als vertrauenswürdigen Standort markieren** (optional)

![Neue IP-Standorte im Azure-Portal](./media/location-condition/new-trusted-location.png)

Benannte Speicherorte, die durch IPv4/IPv6-Adressbereiche definiert werden, unterliegen den folgenden Einschränkungen: 

- Konfigurieren von bis zu 195 benannten Standorten
- Konfigurieren von bis zu 2.000 IP-Adressbereichen pro benanntem Standort
- IPv4- und IPv6-Adressbereiche werden unterstützt.
- Private IP-Adressbereiche können nicht konfiguriert werden.
- Die Anzahl von IP-Adressen innerhalb eines Bereichs ist begrenzt. Beim Konfigurieren eines IP-Adressbereichs sind nur CIDR-Masken größer als /8 zulässig. 

#### <a name="trusted-locations"></a>Vertrauenswürdige Standorte

Administratoren können Standorte, die nach IP-Adressbereichen definiert sind, als vertrauenswürdige benannte Standorte festlegen. 

Die Anmeldung von vertrauenswürdigen benannten Standorten verbessert die Genauigkeit der Risikoberechnung von Azure AD Identity Protection. Außerdem wird das Anmelderisiko von Benutzern verringert, wenn sie sich von einem als vertrauenswürdig gekennzeichneten Standort authentifizieren. Darüber hinaus können vertrauenswürdige benannte Standorte in Richtlinien für bedingten Zugriff verwendet werden. Sie können beispielsweise die [Registrierung für mehrstufige Authentifizierung auf vertrauenswürdige Standorte beschränken](howto-conditional-access-policy-registration.md). 

### <a name="countries"></a>Länder

Organisationen können den Standort des Landes anhand der IP-Adresse oder GPS-Koordinaten bestimmen. 

Zum Definieren eines benannten Standorts nach Land müssen Sie Folgendes angeben: 

- **Name** für den Standort
- Auswählen, ob der Standort anhand der IP-Adresse oder GPS-Koordinaten bestimmt werden soll
- Mindestens ein Land hinzufügen
- **Unbekannte Länder/Regionen einbeziehen** auswählen (optional)

![Land als Standort im Azure-Portal](./media/location-condition/new-named-location-country-region.png)

Wenn Sie **Standort anhand der IP-Adresse bestimmen (nur IPv4)** auswählen, erfasst das System die IP-Adresse des Geräts, bei dem sich der Benutzer anmelden möchte. Wenn sich Benutzer anmelden, löst Azure AD ihre IPv4-Adresse in ein Land oder eine Region auf, und die Zuordnung wird regelmäßig aktualisiert. Organisationen können durch Länder definierte benannte Standorte verwenden, um Datenverkehr aus Ländern zu blockieren, in denen sie nicht tätig sind. 

> [!NOTE]
> Anmeldungen von IPv6-Adressen können keinen Ländern oder Regionen zugeordnet werden und gelten als unbekannte Bereiche. Nur IPv4-Adressen können Ländern oder Regionen zugeordnet werden.

Wenn Sie **Standort anhand von GPS-Koordinaten bestimmen** auswählen, muss die Authentifizerungs-Anwendung von Microsoft auf dem mobilen Gerät des Benutzers installiert sein. Die Microsoft Authenticator-App des Benutzers wird vom System stündlich kontaktiert, um den GPS-Standort des mobilen Geräts des Benutzers zu erfassen.

Wenn der Benutzer seinen Standort zum ersten Mal über die Microsoft Authenticator-App übermitteln muss, erhält er eine Benachrichtigung in der App. Der Benutzer muss die App öffnen und Standortberechtigungen erteilen. 

Wenn der Benutzer in den nächsten 24 Stunden immer noch auf die Ressource zugreift und der App die Berechtigung erteilt hat, im Hintergrund ausgeführt zu werden, wird der Standort des Geräts einmal pro Stunde automatisch übermittelt. Nach 24 Stunden muss der Benutzer die App öffnen und die Benachrichtigung genehmigen. Jedes Mal, wenn der Benutzer seinen GPS-Standort übermittelt, führt die App die Jailbreakerkennung durch (mit der gleichen Logik wie das Intune MAM SDK). Wenn das Gerät einen Jailbreak aufweist, wird der Standort nicht als gültig betrachtet, und dem Benutzer wird kein Zugriff gewährt. 

Eine Richtlinie für bedingten Zugriff mit GPS-basierten benannten Standorten im Modus „Nur melden“ fordert Benutzer auf, ihren GPS-Standort zu übermitteln, auch wenn sie nicht von der Anmeldung blockiert sind.

> [!IMPORTANT]
> Benutzer erhalten in der Authenticator-App unter Umständen stündlich Aufforderungen mit dem Hinweis, dass ihr Standort von Azure AD überprüft wird. Die Vorschauversion sollte nur verwendet werden, um sehr sensible Apps zu schützen, bei denen dieses Verhalten akzeptabel ist oder der Zugriff auf ein bestimmtes Land oder eine bestimmte Region beschränkt werden muss.

#### <a name="include-unknown-countriesregions"></a>Unbekannte Länder/Regionen einbeziehen

Einige IP-Adressen (einschließlich aller IPv6-Adressen) sind weder einem bestimmten Land noch einer bestimmten Region zugeordnet. Aktivieren Sie beim Definieren eines geografischen Standorts das Kontrollkästchen **Unbekannte Länder/Regionen einbeziehen**, um diese IP-Standorte zu erfassen. Mithilfe dieser Option können Sie auswählen, ob der benannte Standort diese IP-Adressen umfassen soll. Verwenden Sie diese Einstellung, wenn die Richtlinie für den benannten Standort auch für unbekannte Standorte gelten soll.

### <a name="configure-mfa-trusted-ips"></a>Konfigurieren durch MFA bestätigter IP-Adressen

Ferner können Sie in den [Einstellungen für den mehrstufigen Authentifizierungsdienst](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) IP-Adressbereiche konfigurieren, die das lokale Intranet Ihrer Organisation darstellen. Mithilfe dieser Funktion können Sie bis zu 50 IP-Adressbereiche konfigurieren. Die IP-Adressbereiche müssen im CIDR-Format angegeben werden. Weitere Informationen finden Sie unter [Vertrauenswürdige IP-Adressen](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Wenn Sie vertrauenswürdige IP-Adressen konfiguriert haben, werden diese in der Liste der Standorte für die Standortbedingung als **Durch MFA bestätigte IP-Adressen** angezeigt.

#### <a name="skipping-multi-factor-authentication"></a>Überspringen der mehrstufigen Authentifizierung

Auf der Einstellungsseite für den mehrstufigen Authentifizierungsdienst können Sie Benutzer aus dem Unternehmensintranet identifizieren, indem Sie **Für Anforderungen von Partnerbenutzern in meinem Intranet die mehrstufige Authentifizierung überspringen** aktivieren. Diese Einstellung gibt an, dass der Anspruch innerhalb des Unternehmensnetzwerks, der von AD FS ausgestellt wird, als vertrauenswürdig angesehen und dazu verwendet wird, den Benutzer als innerhalb des Unternehmensnetzwerks ansässig zu erkennen. Weitere Informationen finden Sie unter [Aktivieren des Features vertrauenswürdige IPs beim bedingten Zugriff](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Nach dem Aktivieren wird diese Option, einschließlich des benannten Standorts **Durch MFA bestätigte IP-Adressen**, auf alle Richtlinien angewendet, für die diese Option ausgewählt ist.

Für mobile und Desktopanwendungen mit langer Sitzungslebensdauer wird der bedingte Zugriff in regelmäßigen Abständen neu ausgewertet. Die Standardeinstellung ist einmal pro Stunde. Wenn der Anspruch innerhalb des Unternehmensnetzwerks nur zum Zeitpunkt der erstmaligen Authentifizierung ausgegeben wird, verfügt Azure AD möglicherweise nicht über eine Liste der vertrauenswürdigen IP-Bereiche. In diesem Fall ist die Bestimmung, ob sich der Benutzer noch im Unternehmensnetzwerk befindet, schwieriger:

1. Überprüfen Sie, ob die IP-Adresse des Benutzers in einem der vertrauenswürdigen IP-Bereiche liegt.
1. Überprüfen Sie, ob die ersten drei Oktette der IP-Adresse des Benutzers mit den ersten drei Oktetten der IP-Adresse der ersten Authentifizierung übereinstimmen. Die IP-Adresse wird mit der ersten Authentifizierung zu dem Zeitpunkt verglichen, zu dem der Anspruch innerhalb des Unternehmensnetzwerks ursprünglich ausgestellt und der Benutzerstandort überprüft wurde.

Wenn bei beiden Schritten ein Fehler auftritt, wird ein Benutzer nicht mehr als vertrauenswürdige IP angesehen.

## <a name="location-condition-in-policy"></a>Standortbedingung in Richtlinie

Beim Konfigurieren der Standortbedingung können Sie zwischen folgenden Elementen unterscheiden:

- Jeden beliebigen Speicherort
- Alle vertrauenswürdigen Speicherorte
- Ausgewählte Speicherorte

### <a name="any-location"></a>Jeden beliebigen Speicherort

Standardmäßig bewirkt das Aktivieren von **Alle Standorte**, dass eine Richtlinie auf alle IP-Adressen angewendet wird, was jede beliebige Adresse im Internet bedeutet. Diese Einstellung ist nicht auf IP-Adressen beschränkt, die von Ihnen als benannter Standort konfiguriert wurden. Wenn Sie **Alle Standorte** aktivieren, können Sie bestimmte Standorte trotzdem noch von einer Richtlinie ausschließen. Beispielsweise können Sie eine Richtlinie auf alle Standorte mit Ausnahme vertrauenswürdiger Standorte anwenden, um den Geltungsbereich auf alle Standorte mit Ausnahme des Unternehmensnetzwerks festzulegen.

### <a name="all-trusted-locations"></a>Alle vertrauenswürdigen Speicherorte

Diese Option gilt für:

- Alle Standorte, die als vertrauenswürdiger Standort gekennzeichnet wurden
- **Durch MFA bestätigte IP-Adressen** (sofern konfiguriert)

### <a name="selected-locations"></a>Ausgewählte Speicherorte

Mit dieser Option können Sie einen oder mehrere benannte Speicherorte auswählen. Damit eine Richtlinie mit dieser Einstellung gilt, muss ein Benutzer von einem der ausgewählten Standorte aus eine Verbindung herstellen. Wenn Sie auf **Auswählen** klicken, wird das Steuerelement für die Auswahl von benannten Netzwerken geöffnet und zeigt die Liste der benannten Netzwerke an. In dieser Liste ist außerdem zu sehen, ob der Netzwerkstandort als vertrauenswürdig gekennzeichnet wurde. Der benannte Standort, der als **Für MFA vertrauenswürdige IPs** bezeichnet ist, wird zum Einschließen der IP-Einstellungen verwendet, die auf der Einstellungsseite des mehrstufigen Authentifizierungsdiensts konfiguriert werden können.

## <a name="ipv6-traffic"></a>IPv6-Datenverkehr

Richtlinien für bedingten Zugriff werden standardmäßig auf den gesamten IPv6-Datenverkehr angewendet. Sie können bestimmte IPv6-Adressbereiche aus einer Richtlinie für bedingten Zugriff ausschließen, wenn Sie bei diesen nicht möchten, dass Richtlinien erzwungen werden. Beispiel: Sie möchten eine Richtlinie für Anwendungsfälle in Ihrem Unternehmensnetzwerk nicht erzwingen, und Ihr Unternehmensnetzwerk wird in öffentlichen IPv6-Adressbereichen gehostet.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identifizieren von IPv6-Datenverkehr in den Azure AD-Anmeldeaktivitätsberichten

Sie können den IPv6-Datenverkehr in Ihrem Mandanten ermitteln, indem Sie die [Azure AD-Anmeldeaktivitätsberichte](../reports-monitoring/concept-sign-ins.md) aufrufen. Nachdem Sie den Aktivitätsbericht geöffnet haben, fügen Sie die Spalte „IP-Adresse“ hinzu. Diese Spalte gibt Ihnen die Möglichkeit, den IPv6-Datenverkehr zu ermitteln.

Sie können auch nach der Client-IP-Adresse suchen, indem Sie auf eine Zeile im Bericht klicken und dann in den Details der Anmeldeaktivität zur Registerkarte „Standort“ wechseln. 

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Wann tritt bei meinem Mandanten IPv6-Datenverkehr auf?

Azure Active Directory (Azure AD) unterstützt derzeit keine direkten Netzwerkverbindungen mit IPv6. Es gibt jedoch einige Fälle, in denen der Authentifizierungsdatenverkehr über einen anderen Proxydienst geleitet wird. In diesen Fällen wird die IPv6-Adresse bei der Richtlinienauswertung verwendet.

Der größte Teil des IPv6-Datenverkehrs, der über einen Proxy an Azure AD geleitet wird, stammt von Microsoft Exchange Online. Exchange bevorzugt IPv6-Verbindungen, wenn sie verfügbar sind. **Wenn Sie also eine Richtlinie für bedingten Zugriff für Exchange haben, die für bestimmte IPv4-Adressbereiche konfiguriert wurde, sollten Sie sicherstellen, dass Sie auch die IPv6-Adressbereiche Ihrer Organisation hinzugefügt haben.** Wenn Sie IPv6-Adressbereiche nicht einschließen, führt das in den folgenden beiden Fällen zu unerwartetem Verhalten:

- Wenn für die Verbindung mit Exchange Online ein E-Mail-Client mit Legacyauthentifizierung verwendet wird, empfängt Azure AD möglicherweise eine IPv6-Adresse. Die anfängliche Authentifizierungsanforderung gelangt zu Exchange und wird dann über einen Proxy an Azure AD geleitet.
- Wenn Sie Outlook Web Access (OWA) im Browser verwenden, wird in regelmäßigen Abständen überprüft, ob alle Richtlinien für bedingten Zugriff weiterhin erfüllt werden. Diese Überprüfung wird zum Erfassen von Fällen verwendet, in denen ein Benutzer möglicherweise von einer zulässigen IP-Adresse an einen neuen Standort gewechselt ist (z. B. ein Cafe, das in der Straße ein paar Häuser weitergezogen ist). Wenn Sie in diesem Fall eine IPv6-Adresse verwenden, die sich nicht in einem konfigurierten Bereich befindet, wird die Sitzung möglicherweise unterbrochen, und der Benutzer wird zur erneuten Authentifizierung zurück zu Azure AD geleitet. 

Wenn Sie Azure-VNets verwenden, geht außerdem Datenverkehr von einer IPv6-Adresse ein. Wenn der VNet-Datenverkehr durch eine Richtlinie für bedingten Zugriff blockiert wird, überprüfen Sie Ihr Azure AD-Anmeldeprotokoll. Nachdem Sie den Datenverkehr identifiziert haben, können Sie die verwendete IPv6-Adresse von Ihrer Richtlinie ausschließen. 

> [!NOTE]
> Wenn Sie für eine einzelne Adresse einen IP-CIDR-Bereich angeben möchten, wenden Sie die /128-Bitmaske an. Wenn die IPv6-Adresse „2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a“ angezeigt wird und Sie diese einzelne Adresse als Adressbereich ausschließen möchten, würden Sie „2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128“ verwenden.

## <a name="what-you-should-know"></a>Wichtige Informationen

### <a name="when-is-a-location-evaluated"></a>Wann wird ein Standort ausgewertet?

Richtlinien für bedingten Zugriff werden in diesen Situationen ausgewertet:

- Ein Benutzer meldet sich zum ersten Mal an einer Web-App, mobilen Anwendung oder Desktopanwendung an.
- Für eine mobile Anwendung oder Desktopanwendung, für die die moderne Authentifizierung verwendet wird, wird ein Aktualisierungstoken zum Beschaffen eines neuen Zugriffstokens genutzt. Standardmäßig erfolgt diese Überprüfung einmal pro Stunde.

Für mobile Anwendungen und Desktopanwendungen mit moderner Authentifizierung bedeutet diese Überprüfung, dass eine Änderung des Netzwerkstandorts innerhalb von einer Stunde erkannt wird. Wenn für mobile Anwendungen und Desktopanwendungen keine moderne Authentifizierung genutzt wird, wird die Richtlinie auf jede Tokenanforderung angewendet. Die Häufigkeit der Anforderung kann basierend auf der Anwendung variieren. Ebenso wird die Richtlinie für Webanwendungen bei der ersten Anmeldung angewendet und gilt für die Lebensdauer der Webanwendungssitzung. Aufgrund von Unterschieden bei den Sitzungslebensdauern von Anwendungen variiert auch die Zeit zwischen den Richtlinienauswertungen. Die Richtlinie wird jedes Mal angewendet, wenn die Anwendung ein neues Anmeldetoken anfordert.

Standardmäßig stellt Azure AD stündlich ein Token aus. Nach dem Verlassen des Unternehmensnetzwerks wird die Richtlinie für Anwendungen mit moderner Authentifizierung innerhalb einer Stunde durchgesetzt.

### <a name="user-ip-address"></a>Benutzer-IP-Adresse

Die IP-Adresse, die in der Auswertung der Richtlinie verwendet wird, ist die öffentliche IP-Adresse des Benutzers. Bei Geräten in einem privaten Netzwerk ist diese IP-Adresse nicht die Client-IP des Geräts des Benutzers im Intranet, sondern die vom Netzwerk für das Herstellen der Verbindung mit dem öffentlichen Internet verwendete Adresse.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Massenhoch- und -herunterladen von benannten Standorten

Beim Erstellen oder Aktualisieren benannter Standorte können Sie für eine Massenaktualisierung eine CSV-Datei mit den IP-Adressbereichen hoch- oder herunterladen. Bei einem Upload werden die IP-Adressbereiche in der Liste durch die Bereiche aus der Datei ersetzt. Jede Zeile der Datei enthält einen IP-Adressbereich im CIDR-Format.

### <a name="cloud-proxies-and-vpns"></a>Cloud-Proxys und VPNs

Wenn Sie einen in der Cloud gehosteten Proxy oder eine VPN-Lösung verwenden, ist die von Azure AD bei der Auswertung einer Richtlinie verwendete IP-Adresse die IP-Adresse des Proxys. Der XFF-Header (X-Forwarded-For), der die öffentliche IP-Adresse des Benutzers enthält, wird nicht verwendet, da es keine Überprüfung gibt, ob er aus einer vertrauenswürdigen Quelle stammt, sodass er ein Verfahren zum Fälschen einer IP-Adresse darstellen kann.

Wenn ein Cloud-Proxy zum Einsatz kommt, kann eine Richtlinie verwendet werden, mit der die Verwendung von hybriden, eingebundenen Azure AD-Geräten oder des Anspruchs aus dem internen Unternehmensnetzwerk von AD FS vorgeschrieben wird.

### <a name="api-support-and-powershell"></a>API-Unterstützung und PowerShell

Eine Vorschauversion der Graph-API für benannte Standorte ist verfügbar. Weitere Informationen finden Sie unter [namedLocation-API](/graph/api/resources/namedlocation).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Konfigurieren einer Richtlinie für bedingten Zugriff mithilfe des Standorts finden Sie im Artikel [Bedingter Zugriff: Blockieren des Zugriffs nach Standort](howto-conditional-access-policy-location.md).

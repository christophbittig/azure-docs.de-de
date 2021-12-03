---
title: Azure Key Vault-Sicherheitsübersicht
description: Übersicht über Sicherheitsfeatures und Best Practices für Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: ccd2e196f1e1a44a79f0d4f9f1f07e4a69f2b974
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053120"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault – Sicherheit

Azure Key Vault schützt kryptografische Schlüssel, Zertifikate (und die privaten Schlüssel, die den Zertifikaten zugeordnet sind) und Geheimnisse (z. B. Verbindungszeichenfolgen und Kennwörter) in der Cloud. Wenn Sie jedoch vertrauliche und unternehmenskritische Daten speichern, müssen Sie Maßnahmen ergreifen, um die Sicherheit Ihrer Tresore und der darin gespeicherten Daten zu maximieren.

Dieser Artikel bietet eine Übersicht über Sicherheitsfeatures und Best Practices für Azure Key Vault.

> [!NOTE]
> Eine umfassende Liste mit Sicherheitsempfehlungen für Azure Key Vault finden Sie in der [Sicherheitsbaseline für Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Netzwerksicherheit

Sie können die Angriffsfläche Ihrer Tresore reduzieren, indem Sie angeben, welche IP-Adressen Zugriff darauf haben. Die VNET-Dienstendpunkte für Azure Key Vault ermöglichen Ihnen, den Zugriff auf angegebene virtuelle Netzwerke zu beschränken. Die Endpunkte ermöglichen Ihnen außerdem, den Zugriff auf eine Liste von IPv4-Adressbereichen (Internet Protocol, Version 4) zu beschränken. Allen Benutzern, die außerhalb dieser Quellen eine Verbindung mit Ihrem Schlüsseltresor herstellen, wird der Zugriff verweigert.  Vollständige Informationen dazu finden Sie unter [VNET-Dienstendpunkte für Azure Key Vault](overview-vnet-service-endpoints.md).

Wenn Firewallregeln in Kraft sind, können Benutzer nur Daten aus Key Vault lesen, wenn ihre Anforderungen aus zulässigen virtuellen Netzwerken oder IPv4-Adressbereichen stammen. Dies gilt auch für den Zugriff auf den Schlüsseltresor aus dem Azure-Portal. Obwohl Benutzer im Azure-Portal zu einem Schlüsseltresor navigieren können, können sie möglicherweise keine Schlüssel, Geheimnisse oder Zertifikate auflisten, wenn ihr Clientcomputer nicht in der Zulassungsliste enthalten ist. Die Implementierungsschritte finden Sie unter [Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken](network-security.md).

Mit dem Azure Private Link-Dienst können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure Key Vault sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen. Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.  Die Implementierungsschritte finden Sie unter [Integrieren von Key Vault in Azure Private Link](private-link-service.md).

## <a name="tls-and-https"></a>TLS und HTTPS

- Beim Key Vault-Front-End (Datenebene) handelt es sich um einen mehrinstanzenfähigen Server. Dies bedeutet, dass die Schlüsseltresore verschiedener Kunden dieselbe öffentliche IP-Adresse gemeinsam nutzen können. Für die Gewährleistung von Isolation wird jede HTTP-Anforderung unabhängig von anderen Anforderungen authentifiziert und autorisiert.
- Sie können ältere TLS-Versionen zwar ermitteln, um Sicherheitsrisiken zu melden, da die öffentliche IP-Adresse jedoch von mehreren Kunden gemeinsam genutzt wird, ist es dem Key Vault-Team nicht möglich, ältere TLS-Versionen für einzelne Schlüsseltresore auf Transportebene zu deaktivieren.
- Das HTTPS-Protokoll ermöglicht es dem Client, an der TLS-Aushandlung teilzunehmen. **Clients können die neueste TLS-Version erzwingen.** Wenn dies der Fall ist, wird für die gesamte Verbindung der entsprechende Ebenenschutz verwendet. Anwendungen, die mit Azure Active Directory kommunizieren oder sich bei Azure Active Directory authentifizieren, funktionieren möglicherweise nicht wie erwartet, wenn sie NICHT in der Lage sind, TLS 1.2 oder die aktuelle Version für die Kommunikation zu verwenden.
- Trotz bekannter Sicherheitsrisiken im TLS-Protokoll gibt es keinen bekannten Angriff, bei dem ein böswilliger Agent Informationen aus Ihrem Schlüsseltresor extrahieren kann, wenn ein Angreifer eine Verbindung mit einer TLS-Version initiiert, die Sicherheitsrisiken aufweist. Der Angreifer muss sich trotzdem authentifizieren und autorisiert werden, und solange berechtigte Clients Verbindungen immer mit den aktuellen TLS-Versionen herstellen, ist es nicht möglich, dass Anmeldeinformationen aufgrund von Sicherheitsrisiken in älteren TLS-Versionen kompromittiert werden.

> [!NOTE]
> Stellen Sie in Azure Key Vault sicher, dass die Anwendung, die auf den Keyvault-Dienst zugreift, auf einer Plattform ausgeführt wird, die TLS 1.2 oder die aktuelle Version unterstützt. Wenn die Anwendung von .NET Framework abhängig ist, sollte sie ebenfalls aktualisiert werden. Sie können auch die in [diesem Artikel](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment) erwähnten Registrierungsänderungen vornehmen, um die Verwendung von TLS 1.2 auf Betriebssystemebene und für .NET Framework explizit zu aktivieren.

## <a name="key-vault-authentication-options"></a>Key Vault-Authentifizierungsoptionen

Wenn Sie in einem Azure-Abonnement einen Schlüsseltresor erstellen, wird dieser automatisch mit dem Azure AD-Mandanten des Abonnements verknüpft. Alle Aufrufer in beiden Ebenen müssen bei diesem Mandanten registriert sein und sich authentifizieren, um auf den Schlüsseltresor zugreifen zu können. In beiden Fällen können Anwendungen auf drei Arten auf den Schlüsseltresor zugreifen:

- **Nur Anwendungszugriff**: Die Anwendung stellt einen Dienstprinzipal oder eine verwaltete Identität dar. Diese Identität ist das häufigste Szenario für Anwendungen, die in regelmäßigen Abständen auf Zertifikate, Schlüssel oder Geheimnisse aus dem Schlüsseltresor zugreifen müssen. Damit dieses Szenario funktioniert, muss die `objectId` der Anwendung in der Zugriffsrichtlinie angegeben werden, und die `applicationId` darf _nicht_ angegeben werden oder muss `null` sein.
- **Nur Benutzerzugriff**: Der Benutzer greift auf den Schlüsseltresor aus allen Anwendungen zu, die im Mandanten registriert sind. Beispiele für diese Art von Zugriff wären etwa Azure PowerShell und das Azure-Portal. Damit dieses Szenario funktioniert, muss die `objectId` des Benutzers in der Zugriffsrichtlinie angegeben werden, und die `applicationId` darf _nicht_ angegeben werden oder muss `null` sein.
- **Anwendungs- und Benutzerzuriff** (manchmal als _Verbundidentität_ bezeichnet): Der Benutzer muss aus einer bestimmten Anwendung auf den Schlüsseltresor zugreifen, _und_ die Anwendung muss den On-Behalf-Of-Authentifizierungsdatenfluss verwenden, um die Identität des Benutzers anzunehmen. Damit dieses Szenario funktioniert, müssen sowohl `applicationId` als auch `objectId` in der Zugriffsrichtlinie angegeben werden. `applicationId` identifiziert die erforderliche Anwendung, und `objectId` identifiziert den Benutzer. Diese Option ist derzeit nicht verfügbar für die Azure RBAC-Datenebene.

Bei allen Arten des Zugriffs wird die Anwendung in Azure AD authentifiziert. Die Anwendung verwendet eine beliebige [unterstützte Authentifizierungsmethode](../../active-directory/develop/authentication-vs-authorization.md), die auf dem Anwendungstyp basiert. Die Anwendung erwirbt ein Token für eine Ressource in der Ebene, um den Zugriff zu gewähren. Die Ressource ist ein Endpunkt in der Verwaltungs- oder Datenebene, basierend auf der Azure-Umgebung. Anschließend sendet die Anwendung unter Verwendung des Tokens eine REST-API-Anforderung an einen Schlüsseltresor. Weitere Informationen finden Sie in der [Gesamtdarstellung des Authentifizierungsablaufs](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Das Modell eines einzelnen Mechanismus für die Authentifizierung für beide Ebenen hat mehrere Vorteile:

- Organisationen können den Zugriff auf alle Schlüsseltresore zentral steuern.
- Wenn ein Benutzer aus der Organisation ausscheidet, verliert er umgehend den Zugriff auf sämtliche Schlüsseltresore in der Organisation.
- Organisationen können die Authentifizierung über die Optionen in Azure AD anpassen und so beispielsweise die mehrstufige Authentifizierung aktivieren, um die Sicherheit zu verbessern.

Weitere Informationen finden Sie unter [Grundlagen der Key Vault-Authentifizierung](authentication.md).

## <a name="access-model-overview"></a>Übersicht über das Zugriffsmodell

Der Zugriff auf einen Schlüsseltresor wird über zwei Schnittstellen gesteuert: die **Verwaltungsebene** und die **Datenebene**. Die Verwaltungsebene dient zum Verwalten des Schlüsseltresors selbst. Zu den Vorgängen in dieser Ebene gehören das Erstellen und Löschen von Schlüsseltresoren, das Abrufen von Schlüsseltresor-Eigenschaften und das Aktualisieren von Zugriffsrichtlinien. Auf der Datenebene arbeiten Sie mit den in einem Schlüsseltresor gespeicherten Daten. Sie können Schlüssel, Geheimnisse und Zertifikate hinzufügen, löschen und ändern.

Für die Authentifizierung verwenden beide Ebenen [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Für die Autorisierung wird auf der Verwaltungsebene die [rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) verwendet, während auf der Datenebene eine [Key Vault-Zugriffsrichtlinie](./assign-access-policy-portal.md) und [Azure RBAC für Key Vault-Vorgänge auf Datenebene](./rbac-guide.md) zum Einsatz kommen.

Um auf einen Schlüsseltresor in beiden Ebenen zugreifen zu können, müssen alle Anrufe (Benutzer oder Anwendungen) über eine ordnungsgemäße Authentifizierung und Autorisierung verfügen. Die Authentifizierung stellt die Identität des Anrufers fest. Die Autorisierung bestimmt, welche Vorgänge der Aufrufer ausführen darf. Die Authentifizierung mit Key Vault funktioniert in Verbindung mit [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), was für die Authentifizierung der Identität eines bestimmten **Sicherheitsprinzipals** zuständig ist.

Ein Sicherheitsprinzipal ist ein Objekt, das Benutzer, Gruppen, Dienste oder Anwendungen darstellt, die Zugriff auf Azure-Ressourcen anfordern. Azure weist jedem Sicherheitsprinzipal eine eindeutige **Objekt-ID** zu.

- Ein **Benutzer** als Sicherheitsprinzipal identifiziert eine Person, die über ein Profil in Azure Active Directory verfügt.
- Eine **Gruppe** als Sicherheitsprinzipal identifiziert eine Gruppe von Benutzern, die in Azure Active Directory erstellt wurden. Alle Rollen oder Berechtigungen, die der Gruppe zugewiesen werden, werden allen Benutzern in der Gruppe erteilt.
- Ein **Dienstprinzipal** ist ein Typ von Sicherheitsprinzipal, der für eine Anwendung oder einen Dienst steht und sozusagen ein Codeausschnitt anstelle eines Benutzers oder einer Gruppe ist. Die Objekt-ID eines Dienstprinzipals wird als dessen **Client-ID** bezeichnet und verhält sich wie der Benutzername. Der **geheime Clientschlüssel** oder das **Zertifikat** des Dienstprinzipals verhält sich wie sein Kennwort. Viele Azure-Dienste unterstützen das Zuweisen von [Verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) mit automatisierter Verwaltung von **Client-ID** und **Zertifikat**. „Verwaltete Identität“ ist die sicherste und empfohlene Option zum Authentifizieren in Azure.

Weitere Informationen zur Authentifizierung bei Key Vault finden Sie unter [Authentifizieren bei Azure Key Vault](authentication.md).

## <a name="conditional-access"></a>Bedingter Zugriff 

Key Vault bietet Unterstützung für Azure Active Directory-Richtlinien für bedingten Zugriff. Mithilfe von Richtlinien für bedingten Zugriff können Sie bei Bedarf die richtigen Zugriffssteuerungen auf Key Vault anwenden, um die Sicherheit Ihrer Organisation zu gewährleisten, und behindern die Benutzer*innen ansonsten nicht.

Weitere Informationen finden Sie unter [Übersicht über den bedingten Zugriff](../../active-directory/conditional-access/overview.md).

## <a name="privileged-access"></a>Privilegierter Zugriff

Die Autorisierung bestimmt, welche Vorgänge der Aufrufer ausführen darf. Für die Autorisierung wird in Key Vault eine Kombination aus [rollenbasierter Zugriffssteuerung in Azure](../../role-based-access-control/overview.md) (Azure RBAC) und Azure Key Vault-Zugriffsrichtlinien verwendet.

Der Zugriff auf Tresore erfolgt über zwei Schnittstellen oder Ebenen. Die Ebenen lauten Verwaltungsebene und Datenebene.

- Auf *Verwaltungsebene* verwalten Sie Key Vault selbst. Sie stellt die Schnittstelle zum Erstellen und Löschen von Tresoren dar. Sie können auch die Eigenschaften von Schlüsseltresoren lesen und Zugriffsrichtlinien verwalten.
- Auf der *Datenebene* arbeiten Sie mit den in einem Schlüsseltresor gespeicherten Daten. Sie können Schlüssel, Geheimnisse und Zertifikate hinzufügen, löschen und ändern.

Die Anwendungen greifen über Endpunkte auf die Ebenen zu. Die Zugriffssteuerung für die beiden Ebenen arbeiten voneinander unabhängig. Um einer Anwendung Zugriff auf die Verwendung von Schlüsseln in einem Schlüsseltresor zu gewähren, müssen Sie den Zugriff auf die Datenebene unter Verwendung einer Key Vault-Zugriffsrichtlinie oder Azure RBAC gewähren. Um einem Benutzer Lesezugriff auf die Eigenschaften und Tags des Schlüsseltresors zu gewähren, aber nicht auf Daten (Schlüssel, Geheimnisse oder Zertifikate), gewähren Sie mit Azure RBAC den Zugriff auf die Verwaltungsebene.

Die folgende Tabelle zeigt die Endpunkte für die Verwaltungs- und Datenebene.

| Zugriffs&nbsp;ebene | Zugriffsendpunkte | Operationen (Operations) | Zugriffs&nbsp;steuerungsmechanismus |
| --- | --- | --- | --- |
| Verwaltungsebene | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> management.microsoftazure.de:443 | Erstellen, Lesen, Aktualisieren und Löschen von Schlüsseltresoren<br><br>Festlegen von Zugriffsrichtlinien für den Schlüsseltresor<br><br>Festlegen der Schlüsseltresortags | Azure RBAC |
| Datenebene | **Global:**<br> &lt;Tresorname&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;Tresorname&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;Tresorname&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> &lt;Tresorname&gt;.vault.microsoftazure.de:443 | Schlüssel: encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Zertifikate: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Geheimnisse: get, list, set, delete,recover, backup, restore, purge | Key Vault-Zugriffsrichtlinie oder Azure RBAC |

### <a name="managing-administrative-access-to-key-vault"></a>Verwalten des Administratorzugriffs auf Key Vault

Wenn Sie einen Schlüsseltresor in einer Ressourcengruppe erstellen, steuern Sie den Zugriff mithilfe von Azure AD. So können Sie Benutzern oder einer Gruppe die Verwaltung von Schlüsseltresoren in einer Ressourcengruppe ermöglichen. Sie können den Zugriff auf eine bestimmte Bereichsebene gewähren, indem Sie entsprechende Azure-Rollen zuordnen. Um einem Benutzer Zugriff für die Verwaltung von Schlüsseltresoren zu gewähren, weisen Sie ihm für einen bestimmten Bereich die vordefinierte Rolle `key vault Contributor` zu. Die folgenden Bereichsebenen können einer Azure-Rolle zugeordnet werden:

- **Abonnement**: Eine auf Abonnementebene zugewiesene Azure-Rolle gilt für alle Ressourcengruppen und Ressourcen innerhalb des Abonnements.
- **Ressourcengruppe**: Eine auf Ressourcengruppenebene zugewiesene Azure-Rolle gilt für alle Ressourcen in der Ressourcengruppe.
- **Bestimmte Ressourcen**: Eine für eine bestimmte Ressource zugewiesene Azure-Rolle gilt für diese Ressource. In diesem Fall ist die Ressource ein bestimmter Schlüsseltresor.

Es gibt verschiedene vordefinierte Rollen. Wenn eine vordefinierte Rolle nicht Ihren Anforderungen entspricht, können Sie Ihre eigene Rolle definieren. Weitere Informationen finden Sie unter [Azure RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Falls ein Benutzer für eine Schlüsseltresor-Verwaltungsebene über die Berechtigung `Contributor` verfügt, kann er sich durch Festlegen einer Zugriffsrichtlinie für den Schlüsseltresor selbst Zugriff auf die Datenebene gewähren. Deshalb sollten sie stets kontrollieren, wer als `Contributor` auf Ihre Schlüsseltresore zugreifen kann. Stellen Sie sicher, dass nur autorisierte Personen auf Ihre Schlüsseltresore, Schlüssel, Geheimnisse und Zertifikate zugreifen und diese verwalten können.

### <a name="controlling-access-to-key-vault-data"></a>Steuern des Zugriffs auf Key Vault-Daten

Mit Schlüsseltresor-Zugriffsrichtlinien können separate Berechtigungen für Schlüssel, Geheimnisse oder Zertifikate gewährt werden. Sie können einem Benutzer nur Zugriff auf Schlüssel, nicht auf Geheimnisse gewähren. Zugriffsberechtigungen für Schlüssel, Geheimnisse und Zertifikate werden auf Tresorebene verwaltet.

> [!IMPORTANT]
> Die Schlüsseltresor-Zugriffsrichtlinie unterstützt keine differenzierten Berechtigungen auf Objektebene, wie z.B. für bestimmte Schlüssel, Geheimnisse oder Zertifikate. Wenn einem Benutzer die Berechtigung zum Erstellen und Löschen von Schlüsseln gewährt wird, kann er diese Vorgänge für alle Schlüssel in diesem Schlüsseltresor ausführen.

Zum Festlegen von Zugriffsrichtlinien für einen Schlüsseltresor können Sie das [Azure-Portal](assign-access-policy-portal.md), die [Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md) oder die [REST-APIs für die Schlüsseltresorverwaltung](/rest/api/keyvault/) verwenden.

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

Bei der Key Vault-Protokollierung werden Informationen zu den Aktivitäten gespeichert, die für Ihren Tresor ausgeführt werden. Ausführliche Informationen finden Sie unter [Key Vault-Protokollierung](logging.md).

Sie können Key Vault in Event Grid integrieren, um Benachrichtigungen zu erhalten, wenn sich der Status eines im Schlüsseltresor gespeicherten Schlüssels, Zertifikats oder Geheimnisses geändert hat. Informationen dazu finden Sie unter [Überwachen von Key Vault mit Azure Event Grid](event-grid-overview.md).

Es ist auch wichtig, die Integrität Ihres Schlüsseltresors zu überwachen, um sicherzustellen, dass Ihr Dienst wie vorgesehen funktioniert. Unter [Überwachung und Warnungen für Azure Key Vault](alert.md) erfahren Sie, wie Sie dazu vorgehen.

## <a name="backup-and-recovery"></a>Sicherung und Wiederherstellung

Mit den Azure Key Vault-Funktionen zum Schutz beim vorläufigen und endgültigen Löschen können Sie gelöschte Tresore und Tresorobjekte wiederherstellen. Vollständige Informationen dazu finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](soft-delete-overview.md).

Sie sollten beim Aktualisieren, Löschen und Erstellen von Objekten in einem Schlüsseltresor auch regelmäßig Sicherungskopien Ihres Schlüsseltresors erstellen.  

## <a name="next-steps"></a>Nächste Schritte

- [Azure Key Vault-Sicherheitsbaseline](security-baseline.md)
- [Bewährte Methoden zum Verwenden von Key Vault](security-baseline.md)
- [VNET-Dienstendpunkte für Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md).

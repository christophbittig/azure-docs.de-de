---
title: Funktionsweise der Azure AD-Geräteregistrierung
description: Azure AD-Geräteregistrierungsflows für verwaltete Domänen und Verbunddomänen
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc24ecd387c011b5a8a36f380916ab815c1f0ab
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122350975"
---
# <a name="how-it-works-device-registration"></a>Funktionsweise: Geräteregistrierung

Die Geräteregistrierung ist eine Voraussetzung für die cloudbasierte Authentifizierung. Häufig werden Geräte in Azure AD oder Azure AD Hybrid eingebunden, um die Geräteregistrierung abzuschließen. Dieser Artikel enthält Details dazu, wie die Azure AD-Einbindung und die Azure AD Hybrid-Einbindung in verwalteten Umgebungen und Verbundumgebungen funktionieren. Weitere Informationen dazu, wie die Azure AD-Authentifizierung bei diesen Geräten funktioniert, finden Sie im Artikel [Primäre Aktualisierungstoken](concept-primary-refresh-token.md#detailed-flows).

## <a name="azure-ad-joined-in-managed-environments"></a>In verwaltete Umgebungen eingebundenes Azure AD

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-managed.png" alt-text="In Azure AD eingebundener Geräteflow in einer verwalteten Umgebung" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-managed.png":::

| Phase | BESCHREIBUNG |
| :----: | :----------- |
| Ein | Die gängigste Methode zur Registrierung von in Azure AD eingebundenen Geräten wird während der Out-of-Box-Experience (OOBE) ausgeführt, bei der die Azure AD Join-Webanwendung in die Cloud Experience Host-Anwendung (CXH) geladen wird. Die Anwendung sendet eine GET-Anforderung an den Azure AD OpenID-Konfigurationsendpunkt, um Autorisierungsendpunkte zu ermitteln. Azure AD gibt die OpenID-Konfiguration mit den Autorisierungsendpunkten als JSON-Dokument an die Anwendung zurück. |
| B | Die Anwendung erstellt eine Anmeldeanforderung für den Autorisierungsendpunkt und sammelt Benutzeranmeldeinformationen. |
| C | Nachdem der Benutzer seinen Benutzernamen (im UPN-Format) angegeben hat, sendet die Anwendung eine GET-Anforderung an Azure AD, um entsprechende Bereichsinformationen für den Benutzer zu ermitteln. Diese Informationen bestimmen, ob die Umgebung verwaltet wird oder im Verbund ist. Azure AD gibt die Informationen in einem JSON-Objekt zurück. Die Anwendung bestimmt, dass die Umgebung verwaltet wird (nicht im Verbund ist).<br><br>Im letzten Schritt dieser Phase erstellt die Anwendung einen Authentifizierungspuffer und (falls in OOBE) speichert ihn für automatische Anmeldung am Ende der OOBE vorübergehend zwischen. Die Anwendung sendet die Anmeldeinformationen an Azure AD, wo sie überprüft werden. Azure AD gibt ein ID-Token mit Ansprüchen zurück. |
| D | Die Anwendung sucht nach MDM-Nutzungsbedingungen (dem Anspruch „mdm_tou_url“). Falls vorhanden, ruft die Anwendung diese Bedingungen aus dem Wert des Anspruchs ab, stellt dem Benutzer den Inhalt bereit und wartet darauf, dass der Benutzer die Bedingungen akzeptiert. Dieser Schritt ist optional und wird übersprungen, wenn der Anspruch nicht vorhanden oder der Anspruchswert leer ist. |
| E | Die Anwendung sendet eine Anforderung zur Ermittlung der Geräteregistrierung an den Azure-Geräteregistrierungsdienst (Azure Device Registration Service, Azure DRS, ADRS). Azure DRS gibt ein Ermittlungsdatendokument zurück, das wiederum mandantenspezifische URIs zurückgibt, um die Geräteregistrierung abzuschließen. |
| F | Die Anwendung erstellt ein TPM-gebundenes (bevorzugtes) RSA 2048-Bit-Schlüsselpaar, das als „Geräteschlüssel“ („dkpub“/„dkpriv“) bezeichnet wird. Die Anwendung erstellt eine Zertifikatanforderung mithilfe von „dkpub“ und dem öffentlichen Schlüssel und signiert die Anforderung mithilfe von „dkpriv“. Als Nächstes leitet die Anwendung das zweite Schlüsselpaar vom Speicherstammschlüssel des TPM ab. Dieser Schlüssel ist der „Transportschlüssel“ („tkpub“/„tkpriv“). |
| G | Die Anwendung sendet eine Geräteregistrierungsanforderung mit dem ID-Token, der Zertifikatanforderung, „tkpub“ und den Nachweisdaten an Azure DRS. Azure DRS überprüft das ID-Token, erstellt eine Geräte-ID und dann basierend auf der enthaltenen Zertifikatanforderung ein Zertifikat. Anschließend schreibt Azure DRS ein Geräteobjekt in Azure AD und sendet die Geräte-ID und das Gerätezertifikat an den Client. |
| H | Die Geräteregistrierung wird durch den Empfang der Geräte-ID und des Gerätezertifikats von Azure DRS abgeschlossen. Die Geräte-ID wird als künftige Referenz gespeichert (kann über `dsregcmd.exe /status` angezeigt werden) und das Gerätezertifikat im Persönlichen Speicher des Computers installiert. Nach Abschluss der Geräteregistrierung wird der Prozess mit der MDM-Registrierung fortgesetzt. |

## <a name="azure-ad-joined-in-federated-environments"></a>In Verbundumgebungen eingebundenes Azure AD

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-federated.png" alt-text="In Azure AD eingebundener Geräteflow in einer Verbundumgebung" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-federated.png":::

| Phase | BESCHREIBUNG |
| :----: | :----------- |
| Ein | Die gängigste Methode zur Registrierung von in Azure AD eingebundenen Geräten wird während der Out-of-Box-Experience (OOBE) ausgeführt, bei der die Azure AD Join-Webanwendung in die Cloud Experience Host-Anwendung (CXH) geladen wird. Die Anwendung sendet eine GET-Anforderung an den Azure AD OpenID-Konfigurationsendpunkt, um Autorisierungsendpunkte zu ermitteln. Azure AD gibt die OpenID-Konfiguration mit den Autorisierungsendpunkten als JSON-Dokument an die Anwendung zurück. |
| B | Die Anwendung erstellt eine Anmeldeanforderung für den Autorisierungsendpunkt und sammelt Benutzeranmeldeinformationen. |
| C | Nachdem der Benutzer seinen Benutzernamen (im UPN-Format) angegeben hat, sendet die Anwendung eine GET-Anforderung an Azure AD, um entsprechende Bereichsinformationen für den Benutzer zu ermitteln. Diese Informationen bestimmen, ob die Umgebung verwaltet wird oder im Verbund ist. Azure AD gibt die Informationen in einem JSON-Objekt zurück. Die Anwendung bestimmt, dass die Umgebung im Verbund ist.<br><br>Die Anwendung leitet an den Wert „AuthURL“ (lokale STS-Anmeldeseite) im zurückgegebenen JSON-Bereichsobjekt um. Die Anwendung sammelt Anmeldeinformationen über die STS-Webseite (Security Token Service, Sicherheitstokendienst). |
| D | Die Anwendung sendet die Anmeldeinformationen an den lokalen STS, wodurch zusätzliche Authentifizierungsfaktoren erforderlich werden können. Der lokale STS authentifiziert den Benutzer und gibt ein Token zurück. Die Anwendung sendet das Token zur Authentifizierung an Azure AD. Azure AD überprüft das Token und gibt ein ID-Token mit Ansprüchen zurück. |
| E | Die Anwendung sucht nach MDM-Nutzungsbedingungen (dem Anspruch „mdm_tou_url“). Falls vorhanden, ruft die Anwendung diese Bedingungen aus dem Wert des Anspruchs ab, stellt dem Benutzer den Inhalt bereit und wartet darauf, dass der Benutzer die Bedingungen akzeptiert. Dieser Schritt ist optional und wird übersprungen, wenn der Anspruch nicht vorhanden oder der Anspruchswert leer ist. |
| F | Die Anwendung sendet eine Anforderung zur Ermittlung der Geräteregistrierung an den Azure-Geräteregistrierungsdienst (Azure Device Registration Service, Azure DRS, ADRS). Azure DRS gibt ein Ermittlungsdatendokument zurück, das wiederum mandantenspezifische URIs zurückgibt, um die Geräteregistrierung abzuschließen. |
| G | Die Anwendung erstellt ein TPM-gebundenes (bevorzugtes) RSA 2048-Bit-Schlüsselpaar, das als „Geräteschlüssel“ („dkpub“/„dkpriv“) bezeichnet wird. Die Anwendung erstellt eine Zertifikatanforderung mithilfe von „dkpub“ und dem öffentlichen Schlüssel und signiert die Anforderung mithilfe von „dkpriv“. Als Nächstes leitet die Anwendung das zweite Schlüsselpaar vom Speicherstammschlüssel des TPM ab. Dieser Schlüssel ist der „Transportschlüssel“ („tkpub“/„tkpriv“). |
| H | Die Anwendung sendet eine Geräteregistrierungsanforderung mit dem ID-Token, der Zertifikatanforderung, „tkpub“ und den Nachweisdaten an Azure DRS. Azure DRS überprüft das ID-Token, erstellt eine Geräte-ID und dann basierend auf der enthaltenen Zertifikatanforderung ein Zertifikat. Anschließend schreibt Azure DRS ein Geräteobjekt in Azure AD und sendet die Geräte-ID und das Gerätezertifikat an den Client. |
| I | Die Geräteregistrierung wird durch den Empfang der Geräte-ID und des Gerätezertifikats von Azure DRS abgeschlossen. Die Geräte-ID wird als künftige Referenz gespeichert (kann über `dsregcmd.exe /status` angezeigt werden) und das Gerätezertifikat im Persönlichen Speicher des Computers installiert. Nach Abschluss der Geräteregistrierung wird der Prozess mit der MDM-Registrierung fortgesetzt. |

## <a name="hybrid-azure-ad-joined-in-managed-environments"></a>In verwaltete Umgebungen eingebundenes Azure AD Hybrid

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png" alt-text="In Azure AD Hybrid eingebundener Geräteflow in einer verwalteten Umgebung" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png":::

| Phase | BESCHREIBUNG |
| :----: | ----------- |
| Ein | Der Benutzer meldet sich mithilfe von Domänenanmeldeinformationen bei einem in die Domäne eingebundenen Windows 10-Computer an. Diese Anmeldeinformationen können ein Benutzername und Kennwort oder eine Smartcard-Authentifizierung sein. Die Benutzeranmeldung löst die Aufgabe „Automatische Geräteeinbindung“ aus. Die Aufgabe „Automatische Geräteeinbindung“ wird beim Domänenbeitritt ausgelöst und stündlich wiederholt. Dies ist nicht ausschließlich abhängig von der Benutzeranmeldung. |
| B | Die Aufgabe fragt Active Directory mithilfe des LDAP-Protokolls für das Attribut „keywords“ auf dem Dienstverbindungspunkt ab, der in Azure Directory in der Konfigurationspartition gespeichert ist (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`). Der im Attribut „keywords“ zurückgegebene Wert bestimmt, ob die Geräteregistrierung an den Azure-Geräteregistrierungsdienst (Azure Device Registration Service, Azure DRS, ADRS) oder den lokal gehosteten Unternehmensgeräteregistrierungsdienst geleitet wird. |
| C | Die Aufgabe erstellt für die verwaltete Umgebung Anmeldeinformationen zur anfänglichen Authentifizierung in Form eines selbstsignierten Zertifikats. Die Aufgabe schreibt das Zertifikat mithilfe von LDAP in das Attribut „userCertificate“ des Computerobjekts in Active Directory. |
| D | Der Computer kann sich bei Azure DRS erst dann authentifizieren, wenn in Azure AD ein Geräteobjekt erstellt wurde, das den Computer mit dem Zertifikat für das Attribut „userCertificate“ darstellt. Azure AD Connect erkennt eine Attributänderung. Im nächsten Synchronisierungszyklus sendet Azure AD Connect das Attribut „userCertificate“, die Objekt-GUID und die Computer-SID an Azure DRS. Azure DRS verwendet die Attributinformationen zum Erstellen eines Geräteobjekts in Azure AD. |
| E | Die Aufgabe „Automatischer Gerätebeitritt“ wird bei jeder Benutzeranmeldung oder stündlich ausgelöst. Sie versucht, den Computer mithilfe des entsprechenden privaten Schlüssels des öffentlichen Schlüssels im Attribut „userCertificate“ bei Azure AD zu authentifizieren. Azure AD authentifiziert den Computer und gibt ein ID-Token für ihn aus. |
| F | Die Aufgabe erstellt ein TPM-gebundenes (bevorzugtes) RSA 2048-Bit-Schlüsselpaar, das als „Geräteschlüssel“ („dkpub“/„dkpriv“) bezeichnet wird. Die Anwendung erstellt eine Zertifikatanforderung mithilfe von „dkpub“ und dem öffentlichen Schlüssel und signiert die Anforderung mithilfe von „dkpriv“. Als Nächstes leitet die Anwendung das zweite Schlüsselpaar vom Speicherstammschlüssel des TPM ab. Dieser Schlüssel ist der „Transportschlüssel“ („tkpub“/„tkpriv“). |
| G | Die Aufgabe sendet eine Geräteregistrierungsanforderung mit dem ID-Token, der Zertifikatanforderung, „tkpub“ und den Nachweisdaten an Azure DRS. Azure DRS überprüft das ID-Token, erstellt eine Geräte-ID und dann basierend auf der enthaltenen Zertifikatanforderung ein Zertifikat. Anschließend aktualisiert Azure DRS das Geräteobjekt in Azure AD und sendet die Geräte-ID und das Gerätezertifikat an den Client. |
| H | Die Geräteregistrierung wird durch den Empfang der Geräte-ID und des Gerätezertifikats von Azure DRS abgeschlossen. Die Geräte-ID wird als künftige Referenz gespeichert (kann über `dsregcmd.exe /status` angezeigt werden) und das Gerätezertifikat im Persönlichen Speicher des Computers installiert. Mit dem Abschluss der Geräteregistrierung wird die Aufgabe beendet. |

## <a name="hybrid-azure-ad-joined-in-federated-environments"></a>In Verbundumgebungen eingebundenes Azure AD Hybrid

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png" alt-text="In Azure AD Hybrid eingebundener Geräteflow in einer verwalteten Umgebung" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png":::

| Phase | BESCHREIBUNG |
| :----: | :----------- |
| Ein | Der Benutzer meldet sich mithilfe von Domänenanmeldeinformationen bei einem in die Domäne eingebundenen Windows 10-Computer an. Diese Anmeldeinformationen können ein Benutzername und Kennwort oder eine Smartcard-Authentifizierung sein. Die Benutzeranmeldung löst die Aufgabe „Automatische Geräteeinbindung“ aus. Die Aufgabe „Automatische Geräteeinbindung“ wird beim Domänenbeitritt ausgelöst und stündlich wiederholt. Dies ist nicht ausschließlich abhängig von der Benutzeranmeldung. |
| B | Die Aufgabe fragt Active Directory mithilfe des LDAP-Protokolls für das Attribut „keywords“ auf dem Dienstverbindungspunkt ab, der in Azure Directory in der Konfigurationspartition gespeichert ist (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`). Der im Attribut „keywords“ zurückgegebene Wert bestimmt, ob die Geräteregistrierung an den Azure-Geräteregistrierungsdienst (Azure Device Registration Service, Azure DRS, ADRS) oder den lokal gehosteten Unternehmensgeräteregistrierungsdienst geleitet wird. |
| C | Bei den Verbundumgebungen authentifiziert der Computer den Endpunkt für Unternehmensgeräteregistrierung mithilfe von integrierter Windows-Authentifizierung. Der Unternehmensgeräteregistrierungsdienst erstellt ein Token, das Ansprüche für die Objekt-GUID, die Computer-SID und den mit der Domäne verknüpften Zustand enthält, und gibt es zurück. Die Aufgabe übermittelt das Token und die Ansprüche an Azure AD, wo sie überprüft werden. Azure  AD gibt ein ID-Token an die gerade ausgeführte Aufgabe zurück. |
| D | Die Anwendung erstellt ein TPM-gebundenes (bevorzugtes) RSA 2048-Bit-Schlüsselpaar, das als „Geräteschlüssel“ („dkpub“/„dkpriv“) bezeichnet wird. Die Anwendung erstellt eine Zertifikatanforderung mithilfe von „dkpub“ und dem öffentlichen Schlüssel und signiert die Anforderung mithilfe von „dkpriv“. Als Nächstes leitet die Anwendung das zweite Schlüsselpaar vom Speicherstammschlüssel des TPM ab. Dieser Schlüssel ist der „Transportschlüssel“ („tkpub“/„tkpriv“). |
| E | Zum Bereitstellen von SSO für lokale Verbundanwendungen fordert die Aufgabe ein Unternehmens-PRT (Primary Refresh Token, Primäres Aktualisierungstoken) vom lokalen STS an. Windows Server 2016-Server, auf denen die Rolle „Active Directory-Verbunddienste (AD FS)“ ausgeführt wird, überprüfen die Anforderung und geben sie an die gerade ausgeführte Aufgabe zurück. |
| F | Die Aufgabe sendet eine Geräteregistrierungsanforderung mit dem ID-Token, der Zertifikatanforderung, „tkpub“ und den Nachweisdaten an Azure DRS. Azure DRS überprüft das ID-Token, erstellt eine Geräte-ID und dann basierend auf der enthaltenen Zertifikatanforderung ein Zertifikat. Anschließend schreibt Azure DRS ein Geräteobjekt in Azure AD und sendet die Geräte-ID und das Gerätezertifikat an den Client. Die Geräteregistrierung wird durch den Empfang der Geräte-ID und des Gerätezertifikats von Azure DRS abgeschlossen. Die Geräte-ID wird als künftige Referenz gespeichert (kann über `dsregcmd.exe /status` angezeigt werden) und das Gerätezertifikat im Persönlichen Speicher des Computers installiert. Mit dem Abschluss der Geräteregistrierung wird die Aufgabe beendet. |
| G | Wenn das Geräterückschreiben in Azure AD Connect aktiviert ist, fordert Azure AD Connect Updates von Azure AD bei seinem nächsten Synchronisierungszyklus an (das Geräterückschreiben ist für die Hybridbereitstellung mithilfe von Zertifikatvertrauen erforderlich). Azure AD korreliert das Geräteobjekt mit einem übereinstimmenden synchronisierten Computerobjekt. Azure AD Connect empfängt das Geräteobjekt mit der Objekt-GUID und Computer-SID und schreibt es in Active Directory. |

## <a name="next-steps"></a>Nächste Schritte

- [In Azure AD eingebundene Geräte](concept-azure-ad-join.md)
- [Bei Azure AD registrierte Geräte](concept-azure-ad-register.md)
- [In Azure AD eingebundene Hybridgeräte](concept-azure-ad-join-hybrid.md)
- [Was ist ein primäres Aktualisierungstoken (Primary Refresh Token, PRT)?](concept-primary-refresh-token.md)
- [Azure AD Connect: Geräteoptionen](../hybrid/how-to-connect-device-options.md)

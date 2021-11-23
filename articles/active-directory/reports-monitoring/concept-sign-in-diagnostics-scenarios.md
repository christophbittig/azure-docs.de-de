---
title: Anmeldediagnose für Azure AD Szenarien
description: Eine Auflistung der Szenarien, die von der Anmeldediagnose für Azure AD unterstützt werden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/12/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab25e7150f7930f85df846ece50a3013c0e45818
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399729"
---
# <a name="sign-in-diagnostics-for-azure-ad-scenarios"></a>Anmeldediagnose für Azure AD Szenarien

Mit der Anmeldediagnose können Sie analysieren, was bei einem Anmeldeversuch passiert ist, und Empfehlungen zur Behebung von Problemen erhalten, ohne den Microsoft-Support einzubeziehen.  

In diesem Artikel erhalten Sie eine Übersicht über die Typen von Szenarien, die Sie identifizieren und lösen können, wenn Sie dieses Tool verwenden.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Die Anmeldediagnose für Azure AD bietet Unterstützung für die folgenden Szenarien:

- **Bedingter Zugriff**

    - Blockiert durch bedingten Zugriff  

    - Fehlerhafte Anmeldung durch bedingten Zugriff  

    - Mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) bei bedingtem Zugriff  

    - B2B blockiert die Anmeldung aufgrund des bedingten Zugriffs 

- **Mehrstufige Authentifizierung (MFA)**  

    - Mehrstufige Authentifizierung (MFA) aufgrund sonstiger Anforderungen erforderlich  

    - MFA-Nachweis erforderlich  

    - MFA-Nachweis erforderlich (als Risikoanmeldung eingestufter Standort)  

- **Korrekte und Inkorrekte Anmeldeinformationen**  

    - Erfolgreiche Anmeldung  

    - Gesperrtes Konto  

    - Ungültiger Benutzername oder ungültiges Kennwort  

- **Unternehmens-Apps**  

    - Enterprise-Apps-Dienstanbieter  

    - Enterprise-Apps-Konfiguration  

- **Andere Szenarien**   

    - Sicherheitsstandards  
    
    - Einblicke in Fehlercode  

    - Legacyauthentifizierung  
    
    - B2B blockiert die Anmeldung aufgrund des bedingten Zugriffs

    - Blockiert durch Benutzerrisikorichtlinie 
    
    - Passthrough-Authentifizierung
    
    - Nahtloses einmaliges Anmelden







## <a name="conditional-access"></a>Bedingter Zugriff  


### <a name="blocked-by-conditional-access"></a>Blockiert durch bedingten Zugriff 

In diesem Szenario wurde ein Anmeldeversuch durch eine Richtlinie für bedingten Zugriff blockiert. 


![Screenshot: Zugriffskonfiguration mit Auswahl von „Zugriff blockieren“](./media/concept-sign-in-diagnostics-scenarios/block-access.png)

Der Diagnoseabschnitt für dieses Szenario enthält Einzelheiten zum Benutzeranmeldeereignis und zu den angewendeten Richtlinien. 

 

### <a name="failed-conditional-access"></a>Fehler beim bedingten Zugriff 

Dieses Szenario ist in der Regel auf einen Anmeldeversuch zurückzuführen, bei dem ein Fehler aufgetreten ist, da die Anforderungen einer Richtlinie für bedingten Zugriff nicht erfüllt wurden. Typische Beispiele: 



![Screenshot: Zugriffskonfiguration mit Beispielen für häufige Richtlinien und Auswahl von „Zugriff gewähren“](./media/concept-sign-in-diagnostics-scenarios/require-controls.png)

- Gerät mit Hybrid-Azure AD-Einbindung erforderlich 

- Genehmigte Client-App erforderlich 

- App-Schutzrichtlinie erforderlich 

Der Diagnoseabschnitt für dieses Szenario enthält Einzelheiten zum Anmeldeversuch des Benutzers und zu den angewendeten Richtlinien. 

 

### <a name="mfa-from-conditional-access"></a>Mehrstufige Authentifizierung (MFA) für bedingten Zugriff erforderlich 

In diesem Szenario verfügt eine Richtlinie für bedingten Zugriff über die Anforderung zum Anmelden per mehrstufiger Authentifizierung. 



![Screenshot: Zugriffskonfiguration mit Auswahl von „Multi-Factor Authentication erforderlich“](./media/concept-sign-in-diagnostics-scenarios/require-mfa.png)

Der Diagnoseabschnitt für dieses Szenario enthält Einzelheiten zum Anmeldeversuch des Benutzers und zu den angewendeten Richtlinien. 

 

 

## <a name="multifactor-authentication"></a>Mehrstufige Authentifizierung  

### <a name="mfa-from-other-requirements"></a>Mehrstufige Authentifizierung (MFA) aufgrund sonstiger Anforderungen erforderlich 

In diesem Szenario wurde keine Anforderung zur Verwendung der mehrstufigen Authentifizierung von einer Richtlinie für bedingten Zugriff erzwungen. Ein Beispiel hierfür ist die mehrstufige Authentifizierung pro Benutzer. 



![Screenshot: Konfiguration der mehrstufigen Authentifizierung pro Benutzer](./media/concept-sign-in-diagnostics-scenarios/mfa-per-user.png) 

Dieses Diagnoseszenario soll weitere Einzelheiten zu folgenden Informationen bereitstellen: 

- Die Quelle der Unterbrechung der mehrstufigen Authentifizierung 

- Ergebnis der Clientinteraktion 

Sie können auch alle Details des Anmeldeversuchs eines Benutzers anzeigen. 

 

### <a name="mfa-proof-up-required"></a>MFA-Nachweis erforderlich 

In diesem Szenario wurden Anmeldeversuche durch Anforderungen zum Einrichten der mehrstufigen Authentifizierung unterbrochen. Diese Einrichtung wird auch als „Nachweis“ (Englisch: Proof Up) bezeichnet. 

 

„MFA-Nachweis erforderlich“ tritt auf, wenn ein Benutzer die mehrstufige Authentifizierung verwenden muss, sie aber noch nicht konfiguriert hat, oder wenn ein Administrator das Konfigurieren der mehrstufigen Authentifizierung für den Benutzer obligatorisch gemacht hat. 

 

Bei diesem Diagnoseszenario soll gezeigt werden, dass der Grund für die Unterbrechung der mehrstufigen Authentifizierung die fehlende Benutzerkonfiguration war. Als Lösung wird empfohlen, dass der Benutzer den Nachweisvorgang durchführen soll. 

 

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>MFA-Nachweis erforderlich (als Risikoanmeldung eingestufter Standort) 

In diesem Szenario wurden Anmeldeversuche durch eine Anforderung zum Einrichten der mehrstufigen Authentifizierung an einem riskanten Anmeldestandort unterbrochen. 

 

Bei diesem Diagnoseszenario soll gezeigt werden, dass der Grund für die Unterbrechung der mehrstufigen Authentifizierung die fehlende Benutzerkonfiguration war. Als Lösung wird empfohlen, dass der Benutzer den Nachweisvorgang durchführen soll, und zwar von einem Netzwerkstandort aus, der nicht als riskant eingestuft wird. 

 

Ein Beispiel für dieses Szenario ist, wenn die Richtlinie erfordert, dass der Benutzer MFA nur von vertrauenswürdigen Netzwerkstandorten aus einrichtet, der Benutzer sich jedoch von einem nicht vertrauenswürdigen Netzwerkstandort aus anmeldet. 

 

## <a name="correct--incorrect-credential"></a>Korrekte und Inkorrekte Anmeldeinformation

### <a name="successful-sign-in"></a>Erfolgreiche Anmeldung 

In diesem Szenario wurden Anmeldeereignisse nicht durch den bedingten Zugriff oder die mehrstufige Authentifizierung unterbrochen.  

 

In diesem Diagnoseszenario werden Details zu Benutzeranmeldeereignissen bereitgestellt, für die erwartet wird, dass sie aufgrund von Richtlinien für bedingten Zugriff oder die mehrstufige Authentifizierung unterbrochen werden. 

 

### <a name="the-account-is-locked"></a>Das Konto ist gesperrt 

In diesem Szenario hat sich ein Benutzer zu oft mit falschen Anmeldeinformationen angemeldet. Dieses Szenario tritt auf, wenn zu viele kennwortbasierte Anmeldeversuche mit falschen Anmeldeinformationen aufgetreten sind. Das Diagnoseszenario stellt Informationen für den Administrator bereit, um zu bestimmen, woher die Versuche stammen und ob es sich um legitime Benutzeranmeldungsversuche handelt. 

 

Dieses Diagnoseszenario enthält Details zu den Apps, der Anzahl der Versuche, dem verwendeten Gerät, dem Betriebssystem und der IP-Adresse. 

 

Weitere Informationen zu diesem Thema finden Sie in der Azure AD Smart Lockout-Dokumentation. 

 

 

### <a name="invalid-username-or-password"></a>Ungültiger Benutzername oder ungültiges Kennwort 

In diesem Szenario hat ein Benutzer versucht, sich mit einem ungültigen Benutzernamen oder Kennwort anzumelden. Die Diagnose soll es einem Administrator ermöglichen, festzustellen, ob das Problem darin besteht, dass ein Benutzer falsche Anmeldeinformationen eingibt, oder ob ein Client und/oder Anwendungen, die ein altes Kennwort zwischengespeichert haben und es erneut übermitteln. 

 

Dieses Diagnoseszenario enthält Details zu den Apps, der Anzahl der Versuche, dem verwendeten Gerät, dem Betriebssystem und der IP-Adresse. 

 

## <a name="enterprise-app"></a>Unternehmens-App 

In Unternehmensanwendungen gibt es zwei Punkte, an denen Probleme auftreten können: 

- Die Anwendungskonfiguration des Identitätsanbieters (Azure AD) 
- Die Seite des Dienstanbieters (Anwendungsdienst, auch als SaaS-Anwendung bezeichnet)

 

Bei der Diagnose für diese Probleme geht es darum, welche Seite des Problems zur Lösung untersucht werden sollte und was zu tun ist. 

 

### <a name="enterprise-apps-service-provider"></a>Enterprise-Apps-Dienstanbieter 

In diesem Szenario hat ein Benutzer versucht, sich bei einer Anwendung anzumelden. Fehler bei der Anmeldung aufgrund eines Problems mit der Anwendungsseite (auch als Dienstanbieter bezeichnet) des Anmeldeablaufs. Probleme, die bei dieser Diagnose erkannt werden, müssen in der Regel durch Ändern der Konfiguration oder Beheben von Problemen im Anwendungsdienst gelöst werden.  

Lösung für dieses Szenario bedeutet, dass Sie sich beim anderen Dienst anmelden und eine Konfiguration gemäß den Diagnoseleitfäden ändern. 

 

### <a name="enterprise-apps-configuration"></a>Enterprise-Apps-Konfiguration 

In diesem Szenario ist eine Anmeldung aufgrund eines Anwendungskonfigurationsproblems für die Azure AD Seite der Anwendung fehlgeschlagen. 

 

Die Lösung für dieses Szenario erfordert das Überprüfen und Aktualisieren der Konfiguration der Anwendung im Blatteintrag Enterprise Anwendungen für die Anwendung. 

 

## <a name="other-scenarios"></a>Andere Szenarien 

### <a name="security-defaults"></a>Sicherheitsstandards 

In diesem Szenario werden Anmeldeereignisse behandelt, bei denen die Anmeldung des Benutzers aufgrund von Einstellungen für Sicherheitsstandards unterbrochen wurde. Sicherheitsstandards erzwingen die Sicherheit bewährter Methoden für Ihr Unternehmen und erfordern die Konfiguration und Verwendung der mehrstufigen Authentifizierung (Multifactor Authentication, MFA) in vielen Szenarien, um zu verhindern, dass Kennwort-Sprays, Replay-Angriffe und Phishingversuche erfolgreich sind. 

Weitere Informationen finden Sie unter [Was sind Sicherheitsstandards?](../fundamentals/concept-fundamentals-security-defaults.md). 

### <a name="error-code-insights"></a>Einblicke in Fehlercode 

Wenn ein Ereignis keine kontextbezogene Analyse in der Anmeldediagnose aufweist, wird möglicherweise eine aktualisierte Fehlercodeerklärung und relevanter Inhalt angezeigt. Die Fehlercode-Erkenntnisse enthalten detaillierten Text zum Szenario, zur Behebung des Problems und alle Inhalte, die in Bezug auf das Problem gelesen werden müssen. 

### <a name="legacy-authentication"></a>Legacyauthentifizierung 

In diesem Diagnoseszenario wird ein Anmeldeereignis diagnostiziert, das blockiert oder unterbrochen wurde, da der Client versucht hat, die Standardauthentifizierung (auch als Legacyauthentifizierung bezeichnet) zu verwenden. 

Als bewährte Sicherheitsmethode empfehlen wir Ihnen, Anmeldungen per Legacy-Authentifizierung zu verhindern. Bei Legacy-Authentifizierungsprotokollen, z. B. POP, SMTP, IMAP und MAPI, kann die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) nicht erzwungen werden. Aus diesem Grund werden diese Protokolle bevorzugt für Angriffe auf Ihr Unternehmen genutzt. 

Weitere Informationen finden Sie unter [Gewusst wie: Blockieren der Legacyauthentifizierung bei Azure AD mit bedingtem Zugriff](../conditional-access/block-legacy-authentication.md). 

### <a name="b2b-blocked-sign-in-due-to-conditional-access"></a>B2B blockiert die Anmeldung aufgrund des bedingten Zugriffs 

In diesem Diagnoseszenario wird eine blockierte oder unterbrochene Anmeldung erkannt, weil der Benutzer aus einem anderen Unternehmen stammt (B2B-Anmeldung), bei der eine Richtlinie für bedingten Zugriff erfordert, dass das Gerät des Clients mit dem Ressourcenmandanten verknüpft ist. 

Weitere Informationen finden Sie unter [Bedingter Zugriff für Benutzer der B2B-Zusammenarbeit](../external-identities/conditional-access.md). 

### <a name="blocked-by-risk-policy"></a>Blockiert durch Benutzerrisikorichtlinie 

In diesem Szenario blockiert die Identity Protection-Richtlinie einen Anmeldeversuch, da der Anmeldeversuch als riskant identifiziert wurde. 

Weitere Informationen finden Sie unter [Gewusst wie: Konfigurieren und Aktivieren von Richtlinien](../identity-protection/howto-identity-protection-configure-risk-policies.md). 


### <a name="pass-through-authentication"></a>Passthrough-Authentifizierung 

Da bei der Passtrough-Authentifizierung lokale und cloudbasierte Authentifizierungstechnologien integriert werden, kann es schwierig sein, die Ursache des Problems zu ermitteln. Diese Diagnose soll das Diagnostizieren und Auflösen dieser Szenarien vereinfachen. 

In diesem Diagnoseszenario werden benutzerspezifische Anmeldeprobleme identifiziert, wenn als Authentifizierungsmethode die Passthrough-Authentifizierung (Pass-Through Authentication, PTA) verwendet wird und ein PTA-spezifischer Fehler auftritt. Fehler aufgrund anderer Probleme werden auch dann ordnungsgemäß diagnostiziert, wenn die PTA-Authentifizierung verwendet wird. 

Bei der Diagnose werden Kontextinformationen zum Fehler und zum angemeldeten Benutzer, zusätzliche Gründe für Anmeldefehler sowie empfohlene Aktionen angezeigt, die der Administrator ergreifen kann, um das Problem zu beheben. Weitere Informationen finden Sie unter [Behandlung von Problemen bei der Azure Active Directory-Passthrough-Authentifizierung](../hybrid/tshoot-connect-pass-through-authentication.md). 


### <a name="seamless-single-sign-on"></a>Nahtloses einmaliges Anmelden

Durch nahtloses einmaliges Anmelden wird die Kerberos-Authentifizierung in die Cloudauthentifizierung integriert. In diesem Szenario kommen zwei Authentifizierungsprotokolle zum Einsatz. Daher kann es schwierig sein, nachzuvollziehen, wo der Fehlerpunkt liegt, wenn Probleme bei der Anmeldung auftreten. Diese Diagnose soll das Diagnostizieren und Auflösen dieser Szenarien vereinfachen.
 
In diesem Diagnoseszenario werden der Kontext des Anmeldefehlers und die spezifische Fehlerursache, Kontextinformationen zum Anmeldeversuch und vorgeschlagene Aktionen untersucht, die der Administrator lokal oder in der Cloud zum Beheben des Problems ausführen kann. Weitere Informationen finden Sie unter [Problembehandlung beim nahtlosen einmaligen Anmelden mit Azure Active Directory](../hybrid/tshoot-connect-sso.md). 
 







## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Anmeldediagnose für Azure AD?](overview-sign-in-diagnostics.md)

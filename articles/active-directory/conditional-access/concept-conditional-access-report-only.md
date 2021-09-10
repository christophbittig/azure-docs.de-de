---
title: Was ist der reine Berichtsmodus des bedingten Zugriffs? – Azure Active Directory
description: Vorteile des reinen Berichtsmodus bei der Bereitstellung von Richtlinien für bedingten Zugriff
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ac2304ffd8f45d2147822f55f35c965da2db7a9
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005220"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Was ist der reine Berichtsmodus des bedingten Zugriffs?

Der bedingte Zugriff wird von unseren Kunden häufig verwendet, um durch die Anwendung der richtigen Zugriffssteuerungen unter den richtigen Umständen für die Sicherheit zu sorgen. Eine der Herausforderungen beim Bereitstellen einer Richtlinie für bedingten Zugriff in Ihrer Organisation ist jedoch das Ermitteln der Auswirkungen auf die Endbenutzer. Es kann schwierig sein, die Anzahl und die Namen von Benutzern zu antizipieren, die von häufigen Bereitstellungsinitiativen betroffen sind, wie z. B. das Blockieren der Legacyauthentifizierung, das Erzwingen der mehrstufigen Authentifizierung für eine Gruppe von Benutzern oder das Implementieren von Anmelderisikorichtlinien. 

Der reine Berichtsmodus ist ein neuer Status von Richtlinien für bedingten Zugriff, mit dem Administratoren die Auswirkungen von Richtlinien für bedingten Zugriff auswerten können, bevor sie die jeweiligen Richtlinien in ihrer Umgebung aktivieren.  Durch die Einführung des reinen Berichtsmodus ergeben sich die folgenden Vorteile:

- Richtlinien für bedingten Zugriff können im reinen Berichtsmodus aktiviert werden. Dies gilt nicht für den Bereich „Benutzeraktionen“.
- Bei der Anmeldung werden Richtlinien im reinen Berichtsmodus ausgewertet, aber nicht erzwungen.
- Die Ergebnisse werden auf den Registerkarten **Bedingter Zugriff** und **Nur melden** der Anmeldeprotokolldetails protokolliert.
- Kunden mit einem Azure Monitor-Abonnement können die Auswirkungen ihrer Richtlinien für bedingten Zugriff mithilfe der Arbeitsmappe für Erkenntnisse zum bedingten Zugriff überwachen.

> [!VIDEO https://www.youtube.com/embed/NZbPYfhb5Kc]

> [!WARNING]
> Richtlinien im reinen Berichtsmodus, die konforme Geräte erfordern, können Benutzer von Mac-, iOS- und Android-Geräten auffordern, bei der Richtlinienauswertung ein Gerätezertifikat auszuwählen, auch wenn die Gerätekonformität nicht erzwungen wird. Diese Aufforderungen können wiederholt werden, bis das Gerät konform ist. Um zu verhindern, dass Endbenutzer bei der Anmeldung Eingabeaufforderungen erhalten, schließen Sie die Geräteplattformen Mac, iOS und Android aus Richtlinien im reinen Berichtsmodus aus, die Gerätekonformitätsprüfungen durchführen. Beachten Sie, dass der reine Berichtsmodus für Richtlinien für bedingten Zugriff mit dem Bereich „Benutzeraktionen“ nicht anwendbar ist.

![Registerkarte „Nur Bericht“ im Azure AD-Anmeldeprotokoll](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Richtlinienergebnisse

Wenn eine Richtlinie im reinen Berichtsmodus für eine bestimmte Anmeldung ausgewertet wird, gibt es vier neue mögliche Ergebniswerte:

| Ergebnis | BESCHREIBUNG |
| --- | --- |
| Nur Bericht: Erfolg | Alle konfigurierten Richtlinienbedingungen, alle erforderlichen nicht interaktiven Steuerelemente zur Rechteerteilung und alle Sitzungssteuerelemente wurden erfüllt. Beispiele: Eine Anforderung zur mehrstufigen Authentifizierung wird durch einen MFA-Anspruch erfüllt, der bereits im Token vorhanden ist, oder eine Richtlinie für konforme Geräte wird durch Ausführen einer Geräteprüfung auf einem konformen Gerät erfüllt. |
| Nur Bericht: Fehler | Alle konfigurierten Richtlinienbedingungen wurden erfüllt, aber es wurden nicht alle erforderlichen nicht interaktiven Steuerelemente zur Rechteerteilung oder Sitzungssteuerelemente erfüllt. Beispiele: Eine Richtlinie gilt für einen Benutzer, für den eine Blockierungssteuerung konfiguriert ist, oder ein Gerät entspricht nicht einer Richtlinie für konforme Geräte. |
| Nur Bericht: Benutzeraktion erforderlich | Alle konfigurierten Richtlinienbedingungen wurden erfüllt, aber es ist eine Benutzeraktion erforderlich, um die erforderlichen Steuerelemente zur Rechteerteilung oder Sitzungssteuerelemente zu erfüllen. Im reinen Berichtsmodus wird der Benutzer nicht aufgefordert, die erforderlichen Maßnahmen zu erfüllen. Beispielsweise werden Benutzer nicht zur mehrstufigen Authentifizierung oder zur Bestätigung der Nutzungsbedingungen aufgefordert.   |
| Nur Bericht: Nicht angewendet | Es wurden nicht alle konfigurierten Richtlinienbedingungen erfüllt. Beispiele: Der Benutzer wird aus der Richtlinie ausgeschlossen, oder die Richtlinie gilt nur für bestimmte vertrauenswürdige benannte Standorte. |

## <a name="conditional-access-insights-workbook"></a>Arbeitsmappe für Erkenntnisse zum bedingten Zugriff

Administratoren haben die Möglichkeit, mehrere Richtlinien im reinen Berichtsmodus zu erstellen. Daher müssen Sie sowohl die einzelnen Auswirkungen jeder Richtlinie als auch die kombinierten Auswirkungen mehrerer Richtlinien verstehen, die zusammen ausgewertet werden. Mit der neuen Arbeitsmappe für Erkenntnisse zum bedingten Zugriff können Administratoren Abfragen für den bedingten Zugriff visualisieren und die Auswirkungen einer Richtlinie für einen bestimmten Zeitraum, für eine Gruppe von Anwendungen und für bestimmte Benutzer überwachen. 
 
## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren einer Richtlinie für bedingten Zugriff im reinen Berichtsmodus](howto-conditional-access-insights-reporting.md)

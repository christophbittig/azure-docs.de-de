---
title: Überwachen und Behandeln von Problemen bei Anmeldungen mit fortlaufender Zugriffsevaluierung in Azure AD
description: Problembehandlung und Reaktion auf Benutzerstatusänderungen mit der fortlaufenden Zugriffsevaluierung in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu, shreyamalik
ms.collection: M365-identity-device-management
ms.openlocfilehash: 292860ed26a39205f221c213f392b4b120006d13
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344767"
---
# <a name="monitor-and-troubleshoot-continuous-access-evaluation"></a>Überwachung und Problembehandlung von fortlaufender Zugriffsevaluierung

Administratoren können Anmeldeereignisse überwachen und Probleme beheben, bei denen die [fortlaufende Zugriffsevaluierung (Continuous Access Evaluation, CAE)](concept-continuous-access-evaluation.md) auf verschiedene Weise angewendet wird.

## <a name="continuous-access-evaluation-sign-in-reporting"></a>Berichterstellung für die Anmeldung bei der fortlaufenden Zugriffsevaluierung

Administratoren haben die Möglichkeit, Benutzeranmeldungen zu überwachen, bei denen die fortlaufende Zugriffsevaluierung angewendet wird. Dieser Bereich kann über die folgenden Anweisungen gefunden werden:

1.  Melden Sie sich als Administrator für bedingten Zugriff, Sicherheitsadministrator oder globaler Administrator beim **Azure-Portal** an.
1.  Navigieren Sie zu **Azure Active Directory** > **Anmeldungen**. 
1.  Wenden Sie den Filter **Is CAE Token** (Ist ein CAE-Token) an. 

[ ![Fügen Sie dem Protokoll „Sitn-ins“ einen Filter hinzu, um zu prüfen, wo CAE angewendet wird.](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png#lightbox)

Von hier aus werden Administratoren Informationen zu den Anmeldeereignissen ihres Benutzers angezeigt. Wählen Sie eine beliebige Anmeldung aus, um Details zur Sitzung anzuzeigen, z. B. welche Richtlinien für bedingten Zugriff angewendet wurden und ob die fortlaufende Zugriffsevaluierung aktiviert ist. 

Es gibt mehrere Anmeldeanforderungen für jede Authentifizierung. Einige werden auf der interaktiven Registerkarte angezeigt, während andere auf der nicht-interaktiven Registerkarte angezeigt werden. CAE wird nur für eine der Anfragen als wahr angezeigt, und zwar entweder auf der interaktiven oder der nicht-interaktiven Registerkarte. Administratoren müssen beide Registerkarten überprüfen, um festzustellen, ob die Authentifizierung des Benutzers CAE aktiviert ist oder nicht. 

### <a name="searching-for-specific-sign-in-attempts"></a>Suchen nach bestimmten Anmeldeversuchen

Verwenden Sie Filter, um Ihre Suche einzugrenzen. Wenn sich z. B. ein Benutzer bei Teams angemeldet hat, verwenden Sie den Filter für die Anwendung, und legen Sie ihn auf „Teams“ fest. Administratoren müssen die Anmeldungen möglicherweise sowohl auf interaktiven als auch auf nicht interaktiven Registerkarten überprüfen, um die spezifische Anmeldung zu finden. Um die Suche weiter einzugrenzen, können Administratoren mehrere Filter anwenden.

## <a name="continuous-access-evaluation-workbooks"></a>Arbeitsmappen für die fortlaufende Zugriffsevaluierung

Mit der Arbeitsmappe zu Erkenntnissen der fortlaufenden Zugriffsevaluierung können Administratoren Erkenntnisse zur CAE-Nutzung für ihre Mandanten anzeigen und überwachen. In der Tabelle werden Authentifizierungsversuche mit IP-Konflikten angezeigt. Diese Arbeitsmappe befindet sich als Vorlage unter der Kategorie „Bedingter Zugriff“. 

### <a name="accessing-the-cae-workbook-template"></a>Zugreifen auf die CAE-Arbeitsmappenvorlage

Die Log Analytics-Integration muss abgeschlossen sein, bevor Arbeitsmappen angezeigt werden. Weitere Informationen zum Streamen von Azure AD Anmeldeprotokollen in einen Log Analytics-Arbeitsbereich finden Sie im Artikel [Integrieren von Azure AD-Protokollen in Azure Monitor-Protokolle](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).
 
1.  Melden Sie sich als Administrator für bedingten Zugriff, Sicherheitsadministrator oder globaler Administrator beim **Azure-Portal** an. 
1.  Navigieren Sie zu **Azure Active Directory** > **Arbeitsmappen**.
1.  Suchen Sie unter **Öffentliche Vorlagen** nach **Erkenntnisse zur fortlaufenden Zugriffsevaluierung**.

[ ![Suchen Sie die Arbeitsmappe für CAE-Erkenntnisse im Katalog, um die Überwachung fortzusetzen.](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png#lightbox)

Die Arbeitsmappe **Erkenntnisse zur fortlaufenden Zugriffsevaluierung** enthält die folgende Tabelle:

### <a name="potential-ip-address-mismatch-between-azure-ad-and-resource-provider"></a>Potenzielle IP-Adresskonflikte zwischen Azure AD und Ressourcenanbieter  

![Arbeitsmappentabelle 1 mit potenziellen IP-Adresskonflikten](./media/howto-continuous-access-evaluation-troubleshoot/continuous-access-evaluation-insights-workbook-table-1.png)

Die potenziellen IP-Adresskonflikte zwischen der Azure AD- und der Ressourcenanbietertabelle ermöglichen es Administratoren, Sitzungen zu untersuchen, bei denen die von Azure AD erkannte IP-Adresse nicht mit der vom Ressourcenanbieter erkannten IP-Adresse übereinstimmen. 

Diese Tabelle in der Arbeitsmappe gibt Aufschluss über diese Szenarien, indem sie die jeweiligen IP-Adressen anzeigt und angibt, ob während der Sitzung ein CAE-Token ausgestellt wurde. 

#### <a name="ip-address-configuration"></a>IP-Adresskonfiguration

Im Identitäts- und Ressourcenanbieter werden möglicherweise andere IP-Adressen angezeigt. Dieser Konflikt kann aufgrund der folgenden Beispiele vorkommen:

- Ihr Netzwerk implementiert getrenntes Tunneln.
- Ihr Ressourcenanbieter verwendet eine IPv6-Adresse und Azure AD eine IPv4-Adresse.
- Aufgrund von Netzwerkkonfigurationen sieht Azure AD eine IP-Adresse vom Client und Ihr Ressourcenanbieter sieht eine andere IP-Adresse vom Client.

Wenn dieses Szenario in Ihrer Umgebung besteht, um Endlosschleifen zu vermeiden, wird in Azure AD ein einstündiges CAE-Token ausgegeben und während dieses Zeitraums keine Änderung des Clientstandorts erzwungen. Selbst in diesem Fall ist die Sicherheit im Vergleich zu herkömmlichen einstündigen Token verbessert, da neben den Ereignissen der Änderung des Clientstandorts die anderen Ereignisse weiterhin ausgewertet werden.

Administratoren können Datensätze anzeigen, die nach Zeitbereich und Anwendung gefiltert sind. Administratoren können die Anzahl der erkannten nicht übereinstimmenden IP-Adressen mit der Gesamtzahl der Anmeldungen während eines angegebenen Zeitraums vergleichen. 

Um die Blockierung von Benutzern aufzuheben, können Administratoren einem vertrauenswürdigen benannten Standort bestimmte IP-Adressen hinzufügen.

1.  Melden Sie sich als Administrator für bedingten Zugriff, Sicherheitsadministrator oder globaler Administrator beim **Azure-Portal** an. 
1.  Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff** > **Benannte Standorte**. Hier können Sie vertrauenswürdige IP-Standorte erstellen oder aktualisieren.

> [!NOTE]
> Bevor Sie eine IP-Adresse als vertrauenswürdigen benannten Standort hinzufügen, vergewissern Sie sich, dass die IP-Adresse tatsächlich zur vorgesehenen Organisation gehört.

Weitere Informationen zu benannten Standorten finden Sie im Artikel [Verwenden der Standortbedingung](location-condition.md#named-locations).
 
## <a name="next-steps"></a>Nächste Schritte

- [Integrieren von Azure AD-Protokollen mit Azure Monitor-Protokollen](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Verwenden der Standortbedingung](location-condition.md#named-locations)
- [Fortlaufende Zugriffsevaluierung](concept-continuous-access-evaluation.md)

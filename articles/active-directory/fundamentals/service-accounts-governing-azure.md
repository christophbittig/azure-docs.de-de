---
title: Steuern von Azure Active Directory-Dienstkonten
description: Hier finden Sie Informationen zu Prinzipien und Verfahren für die Verwaltung des Lebenszyklus von Dienstkonten in Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d3f6163cc8c5f642835562c631d1712f329a788
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292527"
---
# <a name="governing-azure-ad-service-accounts"></a>Steuern von Azure AD-Dienstkonten

In Azure Active Directory (Azure AD) gibt es drei Arten von Dienstkonten: [verwaltete Identitäten](service-accounts-managed-identities.md), [Dienstprinzipale](service-accounts-principal.md) und Benutzerkonten, die als Dienstkonten genutzt werden. Wenn Sie diese Dienstkonten für die automatisierte Verwendung erstellen, werden ihnen Berechtigungen für den Zugriff auf Ressourcen in Azure und Azure AD erteilt. Bei den Ressourcen kann es sich beispielsweise um Microsoft 365-Dienste, SaaS-Anwendungen (Software-as-a-Service), benutzerdefinierte Anwendungen, Datenbanken oder Personalsysteme handeln. Die Steuerung von Azure AD-Dienstkonten umfasst die Verwaltung ihrer Erstellung, ihrer Berechtigungen und ihres Lebenszyklus, um Sicherheit und Kontinuität zu gewährleisten.

> [!IMPORTANT] 
> Benutzerkonten sollten nicht als Dienstkonten verwendet werden, da sie grundsätzlich weniger sicher sind. Das gilt auch für lokale Dienstkonten, die mit Azure AD synchronisiert werden, da sie nicht in Dienstprinzipale konvertiert werden. Verwenden Sie stattdessen verwaltete Identitäten oder Dienstprinzipale. Hinweis: Es können noch keine Richtlinien für bedingten Zugriff mit Dienstprinzipalen verwendet werden. An dieser Funktion wird jedoch bereits gearbeitet.


## <a name="plan-your-service-account"></a>Planen Ihres Dienstkontos

Dokumentieren Sie vor der Erstellung eines Dienstkontos oder der Registrierung einer Anwendung die Schlüsselinformationen des Dienstkontos. Dokumentierte Informationen erleichtern die effektive Überwachung und Steuerung des Kontos. Es empfiehlt sich, die folgenden Daten zu sammeln und in Ihrer Konfigurationsverwaltungsdatenbank (Configuration Management Database, CMDB) nachzuverfolgen:

| Daten| BESCHREIBUNG| Details |
| - | - | - |
| Besitzer| Benutzer oder Gruppe, der bzw. die für die Verwaltung und Überwachung des Dienstkontos verantwortlich ist.| Erteilen Sie dem Besitzer die erforderlichen Berechtigungen für die Kontoüberwachung, und implementieren Sie eine Möglichkeit zur Problembehandlung. Die Problembehandlung kann vom Besitzer oder per Anfrage an die IT durchgeführt werden. |
| Zweck| Gibt an, wie das Konto verwendet wird.| Ordnen Sie das Dienstkonto einem bestimmten Dienst, einer bestimmten Anwendung oder einem bestimmten Skript zu. Vermeiden Sie die Erstellung von Mehrzweck-Dienstkonten. |
| Berechtigungen (Bereiche)| Voraussichtliche Berechtigungen.| Dokumentieren Sie die Ressourcen, auf die zugegriffen wird, sowie die Berechtigungen für diese Ressourcen. |
| CMDB-Link| Link zu den Ressourcen, auf die zugegriffen werden soll, sowie zu Skripts, in denen das Dienstkonto verwendet wird.| Dokumentieren Sie die Ressourcen- und Skriptbesitzer, um alle notwendigen Upstream- und Downstreamauswirkungen von Änderungen kommunizieren zu können. |
| Risikobewertung| Das Risiko und die geschäftlichen Auswirkungen im Falle einer Kompromittierung des Kontos.| Verwenden Sie diese Informationen, um den Berechtigungsbereich einzugrenzen und zu bestimmen, wer Zugriff auf die Kontoinformationen haben soll. |
| Überprüfungszeitraum| Der Zeitplan für die Überprüfung des Dienstkontos durch den Besitzer.| Verwenden Sie diese Information, um die Überprüfungskommunikation und Überprüfungen zu planen. Dokumentieren Sie, was geschehen soll, wenn bis zu einem bestimmten Zeitpunkt nach dem geplanten Überprüfungszeitraum keine Überprüfung stattgefunden hat. |
| Gültigkeitsdauer| Die voraussichtliche maximale Lebensdauer eines Kontos.| Verwenden Sie diese Information, um die Kommunikation mit dem Besitzer zu planen und die Konten letztendlich zu deaktivieren und anschließend zu löschen. Legen Sie nach Möglichkeit ein Ablaufdatum für Anmeldeinformationen fest, sofern kein automatischer Rollover für Anmeldeinformationen möglich ist. |
| Name| Standardisierter Name des Kontos| Erstellen Sie ein Benennungsschema für alle Dienstkonten, um problemlos nach Dienstkonten suchen, sortieren und filtern zu können. |


## <a name="use-the-principle-of-least-privileges"></a>Verwenden des Prinzips der geringsten Rechte
Gewähren Sie dem Dienstkonto nur die Berechtigungen, die zum Ausführen der zugehörigen Aufgaben erforderlich sind. Wenn ein Dienstkonto Berechtigungen auf höherer Ebene benötigt (also beispielsweise Berechtigungen auf der Ebene eines globalen Administrators), untersuchen Sie den Grund dafür, und versuchen Sie, die erforderlichen Berechtigungen zu reduzieren.

Für Dienstkontoberechtigungen empfiehlt sich Folgendes:

**Berechtigungen**

* Weisen Sie Dienstkonten keine integrierten Rollen zu. Verwenden Sie stattdessen das [OAuth2-Berechtigungszuweisungsmodell für Microsoft Graph](/graph/api/resources/oauth2permissiongrant).

* Wenn dem Dienstprinzipal eine privilegierte Rolle zugewiesen werden muss, empfiehlt es sich gegebenenfalls, eine zeitgebundene [benutzerdefinierte Rolle](../roles/custom-create.md) mit spezifischen erforderlichen Berechtigungen zuzuweisen.

* Schließen Sie Dienstkonten nicht in Gruppen mit erhöhten Berechtigungen ein. 

* [Verwenden Sie PowerShell, um Mitglieder privilegierter Rollen aufzuzählen](/powershell/module/azuread/get-azureaddirectoryrolemember) (Beispiel:   
‎`Get-AzureADDirectoryRoleMember`), und filtern Sie nach dem Objekttyp „Dienstprinzipal“.

   Oder verwenden Sie Folgendes:  
‎   `Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Verwenden Sie OAuth 2.0-Bereiche](../develop/v2-permissions-and-consent.md), um die Funktionen einzuschränken, auf die ein Dienstkonto für eine Ressource zugreifen kann.
* Dienstprinzipale und verwaltete Identitäten können OAuth 2.0-Bereiche entweder in einem delegierten Kontext, der die Identität eines angemeldeten Benutzers annimmt, oder als Dienstkonto im Anwendungskontext verwenden. Im Anwendungskontext wird kein Benutzer angemeldet.

* Überprüfen Sie die Bereiche, die von Dienstkonten für Ressourcen angefordert werden, um sicherzustellen, dass sie angemessen sind. Wenn von einem Konto also beispielsweise „Files.ReadWrite.All“ angefordert wird, überprüfen Sie, ob nicht vielleicht „File.Read.All“ ausreicht. Weitere Informationen zu Berechtigungen finden Sie in der [Microsoft Graph-Berechtigungsreferenz](/graph/permissions-reference).

* Vergewissern Sie sich, dass Sie dem Entwickler der Anwendung oder API mit der Zugriffsanforderung für Ihre Ressourcen vertrauen.

**Dauer**

* Schränken Sie die Anmeldeinformationen für das Dienstkonto (geheimer Clientschlüssel, Zertifikat) auf einen voraussichtlichen Verwendungszeitraum ein.

* Planen Sie regelmäßige Überprüfungen für die Verwendung und den Zweck von Dienstkonten. Stellen Sie sicher, dass Überprüfungen vor Ablauf des Kontos durchgeführt werden.

Nachdem Sie sich ein genaues Bild vom Zweck, vom Bereich und von den erforderlichen Berechtigungen gemacht haben, können Sie Ihr Dienstkonto erstellen. 

[Verwenden verwalteter Identitäten für App Service und Azure Functions](../../app-service/overview-managed-identity.md?tabs=dotnet)

[Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../develop/howto-create-service-principal-portal.md)

Verwenden Sie nach Möglichkeit eine verwaltete Identität. Falls Sie keine verwaltete Identität verwenden können, verwenden Sie einen Dienstprinzipal. Falls Sie auch keinen Dienstprinzipal verwenden können, verwenden Sie ein Azure AD-Benutzerkonto.

 

## <a name="build-a-lifecycle-process"></a>Erstellen eines Lebenszyklusprozesses

Die Verwaltung des Lebenszyklus eines Dienstkontos beginnt mit der Planung und endet mit der endgültigen Löschung. 

Planung und Erstellung wurden in diesem Artikel bereits behandelt. Darüber hinaus müssen Sie das Konto überwachen, Berechtigungen überprüfen, über die weitere Nutzung des Kontos entscheiden und letztendlich die Bereitstellung des Kontos aufheben.

### <a name="monitor-service-accounts"></a>Überwachen von Dienstkonten

Überwachen Sie Ihre Dienstkonten proaktiv, um sicherzustellen, dass die Verwendungsmuster des Dienstkontos den beabsichtigten Mustern entsprechen und dass das Dienstkonto weiterhin aktiv verwendet wird.

**Erfassen und überwachen Sie Dienstkontoanmeldungen mithilfe einer der folgenden Methoden:**

* Verwenden der Azure AD-Anmeldeprotokolle im Azure AD-Portal

* Exportieren der Azure AD-Anmeldeprotokolle zu [Azure Storage](../../storage/index.yml), [Azure Event Hubs](../../event-hubs/index.yml) oder [Azure Monitor](../../azure-monitor/logs/data-platform-logs.md)


![Screenshot: Bildschirm für Dienstprinzipalanmeldungen](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Zu den relevanten Informationen in den Anmeldeprotokollen zählt unter anderem Folgendes:**

* Sind Dienstkonten vorhanden, die sich nicht mehr beim Mandanten anmelden?

* Gib es Veränderungen bei den Anmeldemustern von Dienstkonten?

Es empfiehlt sich, Azure AD-Anmeldeprotokolle zu exportieren und sie in Ihre vorhandenen SIEM-Tools (Security Information & Event Management) wie etwa Microsoft Sentinel zu importieren. Verwenden Sie Ihre SIEM-Lösung, um Warnungen und Dashboards zu erstellen.

### <a name="review-service-account-permissions"></a>Überprüfen von Dienstkontoberechtigungen

Überprüfen Sie regelmäßig die gewährten Berechtigungen und die Bereiche, auf die von Dienstkonten zugegriffen wird, um zu ermitteln, ob sie verringert oder entfernt werden können.

* Verwenden Sie [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) zum [Erstellen einer Automatisierung für die Überprüfung und Dokumentation](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) von Bereichen, für die einem Dienstkonto eine Einwilligung erteilt wurde.

* Verwenden Sie PowerShell zum [Überprüfen der Anmeldeinformationen vorhandener Dienstprinzipale](https://github.com/AzureAD/AzureADAssessment) sowie zum Überprüfen ihrer Gültigkeit.

* Legen Sie für die Anmeldeinformationen des Dienstprinzipals die Option „Niemals ablaufen“ fest.

* Verwenden Sie nach Möglichkeit in Azure Key Vault gespeicherte Zertifikate oder Anmeldeinformationen.

Im kostenlosen PowerShell-Beispiel von Microsoft werden die OAuth2-Zuweisungen und die Anmeldeinformationen des Dienstprinzipals in einer CSV-Datei (Datei mit kommagetrennten Werten) erfasst. Außerdem steht ein Power BI-Beispieldashboard zur Verfügung, um die Daten zu interpretieren und zu verwenden. Weitere Informationen finden Sie auf GitHub unter [AzureAD/AzureADAssessment: Tools zum Bewerten von Zustand und Konfiguration eines Azure AD-Mandanten](https://github.com/AzureAD/AzureADAssessment).

### <a name="recertify-service-account-use"></a>Neuzertifizieren der Dienstkontoverwendung

Richten Sie einen Überprüfungsprozess ein, um eine regelmäßige Überprüfung von Dienstkonten durch ihre Besitzer und das Sicherheits- oder IT-Team zu gewährleisten. 

**Der Prozess sollte Folgendes umfassen:**

* Vorgehensweise zur Bestimmung des Überprüfungszyklus des jeweiligen Dienstkontos (sollte in Ihrer Konfigurationsverwaltungsdatenbank dokumentiert sein)

* Kommunikation mit dem Besitzer und dem Sicherheits- oder IT-Team vor Beginn der Überprüfung

* Zeitpunkt und Inhalt von Warnungen, falls die Überprüfung nicht durchgeführt wurde

* Anweisungen für den Fall, dass die Besitzer keine Überprüfung durchführen oder nicht reagieren. Das Konto kann beispielsweise bis zum Abschluss der Überprüfung deaktiviert werden (ohne es jedoch zu löschen).

* Anweisungen zum Bestimmen von Upstream- und Downstreamabhängigkeiten sowie dazu, wie andere Ressourcenbesitzer über mögliche Auswirkungen informiert werden sollen

**Die Überprüfung sollte den Besitzer und den IT-Partner einbeziehen und Folgendes ergeben:**

* Das Konto wird weiterhin benötigt.

* Die dem Konto gewährten Berechtigungen sind angemessen und notwendig. Falls nicht, wird eine Änderung angefordert.

* Der Zugriff auf das Konto und die zugehörigen Anmeldeinformationen wird kontrolliert.

* Art und Lebensdauer der vom Konto verwendeten Anmeldeinformationen sind hinsichtlich der Risikobewertung für das Konto angemessen.

* Die Risikobewertung des Kontos hat sich seit der letzten Neuzertifizierung nicht geändert.

* Die aktualisierte erwartete Lebensdauer des Kontos sowie das nächste Datum für die Neuzertifizierung

### <a name="deprovision-service-accounts"></a>Aufheben der Bereitstellung von Dienstkonten

**Die Bereitstellung von Dienstkonten muss in folgenden Fällen aufgehoben werden:**

* Das Skript oder die Anwendung, für das bzw. die das Dienstkonto erstellt wurde, wird eingestellt.

* Die Funktion innerhalb des Skripts oder der Anwendung, für das bzw. die das Dienstkonto verwendet wird (z. B. Zugriff auf eine bestimmte Ressource), wird eingestellt.


* Das Dienstkonto wird durch ein anderes Dienstkonto ersetzt.

* Die Anmeldeinformationen sind abgelaufen, oder das Konto ist anderweitig nicht funktionsfähig, und es liegen keine Beschwerden vor.

**Die Prozesse für die Aufhebung der Bereitstellung sollten die folgenden Aufgaben umfassen:**

1. [Überwachen Sie die Anmeldungen](../reports-monitoring/concept-sign-ins.md) und den Ressourcenzugriff durch das Dienstkonto, nachdem die Bereitstellung der zugeordneten Anwendung oder des zugeordneten Skripts aufgehoben wurde.

   * Ist das Konto weiterhin aktiv, ermitteln Sie, wie es verwendet wird, bevor Sie weitere Schritte ausführen.
 
2. Handelt es sich um eine verwaltete Dienstidentität, deaktivieren Sie die Anmeldung des Dienstkontos, entfernen Sie es aber nicht aus dem Verzeichnis.

3. Widerrufen Sie Rollenzuweisungen und OAuth2-Einwilligungszuweisungen für das Dienstkonto.

4. Löschen Sie das Dienstkonto nach einem definierten Zeitraum und einer angemessenen Warnung für Besitzer aus dem Verzeichnis.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Schutz von Azure-Dienstkonten finden Sie hier:

[Einführung in den Schutz von Azure-Dienstkonten](service-accounts-introduction-azure.md)

[Schützen verwalteter Identitäten](service-accounts-managed-identities.md)

[Schützen von Dienstprinzipalen](service-accounts-principal.md)




 

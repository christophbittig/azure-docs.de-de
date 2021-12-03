---
title: Konfigurieren zusätzlicher Kontrollen zur Erfüllung von „FedRAMP High Impact“
description: Ausführlicher Leitfaden zum Konfigurieren zusätzlicher Kontrollen zur Erfüllung von FedRAMP High Impact-Stufen.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: celested
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d39594b5d6cf1f9430c8c8d8fbdc74343966cb0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302024"
---
# <a name="configure-additional-controls-to-achieve-fedramp-high-impact-level"></a>Konfigurieren zusätzlicher Kontrollen, um die Stufe „FedRAMP High Impact“ zu erreichen

Die folgende Liste von Steuerelementen (und Steuererweiterungen) erfordert möglicherweise eine Konfiguration in Ihrem Azure AD-Mandanten (Azure Active Directory).

Jede Zeile in den folgenden Tabellen enthält eine präskriptive Anleitung. Dieser Leitfaden bietet Ihnen Unterstützung beim Festlegen der Reaktionen Ihrer Organisation auf gemeinsame Zuständigkeiten hinsichtlich der Steuerung bzw. Steuerungsoptimierung.

## <a name="audit-and-accountability"></a>Überwachung und Verantwortlichkeit

Die Anleitungen in der folgenden Tabelle beziehen sich auf:

* AU-02: Überwachungsereignisse
* AU-03: Inhalt der Überwachung
* AU-06: Prüfung, Analyse und Berichterstellung in Bezug auf die Überwachung

| Kontroll-ID und Unterteil| Kundenverantwortungen und Anleitung |
| - | - |
| AU-02 <br>AU-03 <br>AU-03(1)<br>AU-03(2)| Stellen Sie sicher, dass das System die in AU-02 Teil A definierten Ereignisse überwachen kann. Stimmen Sie sich mit anderen Entitäten innerhalb der Organisation hinsichtlich der überwachbaren Ereignisse ab, um anschließende Untersuchungen zu unterstützen. Implementieren Sie die zentralisierte Verwaltung von Überwachungsdatensätzen.<p>Alle Vorgänge für den Kontolebenszyklus (Kontoerstellung, Änderungen, Aktivierung, Deaktivierung und Entfernung) werden innerhalb der Azure AD Überwachungsprotokolle überwacht. Alle Authentifizierungs- und Autorisierungsereignisse werden in Azure AD-Anmeldeprotokollen und alle erkannten Risiken in den Identity Protection-Protokollen überwacht. Sie können jedes dieser Protokolle direkt in eine SIEM-Lösung (Security Information & Event Management) wie Microsoft Sentinel streamen. Alternativ können Sie auch Azure Event Hubs verwenden, um Protokolle in SIEM-Lösungen von Drittanbietern zu integrieren.<p>Überwachen von Ereignissen<li> [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-audit-logs.md)<li> [Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-sign-ins.md)<li>[Anleitung: Untersuchen eines Risikos](../identity-protection/howto-identity-protection-investigate-risk.md)<p>SIEM-Integrationen<li> [Microsoft Sentinel: Verknüpfen von Azure AD-Daten (Azure Active Directory)](../../sentinel/connect-azure-active-directory.md)<li>[Tutorial: Streamen von Azure Active Directory-Protokollen an einen Azure Event Hub](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| AU-06<br>AU-06(1)<br>AU-06(3)<br>AU-06(4)<br>AU-06(5)<br>AU-06(6)<br>AU-06(7)<br>AU-06(10)<br>| Überprüfen und analysieren Sie Überwachungsdatensätze mindestens einmal pro Woche, um unangemessene oder ungewöhnliche Aktivitäten zu identifizieren, und geben Sie die Ergebnisse an die entsprechenden Mitarbeiter weiter. <p>Die zuvor erwähnten Leitfäden für AU-02 und AU-03 ermöglichen die wöchentliche Überprüfung von Überwachungsdatensätzen und die Weitergabe von Informationen an entsprechende Mitarbeiter. Diese Anforderungen können nicht nur mithilfe von Azure AD erfüllt werden. Dafür ist auch eine SIEM-Lösung wie Microsoft Sentinel erforderlich. Weitere Informationen finden Sie unter [Informationen zu Microsoft Sentinel](../../sentinel/overview.md). |

## <a name="incident-response"></a>Reaktion auf Incidents

Die Anleitungen in der folgenden Tabelle beziehen sich auf:

* IR-04: Vorfallbehandlung

* IR-05: Überwachung von Vorfällen

| Kontroll-ID und Teilbereich| Kundenverantwortungen und Anleitung |
| - | - |
| IR-04<br>IR-04(1)<br>IR-04(2)<br>IR-04(3)<br>IR-04(4)<br>IR-04(6)<br>IR-04(8)<br>IR-05<br>IR-05(1)| Implementieren Sie Funktionen zur Bearbeitung und Überwachung von Vorfällen. Dazu gehören die automatisierte Vorfallbearbeitung, die dynamische Neukonfiguration, Betriebskontinuität, Informationskorrelation, Insiderbedrohungen, Korrelation mit externen Organisationen sowie die Überwachung von Vorfällen und automatisierte Nachverfolgung. <p>Die Überwachungsprotokolle zeichnen alle Konfigurationsänderungen auf. Authentifizierungs- und Autorisierungsereignisse werden in den Anmeldeprotokollen und ggf. erkannte Risiken in den Identity Protection-Protokollen überwacht. Sie können jedes dieser Protokolle direkt in eine SIEM-Lösung wie z. B. Microsoft Sentinel streamen. Alternativ können Sie auch Azure Event Hubs verwenden, um Protokolle in SIEM-Lösungen von Drittanbietern zu integrieren. Automatisieren Sie die dynamische Neukonfiguration basierend auf Ereignissen innerhalb von SIEM mithilfe von Microsoft Graph oder Azure AD PowerShell.<p>Überwachen von Ereignissen<br><li>[Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-audit-logs.md)<li>[Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-sign-ins.md)<li>[Anleitung: Untersuchen eines Risikos](../identity-protection/howto-identity-protection-investigate-risk.md)<p>SIEM-Integrationen<li>[Microsoft Sentinel: Verknüpfen von Azure AD-Daten (Azure Active Directory)](../../sentinel/connect-azure-active-directory.md)<li>[Tutorial: Streamen von Azure Active Directory-Protokollen an einen Azure Event Hub](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)<p>Dynamische Neukonfiguration<li>[AzureAD-Modul](/powershell/module/azuread/)<li>[Übersicht über Microsoft Graph](/graph/overview?view=graph-rest-1.0&preserve-view=true) |

## <a name="personnel-security"></a>Personalsicherheit

Die Anleitungen in der folgenden Tabelle beziehen sich auf:

* PS-04: Personalkündigung

| Kontroll-ID und Teilbereich| Kundenverantwortungen und Anleitung |
| - | - |
| PS-04<br>PS-04(2)| Stellen Sie sicher, dass Mitarbeiter, die für die Deaktivierung des Systemzugriffs zuständig sind, automatisch benachrichtigt werden. <p>Deaktivieren Sie Konten, und widerrufen Sie alle zugehörigen Authentifikatoren und Anmeldeinformationen innerhalb von acht Stunden. <p>Konfigurieren Sie die Bereitstellung von Konten in Azure AD (einschließlich Deaktivierung bei Kündigung) über externe Personalsysteme, lokales Active Directory oder direkt in der Cloud. Widerrufen Sie vorhandene Sitzungen, um den Systemzugriff vollständig zu beenden. <p>Kontobereitstellung<li> Einen ausführlichen Leitfaden finden Sie in AC-02. <p>Widerrufen aller zugeordneten Authentifikatoren <li> [Widerrufen des Benutzerzugriffs in Azure Active Directory](../enterprise-users/users-revoke-access.md) |


## <a name="system-and-information-integrity"></a>System- und Informationsintegrität

Die Anleitungen in der folgenden Tabelle beziehen sich auf:

* SI-04: Überwachung des Informationssystems

 Kontroll-ID und Teilbereich| Kundenverantwortungen und Anleitung |
| - | - |
| SI-04<br>SI-04(1)| Implementieren Sie die informationssystemweite Überwachung und das Intrusion-Detection-System. <p>Schließen Sie alle Azure AD-Protokolle (Überwachung, Anmeldung, Identity Protection) in die Überwachungslösung des Informationssystems ein. <p>Streamen Sie Azure AD-Protokolle in eine SIEM-Lösung (siehe IA-04). |

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der Zugriffssteuerung](fedramp-access-controls.md)

[Konfigurieren von Identifizierungs- und Authentifizierungskontrollen](fedramp-identification-and-authentication-controls.md)

[Konfigurieren Sie andere Steuerelemente](fedramp-other-controls.md)
 

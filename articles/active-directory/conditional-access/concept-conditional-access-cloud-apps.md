---
title: Cloud-Apps, -Aktionen und -Authentifizierungskontext in Richtlinien für bedingten Zugriff – Azure Active Directory
description: Was sind Cloud-Apps, -Aktionen und -Authentifizierungskontext in einer Richtlinie für bedingten Zugriff in Azure AD?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a0bd64f8e3377b78f276b1b3c4bdef6f43d8c76
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308046"
---
# <a name="conditional-access-cloud-apps-actions-and-authentication-context"></a>Bedingter Zugriff: Cloud-Apps, Aktionen und Authentifizierungskontext

Cloud-Apps, -Aktionen und -Authentifizierungskontext sind wichtige Signale in einer Richtlinie für bedingten Zugriff. Richtlinien für bedingten Zugriff ermöglichen Administratoren das Zuweisen von Steuerelementen zu bestimmten Anwendungen, Aktionen oder Authentifizierungskontext.

- Administratoren stehen eine Reihe von Anwendungen zur Auswahl. Dazu zählen integrierte Microsoft-Anwendungen und alle [in Azure AD integrierten Anwendungen](../manage-apps/what-is-application-management.md) (Katalog- und Nicht-Kataloganwendungen) sowie über den [Anwendungsproxy](../app-proxy/what-is-application-proxy.md) veröffentlichte Anwendungen.
- Administratoren können sich entscheiden, die Richtlinie nicht basierend auf einer Cloudanwendung zu definieren, sondern basierend auf einer [Benutzeraktion](#user-actions) wie etwa **Sicherheitsinformationen registrieren** oder **Geräte registrieren oder beitreten**, sodass der bedingte Zugriff Kontrollmaßnahmen bezüglich dieser Aktionen durchsetzen kann.
- Administratoren können den [Authentifizierungskontext](#authentication-context-preview) verwenden, um eine zusätzliche Sicherheitsebene in Anwendungen zu schaffen. 

![Definieren einer Richtlinie für bedingten Zugriff und Angeben von Cloud-Apps](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft Cloud-Anwendungen

Viele der vorhandenen Microsoft-Cloudanwendungen sind in der Liste der Anwendungen enthalten, die zur Auswahl stehen. 

Administratoren können den folgenden Cloud-Apps von Microsoft eine Richtlinie für bedingten Zugriff zuweisen. Zu einigen Apps wie Office 365 und Microsoft Azure Management gehören mehrere untergeordnete Apps oder Dienste. Wir fügen ständig weitere Apps hinzu, sodass die folgende Liste nicht vollständig ist und geändert werden kann.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- Azure Event Hubs
- Azure Service Bus
- [Azure SQL Database und Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Common Data Service
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure Management](#microsoft-azure-management)
- Microsoft Azure-Abonnementverwaltung
- Microsoft Defender für Cloud-Apps
- Portal für die Zugriffssteuerung auf Microsoft Commerce-Tools
- Authentifizierungsdienst für Microsoft Commerce-Tools
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune-Registrierung](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft Power Apps
- Microsoft Power Automate
- Microsoft-Suche in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook-Gruppen
- Power BI-Dienst
- Project Online
- Skype for Business Online
- Virtuelles privates Netzwerk (VPN):
- Windows Defender ATP

> [!IMPORTANT]
> Anwendungen, die für den bedingten Zugriff verfügbar sind, haben einen Onboarding- und Validierungsprozess durchlaufen. Diese Liste schließt nicht alle Microsoft-Apps ein, da viele Back-End-Dienste sind und nicht vorgesehen ist, Richtlinien direkt auf sie anzuwenden. Wenn Sie eine fehlende Anwendung suchen, können Sie sich an das jeweilige Anwendungsteam wenden oder eine Anforderung an [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) senden.

### <a name="office-365"></a>Office 365

Microsoft 365 stellt cloudbasierte Dienste für Produktivität und Zusammenarbeit wie Exchange, SharePoint und Microsoft Teams bereit. Microsoft 365-Clouddienste sind umfassend integriert, um nahtlose Funktionen für die Zusammenarbeit zu gewährleisten. Diese Integration kann beim Erstellen von Richtlinien zu Verwirrung führen, da einige Apps wie Microsoft Teams Abhängigkeiten von anderen Apps wie SharePoint oder Exchange aufweisen.

Mit der Office 365-Suite können Sie gleichzeitig auf all diese Dienste abzielen. Wir empfehlen die Verwendung der neuen Office 365-Suite, statt auf einzelne Cloud-Apps abzuzielen, um Probleme mit [Dienstabhängigkeiten](service-dependencies.md) zu vermeiden. 

Eine Ausrichtung auf diese Gruppe von Anwendungen trägt dazu bei, Probleme zu vermeiden, die aufgrund inkonsistenter Richtlinien und Abhängigkeiten auftreten können. Beispiel: Die Exchange Online-App ist an herkömmliche Exchange Online-Daten wie E-Mail-, Kalender- und Kontaktinformationen gebunden. Zugehörige Metadaten können über verschiedene Ressourcen wie die Suche verfügbar gemacht werden. Administratoren sollten der Office 365-Suite Richtlinien zuweisen, um sicherzustellen, dass alle Metadaten wie beabsichtigt durch geschützt werden.

Administratoren können bestimmte Apps von der Richtlinie ausschließen, indem sie die Office 365-Suite einbeziehen und bestimmte Apps in der Richtlinie ausschließen.

Die Client-App von Office 365 enthält u. a. die folgenden wichtigen Anwendungen:

   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365-Suchdienst
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - Power Automate
   - Power Apps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure Management

Die Microsoft Azure Management-Anwendung enthält mehrere Dienste. 

   - Azure-Portal
   - Azure Resource Manager-Anbieter
   - APIs für das klassische Bereitstellungsmodell
   - Azure PowerShell
   - Azure CLI
   - Administratorportal für Visual Studio-Abonnements
   - Azure DevOps
   - Azure Data Factory-Portal

> [!NOTE]
> Die Microsoft Azure Management-Anwendung gilt für Azure PowerShell, die die Azure Resource Manager-API aufruft. Sie gilt nicht für Azure AD PowerShell, die Microsoft Graph aufruft.

### <a name="other-applications"></a>Andere Anwendungen

Administratoren können jede für Azure AD registrierte Anwendung zu Richtlinien für bedingten Zugriff hinzufügen. Dazu zählen u.a. die folgenden Anwendungen: 

- Über den [Azure AD-Anwendungsproxy](../app-proxy/what-is-application-proxy.md) veröffentlichte Anwendungen
- [Aus dem Katalog hinzugefügte Anwendungen](../manage-apps/add-application-portal.md)
- [Benutzerdefinierte Nicht-Kataloganwendungen](../manage-apps/view-applications-portal.md)
- [Ältere Anwendungen, die über App-Bereitstellungscontroller und -netzwerke veröffentlicht wurden](../manage-apps/secure-hybrid-access.md)
- Anwendungen, die [einmaliges Anmelden mit Kennwort](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) verwenden

> [!NOTE]
> Da die Richtlinie für bedingten Zugriff die Anforderungen für den Zugriff auf einen Dienst festlegt, können Sie sie nicht auf eine (öffentliche/native) Clientanwendung anwenden. Anders ausgedrückt: Die Richtlinie wird nicht direkt in einer Clientanwendung (öffentlichen/nativen Anwendung) festgelegt, sondern angewendet, wenn ein Client einen Dienst aufruft. Eine Richtlinie, die für den SharePoint-Dienst festgelegt wurde, gilt beispielsweise für die Clients, die SharePoint aufrufen. Eine Richtlinie, die für Exchange festgelegt wurde, gilt für den Zugriffsversuch auf E-Mail mithilfe des Outlook-Clients. Aus diesem Grund stehen Clientanwendungen (öffentliche/native Anwendungen) in der Cloud-Apps-Auswahl nicht zur Auswahl zur Verfügung, und die Option „Bedingter Zugriff“ ist in den Anwendungseinstellungen für die in Ihrem Mandanten registrierte Clientanwendung (öffentliche/native Anwendung) nicht verfügbar. 

## <a name="user-actions"></a>Benutzeraktionen

Benutzeraktionen sind Aufgaben, die von einem Benutzer ausgeführt werden können. Der bedingte Zugriff unterstützt derzeit zwei Benutzeraktionen: 

- **Sicherheitsinformationen registrieren**: Mit dieser Benutzeraktion kann eine Richtlinie für bedingten Zugriff erzwungen werden, wenn Benutzer, für die die kombinierte Registrierung aktiviert ist, versuchen, ihre Sicherheitsinformationen zu registrieren. Weitere Informationen finden Sie im Artikel [Kombinierte Registrierung von Sicherheitsinformationen](../authentication/concept-registration-mfa-sspr-combined.md).

- **Geräte registrieren oder verknüpfen**: Mit dieser Benutzeraktion können Administratoren eine Richtlinie für bedingten Zugriff erzwingen, wenn Benutzer Geräte in Azure AD [registrieren](../devices/concept-azure-ad-register.md) oder mit Azure AD [verknüpfen](../devices/concept-azure-ad-join.md). Sie bietet Granularität bei der Konfiguration der Multi-Factor Authentication für das Registrieren oder Einbinden von Geräten anstelle einer derzeit vorhandenen mandantenweiten Richtlinie. Bei dieser Benutzeraktion sind drei wichtige Punkte zu beachten: 
   - `Require multi-factor authentication` ist die einzige Zugriffssteuerung, die bei dieser Benutzeraktion verfügbar ist. Alle anderen Zugriffssteuerungen sind deaktiviert. Durch diese Einschränkung werden Konflikte mit Zugriffssteuerungen verhindert, die entweder von der Azure AD-Geräteregistrierung abhängig sind oder nicht für die Azure AD-Geräteregistrierung gelten. 
   - Die Bedingungen `Client apps`, `Filters for devices` und `Device state` sind bei dieser Benutzeraktion nicht verfügbar, da sie zum Erzwingen von Richtlinien für den bedingten Zugriff von der Azure AD-Geräteregistrierung abhängig sind.
   - Wenn bei dieser Benutzeraktion eine Richtlinie für bedingten Zugriff aktiviert ist, muss **Azure Active Directory** > **Geräte** > **Geräteeinstellungen** - `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` auf **Nein** festgelegt werden. Anderenfalls wird die Richtlinie für den bedingten Zugriff bei dieser Benutzeraktion nicht ordnungsgemäß erzwungen. Weitere Informationen zu dieser Geräteeinstellung finden Sie unter [Konfigurieren von Geräteeinstellungen](../devices/device-management-azure-portal.md#configure-device-settings). 

## <a name="authentication-context-preview"></a>Authentifizierungskontext (Vorschau)

Der Authentifizierungskontext kann verwendet werden, um Daten und Aktionen in Anwendungen besser zu schützen. Bei diesen Anwendungen kann es sich um Ihre eigenen benutzerdefinierten Anwendungen, benutzerdefinierte Geschäftsbereichsanwendungen (LOB), Anwendungen wie SharePoint oder Anwendungen handeln, die durch Microsoft Defender für Cloud Apps geschützt sind. 

Eine Organisation kann z. B. Dateien auf SharePoint-Websites speichern, z. B. eine Speisekarte oder die geheime Rezeptur ihrer BBQ-Soße. Möglicherweise haben alle Zugriff auf die Speisekartenwebsite, aber Benutzer mit Zugriff auf die geheime Soßenrezepturwebsite müssen möglicherweise von einem verwalteten Gerät aus darauf zugreifen und bestimmten Nutzungsbedingungen zustimmen.

### <a name="configure-authentication-contexts"></a>Authentifizierungskontexte konfigurieren

Authentifizierungskontexte werden im Azure-Portal unter **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff** > **Authentifizierungskontext** verwaltet.

![Verwalten von Authentifizierungskontext im Azure-Portal](./media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-get-started.png)

> [!WARNING]
> * Das Löschen von Authentifizierungskontextdefinitionen ist während der Vorschau nicht möglich. 
> * Die Vorschau ist auf insgesamt 25 Authentifizierungskontextdefinitionen im Azure-Portal beschränkt.

Erstellen Sie neue Authentifizierungskontextdefinitionen, indem Sie im Azure-Portal **Neuer Authentifizierungskontext** auswählen. Konfigurieren Sie folgende Attribute:

- **Anzeigename** ist der Name, der verwendet wird, um den Authentifizierungskontext in Azure AD und anwendungsübergreifend in Authentifizierungskontext nutzenden Anwendungen zu identifizieren. Wir empfehlen Namen, die ressourcenübergreifend verwendet werden können, z. B. „vertrauenswürdige Geräte“, um die Anzahl der erforderlichen Authentifizierungskontexte zu reduzieren. Durch einen reduzierten Satz an Namen wird die Anzahl der Umleitungen eingeschränkt, und die Endbenutzererfahrung wird verbessert.
- Die **Beschreibung** enthält weitere Informationen zu Richtlinien, die von Azure AD-Administratoren verwendet werden, und solchen, die Authentifizierungskontexte auf Ressourcen anwenden.
- Wenn das Kontrollkästchen **In Apps veröffentlichen** aktiviert ist, wird der Authentifizierungskontext für Apps ankündigen und zur Zuweisung zur Verfügung gestellt. Wenn es nicht aktiviert ist, ist der Authentifizierungskontext für Downstream-Ressourcen nicht verfügbar. 
- Die **ID** ist schreibgeschützt und wird in Token und Apps für anforderungsspezifische Authentifizierungskontextdefinitionen verwendet. Sie ist hier zu Zwecken der Problembehandlung und für die Entwicklung aufgeführt. 

#### <a name="add-to-conditional-access-policy"></a>Hinzufügen zur Richtlinie für bedingten Zugriff

Administratoren können veröffentlichte Authentifizierungskontexte in ihren Richtlinien für bedingten Zugriff unter **Zuweisungen** > **Cloud-Apps oder -Aktionen** **Authentifizierungskontext** im Menü **Auswählen, wofür diese Richtlinie gilt** auswählen.

:::image type="content" source="media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-in-policy.png" alt-text="Hinzufügen eines Authentifizierungskontexts für bedingten Zugriff zu einer Richtlinie":::

### <a name="tag-resources-with-authentication-contexts"></a>Ressourcen mit Authentifizierungskontexten markieren 

Weitere Informationen zur Verwendung des Authentifizierungskontexts in Anwendungen finden Sie in den folgenden Artikeln.

- [Microsoft Information Protection Vertraulichkeitsbezeichnungen zum Schützen SharePoint Websites](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [Microsoft Defender für Cloud-Apps](/cloud-app-security/session-policy-aad?branch=pr-en-us-2082#require-step-up-authentication-authentication-context)
- [Benutzerdefinierte Anwendungen](../develop/developer-guide-conditional-access-authentication-context.md)

## <a name="next-steps"></a>Nächste Schritte

- [Bedingter Zugriff: Bedingungen](concept-conditional-access-conditions.md)
- [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)
- [Abhängigkeiten von Clientanwendungen](service-dependencies.md)

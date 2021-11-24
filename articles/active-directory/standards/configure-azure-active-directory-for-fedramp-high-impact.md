---
title: Konfigurieren von Azure Active Directory, um die FedRAMP High-Auswirkungsstufe zu erfüllen
description: Übersicht darüber, wie Sie mithilfe von Azure Active Directory eine FedRAMP High-Auswirkungsstufe für Ihre Organisation erreichen können.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1cc2d2bf4a51a0d50c47d41982ced9962a977e2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302045"
---
# <a name="configure-azure-active-directory-to-meet-fedramp-high-impact-level"></a>Konfigurieren von Azure Active Directory, um die FedRAMP High-Auswirkungsstufe zu erfüllen

Das [Federal Risk and Authorization Management Program](https://www.fedramp.gov/) (FedRAMP) ist ein Bewertungs- und Autorisierungsprozess für Clouddienstanbieter (Cloud Service Providers, CSPs). Der Prozess richtet sich insbesondere an CSPs, die Cloudlösungsangebote (Cloud Solution Offerings, CSOs) für die Verwendung in Bundesbehörden erstellen. Azure und Azure Government haben vom Joint Authorization Board, dem höchsten Organ für die FedRAMP-Zulassung, eine [vorübergehende Betriebsgenehmigung (Provisional Authority to Operate, P-ATO) auf der High-Auswirkungsebene](/compliance/regulatory/offering-fedramp) erhalten.

Azure bietet die Möglichkeit, alle Steuerungsanforderungen zu erfüllen, um eine hohe FedRAMP-Bewertung für Ihr CSO oder als Bundesorganisation zu erzielen. Es liegt in der Verantwortung Ihrer Organisation, zusätzliche Konfigurationen oder Prozesse zu erfüllen, um konform zu sein. Diese Verantwortung gilt sowohl für CSPs, die eine hohe FedRAMP-Autorisierung für ihr CSO beantragen, als auch für Bundesbehörden, die eine Autorität für den Betrieb (Authority to Operate, ATO) beantragen. 

## <a name="microsoft-and-fedramp"></a>Microsoft und FedRAMP 

Microsoft Azure unterstützt mehr Dienste auf [FedRAMP High](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md)-Auswirkungsebenen als jeder andere CSP. Und während diese Ebene in der öffentlichen Azure-Cloud die Anforderungen vieler US-Behördenkunden erfüllt, können Behörden mit strengeren Anforderungen auf die Azure Government-Cloud zurückgreifen. Azure Government bietet zusätzliche Sicherheitsvorkehrungen, z. B. die verstärkte Personalüberprüfung. 

Microsoft muss seine Clouddienste jedes Jahr neu zertifizieren, um seine Autorisierungen zu verwalten. Zu diesem Ziel überwacht und bewertet Microsoft kontinuierlich seine Sicherheitskontrollen und zeigt, dass die Sicherheit seiner Dienste konform bleibt. Weitere Informationen finden Sie unter [FedRAMP-Autorisierungen für Microsoft Cloud Services](https://marketplace.fedramp.gov/) und unter [Microsoft FedRAMP-Überwachungsberichte](https://aka.ms/MicrosoftFedRAMPAuditDocuments). Um andere FedRAMP-Berichte zu erhalten, senden Sie eine E-Mail an [Azure Federal Documentation](mailto:AzFedDoc@microsoft.com).

Es gibt mehrere Pfade zur FedRAMP-Autorisierung. Sie können das vorhandene Autorisierungspaket von Azure und die hier enthaltenen Leitfäden wiederverwenden, um Zeit und Aufwand für den Erhalt einer ATO oder P-ATO erheblich zu reduzieren. 

## <a name="scope-of-guidance"></a>Umfang der Leitfäden

Die FedRAMP High-Baseline besteht aus 421 Kontrollen und Kontrollerweiterungen von [NIST 800-53 Security Controls Catalog Revision 4](https://csrc.nist.gov/publications/detail/sp/800-53/rev-4/final). Sofern zutreffend, haben wir klarstellende Informationen aus der [800-53 Revision 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) hinzugefügt. Dieser Artikelsatz behandelt eine Teilmenge dieser Kontrollen, die sich auf die Identität beziehen und die Sie konfigurieren müssen. 

Wir bieten einen ausführlichen Leitfaden, der Sie bei der Einhaltung von Kontrollen unterstützt, für deren Konfiguration in Azure Active Directory (Azure AD) Sie verantwortlich sind. Um einige Anforderungen an die Identitätskontrolle vollständig zu erfüllen, müssen Sie möglicherweise andere Systeme verwenden. Andere Systeme enthalten beispielsweise ein SIEM-Tool (Security Information & Event Management) wie Azure Sentinel. Wenn Sie Azure-Dienste außerhalb von Azure Active Directory verwenden, gibt es weitere Kontrollen, die zu berücksichtigen sind. Sie können die bereits in Azure vorgefertigten Funktionen nutzen, um die Kontrollen zu erfüllen.

Nachstehend sehen Sie eine Liste der FedRAMP-Ressourcen:

* [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/)

* [FedRAMP Security Assessment Framework](https://www.fedramp.gov/assets/resources/documents/FedRAMP_Security_Assessment_Framework.pdf)

* [Agency Guide for FedRAMP Authorizations](https://www.fedramp.gov/assets/resources/documents/Agency_Guide_for_Reuse_of_FedRAMP_Authorizations.pdf)

* [Verwalten von Compliance in der Cloud bei Microsoft](https://www.microsoft.com/trustcenter/common-controls-hub)

* [Microsoft Government Cloud](https://go.microsoft.com/fwlink/p/?linkid=2087246)

* [Complianceangebote von Azure](https://aka.ms/azurecompliance)

* [FedRAMP High: In Azure Policy integrierte Initiativendefinition](../../governance/policy/samples/fedramp-high.md)

* [Microsoft 365 Compliance Center](/microsoft-365/compliance/microsoft-365-compliance-center)

* [Microsoft Compliance-Manager](/microsoft-365/compliance/compliance-manager)

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der Zugriffssteuerung](fedramp-access-controls.md)

[Konfigurieren von Identifizierungs- und Authentifizierungskontrollen](fedramp-identification-and-authentication-controls.md)

[Konfigurieren Sie andere Steuerelemente](fedramp-other-controls.md)

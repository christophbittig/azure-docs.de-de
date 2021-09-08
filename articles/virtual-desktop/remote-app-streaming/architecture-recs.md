---
title: Empfehlungen zur Azure Virtual Desktop-Architektur– Azure
description: Architekturempfehlungen für Azure Virtual Desktop für App-Entwickler.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b398fe41ec1cda06fce1cf9141ec6bd5d6bcbfb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798955"
---
# <a name="architecture-recommendations"></a>Architekturempfehlungen

Azure Virtual Desktop-Bereitstellungen gibt es in vielen verschiedenen Formen und Größen, abhängig von vielen Faktoren wie den Endbenutzeranforderungen, der vorhandenen Infrastruktur der Organisation, die den Dienst bereitstellt usw. Wie stellen Sie sicher, dass Sie die richtige Architektur verwenden, die die Anforderungen Ihrer Organisation erfüllt?

Dieser Artikel enthält Anleitungen für Ihre Azure Virtual Desktop-Bereitstellungsstruktur. Die in diesem Artikel aufgeführten Beispiele sind nicht die einzigen Möglichkeiten, wie Sie Azure Virtual Desktop bereitstellen können. Wir behandeln jedoch zwei der grundlegendsten Arten der Bereitstellung für Benutzer innerhalb und außerhalb Ihrer Organisation.

## <a name="deploying-azure-virtual-desktop-for-users-within-your-organization"></a>Bereitstellen von Azure Virtual Desktop für Benutzer in Ihrer Organisation

Wenn Sie eine Azure Virtual Desktop-Bereitstellung für Benutzer in Ihrer Organisation erstellen, können Sie sämtliche Benutzer und Ressourcen in demselben Azure-Mandanten hosten. Sie können auch die derzeit unterstützten Identitätsverwaltungsmethoden von Azure Virtual Desktop verwenden, um Ihre Benutzer zu schützen.

Dies sind die grundlegendsten Anforderungen für eine Azure Virtual Desktop-Bereitstellung, die RemoteApps und Desktops für Benutzer in Ihrer Organisation bereitstellen kann:

- Ein Hostpool zum Hosten von Benutzersitzungen
- Ein Azure-Abonnement zum Hosten des Hostpools
- Ein Azure-Mandant als Besitzer für das Abonnement und die Identitätsverwaltung

Sie können jedoch auch eine Bereitstellung mit mehreren Hostpools erstellen, die verschiedene Apps für verschiedene Benutzergruppen anbieten.

Einige Kunden entscheiden sich dafür, separate Azure-Abonnements zum Speichern der einzelnen Azure Virtual Desktop-Bereitstellungen zu erstellen. Auf diese Weise können Sie die Kosten der einzelnen Bereitstellungen auf der Grundlage der Unterorganisationen, denen sie Ressourcen zur Verfügung stellen, voneinander abgrenzen. Andere verwenden Azure-Abrechnungsbereiche, um die Kosten auf einer differenzierteren Ebene zu unterscheiden. Weitere Informationen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](../../cost-management-billing/costs/understand-work-scopes.md).

## <a name="deploying-azure-virtual-desktop-for-users-outside-your-organization"></a>Bereitstellen von Azure Virtual Desktop für Benutzer außerhalb Ihrer Organisation

Wenn Ihre Azure Virtual Desktop-Bereitstellung Endbenutzern außerhalb Ihrer Organisation dient, insbesondere Benutzern, die normalerweise nicht Windows verwenden oder keinen Zugriff auf die internen Ressourcen Ihres Unternehmens haben, müssen Sie zusätzliche Sicherheitsempfehlungen berücksichtigen.

Azure Virtual Desktop unterstützt derzeit keine externen Identitäten, einschließlich B2B-Benutzer (Business-to-Business) oder B2C-Benutzer (Business-to-Client). Sie müssen diese Identitäten manuell erstellen und verwalten und die Anmeldeinformationen für Ihre Benutzer selbst bereitstellen. Benutzer verwenden diese Identitäten dann für den Zugriff auf Ressourcen in Azure Virtual Desktop.

Um Ihren Kunden eine sichere Lösung zu bieten, empfiehlt Microsoft dringend, einen AD-Mandanten (Azure Active Directory) und ein Abonnement für jeden Kunden mit einem eigenen dedizierten Active Directory zu erstellen. Diese Trennung bedeutet, dass Sie eine separate Azure Virtual Desktop-Bereitstellung für jede Organisation erstellen müssen, die vollständig von den anderen Bereitstellungen und deren Ressourcen isoliert ist. Die virtuellen Computer, die die einzelnen Organisationen verwenden, sollten nicht auf die Ressourcen anderer Unternehmen zugreifen können, um Informationen zu schützen. Sie können diese separaten Bereitstellungen entweder mit einer Kombination aus Active Directory Domain Services (AD DS) und Azure AD Connect oder mithilfe von Azure AD Domain Services einrichten.

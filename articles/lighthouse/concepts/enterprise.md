---
title: Azure Lighthouse in Unternehmensszenarien
description: Die Funktionen von Azure Lighthouse können zum Vereinfachen der mandantenübergreifenden Verwaltung in einem Unternehmen, in dem mehrere Azure AD-Mandanten vorhanden sind, verwendet werden.
ms.date: 10/21/2021
ms.topic: conceptual
ms.openlocfilehash: 7d0accd765049c671d8d61a76d7c6449260b0140
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249645"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse in Unternehmensszenarien

Ein gängiges Szenario für [Azure Lighthouse](../overview.md) ist ein Dienstanbieter, der Ressourcen in den Azure Active Directory-Mandanten (Azure AD) seiner Kunden verwaltet. Die Funktionen von Azure Lighthouse dienen auch zum Vereinfachen der mandantenübergreifenden Verwaltung in einem Unternehmen mit mehreren Azure AD-Mandanten.

## <a name="single-vs-multiple-tenants"></a>Einzelner Mandant und mehrere Mandanten

Für die meisten Organisationen ist die Verwaltung mit nur einem Azure AD-Mandanten einfacher. Wenn sich alle Ressourcen unter einem Mandanten befinden, können die Verwaltungsaufgaben nach den jeweiligen Benutzern, Benutzergruppen oder Dienstprinzipalen des Mandanten zentralisiert werden. Wir empfehlen Ihnen, nach Möglichkeit nur einen Mandanten für Ihre Organisation zu verwenden.

Einige Organisationen müssen möglicherweise mit mehreren Azure AD-Mandanten arbeiten. Dies kann vorübergehend erforderlich sein, wenn beispielsweise Übernahmen erfolgt sind und noch keine langfristige Konsolidierungsstrategie für Mandanten definiert wurde. Es kann auch sein, dass Organisationen aufgrund von unabhängigen Tochtergesellschaften, geografischen oder rechtlichen Anforderungen oder anderen Aspekten fortlaufend mehrere Mandanten verwalten müssen.

Falls eine Architektur mit mehreren Mandanten benötigt wird, kann Azure Lighthouse zur Zentralisierung und Optimierung von Verwaltungsvorgängen beitragen. Durch den Einsatz von Azure Lighthouse können Benutzer in einem Verwaltungsmandanten [mandantenübergreifende Verwaltungsfunktionen](cross-tenant-management-experience.md) auf zentrale, skalierbare Weise durchführen.

## <a name="tenant-management-architecture"></a>Architektur für die Mandantenverwaltung

Um Azure Lighthouse in einem Unternehmen zu verwenden, müssen Sie bestimmen, welcher Mandant die Benutzer enthält, die Verwaltungsvorgänge für die anderen Mandanten durchführen. Mit anderen Worten: Sie müssen einen Mandanten als verwaltenden Mandanten der anderen Mandanten benennen.

Angenommen, Ihre Organisation verfügt über einen einzelnen Mandanten mit dem Namen *Mandant A*. Ihre Organisation schafft dann *Mandant B* und *Mandant C* an, die aus geschäftlichen Gründen separat verwaltet werden müssen. Sie wünschen sich jedoch für alle dieselben Richtliniendefinitionen, Sicherungspraktiken und Sicherheitsprozesse, wobei die Verwaltungsaufgaben von denselben Benutzern ausgeführt werden sollen.

Da Mandant A bereits Benutzer in Ihrer Organisation enthält, die diese Aufgaben für Mandant A durchgeführt haben, können Sie für Abonnements in Mandant B und Mandant C ein Onboarding durchführen, wodurch dieselben Benutzer in Mandant A diese Aufgaben in allen Mandanten durchführen können.

![Diagramm zu Benutzern in Mandant A, die Ressourcen in Mandant B und Mandant C verwalten](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Sicherheits- und Zugriffsaspekte

In den meisten Unternehmensszenarien empfiehlt es sich, ein vollständiges Abonnement an Azure Lighthouse zu delegieren. Sie können auch festlegen, dass nur bestimmte Ressourcengruppen innerhalb eines Abonnements delegiert werden.

Achten Sie aber jeweils darauf, dass Sie sich [beim Festlegen, welche Benutzer Zugriff auf delegierte Ressourcen haben, an das Prinzip der geringsten Rechte halten](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). So stellen Sie sicher, dass die Benutzer nur über die Berechtigungen verfügen, die sie zum Durchführen der erforderlichen Aufgaben benötigen, und das Risiko unbeabsichtigter Fehler wird reduziert.

Bei Azure Lighthouse werden nur Verknüpfungen zwischen einem verwaltenden Mandanten und den verwalteten Mandanten bereitgestellt, anstatt Daten oder Ressourcen physisch zu verschieben. Außerdem ist der Zugriff immer nur in einer Richtung möglich, und zwar vom verwaltenden Mandanten zu den verwalteten Mandanten. Für Benutzer und Gruppen auf dem verwaltenden Mandanten sollte weiterhin die mehrstufige Authentifizierung genutzt werden, wenn Verwaltungsvorgänge auf verwalteten Mandantenressourcen durchgeführt werden.

Unternehmen mit Leitlinien für interne oder externe Governance und Compliance können [Azure-Aktivitätsprotokolle](../../azure-monitor/essentials/platform-logs-overview.md) verwenden, um die jeweiligen Transparenzanforderungen zu erfüllen. Wenn für Unternehmensmandanten Beziehungen zwischen verwaltenden und verwalteten Mandanten eingerichtet wurden, können die Benutzer in den Mandanten protokollierte Aktivitäten anzeigen und so Aktionen sehen, die von Benutzern im verwaltenden Mandanten ausgeführt wurden.

## <a name="onboarding-considerations"></a>Überlegungen zum Onboarding

Für Abonnements (bzw. Ressourcengruppen unter einem Abonnement) kann das Onboarding in Azure Lighthouse durchgeführt werden. Hierzu werden entweder Azure Resource Manager-Vorlagen bereitgestellt oder im Azure Marketplace veröffentlichte Angebote für verwaltete Dienste genutzt.

Da Unternehmensbenutzer normalerweise Direktzugriff auf die Mandanten des Unternehmens haben und für ein Verwaltungsangebot keine Marketing- oder Förderungsmaßnahmen erforderlich sind, ist es in der Regel schneller und einfacher, Azure Resource Manager-Vorlagen bereitzustellen. Zwar wird in der [Anleitung für das Onboarding](../how-to/onboard-customer.md) auf Dienstanbieter und Kunden verwiesen, doch können Unternehmen die gleichen Prozesse für das Onboarding ihrer Mandanten verwenden.

Falls Sie diese Vorgehensweise bevorzugen, kann das Onboarding für Mandanten eines Unternehmens durchgeführt werden, indem ein [Angebot für verwaltete Dienste im Azure Marketplace veröffentlicht wird](../how-to/publish-managed-services-offers.md). Achten Sie darauf, dass Ihre Pläne als „Privat“ gekennzeichnet sind, um sicherzustellen, dass das Angebot nur für die richtigen Mandanten verfügbar ist. Bei einem privaten Plan geben Sie die Abonnement-IDs für jeden Mandanten an, für den das Onboarding durchgeführt werden soll. Es erhalten dann keine anderen Personen Ihr Angebot.

## <a name="azure-ad-b2c"></a>Azure AD B2C

[Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md) ermöglicht die Benutzeridentifizierung für Unternehmen. Wenn Sie eine Ressourcengruppe über Azure Lighthouse delegieren, können Sie Azure Monitor verwenden, um Azure AD B2C-Anmelde- und Überwachungsprotokolle an verschiedene Überwachungslösungen weiterzuleiten. Sie können die Protokolle entweder zur langfristigen Verwendung speichern oder in SIEM-Drittanbietertools (Security Information & Event Management) integrieren, um Einblicke in Ihre Umgebung zu gewinnen.

Weitere Informationen finden Sie unter [Überwachen von Azure AD B2C mit Azure Monitor](../../active-directory-b2c/azure-monitor.md).

## <a name="terminology-notes"></a>Hinweise zur Terminologie

Für die mandantenübergreifende Verwaltung im Unternehmen können Verweise auf Dienstanbieter in der Azure Lighthouse-Dokumentation so interpretiert werden, dass sie für den verwaltenden Mandanten eines Unternehmens gelten. Dies ist der Mandant mit den Benutzern, die Ressourcen in anderen Mandanten über Azure Lighthouse verwalten. Verweise auf Kunden können entsprechend so interpretiert werden, dass sie für die Mandanten gelten, die Ressourcen für die Verwaltung durch Benutzer des verwaltenden Mandanten delegieren.

Im obigen Beispiel können Sie sich Mandant A als Dienstanbietermandanten (verwaltender Mandant) und Mandant B und Mandant C als Kundenmandanten vorstellen.

Zur Fortführung dieses Beispiels können Benutzer in Mandant A, die über die entsprechenden Berechtigungen verfügen, auf der Seite **Meine Kunden** im Azure-Portal [delegierte Ressourcen anzeigen und verwalten](../how-to/view-manage-customers.md). Ebenso können Benutzer von Mandant B und Mandant C mit den richtigen Berechtigungen [die Ressourcen anzeigen und verwalten](../how-to/view-manage-service-providers.md), die an Mandant A delegiert wurden, indem sie im Azure-Portal die Seite **Dienstanbieter** verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](cross-tenant-management-experience.md).
- Hier erfahren Sie mehr über die [Funktionsweise von Azure Lighthouse](architecture.md).

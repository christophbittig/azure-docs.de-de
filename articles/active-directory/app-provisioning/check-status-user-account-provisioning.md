---
title: Melden der automatischen Benutzerkontobereitstellung von Azure Active Directory an SaaS-Anwendungen (Software-as-a-Service)
description: In diesem Artikel erfahren Sie, wie Sie den Status von Aufträgen zu automatischen Benutzerkontobereitstellungen überprüfen und Probleme bei der Bereitstellung einzelner Benutzer behandeln.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: c4605372964012f67ff269d6bd9ebaa9a4030b67
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991563"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Tutorial: Meldung zur automatischen Benutzerkontobereitstellung

Azure Active Directory beinhaltet einen [Dienst zur Benutzerkontobereitstellung](user-provisioning.md), mit dem die Bereitstellung bzw. die Aufhebung der Bereitstellung von Benutzerkonten in SaaS-Apps und anderen Systemen für End-to-End-Identity Lifecycle Management automatisiert werden kann. Azure AD unterstützt vorab integrierte Benutzerbereitstellungsconnectors für alle Anwendungen und Systeme mit [hier](../saas-apps/tutorial-list.md) erhältlichen Tutorials für die Benutzerbereitstellung.

In diesem Artikel wird beschrieben, wie der Status von Bereitstellungsaufträgen nach ihrer Einrichtung überprüft wird, und Probleme bei der Bereitstellung einzelner Benutzer und Gruppen behandelt werden.

## <a name="overview"></a>Übersicht

Bereitstellungsconnectors werden über das [Azure-Portal](https://portal.azure.com) gemäß der [bereitgestellten Dokumentation](../saas-apps/tutorial-list.md) für die unterstützte Anwendung eingerichtet und konfiguriert. Sobald sie konfiguriert wurden und ausgeführt werden, können mit einer der beiden folgenden Methoden Berichte für Bereitstellungsaufträge genutzt werden:

* **Azure-Portal:** In diesem Artikel wird hauptsächlich das Abrufen von Berichtsinformationen über das [Azure-Portal](https://portal.azure.com) beschrieben. Dabei werden sowohl ein Zusammenfassungsbericht für die Bereitstellung als auch ausführliche Bereitstellungsüberwachungsprotokolle für eine jeweilige Anwendung bereitgestellt.
* **Überwachungs-API** – Azure Active Directory stellt auch eine Überwachungs-API bereit, die den programmgesteuerten Abruf von detaillierten Überwachungsprotokollen für die Bereitstellung ermöglicht. Dokumentation speziell für die Verwendung dieser API finden Sie unter [Referenz zur Überwachungs-API von Azure Active Directory](/graph/api/resources/directoryaudit). Zwar wird in diesem Artikel nicht speziell die Verwendung der API behandelt, jedoch werden die Arten von Bereitstellungsereignissen, die im Überwachungsprotokoll erfasst werden, erläutert.

### <a name="definitions"></a>Definitionen

In diesem Artikel werden die folgenden Bezeichnungen verwendet, die wie folgt definiert sind:

* **Quellsystem** – Das Repository von Benutzern, von dem aus der Azure AD-Bereitstellungsdienst synchronisiert. Azure Active Directory ist das Quellsystem für die meisten vorab integrierten Bereitstellungsconnectors, es gibt jedoch einige Ausnahmen (z.B. Workday Inbound Synchronization).
* **Zielsystem** – Das Repository von Benutzern, in dem der Azure AD-Bereitstellungsdienst synchronisiert. Dabei handelt es sich in der Regel um eine SaaS-Anwendung (z. B. Salesforce, ServiceNow, G Suite, Dropbox for Business), dies kann in einigen Fällen jedoch auch ein lokales System wie Active Directory sein (z. B. Workday Inbound Synchronization in Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Abrufen von Bereitstellungsberichten aus dem Azure-Portal

Um Berichtsinformationen zur Bereitstellung für eine bestimmte Anwendung abzurufen, starten Sie zunächst das [Azure-Portal](https://portal.azure.com), und navigieren Sie im Abschnitt **Aktivität** zu **Azure Active Directory** &gt; **Unternehmensanwendungen** &gt; **Bereitstellungsprotokolle (Vorschau)**. Sie können auch zu der Unternehmensanwendung navigieren, für die die Bereitstellung konfiguriert wird. Wenn Sie beispielsweise Benutzer für LinkedIn Elevate bereitstellen, lautet der Navigationspfad zu den Anwendungsdetails wie folgt:

**Azure Active Directory &gt; Unternehmensanwendungen &gt; Alle Anwendungen &gt; LinkedIn Elevate**

Über diese Ansicht können Sie sowohl auf die Statusanzeige für die Bereitstellung als auch auf die Bereitstellungsprotokolle zugreifen, wie im Folgenden beschrieben.

## <a name="provisioning-progress-bar"></a>Statusanzeige für die Bereitstellung

Die [Statusanzeige für die Bereitstellung](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) wird auf der Registerkarte **Bereitstellung** für die jeweilige Anwendung angezeigt. Sie befindet sich im Abschnitt **Aktueller Status** und zeigt den Status des aktuellen Startzyklus oder inkrementellen Zyklus an. Außerdem wird in diesem Abschnitt Folgendes angezeigt:

* Die Gesamtanzahl von Benutzern und/oder -gruppen, die synchronisiert wurden und derzeit von der Bereitstellung zwischen dem Quell- und Zielsystem eingeschlossen werden.
* Der Zeitpunkt, an dem die Synchronisierung zuletzt ausgeführt wurde. Synchronisierungen werden nach Abschluss eines [Startzyklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in der Regel alle 20 bis 40 Minuten durchgeführt.
* Die Angabe, ob ein [Startzyklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) durchgeführt wurde.
* Die Angabe, ob der Bereitstellungsprozess in Quarantäne gesetzt wurde, und den Grund für den Quarantänestatus (etwa Fehler bei der Kommunikation mit dem Zielsystem aufgrund von ungültigen Administratoranmeldeinformationen).

Um die Betriebsintegrität des Bereitstellungsauftrags zu überprüfen, sollten Administratoren zuerst den **Aktuellen Status** anzeigen.

 ![Zusammenfassungsbericht](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Bereitstellungsprotokolle (Vorschau)

Alle vom Bereitstellungsdienst ausgeführten Vorgänge werden in Azure AD-[Bereitstellungsprotokollen](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) aufgezeichnet. Sie können im Azure-Portal auf die Bereitstellungsprotokolle zugreifen. Wählen Sie dazu im Abschnitt **Aktivität** die Option **Azure Active Directory** &gt; **Unternehmensanwendungen** &gt; **Bereitstellungsprotokolle (Vorschau)** aus. Sie können die Bereitstellungsdaten anhand des Benutzernamens oder des Bezeichners entweder im Quell- oder im Zielsystem durchsuchen. Einzelheiten hierzu finden Sie unter [Bereitstellungsprotokolle (Vorschau)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Zu den protokollierten Aktivitätsereignistypen zählen Folgende:

## <a name="troubleshooting"></a>Problembehandlung

Der Zusammenfassungsbericht für die Bereitstellung und die Bereitstellungsprotokolle spielen eine wichtige Rolle und können für den Administrator hilfreich sein, wenn er verschiedene Probleme in Bezug auf die Benutzerkontobereitstellung behandeln muss.

Einen szenariobasierten Leitfaden zur Problembehandlung bei der automatischen Benutzerbereitstellung finden Sie unter [Probleme bei der Konfiguration und Bereitstellung von Benutzern für eine Anwendung](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
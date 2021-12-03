---
title: Was ist Remote-App-Streaming mit Azure Virtual Desktop? - Azure
description: Eine Übersicht über Remote-App-Streaming mit Azure Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 11/12/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c13996fd5c8373ebe0897fa9caa57a2d94600c1b
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399521"
---
# <a name="what-is-azure-virtual-desktop-remote-app-streaming"></a>Was ist Remote-App-Streaming mit Azure Virtual Desktop?

Azure Virtual Desktop ist ein in der Cloud ausgeführter Desktop- und App-Virtualisierungsdienst, mit dem Sie jederzeit und überall auf Ihren Remotedesktop zugreifen können. Aber wussten Sie schon, dass Sie Azure Virtual Desktop auch als Platform-as-a-Service (PaaS) verwenden können, um die Apps Ihrer Organisation als Software-as-a-Service (SaaS) für Ihre Kunden zur Verfügung zu stellen? Durch das Remote-App-Streaming können Sie Azure Virtual Desktop nun verwenden, um Ihren Kunden Apps über ein sicheres Netzwerk mithilfe von virtuellen Computern zur Verfügung zu stellen.

Wenn Sie noch nicht mit Azure Virtual Desktop (oder der App-Virtualisierung im Allgemeinen) vertraut sind, finden Sie nachfolgend einige Ressourcen, die Sie bei der Bereitstellung unterstützen können.

## <a name="requirements"></a>Anforderungen

Bevor Sie beginnen, sollten Sie sich zunächst die [Übersicht über Azure Virtual Desktop](../overview.md) ansehen, um eine detailliertere Liste mit den Systemanforderungen für die Ausführung von Azure Virtual Desktop zu erhalten. Dabei können Sie auch den Rest der Azure Virtual Desktop-Dokumentation lesen. Auf diese Weise erhalten Sie einen besseren Einblick in die IT-Grundlagen des Dienstes, da die meisten Artikel auch auf das Remote-App-Streaming mit Azure Virtual Desktop zutreffen. Sobald Sie die Grundlagen verstanden haben, können Sie die Dokumentation für das Remote-App-Streaming effektiver nutzen.

Um eine Azure Virtual Desktop-Bereitstellung für Ihre benutzerdefinierten Apps einrichten zu können, die Kunden auch außerhalb Ihrer Organisation zur Verfügung steht, benötigen Sie Folgendes:

- Ihre benutzerdefinierte App. Im Artikel [Hosten benutzerdefinierter Apps mit Azure Virtual Desktop](custom-apps.md) erfahren Sie, welche App-Arten von Azure Virtual Desktop unterstützt werden und wie Sie diese für Ihre Kunden bereitstellen können.

- Wo befinden sich Ihre Anmeldeinformationen für den Domänenbeitritt? Wenn Sie noch nicht über ein Identitätsverwaltungssystem verfügen, das mit Azure Virtual Desktop kompatibel ist, müssen Sie die Identitätsverwaltung für Ihren Hostpool einrichten. Weitere Informationen finden Sie unter [Einrichten verwalteter Identitäten](identities.md).

- Ein Azure-Abonnement. [Erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch nicht über ein Abonnement verfügen.

## <a name="get-started"></a>Erste Schritte

Nachdem die Vorbereitung abgeschlossen ist, sehen wir uns nun an, wie Sie Ihre Azure Virtual Desktop-Bereitstellung einrichten können. Sie haben zwei Möglichkeiten, sich für den Erfolg zu rüsten. Sie können Ihre Bereitstellung entweder manuell oder automatisch einrichten. In den nächsten beiden Abschnitten werden die Unterschiede zwischen diesen beiden Methoden beschrieben.

### <a name="set-up-azure-virtual-desktop-manually"></a>Manuelles Einrichten von Azure Virtual Desktop

Sie können Ihre Bereitstellung manuell einrichten, indem Sie diese Tutorials befolgen:

1. [Erstellen eines Hostpools mit dem Azure-Portal](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

2. [Verwalten von App-Gruppen](../manage-app-groups.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

3. [Erstellen eines Hostpools zum Überprüfen von Dienstupdates](../create-validation-host-pool.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

4. [Einrichten von Dienstwarnungen](../set-up-service-alerts.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

### <a name="set-up-azure-virtual-desktop-automatically"></a>Automatisches Einrichten von Azure Virtual Desktop

Wenn Sie einen automatischen Prozess bevorzugen, können Sie das Feature für „Erste Schritte“ verwenden, um Ihre Bereitstellung für Sie einzurichten. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Feature „Erste Schritte“ zum Bereitstellen von Azure Virtual Desktop](../getting-started-feature.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json) (Wenn Sie diese Anweisungen befolgen, stellen Sie sicher, dass Sie die Anweisungen in [Für Abonnements mit Azure AD DS oder AD DS](../getting-started-feature.md#for-subscriptions-with-azure-ad-ds-or-ad-ds) befolgen. Diese Methode bietet Ihnen eine bessere Identitätsverwaltung und eine bessere App-Kompatibilität, während Sie gleichzeitig die Möglichkeit haben, die Kosten für die identitätsbezogene Infrastruktur differenziert zu steuern. Die Methode für Abonnements, die noch nicht über Azure AD DS oder AD DS verfügen, bietet Ihnen diese Vorteile nicht.)
- [Problembehandlung für das Feature „Erste Schritte“](../troubleshoot-getting-started.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="customize-and-manage-azure-virtual-desktop"></a>Anpassen und Verwalten von Azure Virtual Desktop

Nachdem Sie Azure Virtual Desktop eingerichtet haben, stehen Ihnen mehrere Optionen zur Verfügung, mit denen Sie Ihre Bereitstellung an die Anforderungen Ihrer Organisation oder Kunden anpassen können. Folgende Artikel erleichtern Ihnen den Einstieg:

- [Hosten benutzerdefinierter Apps mit Azure Virtual Desktop](custom-apps.md)
- [Registrieren Ihres Abonnements für Preise für den Zugriff auf Benutzerebene](per-user-access-pricing.md)
- [Verwenden von Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Verwenden von virtuellen Windows 10-Computern mit Intune](/mem/intune/fundamentals/windows-10-virtual-machines)
- [Bereitstellen einer App mit dem MSIX-Feature zum Anfügen von Apps](msix-app-attach.md)
- [Verwenden von Azure Monitor für Azure Virtual Desktop zum Überwachen Ihrer Bereitstellung](../azure-monitor.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Einrichten eines Plans für Geschäftskontinuität und Notfallwiederherstellung](../disaster-recovery.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Skalieren von Sitzungshosts mit Azure Automation](../set-up-scaling-script.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Einrichten von Universal Print](/universal-print/fundamentals/universal-print-getting-started)
- [Einrichten des Features „VM bei Verbindung starten“](../start-virtual-machine-connect.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Markieren von Azure Virtual Desktop-Ressourcen zur Verwaltung von Kosten](../tag-virtual-desktop-resources.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="get-to-know-your-azure-virtual-desktop-deployment"></a>Erfahren Sie mehr über Ihre Azure Virtual Desktop-Bereitstellung

Lesen Sie die folgenden Artikel, um die grundlegenden Konzepte für das Erstellen und Verwalten von Azure Virtual Desktop-Bereitstellungen zu verstehen:

- [Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff](licensing.md)
- [Sicherheitsrichtlinien für organisationsübergreifende Apps](security.md)
- [Bewährte Sicherheitsmethoden für Azure Virtual Desktop](../security-guide.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Monitor für Azure Virtual Desktop: Glossar](../azure-monitor-glossary.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Virtual Desktop für Unternehmen](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
- [Schätzen der gesamten Bereitstellungskosten](total-costs.md)
- [Schätzen der App-Streamingkosten auf Benutzerebene](streaming-costs.md)
- [Architekturempfehlungen](architecture-recs.md)
- [Häufig gestellte Fragen zum Starten von virtuellen Computern bei der Verbindungsherstellung](../start-virtual-machine-connect-faq.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit der manuellen Einrichtung Ihrer Bereitstellung beginnen möchten, sehen Sie sich das folgende Tutorial an.

> [!div class="nextstepaction"]
> [Erstellen eines Hostpools mit dem Azure-Portal](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

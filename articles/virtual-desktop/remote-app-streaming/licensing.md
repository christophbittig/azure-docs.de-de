---
title: Preise für die Lizenzierung des benutzerspezifischen Zugriffs in Azure Virtual Desktop für Remote-App-Streaming – Azure
description: Eine Übersicht über Überlegungen zur Lizenzierung von Azure Virtual Desktop für das Remote-App-Streaming.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 54f164c60a5602bcfe798c98785fca1aa8513279
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292850"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff

In diesem Artikel werden die Lizenzierungsanforderungen für die Verwendung von Azure Virtual Desktop zum Streamen von Remoteanwendungen für externe Benutzer erläutert. In diesem Artikel erfahren Sie, wie sich die Lizenzierung von Azure Virtual Desktop für externe Benutzer von der für interne Benutzer unterscheidet, wie die Preisgestaltung für den benutzerspezifischen Zugriff im Detail funktioniert und wie Sie andere Produkte lizenzieren, die Sie mit Azure Virtual Desktop verwenden möchten.

## <a name="internal-users-and-external-users"></a>Interne Benutzer und externe Benutzer

Im Zusammenhang mit der Bereitstellung einer virtualisierten Infrastruktur mit Azure Virtual Desktop bezieht sich **interner Benutzer** auf Personen, die Mitglieder Ihrer eigenen Organisation sind, zum Beispiel Mitarbeiter eines Unternehmens oder Schüler einer Schule. **Externe Benutzer** sind nicht Mitglieder Ihrer Organisation, zum Beispiel Kunden eines Unternehmens.

>[!NOTE]
>Achten Sie darauf, dass Sie externe *Nutzer* nicht mit externen *Identitäten* verwechseln. Azure Virtual Desktop unterstützt derzeit keine externen Identitäten, einschließlich Gastzugänge und B2B-Identitäten (Business-to-Business). Unabhängig davon, ob Sie interne oder externe Nutzer mit dem Azure Virtual Desktop bedienen, müssen Sie Identitäten für diese Nutzer selbst erstellen und verwalten. Die Preise für den Zugriff pro Nutzer sind keine Möglichkeit, Gastnutzerkonten mit dem Azure Virtual Desktop zu aktivieren. Weitere Informationen finden Sie unter [Architektur.-Empfehlungen](architecture-recs.md).

Die Lizenzierung von Azure Virtual Desktop funktioniert für interne und externe Benutzer unterschiedlich. Betrachten Sie die folgenden Beispiele:

- Ein Fertigungsunternehmen namens Fabrikam, Inc. könnte Azure Virtual Desktop verwenden, um den Mitarbeitern (internen Benutzern) von Fabrikam Zugriff auf virtuelle Workstations und Geschäftsanwendungen zu bieten. Da Fabrikam interne Benutzer bedient, muss Fabrikam für jeden seiner Mitarbeiter, der auf Azure Virtual Desktop zugreift, eine der in der [Azure Virtual Desktop-Preisliste](https://azure.microsoft.com/pricing/details/virtual-desktop/) aufgeführten berechtigten Lizenzen erwerben.

- Ein Softwareanbieter namens Contoso könnte Azure Virtual Desktop verwenden, um Remote-Streams der Produktivitätsanwendung von Contoso an Kunden von Contoso (externe Benutzer) zu verkaufen. Da Contoso externe Benutzer bedient, muss Contoso sich bei den Preisen für den benutzerspezifischen Zugriff von Azure Virtual Desktop registrieren. Dies ermöglicht Contoso die Zahlung für Azure Virtual Desktop-Zugriffsrechte im Namen der externen Benutzer, die eine Verbindung mit der Bereitstellung von Contoso herstellen. Die Benutzer in der Bereitstellung benötigen keine separate Lizenz wie etwa Microsoft 365, um auf Azure Virtual Desktop zuzugreifen. Contoso muss jedoch Identitäten für diese externen Benutzer erstellen und verwalten.

## <a name="per-user-access-pricing-for-azure-virtual-desktop"></a>Preise für benutzerspezifischen Zugriff für Azure Virtual Desktop

Mit den Preisen für den benutzerspezifischen Zugriff können Sie für Azure Virtual Desktop-Zugriffsrechte im Namen externer Benutzer bezahlen. Um eine konforme Bereitstellung für externe Benutzer erstellen zu können, müssen Sie sich für den Preis für den Zugriff auf Benutzerebene registrieren. Weitere Informationen finden Sie unter [Registrieren für Preise für den Zugriff auf Benutzerebene](per-user-access-pricing.md).

Sie zahlen für den Zugriff auf Benutzerebene über Ihre registrierten Azure-Abonnements zusätzlich zu Ihren Gebühren für virtuelle Computer, Speicher und andere Azure-Dienste. Bei jedem Abrechnungszeitraum zahlen Sie nur für Benutzer, die den Dienst tatsächlich genutzt haben. Nur für Benutzer, die in diesem Monat mindestens einmal eine Verbindung mit Azure Virtual Desktop hergestellt haben, fällt eine Zugriffsgebühr an.

Es gibt zwei Tarife für den Zugriff auf Azure Virtual Desktop auf Benutzerebene. Die Gebühren werden automatisch in jedem Abrechnungszeitraum auf der Grundlage der Art der [Anwendungsgruppen](../environment-setup.md#app-groups) ermittelt, mit denen ein Benutzer verbunden ist.

- Der erste Tarif trägt die Bezeichnung „Apps“. Dieser Pauschalpreis wird für jeden Benutzer berechnet, der auf mindestens eine RemoteApp-Anwendungsgruppe und keine Desktopanwendungsgruppen zugreift.
- Der zweite Tarif ist „Apps + Desktops“. Dieser Pauschalpreis wird für jeden Benutzer berechnet, der auf mindestens eine Desktopanwendungsgruppe zugreift.
- Wenn ein Benutzer auf keine Anwendungsgruppen zugreift, fallen keine Kosten an.

Weitere Informationen zu den Preisen finden Sie unter [Azure Virtual Desktop – Preise](https://azure.microsoft.com/pricing/details/virtual-desktop/).

Jeder Tarif verfügt über pauschale Gebühren pro Zugriff auf Benutzerebene. Ein Benutzer verursacht z. B. dieselben Kosten für Ihr Abonnement, unabhängig davon, wann oder wie viele Stunden er den Dienst in diesem Abrechnungszeitraum verwendet hat.

Azure Virtual Desktop wird auch Benutzern mit separat zugewiesenen Lizenzen in Rechnung gestellt, die ansonsten zum Zugriff auf Azure Virtual Desktop berechtigen. Wenn Sie über interne Benutzer verfügen, für die Sie berechtigte Lizenzen erwerben, empfehlen wir Ihnen, ihnen den Zugriff auf Azure Virtual Desktop über ein separates Abonnement zu gewähren, das nicht in den Preis für den Zugriff auf Benutzerebene einbezogen ist, um zu vermeiden, dass Sie für diese Benutzer doppelt zahlen.

Azure Virtual Desktop erhebt höchstens eine Zugriffsgebühr für einen bestimmten Benutzer in einem bestimmten Abrechnungszeitraum. Wenn Ihre Bereitstellung also dem Benutzer „Alice“ Zugriff auf Azure Virtual Desktop-Ressourcen über zwei verschiedene Azure-Abonnements im selben Mandanten gewährt, fällt nur für das erste Abonnement, auf das Alice zugreift, eine Nutzungsgebühr an.

## <a name="comparing-licensing-options"></a>Vergleichen von Lizenzierungsoptionen

Im Folgenden finden Sie eine Zusammenfassung der beiden Arten von Lizenzen für Azure Virtual Desktop, aus der Sie wählen können:

- Eine Windows- oder Microsoft 365-Lizenz:
  - Gewährt Azure Virtual Desktop-Zugriffsrechte ausschließlich für interne Benutzer
  - Im Voraus über ein Abonnement bezahlt
  - Gleiche Kosten pro Benutzer und Monat, unabhängig vom Benutzerverhalten.
  - Umfasst Berechtigungen für einige andere Microsoft-Produkte und -Dienste.

- Preise für benutzerspezifischen Zugriff:
  - Gewährt Azure Virtual Desktop-Zugriffsrechte ausschließlich für externe Benutzer.
  - Nutzungsbasierte Bezahlung über eine Azure-Verbrauchseinheit.
  - Die Kosten pro Benutzer und Monat hängen vom Benutzerverhalten ab
  - Umfasst nur Zugriffsrechte für Azure Virtual Desktop.

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>Lizenzieren anderer Produkte und Dienste für die Verwendung mit Azure Virtual Desktop

Die Azure Virtual Desktop-Lizenz für den Zugriff auf Benutzerebene ist kein vollständiger Ersatz für eine Windows- oder Microsoft 365-Lizenz. Lizenzen pro Benutzer gewähren nur Zugriffsrechte auf den virtuellen Azure-Desktop (Azure Virtual Desktop) und umfassen nicht Microsoft Office, Microsoft 365 Defender oder Universal Print. Das bedeutet, dass Sie, wenn Sie sich für eine Lizenz pro Benutzer entscheiden, andere Produkte und Dienste separat lizenzieren müssen, um Ihren Benutzern den Zugriff auf diese in Ihrer Azure Virtual Desktop-Umgebung zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie mit den Optionen für die Lizenzierung vertraut sind, können Sie mit der Planung Ihrer Azure Virtual Desktop-Umgebung beginnen. Hier sind einige Artikel, die Ihnen helfen könnten:

- [Registrieren Ihres Abonnements für Preise für den Zugriff auf Benutzerebene](per-user-access-pricing.md)
- [Schätzen der App-Streamingkosten auf Benutzerebene für Azure Virtual Desktop](streaming-costs.md)
- Wenn Sie sich für die Einrichtung Ihrer ersten Bereitstellung bereit fühlen, können Sie mit unseren [Tutorials](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json) beginnen.

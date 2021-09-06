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
ms.openlocfilehash: 2fb4ec6ebe5960a6a782b3d76fe2aaec91603302
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798949"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff

In diesem Artikel werden die Lizenzierungsanforderungen für die Verwendung von Azure Virtual Desktop zum Streamen von Remoteanwendungen für externe Benutzer erläutert. In diesem Artikel erfahren Sie, wie sich die Lizenzierung von Azure Virtual Desktop für externe Benutzer von der für interne Benutzer unterscheidet, wie die Preisgestaltung für den benutzerspezifischen Zugriff im Detail funktioniert und wie Sie andere Produkte lizenzieren, die Sie mit Azure Virtual Desktop verwenden möchten.

## <a name="licensing-azure-virtual-desktop-for-external-users"></a>Lizenzieren von Azure Virtual Desktop für externe Benutzer

Es gibt zwei Möglichkeiten für die Verwendung von Azure Virtual Desktop. Jede Möglichkeit weist unterschiedliche Anforderungen an die Lizenzierung auf:

- Für das Streamen von RemoteApps und Desktops für interne Benutzer. Das Produktionsunternehmen Fabrikam, Inc. könnte z. B. Azure Virtual Desktop verwenden, um den Mitarbeitern von Fabrikam Zugriff auf virtuelle Workstations und Branchenanwendungen zu verschaffen. In diesem Fall muss Fabrikam für jeden seiner Mitarbeiter, der auf Azure Virtual Desktop zugreift, eine der in der [Preisliste für Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) aufgeführten Lizenzen erwerben.

- Für das Streamen von RemoteApps und Desktops für externe Benutzer. Beispielsweise könnte ein Softwareanbieter namens Contoso Azure Virtual Desktop verwenden, um Kunden von Contoso (Benutzer, die keine Contoso-Mitarbeiter sind) Remotestreams der Produktivitätsanwendung von Contoso bereitzustellen. In diesem Fall muss sich Contoso für den Azure Virtual Desktop-Preis für den benutzerspezifischen Zugriff registrieren. Mit diesem Lizenztyp kann Contoso für Azure Virtual Desktop-Zugriffsrechte im Namen dieser Benutzer über eine Azure-Verbrauchseinheit bezahlen, die auf der Anzahl der Benutzer basiert, die jeden Monat auf Azure Virtual Desktop zugreifen. Die Benutzer in der Bereitstellung benötigen keine separate Lizenz wie Microsoft 365, um auf Azure Virtual Desktop zuzugreifen.

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

Wie in [Preise für benutzerspezifischen Zugriff für Azure Virtual Desktop](#per-user-access-pricing-for-azure-virtual-desktop) erwähnt, gibt es zwei Arten von Lizenzen für Azure Virtual Desktop, aus denen Sie wählen können:

- Eine Windows- oder Microsoft 365-Lizenz:
   - Gewährt Azure Virtual Desktop-Zugriffsrechte ausschließlich für interne Benutzer.
   - Im Voraus über ein Abonnement bezahlt.
   - Gleiche Kosten pro Benutzer und Monat, unabhängig vom Benutzerverhalten.
   - Umfasst Berechtigungen für einige andere Microsoft-Produkte und -Dienste.

- Preise für benutzerspezifischen Zugriff:
   - Gewährt Azure Virtual Desktop-Zugriffsrechte ausschließlich für externe Benutzer.
   - Nutzungsbasierte Bezahlung über eine Azure-Verbrauchseinheit.
   - Dynamische Kosten pro Benutzer und Monat basierend auf dem Benutzerverhalten.
   - Umfasst nur Zugriffsrechte für Azure Virtual Desktop.

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>Lizenzieren anderer Produkte und Dienste für die Verwendung mit Azure Virtual Desktop

Die Azure Virtual Desktop-Lizenz für den Zugriff auf Benutzerebene ist kein vollständiger Ersatz für eine Windows- oder Microsoft 365-Lizenz. Lizenzen pro Benutzer gewähren nur Zugriffsrechte auf Azure Virtual Desktop und umfassen nicht Microsoft Office, Microsoft Defender oder Universal Print. Das bedeutet, dass Sie, wenn Sie sich für eine Lizenz pro Benutzer entscheiden, andere Produkte und Dienste separat lizenzieren müssen, um Ihren Benutzern den Zugriff auf diese in Ihrer Azure Virtual Desktop-Umgebung zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie mit den Optionen für die Lizenzierung vertraut sind, können Sie mit der Planung Ihrer Azure Virtual Desktop-Umgebung beginnen. Hier sind einige Artikel, die Ihnen helfen könnten:

- [Registrieren Ihres Abonnements für Preise für den Zugriff auf Benutzerebene](per-user-access-pricing.md)
- [Schätzen der App-Streamingkosten auf Benutzerebene für Azure Virtual Desktop](streaming-costs.md)
- Wenn Sie sich für die Einrichtung Ihrer ersten Bereitstellung bereit fühlen, können Sie mit unseren [Tutorials](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json) beginnen.

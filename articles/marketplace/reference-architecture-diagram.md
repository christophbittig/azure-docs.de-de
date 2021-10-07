---
title: Diagramm zur Referenzarchitektur | Azure Marketplace
description: Hier erfahren Sie, wie Sie für ein Angebot im kommerziellen Marketplace von Microsoft ein Diagramm zur Referenzarchitektur erstellen.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/22/2021
ms.openlocfilehash: 63c820a7dd4bcbe1322478a46043963edc45dd2b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129054260"
---
# <a name="reference-architecture-diagram"></a>Diagramm zur Referenzarchitektur

Das Diagramm zur Referenzarchitektur ist ein Modell zur Darstellung der Infrastruktur, auf der Ihr Angebot basiert. Bei Azure-IP-Lösungen muss das Diagramm zudem Aufschluss über die Nutzung der Microsoft-Clouddienste durch Ihr Angebot geben (gemäß den technischen Anforderungen des IP-Co-Sellings). Es ist nicht darauf ausgelegt, die Qualität der Architektur zu bewerten. Vielmehr soll es die Nutzung von Microsoft-Diensten durch Ihre Lösung veranschaulichen.

Das Diagramm zur Referenzarchitektur kann über mehrere Tools erstellt werden. Microsoft Visio wird empfohlen, da es mehrere Schablonen für Azure-Architekturmodelle enthält.

Einen hilfreichen Ausgangspunkt zum Entwickeln eines Diagramms zur Referenzarchitektur bieten die [Azure-Architekturmodelle](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Typische Komponenten eines Diagramms zur Referenzarchitektur

Im Diagramm muss Ihre IP-Adresse eindeutig als Lösungs-, Anwendungs- oder Dienstcode identifiziert werden, der sowohl in Azure bereitgestellt wird als auch den Verbrauch fördert. Dieser Code muss in hohem Maße wiederverwendbar sein und darf nicht von einer umfassenden Anpassung pro Bereitstellung abhängen.

- Clouddienste, die Ihr Angebot hosten und mit ihm interagieren (einschließlich Dienste, die Azure-Ressourcen beanspruchen)
- Von Ihrem Angebot genutzte Datenverbindungen, Datenschichten und Datendienste
- Clouddienste zum Steuern von Sicherheit, Authentifizierung und Benutzern des Angebots
- Benutzeroberflächen und andere Dienste, über die das Angebot für Benutzer verfügbar gemacht wird
- Hybridkonnektivität und/oder lokale Konnektivität sowie Integrationen (oder eine Kombination aus beidem)

Der folgende Screenshot zeigt ein exemplarisches Diagramm zur Referenzarchitektur:

[![Dieses Bild ist ein Beispieldiagramm für eine Co-Selling-Architektur.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Dieses exemplarische Diagramm zur Referenzarchitektur stellt einen Chatbot für eine vertikale Branche dar, der in Intranetsites integriert werden kann, um Benutzer über einen Algorithmus für maschinelles Lernen bei der Bedarfsprognose zu unterstützen. Die Lösung nutzt Lieferketten- und Produktionsplandaten aus verschiedenen ERP-Systemen. Der Bot soll beispielsweise die Frage beantworten, wann ein Vertriebsmitarbeiter mögliche Liefertermine für eine Bestellung zusagen kann.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md)

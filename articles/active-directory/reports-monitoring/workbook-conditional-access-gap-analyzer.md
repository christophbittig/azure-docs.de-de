---
title: Arbeitsmappe „Lückenanalyse für bedingten Zugriff“ in Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Arbeitsmappe „Lückenanalyse für bedingten Zugriff“ verwenden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd62dbd1fc279e2ed26472b1c0a0464b56be92c0
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990552"
---
# <a name="conditional-access-gap-analyzer-workbook"></a>Arbeitsmappe „Lückenanalyse für bedingten Zugriff“

In Azure AD können Sie den Zugriff auf Ihre Ressourcen schützen, indem Sie Richtlinien für bedingten Zugriff konfigurieren.
Als IT-Administrator möchten Sie sicherstellen, dass Ihre Richtlinien für bedingten Zugriff wie erwartet funktionieren, um sicherzustellen, dass Ihre Ressourcen ordnungsgemäß geschützt sind. Mit der Arbeitsmappe „Lückenanalyse für bedingten Zugriff“ können Sie Lücken in Ihrer Implementierung des bedingten Zugriffs erkennen.  

Dieser Artikel enthält eine Übersicht über diese Arbeitsmappe.


## <a name="description"></a>BESCHREIBUNG

![Arbeitsmappenkategorie](./media/workbook-conditional-access-gap-analyzer/workbook-category.png)

Als IT-Administrator möchten Sie sicherstellen, dass nur die richtigen Personen auf Ihre Ressourcen zugreifen können. Der bedingte Zugriff von Azure AD hilft Ihnen dabei, dieses Ziel zu erreichen.  

Mithilfe der Arbeitsmappe „Lückenanalyse für bedingten Zugriff“ können Sie überprüfen, ob Ihre Richtlinien für bedingten Zugriff wie erwartet funktionieren.

**Diese Arbeitsmappe bietet folgende Möglichkeiten:**

- Hervorheben von Benutzeranmeldungen, auf die keine Richtlinien für bedingten Zugriff angewendet wurden 
- Sicherstellen, dass keine Benutzer, Anwendungen oder Standorte unbeabsichtigt von Richtlinien für bedingten Zugriff ausgeschlossen wurden.  

 

## <a name="sections"></a>Abschnitte


Die Arbeitsmappe enthält vier Abschnitte:  

- Benutzeranmeldungen mit Legacyauthentifizierung 

- Anzahl der Anmeldungen von Anwendungen, für die keine Richtlinien für bedingten Zugriff gelten 

- Anmeldeereignisse mit hohem Risiko durch Umgehen von Richtlinien für bedingten Zugriff 

- Anzahl der Anmeldungen nach Standort, auf die keine Richtlinien für bedingten Zugriff angewendet wurden 


![Abdeckung des bedingten Zugriffs nach Standort](./media/workbook-conditional-access-gap-analyzer/conditianal-access-by-location.png)

Jeder dieser Trends stellt eine Aufschlüsselung der Anmeldungen auf Benutzerebene bereit, sodass Sie sehen können, welche Benutzer im jeweiligen Szenario den bedingten Zugriff umgehen. 

## <a name="filters"></a>Filter

Diese Arbeitsmappe unterstützt das Festlegen eines Zeitbereichsfilters.

![Zeitbereichsfilter](./media/workbook-conditional-access-gap-analyzer/time-range.png)



## <a name="best-practices"></a>Bewährte Methoden

Stellen Sie mit dieser Arbeitsmappe sicher, dass für die Konfiguration Ihres Mandanten die folgenden bewährten Methoden für bedingten Zugriff verwendet wurden:  

- Blockieren aller Anmeldungen mit Legacyauthentifizierung 

- Anwenden von mindestens einer Richtlinie für bedingten Zugriff auf jede Anwendung 

- Blockieren aller Anmeldungen mit hohem Risiko  

- Blockieren von Anmeldungen von nicht vertrauenswürdigen Standorten  

 





## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure AD-Arbeitsmappen](howto-use-azure-monitor-workbooks.md)

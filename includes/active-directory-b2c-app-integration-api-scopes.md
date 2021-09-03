---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 6b4ee09212bc62f43c583c73299ac33a842dc942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338910"
---
1. Wählen Sie die von Ihnen erstellte Anwendung *my-api1* (App-ID: 2) aus, um deren Seite **Übersicht** zu öffnen.

1. Wählen Sie unter **Verwalten** die Option **Eine API verfügbar machen** aus.
1. Wählen Sie neben **Anwendungs-ID-URI** den Link **Festlegen** aus. Ersetzen Sie den Standardwert (GUID) durch einen eindeutigen Namen, z. B. *tasks-api*, und wählen Sie dann **Speichern** aus. 
 
   Wenn Ihre Webanwendung ein Zugriffstoken für die Web-API anfordert, sollte sie diesen URI als Präfix für jeden Bereich hinzufügen, den Sie für die API definieren.
1. Wählen Sie unter **Durch diese API definierte Bereiche** die Option **Bereich hinzufügen** aus.
1. Geben Sie die folgenden Werte ein, um einen Bereich zu erstellen, der Lesezugriff auf die API festlegt:

    a. **Bereichsname:** *tasks.read*  
    b. **Anzeigename der Administratoreinwilligung**: *Lesezugriff auf Aufgaben-API*  
    c. **Beschreibung der Administratoreinwilligung**: *Lässt Lesezugriff auf die Aufgaben-API zu*

1. Wählen Sie **Bereich hinzufügen** aus.

1. Wählen Sie **Bereich hinzufügen** aus, und geben Sie dann einen Bereich ein, der Schreibzugriff auf die API festlegt, indem Sie die folgenden Werte eingeben: 

    a. **Bereichsname:** *tasks.write*  
    b. **Anzeigename der Administratoreinwilligung**: *Schreibzugriff auf Aufgaben-API*  
    c. **Beschreibung der Administratoreinwilligung**: *Lässt Schreibzugriff auf die Aufgaben-API zu*
1. Wählen Sie **Bereich hinzufügen** aus.
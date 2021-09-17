---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 74c9ea66cfe94a8c856f1a73e968b5f4ea5bb049
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227640"
---
1. Wählen Sie die von Ihnen erstellte Anwendung **my-api1** (**App-ID: 2**) aus, um deren Seite **Übersicht** zu öffnen.

1. Wählen Sie unter **Verwalten** die Option **Eine API verfügbar machen** aus.
1. Wählen Sie neben **Anwendungs-ID-URI** den Link **Festlegen** aus. Ersetzen Sie den Standardwert (GUID) durch einen eindeutigen Namen, z. B. **tasks-api**, und wählen Sie dann **Speichern** aus. 
 
   Wenn Ihre Webanwendung ein Zugriffstoken für die Web-API anfordert, sollte sie diesen URI als Präfix für jeden Bereich hinzufügen, den Sie für die API definieren.
1. Wählen Sie unter **Durch diese API definierte Bereiche** die Option **Bereich hinzufügen** aus.
1. So erstellen Sie einen Bereich, der Lesezugriff auf die API festlegt:

    1. Geben Sie für **Bereichsname** **tasks.read** ein.  
    1. Geben Sie für **Anzeigename der Administratoreinwilligung** **Lesezugriff auf Aufgaben-API** ein.  
    1. Geben Sie für **Beschreibung der Administratoreinwilligung** **Lässt Lesezugriff auf die Aufgaben-API zu** ein.

1. Wählen Sie **Bereich hinzufügen** aus.

1. Wählen Sie **Bereich hinzufügen** aus, und geben Sie dann einen Bereich ein, der den Schreibzugriff auf die API festlegt: 

    1. Geben Sie für **Bereichsname** **tasks.write** ein.  
    1. Geben Sie für **Anzeigename der Administratoreinwilligung** **Schreibzugriff auf Aufgaben-API** ein.
    1. Geben Sie für **Beschreibung der Administratoreinwilligung** **Lässt Schreibzugriff auf die Aufgaben-API zu** ein.
    
1. Wählen Sie **Bereich hinzufügen** aus.

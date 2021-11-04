---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: aeb4c7ef44791b174940790fa3e03f0f40ed1ba4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095962"
---
Nachdem Ihre Ressource und der Speichercontainer konfiguriert wurden, erstellen Sie ein neues benutzerdefiniertes NER-Projekt. Ein Projekt ist ein Arbeitsbereich zum Erstellen Ihrer benutzerdefinierten KI-Modelle auf der Grundlage Ihrer Daten. Auf Ihr Projekt können nur Sie und andere Personen zugreifen, die Mitwirkendenzugriff auf die verwendete Azure-Ressource haben.

1. Melden Sie sich beim [Language Studio-Portal](https://aka.ms/languageStudio) an. Es wird ein Fenster angezeigt, in dem Sie Ihr Abonnement und Ihre Sprachressource auswählen können. Wählen Sie die Ressource aus, die Sie im Schritt oben erstellt haben. 

2. Suchen Sie den Abschnitt **Entitätsextraktion**, und wählen Sie aus den verfügbaren Diensten **Benutzerdefinierte Erkennung benannter Entitäten** aus.

3. Wählen Sie im oberen Menü ihrer Projektseite **Neues Projekt erstellen** aus. Durch das Erstellen eines Projekts können Sie Daten kennzeichnen sowie Ihre Modelle trainieren, auswerten, verbessern und bereitstellen. 

    
    :::image type="content" source="../media/create-project.png" alt-text="Screenshot der Seite zur Projekterstellung." lightbox="../media/create-project.png":::


4.  Nachdem Sie auf **Neues Projekt erstellen** geklickt haben,wird ein Bildschirm angezeigt, auf dem Sie eine Verbindung mit Ihrem Speicherkonto herstellen können. Wenn Sie Ihr Speicherkonto nicht finden können, vergewissern Sie sich, dass Sie anhand der oben beschriebenen Schritte eine Ressource erstellt haben. 

    >[!NOTE]
    > * Sie müssen diesen Schritt nur einmal für jede neue Ressource durchführen, die Sie verwenden. 
    > * Dieser Prozess kann nicht rückgängig gemacht werden – wenn Sie ein Speicherkonto mit Ihrer Ressource verbinden, können Sie die Verbindung später nicht trennen.
    > * Sie können Ihre Ressource nur mit einem Speicherkonto verbinden.
    > * Wenn Sie bereits eine Verbindung mit einem Speicherkonto hergestellt haben, wird stattdessen der Bildschirm **Projekttyp auswählen** angezeigt. Mehr dazu finden Sie im nächsten Schritt.
    
    :::image type="content" source="../media/connect-storage.png" alt-text="Screenshot: Bildschirm zum Herstellen von Speicherverbindungen." lightbox="../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. Geben Sie die Projektinformationen ein, einschließlich eines Namens, einer Beschreibung und der Sprache der Dateien in Ihrem Projekt. Sie können den Namen Ihres Projekts später nicht mehr ändern. 

6. Überprüfen Sie die eingegebenen Daten, und wählen Sie **Projekt erstellen** aus.

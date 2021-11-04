---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b71810497c555a33fcf8a7359c32c8397cb6cec8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095020"
---
Nachdem Ihre Ressource und der Speichercontainer konfiguriert wurden, erstellen Sie ein neues Textklassifizierungsprojekt. Ein Projekt ist ein Arbeitsbereich zum Erstellen Ihrer benutzerdefinierten KI-Modelle auf der Grundlage Ihrer Daten. Auf Ihr Projekt können nur Sie und andere Personen zugreifen, die Mitwirkendenzugriff auf die verwendete Azure-Ressource haben.

1. Melden Sie sich bei [Language Studio](https://aka.ms/languageStudio) an. Es wird ein Fenster angezeigt, in dem Sie Ihr Abonnement und Ihre Sprachressource auswählen können. Wählen Sie die Ressource aus, die Sie im Schritt oben erstellt haben.

2. Wählen Sie im Abschnitt **Text klassifizieren** von Language Studio in den verfügbaren Diensten die **benutzerdefinierte Textklassifizierung** aus, und wählen Sie sie aus.

3. Wählen Sie im oberen Menü Ihrer Projektseite **Neues Projekt erstellen** aus. Durch das Erstellen eines Projekts können Sie Daten kennzeichnen sowie Ihre Modelle trainieren, auswerten, verbessern und bereitstellen. 

    :::image type="content" source="../media/create-project.png" alt-text="Screenshot der Seite zur Projekterstellung." lightbox="../media/create-project.png":::

4. Wenn Sie Ihre Ressource mit den Schritten oben erstellt haben, müssen Sie Ihrem Projekt Informationen hinzufügen, z. B. einen Namen, und Ihren Speichercontainer auswählen.

    1. Wählen Sie Ihren Projekttyp aus. Für diese Schnellstartanleitung erstellen wir ein Klassifizierungsprojekt mit mehreren Bezeichnungen. Klicken Sie dann auf **Weiter**.

    2. Geben Sie die Projektinformationen ein, einschließlich eines Namens, einer Beschreibung und der Sprache der Dateien in Ihrem Projekt. Sie können den Namen Ihres Projekts später nicht mehr ändern.

        >[!TIP]
        > Ihr Dataset muss nicht zur Gänze in derselben Sprache vorliegen. Sie können mehrere Dateien verwenden, jede mit jeweils einer anderen unterstützten Sprache. Wenn Ihr Dataset Dateien in verschiedenen Sprachen enthält oder Sie zur Laufzeit mit verschiedenen Sprachen rechnen, wählen Sie **mehrsprachiges Dataset aktivieren** aus, wenn Sie die grundlegenden Informationen für Ihr Projekt eingeben.

    3. Wählen Sie den Container aus, in den Sie Ihre Daten hochgeladen haben. Für diese Schnellstartanleitung verwenden wir die vorhandene Tagdatei, die im Container verfügbar ist. Klicken Sie dann auf **Weiter**.
 
    4. Überprüfen Sie die eingegebenen Daten, und wählen Sie **Projekt erstellen** aus.

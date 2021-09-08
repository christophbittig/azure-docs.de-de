---
title: Datei einfügen
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7f4a2e5a7186032995ee277dfb9f2c226853163b
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573980"
---
## <a name="create-an-image-gallery"></a>Erstellen eines Imagekatalogs

Ein Imagekatalog ist die primäre Ressource, die zur Ermöglichung des Teilens von Images verwendet wird. Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten.  Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Im folgenden Beispiel wird der Katalog *myGallery* in der Ressourcengruppe *myGalleryRG* erstellt.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Geben Sie in das Suchfeld **Katalog mit freigegebenen Images** ein und wählen Sie in den Ergebnissen **Katalog mit freigegebenen Images** aus.
1. Klicken Sie auf der Seite **Katalog der freigegebenen Images** auf **Hinzufügen**.
1. Wählen Sie auf der Seite **Katalog mit freigegebenen Images erstellen** das richtige Abonnement aus.
1. Wählen Sie in **Ressourcengruppe** **Neu erstellen** aus, und geben Sie *myGalleryRG* für den Namen ein.
1. Geben Sie unter **Name** *myGallery* als Namen des Katalogs ein.
1. Übernehmen Sie den Standardwert für **Region**.
1. Sie können eine kurze Beschreibung des Katalogs eingeben, wie *Mein Imagekatalog zum Testen*. Klicken Sie dann auf **Überprüfen + Erstellen**.
1. Wenn die Überprüfung erfolgreich war, wählen Sie **Erstellen** aus.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.


## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition 

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden. 

Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../articles/virtual-machines/shared-image-galleries.md#image-definitions).

Erstellen Sie die Definition des Katalogimages innerhalb Ihres Katalogs. 

1. Wählen Sie auf der Seite Ihres neuen Imagekatalogs **Neue Imagedefinition hinzufügen** oben auf der Seite aus. 
1. Wählen Sie unter **Neue Imagedefinition zum Katalog mit freigegebenen Images hinzufügen** für **Region** die Option *USA, Osten* aus.
1. Geben Sie für **Name der Imagedefinition** einen Namen wie *meineImageDefinition* ein.
1. Wählen Sie für **Betriebssystem** die richtige Option basierend auf Ihrer Quell-VM.  
1. Wählen Sie für **VM-Generation** die Option basierend auf Ihrer Quell-VM aus. In den meisten Fällen wird dies *1. Generation* sein. Weitere Informationen finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](../articles/virtual-machines/generation-2.md).
1. Wählen Sie für **Betriebssystemstatus** die richtige Option basierend auf Ihrer Quell-VM. Weitere Informationen finden Sie unter [Generalisierte und spezialisierte Images](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).
1. Geben Sie für **Herausgeber** *myPublisher* ein. 
1. Geben Sie für **Angebot** *myOffer* ein.
1. Geben Sie für **SKU** *mySKU* ein.
1. Wenn Sie fertig sind, wählen Sie **Überprüfen und erstellen** aus.
1. Nachdem die Imagedefinition erfolgreich überprüft wurde, wählen Sie **Erstellen**.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.


## <a name="create-an-image-version"></a>Erstellen einer Imageversion

 Bei der Auswahl der Zielregionen für die Replikation ist zu beachten, dass Sie auch die *Quell* region als Ziel für die Replikation angeben müssen.

Die Schritte zum Erstellen einer Imageversion unterscheiden sich geringfügig, abhängig davon, ob es sich bei der Quelle um ein generalisiertes Image oder eine Momentaufnahme eines spezialisierten virtuellen Computers handelt. 


1. Wählen Sie oben auf der Seite für Ihre Imagedefinition **Version hinzufügen** aus.
1. Wählen Sie unter **Region** die Region aus, in der das Image erstellt werden soll.
1. Geben Sie für **Versionsnummer** eine Zahl wie *1.0.0* ein. Der Name der Imageversion sollte dem Format *major*.*minor*.*patch* mit ganzen Zahlen entsprechen. 
1. Wählen Sie unter **Quellimage** aus der Dropdownliste das von der Quelle verwaltete Image aus. In der folgenden Tabelle finden Sie spezifische Angaben zu den einzelnen Quellentypen.

    | `Source` | Weitere Felder |
    |---|---|
    | Datenträger oder Momentaufnahmen | – Wählen Sie für **Betriebssystemdatenträger** den Datenträger oder die Momentaufnahme aus der Dropdownliste aus. <br> – Zum Hinzufügen eines Datenträgers geben Sie die LUN-Nummer ein, und wählen Sie dann in der Dropdownliste den Datenträger aus. |
    | Imageversion | – Wählen Sie in der Dropdownliste den **Quellkatalog** aus. <br> – Wählen Sie in der Dropdownliste die richtige Bilddefinition aus. <br>– Wählen Sie in der Dropdownliste die vorhandene Imageversion aus, die Sie verwenden möchten. |
    | Verwaltetes Image | – Wählen Sie in der Dropdownliste das **Quellimage** aus. <br>Das verwaltete Image muss sich in derselben Region befinden, die Sie unter **Instanzdetails** ausgewählt haben.
    | VHD in einem Speicherkonto | Wählen Sie **Durchsuchen** aus, um das Speicherkonto für die VHD auszuwählen. |

1. Übernehmen Sie für **Aus aktueller Version ausschließen** den Standardwert *Nein*.
1. Wählen Sie unter **Datum für Ende des Lebenszyklus** ein Datum aus dem Kalender aus, das einige Monate in der Zukunft liegt.
1. Wählen Sie auf der Registerkarte **Replikation** in der Dropdownliste den Speichertyp aus.
1. Legen Sie die **Anzahl von Standardreplikaten** fest. Diese können Sie für jede hinzugefügte Region überschreiben. 
1. Sie müssen in die Quellregion replizieren, damit sich das erste Replikat in der Liste in der Region befindet, in der Sie das Image erstellt haben. Sie können weitere Replikate hinzufügen, indem Sie die Region in der Dropdownliste auswählen und die Anzahl der Replikate nach Bedarf anpassen.
1. Wählen Sie abschließend **Überprüfen + Erstellen** aus. Azure überprüft die Konfiguration.
1. Nachdem die Imageversion erfolgreich überprüft wurde, wählen Sie **Erstellen**.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.

Es kann eine Weile dauern, bis das Image in alle Zielregionen repliziert ist.

Sie können eine vorhandene VM auch über das Portal als Image erfassen. Weitere Informationen finden Sie unter [Erstellen eines Images eines virtuellen Computers im Portal](../articles/virtual-machines/capture-image-portal.md).

## <a name="share-the-gallery"></a>Teilen des Katalogs

Wir empfehlen, dass Sie den Zugriff auf der Ebene des Imagekatalogs teilen. Im Folgenden werden Sie durch das Teilen des Katalogs geführt, den Sie gerade erstellt haben.

1. Wählen Sie auf der Seite für Ihren neuen Imagekatalog im Menü auf der linken Seite die Option **Zugriffssteuerung (IAM)** aus. 
1. Wählen Sie unter **Rollenzuweisung hinzufügen** die Option **Hinzufügen** aus. Der Bereich **Rollenzuweisung hinzufügen** wird geöffnet. 
1. Wählen Sie unter **Rolle** die Option **Leser** aus.
1. Behalten Sie unter **Zugriff zuweisen zu** den Standardwert von **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.
1. Geben Sie unter **Auswählen** die E-Mail-Adresse der Person ein, die Sie einladen möchten.
1. Wenn der Benutzer außerhalb Ihrer Organisation ist, wird die Meldung **Dieser Benutzer erhält eine E-Mail, die ihm die Zusammenarbeit mit Microsoft ermöglicht.** angezeigt. Wählen Sie den Benutzer mit der E-Mail-Adresse aus, und klicken Sie dann auf **Speichern**.

Wenn sich der Benutzer außerhalb Ihrer Organisation befindet, erhält er eine E-Mail-Einladung, um der Organisation beizutreten. Der Benutzer muss die Einladung annehmen, woraufhin er in der Lage ist, den Katalog und alle Imagedefinitionen und -versionen in seiner Liste der Ressourcen anzuzeigen.

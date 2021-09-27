---
title: Erfassen von Daten von Event Hubs in Azure Data Lake Storage Gen1
description: Erfahren Sie, wie Sie Azure Data Lake Storage Gen1 zum Erfassen von Daten verwenden, die von Azure Event Hubs empfangen werden. Beginnen Sie mit dem Überprüfen der Voraussetzungen.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 61677d61ac028f6ae6ce64db665fdc8398d63fca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656966"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Verwenden von Azure Data Lake Storage Gen1 zum Erfassen von Daten von Event Hubs

Erfahren Sie, wie Sie Azure Data Lake Storage Gen1 zum Erfassen von Daten verwenden, die von Azure Event Hubs empfangen werden.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Ein Event Hubs-Namespace**. Anweisungen hierzu finden Sie unter [Erstellen eines Event Hubs-Namespaces](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Stellen Sie sicher, dass das Data Lake Storage Gen1-Konto und der Event Hubs-Namespace im selben Azure-Abonnement enthalten sind.


## <a name="assign-permissions-to-event-hubs"></a>Zuweisen von Berechtigungen für Event Hubs

In diesem Abschnitt erstellen Sie einen Ordner in dem Konto, in dem Sie die Daten von Event Hubs erfassen möchten. Zudem weisen Sie Event Hubs Berechtigungen zu, damit der Dienst Daten in ein Data Lake Storage Gen1-Konto schreiben kann. 

1. Öffnen Sie das Data Lake Storage Gen1-Konto, in dem Sie Daten von Event Hubs erfassen möchten, und klicken Sie dann auf **Daten-Explorer**.

    ![Data Lake Storage Gen1-Daten-Explorer](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1-Daten-Explorer")

1.  Klicken Sie auf **Neuer Ordner**, und geben Sie dann einen Namen für den Ordner ein, in dem Sie die Daten erfassen möchten.

    ![Erstellen eines neuen Ordners in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Erstellen eines neuen Ordners in Data Lake Storage Gen1")

1. Weisen Sie auf der Data Lake Storage Gen1-Stammebene Berechtigungen zu. 

    a. Klicken Sie auf **Daten-Explorer**, wählen Sie den Stamm des Data Lake Storage Gen1-Kontos aus, und klicken Sie dann auf **Zugriff**.

    ![Screenshot: Daten-Explorer mit Hervorhebung des Stamms des Kontos und der Option „Zugriff“](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm")

    b. Klicken Sie unter **Zugriff** auf **Hinzufügen** und dann auf **Benutzer oder Gruppe auswählen**, und suchen Sie anschließend nach `Microsoft.EventHubs`. 

    ![Screenshot: Seite „Zugriff“ mit den hervorgehobenen Optionen „Hinzufügen“, „Benutzer oder Gruppe auswählen“ und „Microsoft.EventHubs“](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm")
    
    Klicken Sie auf **Auswählen**.

    c. Klicken Sie unter **Berechtigungen zuweisen** auf **Berechtigungen auswählen**. Legen Sie **Berechtigungen** auf **Ausführen** fest. Legen Sie **Add to** (Hinzufügen zu) auf **Diesen Ordner und alle untergeordneten Ordner** fest. Legen Sie **Add as** (Hinzufügen als) auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** fest.

    > [!IMPORTANT]
    > Wenn Sie eine neue Ordnerhierarchie für die Erfassung der von Azure Event Hubs empfangenen Daten erstellen, ist dies eine einfache Möglichkeit, den Zugriff auf den Zielordner sicherzustellen.  Das Hinzufügen von Berechtigungen zu allen untergeordneten Ordnern eines Ordners auf oberster Ebene mit vielen untergeordneten Dateien und Ordnern kann jedoch lange dauern.  Wenn Ihr Stammordner eine große Anzahl von Dateien und Ordnern enthält, kann es schneller sein, die Berechtigung **Ausführen** für `Microsoft.EventHubs` jedem Ordner im Pfad zu Ihrem endgültigen Zielordner einzeln hinzuzufügen. 

    ![Screenshot: Abschnitt „Berechtigungen zuweisen“ mit hervorgehobener Option „Berechtigungen auswählen“, daneben der Abschnitt „Berechtigungen auswählen“ mit den hervorgehobenen Optionen „Ausführen“, „Add to“ (Hinzufügen zu) und „Add as“ (Hinzufügen als)](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Stamm")

    Klicken Sie auf **OK**.

1. Weisen Sie Berechtigungen für den Ordner im Data Lake Storage Gen1-Konto zu, in dem Sie Daten erfassen möchten.

    a. Klicken Sie auf **Daten-Explorer**, wählen Sie den Ordner im Data Lake Storage Gen1-Konto aus, und klicken Sie dann auf **Zugriff**.

    ![Screenshot: Daten-Explorer mit Hervorhebung eines Ordners im Konto und der Option „Zugriff“](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner")

    b. Klicken Sie unter **Zugriff** auf **Hinzufügen** und dann auf **Benutzer oder Gruppe auswählen**, und suchen Sie anschließend nach `Microsoft.EventHubs`. 

    ![Screenshot: Seite „Zugriff“ im Daten-Explorer mit den hervorgehobenen Optionen „Hinzufügen“, „Benutzer oder Gruppe auswählen“ und „Microsoft.EventHubs“](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner")
    
    Klicken Sie auf **Auswählen**.

    c. Klicken Sie unter **Berechtigungen zuweisen** auf **Berechtigungen auswählen**. Legen Sie **Berechtigungen** auf **Read, Write** (Lesen, Schreiben) und **Ausführen** fest. Legen Sie **Add to** (Hinzufügen zu) auf **Diesen Ordner und alle untergeordneten Ordner** fest. Legen Sie abschließend **Add as** (Hinzufügen als) auf **Ein Zugriffsberechtigungseintrag und ein Standardberechtigungseintrag** fest.

    ![Screenshot: Abschnitt „Berechtigungen zuweisen“ mit hervorgehobener Option „Berechtigungen auswählen“, daneben der Abschnitt „Berechtigungen auswählen“ mit den hervorgehobenen Optionen „Lesen“, „Schreiben“, „Ausführen“ sowie „Add to“ (Hinzufügen zu) und „Add as“ (Hinzufügen als)](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Zuweisen von Berechtigungen für den Data Lake Storage Gen1-Ordner")
    
    Klicken Sie auf **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurieren von Event Hubs zum Erfassen von Daten in Data Lake Storage Gen1

In diesem Abschnitt erstellen Sie einen Event Hub innerhalb eines Event Hubs-Namespaces. Außerdem konfigurieren Sie den Event Hub zum Erfassen von Daten in einem Azure Data Lake Storage Gen1-Konto. In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen Event Hubs-Namespace erstellt haben.

1. Klicken Sie im Bereich **Übersicht** des Event Hubs-Namespaces auf **+ Event Hub**.

    ![Screenshot: Übersichtsbereich mit hervorgehobener Option „Event Hub“](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Erstellen des Event Hubs")

1. Geben Sie die folgenden Werte an, um Event Hubs zum Erfassen von Daten in Data Lake Storage Gen1 zu konfigurieren.

    ![Screenshot: Dialogfeld „Event Hub erstellen“, in dem das Textfeld „Name“ sowie die Optionen „Capture“, „Anbieter erfassen“, „Data Lake Store auswählen“ und „Data Lake-Pfad“ hervorgehoben sind](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Erstellen des Event Hubs")

    a. Geben Sie einen Namen für den Event Hub an.
    
    b. In diesem Tutorial legen Sie **Partitionsanzahl** und **Nachrichtenaufbewahrung** auf die Standardwerte fest.
    
    c. Legen Sie **Erfassen** auf **On** (Ein) fest. Legen Sie das **Zeitfenster** (Häufigkeit der Erfassung) und **Größenfenster** (zu erfassende Datengröße) fest. 
    
    d. Wählen Sie für **Capture Provider** (Erfassungsanbieter) die Option **Azure Data Lake Store** aus, und wählen Sie dann das zuvor erstellte Data Lake Storage Gen1-Konto aus. Geben Sie für **Data Lake Path** (Data Lake-Pfad) den Namen des Ordners ein, den Sie im Data Lake Storage Gen1-Konto erstellt haben. Sie müssen nur den relativen Pfad zum Ordner angeben.

    e. Übernehmen Sie für **Beispielnamensformate für Erfassungsdateien** den Standardwert. Diese Option steuert die Ordnerstruktur, die unter dem Erfassungsordner erstellt wird.

    f. Klicken Sie auf **Erstellen**.

## <a name="test-the-setup"></a>Testen der Einstellungen

Sie können die Lösung jetzt testen, indem Sie Daten an den Azure Event Hub senden. Folgen Sie den Anweisungen unter [Senden von Ereignissen an Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Sobald Sie mit dem Senden der Daten beginnen, werden die Daten in der von Ihnen angegebenen Ordnerstruktur in Data Lake Storage Gen1 angezeigt. Beispielsweise sehen Sie wie im folgenden Screenshot gezeigt eine Ordnerstruktur in Ihrem Data Lake Storage Gen1-Konto.

![Beispiel für EventHub-Daten in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Beispiel für EventHub-Daten in Data Lake Storage Gen1")

> [!NOTE]
> Auch wenn keine Nachrichten in Event Hubs eingehen, schreibt Event Hubs leere Dateien, die nur die Header enthalten, in das Data Lake Storage Gen1-Konto. Die Dateien werden in dem Zeitintervall geschrieben, das Sie beim Erstellen des Event Hubs angegeben haben.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analysieren von Daten in Data Lake Storage Gen1

Sobald die Daten sich in Data Lake Storage Gen1 befinden, können Sie Analyseaufträge zum Verarbeiten und Berechnen der Daten ausführen. Informationen zum Ausführen solcher Aufträge mit Azure Data Lake Analytics finden Sie unter [U-SQL Avro Example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (U-SQL-Avro-Beispiel).
  

## <a name="see-also"></a>Weitere Informationen
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

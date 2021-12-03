---
title: Behandeln von Problemen mit freigegebenen Images in Azure
description: In diesem Abschnitt erfahren Sie, wie Sie mit Problemen mit freigegebenen Images in Azure Compute Galleries verfahren.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 7/1/2021
ms.openlocfilehash: a838879249bad2edfb8d5105e34cd7697a1f59e2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437085"
---
# <a name="troubleshoot-images-in-an-azure-compute-gallery"></a>Problembehandlung bei Images in der Azure Compute Gallery

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn Sie Probleme beim Ausführen von Vorgängen für Ressourcen bei Azure Compute Gallery (ehemals Shared Image Gallery), z. B. Galerien, Imagedefinitionen und Imageversionen, haben, führen Sie den fehlerhaften Befehl erneut im Debug-Modus aus. Sie aktivieren den Debugmodus, indem Sie die `--debug`-Option mit der Azure CLI oder die `-Debug`-Option mit PowerShell übergeben. Nachdem Sie den Fehler gefunden haben, befolgen Sie diesen Artikel, um das Problem zu beheben.


## <a name="creating-or-modifying-a-gallery"></a>Erstellen oder Ändern eines Katalogs ##

**Meldung:** *Gallery name is invalid. (Der Katalogname ist ungültig.) Zulässig sind englische alphanumerische Zeichen, bei denen in der Mitte Unterstriche und Punkte erlaubt sind, mit bis zu 80 Zeichen. Alle anderen Sonderzeichen, einschließlich Bindestriche, sind unzulässig.*  
**Ursache:** Der Name für den Katalog entspricht nicht den Benennungsanforderungen.  
**Problemumgehung**: Wählen Sie einen Namen, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung**: *Der angegebene Ressourcenname\<galleryName\> enthält diese ungültigen Zeichen am Ende:\<character\> Der Name darf nicht mit Zeichen enden: \<character\>*  
**Ursache:** Der Katalogname endet mit einem Punkt oder Unterstrich.  
**Problemumgehung**: Wählen Sie einen Namen für den Katalog, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung**: *Der angegebene Speicherort\<region\> ist für den Ressourcentyp 'Microsoft.Compute/galleries' nicht verfügbar. Die Liste der verfügbaren Bereiche für den Ressourcentyp ist ...*  
**Ursache:** Die für den Katalog angegebene Region ist falsch oder erfordert eine Zugriffsanforderung.  
**Problemumgehung**: Vergewissern Sie sich, dass der Regionsname richtig ist. Wenn der Regionsname richtig ist, übermitteln Sie [eine Zugriffsanforderung](/troubleshoot/azure/general/region-access-request-process) für die Region.

**Meldung:** *Ressource kann nicht gelöscht werden, bevor verschachtelte Ressourcen gelöscht wurden.*  
**Ursache:** Sie haben versucht, einen Katalog zu löschen, der mindestens eine vorhandene Imagedefinition enthält. Ein Katalog muss leer sein, bevor er gelöscht werden kann.  
**Problemumgehung**: Löschen Sie alle Imagedefinitionen innerhalb des Katalogs, und fahren Sie dann mit dem Löschen des Katalogs fort. Wenn die Imagedefinition Imageversionen enthält, müssen Sie die Imageversionen löschen, bevor Sie die Imagedefinitionen löschen.

**Meldung**: *Der Katalogname\<galleryName\> ist innerhalb des Abonnements nicht eindeutig\<subscriptionID\> Bitte wählen Sie einen anderen Katalognamen.*  
**Ursache:** Es ist bereits ein Katalog mit demselben Namen vorhanden, und Sie haben versucht, einen weiteren Katalog mit diesem Namen zu erstellen.  
**Problemumgehung:** Wählen Sie einen anderen Namen für den Katalog aus.

**Meldung**: *Die Ressource\<galleryName\> existiert bereits am Ort\<region\_1\> in der Ressourcengruppe\<resourceGroup\>. Es kann keine Ressource mit demselben Namen am Ort erstellt werden\<region\_2\>. Bitte wählen Sie einen neuen Ressourcennamen.*  
**Ursache:** Es ist bereits ein Katalog mit demselben Namen vorhanden, und Sie haben versucht, einen weiteren Katalog mit diesem Namen zu erstellen.  
**Problemumgehung:** Wählen Sie einen anderen Namen für den Katalog aus.

## <a name="creating-or-modifying-image-definitions"></a>Erstellen oder Ändern von Imagedefinitionen ##

**Meldung**: *Ändern von Eigenschaft von 'gallerylmage.properties.\<property\> ist nicht erlaubt.*  
**Ursache:** Sie haben versucht, den Betriebssystemtyp, den Zustand des Betriebssystems, die Hyper-V-Generation, das Angebot, den Herausgeber oder die SKU zu ändern. Das Ändern dieser Eigenschaften ist nicht zulässig.  
**Problemumgehung**: Erstellen Sie stattdessen eine neue Imagedefinition.

**Meldung**: *Die Ressource\<galleryName/imageDefinitionName\> existiert bereits am Ort\<region\_1\> in der Ressourcengruppe\<resourceGroup\>. Es kann keine Ressource mit demselben Namen am Ort erstellt werden\<region\_2\>. Bitte wählen Sie einen neuen Ressourcennamen.*  
**Ursache:** Sie verfügen über eine vorhandene Imagedefinition im gleichen Katalog und derselben Ressourcengruppe mit dem gleichen Namen. Sie haben versucht, eine weitere Imagedefinition mit demselben Namen im gleichen Katalog, aber in einer anderen Region zu erstellen.  
**Problemumgehung**: Wählen Sie einen anderen Namen für die Imagedefinition aus, oder legen Sie die Imagedefinition in einem anderen Katalog oder einer anderen Ressourcengruppe ab.

**Meldung**: *Der angegebene Ressourcenname\<galleryName\> /\<imageDefinitionName\>enthält diese ungültigen Zeichen am Ende:\<character\> Der Name darf nicht mit Zeichen enden: \<character\>*  
**Ursache**: Der \<imageDefinitionName\> Name endet mit einem Punkt oder Unterstrich.  
**Problemumgehung**: Wählen Sie einen Namen für die Imagedefinition aus, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche, Bindestriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung**: *Der Entitätsname \<imageDefinitionName\> ist nach der Prüfungsregel ungültig: ^[^\_\\W][\\w-.\_]{0,79}(?<![-.])$"*  
**Ursache**: Der \<imageDefinitionName\> Name endet mit einem Punkt oder Unterstrich.  
**Problemumgehung**: Wählen Sie einen Namen für die Imagedefinition aus, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche, Bindestriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung**: *Der Ressourcename galleryImage.properties.identifier.\<property\> ist ungültig. Er darf nicht leer sein. Erlaubte Zeichen sind Groß- und Kleinbuchstaben, Ziffern, Bindestrich (-), Punkt (.), Unterstrich (\_). Namen dürfen nicht mit einem Punkt (.) enden. Die Länge des Namens darf nicht mehr als \<number\> Zeichen betragen.*  
**Ursache:** Der Herausgeber, das Angebot oder der SKU-Wert entspricht nicht den Benennungsanforderungen.  
**Problemumgehung**: Wählen Sie einen Wert, der die folgenden Bedingungen erfüllt: 
- Für den Herausgeber besteht ein Limit von 128 Zeichen und für das Angebot und die SKU besteht ein Limit von 64 Zeichen.
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Bindestriche, Unterstriche und Punkte
- Endet nicht mit einem Punkt

**Meldung**: *Angeforderter Vorgang kann auf der verschachtelten Ressource nicht ausgeführt werden. Übergeordnete Ressource \<galleryName\> wird nicht gefunden.*  
**Ursache**: Es gibt keinen Katalog mit dem Namen \<galleryName\> im aktuellen Abonnement und in der aktuellen Ressourcengruppe.  
**Problemumgehung**: Überprüfen Sie, ob die Namen des Katalogs, des Abonnements und der Ressourcengruppe richtig sind. Andernfalls erstellen Sie einen neuen Katalog mit dem Namen \<galleryName\>.

**Meldung**: *Der angegebene Speicherort\<region\> ist für den Ressourcentyp 'Microsoft.Compute/galleries' nicht verfügbar. Die Liste der verfügbaren Bereiche für den Ressourcentyp ist ...*  
**Ursache**: Der Name \<region\> ist falsch oder erfordert eine Zugriffsanforderung.  
**Problemumgehung**: Prüfen Sie, ob der Regionsname richtig geschrieben ist. Sie können diesen Befehl ausführen, um die Regionen anzuzeigen, auf die Sie Zugriff haben. Wenn die Region nicht in der Liste enthalten ist, senden Sie [eine Zugriffsanforderung](/troubleshoot/azure/general/region-access-request-process).

**Meldung**: *Serialisieren des Werts nicht möglich\<value\> als Typ: 'iso-8601., ISO8601 Fehler: ISO 8601 Zeitbezeichner „T“ fehlt. Analyse der datetime Zeichenfolge nicht möglich\<value\>*  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung**: *Zeichenfolge konnte nicht zu einem datetime Abstand konvertiert werden: \<value\>. Pfad-‘Eigenschaften.\<property\>'*  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung:** *Das Lebensendedatum muss auf ein zukünftiges Datum festgelegt werden.*  
**Ursache:** Die EndOfLifeDate-Eigenschaft ist nicht ordnungsgemäß als ein Datum formatiert, das auf das heutige Datum folgt.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung**: *Argument --\<property\>: ungültiger Ganzzahlenwert: \<value\>*  
**Ursache**: \<property\>nimmt nur Ganzzahlenwerte an, und \<value\> ist keine ganze Zahl.  
**Problemumgehung**: Wählen Sie als Wert eine ganze Zahl.

**Meldung**: *Der kleinste Wert von \<property\> darf nicht größer sein als der größte Wert von\<property\>.*  
**Ursache**: Der kleinste für \<property\> angegebene Wert ist größer als der größte für \<property\> angegebene Wert.  
**Problemumgehung**: Ändern Sie die Werte so, dass der Mindestwert kleiner oder gleich dem Höchstwert ist.

**Meldung**: *Katalog Image: \<imageDefinitionName\> identifiziert von (Verleger:\<Publisher\>, Angebot:\<Offer\>, SKU:\<SKU\>) gibt es schon. WÄhlen Sie eine andere Verleger-Angebot-SKU-Kombination.*  
**Ursache:** Sie haben versucht, eine neue Imagedefinition mit derselben Kombination aus Herausgeber, Angebot und SKU wie bei einer bestehenden Imagedefinition im selben Katalog zu erstellen.  
**Problemumgehung**: Innerhalb eines Katalogs müssen alle Imagedefinitionen eine eindeutige Kombination aus Herausgeber, Angebot und SKU aufweisen. Wählen Sie eine eindeutige Kombination oder einen neuen Katalog aus, und erstellen Sie die Imagedefinition noch mal.

**Meldung:** *Ressource kann nicht gelöscht werden, bevor verschachtelte Ressourcen gelöscht wurden.*  
**Ursache:** Sie haben versucht, eine Imagedefinition zu löschen, die Imageversionen enthält. Eine Imagedefinition muss leer sein, ehe sie gelöscht werden kann.  
**Problemumgehung**: Löschen Sie alle Imageversionen innerhalb der Imagedefinition, und fahren Sie dann mit dem Löschen der Imagedefinition fort.

**Meldung**: *Nicht möglich, Parameter zu binden \<property\>. Nicht möglich, Wert zu konvertieren \<value\> zu Typ \<propertyType\>. Nicht möglich, den Bezeichnernamen \<value\> an einen gültigen Enumeratornamen anzupassen. Spezifizieren Sie einen von den folgenden Enumeratornamen und versuchen Sie es noch mal: \<choice\_1\>, \<choice\_2\>, …*  
**Ursache**: Die Eigenschaft hat eine begrenzte Liste von möglichen Werten und \<value\> ist keine davon.  
**Problemumgehung**: Wählen Sie einen von den möglichen \<choice\> values.

**Meldung**: *Nicht möglich, Parameter zu binden \<property\>. Nicht möglich, den Wert zu konvertieren\<value\> zum Typ &quot;System.DateTime&quot;*  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung**: *Nicht möglich, Parameter zu binden\<property\>. Nicht möglich, Wert zu konvertieren \<value\> zum Typ &quot;System.Int32&quot;*  
**Ursache**: \<property\>nimmt nur Ganzzahlenwerte an, und \<value\> ist keine ganze Zahl.  
**Problemumgehung**: Wählen Sie als Wert eine ganze Zahl.

**Meldung:** *Der Speicherkontotyp ZRS wird in dieser Region nicht unterstützt.*  
**Ursache:** Sie haben sich für den standardmäßigen zonenredundanten Speicher (ZRS) in einer Region entschieden, die diesen noch nicht unterstützt.  
**Problemumgehung**: Ändern Sie den Speicherkontotyp in **Premium\_LRS** oder **Standard\_LRS**. In unserer Dokumentation finden Sie die aktuelle [Liste der Regionen](../storage/common/storage-redundancy.md#zone-redundant-storage) mit aktivierter ZRS-Vorschau.

## <a name="creating-or-updating-image-versions"></a>Erstellen oder Aktualisieren von Imageversionen ##

**Meldung**: *Der angegebene Speicherort\<region\> ist für den Ressourcentyp 'Microsoft.Compute/galleries' nicht verfügbar. Die Liste der verfügbaren Bereiche für den Ressourcentyp ist ...*  
**Ursache**: Der Name \<region\> ist falsch oder erfordert eine Zugriffsanforderung.  
**Problemumgehung**: Prüfen Sie, ob der Regionsname richtig geschrieben ist. Sie können diesen Befehl ausführen, um die Regionen anzuzeigen, auf die Sie Zugriff haben. Wenn die Region nicht in der Liste enthalten ist, senden Sie [eine Zugriffsanforderung](/troubleshoot/azure/general/region-access-request-process).

**Meldung**: *Nicht möglich, den angeforderten Vorgang auf einer geschachtelten Ressource auszuführen. Übergeordnete Ressource \<galleryName/imageDefinitionName\> wird nicht gefunden.*  
**Ursache**: Es gibt keinen Katalog mit dem Namen \<galleryName/imageDefinitionName\> im aktuellen Abonnement und in der aktuellen Ressourcengruppe.  
**Problemumgehung**: Überprüfen Sie, ob die Namen des Katalogs, des Abonnements und der Ressourcengruppe richtig sind. Andernfalls erstellen Sie einen neuen Katalog mit dem Namen \<galleryName\> und/oder einer Bilddefinition, die in der angegebenen Ressourcengruppe benannt\<imageDefinitionName\> ist.

**Meldung**: *Nicht möglich, Parameter zu binden \<property\>. Nicht möglich, den Wert zu konvertieren\<value\> zum Typ &quot;System.DateTime&quot;*  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung**: *Nicht möglich, Parameter zu binden\<property\>. Nicht möglich, Wert zu konvertieren \<value\> zum Typ &quot;System.Int32&quot;*  
**Ursache**: \<property\>nimmt nur Ganzzahlenwerte an, und \<value\> ist keine ganze Zahl.  
**Problemumgehung**: Wählen Sie als Wert eine ganze Zahl.

**Meldung**: *Katalog Bildversion Veröffentlichungsprofil Regionen \<publishingRegions\> müssen den Ort der Bildversion enthalten\<sourceRegion\>*  
**Ursache**: Der Ort des Quellbildes (\<sourceRegion\>) muss in der Liste \<publishingRegions\> enthalten sein.  
**Problemumgehung**: Nehmen Sie \<sourceRegion\> in die Liste \<publishingRegions\> auf.

**Meldung**: *Der Wert \<value\> des Parameters \<property\> liegt außerhalb des zulässigen Bereichs. Der Wert muss zwischen \<minValue\> und \<maxValue\>: einschließlich*  liegen.  
**Ursache**: \<value\> liegt außerhalb des Bereichs der möglichen Werte für \<property\>.  
**Problemumgehung**: Wählen Sie einen Wert, der innerhalb des Bereichs von \<minValue\> und \<maxValue\> liegt.

**Meldung**: *Quelle \<resourceID\> wird nicht gefunden. Überprüfen Sie, ob die Quelle vorhanden ist und sich in derselben Region befindet wie die zu erstellende Katalogbildversion.*  
**Ursache**: Es gibt keine Quelle bei \<resourceID\>, oder die Quelle bei \<resourceID\> befindet sich nicht im selben Bereich wie das zu erstellende Katalogbild.  
**Problemumgehung**: Überprüfen Sie, ob der Wert \<resourceID\> richtig ist und ob der Quellbereich der Katalogbildversion mit dem Bereich des Wertes \<resourceID\> übereinstimmt.

**Meldung**: *Ändern der Eigenschaft von 'galleryImageVersion.properties.storageProfile.\<diskImage\>.source.id' ist nicht erlaubt.*  
**Ursache:** Die Quell-ID einer Katalogimageversion kann nach der Erstellung nicht mehr geändert werden.  
**Problemumgehung**: Stellen Sie sicher, dass die Quell-ID mit der vorhandenen Quell-ID übereinstimmt, ändern Sie die Versionsnummer der Imageversion, oder löschen Sie die aktuelle Imageversion, und versuchen Sie es dann noch einmal.

**Meldung:** *Duplicated lun numbers have been detected in the input data disks. (Auf den Eingabedatenträgern wurden duplizierte LUN-Nummern erkannt.) Die LUN-Nummer muss für jeden Datenträger eindeutig sein.*  
**Ursache:** Wenn Sie eine Imageversion mithilfe einer Liste von Datenträgern und/oder Datenträgermomentaufnahmen erstellen, weisen zwei oder mehr Datenträger oder Datenträgermomentaufnahmen dieselbe logische Gerätenummer (LUN) auf.  
**Problemumgehung**: Entfernen oder ändern Sie doppelte LUNs.

**Meldung:** *Duplicated source ids are found in the input disks. (Auf den Eingabedatenträgern wurden duplizierte Quell-IDs gefunden.) Die Quell-ID muss für jeden Datenträger eindeutig sein.*  
**Ursache:** Wenn Sie eine Imageversion mithilfe einer Liste von Datenträgern und/oder Datenträgermomentaufnahmen erstellen, weisen zwei oder mehr Datenträger oder Datenträgermomentaufnahmen dieselbe Ressourcen-ID auf.  
**Problemumgehung**: Entfernen oder ändern Sie doppelte Datenträgerquell-IDs.

**Meldung**: *Die Eigenschafts-ID \<resourceID\> im Pfad 'properties.storageProfile.\<diskImages\>source.id' ist ungültig. Es wird eine voll qualifizierte Ressourcenkennung erwartet, die beginnt mit '/subscriptions/ \<subscriptionID>' oder '/providers/\<resourceProviderNamespace>/'.*  
**Ursache**: Der Wert \<resourceID\> ist falsch formatiert.  
**Problemumgehung**: Überprüfen Sie, ob die Ressourcen-ID stimmt.

**Meldung**: *Die Quellen-ID: \<resourceID\> muss entweder ein verwaltetes Bild, ein virtueller Computer oder eine andere Katalogbildversion sein*  
**Ursache**: Der Wert \<resourceID\> ist falsch formatiert.  
**Problemumgehung**: Wenn Sie eine VM, ein verwaltetes Image oder eine Katalogimageversion als Quellimage verwenden, überprüfen Sie, ob die Ressourcen-ID der VM, des verwalteten Images oder der Katalogimageversion stimmt.

**Meldung**: *Die Quell-ID: \<resourceID\> muss ein verwalteter Datenträger oder eine Momentaufnahme sein.*  
**Ursache**: Der Wert \<resourceID\> ist falsch formatiert.  
**Problemumgehung**: Wenn Sie Datenträger und/oder Momentaufnahmen von Datenträgern als Quellen für die Imageversion verwenden, überprüfen Sie, ob die Ressourcen-IDs der Datenträger und/oder Momentaufnahmen von Datenträgern stimmen.

**Meldung**: *Katalogimageversion kann nicht erstellt werden von: \<resourceID\>, da der Betriebssystemstatus im übergeordneten Katalog-Image (\<OsState\_1\>) kein \<OsState\_2\> ist.*  
**Ursache:** Der Betriebssystemstatus („Generalisiert“ oder „Spezialisiert“) stimmt nicht mit dem in der Imagedefinition angegebenen Betriebssystemstatus identisch.  
**Problemumgehung**: Wählen Sie entweder eine Quelle auf der Grundlage einer VM mit dem Betriebssystemstatus von \<OsState\_1\> oder erstellen Sie eine neue Bilddefinition für VMs auf der Grundlage von \<OsState\_2\>.

**Meldung**: *Die Ressource mit der ID '\<resourceID\>' hat eine andere Hypervisor-Generation ['\<V#\_1\>'] als das übergeordnete Katalog-Image Hypervisor-Generation [' \<V#\_2\>']*  
**Ursache:** Die Hypervisor-Generation der Imageversion stimmt nicht mit der in der Imagedefinition angegebenen Hypervisor-Generation überein. Das Betriebssystem der Bilddefinition ist \<V#\_1\>, das Betriebssystem der Bildversion ist \<V#\_2\>.  
**Problemumgehung**: Wählen Sie entweder eine Quelle mit der gleichen Hypervisor-Generation wie in der Imagedefinition aus, oder erstellen/wählen Sie eine neue Imagedefinition, die die gleiche Hypervisor-Generation wie die Imageversion aufweist.

**Meldung**: *Die Ressource mit der ID '\<resourceID\>' hat einen anderen OS-Typ [' \<OsType\_1\>'] als das übergeordnete Katalog-Bild OS-Typ Generation [' \<OsType \_2\>']*  
**Ursache:** Die Hypervisor-Generation der Imageversion stimmt nicht mit der in der Imagedefinition angegebenen Hypervisor-Generation überein. Das Betriebssystem der Bilddefinition ist \<OsType\_1\> und das Betriebssystem der Bildversion ist \<OsType\_2\>.  
**Problemumgehung**: Wählen Sie entweder eine Quelle mit dem gleichen Betriebssystem (Linux/Windows) wie in der Imagedefinition, oder erstellen/wählen Sie eine neue Imagedefinition, die die gleiche Betriebssystemgeneration wie die Imageversion aufweist.

**Meldung**: *Der virtuelle Computer \<resourceID\> kann keinen kurzlebigen Betriebssystemdatenträger enthalten.*  
**Ursache**: Die Quelle bei \<resourceID\> enthält einen kurzlebigen Betriebssystemdatenträger. Die Azure Compute Gallery unterstützt derzeit keine kurzlebigen Betriebssystemdatenträger.  
**Problemumgehung**: Wählen Sie eine andere Quelle basierend auf einer VM ohne kurzlebigen Betriebssystemdatenträger.

**Meldung**: *Der Virtueller Ausgangscomputer \<resourceID\> kann keine Festplatte ['\<diskID\>'] enthalten, die in einem UltraSSD-Kontotyp gespeichert ist.*  
Disk Storage Ultra **Ursache**: Der Datenträger \<diskID\> ist ein Ultra SSD-Datenträger. Die Azure Compute Gallery unterstützt derzeit keine Ultra SSD-Datenträger.  
**Problemumgehung**: Verwenden Sie eine Quelle, die nur verwaltete Datenträger des Typs SSD Premium, SSD Standard und/oder HDD Standard enthält.

**Meldung**: *Der Virtueller Ausgangscomputer \<resourceID\> muss aus verwalteten Datenträgern erstellt werden.*  
**Ursache**: Der Virtuelle Computer in \<resourceID\> verwendet nicht verwaltete Datenträger.  
**Problemumgehung**: Verwenden Sie eine auf einer VM basierende Quelle, die nur verwaltete Datenträger des Typs SSD Premium, SSD Standard und/oder HDD Standard enthält.

**Meldung**: *Zu viele Anforderungen an die Quelle '\<resourceID\>'. Bitte reduzieren Sie die Anzahl der Anforderungen an die Quelle oder warten Sie einige Zeit, bevor Sie es erneut versuchen.*  
**Ursache:** Die Quelle dieser Imageversion wird derzeit aufgrund zu vieler Anforderungen gedrosselt.  
**Problemumgehung**: Versuchen Sie später, die Imageversion zu erstellen.

**Meldung**: *Das Festplattenverschlüsselungsset '\<diskEncryptionSetID\>' muss sich in demselben Abonnement '\<subscriptionID\>' wie die Katalogressource*  befinden.  
**Ursache:** Datenträgerverschlüsselungssätze können nur im selben Abonnement und in derselben Region verwendet werden, in der sie erstellt wurden.  
**Problemumgehung**: Erstellen oder verwenden Sie einen Verschlüsselungssatz, der im gleichen Abonnement und in der gleichen Region wie die Imageversion enthalten ist.

**Meldung**: *Die verschlüsselte Quelle: ‚\<resourceID\>'hat eine andere Abonnement-ID als das aktuelle Abonnement für die Katalogversion ‚\<subscriptionID\_1\>'. Bitte versuchen Sie es erneut mit einer oder mehreren unverschlüsselten Quelle(n) oder verwenden Sie das Abonnement der Quelle ‚\<subcriptionID\_2\>', um die Katalogversion zu erstellen.*  
**Ursache**: Die Azure Compute Gallery unterstützt derzeit nicht die Erstellung von Image-Versionen in einem anderen Abonnement aus einem anderen Quell-Image, wenn das Quell-Image verschlüsselt ist.  
**Problemumgehung**: Verwenden Sie eine unverschlüsselte Quelle, oder erstellen Sie die Imageversion im selben Abonnement wie die Quelle.

**Meldung**: *Datenträgerverschlüsselungssatz \<diskEncryptionSetID\> wird nicht gefunden.*  
**Ursache:** Die Datenträgerverschlüsselung ist möglicherweise falsch.  
**Problemumgehung**: Prüfen Sie, ob die Ressourcen-ID des Datenträgerverschlüsselungssatzes stimmt.

**Meldung:** *The image version name is invalid. (Der Name der Imageversion ist ungültig.) Der Name der Imageversion muss das Format Hauptversion(ganze Zahl).Nebenversion(ganze Zahl).Patch(ganze Zahl) aufweisen. Beispiel:1.0.0, 2018.12.1 usw.*  
**Ursache:** Das gültige Format einer Imageversion umfasst drei durch einen Punkt getrennte ganze Zahlen. Der Name der Imageversion entsprach nicht dem gültigen Format.  
**Problemumgehung**: Verwenden Sie einen Imageversionsnamen mit dem Format Hauptversion(ganze Zahl).Nebenversion(ganze Zahl).Patch(ganze Zahl). Beispiel: 1.0.0. oder 2018.12.1.

**Message**: *Der Wert von Parameter galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.dataDiskImages.diskEncryptionSetId ist ungültig.*  
**Ursache:** Die Ressourcen-ID des Datenträgerverschlüsselungssatzes eines Datenträgerimages hat ein ungültiges Format.  
**Problemumgehung**: Stellen Sie sicher, dass die Ressourcenkennung des Festplattenverschlüsselungssatzes das folgende Format hat: /subscriptions/\<subscriptionID\>/resourceGroups/\<resourceGroupName\>/providers/Microsoft.Compute/\<diskEncryptionSetName\>.

**Message**: *Der Wert von Parameter galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId ist ungültig.*  
**Ursache:** Die Ressourcen-ID des Datenträgerverschlüsselungssatzes für das Image des Betriebssystemdatenträgers weist ein ungültiges Format auf.  
**Problemumgehung**: Stellen Sie sicher, dass die Ressourcenkennung des Festplattenverschlüsselungssatzes das folgende Format hat: /subscriptions/\<subscriptionID\>/resourceGroups/\<resourceGroupName\>/providers/Microsoft.Compute/\<diskEncryptionSetName\>.

**Meldung**: *Es ist nicht möglich, für die Anforderung zur Aktualisierung der Version des Katalogs eine neue Datenverschlüsselungs-LUN [\<number\>] mit einer Festplattenverschlüsselung in Region [\<region\>] anzugeben. Um diese Version zu aktualisieren, entfernen Sie die neue LUN. Wenn Sie die Einstellungen für die Verschlüsselung des Datenträgers ändern müssen, müssen Sie eine neue Katalogversion mit den richtigen Einstellungen erstellen.*  
**Ursache:** Sie haben die Verschlüsselung zum Datenträger einer vorhandenen Imageversion hinzugefügt. Sie können die Verschlüsselung nicht zu einer vorhandenen Imageversion hinzufügen.  
**Problemumgehung**: Erstellen Sie eine neue Katalogimageversion, oder entfernen Sie die hinzugefügten Verschlüsselungseinstellungen.

**Meldung:** *The gallery artifact version source can only be specified either directly under storageProfile or within individual OS or data disks. (Die Quelle der Katalogartefaktversion kann nur entweder direkt unter storageProfile oder innerhalb einzelner Betriebssystem- oder sonstiger Datenträger angegeben werden.) Es kann nur ein einziger Quelltyp (Benutzerimage, Momentaufnahme, Datenträger, virtueller Computer) bereitgestellt werden.*  
**Ursache:** Die Quell-ID fehlt.  
**Problemumgehung**: Stellen Sie sicher, dass die Quell-ID der Quelle vorhanden ist.

**Meldung**: *Quelle wird nicht gefunden: \<resourceID\>. Stellen Sie sicher, dass es die Quelle gibt.*  
**Ursache:** Die Ressourcen-ID der Quelle ist möglicherweise falsch.  
**Problemumgehung**: Stellen Sie sicher, dass die Ressourcen-ID der Quelle stimmt.

**Meldung**: *Es ist ein Festplattenverschlüsselungssatz für die Festplatte 'galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId' in der Zielregion ‚\<region\_1\>' ' ' erforderlich, da der Festplattenverschlüsselungssatz ‚\<diskEncryptionSetID\>' für die korrespondierende Festplatte in der Region verwendet wird '\<region\_2\>'*  
**Ursache**: Verschlüsselung wurde auf der Betriebssystemfestplatte in \<region\_2\> benutzt, aber nicht bei \<region\_1\>.  
**Problemumgehung**: Wenn Sie Verschlüsselung auf dem Betriebssystemdatenträger verwenden, müssen Sie sie in allen Regionen verwenden.

**Meldung**: *Es ist ein Datenträgerverschlüsselungssatz erforderlich für die Festplatte 'LUN \<number\>' in der Zielregion '\<region\_1\>', da Datenträgerverschlüsselungssatz '\<diskEncryptionSetID\>' benutzt wird für die korrespondierende Festplatte in der Region '\<region\_2\>'*  
**Ursache**: Verschlüsselung wurde auf der Datenfestplatte in LUN \<number\> in \<region\_2\>, aber nicht in \<region\_1\>.  
**Problemumgehung**: Wenn Sie Verschlüsselung auf einem Datenträger verwenden, müssen Sie sie in allen Regionen verwenden.

**Meldung**: *Eine gültige LUN [\<number\>] wurde spezifiziert in encryption.dataDiskImages. Die LUN muss eine der folgenden Werte sein ['0,9'].*  
**Ursache:** Die für die Verschlüsselung angegebene LUN stimmt mit keiner der LUNs für an die VM angefügte Datenträger überein.  
**Problemumgehung**: Ändern Sie die LUN in der Verschlüsselung in die eines Datenträgers, der auf der VM vorhanden ist.

**Meldung**: *In der Zielregion ‚\<number\>' wurden doppelte LUNs angegeben '\<region\>' encryption.dataDisklmages.*  
**Ursache**: Die in \<region\> verwendeten Verschlüsselungseinstellungen gaben eine LUN wenigstens zweimal an.  
**Problemumgehung**: Ändern Sie die LUN in \<region\>, um sicherzustellen, dass alle LUNs in \<region\> eindeutig sind.

**Meldung**: *Disklmage und DataDisklmage dürfen nicht auf denselben Blob verweisen \<sourceID\>*  
**Ursache:** Die Quellen des Betriebssystemdatenträgers und mindestens eines anderen Datenträgers sind nicht eindeutig.  
**Problemumgehung**: Ändern Sie die Quelle des Betriebssystemdatenträgers und/oder der sonstigen Datenträger, um sicherzustellen, dass der Betriebssystemdatenträger sowie der jeweilige sonstige Datenträger eindeutig sind.

**Meldung:** *Duplicate regions are not allowed in target publishing regions.* (Duplizierte Regionen sind in Zielveröffentlichungsregionen nicht zulässig.)  
**Ursache:** Eine Region ist in den Veröffentlichungsregionen mehrmals aufgeführt.  
**Problemumgehung**: Entfernen Sie die duplizierte Region.

**Meldung:** *Adding new Data Disks or changing the LUN of a Data Disk in an existing Image is not allowed.* (Das Hinzufügen neuer Datenträger oder Ändern der LUN eines Datenträgers in einem vorhandenen Image ist nicht zulässig.)  
**Ursache:** Ein Updateaufruf für die Imageversion enthält entweder einen neuen Datenträger oder eine neue LUN für einen Datenträger.  
**Problemumgehung**: Verwenden Sie die LUNs und Datenträger der vorhandenen Imageversion.

**Meldung**: *Das Festplattenverschlüsselungsset '\<diskEncryptionSetID\>' muss sich in demselben Abonnement '\<subscriptionID\>' wie die Katalogressource*  befinden.  
**Ursache**: Die Azure Compute Gallery unterstützt derzeit nicht die Verwendung eines Datenträgerverschlüsselungssatzes in einem anderen Abonnement.  
**Problemumgehung**: Erstellen Sie die Imageversion und Datenträgerverschlüsselung im selben Abonnement.

**Meldung:** *Replication failed in this region due to 'The GalleryImageVersion source resource size 2048 exceeds the max size 1024 supported.'* (Fehler bei der Replikation in dieser Region aufgrund des Fehlers "Die Größe 2048 der GalleryImageVersion-Quellressource überschreitet die maximal unterstützte Größe von 1024".)  
**Ursache:** Ein Datenträger in der Quelle ist größer als 1 TB.  
**Problemumgehung:** Legen Sie für den Datenträger eine neue Größe unter 1 TB fest.

**Meldung**: *Der Vorgang „Katalogimageversion aktualisieren“ ist für „\<versionNumber>“ unzulässig, weil dieses Element für den Löschvorgang markiert ist. Sie können den Löschvorgang noch mal versuchen (oder auf den Abschluss eines laufenden Vorgangs warten).*  
**Ursache**: Sie haben versucht, eine Katalogimageversion zu aktualisieren, die gerade gelöscht wird.  
**Problemumgehung**: Warten Sie, bis das Löschereignis abgeschlossen ist, und erstellen Sie die Imageversion erneut.

**Meldung**: *Die Verschlüsselung wird für die Quellressource „\<sourceID>“ nicht unterstützt. Verwenden Sie einen anderen Quellressourcentyp, der die Verschlüsselung unterstützt, oder entfernen Sie die Verschlüsselungseigenschaften.*  
**Ursache**: Derzeit unterstützt die Azure Compute Gallery nur die Verschlüsselung für virtuelle Computer, Datenträger, Momentaufnahmen und verwaltete Images. Eine der für die Imageversion bereitgestellten Quellen ist nicht in der obigen Liste der Quellen enthalten, die die Verschlüsselung unterstützen.  
**Problemumgehung**: Entfernen Sie den Datenträgerverschlüsselungssatz aus der Imageversion, und wenden Sie sich an das Supportteam.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Erstellen oder Aktualisieren einer VM oder Skalierungsgruppe anhand der Imageversion ##

**Meldung**: *Es gibt keine aktuelle Image-Version für "\<imageDefinitionResourceID\>"*  
**Ursache:** Die beim Bereitstellen des virtuellen Computers verwendete Imagedefinition enthält keine in der aktuellen Version vorhandenen Imageversionen.  
**Problemumgehung:** Stellen Sie sicher, dass bei mindestens einer Imageversion die Option „Aus aktueller Version ausschließen“ auf FALSE festgelegt ist. 

**Meldung**: *Das Katalogbild /subscriptions/\<subscriptionID\>/resourceGroups/\<resourceGroup\>/providers/Microsoft.Compute/galleries/\<galleryName\>/images/\<imageName\>/versions/\<versionNumber\> ist in Region \<region\> nicht verfügbar. Bitte wenden Sie sich an den Besitzer des Bildes, um es in diese Region zu vervielfältigen, oder ändern Sie die angeforderte Region.*  
**Ursache:** Die für die Bereitstellung ausgewählte Version ist nicht vorhanden oder verfügt über kein Replikat in der angegebenen Region.  
**Problemumgehung:** Stellen Sie sicher, dass der Name der Imageressource korrekt ist und mindestens ein Replikat in der angegebenen Region vorhanden ist. 

**Meldung**: *Das Katalogbild /subscriptions/\<subscriptionID\>/resourceGroups/\<resourceGroup\>/providers/Microsoft.Compute/galleries/\<galleryName\>/images/\<imageName\> ist in Region \<region\> nicht verfügbar. Bitte wenden Sie sich an den Besitzer des Bildes, um es in diese Region zu replizieren, oder ändern Sie die angeforderte Region.*  
**Ursache:** Die für die Bereitstellung ausgewählte Imagedefinition verfügt über keine Imageversionen, die in der aktuellen Version enthalten und auch in der angegebenen Region vorhanden sind.  
**Problemumgehung:** Stellen Sie sicher, dass in der Region mindestens eine Imageversion vorhanden ist, bei der „Aus aktueller Version ausschließen“ auf „False“ festgelegt ist. 

**Meldung**: *Der Mandant hat die Berechtigung, die Aktion 'Microsoft.Compute/galleries/images/versions/read' für den Bereich \<resourceID\> auszuführen, jedoch ist der aktuelle Mandant \<tenantID\> nicht berechtigt, auf das verknüpfte Abonnement \<subscriptionID\> zuzugreifen.*  
**Ursache**: Der virtuelle Computer oder die Skalierungsgruppe wurde mit einem Galerie-Image bei einem anderen Mandanten erstellt. Sie haben versucht, eine Änderung an der VM oder Skalierungsgruppe vorzunehmen, aber Sie verfügen nicht über den Zugriff auf das Abonnement, zu dem das Image gehört.  
**Problemumgehung**: Bitten Sie den Besitzer des Abonnements der Imageversion, Ihnen Lesezugriff auf die Imageversion zu gewähren.

**Meldung**: *Das Katalogbild \<resourceID\> ist nicht verfügbar in Region \<region\> Bitte wenden Sie sich an den Besitzer des Bildes, um es in diese Region zu replizieren, oder ändern Sie die angeforderte Region.*  
**Ursache:** Die VM wird in einer Region erstellt, die nicht in der Liste der veröffentlichten Regionen für das Katalogimage aufgeführt ist.  
**Problemumgehung**: Replizieren Sie entweder das Image in die Region, oder erstellen Sie eine VM in einer der Regionen in den Veröffentlichungsregionen des Katalogimages.

**Meldung:** *Der Parameter "osProfile" ist nicht zulässig.*  
**Ursache:** Benutzername, Kennwort oder SSH-Schlüssel des Administrators wurden für eine VM bereitgestellt, die aus einer spezialisierten Imageversion erstellt wurde.  
**Problemumgehung**: Geben Sie nicht den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators an, wenn Sie beabsichtigen, eine VM aus diesem Image zu erstellen. Verwenden Sie andernfalls eine generalisierte Imageversion, und geben Sie den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators an.

**Meldung:** *Der erforderliche Parameter "osProfile" fehlt (NULL).*  
**Ursache:** Die VM wird aus einem generalisierten Image erstellt, und es fehlen Benutzername, Kennwort oder SSH-Schlüssel des Administrators. Da generalisierte Images nicht den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators enthalten, müssen diese Felder bei der Erstellung einer VM oder einer Skalierungsgruppe angegeben werden.  
**Problemumgehung**: Geben Sie den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators an, oder verwenden Sie eine spezialisierte Imageversion.

**Meldung:** *Es ist nicht möglich, den Skalierungssatz für virtuelle Computer \<vmssName\> zu aktualisieren, da der aktuelle Betriebssystemstatus des Skalierungssatzes für virtuelle Computer "Generalized" ist, was sich vom Betriebssystemstatus des aktualisierten Katalogs unterscheidet, der "Specialized" ist.*  
**Ursache:** Das aktuelle Quellimage für die Skalierungsgruppe ist ein generalisiertes Quellimage, aber es wird mit einem spezialisierten Quellimage aktualisiert. Das aktuelle und das neue Quellimage für eine Skalierungsgruppe müssen den gleichen Status aufweisen.  
**Problemumgehung**: Verwenden Sie ein generalisierte Imageversion, um die Skalierungsgruppe zu aktualisieren.

**Meldung**: Der *Datenträgerverschlüsselungssatz \<diskEncryptionSetID\> in Azure Compute Gallery \<versionID\> gehört zum Abonnement \<subscriptionID\_1\> und kann nicht mit der Ressource “ im Abonnement \<subscriptionID\_2\>* benutzt werden  
**Ursache:** Der zur Verschlüsselung der Imageversion verwendete Datenträgerverschlüsselungssatz befindet sich in einem anderen Abonnement als dem zum Hosten der Imageversion.  
**Problemumgehung**: Verwenden Sie dasselbe Abonnement für die Imageversion und den Datenträgerverschlüsselungssatz.

**Meldung:** *The VM or virtual machine scale set creation takes a long time.* (Das Erstellen der VM oder VM-Skalierungsgruppe dauert lange.)  
**Problemumgehung**: Überprüfen Sie, ob der **OSType** der Imageversion, aus der Sie die VM oder VM-Skalierungsgruppe erstellen möchten, derselbe **OSType** wie der des Quellimages ist, das Sie zum Erstellen der Imageversion verwendet haben. 

**Meldung**: *Die Ressource mit der ID \<vmID\> hat ein/en anderen Plan ['{\"name\":\"\<name\>\",\"Verleger\":\"\<publisher\>\",\"Produkt\":\"\<product\>\",\"PromotionCode\":\"\<promotionCode\>\"}'] als der übergeordnete Gallery-Image-Plan ['null'].*  
**Ursache**: Die übergeordnete Imagedefinition für die bereitgestellte Imageversion verfügt nicht über eine Kaufplaninformation.  
**Problemumgehung**: Erstellen Sie eine Imagedefinition mit denselben Kaufplandetails aus der Fehlermeldung, und erstellen Sie die Imageversion innerhalb der Imagedefinition.


## <a name="creating-a-disk-from-an-image-version"></a>Erstellen eines Datenträgers aus einer Imageversion ##

**Meldung:** *Der Wert von Parameter imageReference ist ungültig.*  
**Ursache**: Sie haben einen Export aus einer Galerie-Image-Version auf einen Datenträger versucht, aber eine LUN-Position verwendet, die im Image nicht vorhanden ist.    
**Problemumgehung**: Prüfen Sie die Imageversion hinsichtlich der verwendeten LUN-Positionen.

## <a name="sharing-resources"></a>Gemeinsame Nutzung von Ressourcen

Das Freigeben von Galerie-, Image-Definitions- und Image-Versions-Ressourcen zwischen Abonnements wird mithilfe der [rollenbasierten Zugriffssteuerung von Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) (Azure role-based access control, Azure RBAC) aktiviert. 

## <a name="replication-speed"></a>Replikationsgeschwindigkeit

Verwenden Sie das Flag **--expand ReplicationStatus**, um zu überprüfen, ob die Replikation in alle angegebenen Zielregionen abgeschlossen wurde. Wenn nicht, warten Sie bis zu 6 Stunden auf den Abschluss des Auftrags. Falls dies fehlschlägt, lösen Sie den Befehl erneut aus, um die Imageversion zu erstellen und zu replizieren. Wenn es viele Zielregionen gibt, in die die Imageversion repliziert wird, erwägen Sie eine Replikation in mehreren Phasen.

## <a name="azure-limits-and-quotas"></a>Azure-Grenzwerte und -Kontingente 

[Azure-Grenzwerte und -Kontingente](../azure-resource-manager/management/azure-subscription-service-limits.md) gelten für alle Ressourcen in Azure Compute Gallery, der Image-Definiton und der Image-Version. Stellen Sie sicher, dass Sie die Grenzwerte für Ihre Abonnements einhalten. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über [Azure Compute Galleries](./shared-image-galleries.md).

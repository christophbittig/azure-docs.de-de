---
title: Behandeln von Problemen mit freigegebenen Images in Azure
description: In diesem Artikel erfahren Sie, wie Sie Probleme in Katalogen mit freigegebenen Images behandeln.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 7/1/2021
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 974dba9d3770d7d5570896f24d31fc2378472ae2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697903"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Problembehandlung für Kataloge mit freigegebenen Images in Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Wenn beim Ausführen von Vorgängen an Katalogen mit freigegebenen Images, Imagedefinitionen und Imageversionen Probleme auftreten, führen Sie den fehlgeschlagenen Befehl noch mal im Debugmodus aus. Sie aktivieren den Debugmodus, indem Sie die `--debug`-Option mit der Azure CLI oder die `-Debug`-Option mit PowerShell übergeben. Nachdem Sie den Fehler gefunden haben, befolgen Sie diesen Artikel, um das Problem zu beheben.


## <a name="creating-or-modifying-a-gallery"></a>Erstellen oder Ändern eines Katalogs ##

**Meldung:** *Gallery name is invalid. (Der Katalogname ist ungültig.) Zulässig sind englische alphanumerische Zeichen, bei denen in der Mitte Unterstriche und Punkte erlaubt sind, mit bis zu 80 Zeichen. Alle anderen Sonderzeichen, einschließlich Bindestriche, sind unzulässig.*  
**Ursache:** Der Name für den Katalog entspricht nicht den Benennungsanforderungen.  
**Problemumgehung**: Wählen Sie einen Namen, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung:** *The entity name 'galleryName' is invalid according to its validation rule: ^[^\_\W][\w-.\_]{0,79}(?<![-.])$.* . (Der Entitätsname "galleryName" ist gemäß seiner Validierungsregel ungültig: ^[^_\W][\w-._]{0,79}(?<![-.])$.)  
**Ursache:** Der Katalogname entspricht nicht den Benennungsanforderungen.  
**Problemumgehung**: Wählen Sie einen Namen für den Katalog, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung:** *Der angegebene Ressourcenname <galleryName\> weist die folgenden ungültigen nachgestellten Zeichen auf: <character\>. Der Name darf nicht mit diesen Zeichen enden: <character\>*  
**Ursache:** Der Katalogname endet mit einem Punkt oder Unterstrich.  
**Problemumgehung**: Wählen Sie einen Namen für den Katalog, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung:** *Der angegebene Standort <region\> ist für Ressourcentyp "Microsoft.Compute/galleries" nicht verfügbar. Liste der verfügbaren Regionen für den Ressourcentyp:*  
**Ursache:** Die für den Katalog angegebene Region ist falsch oder erfordert eine Zugriffsanforderung.  
**Problemumgehung**: Vergewissern Sie sich, dass der Regionsname richtig ist. Wenn der Regionsname richtig ist, übermitteln Sie [eine Zugriffsanforderung](/troubleshoot/azure/general/region-access-request-process) für die Region.

**Meldung:** *Ressource kann nicht gelöscht werden, bevor verschachtelte Ressourcen gelöscht wurden.*  
**Ursache:** Sie haben versucht, einen Katalog zu löschen, der mindestens eine vorhandene Imagedefinition enthält. Ein Katalog muss leer sein, bevor er gelöscht werden kann.  
**Problemumgehung**: Löschen Sie alle Imagedefinitionen innerhalb des Katalogs, und fahren Sie dann mit dem Löschen des Katalogs fort. Wenn die Imagedefinition Imageversionen enthält, müssen Sie die Imageversionen löschen, bevor Sie die Imagedefinitionen löschen.

**Meldung:** *The gallery name '<galleryName\>' is not unique within the subscription '<subscriptionID>'. (Der Katalogname "<galleryName>" ist innerhalb des Abonnements "[subscriptionID]" nicht eindeutig.) Wählen Sie einen anderen Katalognamen aus.*  
**Ursache:** Es ist bereits ein Katalog mit demselben Namen vorhanden, und Sie haben versucht, einen weiteren Katalog mit diesem Namen zu erstellen.  
**Problemumgehung:** Wählen Sie einen anderen Namen für den Katalog aus.

**Meldung:** *Die Ressource <galleryName\> ist bereits am Standort <region\_1\> in Ressourcengruppe <resourceGroup\> enthalten. Eine Ressource mit dem gleichen Namen kann nicht in der Region <region\_2\>erstellt werden. Wählen Sie einen neuen Ressourcennamen aus.*  
**Ursache:** Es ist bereits ein Katalog mit demselben Namen vorhanden, und Sie haben versucht, einen weiteren Katalog mit diesem Namen zu erstellen.  
**Problemumgehung:** Wählen Sie einen anderen Namen für den Katalog aus.

## <a name="creating-or-modifying-image-definitions"></a>Erstellen oder Ändern von Imagedefinitionen ##

**Meldung:** *Das Ändern der Eigenschaft "galleryImage.properties.<property\>" ist unzulässig.*  
**Ursache:** Sie haben versucht, den Betriebssystemtyp, den Zustand des Betriebssystems, die Hyper-V-Generation, das Angebot, den Herausgeber oder die SKU zu ändern. Das Ändern dieser Eigenschaften ist nicht zulässig.  
**Problemumgehung**: Erstellen Sie stattdessen eine neue Imagedefinition.

**Meldung:** *Die Ressource <galleryName/imageDefinitionName\> ist bereits am Standort <region\_1\> in Ressourcengruppe <resourceGroup\> enthalten. Eine Ressource mit dem gleichen Namen kann nicht in der Region <region\_2\>erstellt werden. Wählen Sie einen neuen Ressourcennamen aus.*  
**Ursache:** Sie verfügen über eine vorhandene Imagedefinition im gleichen Katalog und derselben Ressourcengruppe mit dem gleichen Namen. Sie haben versucht, eine weitere Imagedefinition mit demselben Namen im gleichen Katalog, aber in einer anderen Region zu erstellen.  
**Problemumgehung**: Wählen Sie einen anderen Namen für die Imagedefinition aus, oder legen Sie die Imagedefinition in einem anderen Katalog oder einer anderen Ressourcengruppe ab.

**Meldung:** *Der angegebene Ressourcenname <galleryName\>/<imageDefinitionName\> weist die folgenden ungültigen nachgestellten Zeichen auf: <character\>. Der Name darf nicht mit diesen Zeichen enden: <character\>*  
**Ursache:** Der Name <imageDefinitionName\> endet mit einem Punkt oder Unterstrich.  
**Problemumgehung**: Wählen Sie einen Namen für die Imagedefinition aus, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche, Bindestriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung:** *The entity name <imageDefinitionName\> is invalid according to its validation rule: ^[^\_\\W][\\w-.\_]{0,79}(?<![-.])$"* . (Der Entitätsname <imageDefinitionName> ist gemäß seiner Validierungsregel ungültig: ^[^_\W][\w-._]{0,79}(?<![-.])$".)  
**Ursache:** Der Name <imageDefinitionName\> endet mit einem Punkt oder Unterstrich.  
**Problemumgehung**: Wählen Sie einen Namen für die Imagedefinition aus, der die folgenden Bedingungen erfüllt: 
- Maximal 80 Zeichen
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Unterstriche, Bindestriche und Punkte
- Beginnt und endet mit Zahlen oder Buchstaben des englischen Alphabets

**Meldung:** *Der Ressourcenname galleryImage.properties.identifier.<property\> ist ungültig. Er darf nicht leer sein. Zulässige Zeichen sind Groß- und Kleinbuchstaben, Ziffern, Bindestrich (-), Punkt (.) und Unterstrich (\_). Namen dürfen nicht mit einem Punkt (.) enden. Die Länge des Namens darf <number\> Zeichen nicht überschreiten.*  
**Ursache:** Der Herausgeber, das Angebot oder der SKU-Wert entspricht nicht den Benennungsanforderungen.  
**Problemumgehung**: Wählen Sie einen Wert, der die folgenden Bedingungen erfüllt: 
- Für den Herausgeber besteht ein Limit von 128 Zeichen und für das Angebot und die SKU besteht ein Limit von 64 Zeichen.
- Enthält nur Buchstaben des englischen Alphabets sowie Zahlen, Bindestriche, Unterstriche und Punkte
- Endet nicht mit einem Punkt

**Meldung:** *Der angeforderte Vorgang an geschachtelter Ressource konnte nicht durchgeführt werden. Die übergeordnete Ressource <galleryname\> nicht gefunden.*  
**Ursache:** Im aktuellen Abonnement und in der aktuellen Ressourcengruppe gibt es keinen Katalog mit dem Namen <galleryName\>.  
**Problemumgehung**: Überprüfen Sie, ob die Namen des Katalogs, des Abonnements und der Ressourcengruppe richtig sind. Erstellen Sie andernfalls einen neuen Katalog mit dem Namen <galleryname\>.

**Meldung:** *Der angegebene Standort <region\> ist für Ressourcentyp "Microsoft.Compute/galleries" nicht verfügbar. Liste der verfügbaren Regionen für den Ressourcentyp:*  
**Ursache:** Der Name <region\> ist falsch oder erfordert eine Zugriffsanforderung.  
**Problemumgehung**: Prüfen Sie, ob der Regionsname richtig geschrieben ist. Sie können diesen Befehl ausführen, um die Regionen anzuzeigen, auf die Sie Zugriff haben. Wenn die Region nicht in der Liste enthalten ist, senden Sie [eine Zugriffsanforderung](/troubleshoot/azure/general/region-access-request-process).

**Meldung:** *Unable to serialize value: <value\> as type: 'iso-8601'., ISO8601Error: ISO 8601 time designator 'T' missing. (Der Wert <value> kann nicht als Typ "iso-8601" serialisiert werden, ISO8601Error: ISO 8601-Zeitkennzeichner "T" fehlt.) datetime-Zeichenfolge <value\>* kann nicht analysiert werden.  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung:** *Could not convert string to DateTimeOffset: <value\>. (Die Zeichenfolge konnte nicht in DateTimeOffset konvertiert werden: <value>.) Pfad properties.<property\>*  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung:** *Das Lebensendedatum muss auf ein zukünftiges Datum festgelegt werden.*  
**Ursache:** Die EndOfLifeDate-Eigenschaft ist nicht ordnungsgemäß als ein Datum formatiert, das auf das heutige Datum folgt.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung:** *argument --<property\>: invalid int value: <value\>* (Argument --<property>: ungültiger int-Wert: <value>.)  
**Ursache:** <property\> akzeptiert nur Integerwerte (ganze Zahlen), und <value\> ist kein Integer.  
**Problemumgehung**: Wählen Sie als Wert eine ganze Zahl.

**Meldung:** *Der Mindestwert von <property\> darf nicht höher als der Höchstwert von <property\> sein.*  
**Ursache:** Der für <property\> angegebene Mindestwert ist größer als der für <property\> angegebene Höchstwert.  
**Problemumgehung**: Ändern Sie die Werte so, dass der Mindestwert kleiner oder gleich dem Höchstwert ist.

**Meldung:** *Gallery image: <imageDefinitionName\> identified by (publisher:<Publisher\>, offer:<Offer\>, sku:<SKU\>) already exists. (Das von (Herausgeber:<Publisher>, Angebot:<Offer>, SKU:<SKU>) ermittelte Katalogimage <imageDefinitionName> ist bereits vorhanden.) Wählen Sie eine andere Kombination aus Herausgeber, Angebot und SKU.*  
**Ursache:** Sie haben versucht, eine neue Imagedefinition mit derselben Kombination aus Herausgeber, Angebot und SKU wie bei einer bestehenden Imagedefinition im selben Katalog zu erstellen.  
**Problemumgehung**: Innerhalb eines Katalogs müssen alle Imagedefinitionen eine eindeutige Kombination aus Herausgeber, Angebot und SKU aufweisen. Wählen Sie eine eindeutige Kombination oder einen neuen Katalog aus, und erstellen Sie die Imagedefinition noch mal.

**Meldung:** *Ressource kann nicht gelöscht werden, bevor verschachtelte Ressourcen gelöscht wurden.*  
**Ursache:** Sie haben versucht, eine Imagedefinition zu löschen, die Imageversionen enthält. Eine Imagedefinition muss leer sein, ehe sie gelöscht werden kann.  
**Problemumgehung**: Löschen Sie alle Imageversionen innerhalb der Imagedefinition, und fahren Sie dann mit dem Löschen der Imagedefinition fort.

**Meldung**: *Kann Parameter nicht binden <property\>. Kann Wert nicht <value\> Typ <propertyType konvertieren\>. Kann Bezeichnername nicht <value\> einem gültigen Enumeratornamen zuordnen. Spezifizieren Sie einen der folgenden Enumeratornamen und versuchen Sie es nocheinmal: <choice\_1\>, <choice\_2\>, …*  
**Ursache:** Die Eigenschaft verfügt über eine eingeschränkte Liste möglicher Werte, und <value\> gehört nicht dazu.  
**Problemumgehung**: Wählen Sie einen der möglichen Werte für <choice\> aus.

**Meldung:** *Cannot bind parameter <property\>. (Der Parameter <property> kann nicht gebunden werden.) <value\> kann nicht in den Typ &quot;System.DateTime&quot;* konvertiert werden.  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung:** *Cannot bind parameter <property\>. (Der Parameter <property> kann nicht gebunden werden.) <value\> kann nicht in den Typ &quot;System.Int32&quot;* konvertiert werden.  
**Ursache:** <property\> akzeptiert nur Integerwerte (ganze Zahlen), und <value\> ist kein Integer.  
**Problemumgehung**: Wählen Sie als Wert eine ganze Zahl.

**Meldung:** *Der Speicherkontotyp ZRS wird in dieser Region nicht unterstützt.*  
**Ursache:** Sie haben sich für den standardmäßigen zonenredundanten Speicher (ZRS) in einer Region entschieden, die diesen noch nicht unterstützt.  
**Problemumgehung**: Ändern Sie den Speicherkontotyp in **Premium\_LRS** oder **Standard\_LRS**. In unserer Dokumentation finden Sie die aktuelle [Liste der Regionen](../storage/common/storage-redundancy.md#zone-redundant-storage) mit aktivierter ZRS-Vorschau.

## <a name="creating-or-updating-image-versions"></a>Erstellen oder Aktualisieren von Imageversionen ##

**Meldung:** *Der angegebene Standort <region\> ist für Ressourcentyp "Microsoft.Compute/galleries" nicht verfügbar. Liste der verfügbaren Regionen für den Ressourcentyp:*  
**Ursache:** Der Name <region\> ist falsch oder erfordert eine Zugriffsanforderung.  
**Problemumgehung**: Prüfen Sie, ob der Regionsname richtig geschrieben ist. Sie können diesen Befehl ausführen, um die Regionen anzuzeigen, auf die Sie Zugriff haben. Wenn die Region nicht in der Liste enthalten ist, senden Sie [eine Zugriffsanforderung](/troubleshoot/azure/general/region-access-request-process).

**Meldung:** *Der angeforderte Vorgang an geschachtelter Ressource konnte nicht durchgeführt werden. Übergeordnete Ressource <galleryName/imageDefinitionName\> nicht gefunden.*  
**Ursache:** Im aktuellen Abonnement und in der aktuellen Ressourcengruppe gibt es keinen Katalog mit dem Namen <<galleryName/imageDefinitionName\>.  
**Problemumgehung**: Überprüfen Sie, ob die Namen des Katalogs, des Abonnements und der Ressourcengruppe richtig sind. Erstellen Sie andernfalls einen neuen Katalog mit dem Namen <galleryName\> und/oder eine Imagedefinition mit dem Namen <imageDefinitionName\> in der angegebenen Ressourcengruppe.

**Meldung:** *Cannot bind parameter <property\>. (Der Parameter <property> kann nicht gebunden werden.) <value\> kann nicht in den Typ &quot;System.DateTime&quot;* konvertiert werden.  
**Ursache:** Der Wert, der für die Eigenschaft angegeben wurde, ist nicht ordnungsgemäß als Datum formatiert.  
**Problemumgehung**: Geben Sie ein Datum im Format yyyy-MM-dd, yyyy-MM-dd'T'HH:mm:sszzz oder entsprechend [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) an.

**Meldung:** *Cannot bind parameter <property\>. (Der Parameter <property> kann nicht gebunden werden.) <value\> kann nicht in den Typ &quot;System.Int32&quot;* konvertiert werden.  
**Ursache:** <property\> akzeptiert nur Integerwerte (ganze Zahlen), und <value\> ist kein Integer.  
**Problemumgehung**: Wählen Sie als Wert eine ganze Zahl.

**Meldung:** *Gallery image version publishing profile regions <publishingRegions\> must contain the location of image version <sourceRegion\>* (Die Profilregionen <publishingRegions>, die die Katalogimageversion veröffentlichen, müssen den Standort der Imageversion <sourceRegion> enthalten.)  
**Ursache:** Der Standort des Quellimages (<sourceRegion\>) muss in der Liste <publishingRegions\> enthalten sein.  
**Problemumgehung**: Fügen Sie <sourceRegion\> zur Liste <publishingRegions\> hinzu.

**Meldung:** *The value <value\> of parameter <property\> is out of range. (Der Wert <value> des Parameters <property> liegt außerhalb des zulässigen Bereichs.) Der Wert muss im Bereich von <minValue\> bis <maxValue\> liegen.*  
**Ursache**: <value\> liegt außerhalb des Bereichs möglicher Werte für <property\>.  
**Problemumgehung**: Wählen Sie einen Wert im Bereich von <minValue\> bis einschließlich <maxValue\> aus.

**Meldung:** *Source <resourceID\> is not found. (Die Quelle <resourceID> wurde nicht gefunden.) Prüfen Sie, ob die Quelle vorhanden ist und sich in der gleichen Region befindet wie die zu erstellende Katalogimageversion.*  
**Ursache:** Es gibt keine Quelle bei <resourceID\>, oder die Quelle bei <resourceID\> befindet sich nicht in der gleichen Region wie das zu erstellende Katalogimage.  
**Problemumgehung**: Prüfen Sie, ob der <resourceID\>-Wert stimmt und ob die Quellregion der Katalogimageversion mit der Region des <resourceID\>-Werts übereinstimmt.

**Meldung:** *Das Ändern der Eigenschaft "galleryImageVersion.properties.storageProfile.<diskImage\>.source.id" ist nicht zulässig.*  
**Ursache:** Die Quell-ID einer Katalogimageversion kann nach der Erstellung nicht mehr geändert werden.  
**Problemumgehung**: Stellen Sie sicher, dass die Quell-ID mit der vorhandenen Quell-ID übereinstimmt, ändern Sie die Versionsnummer der Imageversion, oder löschen Sie die aktuelle Imageversion, und versuchen Sie es dann noch einmal.

**Meldung:** *Duplicated lun numbers have been detected in the input data disks. (Auf den Eingabedatenträgern wurden duplizierte LUN-Nummern erkannt.) Die LUN-Nummer muss für jeden Datenträger eindeutig sein.*  
**Ursache:** Wenn Sie eine Imageversion mithilfe einer Liste von Datenträgern und/oder Datenträgermomentaufnahmen erstellen, weisen zwei oder mehr Datenträger oder Datenträgermomentaufnahmen dieselbe logische Gerätenummer (LUN) auf.  
**Problemumgehung**: Entfernen oder ändern Sie doppelte LUNs.

**Meldung:** *Duplicated source ids are found in the input disks. (Auf den Eingabedatenträgern wurden duplizierte Quell-IDs gefunden.) Die Quell-ID muss für jeden Datenträger eindeutig sein.*  
**Ursache:** Wenn Sie eine Imageversion mithilfe einer Liste von Datenträgern und/oder Datenträgermomentaufnahmen erstellen, weisen zwei oder mehr Datenträger oder Datenträgermomentaufnahmen dieselbe Ressourcen-ID auf.  
**Problemumgehung**: Entfernen oder ändern Sie doppelte Datenträgerquell-IDs.

**Meldung**: *Property id <resourceID\> at path 'properties.storageProfile.<diskImages\>.source.id' is invalid. (Die Eigenschaften-ID „<resourceID“ im Pfad „properties.storageProfile.<diskImages.source.id“ ist ungültig.) Es wird eine qualifizierte Ressourcen-ID erwartet, die mit /subscriptions/<subscriptionID> oder /providers/<resourceProviderNamespace>/.* beginnt.  
**Ursache:** Der <resourceID\>-Wert ist falsch formatiert.  
**Problemumgehung**: Überprüfen Sie, ob die Ressourcen-ID stimmt.

**Meldung:** *The source id: <resourceID\> must either be a managed image, virtual machine or another gallery image version* (Die Quell-ID <resourceID> muss entweder ein verwaltetes Image, ein virtueller Computer oder eine andere Katalogimageversion sein.)  
**Ursache:** Der <resourceID\>-Wert ist falsch formatiert.  
**Problemumgehung**: Wenn Sie eine VM, ein verwaltetes Image oder eine Katalogimageversion als Quellimage verwenden, überprüfen Sie, ob die Ressourcen-ID der VM, des verwalteten Images oder der Katalogimageversion stimmt.

**Meldung:** *The source id: <resourceID\> must be a managed disk or snapshot.* (Die Quell-ID <resourceID> muss ein verwalteter Datenträger oder eine Momentaufnahme sein.)  
**Ursache:** Der <resourceID\>-Wert ist falsch formatiert.  
**Problemumgehung**: Wenn Sie Datenträger und/oder Momentaufnahmen von Datenträgern als Quellen für die Imageversion verwenden, überprüfen Sie, ob die Ressourcen-IDs der Datenträger und/oder Momentaufnahmen von Datenträgern stimmen.

**Meldung:** *Cannot create Gallery Image Version from: <resourceID\> since the OS State in the parent gallery image (<OsState\_1\>) is not <OsState\_2\>.* (Die Katalogimageversion kann nicht anhand von <resourceID> erstellt werden, da der Betriebssystemstatus im übergeordneten Katalogimage (<OsState_1>) nicht <OsState_2> ist.)  
**Ursache:** Der Betriebssystemstatus („Generalisiert“ oder „Spezialisiert“) stimmt nicht mit dem in der Imagedefinition angegebenen Betriebssystemstatus identisch.  
**Problemumgehung**: Wählen Sie entweder eine Quelle basierend auf einer VM mit dem Betriebssystemstatus <OsState\_1\>, oder erstellen Sie eine neue Imagedefinition für VMs basierend auf <OsState\_2\>.

**Meldung:** *The resource with id '<resourceID\>' has a different Hypervisor generation ['<V#\_1\>'] than the parent gallery image Hypervisor generation ['<V#\_2\>']* (Die Ressource mit der ID <resourceID> hat eine andere Hypervisor-Generation ['<V#_1>'] als das übergeordnete Katalogimage ['<V#_2>'].)  
**Ursache:** Die Hypervisor-Generation der Imageversion stimmt nicht mit der in der Imagedefinition angegebenen Hypervisor-Generation überein. Das Betriebssystem der Imagedefinition ist <V#\_1\> und das der Imageversion ist <V#\_2\>.  
**Problemumgehung**: Wählen Sie entweder eine Quelle mit der gleichen Hypervisor-Generation wie in der Imagedefinition aus, oder erstellen/wählen Sie eine neue Imagedefinition, die die gleiche Hypervisor-Generation wie die Imageversion aufweist.

**Meldung:** *The resource with id '<resourceID\>' has a different OS type ['<OsType\_1\>'] than the parent gallery image OS type generation ['<OsType \_2\>']* (Die Ressource mit der ID <resourceID> hat den Betriebssystemtyp [<OsType_1>], der nicht der Generation des Betriebssystemtyps des übergeordneten Katalogimages [<OsType_2>] entspricht.)  
**Ursache:** Die Hypervisor-Generation der Imageversion stimmt nicht mit der in der Imagedefinition angegebenen Hypervisor-Generation überein. Das Betriebssystem der Imagedefinition ist <OsType\_1\> und das der Imageversion ist <OsType\_2\>.  
**Problemumgehung**: Wählen Sie entweder eine Quelle mit dem gleichen Betriebssystem (Linux/Windows) wie in der Imagedefinition, oder erstellen/wählen Sie eine neue Imagedefinition, die die gleiche Betriebssystemgeneration wie die Imageversion aufweist.

**Meldung:** *Source virtual machine <resourceID\> cannot contain an ephemeral OS disk.* (Die Quell-VM <resourceID> darf keinen kurzlebigen Betriebssystemdatenträger enthalten.)  
**Ursache:** Die Quelle in <resourceID\> enthält einen kurzlebigen Betriebssystemdatenträger. Shared Image Gallery unterstützt derzeit keine kurzlebigen Betriebssystemdatenträger.  
**Problemumgehung**: Wählen Sie eine andere Quelle basierend auf einer VM ohne kurzlebigen Betriebssystemdatenträger.

**Meldung:** *Source virtual machine <resourceID\> cannot contain disk ['<diskID\>'] stored in an UltraSSD account type.* (Die Quell-VM <resourceID> darf nicht den Datenträger [<diskID>] enthalten, der in einem Konto vom Typ UltraSSD gespeichert ist.)  
**Ursache:** Der Datenträger <diskID\> ist ein SSD Ultra-Datenträger. Shared Image Gallery unterstützt SSD Ultra-Datenträger derzeit nicht.  
**Problemumgehung**: Verwenden Sie eine Quelle, die nur verwaltete Datenträger des Typs SSD Premium, SSD Standard und/oder HDD Standard enthält.

**Meldung:** *Source virtual machine <resourceID\> must be created from Managed Disks.* (Die Quell-VM <resourceID> muss aus verwalteten Datenträgern erstellt werden.)  
**Ursache:** Die VM mit <resourceID\> verwendet nicht verwaltete Datenträger.  
**Problemumgehung**: Verwenden Sie eine auf einer VM basierende Quelle, die nur verwaltete Datenträger des Typs SSD Premium, SSD Standard und/oder HDD Standard enthält.

**Meldung:** *Too many requests on source '<resourceID\>'. (Zu viele Anforderungen an Quelle "<resourceID>".) Verringern Sie die Anzahl der Anforderungen an die Quelle, oder warten Sie einen Moment, ehe Sie den Vorgang wiederholen.*  
**Ursache:** Die Quelle dieser Imageversion wird derzeit aufgrund zu vieler Anforderungen gedrosselt.  
**Problemumgehung**: Versuchen Sie später, die Imageversion zu erstellen.

**Meldung:** *The disk encryption set '<diskEncryptionSetID\>' must be in the same subscription '<subscriptionID\>' as the gallery resource.* (Der Datenträgerverschlüsselungssatz <diskEncryptionSetID> muss sich im selben Abonnement <subscriptionID> wie die Katalogressource befinden.)  
**Ursache:** Datenträgerverschlüsselungssätze können nur im selben Abonnement und in derselben Region verwendet werden, in der sie erstellt wurden.  
**Problemumgehung**: Erstellen oder verwenden Sie einen Verschlüsselungssatz, der im gleichen Abonnement und in der gleichen Region wie die Imageversion enthalten ist.

**Meldung:** *Encrypted source: '<resourceID\>' is in a different subscription ID than the current gallery image version subscription '<subscriptionID\_1\>'. (Die verschlüsselte Quelle "<resourceID>" hat eine andere Abonnement-ID als das aktuelle Abonnement der Katalogimageversion "<subscriptionID_1>".) Wiederholen Sie den Vorgang mit einer unverschlüsselten Quelle, oder verwenden Sie das Abonnement der Quelle <subcriptionID\_2\>, um die Katalogimageversion zu erstellen.*  
**Ursache:** Shared Image Gallery unterstützt derzeit nicht die Erstellung von Imageversionen in einem anderen Abonnement aus einem anderen Quellimage, wenn das Quellimage verschlüsselt ist.  
**Problemumgehung**: Verwenden Sie eine unverschlüsselte Quelle, oder erstellen Sie die Imageversion im selben Abonnement wie die Quelle.

**Meldung:** *The disk encryption set <diskEncryptionSetID\> was not found.* (Der Datenträgerverschlüsselungssatz <diskEncryptionSetID> wurde nicht gefunden.)  
**Ursache:** Die Datenträgerverschlüsselung ist möglicherweise falsch.  
**Problemumgehung**: Prüfen Sie, ob die Ressourcen-ID des Datenträgerverschlüsselungssatzes stimmt.

**Meldung:** *The image version name is invalid. (Der Name der Imageversion ist ungültig.) Der Name der Imageversion muss das Format Hauptversion(ganze Zahl).Nebenversion(ganze Zahl).Patch(ganze Zahl) aufweisen. Beispiel:1.0.0, 2018.12.1 usw.*  
**Ursache:** Das gültige Format einer Imageversion umfasst drei durch einen Punkt getrennte ganze Zahlen. Der Name der Imageversion entsprach nicht dem gültigen Format.  
**Problemumgehung**: Verwenden Sie einen Imageversionsnamen mit dem Format Hauptversion(ganze Zahl).Nebenversion(ganze Zahl).Patch(ganze Zahl). Beispiel: 1.0.0. oder 2018.12.1.

**Message**: *Der Wert von Parameter galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.dataDiskImages.diskEncryptionSetId ist ungültig.*  
**Ursache:** Die Ressourcen-ID des Datenträgerverschlüsselungssatzes eines Datenträgerimages hat ein ungültiges Format.  
**Problemumgehung**: Vergewissern Sie sich, dass die Ressourcen-ID des Datenträgerverschlüsselungssatzes das folgende Format hat: /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroupName\>/providers/Microsoft.Compute/<diskEncryptionSetName\>.

**Message**: *Der Wert von Parameter galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId ist ungültig.*  
**Ursache:** Die Ressourcen-ID des Datenträgerverschlüsselungssatzes für das Image des Betriebssystemdatenträgers weist ein ungültiges Format auf.  
**Problemumgehung**: Vergewissern Sie sich, dass die Ressourcen-ID des Datenträgerverschlüsselungssatzes das folgende Format hat: /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroupName\>/providers/Microsoft.Compute/<diskEncryptionSetName\>.

**Meldung:** *Cannot specify new data disk image encryption lun [<number\>] with a disk encryption set in region [<region\>] for update gallery image version request. (Zur Anforderung einer aktualisierten Katalogimageversion kann keine neue LUN [<number>] für die Verschlüsselung des Datenträgerimages angegeben werden, wenn sich ein Datenträgerverschlüsselungssatz in der Region [<region>] befindet.) Um diese Version zu aktualisieren, entfernen Sie die neue LUN. Wenn Sie die Verschlüsselungseinstellungen für das Datenträgerimage ändern müssen, müssen Sie eine neue Katalogimageversion mit den richtigen Einstellungen erstellen.*  
**Ursache:** Sie haben die Verschlüsselung zum Datenträger einer vorhandenen Imageversion hinzugefügt. Sie können die Verschlüsselung nicht zu einer vorhandenen Imageversion hinzufügen.  
**Problemumgehung**: Erstellen Sie eine neue Katalogimageversion, oder entfernen Sie die hinzugefügten Verschlüsselungseinstellungen.

**Meldung:** *The gallery artifact version source can only be specified either directly under storageProfile or within individual OS or data disks. (Die Quelle der Katalogartefaktversion kann nur entweder direkt unter storageProfile oder innerhalb einzelner Betriebssystem- oder sonstiger Datenträger angegeben werden.) Es kann nur ein einziger Quelltyp (Benutzerimage, Momentaufnahme, Datenträger, virtueller Computer) bereitgestellt werden.*  
**Ursache:** Die Quell-ID fehlt.  
**Problemumgehung**: Stellen Sie sicher, dass die Quell-ID der Quelle vorhanden ist.

**Meldung:** *Source was not found: <resourceID\>. (Quelle nicht gefunden: <resourceID>.) Stellen Sie sicher, dass die Quelle vorhanden ist.*  
**Ursache:** Die Ressourcen-ID der Quelle ist möglicherweise falsch.  
**Problemumgehung**: Stellen Sie sicher, dass die Ressourcen-ID der Quelle stimmt.

**Meldung:** *A disk encryption set is required for disk 'galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId' in target region '<Region\_1\>' since disk encryption set '<diskEncryptionSetID\>' is used for the corresponding disk in region '<Region\_2\>'* (Ein Datenträgerverschlüsselungssatz ist für den Datenträger "galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId" in der Zielregion "<Region_1>" erforderlich, da der Datenträgerverschlüsselungssatz "<diskEncryptionSetID>" für den entsprechenden Datenträger in der Region "<Region_2>" verwendet wird.)  
**Ursache:** Verschlüsselung wurde auf dem Betriebssystemdatenträger in <Region\_2\>, jedoch nicht in <Region\_1\> verwendet.  
**Problemumgehung**: Wenn Sie Verschlüsselung auf dem Betriebssystemdatenträger verwenden, müssen Sie sie in allen Regionen verwenden.

**Meldung:** *A disk encryption set is required for disk 'LUN <number\>' in target region '<Region\_1\>' since disk encryption set '<diskEncryptionSetID\>' is used for the corresponding disk in region '<Region\_2\>'* (Ein Datenträgerverschlüsselungssatz ist für den Datenträger "LUN <number>" in der Zielregion "<Region_1>" erforderlich, da der Datenträgerverschlüsselungssatz "<diskEncryptionSetID>" für den entsprechenden Datenträger in der Region "<Region_2>" verwendet wird.)  
**Ursache:** Verschlüsselung wurde auf dem Datenträger mit LUN <number\> in <Region\_2\> verwendet, aber nicht in <Region\_1\>.  
**Problemumgehung**: Wenn Sie Verschlüsselung auf einem Datenträger verwenden, müssen Sie sie in allen Regionen verwenden.

**Meldung:** *An invalid lun [<number\>] was specified in encryption.dataDiskImages. (In encryption.dataDiskImages wurde eine ungültige LUN [<number>] angegeben.) Die LUN muss einen der folgenden Werte aufweisen ['0,9'].*  
**Ursache:** Die für die Verschlüsselung angegebene LUN stimmt mit keiner der LUNs für an die VM angefügte Datenträger überein.  
**Problemumgehung**: Ändern Sie die LUN in der Verschlüsselung in die eines Datenträgers, der auf der VM vorhanden ist.

**Meldung:** *Duplicate luns '<number\>' were specified in target region '<region\>' encryption.dataDiskImages.* (Die duplizierten LUNs "<number>" wurden in der Zielregion "<Region>" in encryption.dataDiskImages angegeben.)  
**Ursache:** In den in <region\> verwendeten Verschlüsselungseinstellungen wurde eine LUN mindestens zweimal angegeben.  
**Problemumgehung**: Ändern Sie die LUN in <region\>, um sicherzustellen, dass alle LUN-Nummern in <region\> eindeutig sind.

**Meldung:** *OSDiskImage and DataDiskImage cannot point to same blob <sourceID\>* (OSDiskImage und DataDiskImage können nicht auf denselben Blob <sourceID> zeigen.)  
**Ursache:** Die Quellen des Betriebssystemdatenträgers und mindestens eines anderen Datenträgers sind nicht eindeutig.  
**Problemumgehung**: Ändern Sie die Quelle des Betriebssystemdatenträgers und/oder der sonstigen Datenträger, um sicherzustellen, dass der Betriebssystemdatenträger sowie der jeweilige sonstige Datenträger eindeutig sind.

**Meldung:** *Duplicate regions are not allowed in target publishing regions.* (Duplizierte Regionen sind in Zielveröffentlichungsregionen nicht zulässig.)  
**Ursache:** Eine Region ist in den Veröffentlichungsregionen mehrmals aufgeführt.  
**Problemumgehung**: Entfernen Sie die duplizierte Region.

**Meldung:** *Adding new Data Disks or changing the LUN of a Data Disk in an existing Image is not allowed.* (Das Hinzufügen neuer Datenträger oder Ändern der LUN eines Datenträgers in einem vorhandenen Image ist nicht zulässig.)  
**Ursache:** Ein Updateaufruf für die Imageversion enthält entweder einen neuen Datenträger oder eine neue LUN für einen Datenträger.  
**Problemumgehung**: Verwenden Sie die LUNs und Datenträger der vorhandenen Imageversion.

**Meldung:** *The disk encryption set <diskEncryptionSetID\> must be in the same subscription <subscriptionID\> as the gallery resource.* (Der Datenträgerverschlüsselungssatz <diskEncryptionSetID> muss sich im selben Abonnement <subscriptionID> wie die Katalogressource befinden.)  
**Ursache:** Shared Image Gallery unterstützt derzeit nicht die Verwendung eines Datenträgerverschlüsselungssatzes in einem anderen Abonnement.  
**Problemumgehung**: Erstellen Sie die Imageversion und Datenträgerverschlüsselung im selben Abonnement.

**Meldung:** *Replication failed in this region due to 'The GalleryImageVersion source resource size 2048 exceeds the max size 1024 supported.'* (Fehler bei der Replikation in dieser Region aufgrund des Fehlers "Die Größe 2048 der GalleryImageVersion-Quellressource überschreitet die maximal unterstützte Größe von 1024".)  
**Ursache:** Ein Datenträger in der Quelle ist größer als 1 TB.  
**Problemumgehung:** Legen Sie für den Datenträger eine neue Größe unter 1 TB fest.

**Meldung**: *Der Vorgang „Katalogimageversion aktualisieren“ ist für „<versionNumber>“ unzulässig, weil dieses Element für den Löschvorgang markiert ist. Sie können den Löschvorgang noch mal versuchen (oder auf den Abschluss eines laufenden Vorgangs warten).*  
**Ursache**: Sie haben versucht, eine Katalogimageversion zu aktualisieren, die gerade gelöscht wird.  
**Problemumgehung**: Warten Sie, bis das Löschereignis abgeschlossen ist, und erstellen Sie die Imageversion erneut.

**Meldung**: *Die Verschlüsselung wird für die Quellressource „<sourceID>“ nicht unterstützt. Verwenden Sie einen anderen Quellressourcentyp, der die Verschlüsselung unterstützt, oder entfernen Sie die Verschlüsselungseigenschaften.*  
**Ursache**: Derzeit unterstützt Shared Image Gallery nur die Verschlüsselung für VMs, Datenträger, Momentaufnahmen und verwaltete Images. Eine der für die Imageversion bereitgestellten Quellen ist nicht in der obigen Liste der Quellen enthalten, die die Verschlüsselung unterstützen.  
**Problemumgehung**: Entfernen Sie den Datenträgerverschlüsselungssatz aus der Imageversion, und wenden Sie sich an das Supportteam.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Erstellen oder Aktualisieren einer VM oder Skalierungsgruppe anhand der Imageversion ##

**Meldung:** *There is no latest image version exists for "<imageDefinitionResourceID\>"* (Es ist keine aktuelle Imageversion für "<imageDefinitionResourceID>" vorhanden.)  
**Ursache:** Die beim Bereitstellen des virtuellen Computers verwendete Imagedefinition enthält keine in der aktuellen Version vorhandenen Imageversionen.  
**Problemumgehung:** Stellen Sie sicher, dass bei mindestens einer Imageversion die Option „Aus aktueller Version ausschließen“ auf FALSE festgelegt ist. 

**Meldung:** *The gallery image /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroup\>/providers/Microsoft.Compute/galleries/<galleryName\>/images/<imageName\>/versions/<versionNumber\> is not available in <region\> region. Please contact image owner to replicate to this region, or change your requested region.* (Das Katalogimage „/subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Compute/galleries/<Katalogname>/images/<Imagename>/versions/<Versionsnummer>“ ist in der Region „<Region>“ nicht verfügbar. Bitten Sie den Besitzer des Images um eine Replikation in diese Region, oder ändern Sie die angeforderte Region.)  
**Ursache:** Die für die Bereitstellung ausgewählte Version ist nicht vorhanden oder verfügt über kein Replikat in der angegebenen Region.  
**Problemumgehung:** Stellen Sie sicher, dass der Name der Imageressource korrekt ist und mindestens ein Replikat in der angegebenen Region vorhanden ist. 

**Meldung:** *The gallery image /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroup\>/providers/Microsoft.Compute/galleries/<galleryName\>/images/<imageName\> is not available in <region\> region. Please contact image owner to replicate to this region, or change your requested region.* (Das Katalogimage „/subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Compute/galleries/<Katalogname>/images/<Imagename>“ ist in der Region „<Region>“ nicht verfügbar. Bitten Sie den Besitzer des Images um eine Replikation in diese Region, oder ändern Sie die angeforderte Region.)  
**Ursache:** Die für die Bereitstellung ausgewählte Imagedefinition verfügt über keine Imageversionen, die in der aktuellen Version enthalten und auch in der angegebenen Region vorhanden sind.  
**Problemumgehung:** Stellen Sie sicher, dass in der Region mindestens eine Imageversion vorhanden ist, bei der „Aus aktueller Version ausschließen“ auf „False“ festgelegt ist. 

**Meldung:** *Der Client besitzt die Berechtigung zum Durchführen der Aktion "Microsoft.Compute/galleries/images/versions/read" für den Bereich <resourceID\>. Der aktuelle Mandant <tenantId1\> ist jedoch nicht autorisiert, auf das verknüpfte Abonnement <subscriptionID\> zuzugreifen.*  
**Ursache:** Die VM oder Skalierungsgruppe wurde mit einem SIG-Image in einem anderen Mandanten erstellt. Sie haben versucht, eine Änderung an der VM oder Skalierungsgruppe vorzunehmen, aber Sie verfügen nicht über den Zugriff auf das Abonnement, zu dem das Image gehört.  
**Problemumgehung**: Bitten Sie den Besitzer des Abonnements der Imageversion, Ihnen Lesezugriff auf die Imageversion zu gewähren.

**Meldung:** *The gallery image <resourceID\> is not available in <region\> region. (Das Katalogimage <resourceID> ist in der Region <region> nicht verfügbar.) Bitten Sie den Besitzer des Images um eine Replikation in diese Region, oder ändern Sie die angeforderte Region.*  
**Ursache:** Die VM wird in einer Region erstellt, die nicht in der Liste der veröffentlichten Regionen für das Katalogimage aufgeführt ist.  
**Problemumgehung**: Replizieren Sie entweder das Image in die Region, oder erstellen Sie eine VM in einer der Regionen in den Veröffentlichungsregionen des Katalogimages.

**Meldung:** *Der Parameter "osProfile" ist nicht zulässig.*  
**Ursache:** Benutzername, Kennwort oder SSH-Schlüssel des Administrators wurden für eine VM bereitgestellt, die aus einer spezialisierten Imageversion erstellt wurde.  
**Problemumgehung**: Geben Sie nicht den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators an, wenn Sie beabsichtigen, eine VM aus diesem Image zu erstellen. Verwenden Sie andernfalls eine generalisierte Imageversion, und geben Sie den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators an.

**Meldung:** *Der erforderliche Parameter "osProfile" fehlt (NULL).*  
**Ursache:** Die VM wird aus einem generalisierten Image erstellt, und es fehlen Benutzername, Kennwort oder SSH-Schlüssel des Administrators. Da generalisierte Images nicht den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators enthalten, müssen diese Felder bei der Erstellung einer VM oder einer Skalierungsgruppe angegeben werden.  
**Problemumgehung**: Geben Sie den Benutzernamen, das Kennwort oder die SSH-Schlüssel des Administrators an, oder verwenden Sie eine spezialisierte Imageversion.

**Meldung:** *Cannot update Virtual Machine Scale Set <vmssName\> as the current OS state of the VM Scale Set is Generalized which is different from the updated gallery image OS state which is Specialized.* (Die VM-Skalierungsgruppe <vmssName> kann nicht aktualisiert werden, da der aktuelle Betriebssystemstatus der VM-Skalierungsgruppe "Generalisiert" ist und sich somit vom Betriebssystemstatus des aktualisierten Katalogimages unterscheidet, der "Spezialisiert" lautet.)  
**Ursache:** Das aktuelle Quellimage für die Skalierungsgruppe ist ein generalisiertes Quellimage, aber es wird mit einem spezialisierten Quellimage aktualisiert. Das aktuelle und das neue Quellimage für eine Skalierungsgruppe müssen den gleichen Status aufweisen.  
**Problemumgehung**: Verwenden Sie ein generalisierte Imageversion, um die Skalierungsgruppe zu aktualisieren.

**Meldung**: *Der Datenverschlüsselungssatz <diskEncryptionSetID\> in der Shared Image Gallery-Instanz <versionID\> gehört zum Abonnement <subscriptionID\_1\> und und kann nicht mit der Ressource '' im Abonnement <subscriptionID\_2\> verwendet werden*  
**Ursache:** Der zur Verschlüsselung der Imageversion verwendete Datenträgerverschlüsselungssatz befindet sich in einem anderen Abonnement als dem zum Hosten der Imageversion.  
**Problemumgehung**: Verwenden Sie dasselbe Abonnement für die Imageversion und den Datenträgerverschlüsselungssatz.

**Meldung:** *The VM or virtual machine scale set creation takes a long time.* (Das Erstellen der VM oder VM-Skalierungsgruppe dauert lange.)  
**Problemumgehung**: Überprüfen Sie, ob der **OSType** der Imageversion, aus der Sie die VM oder VM-Skalierungsgruppe erstellen möchten, derselbe **OSType** wie der des Quellimages ist, das Sie zum Erstellen der Imageversion verwendet haben. 

**Meldung**: *Die Ressource mit der ID <vmID\> hat ein/en anderen Plan ['{\"name\":\"<name>\",\"Verleger\":\"<publisher>\",\"Produkt\":\"<product>\",\"PromotionCode\":\"<promotionCode>\"}'] als der übergeordnete Gallery-Image-Plan ['null'].*  
**Ursache**: Die übergeordnete Imagedefinition für die bereitgestellte Imageversion verfügt nicht über eine Kaufplaninformation.  
**Problemumgehung**: Erstellen Sie eine Imagedefinition mit denselben Kaufplandetails aus der Fehlermeldung, und erstellen Sie die Imageversion innerhalb der Imagedefinition.


## <a name="creating-a-disk-from-an-image-version"></a>Erstellen eines Datenträgers aus einer Imageversion ##

**Meldung:** *Der Wert von Parameter imageReference ist ungültig.*  
**Ursache:** Sie haben einen Export aus einer SIG-Imageversion auf einen Datenträger versucht, aber eine LUN-Position verwendet, die im Image nicht vorhanden ist.    
**Problemumgehung**: Prüfen Sie die Imageversion hinsichtlich der verwendeten LUN-Positionen.

## <a name="sharing-resources"></a>Gemeinsame Nutzung von Ressourcen

Das Teilen von Ressourcen aus Imagekatalogen, Imagedefinitionen und Imageversionen zwischen Abonnements wird mithilfe der [rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md) aktiviert. 

## <a name="replication-speed"></a>Replikationsgeschwindigkeit

Verwenden Sie das Flag **--expand ReplicationStatus**, um zu überprüfen, ob die Replikation in alle angegebenen Zielregionen abgeschlossen wurde. Wenn nicht, warten Sie bis zu 6 Stunden auf den Abschluss des Auftrags. Falls dies fehlschlägt, lösen Sie den Befehl erneut aus, um die Imageversion zu erstellen und zu replizieren. Wenn es viele Zielregionen gibt, in die die Imageversion repliziert wird, erwägen Sie eine Replikation in mehreren Phasen.

## <a name="azure-limits-and-quotas"></a>Azure-Grenzwerte und -Kontingente 

[Azure-Grenzwerte und -Kontingente](../azure-resource-manager/management/azure-subscription-service-limits.md) gelten für alle Katalog mit geteilten Images-, Imagedefinitions- und Imageversionsressourcen. Stellen Sie sicher, dass Sie die Grenzwerte für Ihre Abonnements einhalten. 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Kataloge mit freigegebenen Images](./shared-image-galleries.md).
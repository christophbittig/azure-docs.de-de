---
title: Einrichten einer Aufbewahrungsrichtlinie
description: Erfahren Sie, wie Sie eine Aufbewahrungsrichtlinie konfigurieren, die Factory bereinigen und alte Images von DevTest Labs ausmustern.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4cb4a343aacda48791293507a95c4c9dfa0f5f47
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397659"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Einrichten von Aufbewahrungsrichtlinien in Azure DevTest Labs
In diesem Artikel wird das Festlegen einer Aufbewahrungsrichtlinie, das Bereinigen der Factory und das Ausmustern alter Images aus allen anderen DevTest Labs in der Organisation behandelt. 

## <a name="prerequisites"></a>Voraussetzungen
Befolgen Sie unbedingt diese Artikel, ehe Sie fortfahren:

- [Erstellen einer Imagefactory](image-factory-create.md)
- [Ausführen einer Image Factory aus AzureDevOps](image-factory-set-up-devops-lab.md)
- [Speichern von benutzerdefinierten Images und Verteilen an mehrere Labs](image-factory-save-distribute-custom-images.md)

Folgende Elemente sollten bereits vorhanden sein:

- Ein Lab für die Image Factory in Azure DevTest Labs
- Ein oder mehrere Azure DevTest Labs als Ziel, in denen die Factory Golden Images verteilt
- Ein Azure DevOps-Projekt zum Automatisieren der Image Factory
- Ein Speicherort für Quellcode mit den Skripts und der Konfiguration (in unserem Beispiel im selben zuvor verwendeten DevOps-Projekt)
- Eine Builddefinition, um Azure PowerShell-Aufgaben zu orchestrieren
 
## <a name="setting-the-retention-policy"></a>Festlegen der Aufbewahrungsrichtlinie
Bevor Sie die Bereinigungsschritte konfigurieren, legen Sie fest, wie viele frühere Images Sie in DevTest Labs beibehalten möchten. Wenn Sie die Anweisungen im Artikel [Run an image factory from Azure DevOps](image-factory-set-up-devops-lab.md) (Ausführen einer Image Factory in Azure DevOps) befolgt haben, haben Sie verschiedene Buildvariablen konfiguriert. Eine davon war **ImageRetention**. Sie legen diese Variable auf `1` fest, was bedeutet, dass in DevTest Labs-Instanzen kein Verlauf benutzerdefinierter Images gespeichert wird. Nur die letzten verteilten Images stehen zur Verfügung. Wenn Sie diese Variable in `2` ändern, werden das letzte verteilte Image sowie die vorherigen beibehalten. Sie können diesen Wert festlegen, um die Anzahl der Images im Verlauf festzulegen, die Sie in Ihren DevTest Labs-Instanzen speichern möchten.

## <a name="cleaning-up-the-factory"></a>Bereinigen der Factory
Der erste Schritt zur Bereinigung der Factory besteht darin, die Golden Image-VMs aus der Image Factory zu entfernen. Wie bei unseren vorherigen Skripts gibt es auch für diese Aufgabe ein Skript. Der erste Schritt ist das Hinzufügen einer weiteren **Azure PowerShell**-Aufgabe zur Builddefinition (siehe die folgende Abbildung):

![Screenshot eines PowerShell-Schritts.](./media/set-retention-policy-cleanup/powershell-step.png)

Sobald die neue Aufgabe in der Liste enthalten ist, wählen Sie das Element aus und füllen alle Details wie in der folgenden Abbildung gezeigt aus:

![Screenshot der PowerShell-Aufgabe „Alte Images bereinigen“.](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Die Skriptparameter sind: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Ausmustern alter Images 
Diese Aufgabe entfernt alle alten Images und behält nur einen Verlauf bei, der der der Buildvariablen **ImageRetention** entspricht. Fügen Sie unserer Builddefinition eine weitere **Azure PowerShell**-Buildaufgabe hinzu. Wählen Sie die hinzugefügte Aufgabe aus, und geben Sie die Details entsprechend der folgenden Abbildung ein: 

![Screenshot der PowerShell-Aufgabe „Alte Images ausmustern“.](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Die Skriptparameter sind: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Einstellen des Builds in die Warteschlange
Sobald die Builddefinition vollständig ist, stellen Sie einen neuen Build in die Warteschlange, um sicherzustellen, dass alles funktioniert. Nachdem der Build erfolgreich abgeschlossen wurde, werden die neuen benutzerdefinierten Images im Ziellab gezeigt. Wenn Sie das Image Factory-Lab überprüfen, sehen Sie keine bereitgestellten VMs. Wenn Sie weitere Builds in die Warteschlange stellen, sehen Sie, dass die Bereinigungsaufgaben alte benutzerdefinierte Images aus den DevTest Labs-Instanzen ausmustern. Die Ausmusterung stimmt mit dem in den Buildvariablen festgelegten Aufbewahrungswert überein.

> [!NOTE]
> Wenn Sie die Buildpipeline am Ende des letzten Artikels der Reihe ausgeführt haben, löschen Sie manuell die virtuellen Computer, die im Image Factory-Lab erstellt wurden, bevor Sie einen neuen Build in die Warteschlange stellen.  Der manuelle Bereinigungsschritt ist nur erforderlich, während Sie alles einrichten und die Funktionsfähigkeit überprüfen.



## <a name="summary"></a>Zusammenfassung
Sie haben nun eine aktive Image Factory, die bei Bedarf benutzerdefinierte Images generieren und an Ihre Labs verteilen kann. An dieser Stelle geht es nur darum, Ihre Images richtig einzurichten und die Ziellabs zu bestimmen. Wie im vorherigen Artikel erwähnt, gibt die Datei **Labs.json** in Ihrem Ordner **Configuration** an, welche Images in jedem der Ziellabs verfügbar gemacht werden sollen. Wenn Sie andere DevTest-Labs zu Ihrer Organisation hinzufügen, müssen Sie lediglich einen Eintrag in der Datei „Labs.json“ für das neue Lab hinzufügen.

Das Hinzufügen eines neuen Image zu Ihrer Factory ist ebenfalls einfach. Wenn Sie ein neues Image in Ihre Factory aufnehmen möchten, wechseln Sie über das [Azure-Portal](https://portal.azure.com) zu Ihrem Factorylab. Wählen Sie die Schaltfläche zum Hinzufügen einer VM und dann das Marketplace-Image und die gewünschten Artefakte aus. Anstatt die Schaltfläche **Erstellen** auszuwählen, um die neue VM zu erstellen, wählen Sie **Azure Resource Manager-Vorlage anzeigen** aus. Speichern Sie die Vorlage in Ihrem Repository im Ordner **GoldenImages** als JSON-Datei. Beim nächsten Ausführen der Image Factory wird Ihr benutzerdefiniertes Image erstellt.


## <a name="next-steps"></a>Nächste Schritte
1. [Planen Sie für Ihren Build/Ihr Release](/azure/devops/pipelines/build/triggers?tabs=designer) die regelmäßige Ausführung der Image Factory. Dadurch werden die von Ihrer Factory generierten Images regelmäßig aktualisiert.
2. Erstellen Sie weitere Golden Images für Ihre Factory. Sie können auch in Betracht ziehen, [Artefakte](devtest-lab-artifact-author.md) zu erstellen, um weitere Teile Ihrer VM-Einrichtungsaufgaben in ein Skript einzubinden und die Artefakte in Ihre Factory-Images aufzunehmen.
4. Erstellen Sie einen [separaten Build bzw. ein separates Release](/azure/devops/pipelines/overview), um das Skript **DistributeImages** getrennt auszuführen. Sie können dieses Skript ausführen, wenn Sie Änderungen an „Labs.json“ vornehmen und Images in Ziellabs kopieren lassen, ohne alle Images erneut erstellen zu müssen.

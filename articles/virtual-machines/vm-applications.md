---
title: Übersicht über Anwendungen mit virtuellen Computern (VMs) in der Azure Compute Gallery (Vorschau)
description: Erfahren Sie mehr über Anwendungspakete mit virtuellen Computern (VMs) in der Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 11/02/2021
ms.author: cynthn
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b43d2c5f20afa67c40115430903b97d49fef4e08
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478743"
---
# <a name="vm-applications-overview-preview"></a>Übersicht über Anwendungen mit virtuellen Computern (Vorschau)

Anwendungen mit virtuellen Computern sind ein Ressourcentyp in Azure Compute Gallery (ehemals Shared Image Gallery), der die Verwaltung, Freigabe und globale Verteilung von Anwendungen für Ihre virtuellen Computer vereinfacht.

> [!IMPORTANT]
> **Anwendungen mit virtuellen Computern in der Azure Compute Gallery** befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Sie können zwar ein Image eines virtuellen Computers mit vorinstallierten Anwendungen (Apps) erstellen, aber Sie müssten dann Ihr Image bei jeder Änderung der Anwendung aktualisieren. Das Trennen Ihrer Anwendungsinstallation von Ihren Images Ihres virtuellen Computers bedeutet, dass nicht für jede Änderung einer Codezeile ein neues Image veröffentlicht werden muss.

Anwendungspakete bieten Vorteile gegenüber anderen Bereitstellungs- und Paketerstellungsmethoden:

- Gruppierung und Versioning Ihrer Pakete

- Anwendungen mit virtuellen Computern können global repliziert werden, um näher an Ihrer Infrastruktur zu sein, sodass Sie AzCopy oder andere Speicherkopiermechanismen nicht verwenden müssen, um die Bits in andere Azure-Bereiche zu kopieren.

- Freigeben für andere Benutzer über die rollenbasierte Azure-Zugangskontrolle (Role Based Access Control, RBAC)

- Unterstützung für virtuelle Computer und flexible sowie einheitliche Skalierungseinstellungen

- Wenn Sie Netzwerksicherheitsgruppen-Regeln (NSG) auf Ihrem virtuellen Computer oder in den Skalierungseinstellungen angewendet haben, ist das Herunterladen der Pakete aus einem Internet-Repository möglicherweise nicht durchführbar. Und bei Speicherkonten würde das Herunterladen von Paketen auf gesperrte virtuelle Computer die Einrichtung privater Links erfordern.
- Anwendungen mit virtuellen Computern können mit der Richtlinie [DeployIfNotExists](/azure/governance/policy/concepts/effects) genutzt werden.


## <a name="what-are-vm-app-packages"></a>Was sind Anwendungs-Pakete für virtuelle Computer?

Anwendungspakete für virtuelle Computer nutzen mehrere Ressourcentypen:

| Resource | BESCHREIBUNG|
|----------|------------|
| **Azure Compute Gallery** | Eine Galerie ist ein Repository zum Verwalten und Freigeben von Anwendungspaketen. Benutzer können die Galerie-Ressource freigeben und alle untergeordneten Ressourcen werden dann automatisch freigegeben. Der Galerie-Name muss pro Abonnement anders ausfallen. Beispielsweise können Sie über eine Galerie verfügen, in der alle Ihre Betriebssystemimages gespeichert werden, und eine andere Galerie, in der alle Ihre Anwendungen für virtuelle Computer gespeichert werden.|
| **Anwendung für virtuelle Computer** | Dies ist die Definition von Ihrer Anwendung für virtuelle Computer. Dies ist eine *logische* Ressource, die die gemeinsamen Metadaten all der Versionen unter ihr speichert. Beispielsweise verfügen Sie möglicherweise über eine Anwendungsdefinition für Apache Tomcat und über mehrere Versionen in diesem Programm. |
| **Anwendungsversion für virtuelle Computer** | Dies ist die bereitstellbare Ressource. Sie können Ihre Anwendungsversionen für virtuelle Computer global in Zielregionen replizieren, die näher an der Infrastruktur Ihres virtuellen Computers liegen. Die Anwendungsversion des virtuellen Computers muss in eine Region repliziert werden, bevor sie auf einem virtuellen Computer in dieser Region bereitgestellt werden kann. |


## <a name="limitations"></a>Einschränkungen

- **Maximal drei Replikate pro Region**: Beim Erstellen einer Anwendungsversion für virtuelle Computer können maximal drei Replikate pro Region erstellt werden. 

- **Wiederholung fehlgeschlagener Installationen**: Derzeit besteht die einzige Möglichkeit zum Wiederholen einer fehlerhaften Installation darin, die Anwendung aus dem Profil zu entfernen, um sie dann wieder hinzuzufügen.

- **Nur fünf Anwendungen pro virtueller Computer**: Es dürfen nicht mehr als fünf Anwendungen zu einem beliebigen Zeitpunkt auf einem virtuellen Computer bereitgestellt werden.

- **1GB Anwendungsgröße**: Die maximale Dateigröße einer Anwendungsversion beträgt 1GB. 

- **Keine Garantie für Neustarts in Ihrem Skript**: Wenn Ihr Skript einen Neustart erfordert, wird empfohlen, diese Anwendung während der Bereitstellung zuletzt zu platzieren. Während der Code versucht, Neustarts zu verarbeiten, schlägt dies möglicherweise fehl.

- **Erfordert einen Agent für virtuelle Computer**: Der Agent für virtuelle Computer muss auf dem virtuellen Computer vorhanden sein und in der Lage sein, Zielzustände zu empfangen.

- **Mehrere Versionen derselben Anwendung auf demselben virtuellen Computer**: Sie können nicht über mehrere Versionen derselben Anwendung auf einem virtuellen Computer verfügen.


## <a name="cost"></a>Cost

Für die Verwendung von Anwendungspaketen für virtuelle Computer fallen keine zusätzlichen Gebühren an, Ihnen werden jedoch die folgenden Ressourcen in Rechnung gestellt:

- Speicherkosten für die Speicherung der einzelnen Pakete und Replikate. 
- Ausgehender Netzwerkdatenverkehr wird für die Replikation der ersten Imageversion aus der Quellregion in die replizierten Regionen berechnet. Weitere Replikate werden in der Region verarbeitet, sodass keine zusätzlichen Gebühren anfallen. 

Weitere Informationen zum ausgehenden Netzwerkdatenverkehr finden Sie unter [Bandbreite: Preisübersicht](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="vm-applications"></a>Anwendungen für virtuelle Computer

Die Anwendungsressource für virtuelle Computer definiert Folgendes zu Ihrer Anwendung für virtuelle Computer:

- Azure Compute Galerie, in der die Anwendung des virtuellen Computers gespeichert ist
- Name der Anwendung
- Unterstützte Betriebssysteme wie Linux oder Windows
- Eine Beschreibung der Anwendung für virtuelle Computer

## <a name="vm-application-versions"></a>Anwendungsversionen für virtuelle Computer

Anwendungsversionen für virtuelle Computer stellen eine bereitstellbare Ressource dar. Versionen werden mit den folgenden Eigenschaften definiert:
- Versionsnummer
- Link zur Datei des Anwendungspakets in einem Speicherkonto
- Installationszeichenfolge für die Installation der Anwendung (App)
- Entfernen einer Zeichenfolge, um zu zeigen, wie die Anwendung ordnungsgemäß entfernt wird
- Paketdateiname, der beim Herunterladen auf den virtuellen Computer verwendet werden soll
- Konfigurationsdateiname, der zum Konfigurieren der Anwendung auf dem virtuellen Computer verwendet werden soll
- Link zur Konfigurationsdatei für die Anwendung des virtuellen Computers
- Aktualisieren der Zeichenfolge zum Aktualisieren der Anwendung für virtuelle Computer auf eine neuere Version
- Ende der Lebensdauer. Datumsangaben zum Ende der Lebensdauer dienen lediglich der Information. Sie können weiterhin Versionen einer Anwendung für virtuelle Computer bereitstellen, die über das Enddatum der Lebensdauer liegen.
- Aus aktueller Version ausschließen. Sie können eine Version ausschließen, die nicht als neuste Version der Anwendung verwendet werden soll. 
- Zielregionen für die Replizierung
- Replikatanzahl pro Region

## <a name="download-directory"></a>Herunterladen des Verzeichnisses 
 
Der Downloadspeicherort des Anwendungspakets und der Konfigurationsdateien sind:
  
- Linux: `/var/lib/waagent/Microsoft.CPlat.Core.VMApplicationManagerLinux/<appname>/<app version> `
- Windows: `C:\Packages\Plugins\Microsoft.CPlat.Core.VMApplicationManagerWindows\1.0.4\Downloads\<appname>\<app version> `


Die Befehle zum Installieren / Aktualisieren / Entfernen sollten geschrieben werden, vorausgesetzt, das Anwendungspaket und die Konfigurationsdatei befinden sich im aktuellen Verzeichnis.

## <a name="file-naming"></a>Benennen der Datei

Wenn die Anwendungsdatei während der Vorschau auf den virtuellen Computer heruntergeladen wird, entspricht der Dateiname dem Namen, den Sie beim Erstellen der Anwendung für virtuelle Computer verwenden. Wenn Sie beispielsweise Ihre Anwendung für virtuelle Computer `myApp` benennen, erhält die Datei, die auf den virtuellen Computer heruntergeladen wird, auch den Namen `myApp`, unabhängig davon, welcher Dateiname im Speicherkonto verwendet wird. Wenn Ihre Anwendung für virtuelle Computer auch über eine Konfigurationsdatei verfügt, dann ist diese Datei der Name der Anwendung mit angefügter `_config`. Wenn `myApp` über eine Konfigurationsdatei verfügt, erhält sie den Namen `myApp_config`.

Wenn Sie beispielsweise Ihre Anwendung für virtuelle Computer `myApp` benennen, wenn Sie sie in der Galerie erstellen, sie aber als `myApplication.exe` im Speicherkonto gespeichert wird, lautet der Dateiname `myApp` beim Herunterladen auf den virtuellen Computer. Ihre Installationszeichenfolge sollte beginnen, indem Sie die Datei so umbenennen, dass sie dem entspricht, was Sie auf dem virtuellen Computer ausführen müssen (z. B. myApp.exe).

Die Befehle zum Installieren, Aktualisieren und Entfernen müssen unter Berücksichtigung dieser Sache geschrieben werden. 
 
## <a name="command-interpreter"></a>Kommandointerpreter  

Die Standardkommandointerpreter sind:
- Linux: `/bin/sh` 
- Windows: `cmd.exe`

Es ist möglich, einen anderen Interpreter zu verwenden, solange er auf dem Computer installiert ist, indem Sie die ausführbare Datei aufrufen und den Befehl an ihn übergeben. Damit Ihr Befehl beispielsweise in PowerShell auf Windows anstelle von cmd ausgeführt wird, können Sie `powershell.exe -Command '<powershell commmand>'` übergeben.
  

## <a name="how-updates-are-handled"></a>Wie mit Updates verfahren wird.

Wenn Sie eine Anwendungsversion aktualisieren, wird der Updatebefehl verwendet, den Sie während der Bereitstellung angegeben haben. Wenn die aktualisierte Version keinen Updatebefehl enthält, wird die aktuelle Version entfernt und die neue Version wird installiert. 

Updatebefehle sollten mit dem Hintergedanken geschrieben werden, dass sie von einer älteren Version der Anwendung für virtuelle Computer aktualisiert werden können.


## <a name="tips-for-creating-vm-applications-on-linux"></a>Tipps zum Erstellen von Anwendungen für virtuelle Computer bei Linux 

Drittanbieteranwendungen für Linux können auf verschiedene Weise gepackt werden. Im Folgenden erfahren Sie, wie Sie die Erstellung der Installationsbefehle für einige der gängigsten Befehle handhaben.

### <a name="tar-and-gz-files"></a>.tar- und .gz-Dateien 

Hierbei handelt es sich um komprimierte Archive, die einfach an einem gewünschten Speicherort extrahiert werden können. Überprüfen Sie die Installationsanweisungen für das ursprüngliche Paket, falls sie an einen bestimmten Speicherort extrahiert werden müssen. Wenn die .tar.gz-Datei Quellcode enthält, lesen Sie die Anweisungen für das Paket, um zu erfahren, wie Sie sie aus der Quelle installieren. 

Beispiel zum Installieren eines Befehls, um auf einem Linuxcomputer `golang` zu installieren:

```bash
tar -C /usr/local -xzf go_linux
```

Beispiel für einen Befehl zum Entfernen:

```bash
rm -rf /usr/local/go
```

### <a name="deb-rpm-and-other-platform-specific-packages"></a>.deb-, .rpm- und andere plattformspezifische Pakete 
Sie können einzelne Pakete für plattformspezifische Paket-Manager herunterladen, aber sie enthalten in der Regel nicht alle Abhängigkeiten. Für diese Dateien müssen Sie auch alle Abhängigkeiten in das Anwendungspaket beifügen oder den Systempaket-Manager die Abhängigkeiten über die Repositorys herunterladen lassen, die für den virtuellen Computer verfügbar sind. Wenn Sie mit einem virtuellen Computer mit eingeschränktem Internetzugriff arbeiten, müssen Sie alle Abhängigkeiten selbst packen.


Die Ermittlung der Abhängigkeiten kann etwas schwierig sein. Es gibt Tools von Drittanbietern, mit denen Sie die gesamte Abhängigkeitsstruktur anzeigen können. 

Bei Ubuntu können Sie `apt-get install <name> --simulate` ausführen, um all die Pakete, die für den Befehl `apt-get install <name>` installiert werden, anzeigen zu lassen. Anschließend können Sie diese Ausgabe verwenden, um alle .deb-Dateien herunterzuladen und ein Archiv zu erstellen, das als Anwendungspaket verwendet werden kann. Der Nachteil dieser Methode ist, dass nicht die Abhängigkeiten angezeigt werden, die bereits auf dem virtuellen Computer installiert sind.
 
Um beispielsweise ein Anwendungspaket für einen virtuellen Computer zum Installieren von PowerShell für Ubuntu zu erstellen, führen Sie den Befehl `apt-get install powershell --simulate` auf einem neuen virtuellen Ubuntu-Computer aus. Überprüfen Sie die Ausgabe der Zeile **Die folgenden NEUEN Pakete werden installiert**, welche die folgenden Pakete auflistet:
- `liblttng-ust-ctl4` 
- `liblttng-ust0` 
- `liburcu6` 
- `powershell`. 

Laden Sie diese Dateien mit `apt-get download` herunter und erstellen Sie auf der Root-Ebene ein tar-Archiv mit allen Dateien. Dieses tar-Archiv ist die Anwendungspaketdatei. Der Installationsbefehl lautet in diesem Fall:

```bash
tar -xf powershell && dpkg -i ./liblttng-ust-ctl4_2.10.1-1_amd64.deb ./liburcu6_0.10.1-1ubuntu1_amd64.deb ./liblttng-ust0_2.10.1-1_amd64.deb ./powershell_7.1.2-1.ubuntu.18.04_amd64.deb
```

Und der Entfernen-Befehl lautet:

```bash
dpkg -r powershell && apt autoremove
```

Verwenden Sie `apt autoremove`, anstatt umständlich zu versuchen, alle Abhängigkeiten zu entfernen. Möglicherweise haben Sie andere Anwendungen mit überlappenden Abhängigkeiten installiert. In diesem Fall würde ein expliziter Entfernen-Befehl fehlschlagen.


Falls Sie die Abhängigkeiten nicht selbst auflösen möchten und apt/rpm eine Verbindung mit den Repositorys herstellen kann, können Sie eine Anwendung mit nur einer .deb- / .rpm-Datei installieren und apt / rpm die Abhängigkeiten verarbeiten lassen.

Beispiel für einen Befehl zum Installieren:

```bash
dpkg -i <appname> || apt --fix-broken install -y
```
 
## <a name="tips-for-creating-vm-applications-on-windows"></a>Tipps zum Erstellen von Anwendungen für virtuelle Computer bei Windows 

Die meisten Anwendungen von Drittanbietern bei Windows sind als .exe- oder .msi-Installationsprogramme verfügbar. Einige sind auch zum Extrahieren und Ausführen von ZIP-Dateien verfügbar. Sehen wir uns die bewährten Methoden für jede dieser Methoden an.


### <a name="exe-installer"></a>.exe-Installationsprogramm 

Ausführbare Installationsprogramme starten in der Regel eine Benutzeroberfläche und erfordern, dass jemand auf die Benutzeroberfläche klickt. Wenn das Installationsprogramm einen Parameter Automatik-Modus (Silent Mode) unterstützt, sollte er in der Installationszeichenfolge enthalten sein. 

Cmd.exe geht auch davon aus, dass ausführbare Dateien die Erweiterung .exe aufweisen, daher müssen Sie die Datei umbenennen, damit sie über die Erweiterung te .exe verfügt.  

Wenn Sie ein Anwendungspaket für virtuelle Computer für myApp.exe erstellen möchten, das als ausführbare Datei versendet wird, heißt Ihre Anwendung für virtuelle Computer "myApp". Daher schreiben Sie den Befehl unter der Voraussetzung, dass sich das Anwendungspaket im aktuellen Verzeichnis befindet:

```
"move .\\myApp .\\myApp.exe & myApp.exe /S -config myApp_config" 
```
 
Wenn die ausführbare Datei des Installationsprogramms keinen Deinstallations-Parameter unterstützt, können Sie manchmal die Registrierung auf einem Testcomputer nachschauen, um zu erfahren, wo sich das Deinstallationsprogramm befindet. 

In der Registrierung wird die Deinstallationszeichenfolge in `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\<installed application name>\UninstallString` gespeichert, sodass Sie die Inhalte als Ihren Entfernen-Befehl verwenden können:

```
'\"C:\\Program Files\\myApp\\uninstall\\helper.exe\" /S'
```

### <a name="msi-installer"></a>.msi-Installationsprogramm 

Für die Befehlszeilenausführung der Installationsprogramme `.msi` sollten die Befehle `msiexec` zum Installieren oder Entfernen einer Anwendung verwendet werden. In der Regel wird `msiexec` als eigener separater Prozess ausgeführt und `cmd` wartet nicht auf dessen Abschluss, was zu Problemen bei der Installation von mehr als einer Anwendung für virtuelle Computer führen kann.  Der Befehl `start` kann zusammen mit `msiexec` verwendet werden, um sicherzugehen, dass die Installation zum Abschluss gebracht wird, bevor der Befehl zurückkehrt. Beispiel:

```
start /wait %windir%\\system32\\msiexec.exe /i myapp /quiet /forcerestart /log myapp_install.log
```

Beispiel für einen Befehl zum Entfernen:

```
start /wait %windir%\\system32\\msiexec.exe /x $appname /quiet /forcerestart /log ${appname}_uninstall.log
```

### <a name="zipped-files"></a>Zip-Dateien 

Bei zip-Dateien entzippen Sie einfach den Inhalt des Anwendungspakets in das gewünschte Ziel. 

Beispiel für einen Befehl zum Installieren:

```
mkdir C:\\myapp && powershell.exe -Command \"Expand-Archive -Path myapp -DestinationPath C:\\myapp\" 
```

Beispiel für einen Befehl zum Entfernen:

```
rmdir /S /Q C:\\myapp
```


## <a name="troubleshooting-during-preview"></a>Problembehandlung während der Vorschau

Während der Vorschau gibt die Anwendungserweiterung für virtuelle Computer immer eine Erfolgsmeldung zurück, unabhängig davon, ob eine Anwendung für virtuelle Computer während der Installation / Aktualisierung / Entfernung fehlgeschlagen ist. Die Anwendungserweiterung für virtuelle Computer gibt den Erweiterungsstatus nur dann als Fehler an, wenn ein Problem mit der Erweiterung oder der zugrundeliegenden Infrastruktur vorliegt. Bitte überprüfen Sie die Meldung der Anwendungserweiterung für virtuelle Computer, um zu erfahren, ob eine bestimmte Anwendung für virtuelle Computer erfolgreich zur Instanz für virtuelle Computer hinzugefügt wurde.

Weitere Informationen zum Abrufen des Status von Erweiterungen für virtuelle Computer finden Sie unter [Erweiterungen und Funktionen für virtuelle Computer bei Windows](extensions/features-windows.md#view-extension-status).

Um den Status für die Erweiterungen für virtuelle Computer abzurufen, verwenden Sie [Get-AzVM](/powershell/module/az.compute/get-azvm):

```azurepowershell-interactive
Get-AzVM -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

Um den Status für die SS-Erweiterungen für virtuelle Computer abzurufen, verwenden Sie [Get-AzVMSS](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

## <a name="error-messages"></a>Fehlermeldungen

| `Message` | BESCHREIBUNG |
|--|--|
| Die aktuelle Anwendungsversion für virtuelle Computer {Name} wurde am {Datum} als veraltet erklärt. | Sie haben versucht, eine Anwendungsversion für virtuelle Computer bereitzustellen, die bereits veraltet ist. Versuchen Sie, `latest` zu verwenden, anstatt eine bestimmte Version anzugeben. |
| Die aktuelle Anwendungsversion für virtuelle Computer {Name} unterstützt das Betriebssystem {Betriebssystem}, während das aktuelle Disk-Betriebssystem {Betriebssystem} ist. | Sie haben versucht, eine Linux-Anwendung auf einer Windows-Instanz oder umgekehrt bereitzustellen. |
| Die maximale Anzahl von Anwendungen für virtuelle Computer (max=5, current={count}) wurde überschritten. Verwenden Sie weniger Anwendungen und wiederholen Sie die Anforderung. | Derzeit werden nur fünf Anwendungen für virtuelle Computer pro virtueller Computer oder pro virtueller SS-Computer unterstützt. |
| Es wurde mehr als eine Anwendung für virtuelle Computer mit derselben Paket-Referenz-ID angegeben. | Dieselbe Anwendung wurde mehr als einmal angegeben. |
| Das Abonnement ist nicht für den Zugriff auf dieses Image autorisiert. | Das Abonnement hat keinen Zugriff auf diese Anwendungsversion. |
| Das Speicher-Konto in den Argumenten ist nicht vorhanden. | Es liegen keine Anwendungen für dieses Abonnement vor. |
| Das Plattformimage {image} ist nicht verfügbar. Überprüfen Sie, ob alle Felder im Speicherprofil richtig sind. Weitere Informationen zu Speicherprofil-Informationen finden Sie unter https://aka.ms/storageprofile. | Die Anwendung ist nicht vorhanden. |
| Das Galerieimage {Image} ist in der Region {Region} nicht verfügbar. Bitten Sie den Besitzer des Images um eine Replikation in diese Region oder ändern Sie die angeforderte Region. | Die Galerie-Anwendungsversion ist vorhanden, wurde aber nicht in diese Region repliziert. |
| Die SAS ist für den Quell-URI {uri} ungültig. | Beim Abrufen von Informationen über die URL (mediaLink oder defaultConfigurationLink) wurde vom Speicher ein Fehler `Forbidden` empfangen. |
| Das Blob, auf das vom Quell-URI {uri} verwiesen wird, ist nicht vorhanden. | Das für die MediaLink- oder defaultConfigurationLink-Eigenschaften bereitgestellte Blob ist nicht vorhanden. |
| Auf die URL der Galerie-Anwendungsversion {url} kann aufgrund des folgenden Fehlers nicht zugegriffen werden: Remotename nicht gefunden. Stellen Sie sicher, dass das Blob vorhanden und öffentlich zugänglich ist oder eine SAS-URL mit Leseberechtigungen ist. | Der wahrscheinlichste Fall ist, dass kein SAS-URI mit Leseberechtigungen bereitgestellt wurde. |
| Auf die URL der Galerie-Anwendungsversionnwendungsversion {url} kann aufgrund des folgenden Fehlers nicht zugegriffen werden: {Fehlerbeschreibung}. Stellen Sie sicher, dass das Blob vorhanden und öffentlich zugänglich ist oder eine SAS-URL mit Leseberechtigungen ist. | Es gibt ein Problem mit dem bereitgestellten Speicher-Blob. Die Fehlerbeschreibung liefert mehr Informationen dazu. |
| Der Vorgang {Name des Vorgangs} ist für {Anwendung} nicht zulässig, da er zum Löschen markiert ist. Sie können nur versuchen, den Löschvorgang zu wiederholen (oder warten Sie auf den Abschluss eines laufenden Vorgangs). | Versuchen Sie, eine Anwendung zu aktualisieren, die gerade gelöscht wird. |
| Der Wert {Wert} des Parameters "galleryApplicationVersion.properties.publishingProfile.replicaCount" liegt außerhalb des zulässigen Bereichs. Der Wert muss zwischen 1 und 3 (einschließlich) liegen. | Nur zwischen 1 und 3 sind Replikate für Anwendungs-Versionen für virtuelle Computer zulässig. |
| Das Ändern der Eigenschaft "galleryApplicationVersion.properties.publishingProfile.manageActions.install" ist nicht zulässig. (oder Aktualisieren, Löschen) | Es ist nicht möglich, eine der Aktionen zum Verwalten auf einer vorhandenen Anwendung für virtuelle Computer zu ändern. Es muss eine neue Anwendungs-Version für virtuelle Computer erstellt werden. |
| Das Ändern der Eigenschaft "galleryApplicationVersion.properties.publishingProfile.settings.packageFileName" ist nicht zulässig. (oder configFileName) (Name der Konfigurationsdatei) | Es ist nicht möglich, eine der Einstellungen wie z. B. den Paketdateinamen oder den Namen der Konfigurationsdatei zu ändern. Es muss eine neue Anwendungs-Version für virtuelle Computer erstellt werden. |
| Das Blob, auf das vom Quell-URI {uri} verwiesen wird, ist zu groß: Größe = {Größe}. Die maximal zulässige Blobgröße beträgt "1 GB". | Die maximale Größe für ein Blob, auf das von mediaLink oder defaultConfigurationLink verwiesen wird, beträgt derzeit 1 GB. |
| Das Blob, auf das vom Quell-URI {uri} verwiesen wird, ist nicht vorhanden. | Es wurde auf ein leeres Blob verwiesen. |
| Der Blobtyp {type} wird vom Vorgang {operation} nicht unterstützt. Es werden nur Seitenblobs und Blockblobs unterstützt. | Die Anwendungen für virtuelle Computer unterstützen nur Seitenblobs und Blockblobs. |
| Die SAS ist für den Quell-URI {uri} ungültig. | Der für mediaLink oder defaultConfigurationLink angegebene SAS-URI ist kein gültiger SAS-URI. |
| {region} kann in Zielregionen nicht angegeben werden, da im Abonnement das erforderliche Feature (Funktion) {FeatureName} fehlt. Registrieren Sie entweder Ihr Abonnement mit dem erforderlichen Feature oder entfernen Sie die Region aus der Liste der Zielregionen. | Um Anwendungen für virtuelle Computer in bestimmten eingeschränkten Regionen verwenden zu können, muss das Feature Flag für dieses Abonnement registriert sein. |
| Galerie-Image-Versionen, die Profilregionen {Regionen} veröffentlichen, müssen den Speicherort der Image-Version {Speicherort} enthalten. | Die Liste der Regionen für die Replizierung muss den Speicherort enthalten, an dem sich die Anwendungsversion befindet. |
| Duplizierten Regionen sind in Zielveröffentlichungsregionen nicht zulässig. | Die Veröffentlichungsregionen verfügen möglicherweise nicht über Duplikate. |
| Ressourcen von Galerie-Anwendungsversionen unterstützen derzeit keine Verschlüsselung. | Die Verschlüsselungseigenschaft für Zielregionen wird für Anwendungen für virtuelle Computer nicht unterstützt. |
| Der Entity-Name ist nicht mit dem Namen in der Anforderungs-URL übereinstimmend. | Die in der Anforderungs-URL angegebene Version der Galerie-Anwendung ist nicht mit der in der Anforderung angegebenen Version übereinstimmend. |
| Der Name der Version der Galerie-Anwendung ist ungültig. Der Name der Anwendungsversion sollte dem Format Major(int32).Minor(int32).Patch(int32) folgen, wobei int zwischen 0 und 2.147.483.647 (einschließlich) liegt. Beispiel: 1.0.0, 2018.12.1 usw. | Die Version der Galerie-Anwendung muss dem angegebenen Format folgen. |



## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Bereitstellen von Anwendungspaketen für virtuelle Computer](vm-applications-how-to.md).



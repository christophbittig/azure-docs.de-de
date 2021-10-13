---
title: Beheben von Fehlern mit Azure NetApp Files-Ressourcenanbietern | Microsoft-Dokumentation
description: Beschreibt Ursachen, Lösungen und Problemumgehungen für häufige Fehler bei Azure NetApp Files-Ressourcenanbietern.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: 2a3e97096109ce89f481b2abca2dc7b5a3a237e6
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407872"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Beheben von Fehlern mit Azure NetApp Files-Ressourcenanbietern 

In diesem Artikel werden häufige Fehler von Azure NetApp Files-Ressourcenanbietern sowie die zugehörigen Ursachen, Lösungen und Problemumgehungen beschrieben (sofern vorhanden).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Häufige Fehler mit Azure NetApp Files-Ressourcenanbietern

***Die Erstellung von `netAppAccounts` wurde in dieser Region eingeschränkt.***

Diese Situation tritt auf, wenn der Benutzer versucht, ein NetApp-Konto zu erstellen.

* Ursache:   
Der Azure-Ressourcenanbieter für Azure NetApp Files wurde nicht erfolgreich registriert. 
 
* Lösung:   
Führen Sie alle Schritte aus, die in [Registrieren des NetApp-Ressourcenanbieters](azure-netapp-files-register.md) beschrieben werden.

***„BaremetalTenantId“ kann nicht geändert werden.***  

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume zu aktualisieren oder zu patchen, und die `BaremetalTenantId`-Eigenschaft einen geänderten Wert aufweist.

* Ursache:   
Sie versuchen, ein Volume zu aktualisieren, und die `BaremetalTenantId`-Eigenschaft weist einen anderen Wert als den in Azure gespeicherten Wert auf.
* Lösung:   
`BaremetalTenantId` nicht in den Patch und die Update (Put)-Anforderung aufnehmen. Alternativ können Sie auch sicherstellen, dass `BaremetalTenantId` in der Anforderung identisch ist.

***Servicelevel kann nicht geändert werden.***  

Dieser Fehler tritt beim Versuch auf, einen Kapazitätspool mit einem anderen Servicelevel zu aktualisieren oder zu patchen, wenn der Kapazitätspool bereits Volumes enthält.

* Ursache:   
Sie versuchen, einen Kapazitätspool-Servicelevel zu aktualisieren, wenn der Pool Volumes enthält.
* Lösung:   
Löschen Sie alle Volumes aus dem Kapazitätspool, und ändern Sie dann den Servicelevel.
* Problemumgehung:   
Legen Sie einen weiteren Kapazitätspool an, und erstellen Sie die Volumes dann im neuen Kapazitätspool neu.

***PoolId kann nicht geändert werden.***  

Dieser Fehler tritt auf, wenn Sie versuchen, einen Kapazitätspool aktualisieren oder zu patchen, und die `PoolId`-Eigenschaft einen geänderten Wert aufweist.

* Ursache:   
Sie versuchen, eine `PoolId`-Eigenschaft für den Kapazitätspool zu aktualisieren. Die `PoolId`-Eigenschaft ist eine schreibgeschützte Eigenschaft und kann nicht geändert werden.
* Lösung:   
`PoolId` nicht in den Patch und die Update (Put)-Anforderung aufnehmen.  Alternativ können Sie auch sicherstellen, dass `PoolId` in der Anforderung identisch ist.

***Erstellungstoken kann nicht geändert werden.***

Dieser Fehler tritt beim Versuch auf, den Dateipfad (`CreationToken`) zu ändern, nachdem das Volume erstellt wurde. Der Dateipfad (`CreationToken`) muss beim Erstellen des Volumes festgelegt werden und kann später nicht mehr geändert werden.

* Ursache:   
Sie versuchen, den Dateipfad (`CreationToken`) nach dem Erstellen des Volumes zu ändern. Dies ist kein unterstützter Vorgang. 
* Lösung:   
Falls Sie den Dateipfad nicht unbedingt ändern müssen, können Sie den Parameter aus der Anforderung entfernen, um die Fehlermeldung zu schließen.
* Problemumgehung:   
Wenn Sie den Dateipfad (`CreationToken`) ändern müssen, können Sie ein neues Volume mit einem neuen Dateipfad erstellen und die Daten dann zum neuen Volume migrieren.

***Erstellungstoken muss mindestens 16 Zeichen umfassen.***

Dieser Fehler tritt auf, wenn der Dateipfad (`CreationToken`) nicht der erforderlichen Länge entspricht. Der Dateipfad muss mindestens ein Zeichen lang sein.

* Ursache:   
Der Dateipfad ist leer.  Wenn Sie ein Volume über die API erstellen, ist ein Erstellungstoken erforderlich. Wenn Sie das Azure-Portal verwenden, wird der Dateipfad automatisch generiert.
* Lösung:   
Geben Sie mindestens ein Zeichen als Dateipfad an (`CreationToken`).

***Der Domänenname kann nicht geändert werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, den Domänennamen in Active Directory zu ändern.

* Ursache:   
Sie versuchen, die Domänennameneigenschaft zu aktualisieren.
* Lösung:    
Keine. Sie können den Domänennamen nicht ändern.
* Problemumgehung:   
Löschen Sie alle Volumes mithilfe der Active Directory-Konfiguration. Löschen Sie dann die Active Directory-Konfiguration, und erstellen Sie die Volumes neu.

***Fehler bei doppeltem Wert für das Objekt „ExportPolicy.Rules[RuleIndex]“.***

Dieser Fehler tritt auf, wenn die Exportrichtlinie nicht mit einem eindeutigen Index definiert ist. Wenn Sie Exportrichtlinien definieren, müssen alle Exportrichtlinienregeln einen eindeutigen Index zwischen 1 und 5 aufweisen.

* Ursache:   
Die definierte Exportrichtlinie erfüllt nicht die Anforderungen an die Exportrichtlinienregeln. Sie müssen mindestens über eine und dürfen höchstens über fünf Exportrichtlinienregeln verfügen.
* Lösung:   
Stellen Sie sicher, dass der Index nicht bereits verwendet wird und im Bereich von 1 bis 5 liegt.
* Problemumgehung:   
Verwenden Sie einen anderen Index für die Regel, die Sie festzulegen versuchen.

***Fehler {action} {resourceTypeName}***

Dieser Fehler wird angezeigt, wenn andere Fehlerbehandlungen den Fehler während der Ausführung einer Aktion auf einer Ressource nicht beseitigen konnten.   Er enthält das Wort „Fehler“. `{action}` kann entweder `getting`, `creating`, `updating` oder `deleting` sein.  `{resourceTypeName}` ist `resourceTypeName` (z. B. `netAppAccount`, `capacityPool`, `volume` usw.).

* Ursache:   
Dieser Fehler ist ein Ausnahmefehler, bei dem die Ursache nicht bekannt ist.
* Lösung:   
Wenden Sie sich an das Azure-Supportcenter, um den genauen Grund zu melden, der in den Protokollen angegeben ist.
* Problemumgehung:   
Keine.

***Der Dateipfadname darf nur Buchstaben, Zahlen und Bindestriche („-“) enthalten.***

Dieser Fehler tritt auf, wenn der Dateipfad nicht unterstützte Zeichen enthält, z. B. einen Punkt („.“), ein Komma („,“), einen Unterstrich („_“) oder ein Dollarzeichen („$“).

* Ursache:   
Dieser Dateipfad enthält nicht unterstützte Zeichen, z. B. einen Punkt („.“), ein Komma („,“), einen Unterstrich („_“) oder ein Dollarzeichen („$“).
* Lösung:   
Entfernen Sie Zeichen, die keine alphabetischen Buchstaben, Zahlen oder Bindestriche („-“) sind, aus dem von Ihnen angegebenen Dateipfad.
* Problemumgehung:   
Sie können einen Unterstrich durch einen Bindestrich ersetzen oder Großbuchstaben anstelle von Leerzeichen verwenden, um den Anfang neuer Wörter anzuzeigen.  Verwenden Sie beispielsweise „NewVolume“ anstelle von „new volume“.

***FileSystemId kann nicht geändert werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, `FileSystemId` zu ändern.  Das Ändern von `FileSystemdId` ist kein unterstützter Vorgang. 

* Ursache:   
Die ID des Dateisystems wird beim Erstellen des Volumes festgelegt. `FileSystemId` kann nachträglich nicht mehr geändert werden.
* Lösung:   
`FileSystemId` nicht in einen Patch und die Update (Put)-Anforderung aufnehmen.  Alternativ können Sie auch sicherstellen, dass `FileSystemId` in der Anforderung identisch ist.

***ActiveDirectory mit der ID „{string}“ ist nicht vorhanden.***

Der `{string}`-Teil ist der Wert, den Sie in der `ActiveDirectoryId`-Eigenschaft für die Active Directory-Verbindung eingegeben haben.

* Ursache:   
Wenn Sie ein Konto mit der Active Directory-Konfiguration erstellt haben, haben Sie einen Wert für `ActiveDirectoryId` eingegeben, der leer sein soll.
* Lösung:   
`ActiveDirectoryId` nicht in die Create (Put)-Anforderung aufnehmen.

***Ungültige API-Version.***

Die API-Version wurde entweder nicht übermittelt oder enthält einen ungültigen Wert.

* Ursache:   
Der Abfrageparameter `api-version` enthält einen ungültigen Wert.
* Lösung:   
Verwenden Sie den richtigen Wert für die API-Version.  Der Ressourcenanbieter unterstützt viele API-Versionen. Der Wert hat das Format yyyy-mm-dd.

***Ein ungültiger Wert „{value}“ wurde für {1} empfangen.***

Diese Meldung zeigt einen Fehler in den Feldern für `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv3` und `Nfsv4` an.

* Ursache:   
Bei mindestens einem der folgenden Felder ist bei der Eingabeüberprüfungsanforderung ein Fehler aufgetreten: `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite`, `Nfsv`3 und `Nfsv4`.
* Lösung:   
Stellen Sie sicher, dass Sie alle erforderlichen Parameter über die Befehlszeile festlegen und keine Konflikte vorhanden sind. Sie können beispielsweise nicht gleichzeitig die Parameter `UnixReadOnly` und `UnixReadWrite` festlegen.
* Problemumgehung:   
Weitere Informationen finden Sie oben unter „Lösung“.

***IP-Bereich{0} bis {1} für vlan {2} wird bereits verwendet.***

Dieser Fehler tritt auf, weil für die internen Einträge der verwendeten IP-Bereiche ein Konflikt mit der neu zugewiesenen IP-Adresse besteht.

* Ursache:   
Die für die Volumeerstellung zugewiesene IP-Adresse ist bereits registriert.
Der Grund dafür könnte eine zuvor fehlgeschlagene Volumeerstellung sein.
* Lösung:   
Wenden Sie sich an das Azure-Supportcenter.

***Fehlender Wert für „{property}“.***

Dieser Fehler zeigt an, dass in der Anforderung eine erforderliche Eigenschaft fehlt. Die Zeichenfolge „{property}“ enthält den Namen der fehlenden Eigenschaft.

* Ursache:   
Bei mindestens einer Eigenschaften ist bei der Eingabeüberprüfungsanforderung ein Fehler aufgetreten.
* Lösung:   
Achten Sie darauf, dass Sie alle erforderlichen Eigenschaften in der Anforderung festlegen und dass keine Konflikte vorhanden sind – insbesondere für die Eigenschaft aus der Fehlermeldung.

***MountTargets kann nicht geändert werden.***

Dieser Fehler tritt auf, wenn ein Benutzer versucht, die „MountTargets“-Eigenschaft des Volumes zu aktualisieren oder zu patchen.

* Ursache:   
Sie versuchen, die `MountTargets`-Eigenschaft des Volumes zu aktualisieren. Die Änderung dieser Eigenschaft wird nicht unterstützt.
* Lösung:   
`MountTargets` nicht in einen Patch und die Update (Put)-Anforderung aufnehmen.  Alternativ können Sie auch sicherstellen, dass `MountTargets` in der Anforderung identisch ist.

***Name wird bereits verwendet.***

Dieser Fehler zeigt an, dass der Name der Ressource bereits verwendet wird.

* Ursache:   
Sie versuchen, eine Ressource mit einem Namen zu erstellen, der bereits für eine vorhandene Ressource verwendet wird.
* Lösung:   
Verwenden Sie beim Erstellen einer Ressource einen eindeutigen Namen.

***Der Dateipfad wird bereits verwendet.***

Dieser Fehler zeigt an, dass der Dateipfad für das Volume bereits verwendet wird.

* Ursache:   
Sie versuchen, ein Volume mit einem Dateipfad zu erstellen, der mit einem vorhandenen Volume identisch ist.
* Lösung:   
Verwenden Sie beim Erstellen des Volumes einen eindeutigen Dateipfad.

***Name ist zu lang.***

Dieser Fehler zeigt an, dass für den Ressourcennamen nicht die erforderliche maximale Länge eingehalten wird.

* Ursache:   
Der Ressourcenname ist zu lang.
* Lösung:   
Geben Sie einen kürzeren Namen für die Ressource ein.

***Dateipfad ist zu lang.***

Dieser Fehler zeigt an, dass für den Dateipfad des Volumes nicht die erforderliche maximale Länge eingehalten wird.

* Ursache:   
Der Dateipfad des Volumes ist zu lang.
* Lösung:   
Verwenden Sie einen kürzeren Dateipfad.

***Name ist zu kurz.***

Dieser Fehler zeigt an, dass der Ressourcenname nicht die erforderliche Mindestlänge aufweist.

* Ursache:   
Der Ressourcenname ist zu kurz.
* Lösung:   
Geben Sie einen längeren Namen für die Ressource ein.

***Dateipfad ist zu kurz.***

Dieser Fehler zeigt an, dass der Dateipfad nicht der erforderlichen Mindestlänge entspricht.

* Ursache:   
Der Dateipfad des Volumes ist zu kurz.
* Lösung:   
Geben Sie einen längeren Dateipfad für das Volume ein.

***Azure NetApp Files-API nicht erreichbar.***

Die Azure API basiert zur Verwaltung von Volumes auf der Azure NetApp Files-API. Dieser Fehler zeigt ein Problem mit der API-Verbindung an.

* Ursache:   
Die zugrunde liegende API antwortet nicht, was zu einem internen Fehler führt. Dieser Fehler ist wahrscheinlich nur temporär.
* Lösung:   
Das Problem ist wahrscheinlich nur temporär. Die Anforderung sollte nach einiger Zeit erfolgreich durchgeführt werden.
* Problemumgehung:   
Keine. Die zugrunde liegende API ist für die Volumenverwaltung unerlässlich.

***Keine Vorgangsergebnis-ID gefunden für „{0}“.***

Dieser Fehler zeigt an, dass ein interner Fehler den Abschluss des Vorgangs verhindert.

* Ursache:   
Ein interner Fehler ist aufgetreten und hat verhindert, dass der Vorgang abgeschlossen wurde.
* Lösung:   
Dieser Fehler ist wahrscheinlich nur temporär. Warten Sie einige Minuten, und versuchen Sie erneut. Wenn das Problem weiterhin besteht, erstellen Sie ein Ticket, damit der technische Support das Problem untersuchen kann.
* Problemumgehung:   
Warten Sie einige Minuten und überprüfen Sie, ob das Problem weiterhin besteht.

***Die Kombination der Protokolltypen CIFS und NFS ist nicht zulässig.***

Dieser Fehler tritt auf, wenn Sie ein Volume erstellen und in den Volumeeigenschaften sowohl der Protokolltyp CIFS (SMB) als auch NFS vorhanden ist.

* Ursache:   
Die Protokolltypen CIFS (SMB) und NFS werden gemeinsam in den Volumeeigenschaften verwendet.
* Lösung:   
Entfernen Sie einen der Protokolltypen.
* Problemumgehung:   
Lassen Sie die Protokolltypeigenschaft leer, oder verwenden Sie einen NULL-Wert.

***Anzahl von Elementen: {Value} für das Objekt: ExportPolicy.Rules [RuleIndex] liegt außerhalb des Min/Max-Bereichs.***

Dieser Fehler tritt auf, wenn die Regeln der Exportrichtlinien nicht den Anforderungen an den minimalen oder maximalen Bereich entsprechen. Wenn Sie die Exportrichtlinie definieren, muss sie mindestens über eine Exportrichtlinienregel und darf maximal über fünf Exportrichtlinienregeln verfügen.

* Ursache:   
Die von Ihnen definierte Exportrichtlinie entspricht nicht dem erforderlichen Bereich.
* Lösung:   
Stellen Sie sicher, dass der Index nicht bereits verwendet wird und im Bereich von 1 bis 5 liegt.
* Problemumgehung:   
Es ist nicht zwingend erforderlich, die Exportrichtlinien auf die Volumes anzuwenden. Sie können die Exportrichtlinie vollständig weglassen, wenn Sie keine Exportrichtlinienregeln benötigen.

***Nur ein aktives Verzeichnis erlaubt.***

Dieser Fehler tritt auf, wenn Sie versuchen, eine Active Directory-Konfiguration zu erstellen, und für das Abonnement in der Region bereits eine vorhanden ist. Der Fehler kann auch auftreten, wenn Sie versuchen, mehr als eine Active Directory-Konfiguration zu erstellen.

* Ursache:   
Sie versuchen, ein aktives Verzeichnis zu erstellen (nicht zu aktualisieren), aber es ist bereits eines vorhanden.
* Lösung:   
Wenn die Active Directory-Konfiguration nicht verwendet wird, können Sie zunächst die bestehende Konfiguration löschen und dann den Erstellungsvorgang erneut ausführen.
* Problemumgehung:   
Keine. Es ist nur eine Active Directory-Instanz erlaubt.

***Der Vorgang „{operation}“ wird nicht unterstützt.***

Dieser Fehler zeigt an, dass der Vorgang für das aktive Abonnement oder die aktive Ressource nicht verfügbar ist.

* Ursache:   
Der Vorgang ist für das Abonnement oder die Ressource nicht verfügbar.
* Lösung:   
Stellen Sie sicher, dass der Vorgang richtig eingegeben wurde und für die Ressource und das Abonnement, das Sie verwenden, verfügbar ist.

***OwnerId kann nicht geändert werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, die `OwnerId`-Eigenschaft des Volumes zu ändern. Das Ändern von `OwnerId` ist kein unterstützter Vorgang. 

* Ursache:   
Die `OwnerId`-Eigenschaft wird beim Erstellen des Volumes festgelegt. Die Eigenschaft kann nachträglich nicht mehr geändert werden.
* Lösung:   
`OwnerId` nicht in einen Patch und die Update (Put)-Anforderung aufnehmen. Alternativ können Sie auch sicherstellen, dass `OwnerId` in der Anforderung identisch ist.

***Übergeordneten Pool nicht gefunden.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume zu erstellen, und der Kapazitätspool für die Erstellung nicht gefunden wird.

* Ursache:   
Der Kapazitätspool, in dem das Volume erstellt wird, wurde nicht gefunden.
* Lösung:   
Wahrscheinlich war der Pool zum Zeitpunkt der Volumeerstellung noch nicht vollständig erstellt oder bereits gelöscht.

***Patchvorgang wird für diesen Ressourcentyp nicht unterstützt.***

Dieser Fehler tritt auf, wenn Sie versuchen, das Einbindungsziel oder die Momentaufnahme zu ändern.

* Ursache:   
Das Einbindungsziel wird bereits beim Erstellen definiert und kann nachträglich nicht mehr geändert werden.
Die Momentaufnahmen enthalten keine Eigenschaften, die geändert werden können.
* Lösung:   
Keine. Diese Ressourcen verfügen nicht über Eigenschaften, die geändert werden können.

***Poolgröße zu klein für die Gesamtvolumegröße.***

Dieser Fehler tritt auf, wenn Sie die Größe des Kapazitätspools aktualisieren und die Größe kleiner als der `usedBytes`-Gesamtwert aller Volumes in diesem Kapazitätspool ist.  Dieser Fehler kann auch auftreten, wenn Sie ein neues Volume erstellen oder die Größe eines bestehenden Volumes ändern und die neue Volumegröße den freien Speicherplatz im Kapazitätspool überschreitet.

* Ursache:   
Sie versuchen, den Kapazitätspool in allen Volumes des Kapazitätspools auf eine kleinere Größe als „usedBytes“ zu aktualisieren.  Oder Sie versuchen, ein Volume anzulegen, das größer als der vorhandene Platz im Kapazitätspool ist.  Oder aber Sie versuchen, die Größe eines Volumes zu ändern, und die neue Größe überschreitet den verfügbaren Platz im Kapazitätspool.
* Lösung:   
Legen Sie die Größe des Kapazitätspools auf einen größeren Wert fest, oder erstellen Sie ein kleineres Volume für ein Volume.
* Problemumgehung:   
Entfernen Sie genügend Volumes, damit die Größe des Kapazitätspools auf diese Größe aktualisiert werden kann.

***Die Eigenschaft: „Location“ für die Momentaufnahme muss mit dem Volume übereinstimmen.***

Dieser Fehler tritt auf, wenn Sie eine Momentaufnahme mit einem anderen Speicherort als dem Volume erstellen, das Eigentümer der Momentaufnahme ist.

* Ursache:   
Ungültiger Wert in der „Location“-Eigenschaft für die Momentaufnahme.
* Lösung:   
Legen Sie eine gültige Zeichenfolge in der „Location“-Eigenschaft fest.

***Der Name „{ResourceType}“ muss mit dem Namen des Ressourcenbezeichners übereinstimmen.***

Dieser Fehler tritt auf, wenn Sie eine Ressource erstellen und in die „Name“-Eigenschaft einen Wert eintragen, der sich von dem in der „Name“-Eigenschaft von `resourceId` unterscheidet.

* Ursache:   
Ungültiger Wert in der „Name“-Eigenschaft beim Erstellen einer Ressource.
* Lösung:   
Lassen Sie die „Name“-Eigenschaft leer, oder ermöglichen Sie es, dass dafür der gleiche Wert wie für die „Name“-Eigenschaft (zwischen dem letzten Backslash „/“ und dem Fragezeichen „?“) in `resourceId` verwendet wird.

***Der Protokolltyp „{value}“ ist nicht bekannt.***

Dieser Fehler tritt auf, wenn Sie ein Volume mit einem unbekannten Protokolltyp erstellen.  Gültige Werte sind „NFSv3“, „NFSv4“ und „CIFS“.

* Ursache:   
Sie versuchen, einen ungültigen Wert in der Volumeeigenschaft `protocolType` festzulegen.
* Lösung:   
Legen Sie in `protocolType` eine gültige Zeichenfolge fest.
* Problemumgehung:   
Legen Sie `protocolType` als NULL-Wert fest.

***Protokolltypen können nicht geändert werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, `ProtocolType` für ein Volume zu aktualisieren oder zu patchen.  Das Ändern von ProtocolType ist kein unterstützter Vorgang.

* Ursache:   
Die `ProtocolType`-Eigenschaft wird beim Erstellen des Volumes festgelegt.  Sie kann nicht aktualisiert werden.
* Lösung:   
Keine.
* Problemumgehung:   
Erstellen Sie ein weiteres Volume mit neuen Protokolltypen.

***Das Erstellen der Ressource vom Typ „{resourceType}“ würde das Kontingent der „{quota}“-Ressourcen vom Typ „{resourceType}“ pro „{parentResourceType}“ überschreiten. Die aktuelle Ressourcenanzahl liegt bei „{currentCount}“. Bitte löschen Sie einige Ressourcen dieses Typs, bevor Sie eine neue erstellen.***

Dieser Fehler tritt auf, wenn Sie versuchen, eine Ressource zu erstellen (`NetAppAccount`, `CapacityPool`, `Volume` oder`Snapshot`), aber Ihre Kontingentgrenze erreicht wurde.

* Ursache:   
Sie versuchen, eine Ressource zu erstellen, aber das Kontingent ist erreicht (Beispiel: `NetAppAccounts` pro Abonnement oder `CapacityPools` pro `NetAppAccount`).
* Lösung:   
Erhöhen Sie die Kontingentgrenze.
* Problemumgehung:   
Löschen Sie ungenutzte Ressourcen des gleichen Typs, und erstellen Sie sie neu.

***Es wurde ein Wert für die schreibgeschützte Eigenschaft „{propertyName}“ empfangen.***

Dieser Fehler tritt auf, wenn Sie einen Wert für eine Eigenschaft definieren, der nicht geändert werden kann. So können Sie z. B. die Volume-ID nicht ändern.

* Ursache:   
Sie versuchen, einen Parameter (z. B. die Volume-ID) zu ändern, der nicht geändert werden kann.
* Lösung:   
Ändern Sie den Wert für die Eigenschaft nicht.

***Die angeforderte Ressource „{resource}“ wurde nicht gefunden.***

Dieser Fehler tritt auf, wenn Sie versuchen, auf eine nicht vorhandene Ressource zu verweisen, z. B. ein Volume oder eine Momentaufnahme. Die Ressource wurde möglicherweise gelöscht oder weist einen falsch geschriebenen Ressourcennamen auf.

* Ursache:   
Sie versuchen, auf eine nicht vorhandene Ressource (z. B. ein Volume oder eine Momentaufnahme) zu verweisen, die bereits gelöscht wurde oder einen falsch geschriebenen Ressourcennamen aufweist.
* Lösung:   
Überprüfen Sie die Anforderung auf Rechtschreibfehler, um sicherzustellen, dass sie ordnungsgemäß referenziert wird.
* Problemumgehung:   
Weitere Informationen finden Sie oben im Abschnitt „Lösung“.

***Servicelevel „{volumeServiceLevel}“ ist höher als der übergeordnete „{poolServiceLevel}“.***

Dieser Fehler tritt auf, wenn Sie ein Volume erstellen oder aktualisieren und den Servicelevel auf einen höheren Level als für den übergeordneten Kapazitätspool festgelegt haben.

* Ursache:   
Sie versuchen, ein Volume mit einem höheren Servicelevel als für den übergeordneten Kapazitätspool zu erstellen oder zu aktualisieren.
* Lösung:   
Legen Sie den Servicelevel auf die gleiche oder eine niedrigere Stufe als für den übergeordneten Kapazitätspool fest.
* Problemumgehung:   
Erstellen Sie das Volume in einem anderen Kapazitätspool mit einem passenden Servicelevel. Alternativ können Sie alle Volumes aus dem Kapazitätspool löschen und den Servicelevel für den Kapazitätspool auf eine höhere Stufe festlegen.

***Der Name des SMB-Servers darf nicht länger als zehn Zeichen sein.***

Dieser Fehler tritt auf, wenn Sie eine Active Directory-Konfiguration für ein Konto erstellen oder aktualisieren.

* Ursache:   
Die Länge des SMB-Servernamens beträgt mehr als zehn Zeichen.
* Lösung:   
Verwenden Sie einen kürzeren Servernamen. Die maximale Länge beträgt zehn Zeichen.
* Problemumgehung:   
Keine.  Weitere Informationen finden Sie oben unter „Lösung“. 

***SubnetId kann nicht geändert werden.***

Dieser Fehler tritt bei dem Versuch auf, `subnetId` zu ändern, nachdem das Volume erstellt wurde.  `SubnetId` muss beim Erstellen des Volumes festgelegt werden und kann später nicht mehr geändert werden.

* Ursache:   
Sie versuchen, `subnetId` nach dem Erstellen des Volumes zu ändern. Dies ist kein unterstützter Vorgang. 
* Lösung:   
Wenn Sie `subnetId` nicht ändern müssen, können Sie den Parameter aus der Anforderung entfernen, um die Fehlermeldung zu schließen.
* Problemumgehung:   
Wenn Sie `subnetId` ändern müssen, können Sie ein neues Volume mit einem neuen `subnetId` erstellen und die Daten dann zum neuen Volume migrieren.

***SubnetId hat kein gültiges Format.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein neues Volume zu erstellen, aber die `subnetId` keine `resourceId` für ein Subnetz ist.

* Ursache:   
Dieser Fehler tritt auf, wenn Sie versuchen, ein neues Volume zu erstellen, aber die `subnetId` keine `resourceId` für ein Subnetz ist. 
* Lösung:   
Überprüfen Sie den Wert für `subnetId`, um sicherzustellen, dass er eine `resourceId` für das verwendete Subnetz enthält.
* Problemumgehung:   
Keine. Weitere Informationen finden Sie oben unter „Lösung“. 

***Das Subnetz muss über eine Delegierung von „Microsoft.NetApp/volumes“ verfügen.***

Dieser Fehler tritt auf, wenn Sie ein Volume erstellen und das ausgewählte Subnetz nicht an `Microsoft.NetApp/volumes` delegiert wird.

* Ursache:   
Sie haben versucht, ein Volume zu erstellen, und ein Subnetz ausgewählt, das nicht an `Microsoft.NetApp/volumes` delegiert wurde.
* Lösung:   
Wählen Sie ein anderes Subnetz aus, das an `Microsoft.NetApp/volumes` delegiert wurde.
* Problemumgehung:   
Fügen Sie dem Subnetz eine korrekte Delegierung hinzu.

***Der angegebene Ressourcentyp ist unbekannt/nicht anwendbar.***

Dieser Fehler tritt auf, wenn eine Namensprüfung entweder für einen nicht anwendbaren Ressourcentyp oder für einen unbekannten Ressourcentyp angefordert wurde.

* Ursache:   
Die Namensprüfung wurde für einen unbekannten oder nicht unterstützten Ressourcentyp angefordert.
* Lösung:   
Überprüfen Sie, ob die Ressource, für die Sie die Anforderung senden, unterstützt wird und keine Rechtschreibfehler enthält.
* Problemumgehung:   
Weitere Informationen finden Sie oben unter „Lösung“.

***Unbekannter Azure NetApp Files-Fehler.***

Die Azure API basiert zur Verwaltung von Volumes auf der Azure NetApp Files-API. Der Fehler zeigt ein Problem bei der Kommunikation mit der API an.

* Ursache:   
Die zugrunde liegende API sendet einen unbekannten Fehler. Dieser Fehler ist wahrscheinlich nur temporär.
* Lösung:   
Das Problem ist wahrscheinlich nur temporär, und die Anforderung sollte nach einiger Zeit erfolgreich durchgeführt werden. Wenn das Problem weiterhin besteht, erstellen Sie ein Supportticket, um das Problem untersuchen zu lassen.
* Problemumgehung:   
Keine. Die zugrunde liegende API ist für die Volumenverwaltung unerlässlich.

***Empfangener Wert für eine unbekannte Eigenschaft „{propertyName}“.***

Dieser Fehler tritt auf, wenn nicht vorhandene Eigenschaften für eine Ressource wie Volume, Momentaufnahme oder Einbindungsziel bereitgestellt werden.

* Ursache:   
Die Anforderung verfügt über eine Reihe von Eigenschaften, die für die einzelnen Ressourcen verwendet werden können. Sie können keine nicht vorhandenen Eigenschaften in die Anforderung aufnehmen.
* Lösung:   
Stellen Sie sicher, dass alle Eigenschaftsnamen richtig geschrieben und die Eigenschaften für das Abonnement und die Ressource verfügbar sind.
* Problemumgehung:   
Verringern Sie die Anzahl der in der Anforderung definierten Eigenschaften, um die Eigenschaft zu beseitigen, die den Fehler verursacht.

***Aktualisierungsvorgang wird für diesen Ressourcentyp nicht unterstützt.***

Es können nur Volumes aktualisiert werden. Dieser Fehler tritt auf, wenn Sie versuchen, einen nicht unterstützten Aktualisierungsvorgang durchzuführen, z. B. das Aktualisieren einer Momentaufnahme.

* Ursache:   
Die Ressource, die Sie zu aktualisieren versuchen, unterstützt den Aktualisierungsvorgang nicht. Lediglich die Eigenschaften von Volumes können geändert werden.
* Lösung:   
Keine. Die Ressource, die Sie zu aktualisieren versuchen, unterstützt den Aktualisierungsvorgang nicht. Somit kann sie nicht geändert werden.
* Problemumgehung:   
Erstellen Sie für ein Volume eine neue Ressource mit vorhandenem Update, und migrieren Sie die Daten.

***Ein Volume kann nicht in einem Pool erstellt werden, der nicht über den Status „Erfolgreich“ verfügt.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume in einem Pool zu erstellen, der nicht den Status „Erfolgreich“ aufweist. Wahrscheinlich ist der Erstellungsvorgang für den Kapazitätspool aus irgendeinem Grund fehlgeschlagen.

* Ursache:   
Der Kapazitätspool, der das neue Volume enthält, befindet sich in einem fehlerhaften Zustand.
* Lösung:   
Überprüfen Sie, ob der Kapazitätspool erfolgreich erstellt wurde und sich nicht in einem fehlerhaften Zustand befindet.
* Problemumgehung:   
Legen Sie einen neuen Kapazitätspool an, und erstellen Sie das Volume im neuen Pool.

***Das Volume wird gerade erstellt und kann derzeit nicht gelöscht werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume zu löschen, das sich noch in der Erstellung befindet.

* Ursache:   
Ein Volume wird noch erstellt, wenn Sie versuchen, das Volume zu löschen.
* Lösung:   
Warten Sie, bis die Volumeerstellung abgeschlossen ist, und versuchen Sie dann erneut, den Löschvorgang durchzuführen.
* Problemumgehung:   
Weitere Informationen finden Sie oben unter „Lösung“.

***Das Volume wird gelöscht und kann derzeit nicht gelöscht werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume zu löschen, während es bereits gelöscht wird.

* Ursache:   
Ein Volume wird bereits gelöscht, während Sie versuchen, das Volume zu löschen.
* Lösung:   
Warten Sie, bis der aktuelle Löschvorgang abgeschlossen ist.
* Problemumgehung:   
Weitere Informationen finden Sie oben unter „Lösung“.

***Das Volume wird gerade aktualisiert und kann derzeit nicht gelöscht werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume zu löschen, das gerade aktualisiert wird.

* Ursache:   
Ein Volume wird aktualisiert, während Sie versuchen, das Volume zu löschen.
* Lösung:   
Warten Sie, bis die Aktualisierung abgeschlossen ist, und versuchen Sie dann erneut, den Löschvorgang durchzuführen.
* Problemumgehung:   
Weitere Informationen finden Sie oben unter „Lösung“.

***Volume wurde nicht gefunden oder nicht erfolgreich erstellt.***

Dieser Fehler tritt auf, wenn die Erstellung des Volumes fehlgeschlagen ist und Sie versuchen, das Volume zu ändern oder eine Momentaufnahme für das Volume zu erstellen.

* Ursache:   
Das Volume ist nicht vorhanden, oder die Erstellung ist fehlgeschlagen.
* Lösung:   
Überprüfen Sie, ob Sie das richtige Volume ändern und ob die Erstellung des Volumes erfolgreich war. Oder überprüfen Sie, ob das Volume, für das Sie eine Momentaufnahme erstellen, vorhanden ist.
* Problemumgehung:   
Keine.  Weitere Informationen finden Sie oben unter „Lösung“. 

***Das angegebene Erstellungstoken ist bereits vorhanden***

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume zu erstellen, und Sie ein Erstellungstoken (Exportpfad) angeben, für das bereits ein Volume vorhanden ist.

* Ursache:   
Das Erstellungstoken (Exportpfad), das Sie bei der Volumeerstellung angegeben haben, ist bereits einem anderen Volume zugeordnet. 
* Lösung:   
Wählen Sie ein anderes Erstellungstoken aus.  Alternativ können Sie auch das andere Volume löschen.

***Das angegebene Erstellungstoken ist reserviert.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein Volume zu erstellen, und als Dateipfad „default“ oder „none“ angeben (Erstellungstoken).

* Ursache:    
Sie versuchen, ein Volume zu erstellen, und haben als Dateipfad „default“ oder „none“ angegeben (Erstellungstoken).
* Lösung:   
Wählen Sie einen anderen Dateipfad (Erstellungstoken) aus.
 
***Active Directory-Anmeldeinformationen werden verwendet.***

Dieser Fehler tritt auf, wenn Sie versuchen, die Active Directory-Konfiguration aus einem Konto zu löschen, in dem noch mindestens ein SMB-Volume vorhanden ist.  Das SMB-Volume wurde unter Verwendung der Active Directory-Konfiguration erstellt, die Sie zu löschen versuchen.

* Ursache:   
Sie versuchen, die Active Directory-Konfiguration aus einem Konto zu löschen, aber es ist noch mindestens ein SMB-Volume vorhanden, das ursprünglich mit der Active Directory-Konfiguration erstellt wurde. 
* Lösung:   
Löschen Sie zunächst alle SMB-Volumes, die mit der Active Directory-Konfiguration erstellt wurden.  Versuchen Sie dann erneut, die Konfiguration zu löschen.

***Die Zuordnung der Organisationseinheit kann nicht geändert werden, wenn die Anmeldeinformationen verwendet werden.***

Dieser Fehler tritt auf, wenn Sie versuchen, die Organisationseinheit einer Active Directory-Konfiguration zu ändern, aber noch mindestens ein SMB-Volume vorhanden ist.  Das SMB-Volume wurde unter Verwendung der Active Directory-Konfiguration erstellt, die Sie zu löschen versuchen.

* Ursache:   
Sie versuchen, die Organisationseinheit einer Active Directory-Konfiguration zu ändern.  Es ist aber noch mindestens ein SMB-Volume vorhanden, das ursprünglich mithilfe der Active Directory-Konfiguration erstellt wurde.
* Lösung:   
 Löschen Sie zunächst alle SMB-Volumes, die mit der Active Directory-Konfiguration erstellt wurden.  Versuchen Sie dann erneut, die Konfiguration zu löschen. 

***Active Directory-Aktualisierung wird bereits ausgeführt.***

Dieser Fehler tritt auf, wenn Sie versuchen, eine Active Directory-Konfiguration zu bearbeiten, für die bereits ein Bearbeitungsvorgang ausgeführt wird.

* Ursache:   
Sie versuchen, eine Active Directory-Konfiguration zu bearbeiten, aber ein weiterer Bearbeitungsvorgang ist bereits aktiv.
* Lösung:   
Warten Sie, bis der gerade laufende Bearbeitungsvorgang abgeschlossen ist.

***Löschen Sie zuerst alle Volumes mit den ausgewählten Anmeldeinformationen.***

Dieser Fehler tritt auf, wenn Sie versuchen, eine Active Directory-Konfiguration zu löschen, aber noch mindestens ein SMB-Volume vorhanden ist.  Das SMB-Volume wurde unter Verwendung der Active Directory-Konfiguration erstellt, die Sie zu löschen versuchen.

* Ursache:   
Sie versuchen, die Active Directory-Konfiguration zu löschen, aber es ist noch mindestens ein SMB-Volume vorhanden, das ursprünglich mit der Active Directory-Konfiguration erstellt wurde.
* Lösung:   
Löschen Sie zunächst alle SMB-Volumes, die mit der Active Directory-Konfiguration erstellt wurden.  Versuchen Sie dann erneut, die Konfiguration zu löschen. 

***Keine Active Directory-Anmeldeinformationen in der Region gefunden.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein SMB-Volume zu erstellen, aber dem Konto für die Region keine Active Directory-Konfiguration hinzugefügt wurde.

* Ursache:   
Sie versuchen, ein SMB-Volume zu erstellen, aber dem Konto wurde keine Active Directory-Konfiguration hinzugefügt. 
* Lösung:   
Fügen Sie dem Konto eine Active Directory-Konfiguration hinzu, bevor Sie ein SMB-Volume erstellen.

***DNS-Server konnte nicht abgefragt werden. Überprüfen Sie, ob die Netzwerkkonfiguration korrekt ist und DNS-Server verfügbar sind.***

Dieser Fehler tritt auf, wenn Sie versuchen, ein SMB-Volume zu erstellen, aber ein DNS-Server (der in Ihrer Active Directory-Konfiguration angegeben ist) nicht erreichbar ist. 

* Ursache:   
Sie versuchen, ein SMB-Volume zu erstellen, aber ein DNS-Server (der in Ihrer Active Directory-Konfiguration angegeben ist) ist nicht erreichbar.
* Lösung:   
Überprüfen Sie Ihre Active Directory-Konfiguration, und stellen Sie sicher, dass die IP-Adressen der DNS-Server korrekt und erreichbar sind.
Wenn es keine Probleme mit den IP-Adressen des DNS-Servers gibt, sollten Sie sicherstellen, dass keine Firewalls den Zugriff blockieren.

***Zu viele gleichzeitige Aufträge.***

Dieser Fehler tritt auf, wenn Sie versuchen, eine Momentaufnahme zu erstellen, wenn drei weitere Vorgänge zur Erstellung von Momentaufnahmen für das Abonnement bereits ausgeführt werden.

* Ursache:   
Sie versuchen, eine Momentaufnahme zu erstellen, wenn drei weitere Vorgänge zur Erstellung von Momentaufnahmen für das Abonnement bereits ausgeführt werden. 
* Lösung:   
Die Erstellung von Momentaufnahmeaufträgen dauert höchstens ein paar Sekunden.  Warten Sie ein paar Sekunden, und versuchen Sie dann erneut, die Erstellung der Momentaufnahme durchzuführen.

***Es können keine zusätzlichen Aufträge geschaffen werden. Bitte warten Sie, bis die laufenden Aufträge abgeschlossen sind, und versuchen Sie es erneut.***

Dieser Fehler kann auftreten, wenn Sie versuchen, ein Volume unter bestimmten Umständen zu erstellen oder zu löschen.

* Ursache:   
Sie versuchen, unter bestimmten Umständen ein Volume zu erstellen oder zu löschen.
* Lösung:   
Warten Sie etwa eine Minute, und wiederholen Sie den Vorgang.

***Das Volume befindet sich in einem Übergangszustand.***

Dieser Fehler kann auftreten, wenn Sie versuchen, ein Volume zu löschen, das sich gerade in einem Übergangszustand befindet (Erstellungs-, Aktualisierungs- oder Löschzustand).

* Ursache:   
Sie versuchen, ein Volume zu löschen, das sich derzeit in einem Übergangszustand befindet.
* Lösung:   
Warten Sie, bis der gerade durchgeführte Vorgang (Statusübergang) abgeschlossen ist, und führen Sie den Vorgang anschließend erneut durch.

***Das neue Volume konnte nicht von der Momentaufnahme des Quellvolumes getrennt werden.***

 Dieser Fehler kann auftreten, wenn Sie versuchen, ein Volume aus einer Momentaufnahme zu erstellen.  

* Ursache:   
Sie versuchen, ein Volume aus einer Momentaufnahme zu erstellen, und das Volume endet in einem Fehlerzustand.
* Lösung:   
Löschen Sie das Volume, und versuchen Sie dann erneut, das Volume aus der Momentaufnahme zu erstellen.

 
## <a name="next-steps"></a>Nächste Schritte

* [Entwickeln für Azure NetApp Files mit REST-API](azure-netapp-files-develop-with-rest-api.md)

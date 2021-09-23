---
title: Häufige Fehler und bekannte Probleme bei der Migration zu Azure Cloud Services (erweiterter Support)
description: Übersicht über häufige Fehler bei der Migration von Cloud Services (klassisch) zu Cloud Services (erweiterter Support)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: ce0c3bb4386e2fcc48ca57a1cd53978fc684d98c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540953"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Häufige Fehler und bekannte Probleme bei der Migration zu Azure Cloud Services (erweiterter Support)

Dieser Artikel behandelt bekannte Probleme und häufige Fehler, die bei der Migration von Cloud Services (klassisch) zu Cloud Services (erweiterter Support) auftreten können. 

## <a name="known-issues"></a>Bekannte Probleme
Folgende Probleme sind bekannt und werden behandelt.

| Bekannte Probleme | Minderung | 
|---|---|
| Rolleninstanzen, die UD von UD nach erfolgreichem Commit neu starten. | Der Neustart folgt derselben Methode wie monatliche Rollouts für Gast-Betriebssysteme. Führen Sie keinen Commit für die Migration von Clouddiensten mit einer einzelnen Rollen Instanz aus, oder verwenden Sie einen Neustart.| 
| Azure-Portal können den Migrationsstatus nach dem Aktualisieren des Browsers nicht lesen. | Führen Sie die Überprüfung und den Vorbereitungs Vorgang erneut aus, um zum ursprünglichen Migrationsstatus zurückzukehren. | 
| Zertifikat, das als geheime Ressource in Key Vault angezeigt wird. | Laden Sie das Zertifikat nach der Migration erneut als Zertifikatressource hoch, um den Aktualisierungs Vorgang auf Cloud Services (erweiterter Support) zu vereinfachen. | 
| Bereitstellungsbezeichnungen werden im Rahmen der Migration nicht als Tags gespeichert. | Erstellen Sie die Tags nach der Migration manuell, um diese Informationen zu erhalten.
| Der Name der Ressourcengruppe ist in allen Obergrenzen. | Ohne Auswirkung. Die Lösung ist noch nicht verfügbar. |
| Der Name der Sperre auf Cloud Services-Sperre (Erweiterte Unterstützung) ist falsch. | Ohne Auswirkung. Die Lösung ist noch nicht verfügbar. | 
| Der Name der IP-Adresse auf dem Cloud Services Portal-Blatt (erweiterter Support) ist falsch. | Ohne Auswirkung. Die Lösung ist noch nicht verfügbar. | 
| Der DNS-Name für die virtuelle IP-Adresse nach dem Update für einen migrierten Cloud Dienst ist ungültig. | Ohne Auswirkung. Die Lösung ist noch nicht verfügbar. | 
| Nach der erfolgreichen Vorbereitung ist das Verknüpfen einer neuen Cloud Services-Bereitstellung (Erweiterte Unterstützung) als „Austauschfähig“ nicht zulässig. | Verknüpfen Sie einen neuen Cloud Service nicht als „Austauschfähig“ mit einem vorbereiteten Cloud Service. | 
| Fehlermeldungen müssen aktualisiert werden. | Ohne Auswirkung. | 

## <a name="common-migration-errors"></a>Häufige Fehler bei der Migration
Häufige Fehler bei der Migration und Gegenmaßnahmen. 

| Fehlermeldung | Details | 
|---|---|
| Der Ressourcentyp konnte nicht im Namespace „{2020-10-01}“ für API-Version `Microsoft.Compute` gefunden werden. | [Registrieren Sie das Abonnement für das](in-place-migration-overview.md#setup-access-for-migration) Cloud Services-Feature-Flag, um auf die öffentliche Vorschau zuzugreifen. | 
| Auf dem Server ist ein interner Fehler aufgetreten. Wiederholen Sie die Anforderung. | Wiederholen Sie den Vorgang und wenden Sie sich an [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) oder an den Support. | 
| Der Server erkannte einen unerwarteten Fehler beim Zuweisen von Netzwerkressourcen für den Cloud Service. Wiederholen Sie die Anforderung. | Wiederholen Sie den Vorgang und wenden Sie sich an [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) oder an den Support. | 
| Bereitstellung „Bereitstellungsname“ im Cloud Service „Cloud Service“ muss sich in einem virtuellen Netzwerk befinden, das migriert werden soll. | Die Bereitstellung befindet sich nicht in einem virtuellen Netzwerk. Ausführlichere Informationen finden Sie in [diesem](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) Dokument. | 
| Migration der Bereitstellung „Bereitstellung“ im Cloud Service „Cloud Service“ wird nicht unterstützt, da Sie sich in der Region „Regionsname“ befindet. Zulässige Regionen: [Liste der verfügbaren Regionen]. | Die Region wird für die Migration noch nicht unterstützt. | 
| Die Bereitstellung „Bereitstellungsname“ im Cloud Service „Cloud Service-Name“ kann nicht migriert werden, weil dem/den Rollennamen der Rolle(n) keine Subnetze zugeordnet sind. Ordnen Sie alle Rollen einem Subnetz zu und wiederholen Sie dann die Migration des Cloud Services. | Aktualisieren Sie die Bereitstellung des Cloud Services (klassisch), indem Sie ihn vor der Migration in einem Subnetz platzieren. |  
| Die Bereitstellung „Bereitstellungsname“ im Cloud Service „Cloud Service-Name“ kann nicht migriert werden, da für die Bereitstellung mindestens eine Funktion erforderlich ist, die in Azure Resource Manager nicht für das Abonnement registriert ist. Registrieren Sie alle erforderlichen Funktionen, um diese Bereitstellung zu migrieren. Fehlende Funktion(en): [Liste der fehlenden Funktionen]. | Kontaktieren Sie den Support, um die Feature-Flags zu registrieren. | 
| Die Bereitstellung kann nicht migriert werden, weil zwei Slots des Cloud Services der Bereitstellung belegt sind. Die Migration von Cloud Services wird nur für Bereitstellungen unterstützt, bei denen es sich um die einzige Bereitstellung in Ihrem Cloud Service handelt. Löschen Sie die andere Bereitstellung im Cloud Service, um mit der Migration dieser Bereitstellung fortzufahren. | Weitere Informationen finden Sie in der Liste der [nicht unterstützten Szenarien](in-place-migration-technical-details.md#unsupported-configurations--migration-scenarios). | 
| Bereitstellung „Bereitstellungsname“ im HostedService „Cloud Service-Name“ befindet sich im Zwischenzustand: State. Migration ist nicht zulässig. | Die Bereitstellung wird entweder erstellt, gelöscht oder aktualisiert. Warten Sie, bis der Vorgang abgeschlossen wurde, und wiederholen Sie den Vorgang. | 
| Die Bereitstellung „Bereitstellungsname“ im gehosteten Dienst „Cloud Service-Name“ verfügt über reservierte IP(s), aber keinen reservierten IP-Namen. Um dieses Problem zu beheben, aktualisieren Sie den reservierten IP-Namen oder wenden Sie sich an das Service Desk von Microsoft Azure. | Aktualisieren Sie die Cloud Service-Bereitstellung. | 
| Die Bereitstellung „Bereitstellungsname“ im gehosteten Dienst „Cloud Service-Name“ verfügt über Namen reservierter IP(s), aber keinen Endpunkt der reservierten IP. Fügen Sie der reservierten IP mindestens einen Endpunkt hinzu, um dieses Problem zu beheben. | Fügen Sie den Endpunkt der reservierten IP hinzu. | 
| Die Migration der Bereitstellung {0} im HostedService {1} wird gerade ausgeführt und kann erst geändert werden, wenn sie erfolgreich abgeschlossen wurde.  | Warten Sie oder wiederholen Sie den Vorgang. | 
| Die Migration der Bereitstellung {0} im HostedService {1} wird gerade abgebrochen und kann erst geändert werden, wenn sie erfolgreich abgeschlossen wurde. | Warten Sie oder wiederholen Sie den Vorgang. |
| Mindestens eine virtuelle Maschine in der Bereitstellung {0} im HostedService {1} wird aktualisiert. Sie kann erst migriert werden, wenn der vorherige Vorgang erfolgreich abgeschlossen wurde. Wiederholen Sie den Vorgang später. | Warten Sie, bis der Vorgang abgeschlossen ist. | 
| Die Migration wird für die Bereitstellung {0} im HostedService {1} nicht unterstützt, weil sie die folgenden Funktionen verwendet, die für die Migration nicht unterstützt werden: nicht-vnet.| Die Bereitstellung befindet sich nicht in einem virtuellen Netzwerk. Ausführlichere Informationen finden Sie in [diesem](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) Dokument. | 
| Der Name des virtuellen Netzwerks darf nicht null oder leer sein. | Geben Sie im REST-Anforderungsbereich den Namen des virtuellen Netzwerks an | 
| Der Subnetz-Name darf nicht null oder leer sein. | Geben Sie im REST-Anforderungstext den Subnetznamen an. | 
| Für „DestinationVirtualNetwork“ muss einer der folgenden Werte festgelegt werden: „Standard“, „Neu“ oder „Vorhanden“. | Geben Sie die Eigenschaft „DestinationVirtualNetwork“ im REST-Anforderungstext ein. | 
| Die Standardoption „VNet-Ziel“ ist nicht implementiert. | Der „Default“-Wert wird für die Eigenschaft „DestinationVirtualNetwork“ im REST-Anforderungstext nicht unterstützt. | 
| Die Bereitstellung {0} kann nicht migriert werden, weil die CSPKG nicht verfügbar ist. | Aktualisieren Sie die Bereitstellung und wiederholen Sie den Vorgang. | 
| Das Subnetz mit der ID {0} befindet sich an einem anderen Speicherort als die Bereitstellung {1} im gehosteten Dienst {2}. Der Speicherort für das Subnetz ist {3} und der Speicherort für den gehosteten Dienst {4} ist.  Geben Sie ein Subnetz an demselben Speicherort wie die Bereitstellung an. | Aktualisieren Sie den Cloud Service so, dass sich vor der Migration sowohl ein Subnetz als auch ein Cloud Service am selben Standort befinden. | 
| Die Migration der Bereitstellung {0} im HostedService {1} wird gerade abgebrochen und kann erst geändert werden, wenn sie erfolgreich abgeschlossen wurde. | Warten Sie, bis der Abbruch abgeschlossen ist, oder wiederholen Sie den Abbruch. Wenden Sie sich an [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) oder an den Support. | 
| Die Bereitstellung {0} in HostedService {1} wurde nicht für die Migration vorbereitet. | Führen Sie Prepare für den Cloud Service aus, bevor Sie den Commit-Vorgang ausführen. | 
| UnknownExceptionInEndExecute: Contract.Assert fehlgeschlagen: rgName ist null oder leer: Ausnahme wurde in EndExecute empfangen, bei der es sich nicht um eine RdfeException handelt. |   Wenden Sie sich an [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) oder an den Support. | 
| UnknownExceptionInEndExecute: eine Aufgabe wurde abgebrochen: in EndExecute wurde eine Ausnahme empfangen, die keine RdfeException ist. | Wenden Sie sich an [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) oder an den Support. | 
| XrpVirtualNetworkMigrationError: Fehler bei der Migration des virtuellen Netzwerks. | Wenden Sie sich an [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) oder an den Support. | 
| Die Bereitstellung {0} in HostedService {1}  gehört zum virtuellen Netzwerk {2}. Migrieren Sie das virtuelle Netzwerk {2} zum Migrieren dieses HostedService {1}. | Siehe [Migration des virtuellen Netzwerks](in-place-migration-technical-details.md#virtual-network-migration). | 
| Das aktuelle Kontingent für den Ressourcennamen in Azure Resource Manager reicht nicht aus, um die Migration abzuschließen. Das aktuelle Kontingent ist {0}, zusätzlich erforderlich ist {1}. Senden Sie eine Supportanfrage, um das Kontingent zu erhöhen, und wiederholen Sie die Migration, wenn das Kontingent erhöht wurde.    | Befolgen Sie die entsprechenden Kanäle, um die Kontingenterhöhung anzufordern: <br>[Kontingenterhöhung für Netzwerkressourcen](../azure-portal/supportability/networking-quota-requests.md) <br>[Kontingenterhöhung für Computerressourcen](../azure-portal/supportability/per-vm-quota-requests.md) | 
|XrpPaaSMigrationCscfgCsdefValidationMismatch: Die Migration konnte für die Bereitstellung „Bereitstellungsname“ im gehosteten Dienst „Dienstname“ nicht abgeschlossen werden, weil die Metadaten der Bereitstellung veraltet sind. Brechen Sie die Migration ab, und führen Sie ein Upgrade der Bereitstellung durch, bevor Sie die Migration wiederholen. Überprüfungsmeldung: Der Dienstname „Dienstname“ in der Dienstdefinitionsdatei stimmt nicht mit dem Namen „Dienstname-in-Konfigurationsdatei“ in der Dienstkonfigurationsdatei überein.|Gleichen Sie die Dienstnamen in der CSDEF-Datei und in der CSCFG-Datei miteinander ab.|
|NetworkingInternalOperationError beim Bereitstellen einer Clouddienstressource (erweiterter Support)| Das Problem kann auftreten, wenn der Dienstname mit dem Rollennamen identisch ist. Die empfohlene Korrektur besteht in der Verwendung unterschiedlicher Namen für Dienste und Rollen.|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den Anforderungen der Migration finden Sie unter [Technische Details der Migration zu Azure Cloud Services (erweiterter Support)](in-place-migration-technical-details.md)

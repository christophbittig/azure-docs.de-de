---
title: Azure Service Bus Resource Manager-Ausnahmen | Microsoft-Dokumentation
description: Liste der Service Bus-Ausnahmen, die durch Azure Resource Manager angezeigt werden, und vorgeschlagene Aktionen.
ms.topic: article
ms.date: 09/15/2021
ms.openlocfilehash: be88fc84fcc8d90994f134a6efd64ed2bbd964a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610824"
---
# <a name="service-bus-resource-manager-exceptions"></a>Service Bus Resource Manager-Ausnahmen

In diesem Artikel werden die Ausnahmen aufgelistet, die bei der Interaktion mit Azure Service Bus mithilfe von Azure Resource Manager (über Vorlagen oder direkte Aufrufe) generiert werden.

> [!IMPORTANT]
> Dieses Dokument wird häufig aktualisiert. Überprüfen Sie zu einem späteren Zeitpunkt auf dieser Seite, ob neue Informationen vorliegen.

Im Folgenden finden Sie die verschiedenen Ausnahmen/Fehler, die durch Azure Resource Manager angezeigt werden.

## <a name="error-bad-request"></a>Fehler: Ungültige Anforderung

„Ungültige Anforderung“ impliziert, dass die von Resource Manager empfangene Anforderung die Überprüfung nicht bestanden hat.

| Fehlercode | Fehler-SubCode | Fehlermeldung | BESCHREIBUNG | Empfehlung |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Ungültige Anforderung | 40.000 | SubCode=40000. Die Eigenschaft *'Eigenschaftsname'* kann beim Erstellen einer Warteschlange nicht festgelegt werden, weil der Namespace *' Namespacename'* den Tarif 'Basic' verwendet. Dieser Vorgang wird nur im Tarif 'Basic' oder 'Premium' unterstützt. | Im Tarif Basic von Azure Service Bus können die folgenden Eigenschaften nicht festgelegt oder aktualisiert werden: <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Themen </li> </ul> | Führen Sie ein Upgrade von Basic auf Standard oder Premium durch, um diese Funktion zu verwenden. |
| Ungültige Anforderung | 40.000 | SubCode=40000. Der Wert für die Eigenschaft 'requiresDuplicateDetection' einer vorhandenen Warteschlange (oder eines Themas) kann nicht geändert werden. | Die Duplikaterkennung muss zum Zeitpunkt der Erstellung der Entität aktiviert/deaktiviert werden. Der Konfigurationsparameter für die Duplikaterkennung kann nach der Erstellung nicht mehr geändert werden. | Um die Duplikaterkennung für eine zuvor erstellte Warteschlange/ein Thema zu aktivieren, können Sie eine neue Warteschlange/ein neues Thema mit Duplikaterkennung erstellen und dann aus der ursprünglichen Warteschlange an die neue Warteschlange/das neue Thema weiterleiten. |
| Ungültige Anforderung | 40.000 | SubCode=40000. Der angegebene Wert 16384 ist ungültig. Die MaxSizeInMegabytes-Eigenschaft muss einen der folgenden Werte aufweisen: 1024, 2048, 3072, 4096, 5120. | Der Wert für MaxSizeInMegabytes ist ungültig. | Stellen Sie sicher, dass MaxSizeInMegabytes einer der folgenden Werte ist: 1024, 2048, 3072, 4096, 5120. |
| Ungültige Anforderung | 40.000 | SubCode=40000. Die Partitionierung kann für die Warteschlange/das Thema nicht geändert werden. | Die Partitionierung kann für die Entität nicht geändert werden. | Erstellen Sie eine neue Entität (Warteschlange oder Thema), und aktivieren Sie Partitionen. | 
| Ungültige Anforderung | none | Der Namespace *'Namespacename'* ist nicht vorhanden. | Der Namespace ist in Ihrem Azure-Abonnement nicht vorhanden. | Versuchen Sie Folgendes, um diesen Fehler zu beheben: <ul> <li> Stellen Sie sicher, dass das Azure-Abonnement richtig ist. </li> <li> Stellen Sie sicher, dass der Namespace vorhanden ist. </li> <li> Vergewissern Sie sich, dass der Namespacename richtig ist (keine Rechtschreibfehler oder NULL-Zeichenfolgen). </li> </ul> | 
| Ungültige Anforderung | 40400 | SubCode=40400. Die Zielentität für die automatische Weiterleitung ist nicht vorhanden. | Das Ziel für die Zielentität für die automatische Weiterleitung ist nicht vorhanden. | Die Zielentität (Warteschlange oder Thema) muss vorhanden sein, bevor die Quelle erstellt wird. Versuchen Sie es nach dem Erstellen der Zielentität erneut. |
| Ungültige Anforderung | 40.000 | SubCode=40000. Die angegebene Sperrzeit überschreitet den zulässigen Maximalwert von '5' Minuten. | Die Zeit, für die eine Nachricht gesperrt werden kann, muss zwischen 1 Minute (Minimum) und 5 Minuten (Maximum) liegen. | Stellen Sie sicher, dass die angegebene Sperrzeit zwischen 1 Minute und 5 Minuten liegt. |
| Ungültige Anforderung | 40.000 | SubCode=40000. Die Eigenschaften DelayedPersistence und RequiresDuplicateDetection können nicht gleichzeitig aktiviert werden. | Entitäten mit aktivierter Duplikaterkennung müssen persistent sein, daher darf die Persistenz nicht verzögert werden. | Weitere Informationen zur [Duplikaterkennung](duplicate-detection.md) |
| Ungültige Anforderung | 40.000 | SubCode=40000. Der Wert für die Eigenschaft RequiresSession einer vorhandenen Warteschlange kann nicht geändert werden. | Unterstützung für Sitzungen sollte zum Zeitpunkt der Erstellung der Entität aktiviert werden. Nach der Erstellung können Sie Sitzungen für eine vorhandene Entität (Warteschlange oder Abonnement) nicht mehr aktivieren/deaktivieren. | Löschen Sie die Entität, und erstellen Sie eine neue Warteschlange (oder ein neues Abonnement) mit aktivierter Eigenschaft RequiresSession. |
| Ungültige Anforderung | 40.000 | SubCode=40000. 'URI_PATH' enthält Zeichen, die für Service Bus unzulässig sind. Entitätssegmente dürfen nur Buchstaben, Ziffern, Punkte (.), Bindestriche (-) und Unterstriche (_) enthalten. | Entitätssegmente dürfen nur Buchstaben, Ziffern, Punkte (.), Bindestriche (-) und Unterstriche (_) enthalten. Alle anderen Zeichen führen dazu, dass die Anforderung fehlschlägt. | Stellen Sie sicher, dass der URI-Pfad keine ungültigen Zeichen enthält. |


## <a name="error-code-429"></a>Fehlercode: 429

Genau wie in HTTP gibt „Fehlercode 429“ an, dass zu viele Anforderungen vorhanden sind. Dies impliziert, dass die spezifische Ressource (Namespace) aufgrund einer zu großen Anzahl von Anforderungen (oder aufgrund von in Konflikt stehenden Vorgängen) für diese Ressource gedrosselt wird.

| Fehlercode | Fehler-SubCode | Fehlermeldung | BESCHREIBUNG | Empfehlung |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | SubCode=50004. Die Anforderung wurde beendet, weil der Namespace *IhrNamespace* gedrosselt wird. | Diese Fehlerbedingung wird ausgelöst, wenn die Anzahl der eingehenden Anforderungen die Einschränkung der Ressource überschreitet. | Warten Sie einige Sekunden, und versuchen Sie es noch mal. <br/> <br/> Weitere Informationen zu den [Kontingenten](service-bus-quotas.md) und [Azure Resource Manager-Anforderungslimits](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | SubCode=40901. Ein anderer in Konflikt stehender Vorgang wird ausgeführt. | Ein anderer in Konflikt stehender Vorgang wird für dieselbe Ressource/Entität ausgeführt. | Warten Sie, bis der aktuell ausgeführte Vorgang abgeschlossen ist, bevor Sie es noch mal versuchen. |
| 429 | 40900 | SubCode=40900. Konflikt. Sie fordern einen Vorgang an, der im aktuellen Zustand der Ressource nicht zulässig ist. | Diese Bedingung kann auftreten, wenn mehrere Anforderungen zum Ausführen der Vorgänge für dieselbe Entität (Warteschlange, Thema, Abonnement oder Regel) gleichzeitig erfolgen. | Warten Sie einige Sekunden, und versuchen Sie es noch mal. |
| 429 | 40901 | Die Anforderung für die Entität *'Entitätsname'* steht mit einer anderen Anforderung in Konflikt. | Ein anderer in Konflikt stehender Vorgang wird für dieselbe Ressource/Entität ausgeführt. | Warten Sie, bis der vorherige ausgeführte Vorgang abgeschlossen ist, bevor Sie es noch mal versuchen. |
| 429 | 40901 | Eine weitere Aktualisierungsanforderung wird aktuell für die Entität *'Entitätsname'* ausgeführt. | Ein anderer in Konflikt stehender Vorgang wird für dieselbe Ressource/Entität ausgeführt. | Warten Sie, bis der vorherige ausgeführte Vorgang abgeschlossen ist, bevor Sie es noch mal versuchen. |
| 429 | none | Ressourcenkonflikt. Ein anderer in Konflikt stehender Vorgang wird ggf. ausgeführt. Wenn dies ein Wiederholungsversuch für einen fehlgeschlagenen Vorgang ist, steht die Hintergrundbereinigung noch aus. Versuchen Sie es später noch einmal. | Diese Bedingung kann auftreten, wenn es einen ausstehenden Vorgang für dieselbe Entität gibt. | Warten Sie, bis der vorherige ausgeführte Vorgang abgeschlossen ist, bevor Sie es noch mal versuchen. |


## <a name="error-code-not-found"></a>Fehlercode: Nicht gefunden

Diese Fehlerklasse gibt an, dass die Ressource nicht gefunden wurde.

| Fehlercode | Fehler-SubCode | Fehlermeldung | BESCHREIBUNG | Empfehlung |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nicht gefunden | none | Die Entität *'Entitätsname'* wurde nicht gefunden. | Die Entität, für die der Vorgang ausgeführt werden soll, wurde nicht gefunden. | Überprüfen Sie, ob die Entität vorhanden ist, und wiederholen Sie den Vorgang. |
| Nicht gefunden | none | Nicht gefunden. Der Vorgang ist nicht vorhanden. | Der Vorgang, den Sie ausführen möchten, ist nicht vorhanden. | Überprüfen Sie den Vorgang, und versuchen Sie es noch mal. |
| Nicht gefunden | none | Die eingehende Anforderung wird nicht als PUT-Anforderung für eine Namespacerichtlinie erkannt. | Der eingehende Anforderungstext ist NULL und kann daher nicht als PUT-Anforderung ausgeführt werden. | Überprüfen Sie den Anforderungstext, um sicherzustellen, dass er nicht NULL ist. | 
| Nicht gefunden | none | Die Messagingentität *'Entitätsname'* wurde nicht gefunden. | Die Entität, für die Sie den Vorgang ausführen möchten, wurde nicht gefunden. | Überprüfen Sie, ob die Entität vorhanden ist, und wiederholen Sie den Vorgang. |

## <a name="error-code-internal-server-error"></a>Fehlercode: Interner Serverfehler

Diese Fehlerklasse gibt an, dass ein interner Serverfehler aufgetreten ist.

| Fehlercode | Fehler-SubCode | Fehlermeldung | BESCHREIBUNG | Empfehlung |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Interner Serverfehler | 50000 | SubCode=50000. Interner Serverfehler| Dies kann aus verschiedenen Gründen geschehen. Einige der Symptome sind: <ul> <li> Clientanforderung/Text ist beschädigt und führt zu einem Fehler. </li> <li> Timeout bei der Clientanforderung aufgrund von Verarbeitungsproblemen für den Dienst. </li> </ul> | Problembehebung: <ul> <li> Stellen Sie sicher, dass die Anforderungsparameter nicht NULL oder falsch formatiert sind. </li> <li> Wiederholen Sie die Anforderung. </li> </ul> |

## <a name="error-code-unauthorized"></a>Fehlercode: Nicht autorisiert

Diese Fehlerklasse gibt an, dass keine Autorisierung zum Ausführen des Befehls vorhanden ist.

| Fehlercode | Fehler-SubCode | Fehlermeldung | BESCHREIBUNG | Empfehlung |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nicht autorisiert | none | Ungültiger Vorgang für den sekundären Namespace. Der sekundäre Namespace ist schreibgeschützt. | Der Vorgang wurde für den sekundären Namespace ausgeführt, der als schreibgeschützter Namespace eingerichtet ist. | Wiederholen Sie den Befehl für den primären Namespace. Weitere Informationen zum [sekundären Namespace](service-bus-geo-dr.md) |
| Nicht autorisiert | none | MissingToken: Der Autorisierungsheader wurde nicht gefunden. | Dieser Fehler tritt auf, wenn die Autorisierung NULL oder falsche Werte aufweist. | Stellen Sie sicher, dass der im Autorisierungsheader angegebene Tokenwert richtig und nicht NULL ist. |
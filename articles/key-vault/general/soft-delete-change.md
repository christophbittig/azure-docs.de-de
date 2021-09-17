---
title: 'Aktivieren des vorläufigen Löschens für alle Schlüsseltresorobjekte: Azure Key Vault | Microsoft-Dokumentation'
description: Verwenden Sie die Informationen in diesem Dokument, um das vorläufige Löschen für alle Schlüsseltresore einzuführen und Anwendungs- und Verwaltungsänderungen vorzunehmen und so Konfliktfehler zu vermeiden.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 45eea8f8809e97f7e424a988140bcf76b47d5947
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609284"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Vorläufiges Löschen wird für alle Schlüsseltresore aktiviert

> [!WARNING]
> Breaking Change: Die Option zum Abwählen des vorläufigen Löschens wird bald eingestellt. Azure Key Vault-Benutzer und -Administratoren sollten das vorläufige Löschen für ihre Schlüsseltresore sofort aktivieren.
>
> Für verwaltetes HSM von Azure Key Vault ist das vorläufige Löschen standardmäßig aktiviert und kann nicht deaktiviert werden.

Wenn ein Geheimnis aus einem Schlüsseltresor ohne vorläufigen Löschschutz gelöscht wird, wird das Geheimnis endgültig gelöscht. Derzeit haben Benutzer die Möglichkeit, das vorläufige Löschen beim Erstellen eines Schlüsseltresors abzuwählen. In Kürze wird Microsoft aber für alle Schlüsseltresore den Schutz durch vorläufiges Löschen aktivieren, um zu verhindern, dass Geheimnisse von Benutzern versehentlich oder böswillig gelöscht werden. Benutzer haben dann nicht mehr die Möglichkeit, das vorläufige Löschen abzuwählen bzw. zu deaktivieren.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagramm: Löschen eines Schlüsseltresors mit und ohne Schutz durch vorläufiges Löschen":::

Vollständige Details zur Funktion für vorläufiges Löschen finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Funktioniert meine Anwendung mit aktiviertem vorläufigem Löschen?

> [!Important] 
> Lesen Sie sich die folgenden Informationen sorgfältig durch, bevor Sie vorläufiges Löschen für Ihre Schlüsseltresore aktivieren.

Schlüsseltresornamen sind global eindeutig. Die Namen von Geheimnissen, die in einem Schlüsseltresor gespeichert sind, sind ebenfalls eindeutig. Der Name eines Schlüsseltresors oder Schlüsseltresorobjekts im vorläufig gelöschten Zustand kann nicht wiederverwendet werden. 

Wenn von Ihrer Anwendung beispielsweise programmgesteuert ein Schlüsseltresor mit dem Namen „Tresor A“ erstellt und später dann gelöscht wird, wird er in den vorläufig gelöschten Zustand versetzt. Von Ihrer Anwendung kann kein weiterer Schlüsseltresor mit dem Namen „Vault A“ erstellt werden, bis der vorläufig gelöschte Schlüsseltresor endgültig gelöscht wurde. 

Wenn Ihre Anwendung einen Schlüssel mit dem Namen `test key` im Schlüsseltresor A erstellt und den Schlüssel später aus Tresor A löscht, kann von Ihrer Anwendung außerdem erst dann ein neuer Schlüssel mit dem Namen `test key` im Schlüsseltresor A erstellt werden, nachdem das vorläufig gelöschte Objekt `test key` endgültig gelöscht wurde. 

Wenn Sie versuchen, ein Schlüsseltresorobjekt zu löschen und es mit demselben Namen neu zu erstellen, ohne es zuerst endgültig zu löschen, können Konfliktfehler auftreten. Diese Fehler können zum Fehlschlagen Ihrer Anwendungen oder der Automatisierung führen. Wenden Sie sich an Ihr Entwicklungsteam, bevor Sie die folgenden erforderlichen Anwendungs- und Verwaltungsänderungen vornehmen. 

### <a name="application-changes"></a>Anwendungsänderungen

Wenn von Ihrer Anwendung die Deaktivierung des vorläufiges Löschens vorausgesetzt und erwartet wird, dass gelöschte Geheimnis- oder Schlüsseltresornamen für die sofortige Wiederverwendung verfügbar sind, müssen Sie die folgenden Änderungen an Ihrer Anwendungslogik vornehmen.

1. Löschen Sie den ursprünglichen Schlüsseltresor oder das Geheimnis.
1. Endgültiges Löschen des Schlüsseltresors oder Geheimnisses im vorläufig gelöschten Zustand.
1. Warten Sie, bis das endgültige Löschen abgeschlossen ist. Die sofortige Neuerstellung kann zu einem Konflikt führen.
1. Erstellen Sie den Schlüsseltresor erneut mit demselben Namen.
1. Falls während des Erstellungsvorgangs weiterhin ein Namenskonflikt auftritt, sollten Sie versuchen, den Schlüsseltresor neu zu erstellen. Die Aktualisierung von Azure DNS-Datensätzen kann maximal zehn Minuten dauern.

### <a name="administration-changes"></a>Verwaltungsänderungen

Sicherheitsprinzipalen, die Zugriff benötigen, um Geheimnisse endgültig zu löschen, müssen zusätzliche Zugriffsrichtlinienberechtigungen gewährt werden, damit diese Geheimnisse und der Schlüsseltresor endgültig gelöscht werden können.

Deaktivieren Sie alle Azure Policy-Zuweisungen für Ihre Schlüsseltresore, in denen festgelegt ist, dass das vorläufige Löschen deaktiviert werden soll. Unter Umständen müssen Sie dieses Problem an einen Administrator eskalieren, der die auf Ihre Umgebung angewendeten Azure Policy-Zuweisungen kontrolliert. Wenn diese Richtlinienzuweisung nicht deaktiviert wird, verlieren Sie ggf. die Möglichkeit, neue Schlüsseltresore im Gültigkeitsbereich der angewendeten Richtlinienzuweisung zu erstellen.

Wenn für Ihre Organisation gesetzliche Complianceanforderungen gelten und nicht zugelassen werden kann, dass gelöschte Schlüsseltresore und Geheimnisse über einen längeren Zeitraum in einem wiederherstellbaren Zustand verbleiben, müssen Sie den Aufbewahrungszeitraum für das vorläufige Löschen anpassen, um die Standards Ihrer Organisation zu erfüllen. Sie können für den Aufbewahrungszeitraum eine Dauer von 7 bis 90 Tagen konfigurieren.

## <a name="procedures"></a>Prozeduren

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Überwachen Ihrer Schlüsseltresore, um zu überprüfen, ob vorläufiges Löschen aktiviert ist

1. Melden Sie sich beim Azure-Portal an.
1. Suchen Sie nach **Azure Policy**.
1. Wählen Sie **Definitionen** aus.
1. Wählen Sie unter **Kategorie** im Filter die Option **Key Vault** aus.
1. Wählen Sie die Richtlinie **Für Key Vault sollte vorläufiges Löschen aktiviert sein** aus.
1. Wählen Sie **Zuweisen** aus.
1. Legen Sie den Gültigkeitsbereich auf Ihr Abonnement fest.
1. Stellen Sie sicher, dass die Auswirkung der Richtlinie auf **Überwachung** festgelegt ist.
1. Klicken Sie auf **Überprüfen + erstellen**. Es kann bis zu 24 Stunden dauern, bis die vollständige Überprüfung Ihrer Umgebung abgeschlossen ist.
1. Wählen Sie im Bereich **Azure Policy** die Option **Konformität** aus.
1. Wählen Sie die Richtlinie aus, die Sie angewendet haben.

Sie können jetzt filtern und ermitteln, für welche Schlüsseltresore vorläufiges Löschen aktiviert ist (konforme Ressourcen) und für welche Schlüsseltresore dies nicht der Fall ist (nicht konforme Ressourcen).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Aktivieren des vorläufigen Löschens für einen vorhandenen Schlüsseltresor

1. Melden Sie sich beim Azure-Portal an.
1. Suchen Sie nach Ihrem Schlüsseltresor.
1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Vorläufiges Löschen** die Option **Wiederherstellung dieses Tresors und zugehöriger Objekte aktivieren** aus.
1. Legen Sie den Aufbewahrungszeitraum für vorläufiges Löschen fest.
1. Wählen Sie **Speichern** aus.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Gewähren von „Endgültig löschen“-Zugriffsrichtlinienberechtigungen für einen Sicherheitsprinzipal

1. Melden Sie sich beim Azure-Portal an.
1. Suchen Sie nach Ihrem Schlüsseltresor.
1. Wählen Sie unter **Einstellungen** die Option **Zugriffsrichtlinien** aus.
1. Wählen Sie den Dienstprinzipal aus, dem Sie den Zugriff gewähren möchten.
1. Gehen Sie die Dropdownmenüs unter **Schlüssel**, **Geheimnis** und **Zertifikatberechtigungen** durch, bis **Privilegierte Vorgänge** angezeigt wird. Wählen Sie die Berechtigung **Bereinigen** aus.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="does-this-change-affect-me"></a>Betrifft mich diese Änderung?

Wenn Sie vorläufiges Löschen bereits aktiviert haben oder Schlüsseltresorobjekte nicht löschen und unter demselben Namen neu erstellen, werden Sie wahrscheinlich keine Änderung beim Verhalten des Schlüsseltresors bemerken.

Falls Sie eine Anwendung besitzen, die häufig Schlüsseltresorobjekte löscht und mit denselben Benennungskonventionen neu erstellt, müssen Sie Änderungen an Ihrer Anwendungslogik vornehmen, um das erwartete Verhalten zu gewährleisten. Weitere Informationen finden Sie im Abschnitt [Anwendungsänderungen](#application-changes) dieses Artikels.

### <a name="how-do-i-benefit-from-this-change"></a>Wie profitiere ich von dieser Änderung?

Der Schutz durch vorläufiges Löschen ist für Ihre Organisation ein zusätzlicher Schutz vor versehentlichen oder böswilligen Löschvorgängen. Als Schlüsseltresoradministrator können Sie den Zugriff sowohl auf Wiederherstellungsberechtigungen als auch auf Berechtigungen zum endgültigen Löschen einschränken.

Wenn ein Benutzer versehentlich einen Schlüsseltresor oder ein Geheimnis löscht, können Sie ihm Zugriffsberechtigungen erteilen, damit er das Geheimnis selbst wiederherstellen kann. Hierbei besteht kein Risiko, dass er das Geheimnis oder den Schlüsseltresor endgültig löscht. Dieser Self-Service-Prozess minimiert Ausfallzeiten in Ihrer Umgebung und stellt die Verfügbarkeit Ihrer Geheimnisse sicher.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Gewusst wie: Muss ich Maßnahmen ergreifen?

Führen Sie die Schritte im Abschnitt [Überwachen Ihrer Schlüsseltresore, um zu überprüfen, ob vorläufiges Löschen aktiviert ist](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) dieses Artikels aus. Diese Änderung wirkt sich auf alle Schlüsseltresore aus, für die das vorläufige Löschen nicht aktiviert ist.

### <a name="what-action-do-i-need-to-take"></a>Welche Maßnahmen muss ich ergreifen?

Aktivieren Sie das vorläufige Löschen für Ihre gesamten Schlüsseltresore, nachdem Sie sich vergewissert haben, dass Sie keine Änderungen an Ihrer Anwendungslogik vornehmen müssen.

### <a name="when-do-i-need-to-take-action"></a>Wann muss ich Maßnahmen ergreifen?

Um sicherzustellen, dass Ihre Anwendungen nicht betroffen sind, sollten Sie das vorläufige Löschen so bald wie möglich für Ihre Schlüsseltresore aktivieren.

## <a name="next-steps"></a>Nächste Schritte

- Falle Sie Fragen zu dieser Änderung haben, können Sie sich unter [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) an uns wenden.
- Lesen Sie [Vorläufiges Löschen: Übersicht](soft-delete-overview.md).

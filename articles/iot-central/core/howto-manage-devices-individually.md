---
title: Verwalten einzelner Geräte in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Hier finden Sie Informationen zum Verwalten einzelner Geräte in Ihrer Azure IoT Central-Anwendung. Sie erfahren, wie Sie Geräte erstellen, löschen und aktualisieren.
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0ca2190bf5994edec56dce638d7361d90d2da2de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810670"
---
# <a name="manage-individual-devices-in-your-azure-iot-central-application"></a>Verwalten einzelner Geräte in Ihrer Azure IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie Geräte in Ihrer Azure IoT Central-Anwendung verwalten. Sie haben folgende Möglichkeiten:

- Mit der **Geräte**-Seite Geräte anzeigen, hinzufügen und löschen, die mit Ihrer Azure IoT Central-Anwendung verbunden sind.
- Halten Sie Ihre Gerätemetadaten durch Ändern der in den Geräteeigenschaften gespeicherten Werte in Ihren Ansichten auf dem neuesten Stand.
- Steuern Sie das Verhalten Ihrer Geräte, indem Sie eine Einstellung auf einem bestimmten Gerät in Ihren Ansichten aktualisieren.

Informationen zum Verwalten von benutzerdefinierten Gruppen von Geräten finden Sie im [Tutorial: Verwenden von Gerätegruppen zum Analysieren von Gerätetelemetriedaten](tutorial-use-device-groups.md).

## <a name="view-your-devices"></a>Anzeigen von Geräten

So zeigen Sie ein einzelnes Gerät an:

1. Wählen Sie im linken Bereich **Geräte** aus. Hier sehen Sie eine Liste Ihrer Gerätevorlagen und eine Liste aller Geräte, auf die Ihre Organisation zugreifen kann.

1. Wählen Sie eine Gerätevorlage aus.

1. Im rechten Bereich der Seite **Geräte** sehen Sie eine Liste von Geräten, die aus dieser Gerätevorlage erstellt wurden und auf die Ihre Organisation zugreifen kann. Wählen Sie ein einzelnes Gerät aus, um die Seite „Gerätedetails“ für dieses Gerät anzuzeigen:

    :::image type="content" source="media/howto-manage-devices-individually/device-list.png" alt-text="Screenshot: Geräteliste":::

    > [!TIP]
    > Mithilfe des Filtertools auf dieser Seite können Sie Geräte in einer bestimmten Organisation anzeigen.

## <a name="add-a-device"></a>Hinzufügen eines Geräts

So fügen Sie ein Gerät Ihrer Azure IoT Central-Anwendung hinzu:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie die Gerätevorlage aus, von der Sie ein Gerät erstellen möchten.

1. Wählen Sie **+ Neu** aus.

1. Geben Sie einen Gerätenamen und eine ID ein, oder übernehmen Sie die Standardwerte. Der Gerätename darf maximal 148 Zeichen lang sein. Die Geräte-ID darf maximal 128 Zeichen lang sein.

1. Schalten Sie die Umschaltfläche **Simuliert** auf **Ein** oder **Aus**. Ein reales Gerät steht für ein physisches Gerät, dessen Verbindung mit der Azure IoT Central-Anwendung Sie herstellen. Ein simuliertes Gerät enthält Beispieldaten, die von Azure IoT Central für Sie generiert werden.

1. Wenn Ihre Anwendung mit Organisationen arbeitet, wählen Sie die Organisation aus, zu der das Gerät gehört.

    > [!TIP]
    > Sie können festlegen, dass in der Dropdownliste der Organisation eine [Standardorganisation](howto-create-organizations.md#default-organization) angezeigt wird.

1. Wählen Sie **Erstellen** aus.

1. Dieses Gerät wird jetzt in der Geräteliste für diese Vorlage angezeigt. Wählen Sie das Gerät aus, um die Seite „Gerätedetails“ anzuzeigen, die alle Ansichten für das Gerät enthält.

## <a name="change-organization"></a>Ändern der Organisation

Zum Verschieben eines Geräts in eine andere Organisation müssen Sie sowohl auf die Quell- als auch auf die Zielorganisation zugreifen können. So verschieben Sie ein Gerät:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie in der Geräteliste das Gerät aus, das verschoben werden soll.

1. Klicken Sie auf **Unternehmen**.

1. Wählen Sie die neue Organisation für das Gerät aus:

    :::image type="content" source="media/howto-manage-devices-individually/change-device-organization.png" alt-text="Screenshot: Verschieben eines Geräts in eine neue Organisation":::

1. Wählen Sie **Speichern** aus.

## <a name="migrate-devices-to-a-template"></a>Migrieren von Geräten zu einer Vorlage

Wenn Sie Geräte registrieren, indem Sie den Import unter **Alle Geräte** starten, dann werden die Geräte ohne Gerätevorlagenzuordnung erstellt. Geräte müssen mit einer Vorlage verknüpft sein, damit ein Zugriff auf Daten und andere Details zum Gerät möglich ist. Führen Sie die folgenden Schritte aus, um Geräte mit einer Vorlage zu verknüpfen:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie im linken Bereich die Option **Alle Geräte** aus:

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-1.png" alt-text="Screenshot: Nicht zugeordnete Geräte":::

1. Verwenden Sie den Filter im Raster, um festzustellen, ob der Wert in der Spalte **Gerätevorlage** für eines Ihrer Geräte **Nicht zugeordnet** lautet.

1. Wählen Sie die Geräte aus, die Sie mit einer Vorlage verknüpfen möchten:

1. Wählen Sie **Migrieren** aus:

    :::image type="content" source="media/howto-manage-devices-individually/unassociated-devices-2.png" alt-text="Screenshot: Zuordnen eines Geräts":::

1. Wählen Sie eine verfügbare Vorlage in der Liste aus, und wählen Sie **Migrieren** aus.

1. Die ausgewählten Geräte werden mit der ausgewählten Gerätevorlage verknüpft.

## <a name="delete-a-device"></a>Löschen eines Mediums

So löschen Sie entweder ein reales oder simuliertes Gerät aus Ihrer Azure IoT Central-Anwendung:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie die Gerätevorlage des Geräts aus, das Sie löschen möchten.

1. Verwenden Sie die Filtertools, um Ihre Geräte zu filtern und zu suchen. Aktivieren Sie das Kontrollkästchen neben den zu löschenden Geräten.

1. Wählen Sie **Löschen** aus. Sie können den Status dieses Löschvorgangs im Vorgangspanel des Geräts verfolgen.

## <a name="change-a-property"></a>Ändern einer Eigenschaft

Cloudeigenschaften sind die dem Gerät zugeordneten Gerätemetadaten, z.B. der Ort und die Seriennummer. Cloudeigenschaften sind nur in der IoT Central-Anwendung vorhanden und werden nicht mit Ihren Geräten synchronisiert. Beschreibbare Eigenschaften steuern das Verhalten eines Geräts und ermöglichen Ihnen, den Zustand eines Geräts remote festzulegen, indem Sie z. B. die Zieltemperatur eines Thermostats angeben.  Geräteeigenschaften werden vom Gerät festgelegt und sind in Azure IoT Central schreibgeschützt. Sie können Eigenschaften in der Ansicht **Gerätedetails** anzeigen und aktualisieren.

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie die Gerätevorlage des Geräts aus, dessen Eigenschaften Sie ändern möchten, und das Zielgerät.

1. Wählen Sie die Ansicht, die Eigenschaften für Ihr Gerät enthält. In dieser Ansicht können Sie Werte eingeben und oben auf der Seite **Speichern** auswählen. Hier sehen Sie alle Eigenschaften Ihres Geräts und ihre aktuellen Werte. Cloudeigenschaften und beschreibbare Eigenschaften haben bearbeitbare Felder, Geräteeigenschaften sind schreibgeschützt. Bei beschreibbaren Eigenschaften sehen Sie deren Synchronisationsstatus am unteren Rand des Feldes.

1. Ändern Sie die Eigenschaften in die benötigten Werte. Sie können mehrere Eigenschaften auf einmal ändern und alle gleichzeitig aktualisieren.

1. Wählen Sie **Speichern** aus. Wenn Sie beschreibbare Eigenschaften gespeichert haben, werden die Werte an Ihr Gerät gesendet. Wenn das Gerät die Änderung für die beschreibbare Eigenschaft bestätigt, wird der Status wieder auf **synchronisiert** gesetzt. Wenn Sie eine Cloudeigenschaft gespeichert haben, wird der Wert aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, wie Sie einzelne Geräte verwalten, können Sie sich als Nächstes mit dem [Verwalten von Geräten per Massenvorgang in Ihrer Azure IoT Central-Anwendung](howto-manage-devices-in-bulk.md) vertraut machen.

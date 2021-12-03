---
title: Integrieren von ClearPass in Microsoft Defender für IoT
description: In diesem Tutorial erfahren Sie, wie Sie Microsoft Defender für loT in ClearPass integrieren.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 536e45171d8769980080d81c59c9f07192addab0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343627"
---
# <a name="tutorial-integrate-clearpass-with-microsoft-defender-for-iot"></a>Tutorial: Integrieren von ClearPass in Microsoft Defender für IoT

In diesem Tutorial lernen Sie, wie Sie ClearPass Policy Manager (CPPM) in Microsoft Defender für IoT integrieren.

Die Defender für IoT-Plattform bietet eine kontinuierliche ICS-Bedrohungsüberwachung und -Geräteermittlung, die eine Kombination von profunden Kenntnissen über Industrieprotokolle, Geräten und Anwendungen mit ICS-spezifischer Erkennung von Verhaltensanomalie, Threat Intelligence, Risikoanalysen und automatisierter Bedrohungsmodellierung darstellt.

Defender für IoT erkennt, ermittelt und klassifiziert OT- und ICS-Endpunkte und gibt Informationen unter Verwendung des ClearPass Security Exchange-Frameworks und der offenen API direkt mit ClearPass frei.

Defender für IoT aktualisiert die ClearPass Policy Manager-Endpunktdatenbank automatisch mit Endpunktklassifizierungs-Daten und mehreren benutzerdefinierten Sicherheitsattributen.

Die Integration ermöglicht Folgendes:

- Anzeigen von ICS- und SCADA-Sicherheitsbedrohungen, die von Defender für IoT-Sicherheits-Engines identifiziert werden.

- Anzeigen der vom Defender für IoT-Sensor gefundenen Gerätebestandsinformationen. Der Sensor bietet zentralisierte Sichtbarkeit aller Netzwerkgeräte und Endpunkte in der IT- und OT-Infrastruktur. Von hier aus können eine zentralisierte Endpunkt- und Edgesicherheitsrichtlinie im ClearPass-System definiert und verwaltet werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Erstellen eines ClearPass-API-Benutzers
> - Erstellen eines ClearPass-Operatorprofils
> - Erstellen eines ClearPass-OAuth-API-Clients
> - Konfigurieren von Defender für IoT für die ClearPass-Integration
> - Definieren der ClearPass-Weiterleitungsregel
> - Überwachen der Kommunikation zwischen ClearPass und Defender für IoT

## <a name="prerequisites"></a>Voraussetzungen

### <a name="aruba-clearpass-requirements"></a>Anforderungen an Aruba ClearPass

CPPM wird auf Hardwaregeräten mit vorinstallierter Software oder als virtueller Computer unter den folgenden Hypervisoren ausgeführt. Auf einem Clientcomputer wie VMware Player ausgeführte Hypervisoren werden nicht unterstützt.

- VMware ESXi 5.5, 6.0, 6.5, 6.6 oder höher.

- Microsoft Hyper-V Server 2012 R2 oder 2016 R2.

- Hyper-V auf Microsoft Windows Server 2012 R2 oder 2016 R2.

- KVM auf CentOS 7.5 oder höher.

### <a name="defender-for-iot-requirements"></a>Defender für IoT-Anforderungen

- Defender für IoT Version 2.5.1 oder höher.

- Ein Azure-Konto. Wenn Sie noch nicht über ein Azure-Konto verfügen, können Sie [noch heute Ihr kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/).

## <a name="create-a-clearpass-api-user"></a>Erstellen eines ClearPass-API-Benutzers

Als Teil des Kommunikationskanals zwischen den beiden Produkten verwendet Defender für IoT viele APIs (sowohl TIPS als auch REST). Der Zugriff auf die TIPS-APIs wird anhand der Anmeldeinformationenkombination aus Benutzername und Kennwort überprüft. Diese Benutzer-ID muss über ein Minimum an Zugriffsebenen verfügen. Verwenden Sie kein Superadministratorprofil, sondern den API-Administrator, wie unten gezeigt.

**So erstellen Sie einen ClearPass-API-Benutzer**:

1. Wählen Sie im linken Bereich **Verwaltung** > **Benutzer und Berechtigungen** und dann **HINZUFÜGEN** aus.

1. Legen Sie im Dialogfeld **Administratorbenutzer hinzufügen** die folgenden Parameter fest:

    :::image type="content" source="media/tutorial-clearpass/policy-manager.png" alt-text="Screenshot der Dialogfeldansicht des Administratorbenutzers.":::

    | Parameter | BESCHREIBUNG |
    |--|--|
    | **UserID** | Geben Sie die Benutzer-ID ein. |
    | **Name** | Geben Sie den Benutzernamen ein. |
    | **Kennwort** | Geben Sie das Kennwort ein. |
    | **Benutzer aktivieren** | Vergewissern Sie sich, dass diese Option aktiviert ist. |
    | **Berechtigungsstufe** | Wählen Sie **API-Administrator** aus. |

1. Wählen Sie **Hinzufügen**.

## <a name="create-a-clearpass-operator-profile"></a>Erstellen eines ClearPass-Operatorprofils

Defender für IoT verwendet die REST-API als Teil der Integration. REST-APIs werden unter einem OAuth-Framework authentifiziert. Für die Synchronisierung mit Defender für IoT müssen Sie einen API-Client erstellen.

Um den Zugriff auf die REST-API für den API-Client zu schützen, erstellen Sie ein eingeschränktes Zugriffsoperatorprofil.

**So erstellen Sie ein ClearPass-Operatorprofil**:

1. Navigieren Sie zum Fenster **Operatorprofil bearbeiten**.

1. Legen Sie mit Ausnahme der folgenden Optionen alle Optionen auf **Kein Zugriff** fest:

| Parameter | BESCHREIBUNG |
|--|--|
| **API-Dienste** | Auf **Zugriff zulassen** festlegen |
| **Richtlinien-Manager** | Legen Sie Folgendes fest: <br />- **Wörterbücher**: **Attribute** auf **Lesen, Schreiben, Löschen** festlegen<br />- **Wörterbücher**: **Fingerabdruckset** auf **Lesen, Schreiben, Löschen** festlegen<br />- **Identität**: **Endpunkte** auf **Lesen, Schreiben, Löschen** festlegen |

:::image type="content" source="media/tutorial-clearpass/api-profile.png" alt-text="Screenshot des Bearbeitungsoperatorprofils.":::

:::image type="content" source="media/tutorial-clearpass/policy.png" alt-text="Screenshot der Optionen auf dem Bildschirm „Richtlinien-Manager“.":::

## <a name="create-a-clearpass-oauth-api-client"></a>Erstellen eines ClearPass-OAuth-API-Clients

1. Wählen Sie im Hauptfenster **Administrator** > **API-Dienste** > **API-Clients** aus.

1. Legen Sie auf der Registerkarte „API-Client erstellen“ die folgenden Parameter fest:

    - **Betriebsmodus**: Dieser Parameter wird für API-Aufrufe von ClearPass verwendet. Wähen Sie **ClearPass-REST-API – Client** aus.

    - **Operatorprofil**: Verwenden Sie das Profil, das Sie zuvor erstellt haben.

    - **Gewährungstyp**: Legen Sie **Clientanmeldeinformationen (Gewährungstyp = Clientanmeldeinformationen)** fest.

1. Stellen Sie sicher, dass Sie den **Geheimen Clientschlüssel** und die Client-ID aufzeichnen. Beispiel: `defender-rest`.

    :::image type="content" source="media/tutorial-clearpass/aruba.png" alt-text="Screenshot der Registerkarte „API-Client erstellen“.":::

1. Stellen Sie im Richtlinien-Manager sicher, dass Sie die folgende Liste von Informationen gesammelt haben, bevor Sie mit dem nächsten Schritt fortfahren.

    - CPPM-UserID

    - CPPM-UserId-Kennwort

    - CPPM-OAuth2-API-Client-ID

    - CPPM-OAuth2-API: Geheimer Clientschlüssel

## <a name="configure-defender-for-iot-to-integrate-with-clearpass"></a>Konfigurieren von Defender für IoT für die ClearPass-Integration

Um die Anzeige des Gerätebestands in ClearPass zu aktivieren, müssen Sie die Defender für IoT-ClearPass-Synchronisierung einrichten. Wenn die Synchronisierungskonfiguration abgeschlossen ist, aktualisiert die Defender für IoT-Plattform die ClearPass Policy Manager EndpointDb, wenn neue Endpunkte gefunden werden.

**So konfigurieren Sie die ClearPass-Synchronisierung auf dem Defender für IoT-Sensor**:

1. Wählen Sie im Defender für IoT-Sensor im linken Bereich die Option **Systemeinstellungen** aus.

1. Wählen Sie im Bereich **Systemeinstellungen** :::image type="content" source="media/tutorial-clearpass/clearpass-icon.png" alt-text="Screenshot des ClearPass-Symbols auf der linken Seite"::: aus.

1. Legen Sie die folgenden Parameter fest:

    :::image type="content" source="media/tutorial-clearpass/settings.png" alt-text="Screenshot: Ausfüllen der erforderlichen Informationen im Bereich „Systemeinstellungen“.":::

    - **Synchronisierung aktivieren:** Aktivieren der Synchronisierung zwischen Defender für IoT und ClearPass

    - **Synchronisierungshäufigkeit:** Definieren Sie die Synchronisierungshäufigkeit in Minuten. Der Standardwert ist 60 Sekunden. Der Mindestwert ist 5 Minuten.

    - **ClearPass-IP-Adresse:** Die IP-Adresse des ClearPass-Systems, mit dem Defender für IoT synchronisiert ist.

    - **Client-ID:** Die Client-ID, die in ClearPass für die Synchronisierung der Daten mit Defender für IoT erstellt wurde.

    - **Geheimer Clientschlüssel:** Der geheime Clientschlüssel, der in ClearPass für die Synchronisierung der Daten mit Defender für IoT erstellt wurde.

    - **Benutzername:** Der ClearPass-Administratorbenutzer.

    - **Kennwort:** Das ClearPass-Administratorkennwort.

1. Wählen Sie **Speichern** aus.

## <a name="define-a-clearpass-forwarding-rule"></a>Definieren einer ClearPass-Weiterleitungsregel

Sie müssen die Weiterleitungsregel festlegen, um die Anzeige der von Defender für IoT in Aruba gefundenen Warnungen zu aktivieren. Diese Regel definiert, welche Informationen zu den ICS- und SCADA-Sicherheitsbedrohungen, die von Defender für IoT-Sicherheits-Engines identifiziert werden, an ClearPass gesendet werden.

**So definieren Sie eine ClearPass-Weiterleitungsregel auf dem Defender für IoT-Sensor**:

1. Wählen Sie im Defender für IoT-Sensor im linken Bereich die Option **Weiterleitung** aus.

1. Wählen Sie im Bereich **Weiterleitung** die Option **Weiterleitungsregel erstellen** aus.

    :::image type="content" source="media/tutorial-clearpass/forwarding.png" alt-text="Screenshot: Bereich „Weiterleitung“ mit allen Optionen.":::

1. Fügen Sie den Namen und den Schweregrad der Regel hinzu, und wählen Sie dann in der Dropdownliste **Aktion** die Option **Senden an** > **ClearPass** aus.

    :::image type="content" source="media/tutorial-clearpass/rule.png" alt-text="Screenshot des Erstellens einer „Weiterleitungsregel“.":::

1. Legen Sie im Bereich **Aktionen** die folgenden Parameter fest:

    :::image type="content" source="media/tutorial-clearpass/actions.png" alt-text="Wählen Sie Ihre Aktionen im Bereich „Aktionen“ aus.":::

    | Parameter | BESCHREIBUNG |
    |--|--|
    | **Host** | Geben Sie die IP-Adresse des ClearPass-Servers ein. |
    | **Port** | Geben Sie den Port der ClearPass-Instanz ein, über den die Weiterleitung erfolgt. |
    | **Konfigurieren** | Richten Sie die folgenden Optionen ein, um die Anzeige von Defender für IoT-Warnungen im ClearPass-System zu ermöglichen: <br />- **Ungültige Funktionscodes melden:** Protokollverletzungen: Ungültiger Feldwert, der gegen die ICS-Protokollspezifikation verstößt (potenzieller Exploit).<br />- **Nicht autorisierte PLC-Programmierung/Firmwareupdates melden:** Nicht autorisierte PLC-Änderungen.<br />- **Nicht autorisierte PLC-Beendigung melden:** PLC-Beendigung (Downtime).<br />- **Warnungen zu Schadsoftware melden:** Angriffsversuche durch industrielle Schadsoftware (TRITON, NotPetya usw.).<br />- **Nicht autorisierte Überprüfung melden:** Nicht autorisierte Überprüfung (potenzielle Aufklärung). |

1. Klicken Sie auf **Senden**.

## <a name="monitor-clearpass-and-defender-for-iot-communication"></a>Überwachen der Kommunikation zwischen ClearPass und Defender für IoT

Nachdem die Synchronisierung gestartet wurde, wird die EndpointDb des Richtlinien-Managers direkt mit Endpunktdaten gefüllt. Sie können den Zeitpunkt der letzten Aktualisierung auf dem Bildschirm für die Integrationskonfiguration anzeigen.

**So überprüfen Sie den Zeitpunkt der letzten Synchronisierung mit ClearPass**:

1. Melden Sie sich am Defender für loT Sensor an.

1. Wählen Sie im linken Bereich **Systemeinstellungen** aus.

1. Wählen Sie **ClearPass** aus.

    :::image type="content" source="media/tutorial-clearpass/last-sync.png" alt-text="Screenshot der Ansicht von Uhrzeit und Datum Ihrer letzten Synchronisierung.":::

Wenn die Synchronisierung nicht funktioniert oder ein Fehler angezeigt wird, haben Sie wahrscheinlich einige der Informationen nicht erfasst. Überprüfen Sie die aufgezeichneten Daten erneut. Darüber hinaus können Sie die API-Aufrufe zwischen Defender für IoT und ClearPass über **Gast** > **Administration** > **Support** > **Anwendungsprotokoll** anzeigen.

Im Folgenden finden Sie ein Beispiel für API-Protokolle zwischen Defender für IoT und ClearPass.

:::image type="content" source="media/tutorial-clearpass/log.png" alt-text="Screenshot von API-Protokollen zwischen Defender für IoT und ClearPass.":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mit der ClearPass-Integration beginnen können. Informieren Sie sich nun über CyberArk.

> [!div class="nextstepaction"]
> [Schaltfläche „Nächste Schritte“](./tutorial-cyberark.md)

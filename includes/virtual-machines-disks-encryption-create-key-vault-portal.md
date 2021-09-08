---
title: Datei einfügen
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3e05dd70ab0919ca4429cee15ecfc9d6ac9ee627
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122261720"
---
Das erstmalige Einrichten von kundenseitig verwalteten Schlüssel für Ihre Datenträger erfordert die Erstellung von Ressourcen in einer bestimmten Reihenfolge. Zuerst müssen Sie eine Azure Key Vault-Instanz erstellen und einrichten.

## <a name="set-up-your-azure-key-vault"></a>Einrichten des Azure-Schlüsseltresors

1. Melden Sie sich beim [Azure-Portal](https://aka.ms/diskencryptionupdates) an.
1. Suchen Sie nach **Schlüsseltresore**, und wählen Sie diese Option aus.

    [![Screenshot des Azure-Portals mit erweitertem Dialogfeld für die Suche](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Ihre Azure Key Vault-Instanz, der Datenträgerverschlüsselungssatz, die VM, die Datenträger und die Momentaufnahmen müssen sich alle in derselben Region und demselben Abonnement befinden, damit die Bereitstellung erfolgreich ausgeführt werden kann.

1. Wählen Sie **+Erstellen** aus, um einen neuen Schlüsseltresor zu erstellen.
1. Erstellen Sie eine neue Ressourcengruppe.
1. Geben Sie einen Namen für den Schlüsseltresor ein, wählen Sie eine Region aus, und wählen Sie dann einen Tarif aus.

    > [!NOTE]
    > Beim Erstellen der Key Vault-Instanz müssen Sie vorläufiges Löschen und den Schutz vor endgültigem Löschen aktivieren. Durch vorläufiges Löschen wird sichergestellt, dass der Schlüsseltresor einen gelöschten Schlüssel für einen bestimmten Aufbewahrungszeitraum (standardmäßig 90 Tage) speichert. Der Schutz vor endgültigem Löschen stellt sicher, dass ein gelöschter Schlüssel erst nach Ablauf der Aufbewahrungsdauer dauerhaft gelöscht werden kann. Diese Einstellungen schützen Sie vor dem Verlust von Daten durch versehentliches Löschen. Diese Einstellungen sind obligatorisch, wenn ein Schlüsseltresor für die Verschlüsselung verwalteter Datenträger verwendet wird.

1. Wählen Sie **Bewerten + erstellen** aus, überprüfen Sie Ihre Auswahl, und wählen Sie dann **Erstellen** aus.

    ![Screenshot des Azure Key Vault-Erstellungsvorgangs. Die von Ihnen erstellten Werte werden dargestellt.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Nach Abschluss der Bereitstellung Ihres Schlüsseltresors wählen Sie diesen aus.
1. Wählen Sie unter **Einstellungen** die Option **Schlüssel** aus.
1. Wählen Sie die Option **Generieren/Importieren** aus.

    ![Screenshot des Key Vault-Bereichs für Ressourceneinstellungen. Zeigt die Schaltfläche Generieren/Importieren in „Einstellungen“ an.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Lassen Sie **Schlüsseltyp** auf **RSA** und **RSA-Schlüsselgröße** auf **2048** festgelegt.
1. Treffen Sie Ihre Auswahl für die verbleibenden Optionen nach Wunsch, und wählen Sie dann **Erstellen** aus.

    ![Screenshot des Bereichs „Schlüssel erstellen“, der nach dem Auswählen der Schaltfläche „Generieren/Importieren“ angezeigt wird](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

### <a name="add-an-azure-rbac-role"></a>Hinzufügen einer Azure RBAC-Rolle

Nachdem Sie den Azure-Schlüsseltresor und einen Schlüssel erstellt haben, müssen Sie eine Azure RBAC-Rolle hinzufügen, damit Sie Ihren Azure-Schlüsseltresor mit Ihrem Datenträgerverschlüsselungssatz verwenden können.

1. Wählen Sie **Zugriffssteuerung (IAM)** aus, und fügen Sie eine Rolle hinzu.
1. Fügen Sie die Rolle **Key Vault-Administrator,** **Besitzer** oder **Mitwirkender** hinzu.

## <a name="set-up-your-disk-encryption-set"></a>Einrichten des Datenträgerverschlüsselungssatzes

1. Suchen Sie nach **Datenträgerverschlüsselungssätze**, und wählen Sie sie aus.
1. Wählen Sie im Bereich **Datenträgerverschlüsselungssätze** die Option **+Erstellen** aus.

    ![Screenshot des Hauptbildschirms des Datenträgerverschlüsselungs-Portals. Die Schaltfläche „Hinzufügen“ ist hervorgehoben.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Wählen Sie Ihre Ressourcengruppe aus, geben Sie Ihrem Verschlüsselungssatz einen Namen, und wählen Sie dieselbe Region aus wie Ihr Schlüsseltresor.
1. Wählen Sie als **Verschlüsselungstyp** die Option **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** aus.

    > [!NOTE]
    > Nachdem Sie einen Datenträgerverschlüsselungssatz mit einem bestimmten Verschlüsselungstyp erstellt haben, kann er nicht mehr geändert werden. Wenn Sie einen anderen Verschlüsselungstyp verwenden möchten, müssen Sie einen neuen Datenträgerverschlüsselungssatz erstellen.

1. Wählen Sie die Option **Klicken Sie hier, um einen Schlüssel auszuwählen**.
1. Wählen Sie den Schlüsseltresor und den Schlüssel, die Sie zuvor erstellt haben, sowie die Version aus.
1. Drücken Sie **Auswählen**.
1. Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus.

    ![Screenshot des Bereichs zum Erstellen der Datenträgerverschlüsselung. Dargestellt sind das Abonnement, die Ressourcengruppe, der Name des Datenträgerverschlüsselungssatzes, die Region und das Auswahlelement für den Schlüsseltresor und den Schlüssel.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

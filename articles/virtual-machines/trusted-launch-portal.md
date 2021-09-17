---
title: 'Vorschau: Bereitstellen eines virtuellen Computers mit vertrauenswürdigem Start'
description: Stellen Sie einen virtuellen Computer mit vertrauenswürdigem Start bereit.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: bcc91283c29aaef251c2a18422e90090c56a6298
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688918"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Bereitstellen eines virtuellen Computers mit aktiviertem vertrauenswürdigem Start (Vorschau)

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen

Der [vertrauenswürdige Start](trusted-launch.md) ist eine Möglichkeit zur Verbesserung der Sicherheit von VMs der [Generation 2](generation-2.md). Der vertrauenswürdige Start schützt vor erweiterten und permanenten Angriffstechniken, indem Infrastrukturtechnologien wie vTPM und sicherer Start kombiniert werden.

> [!IMPORTANT]
> Der vertrauenswürdige Start befindet sich derzeit in der öffentlichen Vorschau.
>
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
>
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Bereitstellen mit dem Portal

Erstellen Sie einen virtuellen Computer, für den vertrauenswürdiger Start aktiviert ist.

1. Melden Sie sich beim Azure-[Portal](https://aka.ms/TL_preview) an.
   > [!NOTE]
   > Der Portal-Link ist für die Vorschauversion des vertrauenswürdigen Starts eindeutig.
   >
2. Suchen Sie nach **Virtuellen Computern**.
3. Wählen Sie unter **Dienste** die Option **Virtuelle Computer** aus.
4. Wählen Sie auf der Seite **Virtuelle Computer** die Option **Hinzufügen** und dann **Virtueller Computer** aus.
5. Stellen Sie sicher, dass unter **Projektdetails** das richtige Abonnement ausgewählt ist.
6. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie einen Namen für Ihre Ressourcengruppe an, oder wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus.
7. Geben Sie unter **Details zur Instanz** einen Namen für den virtuellen Computer ein, und wählen Sie eine Region aus, die den [vertrauenswürdigen Start](trusted-launch.md#public-preview-limitations) unterstützt.
8. Wählen Sie unter **Image** ein [Gen 2-Image, das den vertrauenswürdigen Start unterstützt](trusted-launch.md#public-preview-limitations), aus. Vergewissern Sie sich, dass die folgende Meldung angezeigt wird: **Dieses Image unterstützt „Vertrauenswürdiger Start (Vorschau)“. Konfigurieren Sie das Feature auf der Registerkarte „Erweitert“.**
   > [!TIP]
   > Sollte die Gen 2-Version des gewünschten Images in der Dropdownliste nicht angezeigt werden, wählen Sie **Alle Images anzeigen** aus, und ändern Sie dann den Filter **VM-Generation** so, dass nur Gen 2-Images angezeigt werden. Suchen Sie das Image in der Liste, und verwenden Sie dann die Dropdownliste **Auswählen**, um die Gen 2-Version auszuwählen.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Screenshot mit der Meldung, die bestätigt, dass dieses Gen 2-Image den vertrauenswürdigen Start unterstützt.":::

13. Wählen Sie eine VM-Größe, die den vertrauenswürdigen Start unterstützt. Die Liste der unterstützten Größen finden Sie [hier](trusted-launch.md#public-preview-limitations).
14. Geben Sie die Informationen für das **Administratorkonto** und dann die **Regeln für eingehende Ports** ein.
1. Wechseln Sie zur Registerkarte **Erweitert**, indem Sie sie oben auf der Seite auswählen.
1. Scrollen Sie nach unten zum Abschnitt **VM-Generierung**. Vergewissern Sie sich, dass **2. Generation** ausgewählt ist.
1. Scrollen Sie auf der Registerkarte **Erweitert** nach unten zu **Vertrauenswürdiger Start**, und aktivieren Sie dann das Kontrollkästchen **Vertrauenswürdiger Start**. Dadurch werden zwei weitere Optionen angezeigt: „Sicherer Start“ und „vTPM“. Wählen Sie die passenden Optionen für Ihre Bereitstellung aus.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Screenshot mit den Optionen für den vertrauenswürdigen Start.":::

15. Wählen Sie unten auf der Seite die Option **Überprüfen + erstellen** aus.
16. Auf der Seite zum **Erstellen eines virtuellen Computers** werden die Details des virtuellen Computers angezeigt, den Sie bereitstellen möchten. Klicken Sie auf **Erstellen**, wenn Sie so weit sind.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot der Validierungsseite, die anzeigt, dass die Optionen für den vertrauenswürdigen Start enthalten sind.":::


Die Bereitstellung des virtuellen Computers dauert ein paar Minuten.

## <a name="deploy-using-a-template"></a>Mithilfe einer Vorlage bereitstellen

Sie können VMs für den vertrauenswürdigen Start mithilfe einer Schnellstartvorlage bereitstellen:

**Linux**: [![Bereitstellen in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**: [![Bereitstellen in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Anzeigen und Aktualisieren

Sie können die Konfiguration für den vertrauenswürdigen Start für einen vorhandenen virtuellen Computer anzeigen, indem Sie die Seite **Übersicht** für den virtuellen Computer im Portal aufrufen.

Um die Konfiguration des vertrauenswürdigen Starts zu ändern, wählen Sie im Menü auf der linken Seite im Abschnitt **Einstellungen** die Option **Konfiguration** aus. Sie können „Sicherer Start“ und „vTPM“ im Abschnitt **Vertrauenswürdiger Start** aktivieren oder deaktivieren. Wählen Sie abschließend im oberen Bereich der Seite **Speichern** aus.

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Screenshot zum Ändern der Konfiguration für den vertrauenswürdigen Start.":::

Wenn die VM ausgeführt wird, erhalten Sie eine Meldung, dass der virtuelle Computer neu gestartet wird, um die geänderte Konfiguration für den vertrauenswürdigen Start anzuwenden. Wählen Sie **Ja** aus, und warten Sie, bis die VM neu gestartet wird, damit die Änderungen wirksam werden.


## <a name="verify-secure-boot-and-vtpm"></a>Überprüfen von sicherem Start und vTPM

Sie können überprüfen, ob sicherer Start und vTPM auf dem virtuellen Computer aktiviert sind.

### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: Überprüfen, ob der sichere Start ausgeführt wird

Stellen Sie eine SSH-Verbindung mit dem virtuellen Computer her, und führen Sie dann folgenden Befehl aus:

```bash
mokutil --sb-state
```

Wenn der sichere Start aktiviert ist, gibt der Befehl Folgendes zurück:

```bash
SecureBoot enabled
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: Überprüfen, ob vTPM aktiviert ist

Stellen Sie eine SSH-Verbindung mit Ihrem virtuellen Computer her. Überprüfen Sie, ob das tpm0-Gerät vorhanden ist:

```bash
ls /dev/tpm0
```

Wenn vTPM aktiviert ist, gibt der Befehl Folgendes zurück:

```output
/dev/tpm0
```

Wenn vTPM deaktiviert ist, gibt der Befehl Folgendes zurück:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: Überprüfen, ob der sichere Start ausgeführt wird

Stellen Sie über Remote Desktop eine Verbindung mit dem virtuellen Computer her, und führen Sie `msinfo32.exe` aus.

Überprüfen Sie im rechten Bereich, ob „SecureBoot-Status“ auf **EIN** festgelegt ist.

## <a name="enable-the-azure-security-center-experience"></a>Aktivieren der Azure Security Center-Erfahrung

Um Azure Security Center das Anzeigen von Informationen zu Ihren VMs für den vertrauenswürdigen Start zu ermöglichen, müssen Sie mehrere Richtlinien aktivieren. Die einfachste Möglichkeit, die Richtlinien zu aktivieren, ist die Bereitstellung dieser [Resource Manager-Vorlage](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) für Ihr Abonnement.

Wählen Sie die Schaltfläche unten aus, um die Richtlinien für Ihr Abonnement bereitzustellen:

[![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Die Vorlage muss nur einmal pro Abonnement bereitgestellt werden. Es werden automatisch `GuestAttestation`- und `AzureSecurity`-Erweiterungen auf allen unterstützten VMs installiert. Wenn Sie Fehlermeldungen erhalten, versuchen Sie erneut, die Vorlage bereitzustellen.

Empfehlungen für vTPM und sicheren Start für VMs für vertrauenswürdigen Start finden Sie unter [So fügen Sie eine benutzerdefinierte Initiative zu Ihrem Abonnement hinzu](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription).

## <a name="sign-things-for-secure-boot-on-linux"></a>Signierungen für den sicheren Start unter Linux

In einigen Fällen sind möglicherweise Signierungen für den sicheren UEFI-Start erforderlich.  Beispielsweise müssen Sie möglicherweise die Schritte in [How to sign things for Secure Boot](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) (Signierungen für den sicheren Start) für Ubuntu durchführen. In diesen Fällen müssen Sie die Registrierungsschlüssel des MOK-Hilfsprogramms für Ihre VM eingeben. Hierzu müssen Sie über die serielle Azure-Konsole auf das MOK-Hilfsprogramm zugreifen.

1. Aktivieren Sie die serielle Azure-Konsole für Linux. Weitere Informationen finden Sie unter [Serielle Azure-Konsole für Linux](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
1. Suchen Sie die Option **Virtuelle Computer**, und wählen Sie in der Liste Ihren virtuellen Computer (VM) aus.
1. Wählen Sie im Menü links unter **Support + Problembehandlung** die Option **Serielle Konsole** aus. Rechts wird eine Seite mit der seriellen Konsole geöffnet.
1. Melden Sie sich mit der seriellen Konsole von Azure bei der VM an. Geben Sie zur **Anmeldung** den Benutzernamen ein, den Sie beim Erstellen des virtuellen Computers verwendet haben. Beispiel: *azureuser*. Geben Sie bei entsprechender Aufforderung das Kennwort ein, das dem Benutzernamen zugeordnet ist.
1. Wenn Sie angemeldet sind, importieren Sie mit `mokutil` die `.der`-Datei mit dem öffentlichen Schlüssel.

    ```bash
    sudo mokutil –import <path to public key.der>
    ```
1. Starten Sie den Computer von der seriellen Konsole von Azure neu, indem Sie `sudo reboot` eingeben. Ein 10-Sekunden-Countdown beginnt.
1. Drücken Sie die NACH-OBEN- oder NACH-UNTEN-TASTE, um den Countdown zu unterbrechen und im UEFI-Konsolenmodus zu warten. Wenn der Timer nicht unterbrochen wird, wird der Startvorgang fortgesetzt, und alle MOK-Änderungen gehen verloren.
1. Wählen Sie im Menü des MOK-Hilfsprogramms die entsprechende Aktion aus.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Screenshot mit den verfügbaren Optionen im MOK-Verwaltungsmenü in der seriellen Konsole.":::


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über VMs für [vertrauenswürdigen Start](trusted-launch.md) und [Generation 2](generation-2.md)-VMs.

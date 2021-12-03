---
title: Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit Secure Network Communications (SNC) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Microsoft Sentinel-Datenconnector für SAP-Umgebungen mit einer sicheren Verbindung über SNC für die auf der NetWeaver- bzw. ABAP-Schnittstelle basierenden Protokolle bereitstellen.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: cc3c58743fc1cfca90ff8a2f20e5450895accc75
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519874"
---
# <a name="deploy-the-microsoft-sentinel-sap-data-connector-with-snc"></a>Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit SNC

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel wird beschrieben, wie Sie den Microsoft Sentinel-Datenconnector für SAP bereitstellen, wenn Sie über eine sichere Verbindung mit SAP über Secure Network Communications (SNC) für die auf der NetWeaver- bzw. ABAP-Schnittstelle basierenden Protokolle verfügen.

> [!NOTE]
> Der standardmäßig verwendete und am häufigsten empfohlene Prozess für die Bereitstellung des Microsoft Sentinel-Datenconnectors für SAP ist [die Verwendung einer Azure-VM](sap-deploy-solution.md). Dieser Artikel richtet sich an fortgeschrittene Benutzer.

> [!IMPORTANT]
> Die Microsoft Sentinel-Lösung für SAP befindet sich derzeit in der PREVIEW. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="prerequisites"></a>Voraussetzungen

Die grundlegenden Voraussetzungen für die Bereitstellung des Microsoft Sentinel-Datenconnectors für SAP sind bei allen Bereitstellungsmethoden identisch.

Stellen Sie sicher, dass Ihr System die Voraussetzungen erfüllt, die in der [Hauptanleitung zur Bereitstellung des SAP-Datenconnectors](sap-deploy-solution.md#prerequisites) dokumentiert sind, bevor Sie beginnen.

Weitere Voraussetzungen für die Arbeit mit SNC sind:

- **Eine sichere Verbindung mit SAP via SNC**. Definieren Sie die verbindungsspezifischen SNC-Parameter in den Repository-Konstanten für das AS-ABAP, mit dem Sie eine Verbindung herstellen. Weitere Informationen finden Sie auf der entsprechenden Website [SAP-Onlinecommunity](https://wiki.scn.sap.com/wiki/display/Security/Securing+Connections+to+AS+ABAP+with+SNC).

- **Das SAPCAR-Hilfsprogramm**, das aus dem SAP Service Marketplace heruntergeladen wurde. Weitere Informationen finden Sie im [SAP-Installationshandbuch](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/467291d0dc104d19bba073a0380dc6b4.html).

Weitere Informationen finden Sie unter [Detaillierte SAP-Anforderungen für die Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-detailed-requirements.md).

## <a name="create-your-azure-key-vault"></a>Erstellen Ihres Azure-Schlüsseltresors

Erstellen Sie einen Azure-Schlüsseltresor, den Sie als dedizierten Schlüsseltresor für den Microsoft Sentinel-Datenconnector für SAP nutzen können.

Führen Sie den folgenden Befehl aus, um Ihren Azure-Schlüsseltresor zu erstellen und Zugriff auf einen Azure-Dienstprinzipal zu gewähren:

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

spname=<sp-name>

kvname=<keyvaultname>
# Optional when Azure MI not enabled - Create sp user for AZ cli connection, save details for env.list file

az ad sp create-for-rbac –name $spname

SpID=$(az ad sp list –display-name $spname –query “[].appId” --output tsv

#Create key vault
az keyvault create \
  --name $kvname \
  --resource-group $kvgp

# Add access to SP
az keyvault set-policy --name $kvname --resource-group $kvgp --object-id $spID --secret-permissions get list set
```

Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Schlüsseltresors mithilfe der Azure CLI](../key-vault/general/quick-create-cli.md).

## <a name="add-azure-key-vault-secrets"></a>Hinzufügen von Azure Key Vault-Geheimnissen

Um Azure Key Vault-Geheimnisse hinzuzufügen, führen Sie das folgende Skript mit Ihrer eigenen System-ID und den Anmeldeinformationen aus, die Sie hinzufügen möchten:

```azurecli
#Add Azure Log ws ID
az keyvault secret set \
  --name <SID>-LOG_WS_ID \
  --value "<logwsod>" \
  --description SECRET_AZURE_LOG_WS_ID --vault-name $kvname

#Add Azure Log ws public key
az keyvault secret set \
  --name <SID>-LOG_WS_PUBLICKEY \
  --value "<loswspubkey>" \
  --description SECRET_AZURE_LOG_WS_PUBLIC_KEY --vault-name $kvname
```

Weitere Informationen finden Sie in der CLI-Dokumentation unter [az keyvault secret](/cli/azure/keyvault/secret).

## <a name="deploy-the-sap-data-connector"></a>Den SAP-Datenconnector bereitstellen

In diesem Verfahren wird beschrieben, wie sie den SAP-Datenconnector auf einer VM bereitstellen, wenn sie eine Verbindung über SNC herstellen.

Wir empfehlen diesen Vorgang, nachdem Sie einen [Schlüsseltresor](#create-your-azure-key-vault) mit Ihren [SAP-Anmeldeinformationen](#add-azure-key-vault-secrets) eingerichtet haben.

**So stellen Sie den SAP-Datenconnector bereit**

1. Laden Sie auf Ihrer Datenconnector-VM das neueste SAP NW RFC SDK von der Website [SAP Launchpad](https://support.sap.com) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip** herunter.

    > [!NOTE]
    > Sie benötigen Ihre SAP-Benutzeranmeldeinformationen, um auf das SDK zugreifen zu können. Außerdem müssen Sie das SDK herunterladen, das Ihrem Betriebssystem entspricht.
    >
    > Wählen Sie die Option **LINUX ON X86_64** aus.

1. Erstellen Sie einen neuen Ordner mit einem aussagekräftigen Namen, und kopieren Sie die ZIP-Datei des SDK in Ihren neuen Ordner.

1. Klonen Sie das GitHub-Repository mit der Microsoft Sentinel-Lösung auf Ihre Datenconnector-VM, und kopieren Sie die Datei **systemconfig.ini** der Microsoft Sentinel-Lösung für SAP in Ihren neuen Ordner.

    Beispiel:

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. Bearbeiten Sie nach Bedarf die Datei **systemconfig.ini**. Orientieren Sie sich dabei an den eingebetteten Kommentaren.

    Sie müssen alle Konfigurationen mit Ausnahme der Schlüsseltresorgeheimnisse bearbeiten. Weitere Informationen finden Sie unter [Manuelles Konfigurieren des SAP-Datenconnectors](sap-solution-deploy-alternate.md#manually-configure-the-sap-data-connector).

1. Befolgen Sie die Anweisungen in der Datei **systemconfig.ini**, um die Protokolle zu definieren, die Sie in Microsoft Sentinel erfassen möchten.

    Ein Beispiel hierfür finden Sie unter [Definieren der an Microsoft Sentinel gesendeten SAP-Protokolle](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel).

    > [!NOTE]
    > Relevante Protokolle für die SNC-Kommunikation sind nur die Protokolle, die über die NetWeaver-/ABAP abgerufen werden. SAP Control- und HANA-Protokolle liegen nicht im Gültigkeitsbereich von SNC.
    >

1. Legen Sie die folgenden Konfigurationen anhand der Anweisungen in der Datei **systemconfig.ini** fest:

    - Ob E-Mail-Adressen von Benutzern in Überwachungsprotokollen enthalten sein sollen
    - Ob fehlgeschlagene API-Aufrufe wiederholt werden sollen
    - Ob cexal-Überwachungsprotokolle enthalten sein sollen
    - Ob ein Zeitintervall zwischen Datenextraktionen gewartet werden soll, insbesondere bei großen Extraktionen

    Weitere Informationen finden Sie unter [Konfigurationen des SAL-Protokollconnectors](sap-solution-deploy-alternate.md#sal-logs-connector-settings).

1. Speichern Sie die aktualisierte Datei **systemconfig.ini** im Verzeichnis **sapcon** auf Ihrer VM.

1. Laden Sie das vordefinierte Docker-Image herunter, und führen Sie es bei installiertem SAP-Datenconnector aus. Führen Sie Folgendes aus:

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker create -v $(pwd):/sapcon-app/sapcon/config/system -v /home/azureuser /sap/sec:/sapcon-app/sec --env SCUDIR=/sapcon-app/sec --name sapcon-snc mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    ```

## <a name="post-deployment-sap-system-procedures"></a>SAP-Systemverfahren nach der Bereitstellung

Führen Sie nach der Bereitstellung Ihres SAP-Datenconnectors die folgenden SAP-Systemverfahren aus:

1. Laden Sie die kryptographische SAP-Bibliothek aus [SAP Service Marketplace](https://launchpad.support.sap.com/#/) > **Software Downloads** > **Unseren Download-Katalog durchsuchen** > **SAP Cryptographic Software** herunter.

    Weitere Informationen finden Sie im [SAP-Installationshandbuch](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/86921b29cac044d68d30e7b125846860.html).

1. Verwenden Sie das SAPCAR-Hilfsprogramm, um die Bibliotheksdateien zu extrahieren und auf Ihrer SAP-Datenconnector-VM im Verzeichnis `<sec>` bereitzustellen.

1. Vergewissern Sie sich, dass Sie über Berechtigungen zum Ausführen der Bibliotheksdateien verfügen.

1. Definieren Sie eine Umgebungsvariable namens **SECUDIR**, deren Wert dem vollständigen Pfad zu dem Verzeichnis `<sec>` entspricht.

1. Erstellen Sie eine persönliche Sicherheitsumgebung (Personal Security Environment, PSE). Das **sapgenspe**-Command-Line-Tool ist in Ihrem Verzeichnis `<sec>` auf Ihrem SAP-Datenconnector-VM verfügbar.

    Zum Beispiel:

    ```bash
    ./sapgenpse get_pse -p my_pse.pse -noreq -x my_pin "CN=sapcon.com, O=my_company, C=IL"
    ```

    Weitere Informationen finden Sie unter [Erstellen einer persönlichen Sicherheitsumgebung](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/285bb1fda3fa472c8d9205bae17a6f95.html) in der SAP-Dokumentation.

1. Erstellen Sie Anmeldeinformationen für Ihre PSE. Zum Beispiel:

    ```bash
    ./sapgenpse seclogin -p my_pse.pse -x my_pin -O MXDispatcher_Service_User
    ```

    Weitere Informationen finden Sie in der SAP-Dokumentation unter [Erstellen von Anmeldeinfos](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/d8b50371667740e797e6c9f0e9b7141f.html).

1. Tauschen Sie das öffentliche Schlüsselzertifikat zwischen der Identitätszentrale und der SNC-PSE des AS-ABAP aus.

    Führen Sie beispielsweise folgenden Vorgangs aus, um das öffentliche Schlüsselzertifikat aus der Identitätszentrale zu exportieren:

    ```bash
    ./sapgenpse export_own_cert -o my_cert.crt -p my_pse.pse -x abcpin
    ```

    Importieren Sie das Zertifikat in die SNC-PSE ABAP AS-ABAP, exportieren Sie es aus der PSE, und importieren Sie es dann wieder in die Identitätszentrale.

    Führen Sie beispielsweise folgenden Vorgang aus, um das Zertifikat in die Identitätszentrale zu importieren:

    ```bash
    ./sapgenpse maintain_pk -a full_path/my_secure_dir/my_exported_cert.crt -p my_pse.pse -x my_pin
    ```

    Weitere Informationen finden Sie in der SAP-Dokumentation unter [Austauschen der öffentlichen Schlüsselzertifikate](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/7bbf90b29c694e6080e968559170fbcd.html).

## <a name="edit-the-sap-data-connector-configuration"></a>Ändern der Konfiguration des SAP-Datenconnectors

1. Navigieren Sie auf Ihrem SAP-Datenconnector-VM zur  Datei **systemconfig.ini** und definieren Sie die folgenden Parameter mit den relevanten Werten:

    ```ini
    [Secrets Source]
    secrets = AZURE_KEY_VAULT
    ```

1. Erstellen Sie in Ihrem [Azure-Schlüsseltresor](#create-your-azure-key-vault) die folgenden Geheimnisse:

    - `<Interprefix>-ABAPSNCPARTNERNAME`, wobei der Wert `<Relevant DN details>` ist
    - `<Interprefix>-ABAPSNCLIB`, wobei der Wert `<lib_Path>` ist
    - `<Interprefix>-ABAPX509CERT`, wobei der Wert `<Certificate_Code>)` ist

    Zum Beispiel:

    ```ini
    S4H-ABAPSNCPARTNERNAME  =  'p:CN=help.sap.com, O=SAP_SE, C=IL' (Relevant DN)
    S4H-ABAPSNCLIB = 'home/user/sec-dir' (Relevant directory)
    S4H-ABAPX509CERT = 'MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO' (Relevant certificate code)
    ```

    > [!NOTE]
    > Der Wert `<Interprefix>` ist standardmäßig Ihre Sicherheits-ID, wie beispielsweise `A4H-<ABAPSNCPARTNERNAME>`.
    >

Wenn Sie Geheimnisse direkt in die Konfigurationsdatei eingeben, definieren Sie die Parameter wie folgt:

```ini
[Secrets Source]
secrets = DOCKER_FIXED
[ABAP Central Instance]
snc_partnername =  <Relevant_DN_Deatils>
snc_lib =  <lib_Path>
x509cert = <Certificate_Code>
For example:
snc_partnername =  p:CN=help.sap.com, O=SAP_SE, C=IL (Relevant DN)
snc_lib = /sapcon-app/sec/libsapcrypto.so (Relevant directory)
x509cert = MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO (Relevant certificate code)
```

### <a name="attach-the-snc-parameters-to-your-user"></a>Anfügen der SNC-Parameter an Ihren Benutzer

1. Rufen Sie auf Ihrem SAP-Datenconnector-VM die Transaktion `SM30` auf und wählen Sie diese aus, um die Tabelle `USRACLEXT` zu behalten.

1. Einen neuen Eintrag hinzufügen. Geben Sie in das Feld **Benutzer** den Kommunikationsbenutzer ein, der für die Verbindung mit dem ABAP-System verwendet wird.

1. Geben Sie den SNC-Namen ein, wenn Sie dazu aufgefordert werden. Der SNC-Name ist der eindeutige Name, der beim Erstellen der Identity Manager-PSE angegeben wurde. Beispiel: `CN=IDM, OU=SAP, C=DE`

    Achten Sie darauf, ein `p` vor dem SNC-Namen hinzuzufügen. Beispiel: `p:CN=IDM, OU=SAP, C=DE`.

1. Wählen Sie **Speichern** aus.

SNC ist auf Ihrem Datenconnector-VM aktiviert.

## <a name="activate-the-sap-data-connector"></a>Aktivieren des SAP-Datenconnectors

In diesem Verfahren wird beschrieben, wie Sie den SAP-Datenconnector mithilfe der gesicherten SNC-Verbindung aktivieren, die Sie mithilfe der zuvor in diesem Artikel beschriebenen Verfahren erstellt haben.

1. Aktivieren des Docker-Images:

    ```bash
    docker start sapcon-<SID>
    ```

1. Überprüfen Sie die Verbindung. Führen Sie Folgendes aus:

    ```bash
    docker logs sapcon-<SID>
    ```

1. Wenn die Verbindung nicht hergestellt werden kann, verwenden Sie die Protokolle, um die Problemursache zu ermitteln.

    Deaktivieren Sie bei Bedarf das Docker-Image:

    ```bash
    docker stop sapcon-<SID>
    ```

Beispielsweise können Probleme aufgrund einer Fehlkonfiguration in der Datei **systemconfig.ini** oder in Ihrem Azure-Schlüsseltresor auftreten, oder einige der Schritte zum Erstellen einer sicheren Verbindung über SNC wurden nicht ordnungsgemäß ausgeführt.

Führen Sie die oben beschriebenen Schritte erneut aus, um eine sichere Verbindung über SNC zu konfigurieren. Weitere Informationen finden Sie auch unter [Problembehandlung bei der Bereitstellung der Microsoft Sentinel-Lösung für SAP](sap-deploy-troubleshoot.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Datenconnector für SAP aktiviert wurde, stellen Sie im nächsten Schritt die Lösung **Microsoft Sentinel – Continuous Threat Monitoring for SAP** bereit. Weitere Informationen finden Sie unter [Bereitstellen von SAP-Sicherheitsinhalten](sap-deploy-solution.md#deploy-sap-security-content).

Durch die Bereitstellung der Lösung wird der SAP-Datenconnector in Microsoft Sentinel angezeigt, und die SAP-Arbeitsmappe und -Analyseregeln werden bereitgestellt. Wenn Sie fertig sind, fügen Sie Ihre SAP-Watchlists manuell hinzu, und passen Sie sie an.

Weitere Informationen finden Sie unter

- [Detaillierte SAP-Anforderungen für die Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-detailed-requirements.md)
- [Referenz zu Protokollen für die Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-log-reference.md)
- [Microsoft Sentinel-Lösung für SAP: Referenz zu sicherheitsbezogenen Inhalten (Public Preview)](sap-solution-security-content.md)

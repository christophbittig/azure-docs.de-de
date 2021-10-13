---
title: Bereitstellen der kontinuierlichen Bedrohungsüberwachung in SAP | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure Sentinel-Lösung für SAP-Umgebungen bereitstellen.
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 07/06/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: fb8d75b9dbf4fd2e6c0fd82ecbc0dce2ce2447c0
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857821"
---
#  <a name="deploy-sap-continuous-threat-monitoring-public-preview"></a>Bereitstellen der kontinuierlichen Bedrohungsüberwachung in SAP (öffentliche Vorschau)

In diesem Artikel werden Sie Schritt für Schritt durch den Prozess der Bereitstellung der kontinuierlichen Bedrohungsüberwachung von Azure Sentinel für SAP geführt.

> [!IMPORTANT]
> Die Azure Sentinel-Lösung für SAP befindet sich derzeit in der PREVIEW-Phase. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="overview"></a>Überblick

[Azure Sentinel-Lösungen](sentinel-solutions.md) umfassen gebündelte Sicherheitsinhalte wie Bedrohungserkennungen, Arbeitsmappen und Watchlists. Durch Lösungen können Sie Azure Sentinel-Sicherheitsinhalte für einen bestimmten Datenconnector mithilfe eines einzelnen Prozesses integrieren.

Der SAP-Datenconnector von Azure Sentinel ermöglicht es Ihnen, SAP-Systeme auf komplexe Bedrohungen innerhalb der Geschäfts- und Anwendungsebene zu überwachen.

Der SAP-Datenconnector streamt eine Vielzahl von 14 Anwendungsprotokollen aus der gesamten SAP-Systemlandschaft und sammelt Protokolle sowohl von Advanced Business Application Programming (ABAP) über NetWeaver RFC-Aufrufe als auch Dateispeicherdaten über die OSSAP-Kontrollschnittstelle. Der SAP-Datenconnector bietet Azure Sentinel die Möglichkeit, die zugrunde liegende SAP-Infrastruktur zu überwachen.

Um SAP-Protokolle in Azure Sentinel zu erfassen, muss der SAP-Datenconnector von Azure Sentinel in Ihrer SAP-Umgebung installiert sein. Es wird empfohlen, einen Docker-Container auf einer Azure-VM für die Bereitstellung zu verwenden, wie es in diesem Tutorial beschrieben ist.

Nach der Bereitstellung des SAP-Datenconnectors stellen Sie die Sicherheitsinhalte der SAP-Lösung bereit, um reibungslos Einblicke in die SAP-Umgebung Ihrer Organisation zu erhalten und alle zugehörigen Funktionen für Sicherheitsvorgänge zu verbessern.

In diesem Artikel wird Folgendes behandelt:

> [!div class="checklist"]
> * Vorbereiten Ihres SAP-Systems für die Bereitstellung des SAP-Datenconnectors
> * Verwenden eines Docker-Containers und einer Azure-VM zum Bereitstellen des SAP-Datenconnectors
> * Bereitstellen der Sicherheitsinhalte der SAP-Lösung in Azure Sentinel

> [!NOTE]
> Zusätzliche Schritte sind erforderlich, um Ihren SAP-Datenconnector über eine sichere SNC-Verbindung bereitzustellen. Weitere Informationen finden Sie unter [Bereitstellen des Azure Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md).
>
## <a name="prerequisites"></a>Voraussetzungen

Um den SAP-Datenconnector von Azure Sentinel und die Sicherheitsinhalte wie in diesem Tutorial beschrieben bereitzustellen, müssen die folgenden Voraussetzungen erfüllt sein:

|Bereich  |Beschreibung  |
|---------|---------|
|**Voraussetzungen für Azure**     |  **Zugriff auf Azure Sentinel**. Notieren Sie sich ID und Schlüssel Ihres Azure Sentinel-Arbeitsbereichs, den Sie in diesem Tutorial beim [Bereitstellen Ihres SAP-Datenconnectors](#deploy-your-sap-data-connector) verwenden möchten. <br>Zum Anzeigen dieser Details in Azure Sentinel navigieren Sie zu **Einstellungen** > **Arbeitsbereichseinstellungen** > **Agent-Verwaltung**. <br><br>**Möglichkeit zum Erstellen von Azure-Ressourcen**. Weitere Informationen finden Sie in der [Dokumentation zu Azure Resource Manager](../azure-resource-manager/management/manage-resources-portal.md). <br><br>**Zugriff auf Azure Key Vault**. In diesem Tutorial werden die empfohlenen Schritte für die Verwendung von Azure Key Vault zum Speichern Ihrer Anmeldeinformationen beschrieben. Weitere Informationen finden Sie in der [Dokumentation zu Azure Key Vault](../key-vault/index.yml).       |
|**Systemanforderungen**     |   **Software**. Die Softwarevoraussetzungen werden mit dem Bereitstellungsskript für den SAP-Datenconnector automatisch installiert. Weitere Informationen finden Sie unter [Automatisch installierte Software](#automatically-installed-software). <br><br> **Systemkonnektivität**. Stellen Sie sicher, dass die VM, die als Host für den SAP-Datenconnector dient, über Zugriff auf Folgendes verfügt: <br>– Azure Sentinel <br>– Azure Key Vault <br>– Den Host der SAP-Umgebung über die folgenden TCP-Ports: *32xx*, *5xx13* und *33xx*, wobei *xx* für die SAP-Instanznummer steht <br><br>Stellen Sie sicher, dass Sie auch über ein SAP-Benutzerkonto verfügen, um auf die Downloadseite für SAP-Software zugreifen zu können.<br><br>**Systemarchitektur**. Die SAP-Lösung wird auf einer VM als Docker-Container bereitgestellt, und jeder SAP-Client benötigt eine eigene Containerinstanz. Empfehlungen zur Größe finden Sie unter [Empfohlene Größe von virtuellen Computern](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing). <br>Ihre VM und der Azure Sentinel-Arbeitsbereich können sich in unterschiedlichen Azure-Abonnements und sogar in unterschiedlichen Azure AD-Mandanten befinden.|
|**SAP-Voraussetzungen**     |   **Unterstützte SAP-Versionen**. Es wird empfohlen, die Version [SAP_BASIS 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) oder höher zu verwenden. <br>Ausgewählte Schritte in diesem Tutorial bieten alternative Anweisungen, wenn Sie mit der älteren SAP-Version [SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) arbeiten.<br><br> **SAP-Systemdetails**. Notieren Sie sich die folgenden SAP-Systemdetails, um sie in diesem Tutorial zu verwenden:<br>    – IP-Adresse des SAP-Systems<br>– SAP-Systemnummer, z. B. `00`<br>    – SAP-System-ID aus dem SAP NetWeaver-System, z. B. `NPL` <br>– SAP-Client-ID, z. B. `001`<br><br>**Zugriff auf SAP NetWeaver-Instanz**. Für den Zugriff auf Ihre SAP-Instanzen muss eine der folgenden Optionen verwendet werden: <br>- [SAP ABAP-Benutzer/-Kennwort](#configure-your-sap-system) <br>– Ein Benutzer mit einem X509-Zertifikat, der SAP CRYPTOLIB PSE verwendet. Für diese Option müssen möglicherweise manuelle Schritte von einem Experten ausgeführt werden.<br><br>**Unterstützung durch Ihr SAP-Team**.  Sie brauchen die Unterstützung Ihres SAP-Teams, um sicherzustellen, dass Ihr SAP-System für die Lösungsbereitstellung [richtig konfiguriert](#configure-your-sap-system) ist.   |
|     |         |


### <a name="automatically-installed-software"></a>Automatisch installierte Software

Mit dem [Bereitstellungsskript für den SAP-Datenconnector](#deploy-your-sap-data-connector) wird die folgende Software mit SUDO-Berechtigungen (Root) auf Ihrer VM installiert:

- [Unzip](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 oder höher](https://www.python.org/downloads/)
- [Python3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>Konfigurieren Ihres SAP-Systems

In diesem Verfahren wird beschrieben, wie Sie sicherstellen, dass auf Ihrem SAP-System die richtige erforderliche Software installiert und für die Bereitstellung des SAP-Datenconnectors von Azure Sentinel konfiguriert ist.

> [!IMPORTANT]
> Führen Sie dieses Verfahren gemeinsam mit Ihrem SAP-Team aus, um richtige Konfigurationen zu gewährleisten.
>

**So konfigurieren Sie Ihr SAP-System für den SAP-Datenconnector**:

1. Stellen Sie sicher, dass die folgenden SAP-Hinweise abhängig von Ihrer Version in Ihrem System bereitgestellt werden:

    |SAP BASIS-Versionen  |Erforderlicher Hinweis |
    |---------|---------|
    |– 750 SP01 bis SP12<br>– 751 SP01 bis SP06<br>– 752 SP01 bis SP03     |  2641084: Standardisierter Lesezugriff für die Protokolldaten der Sicherheitsüberwachung       |
    |– 700 bis 702<br>– 710 bis 711, 730, 731, 740 und 750     | 2173545: CD: CHANGEDOCUMENT_READ_ALL        |
    |– 700 bis 702<br>– 710 bis 711, 730, 731 und 740<br>– 750 bis 752     | 2502336: CD (Change Document): RSSCD100 – schreibgeschützt aus dem Archiv, nicht aus der Datenbank        |
    |     |         |

    Für spätere Versionen sind keine zusätzlichen Hinweise erforderlich. Weitere Informationen finden Sie auf der [Launchpad-Website des SAP-Supports](https://support.sap.com/en/index.html), bei der Sie sich mit einem SAP-Benutzerkonto anmelden.

1. Laden Sie eine der folgenden SAP-Änderungsanforderungen aus dem Azure Sentinel-GitHub-Repository unter https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR: herunter, und installieren Sie sie:

    - **SAP-Versionen 750 oder höher**: Installieren Sie die SAP-Änderungsanforderung *144 (NPLK900144)* .
    - **SAP-Versionen 740**: Installieren Sie die SAP-Änderungsanforderung *146 (NPLK900146)* .

    Stellen Sie beim Ausführen dieses Schritts sicher, dass Sie den Binärmodus verwenden, um die Dateien in das SAP-System zu übertragen, und verwenden Sie den SAP-Transaktionscode **STMS_IMPORT**.

    > [!NOTE]
    > Im SAP-Bereich **Importoptionen** wird möglicherweise die Option **Ungültige Komponentenversion ignorieren** angezeigt. Wenn das der Fall ist, wählen Sie diese Option aus, bevor Sie fortfahren.
    >

1. Erstellen Sie eine neue SAP-Rolle mit dem Namen **/MSFTSEN/SENTINEL_CONNECTOR**, indem Sie die SAP-Änderungsanforderung *14 (NPLK900140)* importieren. Verwenden Sie den SAP-Transaktionscode **STMS_IMPORT**.

    Stellen Sie sicher, dass die Rolle mit den erforderlichen Berechtigungen erstellt wird. Hier ein Beispiel:

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Erforderliche SAP-Rollenberechtigungen für den SAP-Datenconnector von Azure Sentinel":::

    Weitere Informationen finden Sie unter den [Autorisierungen für den ABAP-Benutzer](sap-solution-detailed-requirements.md#required-abap-authorizations).

1. Erstellen Sie einen RFC/NetWeaver-Benutzer ohne Dialog für den SAP-Datenconnector, und fügen Sie die neu erstellte Rolle **/MSFTSEN/SENTINEL_CONNECTOR** an.

    - Überprüfen Sie nach dem Anfügen der Rolle, ob die Rollenberechtigungen an den Benutzer verteilt sind.
    - Für diesen Vorgang müssen Sie einen Benutzernamen und ein Kennwort für den ABAP-Benutzer verwenden. Nachdem der neue Benutzer erstellt wurde und über die erforderlichen Berechtigungen verfügt, stellen Sie sicher, dass Sie das ABAP-Benutzerkennwort ändern.

1. Laden Sie die Version **SAP NetWeaver RFC SDK 7.50 for Linux on x86_64 64 BIT** herunter, und legen Sie sie auf Ihrer VM ab, da sie während des Installationsprozesses benötigt wird.

    Suchen Sie beispielsweise das SDK auf der [Downloadwebsite für SAP-Software](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**. Vergewissern Sie sich, dass Sie die Option **LINUX ON X86_64 65BIT** herunterladen. Kopieren Sie die Datei (z. B. mit SCP) auf Ihre VM.

    Sie benötigen ein SAP-Benutzerkonto, um auf die Downloadseite für SAP-Software zuzugreifen.

1. (Optional) Die SAP-Datei **Auditlog** wird systemweit verwendet und unterstützt mehrere SAP-Clients. Jede Instanz der SAP-Lösung von Azure Sentinel unterstützt jedoch nur einen einzigen SAP-Client.

    Wenn Sie über ein SAP-System mit mehreren Clients verfügen, wird daher empfohlen, die Datei **Auditlog** nur für den Client zu aktivieren, auf dem Sie die SAP-Lösung bereitstellen, um Datenduplizierung zu vermeiden.


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>Bereitstellen einer Linux-VM für Ihren SAP-Datenconnector

In diesem Verfahren wird beschrieben, wie Sie mithilfe der Azure CLI eine Ubuntu Server 18.04 LTS-VM bereitstellen und ihr eine [systemseitig verwalteten Identität](../active-directory/managed-identities-azure-resources/index.yml) zuweisen.

> [!TIP]
> Sie können den Datenconnector auch unter RHEL, Version 7.7 und höher, oder SUSE-Versionen 15 und höher bereitstellen. Beachten Sie, dass alle Betriebssystem- und Patchebenen vollständig auf dem neuesten Stand sein müssen.
>

**So stellen Sie Ihre Ubuntu-VM bereit und bereiten sie vor**:

1. Verwenden Sie den folgenden Befehl als Beispiel, und fügen Sie die Werte für Ihre Ressourcengruppe und den VM-Namen ein:

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username azureuser --data-disk-sizes-gb 10 – --size Standard_DS2 --generate-ssh-keys  --assign-identity
    ```

1. Installieren Sie Folgendes auf Ihrer neuen VM:

    - [Venv](https://docs.python.org/3.8/library/venv.html) mit Python-Version 3.8 oder höher
    - [Azure CLI](/cli/azure/), Version 2.8.0 oder höher

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie alle bewährten Sicherheitsmethoden für Ihre Organisation anwenden, genau wie bei jeder anderen VM.
>

Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Linux-VM mit der Azure CLI](../virtual-machines/linux/quick-create-cli.md).

## <a name="create-key-vault-for-your-sap-credentials"></a>Erstellen eines Schlüsseltresors für Ihre SAP-Anmeldeinformationen

In diesem Tutorial wird eine neu erstellte oder dedizierte [Azure Key Vault](../key-vault/index.yml)-Instanz verwendet, um Anmeldeinformationen für Ihren SAP-Datenconnector zu speichern.

**So erstellen oder bestimmen Sie eine Azure Key Vault-Instanz**:

1. Erstellen Sie eine neue Azure Key Vault-Instanz, oder wählen Sie eine vorhandene Instanz aus, um sie für die Bereitstellung Ihres SAP-Datenconnectors festzulegen.

    Wenn Sie beispielsweise eine neue Key Vault-Instanz erstellen möchten, führen Sie die folgenden Befehle aus. Verwenden Sie dabei den Namen Ihrer Key Vault-Ressourcengruppe, und geben Sie Ihren Key Vault-Namen ein:

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create Key Vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. Weisen Sie der verwalteten Identität der VM eine Zugriffsrichtlinie zu, einschließlich GET-, LIST- und SET-Berechtigungen, unter Verwendung einer der folgenden Methoden:

    - **Über das Azure-Portal**:

        Wählen Sie in Azure Key Vault die Optionen **Zugriffsrichtlinien** > **Zugriffsrichtlinie hinzufügen – Geheimnisberechtigungen: Abrufen, Auflisten und Festlegen** > **Prinzipal auswählen**. Geben Sie den [Namen Ihrer VM](#deploy-a-linux-vm-for-your-sap-data-connector) ein, und wählen Sie dann **Hinzufügen** > **Speichern** aus.

        Weitere Informationen finden Sie in der [Key Vault](../key-vault/general/assign-access-policy-portal.md)-Dokumentation.

    - **Über die Azure CLI**:

        1. Führen Sie den folgenden Befehl aus, um die [Prinzipal-ID der VM](#deploy-a-linux-vm-for-your-sap-data-connector) abzurufen, und geben Sie den Namen Ihrer Azure-Ressourcengruppe ein:

            ```azurecli
            VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
            ```

            Ihre Prinzipal-ID wird angezeigt, damit Sie sie im folgenden Schritt verwenden können.

        1. Führen Sie den folgenden Befehl aus, um der Key Vault-Instanz die Zugriffsberechtigungen der VM zuzuweisen. Geben Sie dazu den Namen Ihrer Ressourcengruppe und den Wert für die Prinzipal-ID ein, der im vorherigen Schritt zurückgegeben wurde.

            ```azurecli
            az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
            ```
## <a name="deploy-your-sap-data-connector"></a>Bereitstellen Ihres SAP-Datenconnectors

Das Bereitstellungsskript für den SAP-Datenconnector von Azure Sentinel installiert die [erforderliche Software](#automatically-installed-software) und dann den Connector auf Ihrer [neu erstellten VM](#deploy-a-linux-vm-for-your-sap-data-connector). Dabei werden Anmeldeinformationen in Ihrem [dedizierten Schlüsseltresor](#create-key-vault-for-your-sap-credentials) gespeichert.

Das Bereitstellungsskript für den SAP-Datenconnector ist unter [Azure Sentinel-GitHub-Repository > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh) gespeichert.

Zum Ausführen des Bereitstellungsskripts für den SAP-Datenconnector benötigen Sie folgende Details:

- Ihre Azure Sentinel-Arbeitsbereichsdetails, wie sie im Abschnitt [Voraussetzungen](#prerequisites) aufgeführt sind
- Die im Abschnitt [Voraussetzungen](#prerequisites) aufgeführten SAP-Systemdetails
- Zugriff auf einen VM-Benutzer mit SUDO-Berechtigungen
- Den SAP-Benutzer, den Sie in [Konfigurieren Ihres SAP-Systems](#configure-your-sap-system)erstellt haben, mit der Rolle **/MSFTSEN/SENTINEL_CONNECTOR**
- Die Hilfe Ihres SAP-Teams

**So führen Sie das Bereitstellungsskript für die SAP-Lösung aus**:

1. Führen Sie den folgenden Befehl aus, um die SAP-Lösung auf Ihrer VM bereitzustellen:

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. Befolgen Sie die Anweisungen auf dem Bildschirm, um Ihre SAP- und Key Vault-Details einzugeben und die Bereitstellung abzuschließen. Nach Abschluss der Bereitstellung wird eine Bestätigungsmeldung angezeigt:

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Azure Sentinel beginnt mit dem Abrufen von SAP-Protokollen für die konfigurierte Zeitspanne bis 24 Stunden vor der Initialisierung.

1. Es wird empfohlen, die Systemprotokolle zu überprüfen, um sicherzustellen, dass der Datenconnector Daten überträgt. Führen Sie Folgendes aus:

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>Bereitstellen von SAP-Sicherheitsinhalten

Stellen Sie die [SAP-Sicherheitsinhalte](sap-solution-security-content.md) aus den Bereichen **Lösungen** und **Watchlists** von Azure Sentinel bereit.

Mit der Lösung **Azure Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP** kann der SAP-Datenconnector im Azure Sentinel-Bereich **Datenconnectors** angezeigt werden. Außerdem werden die Arbeitsmappe **SAP – Systemanwendungen und Produkte** sowie SAP-bezogene Analyseregeln bereitgestellt.

Fügen Sie Ihrem Azure Sentinel-Arbeitsbereich manuell SAP-bezogene Watchlists hinzu.

**So stellen Sie Sicherheitsinhalte der SAP-Lösung bereit**:

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Lösungen (Vorschau)** aus.

    Auf der Seite **Lösungen** wird eine gefilterte, durchsuchbare Liste von Lösungen angezeigt.

1. Wählen Sie **Azure Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP (Vorschau)** aus, um die SAP-Lösungsseite zu öffnen.

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Lösung: Azure Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP (Vorschau)":::

1. Wählen Sie **Erstellen** aus, um den Lösungsbereitstellungs-Assistenten zu starten, und geben Sie die Details zum Azure-Abonnement, der Ressourcengruppe und dem Log Analytics-Arbeitsbereich ein, in dem Sie die Lösung bereitstellen möchten.

1. Wählen Sie **Weiter** aus, um die Registerkarten **Datenconnectors**, **Analysen** und **Arbeitsmappen** zu durchlaufen. Dort erfahren Sie mehr über die Komponenten, die mit dieser Lösung bereitgestellt werden.

    Der Standardname für die Arbeitsmappe lautet **SAP – Systemanwendungen und Produkte – Vorschau**. Ändern Sie ihn bei Bedarf auf der Registerkarte „Arbeitsmappen“.

    Weitere Informationen finden Sie unter [SAP-Lösung von Azure Sentinel: Referenz zu Sicherheitsinhalten (öffentliche Vorschau)](sap-solution-security-content.md).

1. Warten Sie, bis auf der Registerkarte **Überprüfen und erstellen** die Meldung **Überprüfung erfolgreich** angezeigt wird, und wählen Sie dann **Erstellen** aus, um die Lösung bereitzustellen.

    > [!TIP]
    > Sie können auch **Vorlage herunterladen** auswählen, um einen Link zum Bereitstellen der Lösung als Code zu erhalten.

1. Nach Abschluss der Bereitstellung wird oben rechts auf der Seite eine Bestätigungsmeldung angezeigt.

    Zum Anzeigen der neu bereitgestellten Inhalte navigieren Sie zu:

    - **Bedrohungsverwaltung** > **Arbeitsmappen** > **Meine Arbeitsmappen**, um die [integrierten SAP-Arbeitsmappen](sap-solution-security-content.md#built-in-workbooks) zu suchen.
    - **Konfiguration** > **Analysen**, um eine Reihe von [SAP-bezogenen Analyseregeln](sap-solution-security-content.md#built-in-analytics-rules) anzuzeigen.

1. Fügen Sie SAP-bezogene Watchlists hinzu, um sie in Ihrer Suche, für Erkennungsregeln, bei der Bedrohungssuche sowie in Playbooks für die Reaktion auf Bedrohungen zu verwenden. Diese Watchlists stellen die Konfiguration für die SAP-Lösung von Azure Sentinel für kontinuierliche Bedrohungsüberwachung bereit.

    1. Laden Sie SAP-Watchlists aus dem Azure Sentinel-GitHub-Repository unter https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists herunter.

    1. Fügen Sie im Bereich **Watchlists** von Azure Sentinel die Watchlists zu ihrem Azure Sentinel-Arbeitsbereich hinzu. Verwenden Sie die heruntergeladenen CSV-Dateien als Quellen, und passen Sie sie dann entsprechend Ihrer Umgebung an. 

        [ ![Azure Sentinel hinzugefügte SAP-bezogene Watchlists](media/sap/sap-watchlists.png) ](media/sap/sap-watchlists.png#lightbox)

        Weitere Informationen finden Sie unter [Verwenden von Azure Sentinel-Watchlists](watchlists.md) und [Verfügbare SAP-Watchlists](sap-solution-security-content.md#available-watchlists).

1. Navigieren Sie in Azure Sentinel zum Datenconnector **Azure Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP**, um die Verbindung zu bestätigen:

    [ ![Seite des Datenconnectors „Azure Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP“](media/sap/sap-data-connector.png) ](media/sap/sap-data-connector.png#lightbox)

    SAP ABAP-Protokolle werden auf der Seite **Protokolle** von Azure Sentinel unter **Benutzerdefinierte Protokolle** angezeigt:

    [ ![SAP ABAP-Protokolle unter „Benutzerdefinierte Protokolle“ in Azure Sentinel](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    Weitere Informationen finden Sie unter [Referenz zu Protokollen der SAP-Lösung von Azure Sentinel (öffentliche Vorschau)](sap-solution-log-reference.md).


## <a name="update-your-sap-data-connector"></a>Aktualisieren Ihres SAP-Datenconnectors

Wenn Sie über einen Docker-Container verfügen, der bereits mit einer früheren Version des SAP-Datenconnectors ausgeführt wird, führen Sie das Updateskript für den SAP-Datenconnector aus, um die neuesten verfügbaren Features zu erhalten.

Vergewissern Sie sich, dass Sie über die neuesten Versionen der relevanten Bereitstellungsskripts aus dem Azure Sentinel-GitHub-Repository verfügen. 

Führen Sie Folgendes aus:

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

Der Docker-Container des SAP-Datenconnectors auf Ihrem Computer wird aktualisiert. 

Prüfen Sie unbedingt, ob weitere Updates verfügbar sind:

- Relevante SAP-Änderungsanforderungen im [GitHub-Repository für Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR)
- Azure Sentinel-Sicherheitsinhalte für SAP in der Lösung **Azure Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP**
- Relevante Watchlists im [Azure Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists)


## <a name="collect-sap-hana-audit-logs"></a>Erfassen von SAP HANA-Überwachungsprotokollen

Wenn die SAP HANA-Datenbanküberwachungsprotokolle mit Syslog konfiguriert sind, müssen Sie auch Ihren Log Analytics-Agent zum Erfassen der Syslog-Dateien konfigurieren.

1. Stellen Sie sicher, dass der SAP HANA-Überwachungsprotokollpfad wie auf der [Launchpad-Website des SAP-Support](https://launchpad.support.sap.com/#/notes/0002624117) im *SAP-Hinweis 0002624117* beschrieben so konfiguriert ist, dass Syslog verwendet wird. Weitere Informationen finden Sie unter:

    - [SAP HANA-Überwachungspfad – Bewährte Methode](https://archive.sap.com/documents/docs/DOC-51098)
    - [Empfehlungen für die Überwachung](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. Überprüfen Sie die Syslog-Dateien Ihres Betriebssystems auf relevante HANA-Datenbankereignisse.

1. Installieren und konfigurieren Sie einen Log Analytics-Agent auf Ihrem Computer:

    1. Melden Sie sich bei Ihrem HANA-Datenbankbetriebssystem als Benutzer mit sudo-Berechtigungen an.
    1. Wechseln Sie im Azure-Portal zu Ihrem Log Analytics-Arbeitsbereich. Wählen Sie auf der linken Seite unter **Einstellungen** die Option **Agent-Verwaltung > Linux-Server** aus.
    1. Kopieren Sie den im Feld unter **Agent für Linux herunterladen und Onboarding durchführen** angezeigten Code in Ihr Terminal, und führen Sie das Skript aus.

    Der Log Analytics-Agent wird auf Ihrem Computer installiert und mit Ihrem Arbeitsbereich verbunden. Weitere Informationen finden Sie unter [Installieren des Log Analytics-Agents auf Linux-Computern](../azure-monitor/agents/agent-linux.md) und [OMS-Agent für Linux](https://github.com/microsoft/OMS-Agent-for-Linux) im Microsoft-GitHub-Repository.

1. Aktualisieren Sie die Registerkarte **Agent-Verwaltung > Linux-Server,** um zu sehen, dass **1 Linux-Computer verbunden** ist.

1. Wählen Sie unter **Einstellungen** auf der linken Seite **Agent-Konfiguration** und dann die Registerkarte **Syslog** aus.

1. Wählen Sie **Facility hinzufügen** aus, um die Facilitys hinzuzufügen, die Sie erfassen möchten. 

    > [!TIP]
    > Da sich die Facilitys, in denen HANA-Datenbankereignisse gespeichert werden, zwischen verschiedenen Distributionen ändern können, empfiehlt es sich, alle Facilitys hinzuzufügen, sie anhand Ihrer Syslog-Protokolle zu überprüfen und dann alle nicht relevanten zu entfernen.
    >

1. Überprüfen Sie in Azure Sentinel, ob HANA-Datenbankereignisse jetzt in den erfassten Protokollen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die SAP-Lösungen von Azure Sentinel:

- [Bereitstellen des Azure Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md)
- [Konfigurationsoptionen für Experten, lokale Bereitstellung und SAPControl-Protokollquellen](sap-solution-deploy-alternate.md)
- [Detaillierte SAP-Anforderungen für die Azure Sentinel-Lösung für SAP](sap-solution-detailed-requirements.md)
- [Referenz zu Protokollen der SAP-Lösung von Azure Sentinel](sap-solution-log-reference.md)
- [SAP-Lösung von Azure Sentinel: integrierte Sicherheitsinhalte](sap-solution-security-content.md)
- [Problembehandlung bei der Bereitstellung der Azure Sentinel-Lösung für SAP](sap-deploy-troubleshoot.md)

Weitere Informationen finden Sie unter [Azure Sentinel-Lösungen](sentinel-solutions.md).

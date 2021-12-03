---
title: Bereitstellen der kontinuierlichen Bedrohungsüberwachung in SAP | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Microsoft Sentinel-Lösung für SAP-Umgebungen bereitstellen.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: e8d58104e2a622482280ea0a546dbacc95cdc832
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520102"
---
#  <a name="deploy-sap-continuous-threat-monitoring-preview"></a>Bereitstellen der kontinuierlichen Bedrohungsüberwachung in SAP (Vorschau)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden Sie Schritt für Schritt durch den Prozess der Bereitstellung der kontinuierlichen Bedrohungsüberwachung von Microsoft Sentinel für SAP geführt.

> [!IMPORTANT]
> Die Microsoft Sentinel-Lösung für SAP befindet sich derzeit in der PREVIEW. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="overview"></a>Überblick

[Microsoft Sentinel-Lösungen](sentinel-solutions.md) umfassen gebündelte Sicherheitsinhalte wie Bedrohungserkennungen, Arbeitsmappen und Watchlists. Mit diesen Lösungen können Sie Microsoft Sentinel-Sicherheitsinhalte für einen bestimmten Datenconnector in einem einzigen Prozess integrieren.

Der SAP-Datenconnector von Microsoft Sentinel ermöglicht es Ihnen, SAP-Systeme auf komplexe Bedrohungen innerhalb der Geschäfts- und Anwendungsebene zu überwachen.

Der SAP-Datenconnector streamt 14 Anwendungsprotokolle aus der gesamten SAP-Systemlandschaft. Der Datenconnector sammelt Protokolle sowohl von Advanced Business Application Programming (ABAP) über NetWeaver RFC-Aufrufe als auch von Dateispeicherdaten über die OSSAP-Kontrollschnittstelle. Der SAP-Datenconnector erweitert die Funktion von Microsoft Sentinel zum Überwachen der zugrunde liegenden SAP-Infrastruktur.

Um SAP-Protokolle in Microsoft Sentinel zu erfassen, muss der SAP-Datenconnector von Microsoft Sentinel in Ihrer SAP-Umgebung installiert sein. Für die Bereitstellung wird empfohlen, einen Docker-Container auf einem virtuellen Azure-Computer für die Bereitstellung zu verwenden, wie es in diesem Tutorial beschrieben ist.

Nach der Bereitstellung des SAP-Datenconnectors stellen Sie die Sicherheitsinhalte der SAP-Lösung bereit, um Einblicke in die SAP-Umgebung Ihrer Organisation zu erhalten und alle zugehörigen Funktionen für Sicherheitsvorgänge zu verbessern.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Vorbereiten Ihres SAP-Systems für die Bereitstellung des SAP-Datenconnectors.
> * Verwenden eines Docker-Containers und eines virtuellen Azure-Computers zum Bereitstellen des SAP-Datenconnectors.
> * Bereitstellen der Sicherheitsinhalte der SAP-Lösung in Microsoft Sentinel.

> [!NOTE]
> Zusätzliche Schritte sind erforderlich, um Ihren SAP-Datenconnector über eine SNC-Verbindung (Secure Network Communications) bereitzustellen. Weitere Informationen finden Sie unter [Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md).
>
## <a name="prerequisites"></a>Voraussetzungen

Um den SAP-Datenconnector von Microsoft Sentinel und die Sicherheitsinhalte wie in diesem Tutorial beschrieben bereitzustellen, müssen die folgenden Voraussetzungen erfüllt sein:

| Bereich | Beschreibung |
| --- | --- |
|**Voraussetzungen für Azure** | **Zugriff auf Microsoft Sentinel**. Notieren Sie sich ID und Schlüssel Ihres Microsoft Sentinel-Arbeitsbereichs, den Sie in diesem Tutorial beim [Bereitstellen Ihres SAP-Datenconnectors](#deploy-your-sap-data-connector) verwenden möchten. <br><br>Zum Anzeigen dieser Details in Microsoft Sentinel navigieren Sie zu **Einstellungen** > **Arbeitsbereichseinstellungen** > **Agent-Verwaltung**. <br><br>**Möglichkeit zum Erstellen von Azure-Ressourcen**. Weitere Informationen finden Sie in der [Dokumentation zu Azure Resource Manager](../azure-resource-manager/management/manage-resources-portal.md). <br><br>**Zugriff auf Ihren Azure-Schlüsseltresor**. In diesem Tutorial werden die empfohlenen Schritte für die Verwendung Ihres Azure Key Vault zum Speichern Ihrer Anmeldeinformationen beschrieben. Weitere Informationen finden Sie in der [Dokumentation zu Azure Key Vault](../key-vault/index.yml). |
|**Systemanforderungen** | **Software**. Die Softwarevoraussetzungen werden mit dem Bereitstellungsskript für den SAP-Datenconnector automatisch installiert. Weitere Informationen finden Sie unter [Automatisch installierte Software](#automatically-installed-software). <br><br> **Systemkonnektivität**. Stellen Sie sicher, dass die VM, die als Host für den SAP-Datenconnector dient, über Zugriff auf Folgendes verfügt: <br>– Microsoft Sentinel <br>– Ihren Azure Key Vault <br>– Den Host der SAP-Umgebung über die folgenden TCP-Ports: *32xx*, *5xx13* und *33xx*, wobei *xx* für die SAP-Instanznummer steht <br><br>Stellen Sie sicher, dass Sie auch über ein SAP-Benutzerkonto verfügen, um auf die Downloadseite für SAP-Software zugreifen zu können.<br><br>**Systemarchitektur**. Die SAP-Lösung wird auf einer VM als Docker-Container bereitgestellt, und jeder SAP-Client benötigt eine eigene Containerinstanz. Empfehlungen zur Größe finden Sie unter [Empfohlene Größe von virtuellen Computern](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing). <br>Ihre VM und der Microsoft Sentinel-Arbeitsbereich können sich in unterschiedlichen Azure-Abonnements und sogar in unterschiedlichen Azure AD-Mandanten befinden.|
|**SAP-Voraussetzungen** | **Unterstützte SAP-Versionen**. Es wird empfohlen, die Version [SAP_BASIS 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) oder höher zu verwenden. <br><br>Bestimmte Schritte in diesem Tutorial bieten alternative Anweisungen, wenn Sie mit der älteren SAP-Version [SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) arbeiten.<br><br> **SAP-Systemdetails**. Notieren Sie sich die folgenden SAP-Systemdetails, um sie in diesem Tutorial zu verwenden:<br>– IP-Adresse des SAP-Systems<br>– SAP-Systemnummer, z. B. `00`<br>– SAP-System-ID aus dem SAP NetWeaver-System (z. B. `NPL`) <br>– SAP-Client-ID, z. B. `001`<br><br>**Zugriff auf SAP NetWeaver-Instanz**. Für den Zugriff auf Ihre SAP-Instanzen muss eine der folgenden Optionen verwendet werden: <br>- [SAP ABAP-Benutzer/-Kennwort](#configure-your-sap-system) <br>– Ein Benutzer mit einem X509-Zertifikat, der SAP CRYPTOLIB PSE verwendet. Für diese Option müssen möglicherweise manuelle Schritte von einem Experten ausgeführt werden.<br><br>**Unterstützung durch Ihr SAP-Team**.  Sie brauchen die Unterstützung Ihres SAP-Teams, um sicherzustellen, dass Ihr SAP-System für die Lösungsbereitstellung [richtig konfiguriert](#configure-your-sap-system) ist. |
| | |

### <a name="automatically-installed-software"></a>Automatisch installierte Software

Mit dem [Bereitstellungsskript für den SAP-Datenconnector](#deploy-your-sap-data-connector) wird die folgende Software mittels *sudo*-Berechtigungen (root) auf Ihrer VM installiert:

- [Unzip](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 oder höher](https://www.python.org/downloads/)
- [Python 3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>Konfigurieren Ihres SAP-Systems

In diesem Verfahren wird beschrieben, wie Sie sicherstellen, dass auf Ihrem SAP-System die richtige erforderliche Software installiert und für die Bereitstellung des SAP-Datenconnectors von Microsoft Sentinel konfiguriert ist.

> [!IMPORTANT]
> Führen Sie dieses Verfahren gemeinsam mit Ihrem SAP-Team aus, um richtige Konfigurationen zu gewährleisten.
>

**So konfigurieren Sie Ihr SAP-System für den SAP-Datenconnector**:

1. Stellen Sie sicher, dass die folgenden SAP-Hinweise abhängig von Ihrer Version in Ihrem System bereitgestellt werden:

    | SAP&nbsp;BASIS-Versionen | Erforderlicher Hinweis |
    | --- | --- |
    | – 750 SP01 bis SP12<br>– 751 SP01 bis SP06<br>– 752 SP01 bis SP03 | 2641084: Standardisierter Lesezugriff für die Protokolldaten der Sicherheitsüberwachung |
    | – 700 bis 702<br>– 710 bis 711, 730, 731, 740 und 750 | 2173545: CD: CHANGEDOCUMENT_READ_ALL |
    | – 700 bis 702<br>– 710 bis 711, 730, 731 und 740<br>– 750 bis 752 | 2502336: CD (Change Document): RSSCD100 – schreibgeschützt aus dem Archiv, nicht aus der Datenbank |
    | | |

   Für höhere Versionen sind keine zusätzlichen Hinweise erforderlich. Weitere Informationen finden Sie auf der [Launchpad-Website des SAP-Supports](https://support.sap.com/en/index.html). Melden Sie sich mit einem SAP-Benutzerkonto an.

1. Laden Sie eine der folgenden SAP-Änderungsanforderungen aus dem [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR) herunter, und installieren Sie sie:

    - **SAP-Version 750 oder höher**: Installieren Sie die SAP-Änderungsanforderung *144 (NPLK900144)* .
    - **SAP-Version 740**: Installieren Sie die SAP-Änderungsanforderung *146 (NPLK900146)* .

    Stellen Sie beim Ausführen dieses Schritts sicher, dass Sie den Binärmodus verwenden, um die Dateien in das SAP-System zu übertragen, und verwenden Sie den SAP-Transaktionscode **STMS_IMPORT**.

    > [!NOTE]
    > Im SAP-Bereich **Importoptionen** wird möglicherweise die Option **Ungültige Komponentenversion ignorieren** angezeigt. Wenn sie angezeigt wird, wählen Sie diese Option aus, bevor Sie fortfahren.
    >

1. Erstellen Sie eine neue SAP-Rolle mit dem Namen **/MSFTSEN/SENTINEL_CONNECTOR**, indem Sie die SAP-Änderungsanforderung *14 (NPLK900163)* importieren. Verwenden Sie den SAP-Transaktionscode **STMS_IMPORT**.

    Stellen Sie sicher, dass die Rolle mit den erforderlichen Berechtigungen erstellt wird. Hier ein Beispiel:

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Erforderliche SAP-Rollenberechtigungen für den SAP-Datenconnector von Microsoft Sentinel.":::

    Weitere Informationen finden Sie unter den [Autorisierungen für den ABAP-Benutzer](sap-solution-detailed-requirements.md#required-abap-authorizations).

1. Erstellen Sie einen RFC/NetWeaver-Benutzer ohne Dialog für den SAP-Datenconnector, und fügen Sie die neu erstellte Rolle */MSFTSEN/SENTINEL_CONNECTOR* an.

    - Überprüfen Sie nach dem Anfügen der Rolle, ob die Rollenberechtigungen an den Benutzer verteilt werden.
    - Für diesen Vorgang müssen Sie einen Benutzernamen und ein Kennwort für den ABAP-Benutzer verwenden. Nachdem der neue Benutzer erstellt wurde und über die erforderlichen Berechtigungen verfügt, stellen Sie sicher, dass Sie das ABAP-Benutzerkennwort ändern.

1. Laden Sie die Version „SAP NetWeaver RFC SDK 7.50 for Linux on x86_64 64 BIT“ herunter, und legen Sie sie auf Ihrer VM ab, da sie während des Installationsprozesses benötigt wird.

    Suchen Sie beispielsweise das SDK auf der [Downloadwebsite für SAP-Software](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**. Vergewissern Sie sich, dass Sie die Option **LINUX ON X86_64 65BIT** herunterladen. Kopieren Sie die Datei (z. B. mit SCP) auf Ihre VM.

    Sie benötigen ein SAP-Benutzerkonto, um auf die Downloadseite für SAP-Software zuzugreifen.

1. (Optional) Die SAP-Datei *Auditlog* wird systemweit verwendet und unterstützt mehrere SAP-Clients. Jede Instanz der SAP-Lösung von Microsoft Sentinel unterstützt jedoch nur einen einzigen SAP-Client.

    Wenn Sie über ein SAP-System mit mehreren Clients verfügen, wird daher empfohlen, die Datei *Auditlog* nur für den Client zu aktivieren, auf dem Sie die SAP-Lösung bereitstellen, um Datenduplizierung zu vermeiden.


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>Bereitstellen einer Linux-VM für Ihren SAP-Datenconnector

In diesem Verfahren wird beschrieben, wie Sie mithilfe der Azure CLI eine Ubuntu Server 18.04 LTS-VM bereitstellen und ihr eine [systemseitig verwalteten Identität](../active-directory/managed-identities-azure-resources/index.yml) zuweisen.

> [!TIP]
> Sie können den Datenconnector auch unter RHEL, Version 7.7 oder höher, oder SUSE, Version 15 oder höher, bereitstellen. Beachten Sie, dass alle Betriebssystem- und Patchebenen vollständig auf dem neuesten Stand sein müssen.
>

Gehen Sie wie folgt vor, um Ihren virtuellen Ubuntu-Computer bereitzustellen und vorzubereiten:

1. Verwenden Sie den folgenden Befehl als Beispiel. Achten Sie darauf, dass Sie die Werte für Ihre Ressourcengruppe und den VM-Namen einfügen.

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username azureuser --data-disk-sizes-gb 10 – --size Standard_DS2 --generate-ssh-keys  --assign-identity
    ```

1. Installieren Sie Folgendes auf Ihrer neuen VM:

    - [Venv](https://docs.python.org/3.8/library/venv.html) mit Python, Version 3.8 oder höher.
    - Die [Azure CLI](/cli/azure/), Version 2.8.0 oder höher.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie alle bewährten Sicherheitsmethoden für Ihre Organisation anwenden, genau wie bei jeder anderen VM.
>

Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Linux-VM mit der Azure CLI](../virtual-machines/linux/quick-create-cli.md).

## <a name="create-a-key-vault-for-your-sap-credentials"></a>Erstellen eines Schlüsseltresors für Ihre SAP-Anmeldeinformationen

In diesem Tutorial wird eine neu erstellte oder dedizierte [Azure Key Vault](../key-vault/index.yml)-Instanz verwendet, um Anmeldeinformationen für Ihren SAP-Datenconnector zu speichern.

Gehen Sie wie folgt vor, um eine Azure Key Vault-Instanz zu erstellen oder zu bestimmen:

1. Erstellen Sie eine neue Azure Key Vault-Instanz, oder wählen Sie eine vorhandene Instanz aus, um sie für die Bereitstellung Ihres SAP-Datenconnectors festzulegen.

    Führen Sie beispielsweise die folgenden Befehle aus, um einen neuen Schlüsseltresor zu erstellen. Achten Sie darauf, dass Sie den Namen Ihrer Schlüsseltresor-Ressourcengruppe verwenden, und geben Sie den Namen Ihres Schlüsseltresors ein.

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create a key vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. Weisen Sie der verwalteten Identität der VM eine Zugriffsrichtlinie zu, einschließlich GET-, LIST- und SET-Berechtigungen, unter Verwendung einer der folgenden Methoden:

    - **Das Azure-Portal**:

        1. Wählen Sie in Ihrer Azure Key Vault-Instanz die Optionen **Zugriffsrichtlinien** > **Zugriffsrichtlinie hinzufügen – Geheimnisberechtigungen: Abrufen, Auflisten und Festlegen** > **Prinzipal auswählen**.  
        1. Geben Sie Ihren [VM-Namen](#deploy-a-linux-vm-for-your-sap-data-connector) ein, und wählen Sie dann **Hinzufügen** > **Speichern** aus.

        Weitere Informationen finden Sie in der [Key Vault](../key-vault/general/assign-access-policy-portal.md)-Dokumentation.

    - **Die Azure CLI**:

        1. Führen Sie den folgenden Befehl aus, um die [Prinzipal-ID der VM](#deploy-a-linux-vm-for-your-sap-data-connector) abzurufen. Achten Sie darauf, den Namen Ihrer Azure-Ressourcengruppe einzugeben.  

            ```azurecli
            VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
            ```  

            Ihre Prinzipal-ID wird angezeigt, damit Sie sie im nächsten Schritt verwenden können.

        1. Führen Sie den folgenden Befehl aus, um dem Schlüsseltresor die VM-Zugriffsberechtigungen zuzuweisen. Achten Sie darauf, den Namen Ihrer Ressourcengruppe und den Prinzipal-ID-Wert einzugeben, der im vorherigen Schritt zurückgegeben wurde.

            ```azurecli
            az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
            ```

## <a name="deploy-your-sap-data-connector"></a>Bereitstellen Ihres SAP-Datenconnectors

Das Bereitstellungsskript für den SAP-Datenconnector von Microsoft Sentinel installiert die [erforderliche Software](#automatically-installed-software) und dann den Connector auf Ihrer [neu erstellten VM](#deploy-a-linux-vm-for-your-sap-data-connector). Außerdem werden Anmeldeinformationen in Ihrem [dedizierten Schlüsseltresor](#create-a-key-vault-for-your-sap-credentials) gespeichert.

Das Bereitstellungsskript für den SAP-Datenconnector ist unter [Microsoft Sentinel-GitHub-Repository > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh) gespeichert.

Zum Ausführen des Bereitstellungsskripts für den SAP-Datenconnector benötigen Sie folgende Elemente:

- Ihre Microsoft Sentinel-Arbeitsbereichsdetails, wie sie im Abschnitt [Voraussetzungen](#prerequisites) aufgeführt sind.
- Die im Abschnitt [Voraussetzungen](#prerequisites) aufgeführten SAP-Systemdetails.
- Zugriff auf einen VM-Benutzer mit sudo-Berechtigungen.
- Den SAP-Benutzer, den Sie in [Konfigurieren Ihres SAP-Systems](#configure-your-sap-system) erstellt haben, mit der angewendeten Rolle **/MSFTSEN/SENTINEL_CONNECTOR**.
- Die Hilfe Ihres SAP-Teams

Gehen Sie wie folgt vor, um das Bereitstellungsskript für die SAP-Lösung auszuführen:

1. Führen Sie den folgenden Befehl aus, um die SAP-Lösung auf Ihrer VM bereitzustellen:

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. Befolgen Sie die Anweisungen auf dem Bildschirm, um Ihre SAP- und Key Vault-Details einzugeben und die Bereitstellung abzuschließen. Nach Abschluss der Bereitstellung wird eine Bestätigungsmeldung angezeigt:

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Microsoft Sentinel beginnt mit dem Abrufen von SAP-Protokollen für die konfigurierte Zeitspanne bis 24 Stunden vor der Initialisierung.

1. Es wird empfohlen, die Systemprotokolle zu überprüfen, um sicherzustellen, dass der Datenconnector Daten überträgt. Führen Sie Folgendes aus:

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>Bereitstellen von SAP-Sicherheitsinhalten

Stellen Sie die [SAP-Sicherheitsinhalte](sap-solution-security-content.md) aus den Bereichen **Lösungen** und **Watchlists** von Microsoft Sentinel bereit.

Mit der Lösung **Microsoft Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP** kann der SAP-Datenconnector im Microsoft Sentinel-Bereich **Datenconnectors** angezeigt werden. Die Lösung stellt außerdem die Arbeitsmappe **SAP – Systemanwendungen und -produkte** sowie SAP-bezogene Analyseregeln bereit.

Fügen Sie Ihrem Microsoft Sentinel-Arbeitsbereich manuell SAP-bezogene Watchlists hinzu.

Gehen Sie wie folgt vor, um SAP-Lösungssicherheitsinhalte bereitzustellen:

1. Wählen Sie in Microsoft Sentinel im linken Bereich **Lösungen (Vorschau)** aus.

    Auf der Seite **Lösungen** wird eine gefilterte, durchsuchbare Liste von Lösungen angezeigt.

1. Wählen Sie **Microsoft Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP (Vorschau)** aus, um die SAP-Lösungsseite zu öffnen.

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Screenshot des Lösungsbereichs „Microsoft Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP (Vorschau)“.":::

1. Um den Lösungsbereitstellungs-Assistenten zu starten, wählen Sie **Erstellen** aus, und geben Sie dann die Details zum Azure-Abonnement, der Ressourcengruppe und dem Log Analytics-Arbeitsbereich ein, in dem Sie die Lösung bereitstellen möchten.

1. Wählen Sie **Weiter** aus, um die Registerkarten **Datenconnectors**, **Analysen** und **Arbeitsmappen** zu durchlaufen. Dort erfahren Sie mehr über die Komponenten, die mit dieser Lösung bereitgestellt werden.

    Der Standardname für die Arbeitsmappe lautet **SAP – Systemanwendungen und Produkte – Vorschau**. Ändern Sie ihn bei Bedarf auf der Registerkarte „Arbeitsmappen“.

    Weitere Informationen finden Sie unter [SAP-Lösung von Microsoft Sentinel: Referenz zu Sicherheitsinhalten (Public Preview)](sap-solution-security-content.md).

1. Warten Sie, bis auf der Registerkarte **Überprüfen und erstellen** die Meldung **Überprüfung erfolgreich** angezeigt wird, und wählen Sie dann **Erstellen** aus, um die Lösung bereitzustellen.

    > [!TIP]
    > Sie können auch **Vorlage herunterladen** auswählen, um einen Link zum Bereitstellen der Lösung als Code zu erhalten.

1. Nach Abschluss der Bereitstellung wird oben rechts auf der Seite eine Bestätigungsmeldung angezeigt.

    Zum Anzeigen der neu bereitgestellten Inhalte navigieren Sie zu:

    - **Bedrohungsverwaltung** > **Arbeitsmappen** > **Meine Arbeitsmappen**, um die [integrierten SAP-Arbeitsmappen](sap-solution-security-content.md#built-in-workbooks) zu suchen.
    - **Konfiguration** > **Analysen**, um eine Reihe von [SAP-bezogenen Analyseregeln](sap-solution-security-content.md#built-in-analytics-rules) anzuzeigen.

1. Fügen Sie SAP-bezogene Watchlists hinzu, um sie in Ihrer Suche, für Erkennungsregeln, bei der Bedrohungssuche sowie in Playbooks für die Reaktion auf Bedrohungen zu verwenden. Diese Watchlists stellen die Konfiguration für die SAP-Lösung „Microsoft Sentinel – Kontinuierliche Bedrohungsüberwachung“ bereit. Gehen Sie folgendermaßen vor:

    a. Laden Sie SAP-Watchlists aus dem Microsoft Sentinel-GitHub-Repository unter https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists herunter.  
    b. Fügen Sie im Bereich **Watchlists** von Microsoft Sentinel die Watchlists zu ihrem Microsoft Sentinel-Arbeitsbereich hinzu. Verwenden Sie die heruntergeladenen CSV-Dateien als Quellen, und passen Sie sie dann entsprechend Ihrer Umgebung an.  

    [ ![Microsoft Sentinel hinzugefügte SAP-bezogene Watchlists](media/sap/sap-watchlists.png) ](media/sap/sap-watchlists.png#lightbox)

    Weitere Informationen finden Sie unter [Verwenden von Microsoft Sentinel-Watchlists](watchlists.md) und [Verfügbare SAP-Watchlists](sap-solution-security-content.md#available-watchlists).

1. Wechseln Sie in Microsoft Sentinel zum Datenconnector **Microsoft Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP**, um die Verbindung zu bestätigen:

    [ ![Screenshot der Datenconnectorseite „Microsoft Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP“.](media/sap/sap-data-connector.png) ](media/sap/sap-data-connector.png#lightbox)

    SAP ABAP-Protokolle werden auf der Seite **Protokolle** von Microsoft Sentinel unter **Benutzerdefinierte Protokolle** angezeigt:

    [ ![Screenshot der SAP ABAP-Protokolle im Bereich „Benutzerdefinierte Protokolle“ in Microsoft Sentinel.](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    Weitere Informationen finden Sie unter [Referenz zu Protokollen für die Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-log-reference.md).


## <a name="update-your-sap-data-connector"></a>Aktualisieren Ihres SAP-Datenconnectors

Wenn Sie über einen Docker-Container verfügen, der bereits mit einer früheren Version des SAP-Datenconnectors ausgeführt wird, führen Sie das Updateskript für den SAP-Datenconnector aus, um die neuesten verfügbaren Features zu erhalten.

Vergewissern Sie sich, dass Sie über die neuesten Versionen der relevanten Bereitstellungsskripts aus dem Microsoft Sentinel-GitHub-Repository verfügen. 

Führen Sie folgenden Befehl aus:

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

Der Docker-Container des SAP-Datenconnectors auf Ihrem Computer wird aktualisiert. 

Achten Sie darauf, dass Sie nach anderen verfügbaren Updates suchen, z. B.:

- Relevante SAP-Änderungsanforderungen im [GitHub-Repository für Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR).
- Microsoft Sentinel-Sicherheitsinhalte für SAP in der Lösung **Microsoft Sentinel – Kontinuierliche Bedrohungsüberwachung für SAP**.
- Relevante Watchlists im [GitHub-Repository für Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists).

## <a name="collect-sap-hana-audit-logs"></a>Erfassen von SAP HANA-Überwachungsprotokollen

Wenn die SAP HANA-Datenbanküberwachungsprotokolle mit Syslog konfiguriert sind, müssen Sie auch Ihren Log Analytics-Agent zum Erfassen der Syslog-Dateien konfigurieren.

1. Stellen Sie sicher, dass der SAP HANA-Überwachungsprotokollpfad für die Verwendung von Syslog konfiguriert ist, wie auf der [Launchpad-Website des SAP-Support](https://launchpad.support.sap.com/#/notes/0002624117) im *SAP-Hinweis 0002624117* beschrieben. Weitere Informationen finden Sie unter

    - [SAP HANA-Überwachungspfad – Bewährte Methode](https://archive.sap.com/documents/docs/DOC-51098)
    - [Empfehlungen für die Überwachung](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. Überprüfen Sie die Syslog-Dateien Ihres Betriebssystems auf relevante HANA-Datenbankereignisse.

1. Installieren und konfigurieren Sie einen Log Analytics-Agent auf Ihrem Computer:

    a. Melden Sie sich bei Ihrem HANA-Datenbankbetriebssystem als Benutzer mit sudo-Berechtigungen an.  
    b. Wechseln Sie im Azure-Portal zu Ihrem Log Analytics-Arbeitsbereich. Wählen Sie im linken Bereich unter **Einstellungen** die Option **Agent-Verwaltung** > **Linux-Server** aus.  
    c. Kopieren Sie den im Feld unter **Agent für Linux herunterladen und Onboarding durchführen** angezeigten Code in Ihr Terminal, und führen Sie dann das Skript aus.

    Der Log Analytics-Agent wird auf Ihrem Computer installiert und mit Ihrem Arbeitsbereich verbunden. Weitere Informationen finden Sie unter [Installieren des Log Analytics-Agents auf Linux-Computern](../azure-monitor/agents/agent-linux.md) und [OMS-Agent für Linux](https://github.com/microsoft/OMS-Agent-for-Linux) im Microsoft-GitHub-Repository.

1. Aktualisieren Sie die Registerkarte **Agent-Verwaltung > Linux-Server,** , um sich zu vergewissern, dass **1 Linux-Computer verbunden** ist.

1. Wählen Sie im linken Bereich unter **Einstellungen** die Option **Agent-Konfiguration** und dann die Registerkarte **Syslog** aus.

1. Wählen Sie **Facility hinzufügen** aus, um die Facilitys hinzuzufügen, die Sie erfassen möchten. 

    > [!TIP]
    > Da sich die Facilitys, in denen HANA-Datenbankereignisse gespeichert werden, zwischen verschiedenen Distributionen ändern können, empfiehlt es sich, alle Facilitys hinzuzufügen, sie anhand Ihrer Syslog-Protokolle zu überprüfen und dann alle nicht relevanten zu entfernen.
    >

1. Überprüfen Sie in Microsoft Sentinel, ob HANA-Datenbankereignisse jetzt in den erfassten Protokollen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die SAP-Lösungen von Microsoft Sentinel:

- [Bereitstellen des Microsoft Sentinel-Datenconnectors für SAP mit SNC](sap-solution-deploy-snc.md)
- [Konfigurationsoptionen für Experten, lokale Bereitstellung und SAPControl-Protokollquellen](sap-solution-deploy-alternate.md)
- [Detaillierte SAP-Anforderungen für die Microsoft Sentinel-Lösung für SAP (Public Preview)](sap-solution-detailed-requirements.md)
- [Referenz zu Protokollen für die Microsoft Sentinel-Lösung für SAP](sap-solution-log-reference.md)
- [Microsoft Sentinel-Lösung für SAP: integrierte Sicherheitsinhalte](sap-solution-security-content.md)
- [Problembehandlung bei der Bereitstellung der Microsoft Sentinel-Lösung für SAP](sap-deploy-troubleshoot.md)

Weitere Informationen finden Sie unter [Microsoft Sentinel-Lösungen](sentinel-solutions.md).

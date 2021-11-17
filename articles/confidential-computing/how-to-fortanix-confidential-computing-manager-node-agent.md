---
title: Ausführen einer App mit dem Fortanix Confidential Computing Manager
description: Hier erfahren Sie, wie Sie den Fortanix Confidential Computing Manager zum Konvertieren Ihrer containerisierten Images verwenden.
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcf3e3a1aab9114ceccc9eb32f96347ce3ed0fc2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306414"
---
# <a name="run-an-application-by-using-fortanix-confidential-computing-manager"></a>Ausführen einer Anwendung mit dem Fortanix Confidential Computing Manager

Hier erfahren Sie, wie Sie Ihre Anwendung in Azure Confidential Computing mit dem [Fortanix Confidential Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.em_managed?tab=Overview) und dem [Node-Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) von [Fortanix](https://www.fortanix.com/) ausführen.


Fortanix ist ein Software-Drittanbieter, der Produkte und Dienste bereitstellt, die mit der Azure-Infrastruktur funktionieren. Es gibt weitere Drittanbieter, die ähnliche Confidential Computing-Dienste für Azure anbieten.

> [!Note]
> Einige der Produkte, auf die in diesem Dokument verwiesen wird, werden von Microsoft nicht bereitgestellt. Microsoft stellt diese Informationen nur als Hinweis zur Verfügung. Verweise auf diese Nicht-Microsoft-Produkte bedeuten nicht, dass sie von Microsoft unterstützt werden.

In diesem Tutorial erfahren Sie, wie Sie Ihr Anwendungsimage in ein durch Confidential Computing geschütztes Image konvertieren können. In dieser Umgebung wird eine [Fortanix](https://www.fortanix.com/)-Software verwendet, die von Intel SGX-fähigen virtuellen Computern der DCsv2-Serie von Azure unterstützt wird. Diese Lösung orchestriert kritische Sicherheitsrichtlinien wie die Identitätsprüfung und die Datenzugriffssteuerung.

Treten Sie der [Fortanix Slack-Community](https://fortanix.com/community/) bei, um Unterstützung für Fortanix zu erhalten. Verwenden Sie den `#enclavemanager` Kanal.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie noch nicht über ein Fortanix Confidential Computing Manager-Konto verfügen, [registrieren Sie sich](https://ccm.fortanix.com/auth/sign-up), bevor Sie beginnen.
- Sie benötigen eine private [Docker](https://docs.docker.com/)-Registrierung für die Push-Übertragung konvertierter Anwendungsimages.
- Wenn Sie noch nicht über ein Azure-Abonnement verfügen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

> [!NOTE]
> Mit kostenlosen Testkonten ist kein Zugriff auf die virtuellen Computer, die in diesem Tutorial verwendet werden, möglich. Zum Abschließen des Tutorials benötigen Sie ein Abonnement mit der nutzungsbasierten Bezahlung.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Hinzufügen einer Anwendung zu Fortanix Confidential Computing Manager

1. Melden Sie sich bei [Fortanix Confidential Computing Manager (Fortanix CCM)](https://ccm.fortanix.com) an.
1. Navigieren Sie zur Seite **Konten**, und wählen Sie die Option **KONTO HINZUFÜGEN** aus, um ein neues Konto zu erstellen:

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="Der Screenshot zeigt, wie Sie ein Konto erstellen.":::

1. Nachdem Ihr Konto erstellt ist, klicken Sie auf **KONTO AUSWÄHLEN**, um das neu erstellte Konto auszuwählen. Jetzt können Sie mit dem Registrieren der Computeknoten und Erstellen von Anwendungen beginnen.
1. Wählen Sie auf der Registerkarte die Option **Anwendungen** und dann **+ ANWENDUNG**, um eine Anwendung hinzuzufügen. In diesem Beispiel fügen wir eine Enclave-Betriebssystemanwendung hinzu, die einen Python Flask-Server ausführt.

1. Wählen Sie die Schaltfläche **HINZUFÜGEN** aus, um die **Enclave OS-Anwendung** hinzuzufügen:

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="Screenshot des Hinzufügens einer Anwendung.":::

   > [!NOTE]
   > Dieses Tutorial beinhaltet nur das Hinzufügen von Enclave OS-Anwendungen. Informationen zum Hinzufügen von Unternehmensdatenschutz Rust-Anwendungen finden Sie unter [Unternehmensdatenschutz Rust-Apps in den Confidential Computing Manager bringen](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager).

1. In diesem Tutorial verwenden wir für die Beispielanwendung die Docker-Registrierung von Fortanix. Eingeben der angegebenen Werte für die folgenden Einstellungen. Verwenden Sie Ihre private Docker-Registrierung, um das Ausgabe-Image beizubehalten.

    - **Anwendungsname**: Python Application Server
    - **Beschreibung**: Python Flask Server
    - **Name des Eingabe-Images**: fortanix/python-flask
    - **Name des Ausgabe-Images**: fortanix-private/python-flask-sgx (Ersetzen Sie das durch Ihre eigene Registrierung.)
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Arbeitsspeichergröße**: 1 GB
    - **Threadanzahl**: 128

      *Optional*: Führen Sie die nicht konvertierte Anwendung aus.
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **App**: fortanix/python-flask

      Führen Sie den folgenden Befehl aus:

      ```bash
         sudo docker run fortanix/python-flask
      ```
      > [!NOTE]
      > Es wird nicht empfohlen, das Ausgabe-Image in Ihrer privaten Docker-Registrierung zu speichern.

1. Fügen Sie ein Zertifikat hinzu. Geben Sie die folgenden Werte ein, und wählen Sie dann **WEITER**:
    - **Domäne**: myapp.domain.dom
    - **Typ:** Dies ist ein von Confidential Computing Manager ausgestelltes Zertifikat.
    - **Schlüsselpfad**: /run/key.pem
    - **Schlüsseltyp**: RSA
    - **Zertifikatpfad**: /run/cert.pem
    - **RSA-Schlüsselgröße**: 2048 Bits

## <a name="create-an-image"></a>Erstellen eines Images

Ein Fortanix CCM-Image ist ein Softwarerelease oder eine Version einer Anwendung. Jedes Image ist einem Enclave-Hash (MRENCLAVE) zugeordnet.

1. Geben Sie auf der Seite **Image hinzufügen** die Registrierungsanmeldeinformationen für **Name des Ausgabe-Images** ein. Diese Anmeldeinformationen werden für den Zugriff auf die private Docker-Registrierung verwendet, in die das Image per Push übertragen wird. Das Eingabe-Image ist in einer öffentlichen Registrierung gespeichert. Daher müssen Sie keine Anmeldeinformationen für das Eingabe-Image angeben.
1. Geben Sie das Imagetag ein, und wählen Sie die Option **ERSTELLEN** aus:

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="Screenshot der zeigt, wie Sie ein Image erstellen.":::


## <a name="domain-and-image-allowlist"></a>Zulassungsliste für Domänen und Images

Eine Anwendung, deren Domäne zur Positivliste hinzugefügt wird, erhält von Fortanix Confidential Computing Manager ein TLS-Zertifikat. Wenn eine Enclave-Betriebssystemanwendung gestartet wird, kontaktiert sie Fortanix Confidential Computing Manager, um dieses TLS-Zertifikat zu erhalten.

Genehmigen Sie auf der Registerkarte **Aufgaben** auf der linken Seite des Bildschirms die ausstehenden Anforderungen, um die Domain und das Image zuzulassen.

## <a name="enroll-the-compute-node-agent-in-azure"></a>Registrieren eines Computeknoten-Agents in Azure

### <a name="create-and-copy-a-join-token"></a>Erstellen und Kopieren eines Beitrittstokens

Erstellen Sie jetzt ein Token in Fortanix Confidential Computing Manager. Dieses Token ermöglicht einem Computeknoten in Azure, sich selbst zu authentifizieren. Ihr virtueller Azure-Computer benötigt dieses Token.

1. Wählen Sie auf der Registerkarte **Computeknoten** die Option **KNOTEN REGISTRIEREN** aus.
1. Wählen die Schaltfläche **Kopieren**, um das Beitrittstoken zu kopieren. Dieses Beitrittstoken wird vom Computeknoten verwendet, um sich selbst zu authentifizieren.

### <a name="enroll-nodes-into-fortanix-node-agent"></a>Registrieren von Knoten in Fortanix Knoten-Agent

Beim Erstellen eines Fortanix Node Agents werden in Ihrer Azure-Ressourcengruppe ein virtueller Computer, eine Netzwerkschnittstelle, ein virtuelles Netzwerk, eine Netzwerksicherheitsgruppe und eine öffentliche IP-Adressen bereitgestellt. Ihr Azure-Abonnement für den virtuellen Computer wird stündlich abgerechnet. Bevor Sie einen Fortanix Node Agent erstellen, lesen Sie die [Azure-Seite mit den Preisen für virtuelle Computer ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) für die DCsv2-Serie. Löschen Sie alle Azure-Ressourcen, die Sie nicht verwenden.

1. Wechseln Sie zu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Geben Sie in dem Suchfeld **Fortanix Confidential Computing Node Agent** ein. Wählen Sie in den Suchergebnissen **Fortanix Confidential Computing Node Agent** um zu der [ Startseite mit den Apps](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent?tab=OverviewFortanix) zu navigieren:

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png" alt-text="Der Screenshot, zeigt, wie Sie die Startseite mit den Apps aufrufen.":::
1. Wählen Sie **Jetzt kaufen** aus. Geben Sie bei Bedarf Ihre Informationen ein, und wählen Sie **Weiter** aus. Sie werden zum Azure-Portal umgeleitet.
1. Wählen Sie **Erstellen** aus, um die Seite zur Bereitstellung des Fortanix Confidential Computing Node Agents aufzurufen.
1. Auf dieser Seite geben Sie die Informationen zur Bereitstellung eines virtuellen Computers ein. Dieser virtuelle Computer ist ein Intel SGX-fähiger virtueller Computer der DCsv2-Serie von Azure mit installierter Fortanix Node Agent-Software. Der Knoten-Agent ermöglicht Ihrem konvertierten Image die Ausführung auf Intel SGX-Knoten in Azure mit erhöhter Sicherheit. Wählen Sie das  Abonnement und die  Ressourcengruppe aus, in der Sie den virtuellen Computer und die zugehörigen Ressourcen bereitstellen möchten.

   > [!NOTE]
   > Es gelten Einschränkungen, wenn Sie virtuelle Computer der DCsv2-Serie in Azure bereitstellen. Möglicherweise müssen Sie Kontingente für zusätzliche Kerne anfordern. Weitere Informationen finden Sie unter [Confidential Computing-Lösungen auf Azure VMs](./virtual-machine-solutions-sgx.md).

1. Wählen Sie eine verfügbare Region aus.
1. Geben Sie in das Feld **Knotenname** einen Namen für Ihren virtuellen Computer ein.
1. Geben Sie einen Benutzernamen und ein Kennwort (oder SSH-Schlüssel) zur Authentifizierung beim virtuellen Computer ein.
1. Belassen Sie die Standardgröße des **Betriebssystem Azure Data Box Disk** bei **200**. Auswahl einer **Größe für den virtuellen Computer**. (**Standard_DC4s_v2** ist ausreichend für dieses Tutorial.)
1. Fügen Sie im Feld **Beitrittstoken** das Token ein, das Sie zuvor in diesem Tutorial erstellt haben:

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="Screenshot, der zeigt, wie Sie eine Ressourcengruppe bereitstellen.":::

1. Klicken Sie auf **Überprüfen + erstellen**. Vergewissern Sie sich, dass die Überprüfung erfolgreich war und wählen Sie dann **Erstellen** aus. Sobald alle Ressourcen bereitgestellt sind, ist der Computeknoten in Fortanix Confidential Computing Manager registriert.

## <a name="run-the-application-image-on-the-compute-node"></a>Ausführen des Anwendungsimages auf dem Computeknoten

Führen Sie den folgenden Befehl aus, um die Anwendung auszuführen. Achten Sie darauf, die Knoten-IP, den Port und den konvertierten Imagenamen in die Werte für Ihre Anwendung zu ändern.

In diesem Tutorial wird der folgende Befehl ausgeführt:

```bash
    sudo docker run \
        --device /dev/isgx:/dev/isgx \
        --device /dev/gsgx:/dev/gsgx \
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket \
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

In diesem Befehl:

- `52.152.206.164` ist die Host-IP-Adresse des Knoten-Agent.
- `9092` ist der Standardport, der den Knoten-Agent überwacht.
- `fortanix-private/python-flask-sgx` ist die konvertierte App. Sie finden es im Fortanix Confidential Computing Manager-Webportal. Sie finden es auf der Registerkarte **Images** in der Spalte **Imagename** der **Images**-Tabelle.

## <a name="verify-and-monitor-the-running-application"></a>Überprüfen und Überwachen der laufenden Anwendung

1. Kehren Sie zum [Fortanix Confidential Computing Manager](https://ccm.fortanix.com/console) zurück.
1. Stellen Sie sicher, dass Sie in dem **Konto** arbeiten, bei dem Sie den Knoten registriert haben.
1. Bestätigen Sie, auf der Registrierkarte **Anwendungen**,  dass es eine laufende Anwendung mit einem zugehörigen Computeknoten gibt.

## <a name="clean-up-resources"></a>Bereinigen der Ressourcen

Wenn die Ressourcengruppe, der virtuelle Computer und zugehörige Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Beim Löschen der Ressourcengruppe wird die Registrierung der Ihrem konvertierten Image zugeordneten Knoten aufgehoben.

Wählen Sie die Ressourcengruppe für den virtuellen Computer und dann **Löschen** aus. Bestätigen Sie dann den Namen der Ressourcengruppe, um das Löschen der Ressourcen abzuschließen.

Navigieren Sie zu der Seite [Konten](https://ccm.fortanix.com/accounts) in dem Fortanix Confidential Computing Manager, um das von Ihnen erstellte Fortanix Confidential Computing Manager-Konto zu löschen. Bewegen Sie den Mauszeiger über dem Konto, das Sie löschen möchten. Wählen Sie die vertikalen schwarzen Punkte in der oberen rechten Ecke und dann **KONTO LÖSCHEN** aus.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="Der Screenshot, zeigt, wie ein Konto gelöscht wird.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mit Fortanix-Tools Ihr Anwendungsimage so konvertiert, dass es auf einer virtuellen Confidential Computing-Maschine läuft. Weitere Informationen zu virtuellen Confidential Computing-Maschinen in Azure finden Sie unter [Lösungen für virtuelle Computer](virtual-machine-solutions-sgx.md).

Weitere Informationen zu den Confidential Computing-Angeboten von Azure finden Sie unter [Azure Confidential Computing-Übersicht](overview.md).

Erfahren Sie auch, wie Sie ähnliche Aufgaben mit anderen Drittanbieter-Angeboten für Azure wie [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) und [Scone](https://sconedocs.github.io) erledigen können.

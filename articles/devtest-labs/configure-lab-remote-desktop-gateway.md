---
title: Ein Lab zur Verwendung eines Remotedesktopgateways konfigurieren
description: Erfahren Sie, wie Sie ein Lab in Azure DevTest Labs mit einem Remotedesktopgateway konfigurieren, um sicheren Zugriff auf die virtuellen Computer (VMs) des Labs ohne Offenlegung des RDP-Ports zu gewährleisten.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: fcafc9f035d57e7685ffc3646cecaf27b60684f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652413"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurieren Ihres Labs in Azure DevTest Labs zum Verwenden eines Remotedesktopgateways
Sie können in Azure DevTest Labs ein Remotedesktopgateway für Ihr Lab konfigurieren, um sicheren Zugriff auf die virtuellen Computer (VMs) des Labs ohne Offenlegung des RDP-Ports zu gewährleisten. Das Lab bietet einen zentralen Ort für Ihre Lab-Benutzer zum Anzeigen aller virtuellen Computer, auf die sie Zugriff haben, sowie für den Zugriff auf diese. Die Schaltfläche **Verbinden** auf der Seite **Virtueller Computer** erstellt eine computerspezifische RDP-Datei, die Sie öffnen können, um eine Verbindung mit dem Computer herzustellen. Sie können die RDP-Verbindung weiter anpassen und sichern, indem Sie Ihr Lab mit einem Remotedesktopgateway verbinden. 

Dieser Ansatz ist sicherer, da sich der Lab-Benutzer direkt beim Gatewaycomputer authentifiziert oder auf einem in die Domäne integrierten Gatewaycomputer die Firmenanmeldeinformationen verwenden kann, um die Verbindung mit seinen Computern herzustellen. Das Lab unterstützt ferner die Verwendung von Tokenauthentifizierung für den Gatewaycomputer, die es Benutzern ermöglicht, Verbindungen mit ihren virtuellen Lab-Computern herzustellen, ohne den RDP-Port im Internet verfügbar zu machen. In diesem Artikel wird anhand eines Beispiels das Einrichten eines Labs beschrieben, bei dem die Tokenauthentifizierung zum Herstellen von Verbindungen mit den Lab-Computern verwendet wird.

## <a name="architecture-of-the-solution"></a>Architektur der Lösung

![Architektur der Lösung](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Die Aktion [RDP-Dateiinhalte abrufen](/rest/api/dtl/virtualmachines/getrdpfilecontents) wird aufgerufen, wenn Sie die Schaltfläche **Verbinden** auswählen. 
1. Die Aktion „RDP-Dateiinhalte abrufen“ ruft `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` auf, um ein Authentifizierungstoken anzufordern.
    1. `{gateway-hostname}` ist der Hostname des Gateways, der auf der Seite **Labeinstellungen** für Ihr Lab im Azure-Portal angegeben wurde. 
    1. `{lab-machine-name}` ist der Name des Computers, mit dem Sie eine Verbindung herstellen möchten.
    1. `{port-number}` ist der Port, über den die Verbindung hergestellt werden muss. Dies ist in der Regel Port 3389. Wenn die Lab-VM die Funktion für [freigegebene IP-Adressen](devtest-lab-shared-ip.md) in DevTest Labs nutzt, wird ein anderer Port verwendet.
1. Das Remotedesktopgateway verzögert den Aufruf von `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` an eine Azure-Funktion zum Generieren des Authentifizierungstokens. Der DevTest Labs-Dienst fügt den Funktionsschlüssel automatisch in den Anforderungsheader ein. Der Funktionsschlüssel muss im Schlüsseltresor des Labs gespeichert werden. Der Name für dieses Geheimnis wird als **Gatewaytokengeheimnis** auf der Seite **Labeinstellungen** für das Lab angezeigt.
1. Von der Azure-Funktion wird die Rückgabe eines Tokens für die zertifikatbasierte Tokenauthentifizierung beim Gatewaycomputer erwartet.  
1. Die Aktion „RDP-Dateiinhalte abrufen“ gibt dann die vollständige RDP-Datei zurück, einschließlich der Authentifizierungsinformationen.
1. Sie öffnen die RDP-Datei mit Ihrem bevorzugten RDP-Verbindungsprogramm. Denken Sie daran, dass nicht alle RDP-Verbindungsprogramm die Tokenauthentifizierung unterstützen. Das Authentifizierungstoken verfügt über ein von der Funktions-App festgelegtes Ablaufdatum. Stellen Sie die Verbindung mit der Lab-VM her, bevor das Token abläuft.
1. Nachdem das Token in der RDP-Datei vom Remotedesktopgateway-Computer authentifiziert wurde, wird die Verbindung an den Lab-Computer weitergeleitet.

### <a name="solution-requirements"></a>Lösungsanforderungen
Für die Verwendung der Funktion für die Tokenauthentifizierung in DevTest Labs müssen einige Konfigurationsanforderungen für Gatewaycomputer, DNS (Domain Name Service) und die Funktionen erfüllt werden.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Anforderungen für Remotedesktopgateway-Computer
- Das TLS-/SSL-Zertifikat muss auf dem Gatewaycomputer installiert sein, damit HTTPS-Datenverkehr verarbeitet werden kann. Das Zertifikat muss mit dem vollqualifizierten Domänennamen (FQDN) des Lastenausgleichs für die Gatewayfarm oder dem FQDN des Computers selbst übereinstimmen, wenn es nur einen Computer gibt. TLS-/SSL-Platzhalterzertifikate funktionieren nicht.  
- Auf dem Gatewaycomputer muss ein Signaturzertifikat installiert sein. Sie erstellen ein Signaturzertifikat mithilfe des Skripts [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1).
- Installieren Sie das Modul [Pluggable Authentication](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273), das die Tokenauthentifizierung für das Remotedesktopgateway unterstützt. Ein Beispiel für ein solches Modul ist `RDGatewayFedAuth.msi`, das in den [Images von System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807&preserve-view=true) enthalten ist. Weitere Informationen zu System Center finden Sie in der [Dokumentation zu System Center](/system-center/) und den [Preisdetails](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Der Gatewayserver kann Anforderungen an `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` verarbeiten.

    Der Gatewayhostname ist der FQDN des Lastenausgleichs der Gatewayfarm oder der FQDN des Computers selbst, wenn es nur einen Computer gibt. `{lab-machine-name}` ist der Name des Lab-Computers, mit dem Sie versuchen, eine Verbindung herzustellen, und `{port-number}` ist der Port, über den die Verbindung hergestellt werden soll.  Das ist standardmäßig Port 3389.  Wenn der virtuelle Computer jedoch die Funktion für [freigegebene IP-Adressen](devtest-lab-shared-ip.md) in DevTest Labs nutzt, wird ein anderer Port verwendet.
- Das Modul [Application Routing Request](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) für IIS (Internet Information Server) kann verwendet werden, um Anforderungen vom Typ `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` an die Azure-Funktion weiterzuleiten, die die Anforderung zum Abrufen eines Tokens für die Authentifizierung behandelt.


## <a name="requirements-for-azure-function"></a>Anforderungen an die Azure-Funktion
Die Azure-Funktion verarbeitet Anforderungen der Form `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` und gibt das Authentifizierungstoken basierend auf demselben Signaturzertifikat zurück, das auf den Gatewaycomputern installiert ist. `{function-app-uri}` ist der URI für den Zugriff auf die Funktion. Der Funktionsschlüssel wird automatisch im Header der Anforderung übergeben. Eine Beispielfunktion finden Sie unter [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Anforderungen an das Netzwerk

- Das DNS für den vollqualifizierten Domänennamen, der dem TLS-/SSL-Zertifikat auf den Gatewaycomputern zugeordnet ist, muss Datenverkehr an den Gatewaycomputer oder den Load Balancer der Gatewayfarm weiterleiten.
- Wenn der Lab-Computer private IP-Adressen verwendet, muss es einen Netzwerkpfad vom Gatewaycomputer zum Lab-Computer geben – entweder, indem beide demselben virtuellen Netzwerk angehören oder mithilfe von mittels Peering verknüpften virtuellen Netzwerken.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurieren des Labs für die Verwendung der Tokenauthentifizierung 
In diesem Abschnitt wird das Konfigurieren eines Labs für die Verwendung eines Remotedesktopgateway-Computers beschrieben, der die Tokenauthentifizierung unterstützt. In diesem Abschnitt wird jedoch nicht das Einrichten einer Remotedesktop-Gatewayfarm selbst behandelt. Informationen dazu finden Sie im Abschnitt mit dem [Beispiel für das Erstellen eines Remotedesktopgateways](#sample-to-create-a-remote-desktop-gateway) am Ende dieses Artikels. 

Bevor Sie die Labeinstellungen ändern, speichern Sie den Schlüssel, den Sie benötigen, um die Funktion zum Zurückgeben eines Authentifizierungstokens im Schlüsseltresor des Labs speichern. Sie finden den Funktionsschlüsselwert auf der Seite **Verwalten** der Funktion im Azure-Portal. Weitere Informationen zum Speichern eines Geheimnisses in einem Schlüsseltresor finden Sie unter [Hinzufügen eines Geheimnisses zu Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Speichern Sie den Namen des Geheimnisses zur späteren Verwendung.

Um die ID des Schlüsseltresors des Labs zu ermitteln, führen Sie den folgenden Azure CLI-Befehl aus: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurieren Sie das Lab für die Verwendung der Tokenauthentifizierung anhand der folgenden Schritte:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
1. Wählen Sie in der Liste mit den Labs Ihr **Lab** aus.
1. Wählen Sie auf der Seite des Labs die Option **Konfiguration und Richtlinien** aus.
1. Wählen Sie im linken Menü im Abschnitt **Einstellungen** die Option **Labeinstellungen** aus.
1. Geben Sie im Abschnitt **Remotedesktop** den vollqualifizierten Domänennamen (FQDN) oder die IP-Adresse des Gatewaycomputers oder der -farm für die Remotedesktopdienste im Feld **Gatewayhostname** ein. Dieser Wert muss dem FQDN des auf dem Gatewaycomputer verwendeten TLS-/SSL-Zertifikats entsprechen.

    ![Remotedesktopoptionen in den Labeinstellungen](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Geben Sie im Abschnitt **Remotedesktop** für das **Gatewaytokengeheimnis** den Namen des zuvor erstellten Geheimnisses ein. Dieser Wert ist nicht der Funktionsschlüssel selbst, sondern der Name des Geheimnisses im Schlüsseltresor des Labs, der den Funktionsschlüssel enthält.

    ![Gatewaytokengeheimnis in den Labeinstellungen](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Speichern** Sie die Änderungen.

    > [!NOTE] 
    > Durch Klicken auf **Speichern** stimmen Sie den [Lizenzbedingungen des Remotedesktopgateways](https://www.microsoft.com/licensing/product-licensing/products) zu. Weitere Informationen zu Remotegateways finden Sie unter [Willkommen bei den Remotedesktopdiensten](/windows-server/remote/remote-desktop-services/Welcome-to-rds) und [Bereitstellen einer Remotedesktopumgebung](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Falls Sie das Konfigurieren des Lab automatisieren möchten, finden Sie unter [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) ein PowerShell-Beispielskript, mit dem die Einstellungen **Gatewayhostname** und **Gatewaytokengeheimnis** festgelegt werden können. Die [GitHub-Repository zu Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) enthält auch eine Azure Resource Manager-Vorlage, die ein Lab mit den Einstellungen **Gatewayhostname** und **Gatewaytokengeheimnis** erstellt oder aktualisiert.

## <a name="configure-network-security-group"></a>Konfigurieren von Netzwerksicherheitsgruppen
Um das Lab weiter zu schützen, können Sie dem virtuellen Netzwerk, das von den Lab-VMs verwendet wird, eine Netzwerksicherheitsgruppe (NSG) hinzufügen. Anweisungen zum Einrichten einer NSG finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](../virtual-network/manage-network-security-group.md).

Die folgende Beispiel-NSG erlaubt nur Datenverkehr über das Gateway an die Lab-Computer. Die Quelle in dieser Regel ist die IP-Adresse des einzelnen Gatewaycomputers oder die IP-Adresse des Lastenausgleichs vor den Gatewaycomputern.

![Netzwerksicherheitsgruppen – Regeln](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Beispiel für das Erstellen eines Remotedesktopgateways

> [!NOTE] 
> Mit der Verwendung der Beispielvorlagen stimmen Sie den [Lizenzbedingungen des Remotedesktopgateways](https://www.microsoft.com/licensing/product-licensing/products) zu. Weitere Informationen zu Remotegateways finden Sie unter [Willkommen bei den Remotedesktopdiensten](/windows-server/remote/remote-desktop-services/Welcome-to-rds) und [Bereitstellen einer Remotedesktopumgebung](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

Das [GitHub-Repository zu Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) enthält einige Beispiele, die Ihnen beim Einrichten der Ressourcen helfen, die Sie für die Verwendung der Tokenauthentifizierung und eines Remotedesktopgateways mit DevTest Labs benötigen. Diese Beispiele enthalten Azure Resource Manager-Vorlagen für Gatewaycomputer, Labeinstellungen und Funktions-Apps.

Befolgen Sie zum Einrichten einer Beispiellösung für die Remotedesktopgateway-Farm die folgenden Schritte.

1. Erstellen Sie ein Signaturzertifikat.  Führen Sie [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) aus. Speichern Sie Fingerabdruck, Kennwort und Base64-Codierung des erstellten Zertifikats.
2. Rufen Sie ein TLS/SSL-Zertifikat ab. Der FQDN, der dem TLS-/SSL-Zertifikat zugeordnet ist, muss für die von Ihnen gesteuerte Domäne gelten. Speichern Sie Fingerabdruck, Kennwort und Base64-Codierung dieses Zertifikats. Wenn Sie den Fingerabdruck mithilfe von PowerShell abrufen möchten, verwenden Sie die folgenden Befehle:

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import('path-to-certificate');
    $hash = $cer.GetCertHashString()
    ```

    Verwenden Sie zum Abrufen der Base64-Codierung mithilfe von PowerShell die folgenden Befehle:

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes('path-to-certificate'))
    ```
3. Laden Sie die Dateien von [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) herunter.

    Die Vorlage erfordert Zugriff auf einige andere Resource Manager-Vorlagen und verknüpfte Ressourcen unter demselben Basis-URI. Kopieren Sie alle Dateien unter [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) und „RDGatewayFedAuth.msi“ in einem Blobcontainer in einem Speicherkonto.  
4. Stellen Sie **azuredeploy.json** von [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) bereit. Die Vorlage akzeptiert die folgenden Parameter:
    - adminUsername: erforderlich.  Administratorbenutzername für die Gatewaycomputer.
    - adminPassword: erforderlich. Kennwort für das Administratorkonto für die Gatewaycomputer.
    - instanceCount: Anzahl der zu erstellenden Gatewaycomputer.  
    - alwaysOn: gibt an, ob die erstellte Azure Functions-App in einem betriebsbereiten Zustand („warm“) bleiben soll. Das Beibehalten von Azure Functions-Apps vermeidet Verzögerungen, wenn Benutzer zum ersten Mal versuchen, eine Verbindung mit ihrer Lab-VM herzustellen. Diese Methode hat jedoch Auswirkungen auf die Kosten.  
    - tokenLifetime: Zeitdauer, die das erstellte Token gültig sein soll. Das Format lautet HH:MM:SS.
    - sslCertificate: Base64-Codierung des TLS-/SSL-Zertifikats für den Gatewaycomputer.
    - sslCertificatePassword: Kennwort des TLS-/SSL-Zertifikats für den Gatewaycomputer.
    - sslCertificateThumbprint: Zertifikatfingerabdruck für die Identifizierung im lokalen Zertifikatspeicher des TLS-/SSL-Zertifikats.
    - signCertificate: Base64-Codierung des Signaturzertifikats für den Gatewaycomputer.
    - signCertificatePassword: Kennwort des Signaturzertifikats für den Gatewaycomputer.
    - signCertificateThumbprint: Zertifikatfingerabdruck für die Identifizierung im lokalen Zertifikatspeicher des Signaturzertifikats.
    - _artifactsLocation: URI des Speicherorts aller unterstützenden Ressourcen. Dieser Wert muss ein vollqualifizierter UIR sein (kein relativer Pfad).
    - _artifactsLocationSasToken: SAS-Token (Shared Access Signature) für den Zugriff auf die unterstützenden Ressourcen, wenn der Speicherort ein Azure Storage-Konto ist.

    Die Vorlage kann mithilfe der Azure-Befehlszeilenschnittstelle mit dem folgenden Befehl bereitgestellt werden:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Beschreibungen der Parameter:

    - {storage-account-endpoint} können Sie durch Ausführen von `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` abrufen.  {storage-acct-name} ist der Name des Speicherkontos mit den Dateien, die Sie hochgeladen haben.  
    - {container-name} ist der Name des Containers im {storage-acct-name} mit den Dateien, die Sie hochgeladen haben.  
    - {sas-Token} können Sie durch Ausführen von `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` abrufen. 
        - {storage-acct-name} ist der Name des Speicherkontos mit den Dateien, die Sie hochgeladen haben.  
        - {container-name} ist der Name des Containers im {storage-acct-name} mit den Dateien, die Sie hochgeladen haben.  
        - {utc-expiration-Date} ist das Datum (in UTC), zu dem das SAS-Token abläuft und nicht mehr für den Zugriff auf das Speicherkonto verwendet werden kann.

    Notieren Sie die Werte für gatewayFQDN und gatewayIP in der Ausgabe der Vorlagenbereitstellung. Sie müssen auch den Wert des Funktionsschlüssels für die neu erstellte Funktion speichern, den Sie auf der Registerkarte [Einstellungen für Funktions-Apps](../azure-functions/functions-how-to-use-azure-function-app-settings.md) finden.
5. Konfigurieren Sie DNS so, dass der FQDN des TLS-/SSL-Zertifikats auf die IP-Adresse von gatewayIP aus dem vorherigen Schritt verweist.

    Nachdem die Remotedesktopgateway-Farm erstellt und die entsprechenden DNS-Aktualisierungen vorgenommen wurden, kann sie von einem Lab in DevTest Labs verwendet werden. Die Einstellungen **Gatewayhostname** und **Gatewaytokengeheimnis** müssen konfiguriert sein, damit die Gatewaycomputer, die Sie bereitgestellt haben, verwendet werden können. 

    > [!NOTE]
    > Wenn der Lab-Computer private IP-Adressen verwendet, muss es einen Netzwerkpfad vom Gatewaycomputer zum Lab-Computer geben – entweder, indem beide demselben virtuellen Netzwerk angehören oder mithilfe von mittels Peering verknüpften virtuellen Netzwerken.

    Wenn sowohl das Gateway als auch das Lab konfiguriert wurden, enthält die Verbindungsdatei, die erstellt wird, wenn ein Lab-Benutzer auf **Verbinden** klickt, automatisch die erforderlichen Informationen zum Herstellen einer Verbindung mithilfe der Tokenauthentifizierung.     

## <a name="next-steps"></a>Nächste Schritte
Im folgenden Artikel erfahren Sie mehr über Remotedesktopdienste: [Dokumentation zu Remotedesktopdienste](/windows-server/remote/remote-desktop-services/Welcome-to-rds) (Remote Desktop Services, RDS).
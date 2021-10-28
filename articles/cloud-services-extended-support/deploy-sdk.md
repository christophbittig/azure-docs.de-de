---
title: Bereitstellen von Cloud Services (erweiterter Support) – SDK
description: Bereitstellen von Cloud Services (erweiterter Support) mithilfe des Azure SDK
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 774b118c1aa74c7de561e7b54843183ac4fc0afb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229865"
---
# <a name="deploy-cloud-services-extended-support-by-using-the-azure-sdk"></a>Bereitstellen von Cloud Services (erweiterter Support) mithilfe des Azure SDK

In diesem Artikel erfahren Sie, wie Sie das [Azure SDK](https://azure.microsoft.com/downloads/) verwenden, um eine Instanz von Cloud Services (erweiterter Support) mit mehreren Rollen (Webrolle und Workerrolle) sowie mit der Remotedesktoperweiterung bereitzustellen. Cloud Services (erweiterter Support) ist ein Bereitstellungsmodell von Azure Cloud Services, das auf dem Azure Resource Manager basiert.

## <a name="before-you-begin"></a>Vorbereitung

Informieren Sie sich über die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support), und erstellen Sie die entsprechenden Ressourcen.

## <a name="deploy-cloud-services-extended-support"></a>Bereitstellen von Cloud Services (erweiterter Support)
1. Installieren Sie das [NuGet-Paket für das Azure Compute SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute/43.0.0-preview), und initialisieren Sie den Client mit einem Standardauthentifizierungsmechanismus.

    ```csharp
        public class CustomLoginCredentials : ServiceClientCredentials
        {
            private string AuthenticationToken { get; set; }
            public override void InitializeServiceClient<T>(ServiceClient<T> client)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantID}");
                var credential = new ClientCredential(clientId: "{clientID}", clientSecret: "{clientSecret}");
                var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
                if (result == null) throw new InvalidOperationException("Failed to obtain the JWT token");
                AuthenticationToken = result.Result.AccessToken;
            }
            public override async Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                if (request == null) throw new ArgumentNullException("request");
                if (AuthenticationToken == null) throw new InvalidOperationException("Token Provider Cannot Be Null");
                request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", AuthenticationToken);
                request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                //request.Version = new Version(apiVersion);
                await base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }
    
        var creds = new CustomLoginCredentials();
        m_subId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
        ResourceManagementClient m_ResourcesClient = new ResourceManagementClient(creds);
        NetworkManagementClient m_NrpClient = new NetworkManagementClient(creds);
        ComputeManagementClient m_CrpClient = new ComputeManagementClient(creds);
        StorageManagementClient m_SrpClient = new StorageManagementClient(creds);
        m_ResourcesClient.SubscriptionId = m_subId;
        m_NrpClient.SubscriptionId = m_subId;
        m_CrpClient.SubscriptionId = m_subId;
        m_SrpClient.SubscriptionId = m_subId;
    ```

2. Erstellen Sie eine neue Ressourcengruppe, indem Sie das NuGet-Paket für Azure Resource Manager installieren.

    ```csharp 
    var resourceGroups = m_ResourcesClient.ResourceGroups;
    var m_location = “East US”;
    var resourceGroupName = "ContosoRG";//provide existing resource group name, if created already
    var resourceGroup = new ResourceGroup(m_location); 
    resourceGroup = await resourceGroups.CreateOrUpdateAsync(resourceGroupName, resourceGroup);
    ```

3. Erstellen Sie ein Speicherkonto und einen Container zum Speichern der Dienstpaketdatei (.cspkg) und der Dienstkonfigurationsdatei (.cscfg). Installieren Sie das [NuGet-Paket für Azure Storage](https://www.nuget.org/packages/Azure.Storage.Common/). Bei Verwendung eines vorhandenen Speicherkontos ist dieser Schritt optional. Der Name des Speicherkontos muss eindeutig sein.

    ```csharp
    string storageAccountName = “ContosoSAS”
    var stoInput = new StorageAccountCreateParameters
       {
            Location = m_location,
            Kind = Microsoft.Azure.Management.Storage.Models.Kind.StorageV2,
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku(SkuName.StandardRAGRS),
        };
            StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.Create(rgName,
            storageAccountName, stoInput);
            bool created = false;
            while (!created)
               {
                    Thread.Sleep(600);
                    var stos = m_SrpClient.StorageAccounts.ListByResourceGroup(rgName);
                    created =
                    stos.Any(
                        t =>
                        StringComparer.OrdinalIgnoreCase.Equals(t.Name, storageAccountName));
                }
        
    StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.GetProperties(rgName, storageAccountName);.
    var accountKeyResult = m_SrpClient.StorageAccounts.ListKeysWithHttpMessagesAsync(rgName, storageAccountName).Result;
    CloudStorageAccount storageAccount = new CloudStorageAccount(new StorageCredentials(storageAccountName, accountKeyResult.Body.Keys.FirstOrDefault(). Value), useHttps: true);
        
    var blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExistsAsync().Wait();
    sharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddDays(-1);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddDays(2);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
    ```

4. Laden Sie die Dienstpaketdatei (.cspkg) in das Speicherkonto hoch. Bei der Paket-URL kann es sich um einen SAS-URI (Shared Access Signature) aus einem beliebigen Speicherkonto handeln.

    ```csharp
    CloudBlockBlob cspkgblockBlob = container.GetBlockBlobReference(“ContosoApp.cspkg”);
    cspkgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cspkg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string cspkgsasContainerToken = cspkgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cspkgSASUrl = cspkgblockBlob.Uri + cspkgsasContainerToken;
    ```

5. Laden Sie Ihre Dienstkonfigurationsdatei (.cscfg) in das Speicherkonto hoch. Geben Sie die Dienstkonfiguration im Zeichenfolgen-XML- oder URL-Format an.

    ```csharp
    CloudBlockBlob cscfgblockBlob = container.GetBlockBlobReference(“ContosoApp.cscfg”);
    cscfgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cscfg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasCscfgContainerToken = cscfgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cscfgSASUrl = cscfgblockBlob.Uri + sasCscfgContainerToken;
    ```

6. Erstellen Sie ein virtuelles Netzwerk und ein Subnetz. Installieren Sie das [NuGet-Paket für das Azure-Netzwerk](https://www.nuget.org/packages/Azure.ResourceManager.Network/). Bei Verwendung eines vorhandenen Netzwerks und Subnetzes ist dieser Schritt optional.

    ```csharp
    VirtualNetwork vnet = new VirtualNetwork(name: vnetName) 
       { 
            AddressSpace = new AddressSpace 
               { 
                    AddressPrefixes = new List<string> { "10.0.0.0/16" } 
               }, 
                    Subnets = new List<Subnet> 
                    { 
                        new Subnet(name: subnetName) 
                        { 
                            AddressPrefix = "10.0.0.0/24" 
                        } 
                    }, 
                    Location = m_location 
               }; 
    m_NrpClient.VirtualNetworks.CreateOrUpdate(resourceGroupName, “ContosoVNet”, vnet);
    ```

7. Erstellen Sie eine öffentliche IP-Adresse, und legen Sie die DNS-Bezeichnungseigenschaft der öffentlichen IP-Adresse fest. Von Cloud Services (erweiterter Support) werden nur öffentliche IP-Adressen mit der SKU [Basic](../virtual-network/ip-services/public-ip-addresses.md#basic) unterstützt. Öffentliche IP-Adressen der Standard-SKU funktionieren nicht mit Cloud Services.
Wenn Sie eine statische IP-Adresse verwenden, muss darauf in der Dienstkonfigurationsdatei (.cscfg) als reservierte IP verwiesen werden.

    ```csharp
    PublicIPAddress publicIPAddressParams = new PublicIPAddress(name: “ContosIp”) 
       { 
            Location = m_location, 
            PublicIPAllocationMethod = IPAllocationMethod.Dynamic, 
            DnsSettings = new PublicIPAddressDnsSettings() 
               { 
                    DomainNameLabel = “contosoappdns” 
               } 
       }; 
    PublicIPAddress publicIpAddress = m_NrpClient.PublicIPAddresses.CreateOrUpdate(resourceGroupName, publicIPAddressName, publicIPAddressParams);
    ```

8. Erstellen Sie ein Netzwerkprofilobjekt, und ordnen Sie die öffentliche IP-Adresse dem Front-End des Lastenausgleichs zu. Die Azure-Plattform erstellt automatisch eine Lastenausgleichsressource der SKU „Klassisch“ in demselben Abonnement wie die Clouddienstressource. Die Lastenausgleichsressource ist eine schreibgeschützte Ressource in ARM. Alle Aktualisierungen der Ressource werden nur über die Clouddienst-Bereitstellungsdateien (.cscfg und .csdef) unterstützt.

    ```csharp
    LoadBalancerFrontendIPConfiguration feipConfiguration = new LoadBalancerFrontendIPConfiguration() 
        { 
            Name = “ContosoFe”, 
            Properties = new LoadBalancerFrontendIPConfigurationProperties() 
                { 
                PublicIPAddress = new CM.SubResource() 
                    { 
                        Id = $"/subscriptions/{m_subId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{publicIPAddressName}", 
                    } 
                } 
        }; 
    
    CloudServiceNetworkProfile cloudServiceNetworkProfile = new CloudServiceNetworkProfile() 
        { 
            LoadBalancerConfigurations = new List<LoadBalancerConfiguration>() 
                { 
                    new LoadBalancerConfiguration() 
                       { 
                        Name  = 'ContosoLB', 
                        Properties = new LoadBalancerConfigurationProperties() 
                            { 
                            FrontendIPConfigurations = new List<LoadBalancerFrontendIPConfiguration>() 
                                { 
                                feipConfig 
                                } 
                            } 
                        } 
                } 
        }; 
    
    ```

9. Erstellen eines Schlüsseltresors Dieser Schlüsseltresor wird zum Speichern von Zertifikaten verwendet, die den Rollen von Cloud Services (erweiterter Support) zugeordnet sind. Der Schlüsseltresor muss sich in der gleichen Region und im gleichen Abonnement befinden wie die Instanz von Cloud Services (erweiterter Support) und einen eindeutigen Namen besitzen. Weitere Informationen finden Sie unter [Verwenden von Zertifikaten mit Azure Cloud Services (erweiterter Support)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US”
    ```

10. Aktualisieren Sie die Zugriffsrichtlinie des Schlüsseltresors, und erteilen Sie Ihrem Benutzerkonto Zertifikatberechtigungen.

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg'      -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
    ```

    Alternativ können Sie die Zugriffsrichtlinie über die Objekt-ID festlegen (die Objekt-ID kann mithilfe von `Get-AzADUser` abgerufen werden).

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -     ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates          create,get,list,delete
    ```

11. In diesem Beispiel fügen wir einem Schlüsseltresor ein selbstsigniertes Zertifikat hinzu. Der Zertifikatfingerabdruck muss in der Dienstkonfigurationsdatei (.cscfg) zur Bereitstellung für Rollen von Cloud Services (erweiterter Support) hinzugefügt werden.

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -       SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -      CertificatePolicy $Policy
    ```

12. Erstellen Sie ein Betriebssystemprofilobjekt. Im Betriebssystemprofil sind die Zertifikate angegeben, die Rollen von Cloud Services (erweiterter Support) zugeordnet sind. In diesem Fall ist es das Zertifikat, das im vorherigen Schritt erstellt wurde.

    ```csharp
    CloudServiceOsProfile cloudServiceOsProfile = 
        new CloudServiceOsProfile 
           { 
                Secrets = new List<CloudServiceVaultSecretGroup> 
                   { 
                        New CloudServiceVaultSecretGroup { 
                        SourceVault = <sourceVault>, 
                        VaultCertificates = <vaultCertificates> 
                        } 
                   } 
           };
    ```

13. Erstellen Sie ein Rollenprofilobjekt. Ein Rollenprofil definiert rollenspezifische Eigenschaften für eine SKU wie Name, Kapazität und Ebene. 

    In diesem Beispiel werden zwei Rollen definiert: „ContosoFrontend“ und „ContosoBackend“. Die Rollenprofilinformationen müssen der Rollenkonfiguration entsprechen, die in der Dienstkonfigurationsdatei (.cscfg) und in der Dienstdefinitionsdatei (.csdef) definiert ist.

    ```csharp
    CloudServiceRoleProfile cloudServiceRoleProfile = new CloudServiceRoleProfile()
       {
            Roles = new List<CloudServiceRoleProfileProperties>();
            
                // foreach role in cloudService
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoFrontend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoBackend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                    }
                    }
    ```

14. Optional: Erstellen Sie ein Erweiterungsprofilobjekt, das Sie Ihrer Instanz von Cloud Services (erweiterter Support) hinzufügen möchten. In diesem Beispiel wird eine RDP-Erweiterung hinzugefügt.

    ```csharp
    string rdpExtensionPublicConfig = "<PublicConfig>" +
        "<UserName>adminRdpTest</UserName>" +
        "<Expiration>2021-10-27T23:59:59</Expiration>" +
        "</PublicConfig>";
        string rdpExtensionPrivateConfig = "<PrivateConfig>" +
        "<Password>VsmrdpTest!</Password>" +
        "</PrivateConfig>";
    
        Extension rdpExtension = new Extension
                {
                    Name = name,
                    Properties = new CloudServiceExtensionProperties
                    {
                        Publisher = "Microsoft.Windows.Azure.Extensions",
                        Type = "RDP",
                        TypeHandlerVersion = "1.2.1",,
                        AutoUpgradeMinorVersion = true,
                        Settings = rdpExtensionPublicConfig,
                        ProtectedSettings = rdpExtensionPrivateConfig,
                        RolesAppliedTo = [“*”],
                    }
                };
                        
    CloudServiceExtensionProfile cloudServiceExtensionProfile = new CloudServiceExtensionProfile
        {
            Extensions = rdpExtension
        };
    ```

15. Erstellen Sie die Bereitstellung der Instanz von Cloud Services (erweiterter Support).

    ```csharp
    CloudService cloudService = new CloudService
        {
            Properties = new CloudServiceProperties
                {
                    RoleProfile = cloudServiceRoleProfile
                    Configuration = < Add Cscfg xml content here>,
                    // ConfigurationUrl = <Add your configuration URL here>,
                    PackageUrl = <Add cspkg SAS url here>,
                    ExtensionProfile = cloudServiceExtensionProfile,
                    OsProfile= cloudServiceOsProfile,
                    NetworkProfile = cloudServiceNetworkProfile,
                    UpgradeMode = 'Auto'
                },
                    Location = m_location
                };
    
    CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
    ```

## <a name="next-steps"></a>Nächste Schritte
- Sehen Sie sich die [häufig gestellten Fragen](faq.yml) zu Cloud Services (erweiterter Support) an.
- Stellen Sie eine Instanz von Cloud Services (erweiterter Support) über das [Azure-Portal](deploy-portal.md), mit [PowerShell](deploy-powershell.md), über eine [Vorlage](deploy-template.md) oder mithilfe von [Visual Studio](deploy-visual-studio.md) bereit.
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).
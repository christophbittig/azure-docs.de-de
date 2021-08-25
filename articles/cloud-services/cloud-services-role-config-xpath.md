---
title: XPath-Spickzettel für die Rollenkonfiguration in Cloud Services (klassisch) | Microsoft-Dokumentation
description: Die verschiedenen XPath-Einstellungen, die Sie in der Clouddienst-Rollenkonfiguration verwenden können, um Einstellungen als Umgebungsvariable verfügbar zu machen.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 56dcf798ccaf96b316d978fa5d8a70f4cd99ae6d
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823930"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Verfügbarmachen von Rollenkonfigurationseinstellungen als Umgebungsvariable mit XPath

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

In der Dienstdefinitionsdatei der Clouddienst-Worker- oder Webrolle können Sie Laufzeitkonfigurationswerte als Umgebungsvariablen verfügbar machen. Die folgenden XPath-Werte (die API-Werten entsprechen) werden unterstützt.

Diese XPath-Werte sind auch über die [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) -Bibliothek verfügbar. 

## <a name="app-running-in-emulator"></a>Im Emulator ausgeführte App
Gibt an, dass die Anwendung im Emulator ausgeführt wird

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Code |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Bereitstellungs-ID
Ruft die Bereitstellungs-ID für die Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Code |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Rollen-ID
Ruft die aktuelle Rollen-ID für die Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Code |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Domäne aktualisieren
Ruft die Updatedomäne der Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Code |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Fehlerdomäne
Ruft die Fehlerdomäne der Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Code |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Rollenname
Ruft den Rollennamen der Instanzen ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Code |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurationseinstellung
Ruft den Wert der angegebenen Konfigurationseinstellung ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Code |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Lokaler Speicherpfad
Ruft den lokalen Speicherpfad für die Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Code |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>Größe des lokalen Speichers
Ruft die Größe des lokalen Speichers für die Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Code |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Endpunktprotokoll
Ruft das Endpunktprotokoll für die Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Code |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>Endpunkt-IP
Ruft die IP-Adresse des angegebenen Endpunkts ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Code |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>Endpunktport
Ruft den Endpunktport für die Instanz ab

| type | Beispiel |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Code |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>Beispiel
Hier ist ein Beispiel für eine Workerrolle, die eine Startaufgabe mit der Umgebungsvariablen `TestIsEmulated` erstellt, festgelegt auf den [XPath-Wert @emulated](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die Datei [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Erstellen Sie ein [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) -Paket.

Aktivieren Sie [Remotedesktop](cloud-services-role-enable-remote-desktop-new-portal.md) für eine Rolle.





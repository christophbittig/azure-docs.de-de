---
title: Definieren einer Dienstkonfiguration in der Datei „StartupServices.xml“ für eine Service Fabric-Anwendung
description: Erfahren Sie, wie Sie die Datei „StartupServices.xml“ verwenden, um die Konfiguration der Serviceebene von der Datei „ApplicationManifest.xml“ zu trennen.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 69ec795bab910f8f2b030ab5758698d3fdbae824
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681377"
---
# <a name="introducing-startupservicesxml-in-service-fabric-application"></a>Einführung in die Datei „StartupServices.xml“ in der Service Fabric-Anwendung
Diese Funktion führt die Datei „StartupServices.xml“ in den Entwurf einer Service Fabric-Anwendung ein. Diese Datei hostet den Abschnitt „DefaultServices“ der Datei „AnwendungManifest.xml“. Bei dieser Implementierung werden die Parameter, die sich auf die DefaultServices und die Dienstdefinition beziehen, aus der bestehenden Datei „ApplicationManifest.xml“ in die neue Datei namens „StartupServices.xml“ verschoben. Diese Datei wird in jeder Funktionalität (Erstellen/Neu  Erstellen/F5/STRG+F5/Veröffentlichen) in Visual Studio verwendet.

Hinweis: Die Datei „StartupServices.xml“ ist nur für Visual Studio-Bereitstellungen vorgesehen, um sicherzustellen, dass mit Visual Studio bereitgestellte Pakete (mit „StartupServices.xml“) keine Konflikte mit per ARM bereitgestellten Diensten verursachen. „StartupServices.xml“ ist nicht als Teil des Anwendungspakets gepackt. Die Datei wird in der DevOps-Pipeline nicht unterstützt, und Kunden müssen einzelne Dienste in der Anwendung entweder über ARM oder über Cmdlets mit gewünschter Konfiguration bereitstellen.

## <a name="existing-service-fabric-application-design"></a>Ein vorhandener Service Fabric-Anwendungsentwurf
Für jede Service Fabric-Anwendung ist die Datei „ApplicationManifest.xml“ die Quelle aller dienstbezogenen Informationen für die Anwendung. Die Datei „ApplicationManifest.xml“ besteht aus allen Parametern, dem ServiceManifestImport und den DefaultServices. Die Konfigurationsparameter werden in den „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters genannt.

Wenn in einer Anwendung ein neuer Dienst hinzugefügt wird, werden für diese neuen Dienstparameter die Abschnitte ServiceManifestImport und DefaultServices der Datei „ApplicationManifest.xml“ hinzugefügt. Die Konfigurationsparameter werden in den „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters hinzugefügt.

Wenn der Benutzer in Visual Studio auf die „Erstellen/Neu Erstellen“-Funktion klickt, erfolgt die Anpassung der Bereiche ServiceManifestImport, Parameters und DefaultServices in der Datei „ApplicationManifest.xml“. Die Konfigurationsparameter werden auch in den „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters bearbeitet.

Wenn der Benutzer die Funktion „F5/STRG+F5/Veröffentlichen“ auslöst, werden die Anwendungen und Dienste basierend auf den Informationen in der Datei „ApplicationManifest.xml“ bereitgestellt oder veröffentlicht.  Die Konfigurationsparameter werden von allen „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters verwendet.

![Ein vorhandener Entwurf für eine Service Fabric-Anwendung][exisiting-design-diagram]

Ein Beispiel für die Datei „ApplicationManifest.xml“ 

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
    <Parameter Name="Web1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Web1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Web1Type" TargetReplicaSetSize="[Web1_TargetReplicaSetSize]" MinReplicaSetSize="[Web1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Web1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="new-service-fabric-application-design-with-startupservicesxml"></a>Ein neuer Service Fabric Anwendungsentwurf mit der Datei „StartupServices.xml“
Bei diesem Entwurf gibt es einen eindeutigen Unterschied zwischen den Servicelevel-Informationen (z. B. Dienstdefinition und Dienstparameter) und den Informationen auf der Anwendungsebene (ServiceManifestImport und ApplicationParameters). Die Datei „StartupServices.xml“ enthält alle Informationen auf der Dienstebene, während die Datei „ApplicationManifest.xml“ alle Informationen auf der Anwendungsebene enthält. Eine weitere eingeführte Änderung ist das Hinzufügen der Datei „Cloud.xml/Local1Node.xml/Local5Node.xml“ unter StartupServiceParameters, die nur für die Dienstparameter konfiguriert ist. Die vorhandenen „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters enthalten nur die Konfiguration von den Parametern auf der Anwendungsebene.

Wenn der Anwendung ein neuer Dienst hinzugefügt wird, werden die Parameter auf der Anwendungsebene und das ServiceManifestImport der Datei „ApplicationManifest.xml“ hinzugefügt. Die Konfiguration für die Anwendungsparameter wird in den „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters hinzugefügt. Die Dienstinformationen und Dienstparameter werden der Datei „StartupService.xml“ und die Konfiguration für die Dienstparameter der Datei „Cloud.xml/Local1Node.xml/Local5Node.xml“ unter StartupServiceParameters hinzugefügt.

Während des Erstellens/Neuerstellens des Projekts werden Änderungen am ServiceManifestImport und den Anwendungsparametern in der Datei „ApplicationManifest.xml“ vorgenommen. Die Konfiguration der Anwendungsparameter werden auch in den „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters bearbeitet. Die dienstbezogenen Informationen werden in der Datei „StartupService.xml“ und die Dienstparameter in der Datei „Cloud.xml/Local1Node.xml/Local5Node.xml“ unter StartupServiceParameters bearbeitet.

Wenn die Funktion „F5/STRG+F5/Veröffentlichen“ in Visual Studio ausgelöst wird, wird die Anwendung basierend auf den Informationen der Datei „ApplicationManifest.xml“ und den Anwendungsparametern aus einer der „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter ApplicationParameters bereitgestellt/veröffentlicht. Jeder Dienst wird einzeln mit den Dienstinformationen aus der Datei „StartupServices.xml“ und den Dienstparameterkonfigurationen aus einer der „Cloud.xml/Local1Node.xml/Local5Node.xml“-Dateien unter StartupServiceParameters gestartet.

![Ein neuer Entwurf für eine Service Fabric-Anwendung mit der Datei „StartupServices.xml“][new-design-diagram]

Diese Dienstparameter und Anwendungsparameter können vor der Veröffentlichung einer Anwendung bearbeitet werden (Rechtsklick -> Veröffentlichen), wie in der Abbildung dargestellt.

![Die Option „Publish“ in einem neuen Entwurf][publish-application]

Ein Beispiel für die Datei „ApplicationManifest.xml“ in einem neuen Entwurf
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
</ApplicationManifest>
```

Ein Beispiel für eine „StartupServices.xml“-Datei
```xml
<?xml version="1.0" encoding="utf-8"?>
<StartupServicesManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                         xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <Services>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </Services>
</StartupServicesManifest>
```

Die Funktion „startupServices.xml“ ist für alle neuen Projekte der SDK-Version 5.0.516.9590 und höher aktiviert. Für Akteur-Dienste ist das in Microsoft.ServiceFabric.Actors NuGet der Version 5.0.516 und höher aktiviert. Projekte, die mit einer älteren Version des SDK erstellt wurden, sind mit der neuesten SDK-Version vollständig rückwärtskompatibel. Die Migration alter Projekte in einen neuen Entwurf wird nicht unterstützt. Wenn der Benutzer eine Service Fabric-Anwendung ohne die Datei „StartupServices.xml“ in einer neueren Version des SDK erstellen möchte, sollte er den Link "Hilfe bei der Auswahl einer Projektvorlage" anklicken, wie in der Abbildung unten dargestellt.

![Erstellen der Option „Neue Anwendung“ in einem neuen Entwurf][create-new-project]


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Service Fabric Anwendungsmodell](service-fabric-application-model.md).
- Erfahren Sie mehr über [Service Fabric Anwendungs- und Dienstmanifeste](service-fabric-application-and-service-manifests.md).

<!--Image references-->
[exisiting-design-diagram]: ./media/service-fabric-startupservices/existing-design.png
[new-design-diagram]: ./media/service-fabric-startupservices/new-design.png
[publish-application]: ./media/service-fabric-startupservices/publish-application.png
[create-new-project]: ./media/service-fabric-startupservices/create-new-project.png


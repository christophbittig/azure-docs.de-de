---
title: Migrieren vom Java SDK zu Maven
description: Aktualisieren Sie ältere Java-Anwendungen, die das Service Fabric-Java-SDK verwendet haben, für den Abruf von Service Fabric-Java-Abhängigkeiten aus Maven. Nach Abschluss dieses Setups können Ihre älteren Java-Anwendungen erstellt werden.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 53f77e719d25ba4b11ad06c0f62f93a227d9becd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022757"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Aktualisieren Ihrer älteren Java Service Fabric-Anwendung für den Abruf von Java-Bibliotheken aus Maven
Service Fabric Java-Binärdateien wurden vom Service Fabric-Java-SDK auf Maven-Hosting umgestellt. Sie können die neuesten Service Fabric-Java-Abhängigkeiten mithilfe von **mavencentral** abrufen. Dieser Leitfaden unterstützt Sie bei der Aktualisierung vorhandener Java-Anwendungen, die für das Service Fabric-Java-SDK unter Verwendung der Yeoman-Vorlage oder Eclipse erstellt wurden, um mit dem Maven-basierten Build kompatibel zu sein.

## <a name="prerequisites"></a>Voraussetzungen

1. Deinstallieren Sie zuerst das vorhandene Java-SDK.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. Installieren Sie die neueste Version der Service Fabric-Befehlszeilenschnittstelle. (Eine entsprechende Anleitung finden Sie [hier](service-fabric-cli.md).)

3. Für die Erstellung und Verwendung von Service Fabric-Java-Anwendungen müssen JDK 1.8 und Gradle installiert sein. Ist dies nicht der Fall, können Sie Folgendes ausführen, um JDK 1.8 (openjdk-1.8-jdk) und Gradle zu installieren:

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. Aktualisieren Sie das Installations-/Deinstallationsskript Ihrer Anwendung für die Verwendung der neuen Service Fabric-Befehlszeilenschnittstelle. (Eine entsprechende Anleitung finden Sie [hier](service-fabric-application-lifecycle-sfctl.md).) Orientieren Sie sich dabei ggf. an unseren [Beispielen](https://github.com/Azure-Samples/service-fabric-java-getting-started) für Einsteiger.

> [!TIP]
> Nach der Deinstallation des Service Fabric-Java-SDKs funktioniert Yeoman nicht mehr. Richten Sie den Service Fabric-Yeoman-Java-Vorlagengenerator gemäß den [hier](service-fabric-create-your-first-linux-application-with-java.md) angegebenen Vorgaben ein.

## <a name="service-fabric-java-libraries-on-maven"></a>Service Fabric-Java-Bibliotheken in Maven

Service Fabric-Java-Bibliotheken wurden in Maven gehostet. Sie können die Abhängigkeiten in ``pom.xml`` oder ``build.gradle`` Ihrer Projekte hinzufügen, um Service Fabric-Java-Bibliotheken aus **mavenCentral** zu verwenden.

### <a name="actors"></a>Akteure

Unterstützung von Service Fabric Reliable Actors für Ihre Anwendung:

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Dienste

Unterstützung von zustandslosen Service Fabric-Diensten für Ihre Anwendung:

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Sonstige

#### <a name="transport"></a>Transport

Transportschichtunterstützung für die Service Fabric-Java-Anwendung: Diese Abhängigkeit muss Reliable Actors- oder Reliable Services-Anwendungen nur dann explizit hinzugefügt werden, wenn Sie in der Transportschicht programmieren.

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric-Unterstützung

Unterstützung auf Systemebene für Service Fabric, wobei die Kommunikation mit der nativen Service Fabric-Runtime erfolgt. Diese Abhängigkeit muss Reliable Actors- oder Reliable Services-Anwendungen nicht explizit hinzugefügt werden. Sie wird automatisch von Maven abgerufen, wenn Sie die anderen oben erwähnten Abhängigkeiten hinzufügen.

  ```xml
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="migrating-service-fabric-stateless-service"></a>Migrieren eines zustandslosen Service Fabric-Diensts

Um Ihren vorhandenen zustandslosen Service Fabric-Java-Dienst mit aus Maven abgerufenen Service Fabric-Abhängigkeiten erstellen zu können, müssen Sie die Datei ``build.gradle`` innerhalb des Diensts aktualisieren. Bislang sah der Code wie folgt aus:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

Die **aktualisierte Datei** ``build.gradle`` für den Abruf der Abhängigkeiten aus Maven sieht wie folgt aus:

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

Die Beispiele für Einsteiger vermitteln jeweils einen allgemeinen Eindruck davon, wie ein Buildskript für einen zustandslosen Service Fabric-Java-Dienst aussieht. Die Datei „build.gradle“ für das EchoServer-Beispiel finden Sie [hier](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle).

## <a name="migrating-service-fabric-actor-service"></a>Migrieren des Service Fabric-Akteurdiensts

Um Ihre vorhandene Service Fabric-Akteur-Java-Anwendung mit aus Maven abgerufenen Service Fabric-Abhängigkeiten erstellen zu können, müssen Sie die Datei ``build.gradle`` innerhalb des Schnittstellenpakets und im Dienstpaket aktualisieren. Wenn Sie über ein TestClient-Paket verfügen, müssen Sie auch dieses aktualisieren. Für den Akteur ``Myactor`` wären also Aktualisierungen an folgenden Stellen erforderlich:

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Aktualisieren des Buildskripts für das Schnittstellenprojekt

Bislang sah der Code wie folgt aus:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

Die **aktualisierte Datei** ``build.gradle`` für den Abruf der Abhängigkeiten aus Maven sieht wie folgt aus:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Aktualisieren des Buildskripts für das Akteurprojekt

Bislang sah der Code wie folgt aus:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

Die **aktualisierte Datei** ``build.gradle`` für den Abruf der Abhängigkeiten aus Maven sieht wie folgt aus:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Aktualisieren des Buildskripts für das Testclientprojekt

Die hier erforderlichen Änderungen sind vergleichbar mit den Änderungen aus dem vorherigen Abschnitt für das Akteurprojekt. Bislang sah das Gradle-Skript wie folgt aus:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
    destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

Die **aktualisierte Datei** ``build.gradle`` für den Abruf der Abhängigkeiten aus Maven sieht wie folgt aus:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe von Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe des Service Fabric-Plug-Ins für Eclipse](service-fabric-get-started-eclipse.md)
* [Interagieren mit Service Fabric-Clustern mithilfe der Service Fabric-Befehlszeilenschnittstelle](service-fabric-cli.md)

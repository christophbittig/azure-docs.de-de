---
title: Konvertieren in die Spring Boot-Bibliothek
titleSuffix: Azure App Configuration
description: Es wird beschrieben, wie Sie die Konvertierung von der vorherigen Version in die neue Spring Boot-Bibliothek von App Configuration durchführen.
ms.service: azure-app-configuration
author: mrm9084
ms.author: mametcal
ms.topic: how-to
ms.date: 07/08/2021
ms.openlocfilehash: 1bff486cb226de6bb9b6c8a0f065dbca134bd684
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553055"
---
# <a name="convert-to-new-app-configuration-spring-boot-library"></a>Konvertieren in die Spring Boot-Bibliothek von App Configuration

Eine neue Version der Spring Boot-Bibliothek von App Configuration ist verfügbar. Mit dieser Version werden neue Features, z. B. die Pushaktualisierung, und einige Breaking Changes eingeführt. Diese Änderungen sind nicht abwärtskompatibel mit Konfigurationssetups, für die die vorherige Version der Bibliothek verwendet wurde. In diesem Artikel werden die folgenden Themen behandelt:

* Gruppen- und Artefakt-IDs
* Spring-Profile
* Laden und erneutes Laden von Konfigurationen
* Laden von Featureflags

Für diese Themen enthält dieser Artikel Referenzinformationen zu den Änderungen und Aktionen, die für die Migration zur neuen Version der Bibliothek erforderlich sind.

## <a name="group-and-artifact-id-changed"></a>Geänderte Gruppen- und Artefakt-ID

Für alle Azure Spring Boot-Bibliotheken wurden die Gruppen- und Artefakt-IDs aktualisiert, damit sie dem neuen Format entsprechen. Die neuen Paketnamen lauten:

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
```

## <a name="use-of-spring-profiles"></a>Verwenden von Spring-Profilen

Im vorherigen Release wurden Spring-Profile im Rahmen der Konfiguration verwendet, um eine Übereinstimmung mit dem Format der Konfigurationsdateien zu erzielen. Beispiel:

```properties
/<application name>_dev/config.message
```

Dies wurde geändert, damit die Standardbezeichnungen in einer Abfrage den Spring-Profilen mit dem folgenden Format entsprechen und eine Bezeichnung verwendet wird, die zum Spring-Profil passt:

```properties
/application/config.message
```

 Für die Konvertierung in das neue Format können Sie die unten angegebenen Befehle mit Ihrem Speichernamen ausführen:

```azurecli
az appconfig kv export -n your-stores-name -d file --format properties --key /application_dev* --prefix /application_dev/ --path convert.properties --skip-features --yes
az appconfig kv import -n your-stores-name -s file --format properties --label dev --prefix /application/ --path convert.properties --skip-features --yes
```

Alternativ können Sie das Feature „Import/Export“ im Portal nutzen.

Nachdem Sie die Umstellung auf die neue Version vollständig abgeschlossen haben, können Sie die alten Schlüssel entfernen, indem Sie Folgendes ausführen:

```azurecli
az appconfig kv delete -n ConversionTest --key /application_dev/*
```

Mit diesem Befehl werden alle Schlüssel aufgelistet, die Sie löschen möchten, damit Sie sicherstellen können, dass keine falschen Schlüssel entfernt werden. Es ist auch möglich, Schlüssel im Portal zu löschen.

## <a name="which-configurations-are-loaded"></a>Auswählen der zu ladenden Konfigurationen

Der Standardfall für das Laden der Konfiguration gemäß `/applicaiton/*` hat sich nicht geändert. Die Änderung besteht darin, dass `/${spring.application.name}/*` nicht mehr automatisch zusätzlich verwendet wird, sofern dies nicht festgelegt wird. Stattdessen können Sie für die Verwendung von `/${spring.application.name}/*` die neue Selects-Option zum Auswählen der Konfiguration nutzen.

```properties
spring.cloud.azure.appconfiguration.stores[0].selects[0].key-filter=/${spring.application.name}/*
```

## <a name="configuration-reloading"></a>Erneutes Laden der Konfiguration

Die Überwachung aller Konfigurationsspeicher ist jetzt standardmäßig deaktiviert. Der Bibliothek wurde eine neue Konfiguration hinzugefügt, damit für Konfigurationsspeicher die Überwachung aktiviert werden kann. Darüber hinaus wurde „cache-expiration“ in „refresh-interval“ umbenannt und gilt jetzt pro Konfigurationsspeicher. Wenn die Überwachung eines Konfigurationsspeichers aktiviert wird, muss außerdem mindestens ein überwachter Schlüssel mit optionaler Bezeichnung konfiguriert werden.

```properties
spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled
spring.cloud.azure.appconfiguration.stores[0].monitoring.refresh-interval
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].key
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].label
```

Die Funktionsweise des Aktualisierungsintervalls hat sich nicht geändert. Die Konfiguration wurde lediglich umbenannt, um die Funktionalität zu verdeutlichen. Durch die Anforderung zur Nutzung eines überwachten Schlüssels wird sichergestellt, dass für die Bibliothek beim Ändern von Konfigurationen nicht versucht wird, die Konfigurationen vor der Durchführung aller Änderungen zu laden.

## <a name="feature-flag-loading"></a>Laden von Featureflags

Das Laden von Featureflags ist jetzt standardmäßig deaktiviert. Darüber hinaus verfügen Featureflags nun über einen Bezeichnungsfilter und ein Aktualisierungsintervall.

```properties
spring.cloud.azure.appconfiguration.stores[0].feature-flags.enable
spring.cloud.azure.appconfiguration.stores[0].feature-flags.label-filter
spring.cloud.azure.appconfiguration.stores[0].monitoring.feature-flag-refresh-interval
```

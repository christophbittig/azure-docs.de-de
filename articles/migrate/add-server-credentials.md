---
title: Bereitstellen von Serveranmeldeinformationen zum Ermitteln von Softwareinventar, Abhängigkeiten und Web-Apps sowie SQL Server-Instanzen und -Datenbanken
description: Informationen zum Bereitstellen von Serveranmeldeinformationen für den Appliancekonfigurations-Manager
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: e1d8e42eaa11aacfc2d05e2f22434b604d3af592
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354908"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-web-apps-and-sql-server-instances-and-databases"></a>Bereitstellen von Serveranmeldeinformationen zum Ermitteln von Softwareinventar, Abhängigkeiten und Web-Apps sowie SQL Server-Instanzen und -Datenbanken

In diesem Artikel erfahren Sie, wie Sie mehrere Serveranmeldeinformationen im Appliancekonfigurations-Manager hinzufügen, um das Softwareinventar (installierte Anwendungen ermitteln), Abhängigkeitsanalyse ohne Agent und Ermittlung von Web-Apps, SQL Server-Instanzen und -Datenbanken durchzuführen.

Die [Azure Migrate-Appliance](migrate-appliance.md) ist eine unkomplizierte Appliance, die vom Tool „Azure Migrate: Ermittlung und Bewertung“ verwendet wird, um lokale Server zu ermitteln, die in einer VMware-Umgebung ausgeführt werden, und Serverkonfigurations- und Leistungsmetadaten an Azure zu senden. Die Appliance kann auch verwendet werden, um das Softwareinventar, Abhängigkeitsanalyse ohne Agent und Ermittlung von Web-Apps, SQL Server-Instanzen und -Datenbanken durchzuführen.

Wenn Sie diese Features nutzen möchten, können Sie Serveranmeldeinformationen bereitstellen, indem Sie die folgenden Schritte ausführen. Die Appliance versucht, die Anmeldeinformationen automatisch den Servern zuzuordnen, um die Ermittlungsfeatures auszuführen.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Hinzufügen von Anmeldeinformationen für Server, die in der VMware-Umgebung ausgeführt werden

### <a name="types-of-server-credentials-supported"></a>Unterstützte Typen von Serveranmeldeinformationen

Sie können im Appliancekonfigurations-Manager mehrere Serveranmeldeinformationen hinzufügen, bei denen es sich um Anmeldeinformationen für die Domäne, nicht für die Domäne bestimmte Windows- oder Linux-Anmeldeinformationen oder Anmeldeinformationen für die SQL Server-Authentifizierung handeln kann.

Die unterstützten Typen von Serveranmeldeinformationen sind in der folgenden Tabelle aufgeführt:

Typ der Anmeldeinformationen | Beschreibung
--- | ---
**Domänenanmeldeinformationen** | Sie können **Domänenanmeldeinformationen** hinzufügen, indem Sie die Option in der Dropdownliste des modalen Dialogfelds **Anmeldeinformationen hinzufügen** auswählen. <br/><br/> Zum Angeben von Domänenanmeldeinformationen müssen Sie den **Domänennamen** im FQDN-Format angeben (z. B. prod.corp.contoso.com). <br/><br/> Außerdem müssen Sie einen Anzeigenamen für Anmeldeinformationen, Benutzername und Kennwort angeben. <br/><br/> Die hinzugefügten Domänenanmeldeinformationen werden automatisch anhand der Active Directory-Instanz der Domäne auf Echtheit überprüft. Dadurch wird verhindert, dass Konten gesperrt werden, wenn die Appliance versucht, die Domänenanmeldeinformationen den ermittelten Servern zuzuordnen. <br/><br/> Die Appliance versucht nicht, die Domänenanmeldeinformationen zuzuordnen, bei denen die Überprüfung negativ verlaufen ist. Um mit der Softwareinventur fortzufahren, benötigen Sie mindestens eine erfolgreich überprüfte Domänenanmeldeinformation oder mindestens eine Anmeldeinformation, die nicht für eine Domäne bestimmt ist.<br/><br/>Die den Windows-Servern automatisch zugeordneten Domänenanmeldeinformationen werden zum Durchführen des Softwareinventars verwendet und können auch genutzt werden, um Web-Apps, SQL Server-Instanzen und -Datenbanken zu ermitteln _(wenn Sie den Windows-Authentifizierungsmodus für Ihre SQL Server-Instanzen konfiguriert haben)_ .<br/> [Weitere Informationen](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) zu den Authentifizierungsmoditypen, die auf SQL Server-Servern unterstützt werden.
**Nicht für die Domäne bestimmte Anmeldeinformationen (Windows/Linux)** | Sie können **Windows (nicht Domäne)** oder **Linux (nicht Domäne)** hinzufügen, indem Sie die erforderliche Option in der Dropdownliste des modalen Dialogfelds **Anmeldeinformationen hinzufügen** auswählen. <br/><br/> Sie müssen einen Anzeigenamen für Anmeldeinformationen, Benutzername und Kennwort angeben.
**Anmeldeinformationen für die SQL Server-Authentifizierung** | Sie können Anmeldeinformationen für die **SQL Server-Authentifizierung** hinzufügen, indem Sie die Option in der Dropdownliste des modalen Dialogfelds **Anmeldeinformationen hinzufügen** auswählen. <br/><br/> Sie müssen einen Anzeigenamen für Anmeldeinformationen, Benutzername und Kennwort angeben. <br/><br/> Sie können diesen Typ von Anmeldeinformationen hinzufügen, um SQL Server-Instanzen und -Datenbanken zu ermitteln, die in Ihrer VMware-Umgebung ausgeführt werden, wenn Sie den SQL Server-Authentifizierungsmodus auf Ihren SQL Server-Instanzen konfiguriert haben.<br/> [Weitere Informationen](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) zu den Authentifizierungsmoditypen, die auf SQL Server-Instanzen unterstützt werden.<br/><br/> Sie müssen mindestens eine erfolgreich überprüfte Domänenanmeldeinformation oder mindestens eine (nicht für die Domäne bestimmte) Windows-Anmeldeinformation bereitstellen, damit die Appliance die Softwareinventur durchführen kann, um SQL-Installationen auf den Servern zu ermitteln, bevor die Anmeldeinformationen für die SQL Server-Authentifizierung verwendet werden, um die SQL Server-Instanzen und -Datenbanken zu ermitteln.

Überprüfen Sie die erforderlichen Berechtigungen für die Windows/Linux-Anmeldeinformationen zum Durchführen des Softwareinventars, der Abhängigkeitsanalyse ohne Agents und zum Ermitteln von Web-Apps, SQL Server-Instanzen und -Datenbanken.

### <a name="required-permissions"></a>Erforderliche Berechtigungen

In der folgenden Tabelle sind die erforderlichen Berechtigungen für die auf der Appliance bereitgestellten Serveranmeldeinformationen aufgeführt, damit die jeweiligen Funktionen ausgeführt werden können:

Funktion | Windows-Anmeldeinformationen | Linux-Anmeldeinformationen
---| ---| ---
**Softwareinventur** | Gastbenutzerkonto | Reguläres/normales Benutzerkonto (keine sudo-Zugriffsberechtigungen)
**Ermittlung von SQL Server-Instanzen und -Datenbanken** | Benutzerkonto, das Mitglied der SysAdmin-Server Rolle ist. | _Derzeit nicht unterstützt_
**Ermittlung von ASP.NET-Web-Apps** | Domänenkonto oder Nicht-Domänenkonto (lokal) mit Administratorberechtigungen | _Derzeit nicht unterstützt_
**Abhängigkeitsanalyse ohne Agent** | Domänenkonto oder Nicht-Domänenkonto (lokal) mit Administratorberechtigungen | Das Root-Benutzerkonto oder <br/> ein Konto mit den folgenden Berechtigungen in den Dateien „/bin/netstat“ und „/bin/ls“: CAP_DAC_READ_SEARCH und CAP_SYS_PTRACE.<br/><br/> Legen Sie diese Funktionen mithilfe der folgenden Befehle fest: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Empfohlene Vorgehensweisen zum Angeben von Anmeldeinformationen

- Sie sollten ein dediziertes Domänenbenutzerkonto mit den [erforderlichen Berechtigungen](add-server-credentials.md#required-permissions) erstellen, das für die Durchführung des Softwareinventars, der Abhängigkeitsanalyse ohne Agent und die Ermittlung von Web-Apps, SQL Server-Instanzen und -Datenbanken auf den gewünschten Serverinstanzen vorgesehen ist.
- Sie sollten mindestens eine erfolgreich überprüfte Domänenanmeldeinformation bereitstellen oder mindestens eine nicht für die Domäne bestimmte Anmeldeinformation, um die Softwareinventur zu initiieren.
- Um SQL Server-Instanzen und -Datenbanken zu ermitteln, können Sie Domänenanmeldeinformationen bereitstellen, wenn Sie den Windows-Authentifizierungsmodus auf Ihren SQL Server-Instanzen konfiguriert haben.
- Sie können auch Anmeldeinformationen für die SQL Server-Authentifizierung bereitstellen, wenn Sie den SQL Server-Authentifizierungsmodus auf Ihren SQL Server-Instanzen konfiguriert haben. Sie sollten jedoch mindestens eine erfolgreich überprüfte Anmeldeinformation für Domänen oder mindestens eine Windows-Anmeldeinformation (nicht für Domänen) bereitstellen, damit die Appliance zunächst die Softwareinventur fertigstellen kann.

## <a name="credentials-handling-on-appliance"></a>Behandlung von Anmeldeinformationen auf der Appliance

- Alle Anmeldeinformationen, die auf dem Appliancekonfigurations-Manager bereitgestellt werden, werden lokal auf dem Applianceserver gespeichert und nicht an Azure gesendet.
- Die auf dem Applianceserver gespeicherten Anmeldeinformationen werden mithilfe der Datenschutz-API (DPAPI) verschlüsselt.
- Nachdem Sie die Anmeldeinformationen hinzugefügt haben, versucht die Appliance, die Anmeldeinformationen automatisch zuzuordnen, um die Ermittlung auf den jeweiligen Servern auszuführen.
- Die Appliance verwendet die automatisch zugeordneten Anmeldeinformationen auf einem Server für alle nachfolgenden Ermittlungszyklen, bis die Anmeldeinformationen die erforderlichen Ermittlungsdaten abrufen können. Wenn die Anmeldeinformationen nicht mehr funktionieren, versucht die Appliance erneut, Zuordnungen aus der Liste der hinzugefügten Anmeldeinformationen durchzuführen und die laufende Ermittlung auf dem Server fortzusetzen.
- Die hinzugefügten Domänenanmeldeinformationen werden automatisch anhand der Active Directory-Instanz der Domäne auf Echtheit überprüft. Dadurch wird verhindert, dass Konten gesperrt werden, wenn die Appliance versucht, die Domänenanmeldeinformationen den ermittelten Servern zuzuordnen. Die Appliance versucht nicht, die Domänenanmeldeinformationen zuzuordnen, bei denen die Überprüfung negativ verlaufen ist.
- Wenn die Appliance keine Domänenanmeldeinformationen oder nicht für die Domäne bestimmte Anmeldeinformationen für einen Server zuordnen kann, wird der Status „Anmeldeinformationen nicht verfügbar" für den Server im Projekt angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Tutorials für die [Ermittlung von Servern, die in Ihrer VMware-Umgebung ausgeführt werden](tutorial-discover-vmware.md).
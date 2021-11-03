---
title: Verwenden erweiterter Gruppen in Azure HPC Cache
description: Konfigurieren der Verzeichnisdienste für den Clientzugriff auf Speicherziele in Azure HPC Cache
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: femila
ms.openlocfilehash: 21bf14ad46ec1625e4c8eb5efed1002da7ed159e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019911"
---
# <a name="configure-directory-services"></a>Konfigurieren von Verzeichnisdiensten

Mit den Einstellungen für **Verzeichnisdienste** kann Ihre Azure HPC Cache-Instanz eine externe Quelle verwenden, um Benutzer für den Zugriff auf den Back-End-Speicher zu authentifizieren.

Möglicherweise müssen Sie **Erweiterte Gruppen** aktivieren, wenn Ihr Workflow NFS-Speicherziele und Clients umfasst, die Mitglieder von mehr als 16 Gruppen sind.

Nachdem Sie auf die Schaltfläche zum Aktivieren erweiterter Gruppen geklickt haben, müssen Sie die Quelle auswählen, von der Azure HPC Cache die Anmeldeinformationen von Benutzern und Gruppen abruft.

* [Active Directory:](#configure-active-directory) Rufen Sie die Anmeldeinformationen von einem externen Active Directory-Server ab. Sie können für diese Aufgabe nicht Azure Active Directory verwenden.
* [Flatfile:](#configure-file-download) Laden Sie die Dateien `/etc/group` und `/etc/passwd` von einer Netzwerkadresse herunter.
* [LDAP:](#configure-ldap) Rufen Sie die Anmeldeinformationen aus einer LDAP-konformen (Lightweight Directory Access Protocol) Quelle ab.

> [!NOTE]
> Stellen Sie sicher, dass Ihr Cache aus seinem sicheren Subnetz auf die Quelle für die Gruppeninformationen zugreifen kann.<!-- + details/examples -->

Im Feld **Username downloaded** (Heruntergeladener Benutzername) wird der Status des letzten Downloads von Gruppeninformationen angezeigt.

![Screenshot der Einstellungsseite für Verzeichnisdienste im Portal mit ausgewählter Option „Ja“ für erweiterte Gruppen und geöffnetem Dropdownmenü „Downloadquelle“](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Konfigurieren von Active Directory

In diesem Abschnitt wird erläutert, wie Sie den Cache so einrichten, dass Benutzer- und Gruppenanmeldeinformationen von einem externen Active Directory-Server (AD) abgerufen werden.

Geben Sie unter **Active Directory-Details** die folgenden Werte an:

* **Primäres DNS:** Geben Sie die IP-Adresse eines Domänennamenservers an, der vom Cache zum Auflösen des AD-Domänennamens verwendet werden kann.

* **Sekundäres DNS** (optional): Geben Sie die Adresse eines Namenservers ein, der verwendet werden soll, wenn der primäre Server nicht verfügbar ist.

* **AD-DNS-Domänenname:** Geben Sie den vollqualifizierten Domänennamen des AD-Servers an, dem der Cache beitreten soll, um Anmeldeinformationen abzurufen.

* **Cacheservername (Computerkonto):** Legen Sie den Namen fest, der diesem HPC Cache beim Beitreten zur AD-Domäne zugewiesen wird. Geben Sie einen Namen an, mit dem dieser Cache einfach zu erkennen ist. Der Name darf bis zu 15 Zeichen lang sein und nur Klein- und Großbuchstaben, Ziffern und Bindestriche enthalten.

Geben Sie im Abschnitt **Anmeldeinformationen** den Benutzernamen und das Kennwort eines AD-Administrators an, den Azure HPC Cache für den Zugriff auf den AD-Server verwenden kann. Diese Informationen werden bei der Speicherung verschlüsselt und können nicht abgefragt werden.

Klicken Sie auf die Schaltfläche am oberen Rand der Seite, um die Einstellungen zu speichern.

![Screenshot des Abschnitts „Details herunterladen“ mit ausgefüllten Werten für Active Directory](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Konfigurieren des Dateidownloads

Diese Werte sind erforderlich, wenn Sie Dateien mit Ihren Benutzer- und Gruppeninformationen herunterladen möchten. Die Dateien müssen das Linux/Unix-Standardformat für `/etc/group` und `/etc/passwrd` aufweisen.

* **User file URI** (URI der Benutzerdatei): Geben Sie den vollständigen URI für die Datei `/etc/passwrd` ein.
* **Group file URI** (URI der Gruppendatei): Geben Sie den vollständigen URI für die Datei `/etc/group` ein.

![Screenshot des Abschnitts „Details herunterladen“ für einen Flatfiledownload](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Konfigurieren von LDAP

Geben Sie diese Werte ein, wenn Sie eine andere LDAP-Quelle als Active Directory verwenden möchten, um die Anmeldeinformationen von Benutzern und Gruppen abzurufen. Wenden Sie sich an Ihren LDAP-Administrator, wenn Sie Hilfe bei diesen Werten benötigen.

* **LDAP-Server:** Geben Sie den vollqualifizierten Domänennamen oder die IP-Adresse des zu verwendenden LDAP-Servers ein. <!-- only one, not up to 3 -->

* **LDAP base DN** (LDAP-Basis-DN): Geben Sie den Base Distinguished Name für die LDAP-Domäne im DN-Format an. Fragen Sie Ihren LDAP-Administrator, wenn Sie den Basis-DN nicht kennen.

Der Server und der Basis-DN sind die einzigen erforderlichen Einstellungen, damit LDAP funktioniert. Mit den zusätzlichen Optionen wird die Verbindung jedoch sicherer.

![Screenshot des LDAP-Konfigurationsbereichs auf der Einstellungsseite für Verzeichnisdienste](media/group-download-details-ldap.png)

Im Abschnitt **Sicherer Zugriff** können Sie die Verschlüsselung und Zertifikatüberprüfung für die LDAP-Verbindung aktivieren. Nachdem Sie auf **Ja** geklickt haben, um die Verschlüsselung zu aktivieren, stehen Ihnen folgende Optionen zur Verfügung:

* **Validate certificate** (Zertifikat überprüfen): Wenn diese Option festgelegt wird, wird das Zertifikat des LDAP-Servers anhand der Zertifizierungsstelle im URI-Feld unten überprüft.

* **CA certificate URI** (Zertifikat-URI der Zertifizierungsstelle): Geben Sie den Pfad zum autorisierenden Zertifikat an. Dies kann ein Link zu einem von einer Zertifizierungsstelle überprüften Zertifikat oder zu einem selbstsignierten Zertifikat sein. Dieses Feld ist erforderlich, um die Einstellung für extern überprüfte Zertifikate verwenden zu können.

* **Auto-download certificate** (Zertifikat automatisch herunterladen): Wählen Sie **Ja** aus, wenn Sie versuchen möchten, direkt nach der Übermittlung dieser Einstellungen ein Zertifikat herunterzuladen.

Füllen Sie den Abschnitt **Anmeldeinformationen** aus, wenn Sie statische Anmeldeinformationen für die LDAP-Sicherheit verwenden möchten. Diese Informationen werden bei der Speicherung verschlüsselt und können nicht abgefragt werden.

* **Bindungs-DN:** Geben Sie den für die Authentifizierung beim LDAP-Server zu verwendenden Distinguished Name der Bindung ein. (Verwenden Sie das DN-Format.)
* **Bindungskennwort:** Geben Sie das Kennwort zum Bindungs-DN an.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Clientzugriff finden Sie unter [Einbinden einer Azure HPC Cache-Instanz](hpc-cache-mount.md).
* Wenn Ihre Anmeldeinformationen nicht ordnungsgemäß heruntergeladen werden, wenden Sie sich an den Administrator für die Quelle Ihrer Anmeldeinformationen. Erstellen Sie bei Bedarf ein [Supportticket](hpc-cache-support-ticket.md).

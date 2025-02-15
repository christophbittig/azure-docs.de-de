---
title: 'Datenverschlüsselung mit einem vom Kunden verwalteten Schlüssel: Azure Database for MySQL'
description: Azure Database for MySQL-Datenverschlüsselung mit einem vom Kunden verwalteten Schlüssel ermöglicht Ihnen BYOK (Bring Your Own Key) für den Schutz von Daten im Ruhezustand. Sie bietet Organisationen auch eine Möglichkeit der Trennung von Aufgaben bei der Verwaltung von Schlüsseln und Daten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6d2fca3ed64711133f1701446ebea61c28a3dcab
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674312"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL-Datenverschlüsselung mit einem vom Kunden verwalteten Schlüssel

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Datenverschlüsselung mit vom Kunden verwalteten Schlüsseln für Azure Database for MySQL ermöglicht Ihnen BYOK (Bring Your Own Key) für den Schutz von Daten im Ruhezustand. Sie bietet Organisationen auch eine Möglichkeit der Trennung von Aufgaben bei der Verwaltung von Schlüsseln und Daten. Bei der vom Kunden verwalteten Verschlüsselung sind Sie für die Verwaltung des Lebenszyklus von Schlüsseln und der Schlüsselnutzungsberechtigungen sowie für die Überwachung von Vorgängen für Schlüssel verantwortlich, erhalten auf diese Weise aber auch die vollständige Kontrolle.

Datenverschlüsselung mit vom Kunden verwalteten Schlüsseln für Azure Database for MySQL wird auf Serverebene festgelegt. Bei einem bestimmten Server wird ein vom Kunden verwalteter Schlüssel verwendet, der als Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) bezeichnet wird, um den vom Dienst verwendeten Datenverschlüsselungsschlüssel (Data Encryption Key, DEK) zu verschlüsseln. Der KEK ist ein asymmetrischer Schlüssel, der in einer vom Kunden verwalteten [Azure Key Vault](../key-vault/general/security-features.md)-Instanz im Besitz des Kunden gespeichert wird. Der Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) und der Datenverschlüsselungsschlüssel (Data Encryption Key, DEK) werden weiter unten in diesem Artikel ausführlicher beschrieben.

Key Vault ist ein cloudbasiertes, externes Schlüsselverwaltungssystem. Es bietet hochverfügbaren und skalierbaren sicheren Speicher für RSA-Kryptografieschlüssel, der optional von FIPS 140-2 Level 2-geprüften Hardwaresicherheitsmodulen (HSM) geschützt wird. Dabei wird kein direkter Zugriff auf einen gespeicherten Schlüssel zugelassen, sondern Verschlüsselung und Entschlüsselung werden für die autorisierten Entitäten bereitgestellt. Key Vault kann den Schlüssel generieren, importieren oder [von einem lokalen HSM-Gerät](../key-vault/keys/hsm-protected-keys.md) übertragen lassen.

> [!NOTE]
> Dieses Feature wird nur für den Speichertyp „Universell V2“ (Unterstützung von bis zu 16 TB) unterstützt, der in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ verfügbar ist. Weitere Informationen finden Sie unter [Speicherkonzepte](concepts-pricing-tiers.md#storage). Weitere Einschränkungen finden Sie im Abschnitt [Einschränkungen](concepts-data-encryption-mysql.md#limitations).

## <a name="benefits"></a>Vorteile

Die Datenverschlüsselung mit vom Kunden verwalteten Schlüsseln für Azure Database for MySQL bietet die folgenden Vorteile:

* Datenzugriff wird von Ihnen vollständig durch die Möglichkeit gesteuert, den Schlüssel zu entfernen und so die Datenbank nicht zugänglich zu machen. 
* Vollständige Kontrolle über den Schlüssellebenszyklus, einschließlich der Rotation des Schlüssels zum Einhalten von Unternehmensrichtlinien
* Zentrale Verwaltung und Organisation von Schlüsseln in Azure Key Vault
* Möglichkeit zur Implementierung der Trennung von Aufgaben zwischen Sicherheitsbeauftragten, DBAs und Systemadministratoren


## <a name="terminology-and-description"></a>Terminologie und Beschreibung

**Datenverschlüsselungsschlüssel (Data Encryption Key, DEK)** : Ein symmetrischer AES256-Schlüssel zum Verschlüsseln einer Partition oder eines Datenblocks. Das Verschlüsseln jedes Datenblocks mit einem anderen Schlüssel erschwert Kryptoanalyseangriffe. Der Ressourcenanbieter oder die Anwendungsinstanz, die einen spezifischen Block ver- oder entschlüsselt, muss Zugriff auf die DEKs gewähren. Wenn Sie einen DEK durch einen neuen Schlüssel ersetzen, müssen nur die Daten im verknüpften Block erneut mit dem neuen Schlüssel verschlüsselt werden.

**Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK)** : Ein Verschlüsselungsschlüssel, der zum Verschlüsseln der DEKs verwendet wird. Mit einem KEK, der Key Vault nie verlässt, können die DEKs selbst verschlüsselt und gesteuert werden. Die Entität, die auf den KEK zugreifen kann, kann sich von der Entität unterscheiden, die den DEK erfordert. Da der KEK erforderlich ist, um DEKs zu entschlüsseln, ist der KEK ein Punkt, an dem DEKs gelöscht werden können, indem der KEK gelöscht wird.

Die mit den KEKs verschlüsselten DEKs werden separat gespeichert. Nur eine Entität mit Zugriff auf den KEK kann diese DEKs entschlüsseln. Weitere Informationen finden Sie unter [Sicherheit bei der Verschlüsselung ruhender Daten](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Datenverschlüsselung mit einem kundenseitig verwalteten Schlüssel

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Abbildung, die eine Übersicht über BYOK (Bring Your Own Key) zeigt":::

Damit ein MySQL-Server vom Kunden verwaltete Schlüssel, die in Key Vault gespeichert sind, für die Verschlüsselung des DEK verwenden kann, erteilt ein Key Vault-Administrator dem Server die folgenden Zugriffsrechte:

* **get**: Zum Abrufen des öffentlichen Teils und der Eigenschaften des Schlüssels in Key Vault.
* **wrapKey**: Zum Verschlüsseln des DEK erforderlich. Der verschlüsselte DEK wird in der Azure Database for MySQL gespeichert.
* **unwrapKey**: Zum Entschlüsseln des DEK erforderlich. Azure Database for MySQL benötigt den entschlüsselten DEK zum Verschlüsseln/Entschlüsseln der Daten.

Der Key Vault-Administrator kann auch die [Protokollierung von Key Vault-Überwachungsereignissen aktivieren](../azure-monitor/insights/key-vault-insights-overview.md), damit sie später überprüft werden können.

Wenn der Server für die Verwendung des in Key Vault gespeicherten vom Kunden verwalteten Schlüssels konfiguriert ist, sendet der Server den DEK für Verschlüsselungen an Key Vault. Key Vault gibt den verschlüsselten DEK zurück, der in der Benutzerdatenbank gespeichert wird. Entsprechend sendet der Server bei Bedarf den geschützten DEK zur Entschlüsselung an Key Vault. Prüfer können Azure Monitor verwenden, um die Überwachungsereignisprotokoll von Key Vault zu überprüfen, sofern die Protokollierung aktiviert wurde.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Anforderungen für die Konfiguration der Datenverschlüsselung für Azure Database for MySQL

Nachfolgend werden die Anforderungen für die Konfiguration von Key Vault aufgeführt:

* Key Vault und Azure Database for MySQL müssen demselben Azure Active Directory-Mandanten (Azure AD) angehören. Mandantenübergreifende Interaktionen zwischen Key Vault und dem Server werden nicht unterstützt. Wenn Sie später Key Vault-Ressourcen verschieben möchten, müssen Sie die Datenverschlüsselung neu konfigurieren.
* Aktivieren Sie das Feature [Vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) für die Key Vault-Instanz mit einem Aufbewahrungszeitraum von **90 Tagen**, um Schutz vor Datenverlust zu bieten, falls ein Schlüssel (oder Key Vault) versehentlich gelöscht wird. Vorläufig gelöschte Ressourcen werden standardmäßig 90 Tage lang aufbewahrt, es sei denn, der Aufbewahrungszeitraum wird explizit auf <=90 Tage festgelegt. Den Aktionen zum Wiederherstellen und endgültigen Löschen sind über Key Vault-Zugriffsrichtlinien eigene Berechtigungen zugewiesen. Die Funktion für vorläufiges Löschen ist standardmäßig deaktiviert. Sie können sie jedoch über PowerShell oder die Azure CLI aktivieren (beachten Sie, dass Sie sie nicht über das Azure-Portal aktivieren können).
* Aktivieren Sie das Feature [Bereinigungsschutz](../key-vault/general/soft-delete-overview.md#purge-protection) für den Schlüsseltresor mit einem Aufbewahrungszeitraum von **90 Tagen**. Der Schutz vor dem endgültigen Löschen kann nur aktiviert werden, wenn das vorläufige Löschen aktiviert ist. Er kann über die Azure-Befehlszeilenschnittstelle oder PowerShell aktiviert werden. Bei aktiviertem Bereinigungsschutz kann ein Tresor oder ein Objekt im gelöschten Zustand erst nach Ablauf der Aufbewahrungsdauer endgültig gelöscht werden. Vorläufig gelöschte Tresore und Objekte können unter Einhaltung der Aufbewahrungsrichtlinie weiterhin wiederhergestellt werden. 
* Gewähren Sie Azure Database for MySQL mit den Berechtigungen get, wrapKey, unwrapKey unter Verwendung der eindeutigen verwalteten Identität Zugriff auf Key Vault. Im Azure-Portal wird die eindeutige Identität „Service“ automatisch erstellt, wenn Datenverschlüsselung für MySQL aktiviert wird. Ausführliche schrittweise Anleitungen für die Verwendung des Azure-Portals finden Sie unter [Konfigurieren der Datenverschlüsselung für MySQL](howto-data-encryption-portal.md).

Nachfolgend werden die Anforderungen für die Konfiguration des vom Kunden verwalteten Schlüssels aufgeführt:

* Der vom kundenseitig verwaltete Schlüssel, der zum Verschlüsseln des DEK verwendet werden soll, muss ein asymmetrischer RSA 2048-Schlüssel sein.
* Das Schlüsselaktivierungsdatum (sofern festgelegt) muss ein Datum und eine Uhrzeit in der Vergangenheit sein. Das Ablaufdatum ist nicht festgelegt.
* Der Schlüssel muss sich im Zustand *Aktiviert* befinden.
* Für den Schlüssel muss das Feature [Vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) mit einem Aufbewahrungszeitraum von **90 Tagen** festgelegt sein. Damit wird implizit die folgende erforderliche Wiederherstellungsstufe für das Schlüsselattribut festgelegt: „Recoverable“. Wenn eine Aufbewahrung von weniger als 90 Tagen festgelegt wird, ist die Wiederherstellungsstufe „CustomizedRecoverable“ angegeben. Diese Stufe erfüllt die Anforderung nicht, stellen Sie deshalb sicher, dass der Aufbewahrungszeitraum auf **90 Tage** festgelegt ist.
* Für den Schlüssel muss der [Bereinigungsschutz aktiviert](../key-vault/general/soft-delete-overview.md#purge-protection) sein.
* Wenn Sie [einen vorhandenen Schlüssel in Key Vault importieren](/rest/api/keyvault/ImportKey/ImportKey), müssen Sie ihn in einem der unterstützten Dateiformate (`.pfx`, `.byok`, `.backup`) bereitstellen.

## <a name="recommendations"></a>Empfehlungen

Wenn Sie Datenverschlüsselung durch einen vom Kunden verwalteten Schlüssel verwenden, finden Sie hier Empfehlungen zum Konfigurieren von Key Vault:

* Legen Sie eine Ressourcensperre für Key Vault fest, um zu steuern, wer diese wichtige Ressource löschen kann, und um ein versehentliches oder nicht autorisiertes Löschen zu verhindern.
* Aktivieren Sie die Überwachung und Berichterstellung für alle Verschlüsselungsschlüssel. Key Vault stellt Protokolle bereit, die sich problemlos in andere SIEM-Tools (Security Information & Event Management) einfügen lassen. Log Analytics in Azure Monitor ist ein Beispiel für einen Dienst, der bereits integriert ist.
* Stellen Sie sicher, dass sich Key Vault und Azure Database for MySQL in derselben Region befinden, um einen schnelleren Zugriff für wrap- und unwrap-Vorgänge bei DEKs sicherzustellen.
* Schränken Sie Azure Key Vault auf den **privaten Endpunkt und die ausgewählten Netzwerke** ein, und erlauben Sie nur *vertrauenswürdigen Microsoft-Diensten*, die Ressourcen zu schützen.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="Vertrauenswürdiger Dienst mit AKV":::

Empfehlungen für die Konfiguration kundenseitig verwalteter Schlüssel:

* Bewahren Sie eine Kopie des kundenseitig verwalteten Schlüssels an einem sicheren Ort auf, oder hinterlegen Sie ihn bei einem entsprechenden Dienst.

* Wenn Key Vault den Schlüssel generiert, erstellen Sie eine Schlüsselsicherung, bevor Sie den Schlüssel erstmals verwenden. Sie können nur die Sicherung in Key Vault wiederherstellen. Weitere Informationen zum Sicherungsbefehl finden Sie unter [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Kein Zugriff auf den kundenseitig verwalteten Schlüssel

Wenn Sie Datenverschlüsselung mit einem vom Kunden verwalteten Schlüssel in Azure Key Vault konfigurieren, wird fortlaufender Zugriff auf diesen Schlüssel benötigt, damit der Server online bleiben kann. Wenn der Server in Key Vault den Zugriff auf den vom Kunden verwalteten Schlüssel verliert, beginnt der Server innerhalb von 10 Minuten damit, alle Verbindungen zu verweigern. Der Server gibt eine entsprechende Fehlermeldung aus und ändert den Serverstatus in *Zugriff nicht möglich*. Der Server kann diesen Zustand u. a. aus den folgenden Gründen erreichen:

* Wenn Sie einen Server für die Point-in-Time-Wiederherstellung für Ihre Azure Database for MySQL-Instanz erstellen, auf dem die Datenverschlüsselung aktiviert ist, hat der neu erstellte Server den Status *Kein Zugriff*. Sie können den Fehler über das [Azure-Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) oder die [Azure CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) korrigieren.
* Wenn Sie ein Lesereplikat für Ihre Azure Database for MySQL-Instanz erstellen, für das die Datenverschlüsselung aktiviert ist, hat der neu erstellte Replikatserver den Status *Kein Zugriff*. Sie können den Fehler über das [Azure-Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) oder die [Azure CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) korrigieren.
* Wenn Sie den Schlüsseltresor löschen, kann die Azure Database for MySQL-Instanz nicht auf den Schlüssel zugreifen und wechselt daher in den Zustand *Kein Zugriff*. Stellen Sie den [Schlüsseltresor](../key-vault/general/key-vault-recovery.md) wieder her, und überprüfen Sie die Datenverschlüsselung erneut, damit der Server wieder den Zustand *Verfügbar* erreicht.
* Wenn Sie den Schlüssel aus dem Schlüsseltresor löschen, kann die Azure Database for MySQL-Instanz nicht auf den Schlüssel zugreifen und wechselt daher in den Zustand *Kein Zugriff*. Stellen Sie den [Schlüssel](../key-vault/general/key-vault-recovery.md) wieder her, und überprüfen Sie die Datenverschlüsselung erneut, damit der Server wieder den Zustand *Verfügbar* erreicht.
* Wenn der in Azure Key Vault gespeicherte Schlüssel abläuft, wird er damit ungültig. Die Azure Database for MySQL-Instanz wechselt dann in den Zustand *Kein Zugriff*. Verlängern Sie das Ablaufdatum des Schlüssels mithilfe der [CLI](/cli/azure/keyvault/key#az_keyvault_key_set-attributes), und überprüfen Sie dann die Datenverschlüsselung erneut, damit der Server wieder den Zustand *Verfügbar* erreicht.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Versehentliche Sperrung des Zugriffs auf den Schlüssel durch Key Vault

Es kann vorkommen, dass ein Benutzer mit ausreichenden Zugriffsrechten für Key Vault den Serverzugriff auf den Schlüssel versehentlich durch eine der folgende Aktionen deaktiviert:

* Widerrufen der Berechtigungen `get`, `wrapKey` und `unwrapKey` des Schlüsseltresors vom Server.
* Löschen des Schlüssels.
* Löschen des Schlüsseltresors.
* Ändern der Firewallregeln des Schlüsseltresors.
* Löschen der verwalteten Identität des Servers in Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Überwachen des kundenseitig verwalteten Schlüssels in Key Vault

Konfigurieren Sie die folgenden Azure-Features, um den Datenbankzustand zu überwachen und Warnungen bei Verlust des Zugriffs auf die TDE-Schutzvorrichtung (Transparent Data Encryption) zu aktivieren:

* [Azure Resource Health](../service-health/resource-health-overview.md): Eine Datenbank, auf die nicht zugegriffen werden kann und die den Zugriff auf den Kundenschlüssel verloren hat, wird als „Zugriff nicht möglich“ angezeigt, nachdem die erste Verbindung mit der Datenbank verweigert wurde.
* [Aktivitätsprotokoll:](../service-health/alerts-activity-log-service-notifications-portal.md) Ist der Zugriff auf den Kundenschlüssel im vom Kunden verwalteten Key Vault nicht möglich, werden dem Aktivitätsprotokoll entsprechende Einträge hinzugefügt. Sie können den Zugriff so bald wie möglich wiederherstellen, wenn Sie Warnungen für diese Ereignisse erstellen.

* [Aktionsgruppen](../azure-monitor/alerts/action-groups.md): Definieren Sie diese Gruppen, um Benachrichtigungen und Warnungen Ihren Einstellungen entsprechend gesendet werden.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Wiederherstellen und Replizieren mit einem vom Kunden verwalteten Schlüssel in Key Vault

Nachdem Azure Database for MySQL mit dem vom Kunden verwalteten Schlüssel, der in Key Vault gespeichert ist, verschlüsselt wurde, wird jede neu erstellte Kopie des Servers ebenfalls verschlüsselt. Sie können diese neue Kopie durch einen lokalen oder Geowiederherstellungsvorgang oder durch Lesereplikate erstellen. Die Kopie kann jedoch so geändert werden, dass für die Verschlüsselung ein neuer kundenseitig verwalteter Schlüssel angegeben wird. Wenn der vom Kunden verwaltete Schlüssel geändert wird, beginnen alte Sicherungen des Servers damit, den neuesten Schlüssel zu verwenden.

Um Probleme bei der Einrichtung von kundenseitig verwalteter Datenverschlüsselung während der Wiederherstellung oder der Lesereplikaterstellung zu vermeiden, sollten Sie unbedingt die folgenden Schritte auf dem Quellserver und den wiederhergestellten Servern bzw. Replikatservern ausführen:

* Initiieren Sie die Wiederherstellung oder die Lesereplikaterstellung in der Azure Database for MySQL-Instanz.
* Der neu erstellte Server (wiederhergestellt oder Replikat) verbleibt im Zustand „Zugriff nicht möglich“, da seiner eindeutigen Identität noch keine Berechtigungen für Azure Key Vault erteilt wurden.
* Überprüfen Sie auf dem wiederhergestellten Server oder dem Replikatserver erneut den kundenseitig verwalteten Schlüssel in den Einstellungen zur Datenverschlüsselung, um sicherzustellen, dass dem neu erstellten Server die Berechtigungen „wrap“ und „unwrap“ für den in Key Vault gespeicherten Schlüssel erteilt wurden.

## <a name="limitations"></a>Einschränkungen

Bei Azure Database for MySQL gelten für die Unterstützung der Verschlüsselung ruhender Daten unter Verwendung eines kundenseitig verwalteten Schlüssels (CMK) nur wenige Einschränkungen:

* Die Unterstützung dieser Funktionalität ist auf die Tarife **Universell** und **Arbeitsspeicheroptimiert** beschränkt.
* Dieses Feature wird nur in Regionen und für Server unterstützt, die den Speichertyp „Universell V2“ (bis zu 16 TB) unterstützen. Eine Liste der Azure-Regionen, die Speicher mit bis zu 16 TB unterstützen, finden Sie in [diesem](concepts-pricing-tiers.md#storage) Abschnitt zum Speicher in der Dokumentation.

    > [!NOTE]
    > - Für alle neuen MySQL-Server, die in **Azure-Regionen** mit Unterstützung des Speichertyps „Universell V2“ erstellt werden, ist die Unterstützung der Verschlüsselung mit kundenseitig verwalteten Schlüsseln [verfügbar](concepts-pricing-tiers.md#storage). Server für die Zeitpunktwiederherstellung oder Lesereplikate kommen nicht in Frage, obwohl sie theoretisch „neu“ sind.
    > - Um zu überprüfen, ob Ihr bereitgestellter Server den Speichertyp „Universell V2“ unterstützt, können Sie im Portal zum Blatt „Tarif“ navigieren und die maximale Speichergröße anzeigen, die Ihr bereitgestellter Server unterstützt. Wenn Sie den Schieberegler auf bis zu 4 TB festlegen können, wird der Speichertyp „Universell V1“ verwendet, und Ihr Server unterstützt keine Verschlüsselung mit kundenseitig verwalteten Schlüsseln. Die Daten sind jedoch jederzeit mit dienstseitig verwalteten Schlüsseln verschlüsselt. Wenn Sie weitere Fragen haben, wenden Sie sich an AskAzureDBforMySQL@service.microsoft.com.

* Verschlüsselung wird nur mit RSA 2048-Kryptografieschlüsseln unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie Datenverschlüsselung mit einem vom Kunden verwalteten Schlüssel für Azure Database for MySQL über das [Azure-Portal](howto-data-encryption-portal.md) und die [Azure-Befehlszeilenschnittstelle](howto-data-encryption-cli.md) einrichten.
* Weitere Informationen zur Speichertypunterstützung für [Azure Database for MySQL – Single Server](concepts-pricing-tiers.md#storage)

---
title: Zertifikatrotation für Azure Database for MariaDB
description: Hier erfahren Sie mehr über die bevorstehenden Änderungen an Stammzertifikaten im Zusammenhang mit Azure Database for MariaDB.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 8a8a7fec5b5c5f62fb44f6c0ccf6859b1141295b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646509"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Grundlegendes zu den Änderungen im Zusammenhang mit der Stammzertifizierungsstelle für Azure Database for MariaDB

Die Änderung des Stammzertifikats am **15. Februar 2021 (15.02.2021)** wurde für Azure Database for MariaDB im Rahmen der standardmäßigen Wartung und bewährten Sicherheitsmethoden erfolgreich abgeschlossen. In diesem Artikel erhalten Sie ausführlichere Informationen zu den Änderungen, den betroffenen Ressourcen sowie den Schritten, die erforderlich sind, um sicherzustellen, dass die Konnektivität Ihrer Anwendung mit dem Datenbankserver bestehen bleibt.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Slave*, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.
>

## <a name="why-root-certificate-update-is-required"></a>Warum muss das Stammzertifikat aktualisiert werden?

Derzeit können Benutzer von Azure Database for MariaDB nur das vordefinierte Zertifikat zum Herstellen einer Verbindung mit einem Azure Database for MariaDB-Server verwenden. Dieses Zertifikat finden Sie [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Im [ZA-/Browserforum](https://cabforum.org/) wurden jedoch kürzlich Berichte veröffentlicht, in denen mehrere von Zertifizierungsstellenanbietern ausgestellte Zertifikate aufgeführt sind, die nicht konform sind.

Gemäß den Konformitätsanforderungen der Branche haben Zertifizierungsstellenanbieter damit begonnen, Zertifizierungsstellenzertifikate für nicht konforme Zertifizierungsstellen zu sperren. Daher müssen Server Zertifikate verwenden, die von konformen Zertifizierungsstellen ausgestellt und durch Zertifizierungsstellenzertifikate von diesen konformen Zertifizierungsstellen signiert wurden. Da Azure Database for MariaDB eines dieser nicht konformen Zertifikate verwendete, musste das Zertifikat zur konformen Version rotiert werden, um die potenzielle Bedrohung für Ihre MySQL-Server zu minimieren.

Der Rollout des neuen Zertifikats hat am 15. Februar 2021 (15.02.2021) begonnen, und es ist ab diesem Datum gültig. 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Welche Änderung wurde am 15. Februar 2021 (15.02.2021) durchgeführt?

Am 15. Februar 2021 wurde das [BaltimoreCyberTrustRoot-Stammzertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) durch eine **konforme Version** desselben [BaltimoreCyberTrustRoot-Stammzertifikats](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) ersetzt, um sicherzustellen, dass Bestandskunden keine Änderungen vornehmen müssen und keine Auswirkungen auf ihre Verbindungen mit dem Server bestehen. Während dieser Änderung wurde das [BaltimoreCyberTrustRoot-Stammzertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nicht** durch [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ersetzt. Diese Änderung wird zurückgestellt, um den Kunden mehr Zeit für die Umstellung zu geben.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Muss ich Änderungen an meinem Client vornehmen, um die Konnektivität aufrechtzuerhalten?

Es ist keine Änderung auf Clientseite erforderlich. Wenn Sie die frühere Empfehlung unten befolgt haben, können Sie weiterhin eine Verbindung herstellen, solange das **BaltimoreCyberTrustRoot-Zertifikat nicht aus dem kombinierten Zertifizierungsstellenzertifikat entfernt wird**. **Es wird empfohlen, das BaltimoreCyberTrustRoot-Zertifikat bis auf Weiteres nicht aus dem kombinierten Zertifizierungsstellenzertifikat zu entfernen, um die Konnektivität aufrechtzuerhalten.**

### <a name="previous-recommendation"></a>Frühere Empfehlung

- Laden Sie die Stammzertifizierungsstellen **BaltimoreCyberTrustRoot** & **DigiCertGlobalRootG2** über die folgenden Links herunter:

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- Generieren Sie einen kombinierten Zertifizierungsstellen-Zertifikatspeicher, in dem sowohl **BaltimoreCyberTrustRoot** als auch **DigiCertGlobalRootG2** enthalten sind.

  - Führen Sie für Java-Benutzer (MariaDB-Connector/J) Folgendes aus:

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    Ersetzen Sie dann die ursprüngliche Keystore-Datei durch die neu generierte Datei:

    - `System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");`
    - `System.setProperty("javax.net.ssl.trustStorePassword","password");`

  - Stellen Sie bei .NET-Benutzern (MariaDB-Connector/NET, MariaDBConnector) sicher, dass **BaltimoreCyberTrustRoot** und **DigiCertGlobalRootG2** im Windows-Zertifikatspeicher unter vertrauenswürdigen Stammzertifizierungsstellen enthalten sind. Ist eines der Zertifikate nicht vorhanden, importieren Sie das fehlende Zertifikat.

    [![Azure Database for MariaDB: .NET-Zertifikat](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - Stellen Sie bei .NET-Benutzern unter Linux, die SSL_CERT_DIR verwenden, sicher, dass **BaltimoreCyberTrustRoot** und **DigiCertGlobalRootG2** in dem durch SSL_CERT_DIR angegebenen Verzeichnis enthalten sind. Ist eines der Zertifikate nicht vorhanden, erstellen Sie die fehlende Zertifikatsdatei.

  - Bei anderen Benutzern (MariaDB-Client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) können Sie zwei ZS-Zertifikatsdateien im folgenden Format zusammenführen:

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- Ersetzen Sie die ursprüngliche PEM-Datei der Stammzertifizierungsstelle durch die kombinierte Datei der Stammzertifizierungsstelle, und starten Sie die Anwendung bzw. den Client neu.
- Nachdem das neue Zertifikat auf der Serverseite bereitgestellt wurde, können Sie die PEM-Datei der Zertifizierungsstelle in Zukunft in „DigiCertGlobalRootG2.crt.pem“ ändern.

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Warum wurde das BaltimoreCyberTrustRoo-Zertifikat während dieser Änderung am 15. Februar 2021 nicht durch DigiCertGlobalRootG2 ersetzt?

Wir haben die Bereitschaft der Kunden für diese Änderung bewertet und erkannt, dass viele Kunden noch zusätzliche Vorlaufzeit zur Abwicklung dieser Änderung benötigen. Um den Kunden eine längere Vorlaufzeit zur Vorbereitung zu bieten, haben wir uns entschieden, die Zertifikatänderung in DigiCertGlobalRootG2 um mindestens ein Jahr zu verschieben, um Kunden und Endbenutzern genügend Vorlaufzeit zu geben. 

Unsere Empfehlungen für Benutzer lauten: Führen Sie die oben genannten Schritte zum Erstellen eines kombinierten Zertifikats und Herstellen einer Verbindung mit dem Server aus, aber entfernen Sie das BaltimoreCyberTrustRoot-Zertifikat erst dann, wenn wir eine entsprechende Mitteilung zum Entfernen senden. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Was geschieht, wenn das BaltimoreCyberTrustRoot-Zertifikat entfernt wurde?

Beim Herstellen einer Verbindung mit dem Azure Database for MariaDB-Server treten Konnektivitätsfehler auf. Sie müssen eine erneute [Konfiguration von SSL](howto-configure-ssl.md) mit dem [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)-Zertifikat durchführen, um Konnektivität zu gewährleisten.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Muss ich die Stammzertifizierungsstelle auch aktualisieren, wenn ich SSL/TLS nicht verwende?

Wenn Sie SSL/TLS nicht verwenden, sind keine Schritte erforderlich.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Muss ich bei Verwendung von SSL/TLS meinen Datenbankserver neu starten, um die Stammzertifizierungsstelle zu aktualisieren?

Nein, der Datenbankserver muss nicht neu gestartet werden, damit das neue Zertifikat verwendet werden kann. Die Zertifikataktualisierung ist eine clientseitige Änderung, und die eingehenden Clientverbindungen müssen das neue Zertifikat verwenden, um sicherzustellen, dass sie eine Verbindung mit dem Datenbankserver herstellen können.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Wie kann ich feststellen, ob ich SSL/TLS mit Stammzertifikatüberprüfung verwende?

Durch Überprüfen der Verbindungszeichenfolge können Sie ermitteln, ob Ihre Verbindungen das Stammzertifikat überprüfen.

- Wenn Ihre Verbindungszeichenfolge `sslmode=verify-ca` oder `sslmode=verify-identity` enthält, müssen Sie das Zertifikat aktualisieren.
- Wenn Ihre Verbindungszeichenfolge `sslmode=disable`, `sslmode=allow`, `sslmode=prefer` oder `sslmode=require` enthält, müssen Sie keine Zertifikate aktualisieren.
- Wenn in Ihrer Verbindungszeichenfolge „sslmode“ nicht angegeben ist, müssen Sie keine Zertifikat aktualisieren.

Wenn Sie einen Client verwenden, der die Verbindungszeichenfolge abstrahiert, lesen Sie die Dokumentation des Clients, um zu ermitteln, ob Zertifikate überprüft werden.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. Welche Auswirkungen hat es, wenn App Service mit Azure Database for MariaDB verwendet wird?

Für Azure App Service-Instanzen, die eine Verbindung mit Azure Database for MariaDB herstellen, gibt es zwei mögliche Szenarien, abhängig davon, wie Sie SSL mit Ihrer Anwendung verwenden.

- Dieses neue Zertifikat wurde App Service auf Plattformebene hinzugefügt. Wenn Sie die SSL-Zertifikate verwenden, die in der App Service-Plattform in Ihrer Anwendung enthalten sind, ist keine Aktion erforderlich. Dies ist das häufigste Szenario. 
- Wenn Sie den Pfad zur SSL-Zertifikatsdatei explizit in Ihren Code einschließen, müssen Sie das neue Zertifikat herunterladen und den Code so aktualisieren, dass das neue Zertifikat verwendet wird. Ein gutes Beispiel für dieses Szenario: Wenn Sie benutzerdefinierte Container in App Service so verwenden, wie in der [App Service-Dokumentation](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress) zu lesen ist. Dies ist ein ungewöhnliches Szenario, doch haben wir festgestellt, dass es von einigen Benutzern verwendet wird.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. Welche Auswirkungen hat es, wenn Azure Kubernetes Service (AKS) mit Azure Database for MariaDB verwendet wird?

Wenn Sie versuchen, mithilfe von Azure Kubernetes Service (AKS) eine Verbindung mit Azure Database for MariaDB herzustellen, ähnelt dies dem Zugriff über eine dedizierte Kundenhostumgebung. Die entsprechenden Schritte finden Sie [hier](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. Welche Auswirkungen hat es, wenn Azure Data Factory zum Herstellen einer Verbindung mit Azure Database for MariaDB verwendet wird?

Ein Connector, der Azure Integration Runtime verwendet, nutzt Zertifikate im Windows-Zertifikatspeicher in der von Azure gehosteten Umgebung. Diese Zertifikate sind bereits mit den neu angewendeten Zertifikaten kompatibel, daher ist keine Aktion erforderlich.

Bei einem Connector, der die selbstgehostete Integration Runtime verwendet, für die Sie den Pfad zur SSL-Zertifikatsdatei explizit in die Verbindungszeichenfolge einschließen, müssen Sie das [neue Zertifikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) herunterladen und die Verbindungszeichenfolge aktualisieren, damit es verwendet werden kann.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Muss ich für diese Änderung eine Wartungsdowntime für den Datenbankserver planen?

Nein. Da die Änderung hier nur auf der Clientseite erfolgt, um eine Verbindung mit dem Datenbankserver herzustellen, ist für diese Änderung keine Wartungsdowntime beim Datenbankserver erforderlich.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Bin ich betroffen, wenn ich nach dem 15. Februar 2021 (15.02.2021) einen neuen Server erstelle?

Bei Servern, die nach dem 15. Februar 2021 (15.02.2021) erstellt werden, verwenden Sie weiterhin [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) für Ihre Anwendungen, um eine Verbindung mithilfe von SSL herzustellen.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Wie oft aktualisiert Microsoft seine Zertifikate, bzw. welche Ablaufrichtlinie gilt?

Diese von Azure Database for MariaDB verwendeten Zertifikate werden von vertrauenswürdigen Zertifizierungsstellen (ZS) bereitgestellt. Die Unterstützung dieser Zertifikate ist also an die Unterstützung dieser Zertifikate durch die ZS gebunden. Das [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)-Zertifikat läuft im Jahr 2025 ab, sodass Microsoft vor dem Ablauf eine Zertifikatänderung durchführen muss. Sollten in diesen vordefinierten Zertifikaten unvorhergesehene Fehler auftreten, muss Microsoft die Zertifikatrotation frühestens wie die Änderung am 15. Februar 2021 durchführen, um zu gewährleisten, dass der Dienst jederzeit sicher und konform ist.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. Muss ich bei Verwendung von Lesereplikaten dieses Update nur auf dem Quellserver oder für die gelesenen Replikate ausführen?

Da dieses Update eine clientseitige Änderung ist, müssen Sie die Änderungen auch für diese Clients anwenden, wenn der Client Daten vom Replikatserver gelesen hat.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. Muss ich bei Verwendung der Datenreplikation eine Aktion ausführen?

- Wenn die Datenreplikation von einem virtuellen Computer (lokal oder Azure-VM) zu Azure Database for MySQL erfolgt, müssen Sie überprüfen, ob zum Erstellen des Replikats SSL verwendet wird. Führen Sie **SHOW SLAVE STATUS** aus, und überprüfen Sie die folgende Einstellung.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

Wenn Sie die [Datenreplikation](concepts-data-in-replication.md) verwenden, um eine Verbindung mit Azure Database for MySQL herzustellen, müssen Sie zwei Punkte berücksichtigen:

- Wenn die Datenreplikation von einem virtuellen Computer (lokal oder Azure-VM) zu Azure Database for MySQL erfolgt, müssen Sie überprüfen, ob zum Erstellen des Replikats SSL verwendet wird. Führen Sie **SHOW SLAVE STATUS** aus, und überprüfen Sie die folgende Einstellung. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  Wenn das Zertifikat für CA_file, SSL_Cert und SSL_Key bereitgestellt wird, müssen Sie die Datei aktualisieren, indem Sie das [neue Zertifikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) hinzufügen und eine kombinierte Zertifikatsdatei erstellen.

- Wenn die Datenreplikation zwischen zwei Azure Database for MySQL-Instanzen erfolgt, müssen Sie das Replikat zurücksetzen, indem Sie **CALL mysql.az_replication_change_master** ausführen und das neue duale Stammzertifikat als letzten Parameter ([master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication)) bereitstellen.

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. Gibt es eine serverseitige Abfrage, um zu überprüfen, ob SSL verwendet wird?

Um zu überprüfen, ob Sie eine SSL-Verbindung zum Herstellen einer Verbindung mit dem Server verwenden, lesen Sie die Informationen unter [Überprüfen der SSL-Verbindung](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. Ist eine Aktion erforderlich, wenn sich DigiCertGlobalRootG2 bereits in meiner Zertifikatsdatei befindet?

Nein Wenn in Ihrer Zertifikatsdatei **DigiCertGlobalRootG2** bereits enthalten ist, ist keine Aktion erforderlich.

### <a name="14-what-if-i-have-further-questions"></a>14. Wie gehe ich vor, wenn ich weitere Fragen habe?

Bei Fragen können Sie Antworten von Communityexperten auf [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com) erhalten. Wenn Sie über einen Supportplan verfügen und technische Hilfe benötigen, [kontaktieren Sie uns](mailto:AzureDatabaseformariadb@service.microsoft.com).

## Technische Dokumentation: Paperless-ngx Backup auf MagentaCLOUD

### 1. Paperless-ngx Export-Skript

#### 1.1 Skript-Erstellung

Ein Bash-Skript `paperless_backup.sh` wurde im Verzeichnis `/home/ms/scripts/` erstellt, um den Export-Befehl für Paperless-ngx auszuführen:

```bash
#!/bin/bash
cd /home/ms/paperless-ngx
sudo docker-compose exec webserver document_exporter ../export
```

#### 1.2 Cronjob-Konfiguration

Ein stündlicher Cronjob wurde eingerichtet, um das Export-Skript auszuführen. Die Konfiguration wurde mit `crontab -e` vorgenommen und enthält folgende Zeile:

```
0 * * * * /home/ms/scripts/paperless_backup.sh
```

Diese Konfiguration führt das Skript `paperless_backup.sh` jede Stunde zur vollen Stunde aus.

### 2. MagentaCLOUD WebDAV-Einbindung

#### 2.1 Voraussetzungen

Installation notwendiger Pakete:

```bash
sudo apt-get install ca-certificates davfs2
sudo dpkg-reconfigure davfs2
sudo usermod -aG davfs2 ms
```

#### 2.2 Einrichtung

1. Erstellung des Verknüpfungspunkts:

```bash
cd /media/
sudo mkdir magentacloud
```

2. Eintrag in `/etc/fstab`:

```
https://magentacloud.de/remote.php/webdav /data/magentacloud davfs user,rw,noauto 0 0
```

3. Konfiguration der WebDAV-Zugangsdaten:

```bash
mkdir ~/.davfs2
echo "https://magentacloud.de/remote.php/webdav e9markusschmitz@t-online.de [Magenta-Cloud EINMALPASSWORT]" >> ~/.davfs2/secrets
chmod 600 ~/.davfs2/secrets
```

#### 2.3 Verbindung herstellen

Einbinden der MagentaCLOUD:

```bash
mount /data/magentacloud
```

Selbstverständlich. Ich werde die Dokumentation um den Punkt 3.2 mit dem initialen Backup-Befehl ergänzen.

## Technische Dokumentation: Paperless-ngx Backup auf MagentaCLOUD

[Punkte 1 und 2 bleiben unverändert]

### 3. Kopia Backup-Konfiguration

#### 3.1 Backup-Intervall

Kopia wurde konfiguriert, um stündliche Backups auf MagentaCLOUD zu erstellen:

```bash
kopia policy set --global --snapshot-interval 1h
```

#### 3.2 Initiales Backup

Nach der Konfiguration von Kopia wurde ein initiales Backup des Paperless-ngx Export-Verzeichnisses erstellt:

```bash
kopia snapshot create /home/paperless-ngx/export
```

Dieser Befehl erstellt einen ersten Snapshot des Export-Verzeichnisses, der als Basis für zukünftige inkrementelle Backups dient.

### 4. Gesamtprozess

1. Das Export-Skript `/home/ms/scripts/paperless_backup.sh` wird stündlich durch den Cronjob ausgeführt und exportiert die Paperless-ngx-Daten.
2. MagentaCLOUD ist als WebDAV-Laufwerk eingebunden und dient als Backup-Ziel.
3. Kopia erstellt stündlich ein verschlüsseltes Backup der exportierten Daten auf MagentaCLOUD, basierend auf dem initialen Snapshot und anschließenden inkrementellen Änderungen.

### 5. Datenwiederherstellung

Die Wiederherstellung der Daten aus dem Kopia-Backup umfasst mehrere Schritte:

#### 5.1 Identifizierung des neuesten Snapshots

Um den neuesten Snapshot zu identifizieren, verwenden Sie folgenden Befehl:

```bash
kopia snapshot list --all
```

Dies listet alle verfügbaren Snapshots auf. Notieren Sie sich die Snapshot-ID des neuesten Eintrags für das Paperless-ngx Backup.

#### 5.2 Wiederherstellung der Daten

Um die Daten aus dem identifizierten Snapshot wiederherzustellen, verwenden Sie den `kopia restore` Befehl:

```bash
kopia restore <snapshot-id> /pfad/zum/wiederherstellungsverzeichnis
```

Ersetzen Sie `<snapshot-id>` durch die im vorherigen Schritt notierte Snapshot-ID.

#### 5.3 Importieren der wiederhergestellten Daten in Paperless-ngx

Nach der Wiederherstellung der Daten können Sie diese in eine neue oder bestehende Paperless-ngx-Instanz importieren:

1. Stellen Sie sicher, dass Paperless-ngx gestoppt ist.
2. Kopieren Sie die wiederhergestellten Daten in die entsprechenden Verzeichnisse Ihrer Paperless-ngx-Installation.
3. Starten Sie Paperless-ngx neu.
4. Führen Sie den Dokumentenimport aus:

```bash
sudo docker exec [paperless docker name] document_importer /pfad/zum/wiederherstellungsverzeichnis
```

### 6. Sicherheitshinweise

Diese erweiterte Konfiguration bietet nun eine vollständige Lösung für Backup und Wiederherstellung von Paperless-ngx, einschließlich der Schritte zur Identifizierung des neuesten Backups und zur Wiederherstellung der Daten in eine Paperless-ngx-Instanz.


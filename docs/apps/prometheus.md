# Prometheus

Prometheus ist ein Open-Source-System zur Überwachung und Alarmierung, das ursprünglich von SoundCloud entwickelt wurde. Es wurde entwickelt, um Metriken aus verschiedenen Zielen effizient zu erfassen und zu speichern und ermöglicht das Erstellen umfangreicher Dashboards und Benachrichtigungen basierend auf diesen Metriken.

## Funktionen

- **Multi-dimensionales Datenmodell**: Metriken werden anhand von Schlüssel-Wert-Paaren (Labels) identifiziert.
- **Flexible Abfragesprache**: PromQL (Prometheus Query Language) ermöglicht komplexe Abfragen und Analysen.
- **Eigenständige Zeitreihendatenbank**: Speichert alle gesammelten Daten.
- **Pushing und Pulling von Metriken**: Unterstützt sowohl das Pulling von Metriken über HTTP als auch das Pushing von Metriken über das Pushgateway.
- **Service Discovery**: Automatische Erkennung von Diensten zur Überwachung.
- **Alertmanager**: Verwaltung und Versenden von Benachrichtigungen basierend auf konfigurierten Regeln.

## Installation

### Docker

Um Prometheus mit Docker zu installieren, führen Sie den folgenden Befehl aus:

```sh
docker run -d -p 9090:9090 --name=prometheus prom/prometheus
```
### Konfiguration

Die Standardkonfigurationsdatei für Prometheus ist `prometheus.yml`, die sich typischerweise in `/etc/prometheus` befindet. Diese Datei definiert, welche Ziele überwacht werden sollen und wie lange Daten aufbewahrt werden.
#### Basiskonfiguration
```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
``` 

### Verwendung
#### Zugriff auf da Dashboard
Nach der Installation und dem Starten des Prometheus-Dienstes können Sie über einen Webbrowser auf das Dashboard zugreifen:
```
http://localhost:9090
``` 

### Dokumentation
Für weitere Informationen und detaillierte Anleitungen besuchen Sie die offizielle Dokumentation:

[Prometheus Documentation](https://prometheus.io/docs/introduction/overview/)
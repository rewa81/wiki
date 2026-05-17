# Kotlin Setup & Deployment

Diese Seite beschreibt das Setup für die lokale Kotlin-Entwicklung sowie die Bereitstellung mittels Docker und Gitea Actions.

## Lokales Setup

Für die Entwicklung mit Kotlin auf deinem lokalen Rechner werden folgende Werkzeuge empfohlen:

### 1. JDK (Java Development Kit)
Kotlin benötigt ein JDK. Empfohlen wird die Installation über **SDKMAN!**, da dies die Verwaltung verschiedener Versionen vereinfacht.

```bash
# Installation von SDKMAN!
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"

# Installation von Java (z.B. Java 21 LTS oder neuer)
sdk install java 21-open
```

### 2. IntelliJ IDEA
Die beste IDE für Kotlin ist **IntelliJ IDEA** (Community oder Ultimate Edition).
- Kotlin-Plugin ist standardmäßig vorinstalliert.
- Native Unterstützung für Gradle und Maven.

### 3. Kotlin Compiler (Optional für CLI)
Wenn du Kotlin direkt im Terminal nutzen möchtest:
```bash
sdk install kotlin
```

---

## Docker Setup (Oracle Linux 10)

Hier ist ein Beispiel für ein Dockerfile, das auf **Oracle Linux 10** basiert. Es verwendet einen **Multi-Stage Build**, um erst die Anwendung zu bauen und dann ein schlankes Runtime-Image zu erstellen.

```dockerfile
# --- Stage 1: Build ---
FROM oraclelinux:10 AS builder

RUN dnf -y install tar gzip unzip curl && dnf clean all

# Java 25 für den Build
RUN curl -L -o /tmp/openjdk.tar.gz https://download.java.net/java/GA/jdk25/archive/openjdk-25_linux-x64_bin.tar.gz && \
    mkdir -p /usr/java && \
    tar -xzf /tmp/openjdk.tar.gz -C /usr/java && \
    rm /tmp/openjdk.tar.gz

ENV JAVA_HOME=/usr/java/jdk-25
ENV PATH=$JAVA_HOME/bin:$PATH

WORKDIR /build
COPY . .

# Build der Applikation (angenommen es wird Gradle verwendet)
# Falls kein Wrapper vorhanden ist, müsste Gradle hier installiert werden
RUN ./gradlew build --no-daemon

# --- Stage 2: Runtime ---
FROM oraclelinux:10

RUN dnf -y install tar gzip && dnf clean all

# Java 25 Runtime
RUN curl -L -o /tmp/openjdk.tar.gz https://download.java.net/java/GA/jdk25/archive/openjdk-25_linux-x64_bin.tar.gz && \
    mkdir -p /usr/java && \
    tar -xzf /tmp/openjdk.tar.gz -C /usr/java && \
    rm /tmp/openjdk.tar.gz

ENV JAVA_HOME=/usr/java/jdk-25
ENV PATH=$JAVA_HOME/bin:$PATH

WORKDIR /app

# Nur die gebaute JAR aus dem Builder-Stage kopieren
COPY --from=builder /build/build/libs/*.jar app.jar

EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

---

## Gitea Action (CI/CD)

Um die Applikation automatisch zu bauen und das Docker-Image zu erstellen, kannst du folgende Gitea Action (`.gitea/workflows/build-and-deploy.yml`) verwenden. In diesem Beispiel nutzen wir die Action auch für den Test und Build vor dem Docker-Schritt.

```yaml
name: Build and Push Kotlin App

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up JDK 25
        uses: actions/setup-java@v4
        with:
          java-version: '25'
          distribution: 'oracle'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      - name: Build with Gradle
        run: ./gradlew build

      - name: Set up Docker Buildx
        uses: actions/setup-buildx-action@v3

      - name: Login to Gitea Registry
        uses: actions/login-action@v3
        with:
          registry: gitea.rwcloud.ch
          username: ${{ github.actor }}
          password: ${{ secrets.GITEA_TOKEN }}

      - name: Build and Push Docker Image
        uses: actions/docker-build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            gitea.rwcloud.ch/${{ github.repository }}/kotlin-app:latest
```

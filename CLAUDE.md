# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TransItalia (fork of Traccar) - GPS tracking system with Java backend and React frontend.

## Build & Run Commands

### Backend (Java 17 required)

```bash
# Build JAR
./gradlew assemble

# Run tests
./gradlew test

# Run single test class
./gradlew test --tests "org.traccar.protocol.Gt06ProtocolDecoderTest"

# Checkstyle (lint)
./gradlew checkstyleMain

# Check dependency updates
./gradlew dependencyUpdates

# Run server (after build)
java -cp "target/tracker-server.jar:target/lib/*" org.traccar.Main debug.xml
```

### Frontend (traccar-web submodule)

```bash
cd traccar-web

# Install dependencies
npm install

# Development server (localhost:3000, proxies to backend:8082)
npm start

# Production build
npm run build

# Lint
npm run lint
npm run lint:fix
```

## Architecture

### Backend Structure (`src/main/java/org/traccar/`)

| Package | Purpose |
|---------|---------|
| `protocol/` | 662 GPS device protocol decoders (GT06, Teltonika, etc.) |
| `api/` | REST API endpoints (Jersey JAX-RS) |
| `model/` | Domain entities (Device, Position, User, Geofence, Event) |
| `handler/` | Netty pipeline handlers for position processing |
| `storage/` | Database abstraction layer |
| `web/` | Jetty web server, WebSocket |
| `notification/` | Alert system |
| `notificators/` | Notification channels (email, SMS, push, webhook) |
| `geocoder/` | Reverse geocoding services |
| `reports/` | Report generation (JXLS Excel) |

### Key Classes

- `Main.java` - Entry point, accepts config file path as argument
- `MainModule.java` - Guice dependency injection configuration
- `BaseProtocolDecoder.java` - Base class for all 200+ protocol decoders
- `ProcessingHandler.java` - Main position processing pipeline

### Frontend Structure (`traccar-web/src/`)

| Directory | Purpose |
|-----------|---------|
| `login/` | Authentication pages |
| `main/` | Main app layout with map |
| `map/` | MapLibre map components |
| `settings/` | Settings pages |
| `reports/` | Report views |
| `common/` | Shared components, hooks, theme |
| `resources/` | Logo, translations (l10n/*.json) |

### Tech Stack

**Backend**: Java 17, Gradle, Netty (GPS protocols), Jetty (HTTP), Jersey (REST), Guice (DI), Liquibase (migrations)

**Frontend**: React 19, Vite, MUI 7, Redux Toolkit, MapLibre GL

**Database**: H2 (dev), PostgreSQL/MySQL/MariaDB/MSSQL (prod)

## Configuration

Server config file (`debug.xml` for dev):
```xml
<properties>
    <entry key='database.driver'>org.h2.Driver</entry>
    <entry key='database.url'>jdbc:h2:./data/database</entry>
</properties>
```

Full config options: https://www.traccar.org/configuration-file/

## Submodule Workflow

Frontend is a git submodule. After frontend changes:
```bash
cd traccar-web && git add . && git commit -m "msg" && git push
cd .. && git add traccar-web && git commit -m "Update submodule" && git push
```

## Code Style

- Backend: Checkstyle with 120 char line limit, LF line endings
- Frontend: ESLint with Airbnb config

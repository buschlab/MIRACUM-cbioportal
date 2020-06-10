# miracum-cbioportal

## Installation

[Installationsanleitung](https://confluence.imi.med.fau.de/pages/viewpage.action?spaceKey=BMBFMI&title=Installationsanleitung+cBioPortal+via+Docker+auf+Ubuntu+18.04+Server)

Ein OncoKB Token kann [hier](https://www.oncokb.org/apiAccess) angefordert werden.

Starten der Development-Version:
```
sudo docker-compose -f docker-compose-dev.yml up
```

**Der erste Startvorgang dauert ca. 15 Minuten**, da hierbei initial drei Docker Images gebaut werden. Nachfolgende Startvorgänge sind deutlich schneller.

Einzelne Dienste (`cbioportal`, `cbioproxy`, `fhirspark`) können über den Befehl
```
./build.sh <Dienstname>
```
neu gebaut werden. Wird das `cbioportal`-Image neu gebaut, sollte unbedingt der `cbioproxy` auch neu gebaut werden.

### Import von Studien

```
docker-compose run cbioportal metaImport.py -u http://cbioportal:8080 -s /study/testpatient -o
```

#### Import der öffentlichen Datenbank (optional)

1. Löschen der Datenbank
```
echo "drop database cbioportal; create database cbioportal;" | docker exec -i cbioportal_database_container mysql -uroot -pP@ssword1
```

2. Import der Studiendatenbank
```
zcat public-portal-dump.latest.sql.gz | docker exec -i cbioportal_database_container mysql -uroot -pP@ssword1 cbioportal
```

## Komponenten

- NGINX Reverse Proxy
- cBioPortal
  - MySQL 5.7 Datenbank
- cBioPortal Session Service
  - Mongo DB
- FHIRspark
  - HAPI FHIR Server

## Ports

| Dienst | Pfad (hinter NGINX) | Port (docker-compose-dev.yml) |
| - | - | - |
| NGINX  | / | 8080 |
| cBioPortal | / | 8081 |
| cBioPortal Debugger | - | 5005 |
| MySQL-Datenbank | - | 3306 |
| Session Service | - | 5000 |
| Mongo DB | - | 27017 |
| FHIRspark | /mtb/ | 3001 |
| HAPI FHIR Server | /fhir/ | 8082 |

## Debugging

An das Backend von cBioPortal kann ein Debugger angehängt werden. Für Visual Studio Code existiert bereits eine [Konfiguration](https://github.com/cBioPortal/cbioportal/blob/master/README.md#%EF%B8%8F%EF%B8%8F-debugging).
# DB-Performance Assignment
## Daten Generation
Für die Generation der Daten benutze ich Rust mit der fake-rs crate. \
So können mehrere millionen Einträge in wenigen Sekunden generiert werden.

## Einspielung in Datenbanken
Beim einspielen der Daten kann man schon einen Starken unterschied in der Benötigten Zeit erkennen. \
Ohne die benutzung von InsertMany ist MongoDB bis zu drei mal langsamer als PostgreSQL. \
Allerdings sind die implementierungen der Einspielungs-Funktionen unterschiedlich und können somit nicht fair verglichen werden. \
(Für Demonstrationszwecke werden hier 100.000 Einträge generiert, der finale Datensatz waren 5.000.000 Einträge) \
\
![image](https://github.com/Toll25/db_performance_assignment/assets/116108222/134390d9-5084-4ea1-9e9c-ab43800183fa)
\
\
![image](https://github.com/Toll25/db_performance_assignment/assets/116108222/487e96cf-e6e4-4483-b44e-4d79833ea869)
\
\
Wenn man InsertMany benutzt sieht es wieder komplett anders aus, dann ist MongoDB um eine Größenordnung schneller. \
Aber wie schon gesagt dieser Vergleich ist nicht ganz fair, wegen den verschiedenen Implementierungen. 

## Performance Aussagen
In MongoDB wird .explain("executionStats") benutzt um die Performance der Datenbank zu analysieren \
Für PostgreSQL werden pg_stat_statements benutzt, diese müssen vorerst aktiviert werden.

Diesen Command ausführen
```
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```
Das in `postgres.conf` hinzufügen, in meinem Fall in `/var/lib/postgresql/data/postgresql.conf`
```
shared_preload_libraries = 'pg_stat_statements'
pg_stat_statements.track = all
```
Container neustarten um die änderungen zu übernehmen

### Select *
MongoDB: 919ms \
PostgreSQL: 2104ms

### Select * where age < 20
MongoDB: 1346ms \
PostgreSQL: 214ms   

Man kann sehen, ohne index ist postgres deutlich schneller als MongoDB wenn nach einer Kondotion gesucht wird. \
Bei einem Select * (bzw find()) ist MongoDB allerdings um einiges schneller.

## Indexes

### Select *
MongoDB: 956ms \
PostgreSQL: 1831ms

### Select * where age < 20
MongoDB: 180ms \
PostgreSQL: 137ms

Mit Indexes gewinnt MongoDB zwar sehr an performance, doch kann immer noch nicht ganz mithalten.

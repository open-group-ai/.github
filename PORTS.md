# Port registry — Open Group fleet

Single source of truth for loopback/service port allocations. Before you bind a new port: read this file, run `sudo ss -ltnp` on the target host, then claim the block here by PR **before** deploying. A repo's own config must match this file; if they disagree, fix the repo or open a PR here, never silently take another app's block.

Convention on `prod-public-eu-01` (app blocks in 18000–20999): each app gets a prod block `N01–N0x` and a dev block `N11–N1x` (some apps use a third decade for extra slots). Redis/mailpit sidecars stay inside the app's own block.

Last verified: 2026-11-26 10:50 Asia/Jerusalem, from `ss -ltnp` + systemd cgroups + nginx upstreams on prod-public-eu-01 (51.77.85.58).

## prod-public-eu-01 (51.77.85.58) — allocated app blocks (curated)

| App | Env | Block | Live listeners (role) | Source |
|---|---|---|---|---|
| OpenLegal | prod | 19801–19803 | 19801 api (`openlegal-api@prod`); 19802/19803 reserved (worker/redis) | openlegal `deploy/README.dev-host.md`, systemd drop-in asserts PORT=19801 |
| OpenLegal | dev | 19831–19833 | 19831 api (`openlegal-api@dev`); 19832/19833 reserved | moved from 19811–19813 (OpenLegal D-061), confirmed by Miguel 2026-11-26 |
| OpenOrder | dev | 19811–19814 | 19811 api, 19813 worker health, 19814 redis; 19812 unused | openorder `deploy/host/provision.sh`; formally reassigned from OpenLegal 2026-11-26 (Miguel) |
| OpenOrder | prod | 19821–19824 | 19821 api, 19823 worker health, 19824 redis; 19822 unused | openorder `deploy/host/provision.sh`, `/etc/nginx/conf.d/openorder-prod.conf` |
| OpenBrain | prod | 19901–19907, 19923–19924 | 19901 api, 19902 web, 19903 worker, 19907 redis; 19923/19924 green slots (nginx) | `/etc/nginx/conf.d/openbrain-prod-upstreams.conf` |
| OpenBrain | dev | 19911–19918, 19921–19922 | 19911 api, 19912 web, 19913 worker, 19917 redis, 19918 model bridge; 19921/19922 green slots (nginx) | `/etc/nginx/conf.d/openbrain-dev-upstreams.conf` |
| OpenHealth | prod / dev | 19701–19703 / 19711–19713 | api, worker, redis | systemd units |
| OpenMap | prod + dev (shared block) | 19021–19039 | 19021 api dev (`openmap-api@dev.socket`), 19022 api prod (`openmap-api@prod.socket`), both nginx upstreams; 19023–19028 reserved (api alt slots); 19029 shadow api for the engine gate (`openmap-api-shadow`, not routed); 19031–19034 reserved; 19035 Photon tunnel to knowledge-ca-01 (`openmap-engine-tunnel`); 19036 Valhalla tunnel to knowledge-ca-01 (`openmap-engine-tunnel`); 19037–19039 reserved (engines) | openmap `deploy/engine-host/ppe/openmap-engine-tunnel.service`, `/etc/openmap/{dev,prod}.env`, `/etc/nginx/sites-available/openmap-{dev,prod}.conf` |
| OpenBook | prod / dev | 20101–20103 / 20111–20113 | none yet (requested by openbook.run 2026-11-26; free at verification) | openbook.run |

Other app blocks are listed in the live table below; promote them to this curated table when the owning app confirms its block.

## Known irregularities (2026-11-26, not yet fixed)

- 19714 / 19715: `openfix-dev-redis` / `openfix-prod-redis` sit in the 197xx range used by OpenHealth (OpenHealth uses 19701–19703, 19711–19713; no live clash).

## prod-public-eu-01 — live listeners (snapshot)

| Port | Bind | systemd unit / scope |
|---|---|---|
| 22 | `0.0.0.0:22` | `ssh.service` |
| 25 | `0.0.0.0:25` | `postfix.service` |
| 53 | `127.0.0.53%lo:53` | `systemd-resolved.service` |
| 80 | `0.0.0.0:80` | `nginx.service` |
| 443 | `0.0.0.0:443` | `nginx.service` |
| 465 | `0.0.0.0:465` | `postfix.service` |
| 587 | `0.0.0.0:587` | `postfix.service` |
| 993 | `0.0.0.0:993` | `dovecot.service` |
| 1025 | `127.0.0.1:1025` | `opencash-dev-mailpit.service` |
| 1026 | `127.0.0.1:1026` | `openaidesk-dev-mailpit.service` |
| 3100 | `127.0.0.1:3100` | `openhome-prod.service` |
| 3101 | `127.0.0.1:3101` | `openhome-dev.service` |
| 3306 | `127.0.0.1:3306` | `mysql.service` |
| 5432 | `127.0.0.1:5432` | `postgresql@18-main.service` |
| 5434 | `127.0.0.1:5434` | `postgresql@18-nsvxdev.service` |
| 5436 | `127.0.0.1:5436` | `postgresql@18-clone.service` |
| 5437 | `127.0.0.1:5437` | `mb-pg16-standby.service` |
| 6379 | `127.0.0.1:6379` | `redis-server@prod.service` |
| 6380 | `127.0.0.1:6380` | `ogp-openhome-dev-redis.service` |
| 6432 | `127.0.0.1:6432` | `pgbouncer.service` |
| 8025 | `127.0.0.1:8025` | `opencash-dev-mailpit.service` |
| 8026 | `127.0.0.1:8026` | `openaidesk-dev-mailpit.service` |
| 8102 | `127.0.0.1:8102` | `opentrail-valhalla.service` |
| 8103 | `127.0.0.1:8103` | `opentrail-overpass.service` |
| 8611 | `127.0.0.1:8611` | `openlooks-realtime@dev.service` |
| 8612 | `127.0.0.1:8612` | `nginx.service` |
| 8691 | `127.0.0.1:8691` | `openfly-webhook.service` |
| 8795 | `127.0.0.1:8795` | `openprank-voice-dev@b.service` |
| 8891 | `127.0.0.1:8891` | `opendkim.service` |
| 9301 | `127.0.0.1:9301` | `openmedia-api@dev.service` |
| 9302 | `127.0.0.1:9302` | `openmedia-api@prod.service` |
| 9501 | `127.0.0.1:9501` | `openanimals-api@dev.service` |
| 9502 | `127.0.0.1:9502` | `openanimals-api@prod.service` |
| 9512 | `127.0.0.1:9512` | `openanimals-ml@prod.service` |
| 9600 | `127.0.0.1:9600` | `openaimusic-api@prod.service` |
| 9601 | `127.0.0.1:9601` | `openaimusic-api@dev.service` |
| 9610 | `127.0.0.1:9610` | `openaimusic-api@prod.service` |
| 9611 | `127.0.0.1:9611` | `openaimusic-api@prod.service` |
| 9612 | `127.0.0.1:9612` | `openaimusic-api@prod.service` |
| 9615 | `127.0.0.1:9615` | `openaimusic-api@dev.service` |
| 9616 | `127.0.0.1:9616` | `openaimusic-api@dev.service` |
| 9617 | `127.0.0.1:9617` | `openaimusic-api@dev.service` |
| 9701 | `127.0.0.1:9701` | `openfood-api@dev.service` |
| 9715 | `127.0.0.1:9715` | `openfood-api@dev.service` |
| 10587 | `127.0.0.1:10587` | `postfix.service` |
| 15432 | `127.0.0.1:15432` | `mb-pg16-tunnel.service` |
| 18001 | `127.0.0.1:18001` | `openfix-prod-api@1.service` |
| 18003 | `127.0.0.1:18003` | `openfix-prod-worker.service` |
| 18004 | `127.0.0.1:18004` | `openfix-prod-api@2.service` |
| 18011 | `127.0.0.1:18011` | `openfix-dev-api@1.service` |
| 18012 | `127.0.0.1:18012` | `openfix-dev-api@2.service` |
| 18013 | `127.0.0.1:18013` | `openfix-dev-worker.service` |
| 18111 | `127.0.0.1:18111` | `opensupply-dev-api.service` |
| 18112 | `127.0.0.1:18112` | `opensupply-dev-worker.service` |
| 18157 | `127.0.0.1:18157` | `nginx.service` |
| 18158 | `127.0.0.1:18158` | `nginx.service` |
| 18311 | `127.0.0.1:18311` | `opensave-dev-api.service` |
| 18312 | `127.0.0.1:18312` | `opensave-dev-internal.service` |
| 18313 | `127.0.0.1:18313` | `opensave-dev-worker.service` |
| 18443 | `127.0.0.1:18443` | `nginx.service` |
| 18501 | `127.0.0.1:18501` | `opencredit-api@prod-blue.service` |
| 18511 | `127.0.0.1:18511` | `opencredit-api@dev-blue.service` |
| 18601 | `127.0.0.1:18601` | `openfuneral-prod-api.service` |
| 18602 | `127.0.0.1:18602` | `openfuneral-prod-web.service` |
| 18603 | `127.0.0.1:18603` | `openfuneral-prod-worker.service` |
| 18611 | `127.0.0.1:18611` | `openfuneral-dev-api.service` |
| 18612 | `127.0.0.1:18612` | `openfuneral-dev-web.service` |
| 18613 | `127.0.0.1:18613` | `openfuneral-dev-worker.service` |
| 18711 | `127.0.0.1:18711` | `opencollect-dev-php.service` |
| 18789 | `127.0.0.1:18789` | `openclaw-gateway-link.service` |
| 18801 | `127.0.0.1:18801` | `openaidesk-prod-api.service` |
| 18802 | `127.0.0.1:18802` | `openaidesk-prod-worker.service` |
| 18811 | `127.0.0.1:18811` | `openaidesk-dev-api.service` |
| 18812 | `127.0.0.1:18812` | `openaidesk-dev-worker.service` |
| 18901 | `127.0.0.1:18901` | `openfaith-prod-web@blue.service` |
| 18902 | `127.0.0.1:18902` | `openfaith-prod-api@blue.service` |
| 18904 | `127.0.0.1:18904` | `openfaith-prod-web@green.service` |
| 18905 | `127.0.0.1:18905` | `openfaith-prod-api@green.service` |
| 18906 | `127.0.0.1:18906` | `openfaith-prod-worker@green.service` |
| 18911 | `127.0.0.1:18911` | `openfaith-dev-web.service` |
| 18912 | `127.0.0.1:18912` | `openfaith-dev-api.service` |
| 18913 | `127.0.0.1:18913` | `openfaith-dev-worker.service` |
| 18921 | `127.0.0.1:18921` | `openspy-dev-api.service` |
| 18931 | `127.0.0.1:18931` | `openspy-prod-api.service` |
| 19011 | `127.0.0.1:19011` | `openagents-dev-api@3c03995e9fce120a241e458e6685c601a9db01d6.service` |
| 19013 | `127.0.0.1:19013` | `openagents-dev-worker@3c03995e9fce120a241e458e6685c601a9db01d6.service` |
| 19014 | `127.0.0.1:19014` | `openagents-dev-mailpit.service` |
| 19015 | `127.0.0.1:19015` | `openagents-dev-mailpit.service` |
| 19021 | `127.0.0.1:19021` | `openmap-api@dev.service` |
| 19022 | `127.0.0.1:19022` | `openmap-api@prod.service` (socket `openmap-api@prod.socket`)` |
| 19035 | `127.0.0.1:19035` | `openmap-photon-tunnel.service` |
| 19036 | `127.0.0.1:19036` | `openmap-valhalla-tunnel.service` |
| 19111 | `127.0.0.1:19111` | `openbusiness-dev-api.service` |
| 19112 | `127.0.0.1:19112` | `openbusiness-dev-worker.service` |
| 19201 | `127.0.0.1:19201` | `openbuy-prod-fpm.service` |
| 19203 | `127.0.0.1:19203` | `nginx.service` |
| 19211 | `127.0.0.1:19211` | `openbuy-dev-fpm.service` |
| 19213 | `127.0.0.1:19213` | `nginx.service` |
| 19312 | `127.0.0.1:19312` | `openmove-dev-api-slot@b.service` |
| 19313 | `127.0.0.1:19313` | `openmove-dev-worker-slot@b.service` |
| 19401 | `127.0.0.1:19401` | `openlove-run-prod-api.service` |
| 19403 | `127.0.0.1:19403` | `openlove-run-prod-worker.service` |
| 19411 | `127.0.0.1:19411` | `openlove-run-dev-api.service` |
| 19413 | `127.0.0.1:19413` | `openlove-run-dev-worker.service` |
| 19511 | `127.0.0.1:19511` | `opencash-dev-api@63612b389d6395bba7854208430e816495f442d3.service` |
| 19512 | `127.0.0.1:19512` | `opencash-dev-worker@63612b389d6395bba7854208430e816495f442d3.service` |
| 19611 | `127.0.0.1:19611` | `opentravel-dev-api.service` |
| 19612 | `127.0.0.1:19612` | `opentravel-dev-worker.service` |
| 19701 | `127.0.0.1:19701` | `openhealth-prod-api.service` |
| 19702 | `127.0.0.1:19702` | `openhealth-prod-worker.service` |
| 19703 | `127.0.0.1:19703` | `openhealth-prod-redis.service` |
| 19711 | `127.0.0.1:19711` | `openhealth-dev-api.service` |
| 19712 | `127.0.0.1:19712` | `openhealth-dev-worker.service` |
| 19713 | `127.0.0.1:19713` | `openhealth-dev-redis.service` |
| 19714 | `127.0.0.1:19714` | `openfix-dev-redis.service` |
| 19715 | `127.0.0.1:19715` | `openfix-prod-redis.service` |
| 19801 | `127.0.0.1:19801` | `openlegal-api@prod.service` |
| 19811 | `127.0.0.1:19811` | `openorder-dev-api.service` |
| 19813 | `127.0.0.1:19813` | `openorder-dev-worker.service` |
| 19814 | `127.0.0.1:19814` | `openorder-dev-redis.service` |
| 19821 | `127.0.0.1:19821` | `openorder-prod-api.service` |
| 19823 | `127.0.0.1:19823` | `openorder-prod-worker.service` |
| 19824 | `127.0.0.1:19824` | `openorder-prod-redis.service` |
| 19831 | `127.0.0.1:19831` | `openlegal-api@dev.service` |
| 19901 | `127.0.0.1:19901` | `openbrain-prod-api@blue.service` |
| 19902 | `127.0.0.1:19902` | `openbrain-prod-web@blue.service` |
| 19903 | `127.0.0.1:19903` | `openbrain-prod-worker.service` |
| 19907 | `127.0.0.1:19907` | `openbrain-prod-redis.service` |
| 19911 | `127.0.0.1:19911` | `openbrain-dev-api@blue.service` |
| 19912 | `127.0.0.1:19912` | `openbrain-dev-web@blue.service` |
| 19913 | `127.0.0.1:19913` | `openbrain-dev-worker.service` |
| 19917 | `127.0.0.1:19917` | `openbrain-dev-redis.service` |
| 19918 | `127.0.0.1:19918` | `openbrain-dev-model-bridge.service` |
| 20001 | `127.0.0.1:20001` | `opendate-prod-api.service` |
| 20003 | `127.0.0.1:20003` | `opendate-prod-worker.service` |
| 20011 | `127.0.0.1:20011` | `opendate-dev-api.service` |
| 20013 | `127.0.0.1:20013` | `opendate-dev-worker.service` |
| 33060 | `127.0.0.1:33060` | `mysql.service` |

## Changelog

- 2026-11-26: OpenMap claims 19021–19039 (see curated table). The 19031/19032/19098 listeners were leftover test APIs (not nginx upstreams; dev and prod are served by `openmap-api@{dev,prod}` on 19021/19022) and were stopped 10:52 Asia/Jerusalem. The engine tunnel moved 18002 (Valhalla, was inside OpenFix 18001–18004) → 19036 and 12322 (Photon) → 19035 as `openmap-valhalla-tunnel` / `openmap-photon-tunnel` (user `openmap-tunnel`); `openmap-engine-tunnel` stopped and disabled 10:56, so 12322 and 18002 are free. OpenMap snapshot rows refreshed 10:58.

- 2026-11-26: Registry created (no prior fleet registry existed in open-group-ai, master-execute, prod-public-eu-01 or Mac48 agent-system). Resolved the OpenLegal/OpenOrder dev conflict: OpenLegal keeps prod 19801–19803 and dev moves to 19831–19833; 19811–19814 formally reassigned to OpenOrder dev; OpenOrder prod 19821–19824. No running service was changed.

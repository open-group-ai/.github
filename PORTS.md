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
| OpenBook | prod / dev | 20101–20103 / 20111–20113 | none yet (requested by openbook.run 2026-11-26; free at verification) | openbook.run |
| Claudia | prod / dev | 20701–20705 / 20711–20715 | none yet (N01 web, N02 api, N03 worker health, N04 redis, N05 reserved; requested by Claudia 2026-11-26; free at verification) | open-group-ai/claudia |
| OpenStar | prod / dev | 20201–20203 / 20211–20213 | none yet (claim by openstar.run 2026-11-26: api, worker, reserved; free at `ss -ltnp` 2026-11-26 12:32 Asia/Jerusalem) | openstar.run |
| OpenVideo | prod / dev | 20301–20305 / 20311–20315 | none yet (claimed by openvideo.run 2026-11-26; free at `sudo ss -ltnp` 2026-11-26 12:54 and re-checked 13:47 Asia/Jerusalem, no nginx/systemd references). Roles: N01 web, N02 api, N03 worker health, N04 render worker, N05 redis | open-group-ai/openvideo AGENTS.md |
| OpenVisa | prod / dev | 20501–20509 / 20511–20519 (block 20500–20519) | none yet (block reassigned by Boss 2026-11-26 after OpenStar took 202xx; `ss -ltnp` on prod-public-eu-01 shows 20500–20519 free at 12:57 Asia/Jerusalem; no open PR overlaps) | openvisa.run |
| OpenPlay | prod / dev | 20601–20609 / 20611–20619 (block 20600–20619) | none yet (claim by openplay.run 2026-11-26: api, worker, realtime/multiplayer, redis, reserved; free at `sudo ss -ltnp` on prod-public-eu-01 2026-11-26 13:13 and absent from this file and open PRs #7/#8/#12/#13) | openplay.run (open-group-ai/openplay) |
| OpenArt | prod / dev | 20401–20409 / 20411–20419 | none yet (claim by openart.run 2026-11-26: api blue/green, worker health, renderer, reserved; free at `sudo ss -ltnp` on prod-public-eu-01 2026-11-26 12:58 and re-checked 13:16 Asia/Jerusalem; absent from this file and the host copy) | openart.run (open-group-ai/openart) |
| OpenPDF | prod / dev | 22301–22309 / 22311–22319 (block 22300–22319) | none yet (claim by openpdf.run 2026-11-27: N01 api, N02 worker health, N03–N09 reserved; free at `sudo ss -ltn` on prod-public-eu-01 2026-11-27 and absent from this file, nginx, systemd and open PRs #7–#27) | open-group-ai/openpdf `deploy/README.md` |

| og-connect (Universal Connect AI) | prod / dev | 21001–21005 / 21011–21015 (block 21000–21019) | none yet (claim by Boss 2026-11-26 for open-group-ai/og-connect: N01 api, N02 hosted web, N03 worker/events, N04 redis, N05 reserved; `sudo ss -ltnH` on prod-public-eu-01 at 14:10 Asia/Jerusalem shows no listeners in 210xx; absent from this file; 207xx/208xx held by open PRs #16/#18). Note: first block above the 18000–20999 convention because 180xx–209xx are allocated or claimed | open-group-ai/og-connect |
| OpenMeeting | prod / dev | 22401–22405 / 22411–22415 (block 22400–22419) | none yet (claim by openmeeting.run 2026-11-27: N01 api+web (in-process worker), N02 reserved worker health, N03–N05 reserved; `sudo ss -ltnH` and nginx/systemd grep on prod-public-eu-01 show nothing in 224xx; absent from this file and open PRs) | open-group-ai/openmeeting |
| OpenHost | prod / dev | 22501–22505 / 22511–22515 (block 22500–22519) | none yet (claim by openhost.run 2026-11-27: N01 api+web, N02 worker health, N03–N05 reserved; `sudo ss -ltnH` and nginx/systemd grep on prod-public-eu-01 show nothing in 225xx; absent from this file and open PRs) | open-group-ai/openhost |
Other app blocks are listed in the live table below; promote them to this curated table when the owning app confirms its block.

## prod-adult-eu-01 (51.77.85.223) — allocated app blocks (curated)

Adult apps only (separate host from prod-public-eu-01; numbers may repeat across hosts without clashing).

| App | Env | Block | Live listeners (role) | Source |
|---|---|---|---|---|
| Open Porn | dev | 18800–18849 | 18800 api slot A (`openporn-dev-api`), 18801 worker health (`openporn-dev-worker`), 18811 redis (`openporn-dev-redis`); 18802 api slot B (zero-downtime switch, reserved); 18810 local-test PostgreSQL on build hosts only | openporn `deploy/lib.sh`, `deploy/README.dev-host.md` |
| Open Porn | prod | 18850–18899 | 18850 api slot A (`openporn-prod-api`), 18851 worker health (`openporn-prod-worker`), 18861 redis (`openporn-prod-redis`); 18852 api slot B (reserved) | openporn `deploy/lib.sh`, `deploy/prod/openporn.conf` |

Verified 2026-11-26 12:50 Asia/Jerusalem with `ss -ltnp` on prod-adult-eu-01: 18800, 18801, 18811, 18850, 18851, 18861 listening; 18802/18852 idle (B slots).

## Known irregularities (2026-11-26, not yet fixed)

- 19031 / 19032 / 19098: OpenMap API processes (`node apps/api/src/server.ts`) running outside systemd in login sessions (19031 dev with deleted cwd, 19032 prod, 19098 places bootstrap). Owner: OpenMap; move into units or stop.
- 18002: `openmap-engine-tunnel` (Valhalla forward) sits inside OpenFix's 18001–18004 block.
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
| 12322 | `127.0.0.1:12322` | `openmap-engine-tunnel.service` |
| 15432 | `127.0.0.1:15432` | `mb-pg16-tunnel.service` |
| 18001 | `127.0.0.1:18001` | `openfix-prod-api@1.service` |
| 18002 | `127.0.0.1:18002` | `openmap-engine-tunnel.service` |
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
| 19022 | `127.0.0.1:19022` | `openmap-api@prod.service` |
| 19031 | `127.0.0.1:19031` | `session-55125.scope` |
| 19032 | `127.0.0.1:19032` | `session-55291.scope` |
| 19098 | `127.0.0.1:19098` | `session-40672.scope` |
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

- 2026-11-27: OpenHost claims 22500–22519 on prod-public-eu-01 (prod 22501–22505, dev 22511–22515; N01 api+web, N02 worker health). Nothing bound at 225xx; no other entry changed.
- 2026-11-27: OpenMeeting claims 22400–22419 on prod-public-eu-01 (prod 22401–22405, dev 22411–22415). Nothing bound at 224xx; no other entry changed.
- 2026-11-27: OpenForm claims 21900–21919 on prod-public-eu-01 (prod 21901–21909, dev 21911–21919; API 21901/21911, worker 21902/21912). Nothing bound at 21900–21999 at 09:07 UTC; no other entry changed.
- 2026-11-26: OpenVideo claims prod 20301–20305 and dev 20311–20315 on prod-public-eu-01 (moved from 202xx after OpenStar #11; nothing listening and no nginx/systemd references at 12:54, re-checked 13:47 Asia/Jerusalem).
- 2026-11-26: OpenArt claims 20400–20419 on prod-public-eu-01 (prod 20401–20409, dev 20411–20419). Nothing bound yet; no other entry changed.
- 2026-11-26: OpenPlay claims 20600–20619 on prod-public-eu-01 (prod 20601–20609, dev 20611–20619). Earlier candidate 20201–20213 was already OpenStar's (#11). No running service changed.
- 2026-11-26: OpenVisa claims 20500–20519 (prod 20501–20509, dev 20511–20519). Replaces the withdrawn 20200–20219 claim (PR #9), which collided with OpenStar. Nothing bound yet; no other entry changed.
- 2026-11-26: OpenStar claims prod 20201–20203 and dev 20211–20213 (free at verification; nothing bound yet).

- 2026-11-26: Registry created (no prior fleet registry existed in open-group-ai, master-execute, prod-public-eu-01 or Mac48 agent-system). Resolved the OpenLegal/OpenOrder dev conflict: OpenLegal keeps prod 19801–19803 and dev moves to 19831–19833; 19811–19814 formally reassigned to OpenOrder dev; OpenOrder prod 19821–19824. No running service was changed.
- 2026-11-26: Registered Open Porn on prod-adult-eu-01 (dev 18800–18849, prod 18850–18899; live 18800/18801/18811 and 18850/18851/18861, B slots 18802/18852). No running service was changed.

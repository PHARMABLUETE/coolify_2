# Coolify 2.0 — Pharmablüte Deploy-Plattform

## Was ist das?
Selbst-gehostete Deploy-Plattform für alle Pharmablüte-Apps.
Coolify 2.0 ist der Nachfolger des alten Coolify-Servers (automation.pharmabluete.de),
der wegen eines Caddy-Konflikts auf Port 80/443 nicht sauber funktioniert hat.

## Status (Stand 2026-04-09)
- [x] Server vorhanden und eingerichtet
- [x] Coolify installiert, Traefik läuft auf Port 80/443
- [x] API-Token erstellt → `secrets/coolify_2.md`
- [x] Coolify Dashboard: https://coolify.pharmabluete.de
- [x] Resterampe deployed und live auf https://lagerverwaltung.t3rp.de
- [x] DNS bei Netcup aktualisiert (vollständige Propagierung bis ~10.04. Mittag)

## Server
- **Host:** coolify.pharmabluete.de
- **IP:** 49.12.103.251
- **SSH:** `ssh -i ~/.ssh/tn-coolify root@coolify.pharmabluete.de -p 2232`
- **Zugangsdaten:** `secrets/coolify_2.md`

## Coolify API
- **URL:** https://coolify.pharmabluete.de/api/v1
- **Token:** siehe `secrets/coolify_2.md`
- **Skill:** `claude-skills/deployment/coolify/SKILL.md`
- **Deploy:** `POST /api/v1/deploy?uuid={uuid}&force=false`

## Deployed Apps
| App | UUID | Domain | Status |
|-----|------|--------|--------|
| resterampe | b5es5tmzj8v0j4rphebql8qs | https://lagerverwaltung.t3rp.de | ✅ live |

## Coolify Ressourcen (UUIDs)
| Name | UUID | Typ |
|------|------|-----|
| pharmabluete | k2er8yrj2v6wr3o6svrnfxv1 | Project |
| localhost | vibbo44u6v36l7v628sbt1y8 | Server |

## Offene Punkte
- [ ] DNS-Propagierung vollständig (lagerverwaltung.t3rp.de → 49.12.103.251, bis ~10.04. Mittag)
- [ ] Resterampe: Produkte laden nicht → separates Problem im n8n-Workflow (nicht Coolify)
- [ ] mcpmarket.com Hetzner Cloud MCP-Skill einbinden (Zukunft)
- [ ] n8n-Stack auf diesen Server migrieren (eigenes zukünftiges Projekt — siehe unten)

## Zukünftiges Projekt: n8n-Stack hierher migrieren
Aktuell laufen n8n, pgAdmin und PostgreSQL noch manuell auf dem alten Server
(automation.pharmabluete.de, 128.140.38.246). Ziel ist es, diese Services
ebenfalls auf Coolify 2.0 zu bringen — damit Traefik alles managed und
der alte Server abgeschaltet werden kann.

### Was zu migrieren ist
| Service | Aktuell (alter Server) | Ziel |
|---------|----------------------|------|
| PostgreSQL | Docker Container manuell | Coolify DB-Service |
| n8n | Docker Compose manuell | Coolify App |
| pgAdmin | Docker Compose manuell | Coolify App |

### Vorgehen (kein Downtime)
1. PostgreSQL als Coolify DB-Service anlegen — gleiche Credentials
2. n8n als Coolify App anlegen — gleiche Env Vars testen
3. pgAdmin als Coolify App anlegen — testen
4. Umschalten: alten Stack stoppen → Traefik übernimmt Port 80/443
5. Alten Server abschalten

### Wichtige Infos für die Migration
- n8n Env Vars: aus `/root/n8n-docker-caddy/docker-compose.yml` auf altem Server lesen
- PostgreSQL Credentials: `secrets/postgres_hetzner.md`
- n8n Credential IDs bleiben gleich (in der PostgreSQL DB gespeichert)
- SSH alter Server: `ssh -i ~/.ssh/til_nagel -p 2232 root@automation.pharmabluete.de`
- Rollback jederzeit: `docker compose -f /root/n8n-docker-caddy/docker-compose.yml up -d`

## Entscheidungen & Learnings
- SSH-Key: `~/.ssh/tn-coolify` (alter Server nutzte `~/.ssh/til_nagel`)
- `base_directory` in Coolify API mit führendem Slash: `/dashboard`
- Domain per PATCH mit Feld `domains` setzen (nicht `fqdn`)
- Deploy: `POST /api/v1/deploy?uuid=...` (nicht `/applications/{uuid}/deploy`)
- Next.js `output: standalone` → Start Command leer lassen (`npm run start`)
  → `node .next/standalone/server.js` serviert keine statischen Files (CSS 404)
- Traefik läuft direkt auf 80/443 → kein iptables-Workaround nötig

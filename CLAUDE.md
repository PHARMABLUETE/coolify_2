# Coolify 2.0

## Ziel
Neuer Coolify-Server — saubere Installation, überträgt den Stand aus `projects/coolify/`.

## Status (Stand 2026-04-09)
- [x] Server vorhanden
- [x] Coolify installiert
- [x] API-Token erstellt → `secrets/coolify_2.md`
- [x] Coolify Dashboard erreichbar: https://coolify.pharmabluete.de
- [x] Resterampe deployed und live (HTTP 200 auf neuem Server)
- [x] DNS bei Netcup aktualisiert (Propagierung ~14h)

## Server
- **Host:** coolify.pharmabluete.de
- **IP:** 49.12.103.251
- **SSH:** `ssh -i ~/.ssh/tn-coolify root@coolify.pharmabluete.de -p 2232`
- **Zugangsdaten:** siehe `secrets/coolify_2.md`

## Coolify API
- URL: https://coolify.pharmabluete.de/api/v1
- Token: siehe `secrets/coolify_2.md`
- Skill: `claude-skills/deployment/coolify/SKILL.md`
- Deploy-Endpoint: `POST /api/v1/deploy?uuid={uuid}&force=false`

## Coolify Ressourcen
| Name | UUID | Typ |
|------|------|-----|
| pharmabluete (Projekt) | k2er8yrj2v6wr3o6svrnfxv1 | Project |
| localhost (Server) | vibbo44u6v36l7v628sbt1y8 | Server |
| resterampe (App) | b5es5tmzj8v0j4rphebql8qs | Application |

## Domains
| Domain | Ziel | Status |
|--------|------|--------|
| coolify.pharmabluete.de | Coolify Dashboard | ✅ live |
| lagerverwaltung.t3rp.de | Resterampe | ✅ live (DNS propagiert ~14h) |

## Vorteile gegenüber Coolify 1.0
- Traefik läuft direkt auf Port 80/443 — kein Caddy-Konflikt
- HTTPS automatisch via Let's Encrypt für alle Apps
- Kein iptables-Workaround nötig
- Kein manueller Caddy-Eintrag nötig

## Offene Punkte
- [ ] DNS-Propagierung abwarten (lagerverwaltung.t3rp.de → 49.12.103.251)
- [ ] Weitere Apps nach Bedarf deployen

## Entscheidungen
- Neuer SSH-Key: `~/.ssh/tn-coolify` (statt `til_nagel` wie beim alten Server)
- base_directory mit führendem Slash: `/dashboard` (API-Anforderung dieser Coolify-Version)
- Domain per PATCH mit Feld `domains` setzen (nicht `fqdn`)

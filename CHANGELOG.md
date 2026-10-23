# Changelog

## 2026-10-12
- Configured Alertmanager email notifications via Gmail App Password
- Alerts routing: critical (1h repeat), warning (6h repeat)
- App password stored as SealedSecret, no credentials in repo

## 2026-10-07
- Observed pakube2 worker node i/o timeout causing cluster-wide pod restart
- Cluster self-healed within ~2 minutes, all pods returned to healthy
- Root cause likely aging Netgear unmanaged switch — planned Mikrotik upgrade will address

## 2026-10-04
- Added Renovate bot for automated dependency updates
- Added branch protection rules on main
- Added CI status badge to README
- Disabled Docker service on control plane node (unused, DNS noise source)
- Added Technitium reverse DNS zones for pod/service CIDRs (10.42, 10.43)

## 2026-10-03
- Added GitHub Actions CI pipeline (helm template | kubeconform)
- Deployed Home Assistant via full CI/CD pipeline
- Added Traefik IngressRoute for ha.codexasystems.net
- Configured trusted proxy via HA UI for Traefik reverse proxy
- Updated main README with CI pipeline section and Mermaid diagram
- Added per-app README for Home Assistant

## Prior
- See git log and README for evolutionary history

## 2026-10-25
- Attempted Minio deployment for Velero backup target
- Abandoned — MinIO removed community images from Docker Hub (Sep 2026) and quay.io (Sep 2026)
- Backup strategy deferred pending evaluation of SeaweedFS or alternative S3-compatible storage

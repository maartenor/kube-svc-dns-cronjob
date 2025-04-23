# Readme.MD
## Description kube-svc-dns-cronjob
This CronJob will collect all K3s services from all namespaces that have an external IP assigned, along with their FQDNs, and save them in a format compatible a Pi-hole's `/etc/hosts` file. Here's how it works:

1. **Schedule:** Runs every 6 hours, but you can adjust the schedule: `0 */6 * * *` line to your preferred frequency.
2. **Data Collection**: The script:
    * Collects all services across all namespaces
    * Filters for only services with external IPs
    * Formats them as `<external-ip> <service-name> <service-name>.local <service-name>.<namespace>.svc.cluster.local` for example: 
    * Saves the output to a persistent volume

3. **Persistent Storage:** Uses a PersistentVolumeClaim with the nfs-client storage class to store the generated hosts file.

4. **Output:** Creates a file at `external-ips-hosts.txt` that you can mount to your Pi-hole server.

## Usage
To use this with Pi-hole, you would need to:
1. Deploy this CronJob in your K3s cluster `kubectl apply -f ./kube-svc-dns-cronjob.yaml`.
2. Configure your Pi-hole or a cronjob to periodically copy and include this `external-ips-hosts.txt` file in its `/etc/hosts` configuration.

```bash
$ cat /mnt/nfs_client/pihole-external-ip-services-pvc-pvc-f5c3c31b-df14-45d9-bba2-b144fa33898b/external-ips-hosts.txt
# K3s External Services - Auto-generated Wed Apr 23 18:22:18 UTC 2025

192.168.1.10 traefik traefik.local traefik.traefik.svc.cluster.local
192.168.1.15 prometheus prometheus.local prometheus.develop.svc.cluster.local
192.168.1.20 argocd-server argocd-server.local argocd-server.argocd.svc.cluster.local
192.168.1.25 youtransfer youtransfer.local youtransfer.develop.svc.cluster.local

# End of K3s External Services - Wed Apr 23 18:22:26 UTC 2025
```

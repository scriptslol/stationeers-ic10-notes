> is there a clear, cost-effective, means of running a dedicated stationeers server?

---

Yes, and you've got several solid options. The dedicated server itself is free — RocketWerkz ships it as a SteamCMD app (app ID 600760), runs on Windows or Linux (needs glibc 2.34+, so Ubuntu 22.04/24.04, Debian 12, or RHEL 9 clones), and the binary is `rocketstation_DedicatedServer.exe` / equivalent Linux build. So cost just comes down to where you run it.

The real cost driver is RAM: 6–8 GB for a small 4–8 player crew, 12+ GB for larger stations with heavy automation. CPU is mostly single-thread bound (atmos sim, IC10 ticks), so prioritize clock speed over core count.

**Cheapest real-world options, roughly cheapest to most expensive:**

A small VPS will beat any managed game host on price by a wide margin. Hetzner CX32 (4 vCPU shared AMD, 8 GB RAM, 80 GB NVMe) runs ~$8/mo and handles a small group comfortably; their CX42 (8 vCPU / 16 GB) is around $16/mo if you want headroom for a big station. Contabo and OVH are similar. You'd just `steamcmd +login anonymous +app_update 600760 validate +quit`, run it under `systemd` or `tmux`, and open UDP 27015/27016. Given your platform-engineering background this is probably the path of least friction for the money — and honestly you could just drop it on your existing DOKS cluster as a `StatefulSet` with a PVC for the save dir and a `LoadBalancer` (or NodePort + Cloudflare Spectrum) for the UDP ports. Marginal cost is basically the RAM reservation.

If you want zero ops, the managed hosts run roughly **$8–$15/mo for small servers, $20–$40/mo if you want guaranteed 8–10 GB RAM**. XGamingServer advertises $8.50/mo entry pricing on Ryzen 9 7950X hardware with NVMe and Workshop mod support, BisectHosting and GTXGaming sit in the middle, and LOW.MS is at the premium end at $40.40/mo with a guaranteed 10 GB RAM allocation. Most of them give you a Pterodactyl-ish panel, automated backups, and one-click Workshop mods, which is the actual value you're paying for over a raw VPS.

Hosting on your own desktop works but I'd skip it — Stationeers' server doesn't love sharing a machine with a client (NAT loopback weirdness, plus your 4090 box drawing idle power 24/7 isn't free either).

For a small private group I'd grab a Hetzner CX32 in Ashburn or Hillsboro, automate the SteamCMD update + restart in a `systemd` unit with `ExecStartPre`, and call it done — maybe $8–10/mo all-in. If you'd rather not babysit updates and mods, BisectHosting and GTXGaming have decent reputations for Stationeers specifically.

Want me to draft the systemd unit + SteamCMD update script, or the k8s manifests if you'd rather drop it on your existing cluster?

---

(END)

---

# Summary

Another mention of using Hetzner as cloud provider- I should look into this more when I have more time to invest in deploying my personal/protolab Kubernetes cluster... which still requires smoothing over the bootstrap step that AWS handles via EKS

That said, it still might be worthwhile to deploy on AWS since Karpenter would enable declarative node provisioning... Something for future me to figure out I suppose :)

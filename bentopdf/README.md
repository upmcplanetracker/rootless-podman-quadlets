# [BentoPDF](https://github.com/goodtab/bentopdf)

Stateless PDF conversion service – no volumes, no persistent data.

## Access
After starting the container, open: `http://<your-ip>:8084`

## Hardening
The Quadlet file includes a fully commented‑out hardening section.  
Because the service is stateless, it’s an excellent candidate for `ReadOnly=true`.  
To lock it down, uncomment the `ReadOnly` line **and all** the `Mount=type=tmpfs` lines.

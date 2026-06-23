# DNS — migrazione coop-goodjob.com → Cloudflare

Backup dei record DNS attuali (da website.com) + lista da ricreare su Cloudflare.
Data snapshot: 2026-06-23

## Obiettivo
Togliere i due record builder-proxy `149.56.47.18` che impediscono a GitHub Pages
di emettere il certificato HTTPS per coop-goodjob.com. Preservare la posta.

---

## Record DA RICREARE su Cloudflare

### A records — sito (GitHub Pages) — METTERE "DNS only" (nuvola GRIGIA, non arancione)
| Tipo | Nome | Valore           | Proxy        |
|------|------|------------------|--------------|
| A    | @    | 185.199.108.153  | DNS only 🌫️ |
| A    | @    | 185.199.109.153  | DNS only 🌫️ |
| A    | @    | 185.199.110.153  | DNS only 🌫️ |
| A    | @    | 185.199.111.153  | DNS only 🌫️ |
| A    | www  | 185.199.108.153  | DNS only 🌫️ |
| A    | www  | 185.199.109.153  | DNS only 🌫️ |
| A    | www  | 185.199.110.153  | DNS only 🌫️ |
| A    | www  | 185.199.111.153  | DNS only 🌫️ |

### Posta — PRESERVARE (copiare esattamente)
| Tipo  | Nome | Valore                       | Priorità |
|-------|------|------------------------------|----------|
| MX    | @    | mail2.sitemailserver.com     | 10       |
| MX    | @    | backup.sitemailserver.com    | 15       |
| CNAME | mail | mail2.sitemailserver.com     | DNS only |
| TXT   | @    | v=spf1 include:_hostingspf.website.com ~all | — |

---

## NON ricreare
- ❌ A @ 149.56.47.18      (builder proxy — è il problema)
- ❌ A www 149.56.47.18    (builder proxy — è il problema)

## Nameserver attuali (website.com) — verranno sostituiti da quelli Cloudflare
- dns1.website.com
- dns2.website.com
- dns21.website.com
- dns22.website.com

---

## Procedura
1. Crea account Cloudflare (free) + "Add a site" → coop-goodjob.com
2. Cloudflare scansiona i record esistenti: VERIFICA che ci siano MX/TXT/mail;
   CANCELLA gli A 149.56.47.18; AGGIUNGI/CORREGGI gli 8 A di GitHub (DNS only).
3. Cloudflare ti dà 2 nameserver (es. xxx.ns.cloudflare.com). Copiali.
4. Su website.com → DNS / dominio → cambia i nameserver con quelli Cloudflare.
5. Propagazione 1-24h. Quando il DNS mostra SOLO 185.199.x.153:
   - GitHub repo → Settings → Pages → riconferma custom domain → attende cert
   - spunta "Enforce HTTPS" appena disponibile.

## Verifica (dal terminale)
nslookup coop-goodjob.com 8.8.8.8        # deve mostrare SOLO 185.199.x.153
gh api repos/alutori-lang/GOODJOB/pages  # https_enforced deve diventare true

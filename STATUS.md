# STATUS — coop-goodjob.com (progress & resume notes)

Ultimo aggiornamento: 2026-06-23

Questo file traccia lo stato del progetto sito Good Jobs SRLS così che,
riaprendo il repository, sia chiaro a che punto siamo e cosa fare dopo.

---

## ✅ Fatto

- Sito costruito: `index.html` (single-file, responsive, italiano) + `images/`
  (forklift/warehouse, Unsplash) + `CNAME` (coop-goodjob.com).
- Live su GitHub Pages: https://alutori-lang.github.io/GOODJOB/
- GitHub repo: https://github.com/alutori-lang/GOODJOB (branch `main`).
- Contenuti reali dentro il sito: tel 335 146 1347; email
  alessia@coop-goodjob.com; P.IVA 01891350330, REA PC 357808; sedi Piacenza
  (Via Dei Ripalta 29, 29122 PC) + Salsomaggiore Terme (Via Don Minzoni 3,
  43039 PR). Servizi: agricoltura conto terzi, facchinaggio, movimentazione
  merci, pulizia aree agricole, supporto logistico, volantinaggio.
- Cloudflare: zona `coop-goodjob.com` creata (piano Free). Record ricreati e
  puliti (vedi `DNS-cloudflare-records.md`). Nameserver assegnati:
  **clint.ns.cloudflare.com / kiki.ns.cloudflare.com**.
  Zone ID `07f8a8b5fd76785228843f14a0a3d6fd`.

## ⏳ In sospeso — dominio custom HTTPS

Obiettivo: far funzionare https://coop-goodjob.com con lucchetto verde.

**Blocco:** il certificato HTTPS di GitHub Pages non viene emesso finché il DNS
contiene i due record A "Site Builder Proxy IP" (149.56.47.18) di website.com.
Questi record NON sono cancellabili dalla GUI di website.com (niente Edit|Delete),
e website.com NON espone un controllo per cambiare i nameserver (sono read-only
sia in DNS Manager che in Domain Summary).

**Soluzione in corso:** spostare il DNS su Cloudflare (zona già pronta). Manca solo
la delega dei nameserver, che richiede l'intervento del supporto website.com.

**Caso supporto aperto:** website.com case **#823601-269078702**
(account email vidardo100@gmail.com). Richiesta: (A) cambiare i NS a
clint/kiki.ns.cloudflare.com, OPPURE (B) cancellare i due record proxy +
scollegare il dominio dal site builder. Risposta entro max 2 giorni lavorativi
(mittente noreply@notifications.website.com — controllare spam).

## ▶️ Prossimi passi (quando website.com risponde / agisce)

1. Verifica delega:
   `nslookup -type=NS coop-goodjob.com a.gtld-servers.net`
   → deve mostrare clint/kiki.ns.cloudflare.com (Piano A) oppure i NS website.com
     ma senza i record proxy (Piano B).
2. Verifica risoluzione:
   `nslookup coop-goodjob.com 8.8.8.8`
   → deve mostrare SOLO 185.199.108-111.153 (nessun 149.56.47.18).
3. GitHub: Settings → Pages → riconferma custom domain → attendi certificato →
   spunta "Enforce HTTPS".
   Check API: `gh api repos/alutori-lang/GOODJOB/pages` → `https_enforced: true`.

## Note

- Se website.com sceglie il Piano B (cancella proxy, tiene i propri NS), la zona
  Cloudflare diventa superflua: lo zone file website.com ha già i record GitHub
  corretti. Va bene comunque.
- Email da preservare sempre: MX mail2.sitemailserver.com (10) +
  backup.sitemailserver.com (15); CNAME mail → mail2.sitemailserver.com;
  TXT SPF `v=spf1 include:_hostingspf.website.com ~all`.
- Su Cloudflare i record A di GitHub vanno "DNS only" (nuvola grigia), MAI proxati,
  altrimenti il certificato GitHub Pages si rompe.

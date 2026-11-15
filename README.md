# TRS – Tactical Racing Simulator

Corse automobilistiche **a turni**, 3D low poly in stile "plastico", di Stile Digitale.

- `index.html` è il gioco; `lib/` contiene Three.js 0.160 e `fonts/` il font Rubik: tutto incluso, nessuna richiesta a CDN esterni (requisito dei portali web). Nessun build, audio sintetizzato.
- Si apre in qualsiasi browser; pubblicato su Cloudflare Pages.
- Classifica online e multiplayer: Supabase (progetto `stiledigitale_games`, tabelle con prefisso `trs_`).

## Portali web
Il codice è pronto per i portali: tutto incluso nel pacchetto e un **adattatore** unico (`const Portal` in `index.html`) con le funzioni che i portali chiedono: caricamento finito, inizio/fine partita, pubblicità tra le gare, nome del giocatore. Sul sito non fanno niente; per ogni portale si riempiono solo quelle. Il portale si sceglie con `?portal=nome` o `window.TRS_PORTAL`. L'unico servizio esterno è Supabase, disattivabile con `Portal.online = false`.

## Versioni
- **v0.19** – stanze aperte: elenco in tempo reale delle stanze pubbliche (canale `trs-lobbies`), nome della stanza, posti 2–8, password (canale segreto), partenza automatica dopo 5 s quando la stanza è piena e tutti sono pronti, migliori giri nel multiplayer e record nella stanza; 15 secondi per turno online; nessuna mossa suggerita; in Pro nessun avviso di collisione.
- **v0.18** – telefono: pannelli di gioco compatti (tocco sul pannello del pilota per tutti i dati), pulsanti solo icona; Frecce Tricolori e scie spariscono al VIA!; multiplayer con auto CPU a 0 di base e solo Mini Monza disponibile (le altre piste con il lucchetto).
- **v0.17** – salvataggio della partita (solo gare non online): automatico a ogni turno del giocatore, "Salva ed esci" nel menu di pausa, "Riprendi la partita" nel menu principale; replay con telecamera sempre sull'auto protagonista; registro che non copre più il pulsante pausa.
- **v0.16** – multiplayer online: stanze con codice di 5 lettere, host arbitro, mosse in tempo reale con Supabase Realtime, fotografia ufficiale dopo ogni mossa, 30 secondi per turno, chi esce passa alla CPU. Con `?net=local` la rete è simulata tra schede dello stesso browser.
- **v0.15** – pronto per i portali: Three.js e font inclusi, adattatore portale, pausa e audio muto con la scheda nascosta o durante le pubblicità, frecce/spazio senza scorrimento della pagina.
- **v0.14** – classifica online (Supabase): miglior giro e gara completa per pista e classe, invio automatico a fine gara.
- **v0.13** – replay dei giocatori sempre visibili, tasto "Salta replay", regia TV sulle torrette, "CPU sta pensando…".

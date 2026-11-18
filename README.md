# TRS – Tactical Racing Simulator

Corse automobilistiche **a turni**, 3D low poly in stile "plastico", di Stile Digitale.

- `index.html` è il gioco; `lib/` contiene Three.js 0.160 e `fonts/` il font Rubik: tutto incluso, nessuna richiesta a CDN esterni (requisito dei portali web). Nessun build, audio sintetizzato.
- Si apre in qualsiasi browser; pubblicato su Cloudflare Pages.
- Classifica online e multiplayer: Supabase (progetto `stiledigitale_games`, tabelle con prefisso `trs_`).

## Portali web
Il codice è pronto per i portali: tutto incluso nel pacchetto e un **adattatore** unico (`const Portal` in `index.html`) con le funzioni che i portali chiedono: caricamento finito, inizio/fine partita, pubblicità tra le gare, nome del giocatore. Sul sito non fanno niente; per ogni portale si riempiono solo quelle. Il portale si sceglie con `?portal=nome` o `window.TRS_PORTAL`. L'unico servizio esterno è Supabase, disattivabile con `Portal.online = false`.

## Versioni
- **v0.29** – pulsante "📣 Condividi" a fine gara (con posizione, pista e miglior giro) e "Condividi il gioco" nel menu: sul telefono si apre la condivisione del sistema, sul computer un pannello con WhatsApp, Facebook, X, Telegram e Copia link. Solo sul sito: nascosti sui portali.
- **v0.28** – online ci si può ritirare dal menu di pausa (con conferma): l'auto esce di pista al proprio turno e si resta a guardare la gara; niente più sassi sulla ghiaia delle curve.
- **v0.27** – online: dopo il proprio giro di qualifica compare subito la schermata d'attesa (prima si restava sulla pista vuota vista dall'alto); la qualifica parte sull'ultimo tratto dritto e non dentro la curva; l'ordine dei turni segue la classifica ufficiale (regola della mezza auto); corsia box più larga con raccordi dolci e linee bianche ai bordi; tolta la riga tratteggiata al centro della pista; replay automatici anche online quando riguardano un giocatore; i tempi delle CPU non vanno più in classifica; la classifica online mostra solo i migliori giri, con filtro per livello.
- **v0.26.2** – traduzione mancante: "giro più veloce" quando arriva sul traguardo.
- **v0.26.1** – il pilota della Turismo sta a sinistra.
- **v0.26** – numeri di gara veri per pilota (7, 22, 12, 5, 31, 18, 44, 9, legati al colore), 4 livree diverse, vetri trasparenti con il pilota dentro la Turismo, casco del colore del pilota in F1; cordoli: l'auto vibra e si sente il brrr al passaggio, e sui cordoli c'è 40 cm di margine in più prima del testacoda.
- **v0.25** – auto rifatte: Turismo con passaruota, vetri, livrea a strisce, numero sulle porte, fari, splitter e cerchi a razze; Formula 1 con muso, pance, halo, casco, ali a due elementi e sospensioni. I pezzi di ogni auto sono uniti in pochi oggetti, quindi più dettagli senza rallentare.
- **v0.24** – musica di sottofondo generata dal gioco (menu e gara), folla più viva, nuovi effetti sonori (ghiaia, fischio, chiave pneumatica ai box, sorpasso, fanfara e applausi, ritiro, avviso del proprio turno); lingue italiano, inglese, francese, tedesco e spagnolo (Opzioni → Lingua; traduzioni in `lang/i18n.js`).
- **v0.23** – per ora si corre solo sulla Mini Monza in tutte le modalità: le altre due piste si vedono con il lucchetto ("Presto disponibile"); Ultra Pro disponibile anche online.
- **v0.22.1** – la scia colorata di ogni auto resta per tutta la gara (prima si cancellava dopo circa 10 mosse).
- **v0.22** – nuovo livello Ultra Pro: regole del Pro (nessun avviso, ritiro con danni gravi) con solo 5 secondi a mossa, anche in hot-seat e online; in classifica online conta come Pro.
- **v0.21.1** – alberi, cespugli e oggetti sempre dentro la base del plastico: niente più chiome che sporgono dal bordo.
- **v0.21** – partenza: griglia in ordine, casuale o qualifica con giro lanciato (anche online, tutti insieme); commissari con bandiera gialla (uscite e contatti) e blu (doppiaggi); multiplayer diviso in "Gioca con gli amici" (codice di invito) e "Stanze online" (pubbliche e private con password); chi si disconnette va fuori pista e si ritira al suo turno.
- **v0.20** – multiplayer: chi lascia scadere il tempo del turno resta fermo; al secondo turno di fila senza muovere viene espulso dalla gara.
- **v0.19** – stanze aperte: elenco in tempo reale delle stanze pubbliche (canale `trs-lobbies`), nome della stanza, posti 2–8, password (canale segreto), partenza automatica dopo 5 s quando la stanza è piena e tutti sono pronti, migliori giri nel multiplayer e record nella stanza; 15 secondi per turno online; nessuna mossa suggerita; in Pro nessun avviso di collisione.
- **v0.18** – telefono: pannelli di gioco compatti (tocco sul pannello del pilota per tutti i dati), pulsanti solo icona; Frecce Tricolori e scie spariscono al VIA!; multiplayer con auto CPU a 0 di base e solo Mini Monza disponibile (le altre piste con il lucchetto).
- **v0.17** – salvataggio della partita (solo gare non online): automatico a ogni turno del giocatore, "Salva ed esci" nel menu di pausa, "Riprendi la partita" nel menu principale; replay con telecamera sempre sull'auto protagonista; registro che non copre più il pulsante pausa.
- **v0.16** – multiplayer online: stanze con codice di 5 lettere, host arbitro, mosse in tempo reale con Supabase Realtime, fotografia ufficiale dopo ogni mossa, 30 secondi per turno, chi esce passa alla CPU. Con `?net=local` la rete è simulata tra schede dello stesso browser.
- **v0.15** – pronto per i portali: Three.js e font inclusi, adattatore portale, pausa e audio muto con la scheda nascosta o durante le pubblicità, frecce/spazio senza scorrimento della pagina.
- **v0.14** – classifica online (Supabase): miglior giro e gara completa per pista e classe, invio automatico a fine gara.
- **v0.13** – replay dei giocatori sempre visibili, tasto "Salta replay", regia TV sulle torrette, "CPU sta pensando…".

# Tactical Racing Simulator — scheda del progetto

Gioco di corse automobilistiche **a turni**, 3D low poly in stile "plastico", ideato da Christian.
Versione attuale: **v0.28** (15 novembre 2026). `index.html` più le cartelle `lib/` (Three.js 0.160, client Supabase), `fonts/` (Rubik) e `lang/` (traduzioni): tutto incluso, nessun build, audio e musica sintetizzati dal gioco (nessun file audio).
**Copia di riferimento:** repository GitHub **stiledigitale/trs** (branch `main`), pubblicato in automatico da Cloudflare Pages su **https://tactical-racing.pages.dev** (pubblico per chi ha il link). Ogni modifica si fa sul repository e il sito si aggiorna da solo. Questa scheda sta anche nel repository (`docs/scheda-progetto.md`) e nel progetto Claude "01_SVILUPPO GIOCHI". Il vecchio artifact "Tactical Racing Simulator — prototipo" è fermo alla v0.13 e non va più aggiornato.
Si lavora sempre su questo account Claude; i servizi (GitHub, Cloudflare, Supabase) sono quelli di **Stile Digitale**. Regole comuni per i portali: documento `claude/standard-portali-web.md` del progetto.

---

## Regola base del turno
- A ogni turno il giocatore trascina un **bersaglio** dentro la **cornice** davanti all'auto. La traiettoria è una curva di Bézier quadratica tangente alla direzione attuale, quindi conta l'inerzia.
- **La distanza si misura dal muso dell'auto.** Il punto scelto è dove arriverà il muso a fine mossa.
- La lunghezza della mossa è la nuova velocità. **1 turno = 3 secondi di gara**, così i km/h restano realistici.
- **Ordine dei turni:** a ogni round muove per primo chi è in testa, secondo la **classifica ufficiale** (dalla v0.27): la posizione cambia solo quando mezza auto ha superato il muso dell'avversaria, quindi turni, frecce ▲▼ e messaggio "SORPASSO!" dicono tutti la stessa cosa (prima l'ordine dei turni cambiava già con le auto appaiate).
- **Nessuna mossa suggerita** (dalla v0.19, a tutti i livelli): il bersaglio parte dritto alla velocità attuale.

## La cornice (decisa con Christian, molto importante)
- **Forma a scudo** (dal disegno di Christian): base stretta = frenata forte (cerchio d'aderenza), fianchi che si aprono, larghezza massima a circa 2/3, cima piatta e appena più stretta. È uguale in tutte le marce.
- **Posizione:** la velocità del turno precedente non sta al centro della cornice ma un po' più in alto. La cornice è "arretrata": circa il 58% della profondità è per frenare, il resto per accelerare.
- **Proporzioni:** profondità ≈ larghezza (`Dp = 2,4 × latEff`). Non deve mai diventare una striscia lunga e stretta.
- **Spazio laterale:** limitato a `lat` metri per turno. Le **cornici lontane**, cioè ad alta velocità, si restringono fino a −35% (`latEff`), per avere meno correzione e più studio della mossa; in curva lenta restano ampie.
- **Accelerazione e frenata:** l'accelerazione cala vicino alla velocità massima; la frenata è circa 0,7–1 g.
- **Parametri attuali:** `DIST = 3.0`, `LAT_SCALE = 2.8`, `MOVE_SCALE = 1.6`, frenata = `brake × 0.72 × DIST`, `lat = grip × 0.4 × LAT_SCALE`, cap sterzo 0,85 rad.
- **Turni a giro sulla MiniMonza:** circa 21–32 in Turismo e circa 16–22 in Formula 1. Christian vuole pochi turni a giro, perché non deve diventare noioso. Con 4 auto un round dura 7–8 secondi reali.

## Partenza (v0.21)
- Si sceglie nella gara da soli (**Partenza**) e nella stanza online (impostazione dell'host):
  - **Griglia in ordine:** l'ordine dei piloti (online: host, poi gli ospiti in ordine di arrivo, poi le CPU).
  - **Griglia casuale.**
  - **Qualifica (giro lanciato):** ognuno da solo in pista, parte sull'**ultimo tratto dritto prima del traguardo** (`qualiBack()`, al massimo 90 m: sulla Mini Monza il dritto è corto, circa 70 m, e partire 90 m indietro voleva dire partire dentro l'ultima curva e uscire di pista alla prima mossa) già lanciato (45% della velocità massima); il cronometro parte sul traguardo e si ferma al passaggio successivo; la griglia va dal miglior tempo. Chi non fa il tempo parte in fondo. Le CPU fanno il loro giro "a mente", senza animazione. Nella gara da soli i giocatori umani girano uno dopo l'altro, poi compare la griglia con "Via alla gara". La camera inquadra l'auto anche con la camera automatica spenta. **Online** tutti fanno il giro nello stesso momento (15 s per mossa); appena uno finisce il suo giro vede la schermata con i tempi di tutti (v0.27: prima restava sulla pista vuota vista dall'alto, sembrava che il gioco si fosse rotto), l'host fa girare le CPU, raccoglie i tempi (al massimo 2 minuti e mezzo), mostra la griglia a tutti e dopo pochi secondi parte la gara. La rivincita rifà la qualifica.
- I colori delle auto restano dei piloti anche quando cambia la posizione in griglia.

## Regole di gara
- **Uscita di pista:** testacoda, si riparte da fermi e si salta un turno. Il limite è il centro dell'auto a `HALF + 0,6` m dal centro pista; **sui cordoli (v0.26) 40 cm in più** (`KERB_EXTRA`): la ruota esterna può stare a cavallo tra cordolo ed erba, circa mezza ruota fuori, senza testacoda. Sui tratti senza cordolo il limite non cambia.
- **Cordoli (v0.26):** quando l'auto ci passa sopra "martella": sobbalzi rapidi, leggera inclinazione e il brrr del cordolo (onda quadra bassa pulsata). I cordoli ci sono nelle curve (`KERB`, curvatura > 1/70).
- **Contatto:** chi tampona si ferma dietro e perde metà della velocità; chi viene colpito perde il 30%. Le collisioni usano rettangoli orientati.
- **Sorpasso:** conta solo quando **mezza auto ha superato il muso dell'avversaria**. Su questa regola si basano la classifica ufficiale, la scritta "SORPASSO!" e le frecce ▲▼.
- **Scia:** chi chiude il turno subito dietro un avversario, sulla stessa linea, ha più accelerazione al turno dopo (💨).
- **Commissari:** se un'auto resta ferma e girata contromano, o non avanza per 2 turni (3 per un umano), viene rimessa in pista e perde un turno.
- **Bandiere dei commissari (v0.21):** alle postazioni dei commissari (tuta arancione, prima delle curve) sventola la **bandiera gialla** quando c'è un'uscita di pista o un contatto in quel tratto, e la **bandiera blu** vicino all'auto che sta per essere doppiata (il leader è a meno di circa 90 m dietro di lei con un giro in più); il giocatore doppiato legge nel registro "Bandiera blu: lascialo passare". Sono segnali visivi: non cambiano le regole.
- **Partenza da fermi:** semaforo a 5 luci con attesa casuale; le Frecce Tricolori passano nel menu e durante il semaforo e **al VIA! spariscono** con le loro scie.
- **Livelli di guida** (per gli umani):
  - **Facile:** traiettoria colorata (verde pulita, arancione contatto, rossa uscita) e avviso **giallo** per le traiettorie pulite ma senza scampo al turno dopo; danni dimezzati, massimo 60%.
  - **Medio:** nessun avviso di uscita di pista (resta quello di contatto); danni pieni ma niente ritiro.
  - **Pro:** **nessun avviso**, né di uscita né di contatto: traiettoria sempre blu. 15 secondi per turno, ritiro con danni ≥ 75% ("se rompi sei fuori").
  - **Ultra Pro (v0.22):** come il Pro ma con **5 secondi a mossa** (anche in hot-seat e online: l'host lo sceglie nella stanza); allo scadere parte la mossa impostata in quel momento. Nella classifica online i tempi contano come Pro.
  - Idea scartata da Christian: turni simultanei (tutti muovono insieme). Resta a turni alterni.
- **Classi:**
  - **Turismo:** assetti Bilanciata, Scatto, Aderenza, Velocità.
  - **Formula 1:** assetti Equilibrata, Trazione, Alto carico, Motore. Monoposto low poly, danni +30%. La velocità massima è circa il 60% più alta delle Turismo; il "27% più veloce" indicato in passato si riferisce probabilmente al tempo sul giro (da verificare).
- **Piloti:** fino a 8, umani in hot-seat o CPU; online fino a 8, ognuno dal proprio computer. **Giri:** da 1 a 10.

## Gomme, carburante, danni, box
- **Usura e consumi:** Off, Realistica o Accelerata ×2,5. In Realistica le gomme durano circa 5 giri e il serbatoio circa 6–6,5 giri (da verificare in gara). In qualifica niente usura.
- **Gomme consumate:** la cornice si stringe fino a −38% e la frenata cala.
- **Carburante:** il serbatoio pieno pesa; a secco l'auto rallenta per inerzia e si ritira (in Facile il carburante non scende sotto il 2%).
- **Danni** (da contatti e uscite): riducono accelerazione, velocità massima e sterzo. Oltre il 40% l'auto fuma, oltre il 50% perde le ali.
- **Box:** si prenota con 📥 Box (tasto B) e si entra al passaggio sul traguardo. In hot-seat la prenotazione vale per il giocatore di turno. Costo in turni: 1 di base, +1 per le gomme, +1–2 per la benzina, +1 ogni 35% di danni. La CPU decide da sola quando fermarsi. Il tasto compare solo con più di 1 giro.

## Salvataggio della partita (v0.17, solo gare non online)
- A ogni inizio turno il gioco prende la fotografia dello stato; **all'inizio di ogni turno di un giocatore umano la salva da sola** nel browser (localStorage, chiave `trs-partita`).
- Menu di pausa → **"💾 Salva ed esci"**. Menu principale → **"Riprendi la partita"** (con pista, round, data e ora): riparte esattamente dal turno salvato, con auto, gomme, carburante, danni, box e la registrazione per il replay finale.
- A gara finita il salvataggio si cancella. Non c'è online né durante la qualifica.

## CPU
- Ricerca a 2 mosse (beam), con verifica di poter sempre frenare in tempo (profondità 4) e margine di sicurezza dal bordo.
- Segue una traiettoria ideale (esterno, corda, esterno), evita il controsterzo sui rettilinei e nei primi round sta disciplinata in griglia.
- **Difficoltà:** errori programmati (staccata lunga, traiettoria sbagliata, avversario non visto; più frequenti sotto pressione): Facile 5%, Normale 2,5%, Difficile 1,2%.
- **Manovra di emergenza** quando non ha mosse sicure: non resta mai ferma per sempre.
- Durante il suo turno il pannello mostra **"CPU sta pensando…"** e la traiettoria compare solo a decisione presa. Nella classifica le CPU hanno la scritta grigia "CPU" (niente icone robot: a Christian non piacciono).

## Piste
- **Mini Monza:** ricalcata dalla piantina "MiniMonza" di Christian, a 0,3 m per pixel. Giro di circa 825 m, corsia box larga circa 10 m con raccordi lunghi e dolci (smoothstep sui due bordi), linee bianche su entrambi i bordi per tutta la lunghezza e muretto che accompagna anche i raccordi (v0.27), ruderi della sopraelevata, Frecce Tricolori.
- **Anello di prova:** corto (circa 538 m), con prati, lago, fattoria e pale eoliche.
- **Valle delle Dolomiti:** lunga (circa 1105 m), con abeti e rocce innevate.
- **Per ora si corre solo sulla Mini Monza, in tutte le modalità (v0.23):** le altre due piste si vedono con il lucchetto, grigie e con "Presto disponibile" nella gara da soli, "🔒 … · presto" nella stanza online. Si sbloccano cambiando `TRACKS_OPEN` nel codice (e le opzioni di `#lbTrack`).

## Grafica e atmosfera (stile "plastico")
- **Auto (v0.25):** Turismo con carrozzeria a profilo estruso, passaruota, vetri, montante, livrea a strisce (scura sulle auto chiare), tondo del numero sulle porte, minigonne, specchietti, fari e fanali, griglia, splitter, estrattore, scarico, alettone; Formula 1 con muso, abitacolo, cofano con presa d'aria e pinna, pance con prese, fondo, halo, casco con visiera, ala anteriore e posteriore a due elementi con paratie, beam wing, sospensioni. Ruote con cerchio e razze (si vede che girano). **v0.26:** numero di gara vero sulle porte (Turismo) e su cofano motore e muso (F1), legato al colore del pilota: rosso 7, blu 22, giallo 12, verde 5, arancione 31, viola 18, turchese 44, rosa 9; quattro livree a seconda del numero (strisce centrali, fascia laterale con tetto a contrasto, doppie strisce, tetto e cofano a contrasto); Turismo con vetri trasparenti e pilota dentro, **seduto a sinistra** (casco, tuta, sedile, volante); casco F1 nel colore del pilota schiarito, con banda bianca. I pezzi sono uniti in pochi oggetti con i colori sui vertici (`mergeCarParts`, usa `lib/addons/utils/BufferGeometryUtils.js`); alettoni e ruote restano separati perché cadono con i danni (oltre il 50%) e girano.
- Niente riga tratteggiata al centro della pista (tolta nella v0.27). Niente sassi sulla ghiaia delle curve (v0.28: i sassi stanno ad almeno `HALF + 11` m dal centro pista, la ghiaia arriva a `HALF + 10`).
- Base di terra marrone, cielo azzurro pulito, erba brillante, alberi tondi. **Niente deve sporgere dal bordo del plastico** (regola di Christian): alberi, rocce e oggetti stanno dentro la base, con margine per la chioma.
- Muretti con striscioni, reti, gomme rosse e bianche, ghiaia, tribune coperte, box con tetto arancione e torre, postazioni dei commissari con il commissario e la bandiera.
- Case, palazzi, strada perimetrale, parcheggi, torri faro, mongolfiere, folla animata che esulta.
- Segni neri sull'asfalto in frenata e testacoda, fumo delle gomme poligonale e trasparente, scoppiettate dallo scarico.

## Replay e telecamere TV
- **Torrette TV:** ogni telecamera da bordo pista ha la sua postazione (impalcatura alta circa 10 m), a circa 30–40 m dalla pista, scelta **prima** di alberi, rocce, lago e fattoria con una **verifica preliminare** (niente tribune, box, edifici o cartelloni davanti o ai lati) e visuale verso la pista tenuta libera; controllo finale sulla visuale reale. Circa 17 telecamere sulla Mini Monza, 23 sull'Anello di prova, 28 sulle Dolomiti.
- **Regia:** torretta che guarda il tratto dove si trova l'auto, preferendo quella poco più avanti; se nessuna la vede si passa all'inseguimento. Il replay parte già inquadrando l'auto protagonista.
- **Replay automatici (highlight):** quelli che coinvolgono un **giocatore umano** partono **sempre**; quelli solo tra CPU: mai prima del round 3, almeno 4 round tra uno e l'altro, solo se riguardano il podio o la vittoria. In **qualifica** non ci sono replay automatici; **online** (dalla v0.27) ci sono solo quando riguardano un giocatore e mai due round di fila, così nessuno aspetta troppo.
- **Tasto "⏭ Salta replay"**; funzionano anche Spazio e il clic sulla scena. **Replay completo** a fine gara, con la scritta SORPASSO!.

## Classifica online (v0.14)
- Supabase, progetto **stiledigitale_games**, tabella **`trs_lap_records`**: lettura e inserimento pubblici, nessuna modifica o cancellazione, valori assurdi bloccati (tempo tra 5 s e 1 ora, nome 1–13 caratteri, piste e classi ammesse).
- A fine gara il gioco invia, per ogni pilota umano, il **miglior giro** (dalla v0.27 solo quello: il tempo della gara completa non si invia più), più assetto, livello e versione; sotto i risultati mostra la posizione. Online ognuno invia solo i propri tempi. I giri di qualifica non vengono inviati.
- **Mai i tempi delle CPU (v0.27):** i nomi che cominciano per "CPU" sono esclusi dall'invio, e nella schermata Nuova gara cambiando una riga da CPU a Umano il nome "CPU …" viene sostituito con "Giocatore N" (era così che i tempi delle CPU finivano in classifica).
- Menu → **Classifica online**: solo **migliori giri**, top 20 per **pista, classe e livello** (Tutti/Facile/Medio/Pro; Ultra Pro conta come Pro), un tempo per pilota. In "Stanze online" c'è il riquadro **"Migliori giri · Mini Monza"** (top 5) e nella stanza il **record della pista**.
- Opzione "Invia i tempi alla classifica online" (Sì di base). Limite noto: un tempo falso inviato a mano è possibile; i vincoli bloccano solo i valori assurdi.
- **Tempi di prova presenti:** "Christian" 1:04.37 e "Marco" 1:07.54 (Mini Monza, Turismo), dalla prova automatica del 15/11. Christian ha deciso di lasciarli durante i test: **da cancellare prima del lancio**.

## Multiplayer online (v0.16–v0.21)
Due voci separate nel menu principale (v0.21):
- **"Gioca con gli amici"** (codice di invito): si crea la stanza e si riceve un codice di 5 lettere (senza I e O) da mandare agli amici, che lo scrivono in "Hai un codice?". Fino a 8 posti, nessuna password, la stanza non compare in nessun elenco.
- **"Stanze online":** elenco in tempo reale delle stanze aperte (nome, creatore, pista, classe, giri, livello, posti, 🔒) con "Entra" con un tocco; si crea una stanza scegliendo **nome**, **posti da 2 a 8** e **password**: senza password la stanza è **pubblica**, con la password è **privata** (compare con il lucchetto e serve la password per entrare).
- **Password:** non viaggia mai in rete: la stanza vera ha un indirizzo segreto (`trs-room-CODICE-impronta`), calcolato da codice + password (maiuscole e minuscole indifferenti). Con la password sbagliata: "Password errata".
- **Lobby:** piloti con colore, assetto e stato "pronto"; l'host sceglie pista (online solo Mini Monza), classe, giri, livello, usura, **partenza** (ordine, casuale, qualifica) e auto CPU (0 di base, fino a 6); gli ospiti scelgono solo il proprio assetto. Si parte con **Via!** dell'host quando tutti sono pronti, oppure **da soli dopo 5 secondi** quando la stanza è piena e tutti sono pronti. Non si entra in una stanza già in gara o piena.
- **Bacheca stanze:** canale Supabase Realtime comune `trs-lobbies` (presence); ogni stanza di "Stanze online" in attesa vi annuncia i suoi dati e sparisce da sola quando l'host esce, chiude la pagina o la gara parte.
- **Come funziona la rete:** Supabase Realtime (canale della stanza, presence per la lobby, broadcast per i messaggi). Ognuno simula la gara sul proprio computer; viaggiano solo le mosse (`mv`) e tutti le eseguono insieme. L'host decide le mosse delle CPU e dopo ogni mossa manda la **fotografia ufficiale** (`fix`) di tutte le auto e del turno: gli ospiti la applicano prima di continuare, quindi non possono andare fuori sincronia.
- **Tempo per turno online: 15 secondi a tutti i livelli.** Chi lascia scadere il tempo **resta fermo** (velocità zero); al **secondo turno di fila senza muovere viene espulso** (v0.20). Lo stesso vale per chi non risponde (dopo 23 s l'host lo considera fermo).
- **Ritiro volontario (v0.28):** nel menu di pausa, solo online, c'è "🏳 Ritirati dalla gara"; dopo la conferma ("Sì, mi ritiro" / "Annulla") parte il messaggio `quit` a tutti, l'auto viene trattata come quella di un disconnesso (al suo turno l'host la parcheggia fuori pista e si ritira, motivo "si è ritirato dalla gara") e chi si ritira resta collegato a guardare, con la camera che passa a "Segui i turni".
- **Disconnessioni (v0.21):** chi resta assente per più di 5 secondi è "disconnesso"; al suo turno successivo **l'auto viene spostata fuori pista e resta lì, ritirata**. Se esce l'host la partita finisce per tutti.
- **Online:** la pausa apre il menu ma non ferma gli altri; il box si prenota in rete; niente replay automatici. A fine gara **"Torna alla stanza"** riporta tutti nella lobby per la rivincita.
- Le mosse si chiudono anche con la scheda nascosta (il browser smette di disegnare): un host in secondo piano non blocca gli altri.
- **Prove:** con `?net=local` nell'indirizzo la rete è simulata tra le schede dello stesso browser. Verificati il 15/11: sincronizzazione al centesimo, disconnessioni, vero Supabase dal sito, stanze pubbliche e private, partenza automatica, espulsione dopo due turni, qualifica online con griglia uguale per tutti, auto del disconnesso fuori pista. **Prova di Christian:** PC fisso host, Surface e smartphone come ospiti ("hai realizzato il sogno della mia vita").

## Telefono (v0.18)
- Su schermi fino a 760 px di larghezza o 520 px di altezza: pannello del pilota compatto (nome, giro, velocità, stato; toccandolo **"▾ dati"** si vedono tutti i dati), classifica piccola, minimappa e registro nascosti, pulsanti solo icona (☰, 📥, 🎥, 🎯, "Conferma"), semaforo e scritte più piccoli, schermate del menu scorrevoli.

## Pronto per i portali web (v0.15)
- Three.js, client Supabase e font inclusi nel pacchetto, nessun CDN esterno; unico servizio esterno Supabase, disattivabile.
- **Adattatore `const Portal`** nel codice: `init`, `loadingFinished`, `gameplayStart`/`gameplayStop`, `commercialBreak` (pubblicità prima di ogni gara dopo la prima), `playerName`, `online`. Sul sito sono vuote; il portale si sceglie con `?portal=nome` o `window.TRS_PORTAL`.
- Audio muto e gara in pausa con la scheda nascosta e durante le pubblicità (online solo audio muto); frecce e spazio non scorrono la pagina; niente menu del tasto destro sul gioco.

## Audio (v0.24)
- **Musica** generata dal gioco con Web Audio, niente file: batteria, basso, accordi, arpeggio e melodia. Due atmosfere: **menu** (92 bpm, rilassata) e **gara** (126 bpm, più spinta e più bassa di volume per sentire motori e folla). Cambia giro di accordi ogni 8 battute e melodia ogni 16; il passaggio menu ↔ gara avviene a inizio battuta. Lo scheduler anticipa di 0,4 s e, se la pagina si blocca, salta avanti tenendo il ritmo. Opzione **Musica** Sì/No.
- **Folla:** brusio a tre bande con oscillazioni lente, più forte con l'eccitazione della gara (sorpassi, arrivo).
- **Effetti:** motore, sgommate, contatti, scoppiettate, semaforo, giro, Frecce Tricolori, più (v0.24) ghiaia nelle uscite, fischio del commissario quando esce un umano, chiave pneumatica ai box, "whoosh" sui sorpassi, fanfara e applausi alla vittoria, suono del ritiro, avviso "tocca a te" in hot-seat e online.

## Lingue (v0.24)
- Italiano, inglese, francese, tedesco, spagnolo: **Opzioni → Lingua**; al primo avvio si sceglie quella del browser (inglese se non è tra le cinque).
- Il gioco resta scritto in italiano: un traduttore interno guarda ogni scritta che compare sullo schermo (menu, HUD, registro di gara, stanze, classifica) e la sostituisce con il dizionario `lang/i18n.js` (chiave = testo italiano, `{}` = parte variabile). La pagina "Come si gioca" ha il testo intero per lingua. Per aggiungere una scritta nuova basta aggiungere la riga al dizionario; con `window.TRS_I18N_DEBUG = true` il gioco raccoglie in `window.__missing` le scritte senza traduzione.
- Restano in italiano i nomi propri: piloti CPU, sponsor sui cartelloni, "Mini Monza".

## Interfaccia
- Layout compatto fino a 900 px di larghezza; il testo d'aiuto della barra comandi sparisce sotto i 1100 px.
- Il registro delle azioni non intercetta i clic e, sotto i 1300 px, sta sopra la barra dei comandi.

## Storico versioni
- **v0.10:** versione di partenza importata da un altro account.
- **v0.11:** tasto Box corretto in hot-seat; giri da 1 a 10; pulizia del codice.
- **v0.12:** avviso giallo delle traiettorie senza scampo; "CPU sta pensando"; replay tra CPU meno frequenti; torrette TV con verifica preliminare; scie delle Frecce più piccole; layout per schermi stretti.
- **v0.13:** replay dei giocatori umani sempre visibili; tasto Salta replay; regia che sceglie la torretta giusta; niente icona robot.
- **v0.14:** classifica online con Supabase; primo caricamento su GitHub e Cloudflare Pages.
- **v0.15:** base standard per i portali web (librerie incluse, adattatore Portal, pausa e audio in background).
- **v0.16:** multiplayer online con stanze a codice; le mosse si chiudono anche con la scheda nascosta.
- **v0.17:** salvataggio e ripresa della partita (non online); replay sempre sull'auto protagonista; registro che non copre il pulsante pausa.
- **v0.18:** versione per telefono; Frecce Tricolori via al VIA!; online auto CPU a 0 e solo Mini Monza.
- **v0.19:** stanze aperte in elenco, nome stanza, posti, password, partenza automatica, migliori giri nel multiplayer; online 15 s per turno; nessuna mossa suggerita; in Pro nessun avviso di collisione.
- **v0.20:** online chi non muove resta fermo; espulso dopo due turni di fila senza muovere.
- **v0.28:** ritiro volontario online dal menu di pausa; niente sassi sulla ghiaia.
- **v0.27:** schermata d'attesa dopo il proprio giro di qualifica online, via della qualifica sul dritto, ordine dei turni come la classifica ufficiale, corsia box rifatta, niente riga centrale, replay online, niente tempi CPU in classifica, classifica solo migliori giri con filtro livello.
- **v0.26.2:** traduzione del "giro più veloce" sul traguardo.
- **v0.26.1:** pilota della Turismo spostato a sinistra.
- **v0.26:** numeri di gara, livree, pilota visibile, casco colorato; cordoli che vibrano con il suono e 40 cm di margine in più prima del testacoda.
- **v0.25:** auto rifatte (Turismo e Formula 1 più dettagliate, pezzi uniti per non rallentare).
- **v0.24:** musica, folla e nuovi effetti sonori; 5 lingue.
- **v0.23:** solo Mini Monza in tutte le modalità (altre piste con il lucchetto); Ultra Pro verificato online.
- **v0.22.1:** la scia colorata delle auto resta per tutta la gara, su tutti i giri (prima si cancellava dopo circa 10 mosse); si può spegnere in Opzioni → Scie delle auto.
- **v0.22:** livello Ultra Pro con 5 secondi a mossa.
- **v0.21.1:** alberi e oggetti sempre dentro il bordo del plastico (prima i boschetti vicino al bordo sporgevano).
- **v0.21:** partenza con griglia in ordine, casuale o qualifica (anche online); commissari con bandiera gialla e blu; menu "Gioca con gli amici" e "Stanze online" separati; il disconnesso va fuori pista e si ritira.

## Da verificare giocando
- L'avviso giallo delle traiettorie senza scampo (Facile): se compare al momento giusto e non troppo spesso (al via, con il bersaglio dritto, può comparire subito).
- I replay dei giocatori umani: se a ogni testacoda diventano troppi in una gara lunga.
- La scia: potrebbe essere troppo forte.
- La CPU in Difficile su PC lenti o telefono: possibili scatti mentre pensa (anche durante la qualifica, quando le CPU girano "a mente").
- Durata reale del serbatoio (6 o 6,5 giri) e significato del "+27%" della Formula 1.
- I 15 secondi per turno online dal telefono.
- Le bandiere: se sono abbastanza visibili dalla vista di gara.
- La musica: volume rispetto a motori e folla, e se stanca dopo molte gare.
- Le traduzioni: qualche frase del registro di gara può suonare meno naturale, perché alcune frasi sono tradotte a pezzi.

## Pubblicazione su itch.io (16/11)
- Pagina creata da Christian sull'account itch.io **stiledigitale**, per ora in Draft. L'indirizzo era `tactical-raging-simulator` (errore: "raging"): da correggere in `tactical-racing-simulator` prima di renderla pubblica.
- Si carica lo ZIP con `index.html`, `lib/`, `fonts/`, `lang/` (Kind of project: HTML, "This file will be played in the browser", viewport 1280×720, Mobile friendly orizzontale, Fullscreen button).
- Copertina 630×500 / 1260×1000 (gruppo di Turismo in curva con il titolo, scritta "TURN-BASED RACING · ONLINE MULTIPLAYER") e 6 screenshot 1920×1080 in inglese (menu, cornice di mira, F1 sotto il portale, replay TV, risultati, stanza multiplayer), preparati il 16/11.
- Short Description massimo 120 caratteri.

## Prossimi passi concordati
1. Rifiniture del multiplayer dopo altre prove (per esempio invito con link, messaggi rapidi, host che passa a un altro giocatore se esce).
2. Sbloccare le altre piste (gara da soli e online) quando sono pronte; pulizia della classifica dai tempi di prova prima del lancio.
3. Scelta del portale e adattamento dell'adattatore `Portal` (SDK, pubblicità, nome del giocatore).
4. In seguito: editor dei tracciati (anche importando una piantina, come per la MiniMonza) ed editor delle vetture.

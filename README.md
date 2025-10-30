# OverTheWire **Bandit** — Walkthrough & Appunti

[![Stato](https://img.shields.io/badge/Completato-Livelli_0%E2%86%9233-brightgreen)](#)
[![Sistema](https://img.shields.io/badge/OS-Ubuntu_22.04_LTS-lightgrey)](#)
[![Ultimo aggiornamento](https://img.shields.io/badge/Aggiornamento-2025--10--29-blue)](#)

> Percorso riproducibile attraverso **OverTheWire — Bandit**.  
> Questo README documenta l’approccio, i comandi e le tecniche usate per risolvere i livelli.  
> Tutte le **password sono oscurate** in conformità con la policy ufficiale di OverTheWire.

---

## 🧭 Introduzione
Questa guida raccoglie le soluzioni per i livelli di **Bandit (0 → 33)**, con:
- comandi e workflow utilizzati;
- spiegazione dei passaggi logici;
- esempi di snippet testabili su Linux (**Ubuntu 22.04 LTS**).

---

## 🧰 Tools — principali comandi e servizi

### 🌐 Rete
`ssh`, `scp`, `nc`, `ncat`, `telnet`, `openssl s_client`, `socat`, `nmap`, `ss`, `netstat`

### 📁 Filesystem & gestione file
`ls`, `cd`, `pwd`, `find`, `file`, `stat`, `du`, `mkdir`, `mktemp`, `cp`, `mv`, `rm`, `touch`, `chmod`, `cat`, `diff`

### 🧾 Testo & stream processing
`grep`, `egrep`, `awk`, `sed`, `cut`, `sort`, `uniq`, `tr`, `wc`, `printf`, `echo`, `tail`, `head`

### 🔡 Stringhe / binario
`strings`, `xxd`, `base64`

### 🔐 Hash & checksum
`md5sum`

### 📦 Compressione e archivi
`tar`, `gzip`, `bzip2`, `gunzip`, `bunzip2`

### ✏️ Editor & shell
`more`, `vim`, `bash`, `sh`

### ⚙️ Processi & job control
`jobs`, `bg`, `fg`, `&`, `timeout`

### 🌀 Version control (Git)
`git clone`, `git log`, `git show`, `git tag`, `git branch`, `git checkout`, `git add -f`, `git commit`, `git push`, `git status`

---

# 🧩 Percorso dei Livelli
Ogni livello introduce un nuovo concetto di sicurezza o un comando Linux utile alla progressione.  
Le password sono **oscurate** per rispetto della policy di OverTheWire.  
Tutti gli esempi sono **riproducibili su Ubuntu 22.04 LTS**.

---

## ✅ Formato dei contenuti
Ogni livello seguente è strutturato con:

1. **🎯 Obiettivo** — descrizione breve del livello.  
2. **💻 Comandi principali** — snippet eseguibili.  
3. **🧠 Spiegazione** — analisi del funzionamento.  
4. **🪄 Takeaway** — concetto o tecnica da ricordare.

---

## 🔹 Livello 0 → 1

### 🎯 Obiettivo
Effettuare il primo accesso via **SSH** al server `bandit.labs.overthewire.org` (porta `2220`) con le credenziali fornite, e trovare la password per il livello successivo nel file `readme`.

### 💻 Comandi principali
```bash
# Connessione al server (porta 2220)
ssh bandit0@bandit.labs.overthewire.org -p 2220

# Una volta loggati, leggere il file readme
cat readme
# → Estrarre la password per il livello 1 (qui oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
Questo primo livello introduce il concetto di **accesso remoto sicuro tramite SSH** e mostra la struttura tipica del gioco:  
1️⃣ collegarsi al server,  
2️⃣ esplorare la home,  
3️⃣ trovare il file con la password per il livello successivo.

### 🪄 Takeaway
- Ricorda sempre di specificare la **porta corretta** (`-p 2220`).  
- Il flusso di gioco standard: *login → leggere file → accedere al livello successivo*.  
- Comandi fondamentali: `ls`, `cd`, `cat`, `file`, `find`, `du`.

---

## 🔹 Livello 1 → 2

### 🎯 Obiettivo
Recuperare la password per il livello 2 leggendo un file chiamato `readme` nella home dell’utente, imparando a muoversi nel filesystem e ad utilizzare i comandi base.

### 💻 Comandi principali
```bash
# Dopo l'accesso come bandit0
ls -la
cd ~
cat readme
# → Password per bandit1 (oscurata)
# [REDACTED]

# Accesso al livello successivo
ssh bandit1@bandit.labs.overthewire.org -p 2220
```

### 🧠 Spiegazione
Il livello consolida le basi: navigare nel filesystem (`cd`, `ls`), leggere file (`cat`) e riconoscere la struttura della home di ogni utente.  
È il primo passo verso la metodologia “leggi → analizza → esegui” tipica di tutta la serie Bandit.

### 🪄 Takeaway
- Usa `ls -la` per visualizzare anche file nascosti.  
- Attenzione a non pubblicare le password trovate.  
- Comprendere la logica ricorsiva del wargame: ogni livello ti insegna un nuovo concetto UNIX di base.

---

## 🔹 Livello 2 → 3

### 🎯 Obiettivo
Recuperare la password per il livello 3 leggendo un file nella home che contiene spazi nel nome: `--spaces in this filename--`. Questo livello insegna a gestire nomi di file con spazi ed escape di shell.

### 💻 Comandi principali
```bash
# Visualizzare la lista dei file (nota spazi nel nome)
ls -la

# Leggere il file che contiene spazi (escape con backslash o usare virgolette)
cat ./--spaces\ in\ this\ filename--
# → password per il livello 3 (oscurata)
# [REDACTED]

# alternativa (più leggibile)
cat "./--spaces in this filename--"
```

### 🧠 Spiegazione
I nomi di file con spazi vanno gestiti correttamente: la shell separa gli argomenti sugli spazi, quindi è necessario **escapare** gli spazi (`\ `) o racchiudere l'intero nome tra virgolette. Il comando `cat` stampa il contenuto che contiene la password per il livello successivo.

### 🪄 Takeaway
- Quando incontri file con spazi, usa `\ ` o `"..."`.  
- `ls -la` mostra anche file con caratteri speciali o spazi; presta attenzione alla sintassi quando li apri.  
- Abitudine utile: se hai dubbi sul nome, usa il completamento tab (`Tab`) per lasciare che la shell gestisca gli escape.

---

## 🔹 Livello 3 → 4

### 🎯 Obiettivo
Trovare la password per il livello 4: è nascosta in un file **nascosto** dentro la directory `inhere/`. Questo livello insegna a cercare file nascosti e a interpretare l'output di `ls -a`.

### 💻 Comandi principali
```bash
# entrare nella directory e mostrare anche i file nascosti
cd inhere
ls -la

# visualizzare il file nascosto (esempio: ...Hiding-from-you)
cat ...Hiding-from-you
# → password per il livello 4 (oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
I file il cui nome inizia con un punto (.) sono considerati nascosti; `ls` senza `-a` non li mostra. Il comando `ls -a` elenca tutto, compresi `.` e `..`, e eventuali file nascosti creati con nomi atipici (es. `...Hiding-from-you`). Usando `cat` è possibile leggere il contenuto e ottenere la password.

### 🪄 Takeaway
- Usa sempre `ls -la` quando cerchi file nascosti o nomi strani.  
- I file nascosti possono avere nomi non convenzionali (più punti, underscore, ecc.); non dare nulla per scontato.  
- Buona pratica: ispeziona i permessi (`ls -l`) se qualcosa non è leggibile.

---

## 🔹 Livello 4 → 5

### 🎯 Obiettivo
Trovare la password per il livello 5 leggendo **l’unico file human-readable** presente nella directory `inhere/`. (Se il terminale è corrotto, prova `reset`.)

### 💻 Comandi principali
```bash
cd inhere/
# controllare rapidamente tipo di file per i file che iniziano con -file0
file ./-file0*

# individuato il file leggibile (-file07), leggerne il contenuto
cat ./-file07
# → password (oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
I file nella directory possono essere di vari formati (binari, dati compressi, testo con formattazioni strane). Il comando `file` identifica il tipo di ciascun elemento: qui solo `-file07` è `ASCII text` quindi è l’unico candidabile ad avere la password in chiaro. `cat` la stampa.

> [!TIP] Se l’output del terminale è “mangiato” da caratteri non stampabili, esegui `reset` per ripristinare la tty prima di usare `cat`.

### 🪄 Takeaway
- Usa `file` per distinguere file binari da file di testo prima di leggere.  
- Occhio a file con nomi che iniziano con `-` (usa `./-name` per riferirti correttamente).  
- Comandi utili: `ls -la`, `file`, `cat`.

---

## 🔹 Livello 5 → 6

### 🎯 Obiettivo
Trovare la password per il livello 6 cercando sotto `inhere/` un file che soddisfi **tutte** le condizioni: leggibile dall’umano, **esattamente 1033 byte** e **non eseguibile**.

### 💻 Comandi principali
```bash
# dalla home dell'utente
# trovare file di 1033 byte che non siano eseguibili
find -type f -size 1033c ! -executable

# esempio risultato:
# ./inhere/maybehere07/.file2

# leggere il file trovato
cat inhere/maybehere07/.file2
# → password (oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
`find` consente di combinare filtri precisi: `-size 1033c` cerca file **esattamente** di 1033 byte (unità `c` = byte), mentre `! -executable` esclude file con bit esecuzione impostato. Questo restringe rapidamente i candidati al singolo file che contiene la password.

### 🪄 Takeaway
- `find` è potente per criteri combinati (dimensione, permessi, tipo, proprietà).  
- Ricorda che `-size` ha unità diverse (`c` per byte, `k` per kilobyte, ecc.).  
- Quando trovi il file giusto, `file` può confermare che è effettivamente testo leggibile prima di `cat`.  

---

## 🔹 Livello 6 → 7

### 🎯 Obiettivo
Trovare la password per il livello 7 cercando sul filesystem un file che soddisfi **tutti** i seguenti vincoli:  
- proprietario **user = bandit7**  
- gruppo **group = bandit6**  
- dimensione **33 byte**

### 💻 Comandi principali
```bash
# ricerca ricorsiva su tutto il filesystem (sopprime gli errori di permission)
find / -user bandit7 -group bandit6 -size 33c -type f 2>/dev/null

# esempio: risultato trovato
# /var/lib/dpkg/info/bandit7.password

# leggere il file per ottenere la password (oscurata)
cat /var/lib/dpkg/info/bandit7.password
# → [REDACTED]
```

### 🧠 Spiegazione
Usiamo `find` con filtri combinati:
- `-user` e `-group` per limitare la ricerca alla proprietà del file,  
- `-size 33c` per selezionare esattamente 33 byte (`c` = byte),  
- `-type f` per limitare ai file normali.  
`2>/dev/null` elimina i messaggi di errore dovuti a directory inaccessibili, rendendo l'output leggibile.

### 🪄 Takeaway
- `find` è lo strumento più efficace per ricerche basate su proprietà (owner/group/size/permessi).  
- Quando fai ricerche a livello di root, filtra errori di permessi con `2>/dev/null` per non essere sommerso dagli errori.  
- Se trovi il file ma non puoi leggerlo, verifica permessi e possibili restrizioni; in questo livello il file è leggibile.

---

## 🔹 Livello 7 → 8

### 🎯 Obiettivo
Trovare la password per il livello 8 all'interno di `data.txt`: la password è la stringa che compare **accanto** alla parola `millionth`.

### 💻 Comandi principali
```bash
# cercare la riga contenente la parola 'millionth' e mostrare il secondo campo
cat data.txt | grep millionth
# esempio output:
# millionth    dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

# estrarre direttamente la password (campo 2)
grep -m1 -E '^millionth[[:space:]]' data.txt | awk '{print $2}'
# → [REDACTED]
```

### 🧠 Spiegazione
Qui il file `data.txt` contiene righe di testo strutturate: la parola chiave `millionth` è seguita dalla password. `grep` individua la riga corretta; `awk` (o `cut`) estrae il secondo campo. L'opzione `-m1` ferma `grep` dopo la prima corrispondenza, utile per efficienza.

### 🪄 Takeaway
- Quando una password è "vicino" ad una parola-chiave, usa `grep` per localizzare la riga e `awk`/`cut` per estrarre il campo.  
- Combinare comandi (`grep | awk`) è spesso più rapido e leggibile che fare parsing manuale.  
- Abitudine utile: testare il pattern con `grep -n` per vedere numeri di riga se necessario.

---

## 🔹 Livello 8 → 9

### 🎯 Obiettivo
Trovare la password per il livello 9 all'interno del file `data.txt`: la password è **l'unica riga** che compare **una sola volta** nel file (tutte le altre righe sono duplicate).

### 💻 Comandi principali
```bash
# trovare la riga unica usando sort + uniq
sort data.txt | uniq -u
# → restituisce la riga che appare una sola volta (password) -> [REDACTED]

# alternativa (stesso risultato, più esplicito)
sort data.txt | uniq -c | awk '$1==1{print $2}'
```

### 🧠 Spiegazione
Quando un file contiene molte righe duplicate e una sola riga unica, ordinare (`sort`) e poi usare `uniq -u` è il modo più semplice per isolare la riga non duplicata. `uniq -u` stampa solo le righe che non hanno duplicati consecutivi — per questo prima si usa `sort` che raggruppa le copie insieme.

### 🪄 Takeaway
- `sort | uniq -u` è una pattern-library utile per trovare elementi unici in dataset testuali.  
- Se vuoi vedere anche il conteggio delle occorrenze, usa `uniq -c`.  
- Usare `awk` o `cut` per estrarre campi quando il formato è più complesso.

---

## 🔹 Livello 9 → 10

### 🎯 Obiettivo
Recuperare la password per il livello 10 leggendo `data.txt`: la password è contenuta in una delle poche stringhe "leggibili", **preceduta da più caratteri `=`**.

### 💻 Comandi principali
```bash
# estrarre le stringhe leggibili e filtrare quelle che hanno più '=' prima di un token lungo
strings data.txt | grep -oE '={2,}[[:space:]]*[A-Za-z0-9+/=]{20,}'
# esempio output:
# ========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

# poi rimuovere i '=' e spazi per ottenere solo la password (opzionale)
strings data.txt | grep -oE '={2,}[[:space:]]*[A-Za-z0-9+/=]{20,}' | sed -E 's/^=+\s*//'
# → [REDACTED]
```

### 🧠 Spiegazione
`strings` estrae sequenze imprimibili da file binari o misti; tra queste si trova la stringa di interesse preceduta da molti `=`. `grep -oE` con una regex mirata individua esattamente le occorrenze con almeno due `=` seguiti da un token alfanumerico di lunghezza adeguata (qui la password). Infine `sed` o `awk` rimuovono i caratteri `=` e gli spazi per ottenere la password pulita.

### 🪄 Takeaway
- `strings` è indispensabile per analizzare file binari o "mischiati".  
- Costruisci regex conservative (`={2,}` + pattern per token) per evitare falsi positivi.  
- Se il token sembra Base64 o simile, verifica con `file` o prova una decodifica con `base64 -d` (solo se il formato lo suggerisce).

---
## 🔹 Livello 8 → 9

### 🎯 Obiettivo
Trovare la password per il livello 9 all'interno del file `data.txt`: la password è **l'unica riga** che compare **una sola volta** nel file (tutte le altre righe sono duplicate).

### 💻 Comandi principali
```bash
# trovare la riga unica usando sort + uniq
sort data.txt | uniq -u
# → restituisce la riga che appare una sola volta (password) -> [REDACTED]

# alternativa (stesso risultato, più esplicito)
sort data.txt | uniq -c | awk '$1==1{print $2}'
```

### 🧠 Spiegazione
Quando un file contiene molte righe duplicate e una sola riga unica, ordinare (`sort`) e poi usare `uniq -u` è il modo più semplice per isolare la riga non duplicata. `uniq -u` stampa solo le righe che non hanno duplicati consecutivi — per questo prima si usa `sort` che raggruppa le copie insieme.

### 🪄 Takeaway
- `sort | uniq -u` è una pattern-library utile per trovare elementi unici in dataset testuali.  
- Se vuoi vedere anche il conteggio delle occorrenze, usa `uniq -c`.  
- Usare `awk` o `cut` per estrarre campi quando il formato è più complesso.

---

## 🔹 Livello 9 → 10

### 🎯 Obiettivo
Recuperare la password per il livello 10 leggendo `data.txt`: la password è contenuta in una delle poche stringhe "leggibili", **preceduta da più caratteri `=`**.

### 💻 Comandi principali
```bash
# estrarre le stringhe leggibili e filtrare quelle che hanno più '=' prima di un token lungo
strings data.txt | grep -oE '={2,}[[:space:]]*[A-Za-z0-9+/=]{20,}'
# esempio output:
# ========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

# poi rimuovere i '=' e spazi per ottenere solo la password (opzionale)
strings data.txt | grep -oE '={2,}[[:space:]]*[A-Za-z0-9+/=]{20,}' | sed -E 's/^=+\s*//'
# → [REDACTED]
```

### 🧠 Spiegazione
`strings` estrae sequenze imprimibili da file binari o misti; tra queste si trova la stringa di interesse preceduta da molti `=`. `grep -oE` con una regex mirata individua esattamente le occorrenze con almeno due `=` seguiti da un token alfanumerico di lunghezza adeguata (qui la password). Infine `sed` o `awk` rimuovono i caratteri `=` e gli spazi per ottenere la password pulita.

### 🪄 Takeaway
- `strings` è indispensabile per analizzare file binari o "mischiati".  
- Costruisci regex conservative (`={2,}` + pattern per token) per evitare falsi positivi.  
- Se il token sembra Base64 o simile, verifica con `file` o prova una decodifica con `base64 -d` (solo se il formato lo suggerisce).

---

## 🔹 Livello 10 → 11

### 🎯 Obiettivo
Decodificare il contenuto Base64 del file `data.txt` per recuperare la password del livello successivo.

### 💻 Comandi principali
```bash
# Decodificare il file e cercare il token (esempio)
base64 -d data.txt | grep -oE '[A-Za-z0-9]{20,}'

# output (password oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
`base64 -d` decodifica il flusso Base64 in output binario/testuale leggibile. Spesso il risultato contiene molte stringhe; `grep -oE '[A-Za-z0-9]{20,}'` estrae token alfanumerici di lunghezza significativa (qui la password). Se il file è molto grande, puoi prima usare `head`/`less` per ispezionarlo.

### 🪄 Takeaway
- Usa `base64 -d` per trasformare dati codificati in Base64 in testo/byte utili.  
- Combina decodifica + parsing (`base64 -d | grep`) per estrarre rapidamente la password.  
- Se il risultato appare binario, ispeziona con `file` o `strings` per trovare porzioni leggibili.

---

## 🔹 Livello 11 → 12

### 🎯 Obiettivo
Applicare la rotazione ROT13 al contenuto di `data.txt` per ottenere la password del livello successivo (tutte le lettere A–Z / a–z sono state ruotate di 13 posizioni).

### 💻 Comandi principali
```bash
# Applicare ROT13 e leggere la riga contenente la password
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt

# output (password oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
ROT13 è una cifratura di tipo Cesare con shift 13; `tr` mappa ogni lettera al suo corrispondente spostato di 13 posizioni. Questo è perfetto per trasformazioni rapide da riga di comando senza strumenti esterni. In genere il testo decifrato contiene una frase del tipo "The password is ...".

### 🪄 Takeaway
- `tr` è lo strumento semplice e veloce per trasposizioni di caratteri (ROT13, maiuscole/minuscole, ecc.).  
- Quando sospetti trasformazioni semplici (ROT13/rotN), prova prima con `tr` prima di soluzioni più complesse.  
- Se vuoi automatizzare l’estrazione: `tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt | grep -oE '[A-Za-z0-9]{20,}'`.

---

## 🔹 Livello 12 → 13

### 🎯 Obiettivo
Il file `data.txt` è un **hexdump** di un file che è stato **ripetutamente compresso**. L’obiettivo è ricostruire il file originale (invertire l’hexdump) e poi decomprimerlo/estrarlo passo-passo fino ad ottenere il testo contenente la password per il livello successivo.

### 💻 Comandi principali
```bash
# creare una directory di lavoro sicura sotto /tmp
mktemp -d
cd "$_"

# convertire l'hexdump in binario
xxd -r data.txt data.bin

# identificare il formato e decomprimere in base al tipo
file data.bin

# esempio di flusso (adattare i nomi e ripetere i passaggi in base all'output di `file`):
mv data.bin data.gz            # se file è gzip
gzip -d data.gz

mv data data.bz2               # se file è bzip2
bzip2 -d data.bz2

mv data data.tar               # se file è tar
tar xf data.tar

# ripetere: file -> rinomina -> decomprimi/estrai ... finché `file` non dice "ASCII text"
file data*
cat data   # o il file testuale finale
# → password (oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
1. `xxd -r` ricostruisce il contenuto binario a partire dall’hexdump.  
2. Il comando `file` dice quale compressione/archivio è presente (gzip, bzip2, tar, ecc.).  
3. Rinomina il file in base al formato (`.gz`, `.bz2`, `.tar`) e usa lo strumento adatto (`gzip -d`, `bzip2 -d`, `tar xf`) per decomprimere/estrarre.  
4. Spesso i file sono nidificati: dopo ogni estrazione riesegui `file` e ripeti il procedimento finché non ottieni testo leggibile.

### 🪄 Takeaway
- Lavora sempre in una directory temporanea (`mktemp -d`) per non inquinare la home e per sicurezza.  
- Usa `file` dopo ogni passaggio per decidere il comando giusto — non provare a forzare una decompressione senza verificarne il formato.  
- Rinomina i file con estensioni appropriate (es. `.gz`, `.bz2`, `.tar`) per semplificare il flusso mentale.  
- Se il processo è ripetitivo, puoi automatizzarlo con uno script che legge `file` e applica la decompressione corretta (ma attenzione a non cancellare file prima di verificarli).

---

## 🔹 Livello 13 → 14

### 🎯 Obiettivo
Per questo livello non ottieni direttamente la password successiva. Invece, ti viene fornita una **chiave SSH privata** sul server di `bandit13` che dovrai copiare in locale e usare per autenticarti come `bandit14` (utente che può leggere `/etc/bandit_pass/bandit14`).

### 💻 Comandi principali
```bash
# dalla tua macchina locale, copiare la chiave privata dal server (porta 2220)
scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private .

# proteggere la chiave
chmod 600 sshkey.private

# con la chiave privata, connettersi come bandit14 (porta 2220)
ssh -i sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org

# una volta dentro:
cat /etc/bandit_pass/bandit14
# → password (oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
`scp` copia la chiave privata dal server remoto alla macchina locale. È fondamentale impostare permessi restrittivi (`chmod 600`) sulla chiave prima di usarla con `ssh`, altrimenti `ssh` rifiuterà l’uso per ragioni di sicurezza. L’autenticazione avviene tramite la chiave privata: una volta connessi come `bandit14`, si può leggere `/etc/bandit_pass/bandit14` per ottenere la password del livello successivo.

### 🪄 Takeaway
- Tratta la chiave privata come materiale **sensibile**: non committarla mai in un repo e assicurati che i permessi siano `600`.  
- Usa sempre l’opzione `-i <key>` per specificare la chiave privata con `ssh`.  
- Se lo step di copia non funziona (permessi o path errato), controlla che il file esista nella home remota e che tu abbia permessi di lettura su di esso.  
- Dopo aver usato la chiave, puoi rimuoverla localmente o conservarla in un’area sicura (es. `~/.ssh/` con permessi appropriati) se ti serve ancora per ulteriori accessi.

---

## 🔹 Livello 14 → 15

### 🎯 Obiettivo
Inviare la password corrente al servizio in ascolto su **localhost:30000** per ricevere, in risposta, la password del livello successivo.

### 💻 Comandi principali
```bash
# leggere la password corrente e inviarla a localhost:30000 via TCP
cat /etc/bandit_pass/bandit14 | nc localhost 30000

# output di esempio (qui oscurato):
# Correct!
# [REDACTED]
```

> Alternativa (stesso effetto, più esplicita):
```bash
printf "%s\n" "$(cat /etc/bandit_pass/bandit14)" | nc localhost 30000
```

### 🧠 Spiegazione
Il server locale sulla porta 30000 si aspetta in ingresso **una singola riga** contenente la password corrente; quando la riceve e la verifica, risponde con la password successiva. `nc` (netcat) apre una connessione TCP semplice e inoltra la stringa. Usare `printf`/`echo` assicura che venga inviato un terminatore di linea (`\n`) se necessario.

### 🪄 Takeaway
- `nc` è perfetto per servizi TCP "plain" che leggono una riga e rispondono.  
- Assicurati di inviare **esattamente** la stringa corretta (nessun carattere extra/whitespace).  
- Se la risposta non arriva, verifica che il servizio sia in ascolto (`ss -ltnp` o `netstat -ltnp`) e che la porta sia corretta.

---

## 🔹 Livello 15 → 16

### 🎯 Obiettivo
Simile al livello precedente, ma la comunicazione con il servizio avviene su **TLS**: inviare la password corrente a **localhost:30001** usando SSL/TLS e ricevere la password successiva.

### 💻 Comandi principali
Metodo interattivo (apri connessione TLS e incolla la password):
```bash
openssl s_client -connect localhost:30001
# incolla la password, premi Invio
# attendi la risposta (poi CTRL+C/CTRL+D per chiudere se necessario)
# → [REDACTED]
```

Metodo non interattivo (pipe):
```bash
printf "%s\n" "$(cat /etc/bandit_pass/bandit15)" | openssl s_client -connect localhost:30001 -quiet
# → [REDACTED]
```

Se incontri messaggi come `DONE`, `RENEGOTIATING` o `KEYUPDATE`, prova ad aggiungere `-ign_eof` o leggere la sezione "CONNECTED COMMANDS" del manpage `openssl s_client`.

### 🧠 Spiegazione
`openssl s_client` stabilisce una connessione TLS verso il servizio. Il server spesso presenta un certificato **self-signed** (verifica fallita) — questo è atteso in ambiente di test. Dopo l’handshake, invii la password sulla connessione sicura; il server risponde con la password successiva. L’opzione `-quiet` riduce l’output informativo, mentre `-ign_eof` aiuta a gestire connessioni che chiudono in modo anomalo.

### 🪄 Takeaway
- Per servizi TLS locali usa `openssl s_client -connect host:port` per testare e inviare payload sicuri.  
- È normale vedere `self-signed certificate` in questi wargame; non è un errore funzionale (ma non ignorare avvisi in produzione).  
- Quando automatizzi l’invio, preferisci `printf ... | openssl s_client -quiet` per evitare input interattivi e ottenere output pulito.  
- Se la sessione TLS rimane "bloccata" o produce messaggi di stato, sperimenta con `-ign_eof`, `-quiet` o usa `-crlf` se il server richiede CRLF come terminatore.

---

## 🔹 Livello 16 → 17

### 🎯 Obiettivo
Individuare nell’intervallo **31000–32000** le porte su **localhost** che hanno un servizio attivo, distinguere quali parlano **TLS** e inviare la password corrente al servizio corretto. Solo **un** servizio restituisce le credenziali/chiave per il livello successivo.

### 💻 Comandi principali
```bash
# 1) Scansione servizi nell'intervallo richiesto, con rilevazione versione/protocollo
nmap -sV localhost -p 31000-32000

# (esempio esiti rilevanti)
# 31046/tcp open  echo
# 31518/tcp open  ssl/echo
# 31691/tcp open  echo
# 31790/tcp open  ssl/unknown   <-- candidato
# 31960/tcp open  echo

# 2) Test del servizio TLS candidato
openssl s_client -connect localhost:31790 -ign_eof

# 3) Inviare la password corrente sulla connessione TLS (una riga e Invio)
# (puoi pipe-are in modo non interattivo)
printf "%s\n" "$(cat /etc/bandit_pass/bandit16)" \
  | openssl s_client -connect localhost:31790 -quiet

# 4) Riceverai in risposta un blocco PEM (chiave privata RSA) da salvare
#    (qui oscurato per policy)
# -----BEGIN RSA PRIVATE KEY-----
# [REDACTED PRIVATE KEY]
# -----END RSA PRIVATE KEY-----

# 5) Salva la chiave in un file e proteggila
cat > /tmp/bandit17.key <<'EOF'
-----BEGIN RSA PRIVATE KEY-----
[REDACTED PRIVATE KEY]
-----END RSA PRIVATE KEY-----
EOF
chmod 600 /tmp/bandit17.key
```

### 🧠 Spiegazione
- **Ricognizione**: `nmap -sV` identifica quali porte nell’intervallo sono aperte e tenta di capire se parlano **SSL/TLS**.  
- **Selezione**: tra i risultati, i servizi `ssl/*` sono i candidate; il comportamento del livello suggerisce che **uno solo** risponde con le credenziali corrette se riceve la password valida.  
- **Dialogo TLS**: `openssl s_client` stabilisce l’handshake; eventuali warning (certificato self-signed) sono **attesi** nel wargame. Dopo l’handshake, inviando la password **esatta** su una singola riga, il server restituisce una **RSA private key** (PEM).  
- **Uso successivo**: quella chiave è l’artefatto da usare per autenticarti al livello 17 (nel prossimo step).

### 🪄 Takeaway
- Usa `nmap -sV` per combinare **port scanning** e **fingerprinting** dei servizi.  
- Con servizi TLS, `openssl s_client -connect host:port` è lo strumento più veloce per test e scambio dati.  
- I server “trappola” in questo livello fanno eco: solo **uno** consegna la credenziale vera.  
- Tratta la chiave privata come **sensibile**: salvala con permessi `600` e **non** committarla mai su GitHub.  

---

## 🔹 Livello 17 → 18

### 🎯 Obiettivo
Identificare quale riga è cambiata tra i file `passwords.old` e `passwords.new` nella home. La riga modificata (cioè quella nuova) contiene la password per il livello successivo.

### 💻 Comandi principali
```bash
# confrontare i due file e individuare la riga modificata
diff passwords.old passwords.new

# esempio di output
# 42c42
# < pGozC8kOHLkBMOaL0ICPvLV1IjQ5F1VA
# ---
# > x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO

# la password è quella con il simbolo ">"
# → [REDACTED]
```

### 🧠 Spiegazione
Il comando `diff` confronta due file linea per linea:
- `<` indica una riga presente solo nel primo file (`passwords.old`);
- `>` indica una riga nuova nel secondo file (`passwords.new`).  
La differenza in questo caso è **una sola riga**, e il contenuto marcato con `>` è la password per il livello 18.

### 🪄 Takeaway
- `diff` è utile per individuare variazioni puntuali in configurazioni o password file.  
- Se vuoi un’uscita più leggibile, puoi usare `diff -u` (unified format).  
- Ricorda: la password corretta è sempre nel **file nuovo**, non in quello vecchio.

---

## 🔹 Livello 18 → 19

### 🎯 Obiettivo
Ottenere la password del livello 19 leggendo il file `readme` nella home, **senza** essere disconnesso automaticamente. Il file `.bashrc` dell’utente `bandit18` è stato modificato per eseguire un logout immediato appena si apre una sessione SSH interattiva.

### 💻 Comandi principali
```bash
# accedere con SSH in modalità "non interattiva" e leggere direttamente il file
ssh -i sshkey17.private -p 2220 bandit18@bandit.labs.overthewire.org cat readme

# output:
# [REDACTED]
```

### 🧠 Spiegazione
L’esecuzione diretta di un comando remoto (`cat readme`) evita di aprire una shell interattiva.  
Dato che `.bashrc` si attiva **solo in sessioni interattive**, questa tecnica bypassa lo script di logout.  
Si usa la chiave privata del livello 17 (`sshkey17.private`) e la porta 2220, come in tutti i livelli precedenti.

### 🪄 Takeaway
- I file `.bashrc` e `.bash_profile` vengono caricati solo in sessioni **login interattive**.  
- Per aggirare logout o comandi indesiderati, usa SSH in modalità **non interattiva** specificando direttamente il comando.  
- Questa tecnica è utile anche per automazioni (`ssh host "cmd"`) e raccolta dati remoti.

---

## 🔹 Livello 19 → 20

### 🎯 Obiettivo
Utilizzare il binario **setuid** presente nella home (`bandit20-do`) per eseguire comandi come l’utente `bandit20` e ottenere la password dal file `/etc/bandit_pass/bandit20`.

### 💻 Comandi principali
```bash
# verificare cosa fa il binario
./bandit20-do

# output:
# Run a command as another user.
#   Example: ./bandit20-do whoami

# eseguire un comando come bandit20
./bandit20-do whoami
# → bandit20

# leggere la password per bandit20
./bandit20-do cat /etc/bandit_pass/bandit20
# → [REDACTED]
```

### 🧠 Spiegazione
`bandit20-do` è un binario **setuid**, ovvero eseguito con i privilegi dell’utente proprietario (`bandit20`).  
Quando viene lanciato, qualunque comando passato come argomento viene eseguito con tali privilegi.  
In questo caso si usa per leggere il file di password a cui `bandit19` normalmente non avrebbe accesso.

### 🪄 Takeaway
- I binari **setuid** sono strumenti per eseguire codice con privilegi superiori (spesso sfruttati anche in privilege escalation).  
- È buona prassi **analizzare l’uso del comando** (`./nomecomando` senza argomenti) prima di tentare l’esecuzione.  
- Controlla sempre i permessi con `ls -l`: un bit “s” nella colonna dei permessi (`-rwsr-x---`) indica setuid/setgid attivo.

---

## 🔹 Livello 20 → 21

### 🎯 Obiettivo
Usare un altro binario **setuid**, chiamato `suconnect`, che:
1. Si connette a una porta locale specificata come argomento;  
2. Legge una riga di testo dalla connessione;  
3. La confronta con la password del livello corrente (`bandit20`);  
4. Se la password è corretta, invia la password per il livello successivo (`bandit21`).

### 💻 Comandi principali
```bash
# aprire un listener locale che invii la password corrente quando qualcuno si connette
echo 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO | nc -l -p 2000 &

# eseguire il binario setuid passando la porta come argomento
./suconnect 2000

# output:
# Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
# Password matches, sending next password
# [REDACTED]

# terminare il job in background
fg
```

### 🧠 Spiegazione
Il binario `suconnect` tenta di aprire una connessione TCP verso una porta locale.  
Con `nc -l -p 2000` crei un **server locale** che, non appena riceve una connessione, invia la password corrente.  
Il binario riceve la password, la valida e risponde con la password successiva, che viene stampata sul terminale.

> [!NOTE]
> È importante aprire prima il listener (`nc -l -p 2000 &`) e solo dopo eseguire `./suconnect`, altrimenti il programma non troverà nessun servizio in ascolto.

### 🪄 Takeaway
- Questa sfida introduce il concetto di **network loopback** e interazione tra processi locali.  
- `nc` (netcat) è uno strumento versatile per debugging e trasferimenti TCP/UDP.  
- In pentesting, pattern simili vengono usati per **reverse shell** e test di **local privilege escalation**.  
- Se vuoi osservare il traffico, puoi usare `ss -ltn` o `netstat -anp` mentre la connessione avviene.

---

## 🔹 Livello 21 → 22

### 🎯 Obiettivo
Identificare quale job **cron** è configurato in `/etc/cron.d/` e capire quale comando viene eseguito periodicamente. Il job copiando la password di `bandit22` in un file temporaneo rende quella password leggibile: bisogna individuare e leggere quel file.

### 💻 Comandi principali
```bash
# leggere il file di configurazione del cron per questo job
cat /etc/cron.d/cronjob_bandit22

# aprire lo script eseguito dal job
cat /usr/bin/cronjob_bandit22.sh

# ispezionare il file temporaneo creato dallo script (il nome può cambiare)
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
# → password per bandit22 (oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
`/etc/cron.d/cronjob_bandit22` contiene la schedule che esegue `/usr/bin/cronjob_bandit22.sh` ogni minuto (e al reboot). Lo script esegue due operazioni principali:

1. `chmod 644 /tmp/<nome>`: imposta permessi leggibili per tutti sul file temporaneo;  
2. `cat /etc/bandit_pass/bandit22 > /tmp/<nome>`: copia la password protetta in un file temporaneo con permessi che consentono la lettura da altri utenti.

Poiché il file temporaneo viene creato con permessi globalmente leggibili, un utente con accesso alla macchina può semplicemente leggere il file in `/tmp` per ottenere la password.

### 🪄 Takeaway
- Controlla sempre `/etc/cron.d/` per job di sistema che eseguono script con privilegi e potrebbero esporre informazioni.  
- Gli script cron che scrivono in `/tmp` possono accidentalmente rendere dati sensibili leggibili se non impostano permessi restrittivi.  
- `cat` sul file temporaneo (quando presente) è spesso il modo più rapido per recuperare la password.  
- In ambienti reali, evitare di scrivere credenziali in `/tmp` o, se necessario, usare permessi stretti e nomi non prevedibili.

---

## 🔹 Livello 22 → 23

### 🎯 Obiettivo
Analizzare lo script cron `/usr/bin/cronjob_bandit23.sh` per capire come calcola il percorso del file temporaneo in cui la password dell’utente `bandit23` viene scritta, e quindi leggere quel file per ottenere la password per il livello successivo.

### 💻 Comandi principali
```bash
# leggere la definizione del job cron
cat /etc/cron.d/cronjob_bandit23

# aprire lo script eseguito dal cron
cat /usr/bin/cronjob_bandit23.sh

# lo script usa whoami e md5sum per costruire il nome:
# myname=$(whoami)
# mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

# calcolare localmente lo stesso nome (sostituire con l'utente target)
echo "I am user bandit23" | md5sum | cut -d ' ' -f 1

# leggere il file temporaneo corrispondente
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
# → password per bandit23 (oscurata)
# [REDACTED]
```

### 🧠 Spiegazione
Lo script eseguito dal cron:
1. calcola il nome del file temporaneo come `md5("I am user <username>")`;  
2. copia `/etc/bandit_pass/<username>` in `/tmp/<md5sum>`.

Per recuperare la password basta replicare la trasformazione (calcolare l’MD5 della stringa `I am user <nome_utente>`) e leggere `/tmp/<risultato>`. Il file risultante conterrà la password per l’utente target.

### 🪄 Takeaway
- Leggere gli script cron ti permette di prevedere percorsi e nomi generati dinamicamente.  
- `md5sum` + `echo` + `cut` è una tecnica comune per generare nomi deterministici e riproducibili.  
- Per livelli simili: ricostruisci la funzione che genera il nome del file e poi leggi il file in `/tmp`.  
- In produzione, non generare file contenenti segreti con nomi prevedibili in directory globali come `/tmp`.

---

## 🔹 Livello 23 → 24

### 🎯 Obiettivo
Creare uno script shell che verrà eseguito automaticamente dal job `cron` del livello. Lo script deve essere posizionato in `/var/spool/<utente>/foo` (per questo livello `/var/spool/bandit24/foo`) e verrà eseguito dal cron con i permessi di `bandit24`. Lo script, dopo l'esecuzione, viene rimosso dal job: preparare quindi una copia di backup se vuoi conservarlo.

### 💻 Comandi principali
```bash
# leggere il crontab e lo script eseguito
cat /etc/cron.d/cronjob_bandit24
cat /usr/bin/cronjob_bandit24.sh

# contenuto rilevante dello script (esempio):
# myname=$(whoami)
# cd /var/spool/$myname/foo
# for i in * .*; do
#   owner="$(stat --format "%U" ./$i)"
#   if [ "${owner}" = "bandit23" ]; then
#       timeout -s 9 60 ./$i
#   fi
#   rm -f ./$i
# done

# creare una working dir temporanea
mktemp -d
cd /tmp/tmp.XXXXXX

# preparare lo script che verrà eseguito dal cron
cat > bandit24.sh <<'EOF'
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/tmp.XXXXXX/passwd_bandit24
EOF

chmod +x bandit24.sh

# copiare lo script nella directory controllata dal cron (richiede permessi scrivibili dall'utente)
cp bandit24.sh /var/spool/bandit24/foo/

# dopo che il cron esegue lo script, leggere il file temporaneo
cat /tmp/tmp.XXXXXX/passwd_bandit24
# → [REDACTED]
```

### 🧠 Spiegazione
- Il job cron legge ed esegue ogni file presente in `/var/spool/<utente>/foo`. Lo script controlla l'owner del file e, se corrisponde all'utente previsto (qui `bandit23`), lo esegue con `timeout` per evitare processi bloccati.  
- Poiché lo script è cancellato dopo l'esecuzione (`rm -f ./$i`), è fondamentale mantenere una copia locale se vuoi ri-eseguirlo o analizzarlo.  
- L'idea è sfruttare il fatto che lo script verrà eseguito con i permessi di `bandit24` e quindi potrà leggere `/etc/bandit_pass/bandit24` e scriverne il contenuto in un file temporaneo con permessi leggibili.

### 🪄 Takeaway
- Cron jobs che eseguono script trovati in directory pubbliche sono vettori comuni per escalation o per ottenere dati sensibili: attenzione a cosa si può scrivere in quelle directory.  
- Quando lo script viene rimosso automaticamente dal job, salva una copia locale (ad esempio `cp bandit24.sh ~/bandit24.sh`) prima di copiarlo nella directory del cron.  
- `timeout` è usato per limitare l'esecuzione — utile quando non si conosce il comportamento dello script eseguito.  
- Non lasciare script o file con permessi insicuri in directory pubbliche; in questo esercizio lo sfruttiamo appositamente per ottenere la password.

---

## 🔹 Livello 24 → 25

### 🎯 Obiettivo
Interagire con un **daemon** in ascolto su `localhost:30002` che, se riceve la password corrente e un pincode a 4 cifre corretti (in una singola riga separati da uno spazio), restituisce la password del livello successivo. L'unico modo per scoprire il pincode è provare tutte le 10.000 combinazioni (0000–9999).

### 💻 Comandi principali
```bash
# prova manuale (interattiva)
nc localhost 30002
# poi digitare: "<password_corrente> 1234" e premere Invio

# esempio di brute-force scrivendo tutte le combinazioni in un file e poi pipe-ando a nc
mktemp -d
cd /tmp/tmp.XXXXXX

# generare le righe "password 0000" .. "password 9999"
for i in {0000..9999}; do
  printf "%s %04d\n" "REDACTED_PASSWORD_BANDIT24" "$i" >> 4pincodes.txt
done

# inviare tutte le righe al daemon (una singola connessione)
cat 4pincodes.txt | nc localhost 30002 > result.txt

# cercare nella risposta la riga "Correct!" e la password successiva
grep -i "Correct" -n result.txt && tail -n 1 result.txt
# → [REDACTED_NEXT_PASSWORD]
```

> Variante (meno I/O su disco — stream diretto):
```bash
# inviare direttamente dalla shell evitando il file intermedio
( for i in {0000..9999}; do printf "%s %04d\n" "REDACTED_PASSWORD_BANDIT24" "$i"; done ) | nc localhost 30002 > result.txt
```

### 🧠 Spiegazione
- Il servizio accetta una singola riga contenente la password corrente e il pincode. Inviare 10.000 richieste aprendo/chiudendo la connessione per ognuna sarebbe lento; invece si **prepara** la lista di tutte le possibili righe e la **pipe-iamo** in una singola connessione `nc`, così il server processa tutte le linee in sequenza e risponde quando trova la combinazione corretta.  
- Il loop `for i in {0000..9999}` con `printf "%04d"` garantisce il padding dei numeri fino a 4 cifre (0000, 0001, …, 9999).  
- `result.txt` contiene tutta la conversazione; cercando la parola `Correct!` o leggendo l'ultima parte del file si ottiene la password del livello successivo.

### 🪄 Takeaway
- Quando devi bruteforcare server che leggono input riga-per-riga, è efficiente inviare tutte le combinazioni su **una sola connessione** tramite pipe, evitando il costo di 10.000 handshake TCP.  
- Evita di generare file giganteschi su dischi lenti quando possibile — lo streaming in memoria / pipe è preferibile.  
- Assicurati di includere il terminatore di linea (`\n`) in ogni input (qui `printf` lo fa).  
- In ambienti reali, brute-force su servizi di rete dovrebbe essere fatto con cautela (load, rate-limiting, leggi e policy). Qui è un esercizio di laboratorio controllato.

---

## ## 🔹 Livello 25 → 26

### ### 🎯 Obiettivo
Accedere come `bandit26`. L’utente `bandit26` ha come shell `/usr/bin/showtext` (non una bash interattiva). Scopri che fa questo programma, come funziona `more` in quel contesto e come "sfuggire" alla shell restrittiva per leggere la password in `/etc/bandit_pass/bandit26`.

### ### 💻 Comandi principali (estratto)
```bash
# verificare la shell impostata per bandit26
cat /etc/passwd | grep bandit26
# → mostra /usr/bin/showtext (o simile)

# leggere il binario/script showtext
cat /usr/bin/showtext
# esempio output:
# #!/bin/sh
# export TERM=linux
# exec more ~/text.txt
# exit 0

# copiare la chiave SSH fornita dall'utente precedente
scp -P 2220 bandit25@bandit.labs.overthewire.org:~/bandit26.sshkey .

# connettersi usando la chiave privata
ssh -i bandit26.sshkey -p 2220 bandit26@bandit.labs.overthewire.org

# una volta dentro, usare i comandi per interagire con more/vim:
# - ridurre la dimensione del terminale (per abilitare modalità "more -> v -> vim" su alcuni server)
# - quando compare "--More--", premere 'v' per aprire vim (modalità visual editor)
# dentro vim:
# :set shell=/bin/bash
# :shell
# ora si è in una shell bash interattiva

# ALTERNATIVA (dentro vim): :e /etc/bandit_pass/bandit26
# oppure, una volta in shell:
cat /etc/bandit_pass/bandit26
# → [REDACTED]
```

### ### 🧠 Spiegazione
- `/usr/bin/showtext` invoca `more ~/text.txt`, fornendo un'interfaccia di sola lettura.  
- `more` spesso permette di avviare un editor (premendo `v`) aprendo il file con `vi`/`vim`. In molte installazioni `more` lancia l'editor impostato dalla variabile d'ambiente `$EDITOR` o usa `vi`.  
- In `vim` si può cambiare temporaneamente la shell con `:set shell=/bin/bash` e poi eseguire `:shell` per ottenere una vera shell interattiva (breakout). Da lì puoi leggere `/etc/bandit_pass/bandit26`.  
- Su alcuni client Windows (PowerShell) il comportamento può bloccarsi — usare cmd.exe o un terminale POSIX-like per evitare problemi.

### ### 🪄 Takeaway
- I visual pager/editor possono essere vettori di “escape” da shell limitate: impara le scorciatoie (`v`, `:shell`, `:e`) per trasformare un viewer in una shell.  
- Se hai una chiave privata (`.sshkey`) fornita dal livello precedente, scaricala con `scp` e usa `ssh -i` per autenticarti.  
- Tratta sempre le chiavi private come sensibili: `chmod 600` localmente e non committarle.  
- Attenzione a PowerShell su Windows: preferisci cmd o un terminale che emuli POSIX per evitare incompatibilità con escape tramite `more`/`vim`.

---

## ## 🔹 Livello 26 → 27

### ### 🎯 Obiettivo
Dopo aver ottenuto una shell valida come `bandit26`, recuperare la password di `bandit27` eseguendo lo script/setuid (fornito nella home) che esegue comandi come l’utente appropriato.

### ### 💻 Comandi principali (estratto)
```bash
# esplorare la home
ls -la

# identificare i file utili
# ad esempio: bandit27-do  text.txt
ls

# eseguire il binario fornito per leggere la password
./bandit27-do cat /etc/bandit_pass/bandit27
# → [REDACTED]
```

### ### 🧠 Spiegazione
- Il file `bandit27-do` è un eseguibile con permessi che consentono di eseguire comandi con privilegi o contesto diverso (tipicamente setuid o wrapper).  
- Chiamandolo con argomento `cat /etc/bandit_pass/bandit27`, il programma esegue `cat` come utente target e stampa la password del livello successivo.  
- Questo pattern è comune: un eseguibile limitato che esegue comandi per recuperare segreti. Comprendere cosa fa il binario (eseguendolo senza argomenti, o analizzandone i permessi con `ls -l`) è il primo passo.

### ### 🪄 Takeaway
- Controlla sempre i permessi (`ls -l`) e prova a eseguire i binari con `--help` o senza argomenti per scoprire il loro comportamento.  
- I wrapper/setuid sono spesso usati nei CTF per dimostrare il concetto di escalation controllata: usali per leggere file a cui normalmente non avresti accesso.  
- Dopo aver ottenuto la password, ricordati di non lasciare artefatti sensibili (chiavi, script) in posizioni pubbliche: pulisci la tua working dir se necessario.

---

## 🔹 Livello 27 → 28 🗂️

### ### 🎯 Obiettivo
Clonare un repository Git remoto accessibile via SSH (utente `bandit27-git` su porta `2220`) e cercare al suo interno la password per il livello successivo leggendo i file del repo.

### ### 💻 Comandi principali
```bash
# clonare il repository via SSH (porta 2220)
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo

# entrare nella directory clonata e ispezionare i file
cd repo
ls -la
cat README

# se il file contiene la password in chiaro, è lì (ma nel README della guida lasciamo la password oscurata)
# → [REDACTED]
```

### ### 🧠 Spiegazione
Il repository remoto contiene la password in un file di testo (README o simile). Poiché l'autenticazione SSH avviene con le credenziali `bandit27` (stessa password dell'utente `bandit27`), il clone è permesso e basta leggere i file del repo per trovare la stringa contenente la password del livello successivo.

### ### 🪄 Takeaway
- Quando un repo remoto è accessibile via SSH, la prima azione è clonarlo e ispezionare i file comuni (`README`, `CHANGELOG`, ecc.).  
- I repository possono contenere *leak* accidentali (password in chiaro, commit con dati sensibili).  
- In un writeup su GitHub mantieni le password oscurate (`[REDACTED]`) e spiega come riprodurre il passo se l’utente ha accesso legittimo.

---

## 🔹 Livello 28 → 29 🔎

### ### 🎯 Obiettivo
Clonare il repository SSH (`bandit28-git`) e analizzare la storia Git per trovare dove è stata esposta (o rimossa) la password: il leak spesso è in un commit storico.

### ### 💻 Comandi principali
```bash
# clonare il repository del livello 28
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
cd repo

# ispezionare il contenuto corrente
ls -la
cat README.md

# esplorare la storia dei commit per cercare informazioni rimosse o modificate
git log --oneline --decorate --all

# visualizzare il commit sospetto per vedere la diff (sostituire <commit> con l'hash reale)
git show b5ed4b5a3499533c2611217c8780e8ead48609f6

# se la password è stata rimossa nel repo (info leak fix), cercarla nella diff:
# la diff mostrerà la vecchia riga con la password (→ [REDACTED])

# altre tecniche utili:
git branch -a        # vedere branch remoti
git checkout dev     # switching su branch che potrebbe contenere info
git tag              # cercare tag con segreti (es. 'secret')
git show secret      # mostra il contenuto del tag
```

### ### 🧠 Spiegazione
I maintainer possono aver rimosso la password dal file `README.md` ma la password rimane nella storia dei commit. Con `git log` e `git show <commit>` è possibile visualizzare la diff che mostra cosa è stato cambiato e recuperare la stringa rimossa. Inoltre branch diversi (`dev`) o tag (`secret`) possono contenere versioni diverse del repo dove la password è ancora presente.

### ### 🪄 Takeaway
- Git conserva la cronologia completa: rimozioni nel working tree non cancellano necessariamente i dati dalla storia. `git show` su commit specifici è un modo potente per recuperare informazioni.  
- Controlla sempre: `git log`, `git show`, `git branch -a`, `git tag` e ispeziona commit diff per informazioni nascoste.  
- In un writeup pubblico non ripubblicare segreti reali: sostituisci con `[REDACTED]` e indica i comandi per riprodurre la scoperta se l'utente possiede accesso legittimo.

---

[!IMPORTANT] Note pratiche per il README GitHub
- Mantieni tutte le password oscurate (`[REDACTED]`) per rispetto delle regole di OverTheWire.  
- Quando descrivi la procedura, mostra i comandi esatti usati per clonare e ispezionare il repo (come sopra) così che il lettore possa riprodurre la ricerca senza rivelare il segreto.

---

## ⚙️ Bandit: Livelli 29 → 33 (Git e Shell Escape)

---

## ## 🔹 Livello 29 → 30 🪐

### ### 🎯 Obiettivo  
Clonare un repository Git (`bandit29-git`) e trovare la password nascosta, che non è nella branch principale ma in un’altra branch remota.

### ### 💻 Comandi principali
```bash
# clonare il repository
git clone ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo
cd repo

# controllare branch remoti
git branch -a

# cambiare branch
git checkout dev

# leggere i file del branch alternativo
ls
cat README.md
# → [REDACTED]
```

### ### 🧠 Spiegazione
La password non è presente nel branch `master` ma nella branch `dev`.  
`git branch -a` rivela l’esistenza di branch remoti come `origin/dev` o `origin/sploits-dev`.  
Passando a `dev` (`git checkout dev`) compare un file `code/` e un `README.md` aggiornato contenente le credenziali del livello successivo.

### ### 🪄 Takeaway
- Le password possono trovarsi in branch non visibili nel default branch.  
- `git branch -a` e `git checkout <branch>` sono strumenti essenziali per esplorare repository con più versioni o branch di sviluppo.  
- L’abitudine di lasciare credenziali su branch di test o dev è un errore comune anche in progetti reali.

---

## ## 🔹 Livello 30 → 31 🕵️‍♂️

### ### 🎯 Obiettivo  
Clonare il repo Git (`bandit30-git`) e scoprire dove è nascosta la password — stavolta tramite un **tag Git**.

### ### 💻 Comandi principali
```bash
# clonare il repo
git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo
cd repo

# ispezionare il contenuto
cat README.md
# → "just an empty file... muahaha"

# controllare tag esistenti
git tag

# mostrare il contenuto del tag
git show secret
# → [REDACTED]
```

### ### 🧠 Spiegazione
Nel repository non ci sono file utili, ma è presente un tag (`secret`) che contiene un messaggio (la password).  
I tag in Git possono contenere dati testuali, e sono spesso usati per marcare versioni, ma qui fungono da *contenitore nascosto*.

### ### 🪄 Takeaway
- Non limitarti a guardare i file: usa `git tag`, `git show`, `git log`, `git reflog`.  
- Tag e branch sono ottimi nascondigli per dati sensibili.  
- Nella sicurezza reale, analizzare metadati Git è parte del code forensics.

---

## ## 🔹 Livello 31 → 32 🚀

### ### 🎯 Obiettivo  
Creare e pushare un file (`key.txt`) al repository remoto seguendo le istruzioni del `README.md`.

### ### 💻 Comandi principali
```bash
# clonare il repository
git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
cd repo

# leggere le istruzioni
cat README.md

# creare il file richiesto
echo "May I come in?" > key.txt

# bypassare .gitignore
cat .gitignore
# → *.txt
git add -f key.txt

# commit e push
git commit -m "Ciao ;) from Italia!"
git push -u origin master
```

Durante il push, il server remoto esegue un **hook di validazione**:
```bash
remote: ### Attempting to validate files... ####
remote: Well done! Here is the password for the next level:
remote: [REDACTED]
```

### ### 🧠 Spiegazione
Il `.gitignore` blocca i `.txt`, ma l’opzione `-f` (`--force`) in `git add` permette di forzare l’inclusione.  
Il server esegue uno script lato server che verifica la presenza del file `key.txt` con contenuto corretto, restituendo la password nel messaggio di risposta SSH.

### ### 🪄 Takeaway
- I *git hooks remoti* possono automatizzare controlli e validazioni — utile anche in pipeline CI/CD.  
- Forzare il commit di file ignorati (`git add -f`) è una tecnica utile ma da usare con cautela.  
- Questo livello insegna a “dialogare” con repository remoti tramite azioni di push controllate.

---

## ## 🔹 Livello 32 → 33 🧩

### ### 🎯 Obiettivo  
Uscire da una shell limitata (“UPPERCASE SHELL”) e ottenere una shell reale per leggere la password finale.

### ### 💻 Comandi principali
```bash
# all'accesso viene mostrato "WELCOME TO THE UPPERCASE SHELL"
# prompt: >>
# digitare un comando che richiami la shell reale

$0        # stampa il nome della shell
# output: sh

# da lì puoi usare comandi standard:
echo $0
ls
whoami
cat /etc/bandit_pass/bandit33
# → [REDACTED]
```

### ### 🧠 Spiegazione
Il sistema sostituisce comandi digitati con la loro versione maiuscola, rendendoli invalidi (es. `ls` → `LS`).  
Digitando `$0`, si richiama il programma corrente (in questo caso `sh`) e si ottiene una shell interattiva standard.  
Da lì si possono eseguire normalmente i comandi Linux e leggere la password finale.

### ### 🪄 Takeaway
- `$0` è una variabile bash che rappresenta il nome del programma corrente. Usarla può aprire una shell “vera” anche in ambienti limitati.  
- Molte restrizioni di shell si basano su alias o wrapper: conoscere i meccanismi base della shell aiuta a “sfuggire” da ambienti controllati.  
- È il livello finale del wargame **Bandit**: chiude il percorso mostrando che la conoscenza della shell è tanto importante quanto quella degli strumenti.

---



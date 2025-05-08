
# BlueSky Social Analisys

### Introduzione

Questo studio analizza il comportamento degli utenti sulla piattaforma BlueSky, con particolare attenzione al calcolo dell'engagement sia all'interno che all'esterno dei feed. L'obiettivo principale è comprendere come varia l'interazione e la partecipazione degli utenti in base al contesto in cui pubblicano.

Parallelamente, è stato implementato un task di classificazione che permette di assegnare ai post pubblicati al di fuori dei feed un tema tra quelli già esistenti nei feed analizzati.

**Dataset**: Bluesky Social Dataset, disponibile su Zenodo al seguente link: https://zenodo.org/records/11082879  
Percorso dataset: `/kaggle/input/bluesky-dataset/bluesky_dataset/`

---

  
### SMA - Calcolo e confonto dell'engagment  
# Notebook: bluesky-project.ipynb
Di seguito, si presenta panoramica delle fasi principali della parte di Social Media Analysis, specificando i relativi input e output.

    
#### 1. Preprocessing - Esclusione dei Post senza Interazioni Significative
Selezione dei post dal dataset mantenendo esclusivamente quelli che presentano un numero di like maggiore di zero.

**Input**:  tutti i post Bluesky memorizzati in`/kaggle/input/bluesky-dataset/bluesky_dataset/final_posts`  
**Output**: i post filtrati sono memorizzati nei file `50_2_posts.csv` e `50_posts.csv` memorizzati nella cartella `/kaggle/input/posts-bluesky`

  
#### 2. Creazione di due insiemi di post: dentro e fuori dai feed
I posts vengono separati in due insiemi:
-   feed_posts: contiene tutti i post che compaiono all'interno di uno dei feed analizzati
-   outside_feed_posts: contiene i post che sono stati pubblicati dagli utenti al di fuori dei feed

**Input**: post nei feed presenti in `/kaggle/input/bluesky-dataset/feed_posts` e l'insieme completo dei post di BlueSky elaborati nella fase precedente, utilizzato per identificare i post pubblicati al di fuori dei feed.  
**Output**: DataFrame `df_feed_posts` e `df_outside_feed_posts`

  
#### 3. Identificazione degli utenti rilevanti
Rimozione degli utenti che non hanno pubblicato almeno un post sia all'interno che all'esterno dei feed.

  
#### 4. Creazione dei grafi di interazione
Si creano due grafi di interazione, uno relativo alle interazioni dentro i feed, l'altro relativo alle interazioni fuori dai feed.
  
**Input**: DataFrame `df_feed_posts` e `df_outside_feed_posts`  
**Output**: i grafi creati vengono salvati nella cartella `/kaggle/input/interaction-graphs`

  
#### 5. Calcolo dell'engagement
Si calcola l'engagement di ogni utente presente nel grafo di interazione.

 **Input**: i grafi di interazioni caricati dalla cartella: `/kaggle/input/interaction-graphs`  
 **Output**: valori di engagement salvati in opportuni dizionari

  
#### 6. Confronto dell'engagement
Confronto dei valori di engagement fuori e dentro ai feed.
 **Input**: valori di engagement calcolati nella fase precedente  
 **Output**: istogrammi delle distribuzioni dell'engagement

  
#### 7. Calcolo avanzato dell'engagement
Si calcola e si confrontano i valori di engagement di ogni utente utilizzando altri due approcci:
1.  In base al livello di impegno richiesto per le interazioni.
2.  In base alle frequenze relative delle interazioni.

 **Input**: i grafi di interazioni caricati dalla cartella: `/kaggle/input/interaction-graphs`    
 **Output**: valori di engagement salvati in opportuni dizionari


## PArte integrativa SMA
# Notebook: SMA_graph_centrality_analysis.ipynb

In questo notebook è stata effettuata un'analisi più specifica delle interazioni tra utenti, in particolare partendo dal grafo originale, verranno generati quattro grafi distinti: uno che rappresenta le interazioni tramite commenti, uno per i repost, uno per le citazioni e uno che combina tutte le tipologie di interazione in modo "flattered". Successivamente, per ciascun grafo, sono state calcolate due misure di centralità, ovvero la degree centrality e il PageRank, e infine tali metriche sono state confrontate con i valori di engagement, per identificare eventuali correlazioni tra centralità e attività degli utenti.

### 1. Generazione dei grafi per singola interazione
Creazione dei grafi incentrati su un'unica tipologia di interazione (commenti, reply, citazioni) a partire dai grafi di interazione creati e salvati nel notebook principale.

 **Input**: grafi di interazione preesistenti contenuti nel dataset interaction-graphs
 **Output**: grafi per singola interazione, salvati nelle rispettive variabili.

### 2. Calcolo della degree centrality e del PageRank
Calcolo della degree centrality e del PageRank su tutti i grafi creati.

### 3. Calcolo dell'engagment
Calcolo dell'engagment utilizzando le stesse tecniche viste nel notebook principale.

 **Input**: i grafi di interazioni caricati dalla cartella: `/kaggle/input/interaction-graphs`  
 **Output**: valori di engagement salvati in opportuni dizionarivariabili

### 4. Confronto tra Degree Centrality / PageRank e Engagment
Confronto tra degree centrality e PageRank rispetto all'engagment calcolato con le tre tecniche precedenti. Il confronto avviene calcolando il coefficente di Pearson e visualizzando le distribuzioni delle misure.

 **Input**: valori di degree centrality, pagerank e engagment, salvati precedentemente 
 **Output**: i risultati sono visualizzati in opportune tabelle e grafici

---

   
### NLP - Classificazione dei post fuori dai feed
# Notebook: BlueSky_post_processing.ipynb
Di seguito, si presenta panoramica delle fasi principali della parte di Natural Language Processing, specificando i relativi input e output.

  
#### 1. Configurazioni
Installazione di Ollama e avvio di un server locale per utilizzarlo. 

  
#### 2. LLAMA3.1
Si utilizza il modello `llama 3.1` per classificare i post fuori dai feed utilizzando quattro tecniche di prompting: zeroshot, oneshot, twoshot e fewshot.

  
**Input**: post di BlueSky pubblicati fuori dai feed, memorizzati nel dataset `/kaggle/input/outside-feed-posts/outside_feed_limited_posts.json`.  
**Output**: le classificazioni ottenute sono state salvate nella directory `/kaggle/input/post-feed-assignments/feed_assigment_result/llama3.1` di un nuovo dataset, con un file separato per ciascuna tecnica di prompting. 

  
#### 3. QWEN 2.5
Si utilizza anche il modello `qwen 2.5` per classificare i post fuori dai feed utilizzando sempre le stesse quattro tecniche di prompting: zeroshot, oneshot, twoshot e fewshot.

**Input**: post di BlueSky pubblicati fuori dai feed, memorizzati nel dataset `/kaggle/input/outside-feed-posts/outside_feed_limited_posts.json`.  
**Output**: le classificazioni ottenute sono state salvate nella directory `/kaggle/input/post-feed-assignments/feed_assigment_result/qwen2.5`, con un file separato per ciascuna tecnica di prompting. 

  
#### 4. Confronto tra i due modelli
Si verifica in quante occasioni i due modelli sono in accordo.  
**Input**:  le classificazioni dei due modelli salvate nel dataset`/kaggle/input/post-feed-assignments/feed_assigment_result`  
**Output**: i risultati sono presentati sotto forma di tabelle.


## Parte integrativa NLP
# Notebook: NLP_in-feed_Validation.ipynb
In questa parte aggiuntiva l'obiettivo è valutare le prestazioni dei modelli sui post pubblicati all'interno dei feed di Bluesky, di cui conosciamo già il corretto assegnamento al rispettivo feed.dai feed.

### 1. Selezione dei post nei feed
Si selezionano i post provenienti dai feed di Bluesky, che saranno successivamente elaborati dai modelli. 

 **Input**: Cartella feed_posts contenente i post originali per ciascun feed, disponibile in /kaggle/input/bluesky-dataset/feed_posts.
 **Output**: File feed_posts.jsonl,successivamente salvato nel dataset feed_posts_for_validation per un utilizzo futuro.


### 2. Ottenimento delle classificazioni
I post selezionati nella fase precedente sono stati classificati utilizzando sempre i modelli LLaMA e Qwen e impiegando le tecniche di prompting zeroshot, oneshot, twoshot e fewshot.

 **Input**: file feed_posts.jsonl contenente i post nei feed selezionati nella fase precedente, path: /kaggle/input/feed-post-for-validation/feed_posts.jsonl.
 **Output**: ogni modello salva i risulati della classificazione in un file. Si ha un file per ogni modello e per ciascuna tecnica di prompting. Tutti tali file vengono infine salvati all'interno del dataset feed_classification_results per poterli utilizzare in seguito.


### 3. Confronto tra i modelli
Procediamo ora con il confronto delle classificazioni assegnate dai modelli LLaMA 3.1 e Qwen 2.5. L'analisi include sia il numero di occasioni in cui i due modelli concordano o discordano nelle loro previsioni, sia l'osservazione di metriche aggiuntive come accuracy, precision, recall e F1-score, per valutare in modo completo le prestazioni di entrambi i modelli.

 **Input**: file con le classificazioni di ciascun modello e ciascuna tecnica di prompting, contenuti nel dataset feed_classification_results.
 **Output**: i risultati vengono visualizzati in tabelle.
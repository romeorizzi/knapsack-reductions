# Problemi di decisione, di ottimizzazione, di search

Il modello del **Knapsack** è di pertinenza quando ci si trova a dover scegliere un sottoinsieme di oggetti da un insieme di oggetti dato, che assumiamo finito, di cardinalità $n$.
Magari ogni oggetto $i=1,\ldots,n$ ha un *peso* $w_i\in \mathbf{N}$ ed un *valore* $v_i\in \mathbf{N}$
e noi vogliamo scegliere un sottoinsieme $S$ di $\mathbf{N}_n := \{0,1,2,\ldots, n-1\}$
tale che $\sum_{i\in S} v_i$ sia massimo sotto il rispetto del vincolo $\sum_{i\in S} w_i \leq C$, dove $C\in \mathbf{N}$ è un parametro in input che esprime il massimo carico sostenibile, la *capacità dello zaino* (knapsack) appunto.

Per meglio analizzare la complessità di questo problema di ottimizzazione combinatorica potremmo associare ad esso il seguente problema di decisione:

**Forma decisionale dello Knapack:** in input vengono forniti $C$, $n$, e i $2\,n$ numeri naturali
$v_i$ e $w_i$, $i\in \mathbf{N}_n$, ma anche un valore target $T\in \mathbf{N}$.
La domanda è se $opt(C,n,w,v) \geq T$ valga, dove con $opt(C,n,w,v)$ indichiamo il valore della soluzione ottima, ossia il massimo valore totale acquisibile senza forare il vincolo di capacità dello zaino. Si risponda **SI** oppure **NO**.

La prima questione che vogliamo porti è quella di investigare la relazione che vi sia tra queste due versioni (forma di ottimizzazione e forma di decisione) del problema Knapsack.
L'intuizione ci dice che la forma di decisione possa solo essere più modesta della forma di ottimizzazione ma l'unico modo per argomentare questo in modo convincente e formalizzabile è quello di ridurre un problema all'altro, ossia operare secondo il seguente schema:

1. assumere di avere un oracolo (una procedura di libreria) che quando le assegnamo un'istanza del problema di ottimizzazione lo risolva;
2. implementare noi una procedura (scrivere un'algoritmo) che, data un'istanza del problema di decisione la risolva in tempo polinomiale avvalendosi di un numero al più polinomiale di chiamate all'oracolo che risolve il problema di ottimizzazione.

Di fatto in questo caso dovrebbe bastarti una sola chiamata, vedi quale parte del tuo input passare all'oracolo. (In questo primo caso di riscaldamento non sevono quindi nemmeno elaborazioni particolarmente fantasiose da effettuare sull'input per formulare domande istruttive e rivelatrici da rivolgere all'oracolo).
Già più interessante diventa elaborare l'algoritmo che dimostra la riduzione inversa (dove in pratica vanno invertiti i ruoli dei due problemi nei punti 1 e 2 dello schema dato sopra), per dimostrare che la forma di decisione e la forma di ottimizzazione di questo problema sono computazionalmente equivalenti.
Poichè crediamo che P $\neq$ NP, ossia che per mettere un problema in P esso debba averci della magia e noi si debba svelarla (ossia si debba arrivare a leggere la struttura sottostante, essenziale e talvolta più o meno invisibile agli occhi almeno finchè *lux fuit*), allora possiamo concentrarci sulla versione del problema che ci pare più semplice, con buona aspettativa che quando avremo saputo accendere la luce in quella stanza essa si propagherà in tutta la casa che è nostro compito esplorare (liberamento preso dalla metafora di Andrew Wiles https://www.youtube.com/watch?v=zhpTtJDBFg4). Partiamo dal locale dove ci appare più probabile riuscire a trovare un primo interruttore.
Una nostra tesi in questo interruttore è che la fucina metodologica della complessità computazionale è un grandioso strumento anche per guidare i nostri passi e le noste scelte in queste esplorazioni, e dovrebbe pertanto essere ben conosciuta da ogni matematico.

Goal 1: scrivere una procedura che risolva la forma decisione del problema avendo accesso (tramite callbacks) ad un oracolo che risolva per noi istanze del problema in forma di ottimizzazione.

Goal 2: raggiungi questo goal se riesci a fare quanto nel punto precedente avvalendoti di una sola chiamata all'oracolo.

Goal 3: scrivere una procedura che risolva il problema di ottimizzazione Knapsack avendo accesso (tramite callbacks) ad un oracolo che risolva la forma decisionale del problema. Le chiamate non devono essere più di
$\sum_{i\in \mathbf{N}_n} v_i$ ma dovrebbe esserti possibile anche in al più $opt(C,n,w,v) +1$ chiamate.

Si noti che un algoritmo che effetua $opt(C,n,w,v) +1$ chiamate non è a rigore polinomiale nel numero di bit necessari per descrivere l'istanza in input in quanto i numeri $v_i$ e $w_i$, $i\in \mathbf{N}_n$, saranno stati convenientemente scritti in binario, utilizzando al più $O(n \log \sum_{i\in \mathbf{N}_n} v_i + w_i)$ inchiostro.
Perchè la nostra riduzione sia davvero polinomiale e non solo pseudo-polinomiale, e per poter quindi concludere sull'equivalenza polinomiale dei due problemi e derivarne correttamente le implicazioni metodologiche su come affrontarne lo studio, alziamo allora l'asticella nel seguente goal.

Goal 4: raggiungi questo goal se realizzi quanto richiesto per il goal precedente ma avvalendoti di al più $O(n \log \sum_{i\in \mathbf{N}_n} v_i + w_i)$ chiamate all'oracolo.

In realtà nelle applicazioni non siamo poi nemmeno tanto interessati a solo scoprire il valore di una soluzione ottima. Ciò che vorremo è che una soluzione ottima ci venisse consegnata.
Vorremmo cioè un algoritmo che ci restituisca un sotoinsieme $S$ di $\mathbf{N}_n$ che è soluzione ammissibile per il problema (ossia $\sum_{i\in S} w_i \leq C$) e per il quale si abbia $\sum_{i\in S} v_i = opt(C,n,w,v)$.

Un tale algoritmo risolverebbe la versione di costruzione del problema, che, potete vedere, appare ancora un attimo più ambiziosa della forma di ottimizzazione.
Perchè in casi come questo si considera legittimo concentrarsi sulle forme di ottimizzazione o di decisione quando quella che ci interessa è in fondo la forma di costruzione?
E' così perchè mostrare l'equivalenza di tutte queste forme non è difficile.
Ci limiteremo a dimostrare l'equivalenza tra la forma di costruzione e quella di ottimizzazione dato che quest'ultima abbiamo già visto essere equivalente a quella di decisione.

Goal 5: scrivere una procedura che risolva il problema di ottimizzazione Knapsack avendo accesso (tramite callbacks) ad un oracolo che risolva la forma di costruzione. Questa è la riduzione facile, vi consentiamo di fare una sola domanda all'oracolo.

Goal 6: scrivere una procedura che costruisca e restituisca una soluzione ottima per ogni possibile istanza del problema Knapsack assegnatale in input. Il vostro algorimo può interrogare (tramite callbacks) un oracolo che risolva la forma di ottimizzazione del problema Knapsack. Le chiamate non devono essere più di
$n$.

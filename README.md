# PiSerra
Software di controllo di un box PiSerra

Per ulteriore documentazione sul software originale visita:

     https://www.patreon.com/Pigrow

     https://www.reddit.com/r/Pigrow/


#Come costruire una PiSerra

Per prima cosa hai bisogno dell'hardware, segui la guida alla creazione disponibile su www.reddit.com/r/Pigrow/wiki/index per avere un'idea.

Quando avrai un raspberry funzionante collegato alla tua rete, ai sensori e ai relè appropriati, potrai usare la GUI  o connetterti al pi tramite un terminale della riga di comando SSH o con il pi collegato ad uno schermo, tastiera e topo.
#Primo passo  
Supponendo che tu abbia un Pi connesso a internet e che tu abbia effettuato l'accesso tramite ssh o utilizzandolo con una tastiera e uno schermo collegati;

Per prima cosa è necessario installare il software, per farlo clona la repository git usando il comando
     git clone https://github.com/elbrt99/PiSerra

questo copierà tutti i file all'interno di /home/pi/Pigrow, ora avvia il programma di setup:

    cd Pigrow/scripts/config
    ./setup.py
    
Questo script apre un menu di testo che offre un certo numero di opzioni, il primo è installa dipendenze. Selezionalo digitando 1 e premendo Invio, questo scaricherà e installerà tutte le risorse e le dipendenze per la PiSerra, potrebbe richiedere un po 'di tempo.

La tua PiSerra è pronta all'uso ora!

#Settaggio Webcam e inizio Timelapse
esegui lo script di configurazione della webcam che si trova nella cartella /Pigrow/scripts/config/ e segui le istruzioni finché non sei soddisfatto dell'immagine catturata dalla tua fotocamera e quando sei nelle condizioni di illuminazione in cui intendi utilizzarla - ricorda SALVA le impostazioni una volta che ti gasano.

esegui setup.py e seleziona cron run scripts, quindi seleziona script ripetuti e segui le istruzioni per scegliere lo script di acquisizione della videocamera e la frequenza di ripetizione della tua scelta - al momento la scelta migliore è probabilmente camcap.py ogni cinque minuti.

una volta impostato, il tuo pi inizierà a scattare immagini ogni N minuti, questo continuerà ad accadere anche se riavvii il tuo pi, quindi per fermarlo dovrai tornare in setup.py e rimuovere il cron job che attiva la fotocamera dal menù apposito.

Dal pi è possibile eseguire Pigrow/scripts/visulaisation/timelapse_assemble.py per costruire un timelapse, eseguendo lo script con il flag -h otterrai un elenco completo delle opzioni della riga di comando e alcune istruzioni per l'uso. Questo script può anche essere eseguito da una macchina Linux con MPV installato quando i file sono stati scaricati manualmente o possono essere scaricati automaticamente usando la GUI remota.

#scripts da chiamare da cron;

Per aggiungere semplicemente uno dei seguenti programmi timelapse al cron devi:

       sudo crontab -e

e nella parte inferiore del file dopo la spiegazione di come funziona aggiungere la linea:

       */1 * * * * python /home/pi/Pigrow/scripts/camcap_text_simple.py

Questo eseguirà lo script e catturerà un'immagine ogni minuto.

              Camera -
                     - camcap.py - cattura una singola immagine
                     - camcap_text_simple.py - cattura un'immagine e ci aggiunge la data e i dati dei sensori 
            !!!!     - camcap_text_colour.py - cambia il colore del testo in base ai dati forniti dai sensori


Per settare un ciclo di luce 12:12 basta aggiungere semplicemente due righe:

     0 10 * * * python /home/pi/pigrow3/lamp_on.py
     0 22 * * * python /home/pi/pigrow3/lamp_off.py     

Questo accenderà la lampada alle dieci del mattino e la spegnerà alle dieci di sera. Cerca di non accendere le ventole e le lampade esattamente nello stesso momento, funzionerà ma entrambi gli elementi causano picchi di potenza che, se combinati, potrebbero far scattare il tuo RCD.
              Relay -
                    - lamp_on / off - attiva lamp relay
                    - fan_on / off - per gestione manuale ventole
                    - heat_on / off - per gestione manuale riscaldatore
                    - dehumi_on / off - per gestione manuale deumidificatore

Questi script vengono eseguiti periodicamente come con gli script della fotocamera per verificare lo stato di salute del pi o di un amico, è possibile controllare molti pi chiamando lo script molte volte.

        Sanity Check -
                     - self_awareness - sensor log, camera, health check
                     - nosey_neighbour - check on a brother pigrow

Scripts da eseguire continuamente:

          autorunlog.py - registra i log dei sensori in un file, switcha le ventole o i riscaldatori in accordo con i sensori.

------- Useful information which might be needed before release of setup scripts

#Installare software di Terze parti:

--Adafruit DHT sensor drivers,

    git clone https://github.com/adafruit/Adafruit_Python_DHT.git
    cd Adafruit_Python_DHT
    sudo apt-get update
    sudo apt-get install build-essential python-dev python-openssl
    sudo python setup.py install

      -Più informazioni su, https://learn.adafruit.com/dht-humidity-sensing-on-raspberry-pi-with-gdocs-logging/software-install-updated

--From linux Repository

     sudo apt-get install uvccapture           #per usare la camera

     sudo pip install pexpect                  #da usare quando sono attivate più piSerre

     sudo apt-get install python-matplotlib    #per creare grafici

     sudo apt-get install sshpass              #per scaricare i file e le immagini

     sudo apt install mpv                      #per assemblare i video in timelapse

     pip install python-crontab                #per configurare la temporizzazione di relay e scripts

-su sistemi diversi da Raspberry dovrai probabilmente scaricare

     sudo apt-get install python-pip           #per aggiungere moduli Python
     pip install pillow                        #PIL the Python Image Library (for camera capture scripts that edit image)
     sudo apt-get install gpicview             #used by the config program (non-vital, easy to change-out in the code)


#struttura cartelle

            mkdir /home/pi/Pigrow/logs
            mkdir /home/pi/Pigrow/config
            mkdir /home/pi/Pigrow/graphs
            mkdir /home/pi/cam_caps

#per settare eventi temporizzati (in modo semi manuale)

     crontab -e

le linee da aggiungere saranno del tipo...

     0 7 * * * python /home/pi/pigrow2/lampon.py           #turns lamp relay on at 7 am
     0 1 * * * python /home/pi/pigrow2/lampoff.py          #turns lamp relay off at 1 am

     */5 * * * * python /home/pi/Pigrow/scripts/pi_eye_logger.py    #runs pi_eye monitoring script every 5min

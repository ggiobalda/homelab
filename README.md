# Home Server Infrastructure
Repo per poter replicare velocemente infrastruttura dell'home server (Ubuntu Server) lanciando i servizi dockerizzati e non. I dati hostati non sono ovviamente qui.

Struttura archiviazione suggerita:
- in home (sperabilmente SSD) una cartella `~/homelab` dove clonare questa repo con i docker files e una cartella `~/appdata` dove tenere dati locali dei servizi (database, cache, config, etc)
- in un HDD i dati hostati

In entrambe creare una directory per servizio. 

Sia Jellyfin (`/dev/dri`) sia Immich (`quicksync` in `extends`) presuppongono una CPU Intel con grafica integrata

## 1. Ripristino servizi dockerizzati
Servizi inclusi:
- Audiobookshelf (Audiolibri + Podcast)
- Immich (Foto)
- Jellyfin (Film + Serie Tv)
- Kiwix (Offline Wiki)
- Navidrome (Musica)
- QbitTorrent (Torrent client)

1. Clona questo repository in `~/homelab`
2. Crea la directory `~/appdata` con una sottodirectory per servizio (eccetto kiwix - non ne ha bisogno in quanto stateless)
	```
	mkdir ~/appdata
	mkdir ~/appdata/<servizio>
	```
3. Copia il file `.env` e configura i segreti
	```bash
	cd ~/homelab
	cp .env.example .env
	nano .env
   	```
4. Avvia i servizi:
	```bash
	docker compose up -d
	```

## 2. Servizi di sistema
Da reinstallare manualmente sul nuovo server.

### Tailscale
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

### Cockpit
Interfaccia web sulla porta 9090.
```bash
sudo apt update && sudo apt install -y cockpit
sudo systemctl enable --now cockpit.socket
```

### Syncthing
Interfaccia web sulla porta 8384. Dati e configurazione in `~/syncthing`.
```bash
sudo apt install syncthing
sudo systemctl enable --now syncthing@$USER.service
```

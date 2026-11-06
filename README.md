# Home Server Infrastructure
Repo per poter replicare velocemente infrastruttura dell'home server lanciando i servizi dockerizzati e non. I dati hostati non sono ovviamente qui.

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
3. Copia i file `.env`:
	```bash
	cp ~/homelab/.env.example ~/homelab/.env
  	cp ~/homelab/immich/.env.example ~/homelab/immich/.env
  	cp ~/homelab/qbittorrent/.env.example ~/homelab/qbittorrent/.env
   	```
4. Configura i segreti:
	- Percorso `APPDATA_PATH` e `DATA_PATH` in tutti e tre
	- Database password in `~/homelab/immich/.env`
	- Info VPN in `~/homelab/qbittorrent/.env`
5. Crea un link simbolico a `~/homelab/.env` per ogni servizio (eccetto immich e qbittorrent):
	```bash
	ln -s ~/homelab/.env ~/homelab/<servizio>/.env 
	```
6. Avvia ogni servizio:
	```bash
	cd ~/homelab/<servizio>
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

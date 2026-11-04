# Home Server Infrastructure
Repo per poter replicare velocemente infrastruttura dell'home server lanciando i servizi dockerizzati e non. I dati hostati non sono ovviamente qui.

Struttura archiviazione:
- **SSD (App Data):** `/home/gio/appdata/`
- **HDD (Media):** `/mnt/data/`

## 1. Ripristino servizi dockerizzati
Servizi inclusi:
- Audiobookshelf (Audiolibri + Podcast)
- Immich (Foto)
- Jellyfin (Film + Serie Tv)
- Kiwix (Offline Wiki)
- Navidrome (Musica)

1. Installa Docker e Git
2. Clona questo repository in `~/homelab`
3. Crea la directory `~/appdata` con una sottodirectory per servizio (eccetto kiwix - non ne ha bisogno in quanto stateless)
4. Copia i file `.env`:
	```bash
	cp ~/homelab/.env.example ~/homelab/.env
  	cp ~/homelab/immich/.env.example ~/homelab/immich/.env
   	```
5. Configura i segreti:
	- Percorso APPDATA in `~/homelab/.env`
	- Percorso APPDATA e database password in `~/homelab/immich/.env`
6. Link simbolico a `~/homelab/.env` per ogni servizio eccetto immich:
	```bash
	ln -s ~/homelab/.env ~/homelab/<servizio>/.env 
	```
7. Avvia ogni servizio:
	```bash
	cd ~/homelab/<servizio>
	docker compose up -d
	```

## 2. Servizi di sistema
Da reinstallare manualmente sul nuovo server.

### Tailscale
```bash
curl -fsSL [https://tailscale.com/install.sh](https://tailscale.com/install.sh) | sh
sudo tailscale up
```

### Cockpit
Interfaccia web sulla porta 9090.
```bash
sudo apt update && sudo apt install -y cockpit
sudo systemctl enable --now cockpit.socket
```

### Syncthing
Interfaccia web sulla porta 8384. Dati e configurazione in `/home/gio/syncthing`.
```bash
sudo apt install syncthing
sudo systemctl enable --now syncthing@gio.service
```

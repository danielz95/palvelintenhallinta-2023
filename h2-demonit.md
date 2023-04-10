# h2 Demonit tehtävän dokumentointi

## x) Tiivistelmä tehtävän ohjeistuksesta, sekä alkutietoja
Tässä osiossa kävin läpi tehtävän ohjeistusta Daemonien hallintaan Salt:in avulla.
Ensin kertasin viime viikon 'h1 salt' tehtävän dokumentointia mielen virkistämiseksi, sekä luin Karvisen luomaa pikaohjetta
asiaa koskien <a href=https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/> 
(Salt Quickstart) </a>

Tämän viikon varsinaisen tehtävän tarkoitus olisi asentaa ja konfiguroida paketteja (PKG-File service) hallita Daemoneja ja 
konfiguroida SSH asetuksia. Luettuani Karvisen 
<a href=https://terokarvinen.com/2018/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/> 
(PKG, Daemons and SSH with salt) </a> ohjeistuksen läpi, tehtävässä edetään vaiheittain:

- Luodaan .sls tiedosto, joka asentaa minion koneille automaattisesti 'sshd' daemonia
- Konfiguroidaan 'sshd' portit niin, että se kuuntelee tarvittavia portteja yhteyksien toimivuuden testaamiseen.
- Ajetaan .sls tiedosto asentamaan minion koneisiin 'sshd' daemonia luomallamme konfiguraatioilla (state.apply).
- Testaamalla, että kustomoitu asennuksemme toimii niin kuin pitää ottamalla SSH yhteys minion koneelle valistemastamme portista.

### Käyttöympäristön specsit
Tehtävän tekemisen Host OS:nä toimii Windows 10 64-bit, johon asennettu VirtualBox Ubuntu Desktop 22.04 LTS. Tähän
olen kytkenyt 'VM Nesting' asetusta päälle, jolla pystyn asentamaan sen sisälle meidän Vagrant testikoneiden ympäristöä.
## a) SSH palvelimen asennus käsin, ja porttien testaaminen.

Asensin aikaisemman tehtävän 'Vagrantfilen' pohjaa käyttäen asentamaan testikoneet 'tmaster' 't001' ja 't002' komennolla:

    vagrant up

Kun asennukset olivat valmiita otin SSH yhteyden 'tmaster' koneeseen ja kävin hyväksyttämässä avaimet minion koneille:

![image](https://user-images.githubusercontent.com/128583292/230804536-00528009-28de-4598-81ff-51f53a899767.png)

Seuraavaksi asensin 'tmaster' koneelle 'openssh-server' palvelimen:

    sudo apt install openssh-server
    
Asennuksen jälkeen tarkistin, että palvelin on käynnissä:

    sudo systemctl status sshd

![image](https://user-images.githubusercontent.com/128583292/230804816-bfde2063-c240-4a4f-9826-835cf4674524.png)

#### Yllä olevassa kuvassa nähdään, kuinka oletusportti 22 kuuntelee SSH liikennettä.

Muutin seuraavaksi 'tmaster' koneen '/etc/ssh/sshd_config' tiedostoa lisäämällä:

    Port: 8888
    
![image](https://user-images.githubusercontent.com/128583292/230811482-4aea89ff-1a6b-48bc-8220-00b14cb90b0e.png)

Nyt kun tiedostoa tallennetaan, sekä käynnistetään 'sshd' service uudestaan ja tarkistetaan sen tilaa:

    sudo systemctl restart sshd
    sudo systemctl status sshd

![image](https://user-images.githubusercontent.com/128583292/230812068-b7b6b681-cf06-45f5-970a-3181e2e927b2.png)

#### Nähdään, kuinka portti 8888 myös kuuntelee SSH liikennettä.

## Ongelmakohta

Yritin yhdistää Host VM (Ubuntu Desktop) koneen kautta ssh:lla vagrantin 'tmaster' koneeseen:

    ssh tmaster@192.168.56.3
    
    ssh -p 8888 tmaster@192.168.56.3

![image](https://user-images.githubusercontent.com/128583292/230813405-067b1407-c583-465f-bb68-aeaabe2349b7.png)

#### Yllä olevaan virheeseen en ole löytänyt vielä ratkaisua, mutta porttiasetukset kuitenkin toimivat, sillä väärän portin syöttäminen olisi tuottanut seuraavanlaista tulosta:

![image](https://user-images.githubusercontent.com/128583292/230813669-9e896bc7-6aa6-4550-a314-91b8e27cebc6.png)

## b) SSH konfiguraation automatisointi Saltilla

Luodaan .sls tiedosto 'tmaster' koneelle käyttäen harjoituksen valmista pohjaa. Loin ensin hakemiston '/srv/salt/' ja siihen
'sshd.sls' tiedoston, sekä 'sshd_config' tiedoston
      
    sudo mkdir -p /srv/salt/
    sudoedit /srv/salt/sshd.sls
    sudoedit /srv/salt/sshd_config
    
#### sshd.sls tiedoston sisään:

    openssh-server:
     pkg.installed
    /etc/ssh/sshd_config:
     file.managed:
       - source: salt://sshd_config
    sshd:
     service.running:
       - watch:
         - file: /etc/ssh/sshd_config
         
Tuo 'file.managed' tiedon alla tulee olemaan lähdetiedosto, mihin muutokset ajetaan minion-koneille, eli tässä tapausessa luomamme '/srv/salt/sshd_config' tiedosto.


#### sshd_config tiedoston sisään:

    Port 8888
    Protocol 2
    HostKey /etc/ssh/ssh_host_rsa_key
    HostKey /etc/ssh/ssh_host_dsa_key
    HostKey /etc/ssh/ssh_host_ecdsa_key
    HostKey /etc/ssh/ssh_host_ed25519_key
    UsePrivilegeSeparation yes
    KeyRegenerationInterval 3600
    ServerKeyBits 1024
    SyslogFacility AUTH
    LogLevel INFO
    LoginGraceTime 120
    PermitRootLogin prohibit-password
    StrictModes yes
    RSAAuthentication yes
    PubkeyAuthentication yes
    IgnoreRhosts yes
    RhostsRSAAuthentication no
    HostbasedAuthentication no
    PermitEmptyPasswords no
    ChallengeResponseAuthentication no
    X11Forwarding yes
    X11DisplayOffset 10
    PrintMotd no
    PrintLastLog yes
    TCPKeepAlive yes
    AcceptEnv LANG LC_*
    Subsystem sftp /usr/lib/openssh/sftp-server
    UsePAM yes
      
Nyt on luotu alla olevat tiedot yllä olevilla sisällöillä:

![image](https://user-images.githubusercontent.com/128583292/230817176-3e321563-da95-4e33-8aab-b04a142223b0.png)

Seuraavaksi on aika ajaa konfiguraatiota:
    
    sudo salt '*' state.apply sshd

Tuloksen yhteenvetona oli seuraavanlainen monemmille 't001' ja 't002' minion koneille:

  ![image](https://user-images.githubusercontent.com/128583292/230817679-5717c9dd-e5c6-4e3f-91fa-47f1b0196554.png)
  
## c) SSH-state asetuksien muutokset, vahvistukset ja tarkistukset

Edellisessä vaiheessa ajettiin uudet 'sshd' konfiguraatiot minion-koneille. Nyt kun **state.apply** komentoa ajetaan
uudestaan, tulokseen ei tule muutoksia:

![image](https://user-images.githubusercontent.com/128583292/230819634-6de8581d-8333-42a3-ab92-93cf76eb1e1c.png)

### sshd_config tiedoston muokkaus, muutoksien tallennus ja niiden ajaminen:

Seuraavaksi lisään yhden rivin meidän '/srv/salt/sshd_config' tiedostoon:

    sudoedit /srv/salt/sshd_config
    
    Port: 22
    
![image](https://user-images.githubusercontent.com/128583292/230820114-730a8729-cd8c-4e63-a849-27cf97be2b7a.png)

Tallennuksen jälkeen ajoin **state.apply** komennon uudestaan seuraavilla tuloksilla:

![image](https://user-images.githubusercontent.com/128583292/230820878-111f8843-1068-4d7d-92a6-e76f7b1a6b99.png)
![image](https://user-images.githubusercontent.com/128583292/230820943-c05c8f11-8057-4851-aca4-7802ab53b69b.png)

Tässä nähdään 3 tehtyä toimenpidettä yhteensä, josta 2 oli havaittu muutoksia.
- pkg.installed (Ei muutoksia, sshd on jo asennettuna ja ajan tasalla)
- file.managed (Muutos havaittu, muokattiin sshd_config tiedostoa ja lisättiin rivi 'Port: 22')
- service.running (Muutos havaittu, sshd käynnistetty uudelleen muutoksia havaitessa.)

Yritin vielä testaa ssh-yhteyden 't001' minion koneeseen, mutta ei löytynyt sopivaa salasanaa, eikä publickey:ta.
Porttiyhteys kuitenkin toimii:

![image](https://user-images.githubusercontent.com/128583292/230823544-8ff49d5a-6193-4288-a8c0-1c3873a60409.png)




# Lähteet

Karvinen T., 2023, <a href=https://terokarvinen.com/2018/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/> 
PKG-File-Service, Daemons with Salt, Change SSH Server Port </a>

Karvinen T., 2023, <a href=https://terokarvinen.com/2023/salt-vagrant/> Salt Vagrant </a>

Hardy K., 2020, <a href=https://github.com/harkx/saltstack-cheatsheet#packages> Saltstack cheatsheet </a>

# h1 Suolan kotitehtävä

## x) Tiivistelmä alkuohjeistuksesta
Alkuun loin omaan GitHub sivuuni repositorion kurssiin liittyvistä dokumenteista (tämä kyseinen repo) seuraamalla
valmiiksi luotuja ohjeita (https://terokarvinen.com/2023/create-a-web-page-using-github/)
Ohjeet olivat selkeät ja yksinkertaiset, jolla sain:
- Luotua GitHubiin oma repo
- Luotua repoon uusi tiedosto GNU v3 lisenssillä ja ReadMe.txt tiedostolla
- Tämän .md sivun tehtyä ja julkaistua.

Seuraavaksi, luin ohjeet varsinaisesta Salt Vagrantin asennuksesta
ja käytöstä (https://terokarvinen.com/2023/salt-vagrant/)

Ojeistuksessa edettiin selkeästi ja järjestyksessä:
- Saltin Vagrantin asennus ja konfigurointi
- Testikoneiden konfigurointi (t001, t002, tmaster)
- Testikoneiden käynnistys ja Slave-Master hierarkian vahvistus
- Minion-testikoneiden komentaminen ja komentojen ajamista
- "Indepotent" komentojen ajaminen ja testaaminen
- IaC (Infrastructure as Code) skriptin luonti ja testaus
- Lopuksi, koko Salt-virtuaalikonestruktuurin tuhoaminen

## a) Debian ja Salt Vagrant asennus
 Latasin (https://www.debian.org/download) ja asensin Debian 11.6.0 version omalla Windows 10 työpöytäkoneellani ja kävin sen asennusprosessia läpi käyttäen VirtualBox:ia.
Kun asennus oli valmis, käynnistin Debianin ja avasi terminaalin, jolloin asentaakseen Vagrantin ja micro-tekstieditorin suoritin komennot:
    
    sudo apt-get update
    
    sudo apt install -y vagrant micro
## b) Kolmen koneen verkon asennus
Loin alkuun testausympäristöön tarvittavan hakemiston kotihakemistooni:

    mkdir saltdemo; cd saltdemo
    
Loin saltdemo hakemistoon tiedoston nimeltä 'Vagrantfile' ja kopioin siihen kurssimateriaalista saadun valmiin scriptin uuden tiedoston sisällöksi <a href=https://terokarvinen.com/2023/salt-vagrant/> (Karvinen 2023) </a>:
    
    nano Vagrantfile
    
Jonka jälkeen liitin alla olevan scriptin tiedostoon, sekä tallensin:
    
    # -*- mode: ruby -*-
    # vi: set ft=ruby :
    # Copyright 2014-2023 Tero Karvinen http://TeroKarvinen.com
    
    $minion = <<MINION
    sudo apt-get update
    sudo apt-get -qy install salt-minion
    echo "master: 192.168.12.3">/etc/salt/minion
    sudo service salt-minion restart
    echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
    MINION

    $master = <<MASTER
    sudo apt-get update
    sudo apt-get -qy install salt-master
    echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
    MASTER

    Vagrant.configure("2") do |config|
	    config.vm.box = "debian/bullseye64"

	    config.vm.define "t001" do |t001|
		    t001.vm.provision :shell, inline: $minion
		    t001.vm.network "private_network", ip: "192.168.12.100"
		    t001.vm.hostname = "t001"
	    end

	    config.vm.define "t002" do |t002|
		    t002.vm.provision :shell, inline: $minion
		    t002.vm.network "private_network", ip: "192.168.12.102"
		    t002.vm.hostname = "t002"
        end

	    config.vm.define "tmaster", primary: true do |tmaster|
		    tmaster.vm.provision :shell, inline: $master
		    tmaster.vm.network "private_network", ip: "192.168.12.3"
		    tmaster.vm.hostname = "tmaster"
	    end
    end

Yritin tämän jälkeen käynnistää skriptiä käyttämällä komentoa:

    vagrant up
    
Sen sijaan, eteen tuli seuraavanlaiset virhetekstit, mitkä viittaavat vahvasti 'libvirt' palvelun puuttumiseen:

![image](https://user-images.githubusercontent.com/128583292/229397789-cc4258aa-a545-4054-94d4-a12caea738ff.png)

Ongelman ratkaiseminen yritin lukuisia eri ratkaisukeinoja mm.
- Asentamalla 'libvirt' paketteja ja lisäosia 
- Asentamalla vagrantia ja virtualboxia uudestaan
- Tarkistamalla, ettei 'Vagrantfile' tiedostossa ollut kirjoitusvirheitä

Huomasin myös tässä vaiheessa, ettei minulla ollut VirtualBox asetuksissa 'VM Nesting' asetusta päällä, eli virtualisointia virtuaalikoneen sisällä, mikä varmasti vaikutti asiaan...

En keksinyt mitään toimivaa ratkaisua ongelmaan, joten päätin kokeilla Vagrant:ia toisella Ubuntu virtuaalikoneellani
# Tehtävän jatkaminen Ubuntu Desktop koneella

Tein aikaisemmat vaiheet Ubuntu koneellani virhenäkymään asti. Laitoin sormet ristiin ja suoritin komennon:

    vagrant up
    
Palautukseen tuli seuraavanlainen virhe:

![image](https://user-images.githubusercontent.com/128583292/229411123-3486e1e6-971c-4dbd-b8a3-be88547bcc6b.png)

Muokkasin 'Vagrantfile' tiedoston koneiden IP-osoitteet, jotta se vastaa vaadittua IP-avaruutta:

![image](https://user-images.githubusercontent.com/128583292/229411834-485f3113-4d34-4d0f-93b6-2749072f679d.png)

Suoritin komennon uudestaan ja se viihdoin toimii ongelmatta, sekä asennus suoriutui loppuun.

## c) Minion-koneiden hyväksyttäminen ja testaus

Otin yhteyden 'tmaster' Master-koneelle komennolla:

    vagrant ssh tmaster
    
![image](https://user-images.githubusercontent.com/128583292/229413048-03529bb0-0a96-4a67-91d1-01f0f1af057e.png)

Seuraavaksi yritin hyväksyttää 't001' ja 't002' testikoneiden avaimet mutta se palautti virheen:

    The key glob '*' does not match any unaccepted keys.

Tässä tarkistin ja huomasin vielä 'Vagrantfile' konfiguraationsta, että 'master' IP-osoite ei täsmännyt meidän käyttämään osoitteseen:

![image](https://user-images.githubusercontent.com/128583292/229420984-c0a55286-4e60-4c04-b304-5aa9f313f503.png)

Muutin tämän oikeaksi, ja otin ssh yhteyden 't001' ja 't002' koneisiin, sekä korjasin niiden /etc/salt/minion tiedostojen väärät 'master' IP-osoitteet myös. Tämän jälkeen suoritin uudelleenkäynnistyksen kaikista testikoneista komennolla:

    vagrant reload
    
Rebootin jälkeen yritin uudelleen hyväksyttää 'tmaster' koneelta 'minion' koneiden avaimet:
    
![image](https://user-images.githubusercontent.com/128583292/229422169-4186a3dc-5ccb-4c83-99f0-53aac107cbe8.png)

Tämän jälkeen sain testattua ja todettua yhteyden myös toimivaksi:

![image](https://user-images.githubusercontent.com/128583292/229423146-3210445c-2790-4c8b-b82e-933527729cb8.png)

## d) Eri tilojen/komentojen testaus

cmd.run komennon käyttö:

![image](https://user-images.githubusercontent.com/128583292/229422648-de32f775-b1c0-4abb-bc3d-675ee3fe79b3.png)

CPU ja ipv4 tiedot grains.item avulla:

![image](https://user-images.githubusercontent.com/128583292/229424774-fa96af51-a165-444c-bc09-e8dc5f866ac3.png)

'testi.txt' tiedoston luominen minion koneille:

![image](https://user-images.githubusercontent.com/128583292/229432464-b845ffe0-8538-492c-b9a7-8fefdc3fe5fa.png)
![image](https://user-images.githubusercontent.com/128583292/229432618-3857d22a-6a1c-48bb-84e9-da49a436ba8d.png)

Uuden käyttäjän luominen user.present avulla:

![image](https://user-images.githubusercontent.com/128583292/229435037-0e6b1497-e3ab-4607-be7f-6e6a411f18ad.png)

Sekä käyttäjän poistaminen user.absent avulla:

![image](https://user-images.githubusercontent.com/128583292/229436202-2741ed1c-b302-4e99-887d-30ae24c06160.png)

## e) Infrastructure as Code - tekstitiedoston luonti ja ajaminen

Luodaan kansio, mistä tekstitiedosto ajetaan:

    sudo mkdir -p /srv/salt/testi
    sudoedit /srv/salt/testi/init.sls
    
Muokatessaan init.sls tiedostoa, syötetään alla olevat tekstirivit:

    /tmp/testi:
      file.managed

Tallennuksen jälkeen, kun ajetaan alla oleva komento:

    sudo salt '*' state.apply testi

Tässä nähdään, kun yllä oleva scripti luo minion koneille /tmp/testi tiedoston

![image](https://user-images.githubusercontent.com/128583292/229447720-839c49ab-2144-485b-a8b3-14db15947f9a.png)

## Pakettien asennus IaC:n avulla

Seuraavaksi teen scriptin, jossa pakotetaan 'vim' tekstieditorin asennus kaikkiin minion koneisiin. Käytin apuna saltproject.io:n artikkelin ohjeistusta <a href=https://docs.saltproject.io/en/latest/topics/tutorials/walkthrough.html> (Salt in 10 minutes)</a>

Aloitin luomalla /srv/salt/ kansioon uuden 'vim.sls' tiedoston:

    sudoedit /srv/salt/vim.sls

Tiedostoon syötin:

    vim:
      pkg.installed

Tallennuksen jälkeen ajoin komennon:
    
    sudo salt '*' state apply vim

![image](https://user-images.githubusercontent.com/128583292/229451856-c48bd63b-cecd-4a90-88ea-478a5038b2e1.png)
![image](https://user-images.githubusercontent.com/128583292/229451919-bc9d8222-f65e-481a-bbcf-55c4eaad3736.png)


## Vagrant koneiden tuhoaminen

Harjoituksen jälkeen, voidaan tuhota kaikki vagrant koneet komennolla:

    vagrant destroy

Tämä poistaa kaikki asennukset ja tiedostot kyseisistä koneista, ja tuoreeseen alkuun pääsee taas komennolla:

    vagrant up 

# Lähteet

Tero Karvinen, 2023 (https://terokarvinen.com/2023/salt-vagrant/)

Salt in 10 minutes, 2023 (https://docs.saltproject.io/en/latest/topics/tutorials/walkthrough.html)



    








    
    

# h6 puolikas (Miniprojekti):n ensimmäinen versio
Tässä projektissa tulen asentamaan Google Chrome selaimen muokatuilla oletuskonfiguraatiolla eri käyttöjärjestelmille (Windows, Debian) Saltin avulla.

Oletuskonfiguraatiossa tulee olemaan Haaga-Helian kotisivu, sekä valmiiksi asennettuja kirjainmerkkejä (Haaga-Helian moodle, Tuudo ja 3amk sivustot) Asennetaan myös oletuksena BlockSite-laajennusta, mikä estää
tietyille sivustoille pääsyä.

## Isäntäkoneen specsit:
- CPU: AMD Ryzen 5 3600, 6C/12T @ 4,3 GHz
- GPU: Radeon RX 6800 XT
- RAM: Kingston HyperX 16GB 3000Mhz DDR4
- HDD: 1 TB Samsung QVO SSD

### Toteutan projektin VMWare Workstation Player 17 ympäristössä jossa toimii:

- Salt Master : Linux Ubuntu Desktop 22.04 LTS
- Salt Minion 1: Windows 11 Enterprise
- Salt Minion 2: Linux Debian 11

### Tässä piti alunperin myös lisätä macOS yhtenä käyttöjärjestelmänä, mutta sen virtualisointi osoittautui todella hankalaksi ja tuskaiseksi. Suurimpana ongelmana oli, että macOS virtualisointi ei onnistunut omalla AMD Ryzen CPU:lla. Päätin tästä syystä jättää sen osion pois.

Tästä löytyy 'initial_preferences' tiedoston pohja, jonka olin ladannut <a href=https://storage.googleapis.com/support-kms-prod/Y2dvgj4vUhoxiiLqzWmhjpLW3b3rBaLWuW8L> Suoraan Googlelta </a>

Saltin hallinnoitiin löytyvät vinkit Windows:illa ja Linux:illa yhdellä .sls state tiedostolla löysin <a href=https://terokarvinen.com/2018/configure-windows-and-linux-with-salt-jinja-if-else-and-grains/>tästä </a>

# Testiympäristön rakentaminen

### Ubuntu master

Tämän Ubuntu Desktop version olin käyttänyt aikaisemmissa kurssin harjoituksissa, joten siinä oli valmiiksi asennettu ja konfiguroitu Salt-master ja Salt-minion palvelut.

### Windows minion

Aloitin asentamalla Windowsiin Salt Minion palvelun, joka ottaa yhteyden Ubuntu Master koneeseen. Tämän vaiheen olen tehnyt aikaisemmassa kurssitehtävässä <a href=https://github.com/danielz95/palvelintenhallinta-2023/blob/main/h5-Salt%20Windows.md> h5 Salt Windows </a>. Olen myös ladannut tämän aikaisemman tehtävän yhteydessä winrepon windows minionille, jonka kautta pystyn Saltilla asentamaan Chrome-selaimen.

### Debian minion

Asensin Debianin VMware:lla ja käynnistin sen.

Asensin tämän jälkeen salt-minion palvelun:

    sudo apt update
    sudo apt install salt-minion

Kävin tämän jälkeen muokkaamassa /etc/salt/minion konfiguraatiotiedostoa ja lisäsin siihen masterin osoitteen:

![image](https://user-images.githubusercontent.com/128583292/236911375-c67f4cec-990f-4378-b5d4-2a8a2932675d.png)

Tallensin muutokset ja muokkasin myös /etc/salt/minion_id tiedoston, eli vaihdoin Deb minionin nimeksi **minion2** (minion1 on varattu Windows minion nimeksi):

![image](https://user-images.githubusercontent.com/128583292/236911748-e69be7a3-a179-4a14-a1db-23a5d2fb6b84.png)

Käynnistin salt-minion palvelun uudelleen:

    sudo systemctl restart salt-minion

Tarkistin, että demoni on käynnissä:

    sudo systemctl status salt-minion

![image](https://user-images.githubusercontent.com/128583292/236912292-55f39c0f-181a-4622-a22c-48f8afe94281.png)

# Testiympäristön testaus

Seuraavaksi kävin Ubuntu Master koneelta hyväksyttämässä salt avaimet minioneilta:

      sudo salt-key -L
      sudo salt-key -a minion2
      
![image](https://user-images.githubusercontent.com/128583292/236912825-30533f7c-5e23-4347-a18e-f2c377f740e4.png)

**Yllä näkyy 'debian' minionin hyväksymätön avain, joka on jäänyt siitä ennen kuin olin muokannut Debian minionin 'minion_id' nimeä. Sitä ei tarvitse siis huomioida. 'minion1' on Windows minioni, jonka olin hyväksyttänyt jo aiemmin. **

Nyt testasin yhteyden molempiin minioniin:

![image](https://user-images.githubusercontent.com/128583292/236913882-2c340c10-91eb-4fe8-8688-f234cba04454.png)

Molemmat minionit ovat nyt hallittavissa.





# Lähteet
initial_preferencees documentation, Google Support, https://support.google.com/chrome/a/answer/187948?hl=en#zippy=%2Cstep-create-the-initial-preferences-file

Control Windows and Linux with single salt module, Karvinen. T 2018, https://terokarvinen.com/2018/configure-windows-and-linux-with-salt-jinja-if-else-and-grains/

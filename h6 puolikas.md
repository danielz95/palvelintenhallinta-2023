# h6 puolikas (Miniprojekti):n ensimmäinen versio
Tässä projektissa tulen asentamaan Google Chrome selaimen muokatuilla oletuskonfiguraatiolla eri käyttöjärjestelmille (Windows, Debian) Saltin avulla.

Oletuskonfiguraatiossa tulee olemaan Haaga-Helian kotisivu, sekä valmiiksi asennettuja kirjainmerkkejä (Haaga-Helian moodle, Tuudo ja 3amk sivustot) Asennetaan myös oletuksena BlockSite-laajennusta, mikä estää
tietyille sivustoille pääsyä.

## Isäntäkoneen specsit:
- CPU: AMD Ryzen 7 5800X3D, 8C/16T @ 3,6 GHz
- GPU: Radeon RX 6800 XT
- RAM: Kingston HyperX 16GB 3200Mhz DDR4
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

# Ensin käsin

Aloitin asentamalla Ubuntu Masterille Chrome-selaimen hakemalla asennuspakettia googlelta:

    wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
    
Asensin paketin komennolla:
    
    sudo dpkg -i google-chrome-stable_current_amd64.deb

![image](https://user-images.githubusercontent.com/128583292/236978765-d0e8eeed-90c2-4edf-b696-302d78da2632.png)

Chrome on nyt asennettu.

### Chrome policies ja 'managed_policies.json' konfigurointitiedostot

Seuraavaksi meidän täytyy luoda uudet kansiot, johon chromen policy:t sijoitetaan:

    cd /etc/opt/
    sudo mkdir -p chrome/policies/managed

![image](https://user-images.githubusercontent.com/128583292/236980779-3dccd67b-bd42-4948-94b2-d193c1e6fe0b.png)

Loin seuraavaksi 'managed_policies.json' tiedoston polkuun 'etc/opt/chrome/policies/managed'

    cd /etc/opt/chrome/policies/managed
    sudoedit managed_policies.json

Kopioin esimerkkitiedostoa 'managed_policies.json' <a href=https://github.com/google/ChromeBrowserEnterprise/blob/main/docs/policy_examples/managed_policies.json> tästä</a>, jonka olen muokannut omiin tarpeisiin:

    {
        "HomepageLocation": "https://www.haaga-helia.fi",
        "HomepageIsNewTabPage": false,
        "DefaultBrowserSettingEnabled": true,
        "RestoreOnStartup": 1,
        "SafeBrowsingProtectionLevel": 1,
        "MaxConnectionsPerProxy": 99,
        "ShowHomeButton": true,
        "URLBlocklist": [
            "www.youtube.com",
            "www.wikipedia.org",
            "www.bing.com"
        ],
        "ExtensionInstallForcelist": [
            "noondiphcddnnabmjcihcjfbhfklnnep",
            "djflhoibgkdhkhhcedjiklpkjnoahfmg"
        ],
        "ManagedBookmarks": [
            {
            "name": "Tuudo",
            "url": "tuudo.fi"
            },
            {
            "name": "3AMK",
            "url": "3amk.fi"
            },
            {
            "name": "Haaga-Helia",
            "url": "haaga-helia.fi"
            }
        ]
    }

Tämän jälkeen tallensin tiedoston ja avasin Ubuntu Masterilla Chromen. Tarkistin käytäntöjen tilaa syöttämällä hakukenttään:

    chrome://policy

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/5760e7e2-99bd-4f49-a6b7-7d211b4b727c)

### Yllä näkyy, kuinka käytännöt ovat voimassa 'Status: OK' indikaattorilla. Ainut ongelma mihin törmäsin oli, ettei kotisivua (www.haaga-helia.fi) saa automaattisesti näkyville, kun avaa selainta. Se toimii ainoastaan, kun painetaan Homepage-nappia hakukentän vierestä : 

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/ab15b8ab-58d0-49b1-8046-2a325a359986)
![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/1a96156c-2e5c-4996-8c38-331eb00dd56e)

Alla näkyy myös 'Managed Bookmarks' kirjainmerkit, mitkä ovat asetettu:

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/5197c835-5d04-4706-9151-1f782fd61553)

Testasin vielä, että 'URLBlocklist' käytäntö toimii niin kuin pitää:

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/fa998135-8f87-4460-8985-a4968dbf98a1)

### Alkuperäinen suunnitelma oli ladata BlockSite lisäosa, konfiguroida sen estämään tietyt sivustot ja puskemaan lisäosan automaattista asennusta ja konffausta minioneille, mutta en löytänyt järkevää tapaa toteuttaa sen. Tyydyin tällöin käyttämään 'URLBlocklist' käytäntöä, mikä periaatteessa ajaa saman asian.

# Chrome:n ja sen 'managed_policies.json' käytäntöjen puskeminen minioneille

## Debian Minion

Aloitin kokeilemalla ensin luomaan idempotenttia Debian Minionille.

Kopioin aikaisemmin ladatun chromen .deb asennuspakettia ja managed_policies.json tiedostoa  'srv/salt/chrome' polkuun

    sudo cp google-chrome-stable_current_amd64.deb /srv/salt/chrome
    sudo cp /etc/opt/chrome/policies/managed/managed_policies.json /srv/salt/chrome

Loin tämän jälkeen .sls tiedoston, joka asentaa Chromen meidän valmiista .deb paketista ja kopioi 'managed_policies.json' tiedoston oikeaan polkuun:

    chrome:
      pkg.installed:
        - sources:
        - google-chrome-stable: salt://chrome/google-chrome-stable_current_amd64.deb 
      - mode: 755

    /etc/opt/chrome/policies/managed/managed_policies.json:
      file.managed:
      - source: salt://chrome/managed_policies.json
      - makedirs: true
      - mode: 755

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/e64f9e6b-281f-418d-b0dd-1e1a411b40d6)

Tämän jälkeen, ajoin 'state.apply' komentoa minioneille:

    sudo salt '*' state.apply chrome

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/ac2adb6e-34fd-4c72-a0b8-4deccb46cf10)

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/19ebbd06-b7b0-43ca-9485-0f55b4687af0)

### minion2 (debian) sai paketin asennettua ja tiedoston luotua, mutta minion1 (windows) ei, tämä siis odotettua, sillä siihen ei toimi Linux:in polut ja pkg.installed tilaa. Luodaan ne seuraavassa vaiheessa.

Testasin vielä Debian Minionilla, että Chrome on asentunut ja käytännöt tulleet voimaan:

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/ed532db5-934c-4270-b805-93fe70f6a5b0)

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/ac18ee86-4145-417c-ad79-50796b424987)
![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/96487f3c-9ef5-40dd-98d6-e6fc1b0d0bfb)
![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/e06cddd4-be89-494d-a0a5-21a59b0cf59c)

## Windows Minion

Aloitin lataamalla asennuspakettia Chrome Enterprise versiolle, jonka mukana tulee tarvittavat '.adml' ja '.admx' Windows administrative templates-tiedostot:

    wget https://dl.google.com/tag/s/appguid%253D%257B8A69D345-D564-463C-AFF1-A69D9E530F96%257D%2526iid%253D%257BBEF3DB5A-5C0B-4098-B932-87EC614379B7%257D%2526lang%253Den%2526browser%253D4%2526usagestats%253D1%2526appname%253DGoogle%252520Chrome%2526needsadmin%253Dtrue%2526ap%253Dx64-stable-statsdef_1%2526brand%253DGCEB/dl/chrome/install/GoogleChromeEnterpriseBundle64.zip?_ga%3D2.8891187.708273100.1528207374-1188218225.1527264447

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/657f2d99-267a-4090-98cd-084284b334b5)

Latauksen jälkeen purin .zip tiedoston:

    unzip 'GoogleChromeEnterpriseBundle64.zip?_ga=2.8891187.708273100.1528207374-1188218225.1527264447'

Tämän jälkeen kopioin 4 kpl tarvittavat .adml ja .admx tiedostot jotka löytyivät purkamisen jälkeen ~/Configuration/admx kansiosta '/srv/salt/chrome' polkuun:

    cp Configuration/admx/chrome.admx srv/salt/chrome
    cp Configuration/admx/google.admx srv/salt/chrome
    cp Configuration/admx/en-US/chrome.adml srv/salt/chrome
    cp Configuration/admx/en-US/google.adml srv/salt/chrome

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/dbb6fbd5-9ff8-4d84-9b1a-8456be817ad9)


Kun kopioinnit olivat tehty, muokkasin 'init.sls' tiedoston ajamaan 'file.managed' toimintoa Windows Minionin polkuihin:

![image](https://github.com/danielz95/palvelintenhallinta-2023/assets/128583292/b5625653-1e5b-451a-a087-b8c6ef069540)

### Käytän siis yhteistä init.sls tiedostoa myös Linux:in kanssa, joten 'state.apply' saattaa näyttää tarkoituksella virheitä.



### Source file salt://source_file not found in saltenv 'base'





# Lähteet
initial_preferencees documentation, Google Support, https://support.google.com/chrome/a/answer/187948?hl=en#zippy=%2Cstep-create-the-initial-preferences-file

Control Windows and Linux with single salt module, Karvinen. T 2018, https://terokarvinen.com/2018/configure-windows-and-linux-with-salt-jinja-if-else-and-grains/

Chrome on Linux, WikiHow, https://www.wikihow.com/Install-Google-Chrome-Using-Terminal-on-Linux

Chrome policies, Google Support, https://support.google.com/chrome/a/answer/9025903?hl=en&ref_topic=9025817&sjid=10060259247724311218-EU

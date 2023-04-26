# h4 Komennukset

Tässä harjoituksessa on käytetty aiemmin luotua VirtualBox Ubuntu Desktop käyttöympäristöä.

# a) Skriptin luonti ja sen jakaminen käyttäjille

Aloitin luomalla kotihakemistoon hakemiston 'shine' ja sen sisään 'shine' skriptitiedoston:

    mkdir shine
    nano shine

![image](https://user-images.githubusercontent.com/128583292/234601225-52c14dad-9aa5-473d-8205-9a7e36a115dc.png)

Yritin ajaa skriptin, mutta oikeudet eivät riittäneet:

![image](https://user-images.githubusercontent.com/128583292/234601415-c5493bf0-87a2-447a-88b0-61948baf5b7a.png)

Muutin tällöin oikeudet niin, että user ja group pystyvät ajamaan skriptiä, jonka jälkeen ajaminen toimii:

    sudo chmod ug+x shine

![image](https://user-images.githubusercontent.com/128583292/234601838-640e0e0c-4413-46b1-8488-cc6292cc6a44.png)

Kopioin tämän jälkeen skriptin '/usr/local/bin' polkuun:

    sudo cp shine /usr/local/bin

Oikeudet eivät edelleen riittäneet skriptin ajamiseen kaikilta käyttäjiltä, joten lisäsin vielä 'others' käyttäjille oikeudet ajaa skirptiä:

    sudo chmod o+x shine

![image](https://user-images.githubusercontent.com/128583292/234604163-8a071587-2caf-4007-b14c-0179488d54a9.png)

Näin skriptin jakaminen ja ajaminen onnistuu kaikilta käyttäjiltä.

# b) Python skriptin luonti ja jakaminen käyttäjille

Loin kotihakemistoon 'scripts' hakemiston ja sen sisään 'hello.py' skriptitiedoston:

    mkdir scripts
    nano hello.py

Muutin vielä oikeudet niin, että kaikki käyttäjät pystyvät ajamaan sitä:

    sudo chmod ugo+x hello.py
    
    ./hello.py

![image](https://user-images.githubusercontent.com/128583292/234648127-2c7dd603-b90d-42ea-b55e-f0c975037548.png)
    
Kopioin 'hello.py' tiedoston polkuun '/usr/local/bin' tiedostoon, jonka jälkeen pystyn ajamaan skriptin mistä vaan:

    sudo cp hello.py /usr/local/bin

![image](https://user-images.githubusercontent.com/128583292/234615888-0d7d979a-e1cf-4b81-b05d-5100d857f93d.png)

# c) Skriptien automatisointi Salt minion koneille

Aloitin kokeilemalla yhteyttä vagrant isäntäkoneen ja minionien välillä:

    sudo salt '*' test.ping
    
![image](https://user-images.githubusercontent.com/128583292/234629827-02eca694-5bbc-4c22-b2f0-931897dc1f54.png)

Loin aikaisemmit luodut scriptitiedostot 'hello.py' ja 'shine' vagrant 'tmaster' koneelle, sillä en saanut niitä isäntäkoneeltani SSH:lla siirrettyä (publickey error).

Vaihdoin vielä niihin oikeudet aikaisemman mukaisesti:

    mkdir scripts
    nano shine
    nano hello.py
    chmod ugo+x shine hello.py

![image](https://user-images.githubusercontent.com/128583292/234639210-1e535029-ec13-4a7e-9077-7697cfeda623.png)

**HUOM! yllä olevissa skripteissä virheet poluissa, puuttuu alussa / merkki #! jälkeen, huomattu ja korjattu jälkeenpäin.**

Tämän jälkeen loin 'tmaster' koneelle uuden hakemiston 'scripts' polkuun '/srv/salt/

Kopioin tämän jälkeen skriptitiedostot siihen:

    sudo cp hello.py shine /srv/salt/scripts

Loin 'init.sls' tiedoston /srv/salt/scripts polkuun ja syötin alla olevat tiedot:

    /usr/local/bin/shine:
      file.managed:
        - source: salt://scripts/shine
        - mode: '0755'

    /usr/local/bin/hello.py:
      file.managed:
        - source: salt://scripts/hello.py
        - mode: '0755'

![image](https://user-images.githubusercontent.com/128583292/234643066-1cff129e-9892-4283-b56e-04d41c874919.png)

Lopuksi ajoin nämä tiedot minion koneille:
 
    sudo salt '*' state.apply scripts
 
![image](https://user-images.githubusercontent.com/128583292/234644804-a99e2508-b4e8-44ec-922a-894c13e7e94c.png)

Kuten näkyy, skriptitiedostot ovat onnistuneesti ajettu minion-koneille ja niissä pitäisi olla riittävät oikeudet niiden ajamiseen.

Testasin vielä, että skriptit toimivat etänä ja paikallisesti:

    sudo salt '*' cmd.run 'shine'
    sudo salt '*' cmd.run 'hello.py'

![image](https://user-images.githubusercontent.com/128583292/234649115-fd1bf1dd-4646-401a-9480-62225bf9dc42.png)

![image](https://user-images.githubusercontent.com/128583292/234654021-3a5bc40a-a7fa-4635-84fa-a5f134597776.png)

# d) Yhden binäärin ohjelman asennus minion koneille

Tässä asennan micro-tekstieditoria salt:ia hyödyntäen

Latasin <a href=https://github.com/zyedidia/micro/releases/tag/v2.0.11> tästä </a> micro-tekstieditorin alla olevan asennuspaketin:

micro-2.0.11-linux-arm.tar.gz

Käytin 'wget' komentoa sen lataamiseen 'tmaster' koneelle:

![image](https://user-images.githubusercontent.com/128583292/234655852-b41d9806-c0b7-4e41-9772-c7fde8494fbf.png)

![image](https://user-images.githubusercontent.com/128583292/234655981-8b19b54b-88be-4cb3-97e4-93dd3ab81819.png)

**Korjauksena ylläoleva toimenpide tehty 'tmaster' koneella, ei 't001' koneella.**


    


 

    
    

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

Purin tiedoston 'tmaster' kotihakemistoonsa:

    tar xvf micro-2.0.11-linux-arm64.tar

![image](https://user-images.githubusercontent.com/128583292/234675194-a5da500a-310a-46ff-b3d7-82e33f382221.png)

Tein tämän jälkeen uuden 'micro' kansion '/srv/salt' hakemistoon ja kopioin micro binääritiedoston siihen:

    sudo mkdir /srv/salt/micro
    sudo cp micro /srv/salt/micro

![image](https://user-images.githubusercontent.com/128583292/234676330-34f3c38a-4da1-4128-959a-1411cae5dbfe.png)

Loin tämän jälkeen 'init.sls' tiedoston '/srv/salt/micro' hakemistoon

    sudoedit init.sls
    
 ![image](https://user-images.githubusercontent.com/128583292/234679316-e5c0ff5f-97f1-4059-beaa-7dca1ba56589.png)

Tallensin 'init.sls' tiedoston ja ajoin komennon siirtää micro:n minion koneille:
 
    sudo salt '*' state.apply micro
 
 ![image](https://user-images.githubusercontent.com/128583292/234679209-6f92304b-2b4d-4bd2-b0cb-c1b253db0086.png)

Yritin tämän jälkeen käynnistää micron minion koneilla, mutta tuli error (Permission denied):

![image](https://user-images.githubusercontent.com/128583292/234679837-c26d3b7a-0b09-451f-9d8b-5ef9db3bb605.png)

Muutin 'init.sls' tiedoston hieman, että se antaa riittävät oikeudet micron ajamiseen minion koneilla:

![image](https://user-images.githubusercontent.com/128583292/234680162-9662bfe6-1c34-41eb-8a38-1d76bb9cb5f1.png)

Ajoit state.apply salt komennon uudestaan, jotta uusi 'init.sls' tila päivittyisi uusilla oikeuksilla ja yritin ajaa cmd.run komentoa sen jälkeen uudestaan:

![image](https://user-images.githubusercontent.com/128583292/234681912-28ac2f5e-7a1f-495a-9e8b-7b73df91f9a2.png)

Tällä kertaa error:in syy on Exec format error. **Tämä todennäköisesti johtuu siitä, että olin ladannut väärän version microsta, joka ei käy minun linux versiolleni. Täytyy kokeilla ladata eri versiota ja kokeilla uudestaan.**
 
# Lähteet

h4 Komennukset, Tero Karvinen, <a> https://terokarvinen.com/2023/palvelinten-hallinta-2023-kevat/ </a>

Salt tips, saltproject.io, <a> https://docs.saltproject.io/en/getstarted/config/files.html </a>

Error troubleshooting, Superuser.com, <a> https://superuser.com/questions/435988/how-can-i-resolve-the-error-cannot-execute-binary-file </a>


    


 

    
    

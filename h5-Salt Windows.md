# x)

Tässä harjoituksessa käytetään Salt:ia ohjaamaan 2 Windows konetta, jotka toimivat minion koneina.

Tehtävän ohjeistuksesta kävin läpi:
 
 - Salt Slave-master asennus ja konfigurointi linuxille. Itse käytän Ubuntu Desktop 22.04 versiota.
 
 - Salt Minion asentamista Windowsin koneille ja niiden GPG avainten konfigurointi ohjausta varten. Uudemman version Salt
 minion/master toimii parhaiten Windowsin ohjaamiseen ( <a href=https://repo.saltproject.io/](https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html>
  Windows Salt Minion Download </a>)
 
 - Master koneen IP osoitteen selvittäminen ja ilmoittaminen Windows Minion koneille, sekä GPG avainten hyväksynnät.

 - Pakettihallinnan asennus Windowsille (git repos)

 - Pakettien lataaminen ja asentaminen käsin Windowsille (pkg.install), sekä idempotenttien käyttäminen .sls tiedostojen avulla

 - PowerShellin käyttö ja Salt:in käyttö paikallisesti Windows Minion koneilla.
 
 - Windows Server Minionien asennus, konfigurointi, sekä erilaiset mahdollisuudet ja toiminnon sen etäohjauksessa. 
 
Harjoituksessa tulen siis käyttämään Master koneena VMware Ubuntu Desktop 22.04 LTS versiota, ja Minion koneena VMware Winodws 11 Enterprise (Evaluation) versiota.
 
# a) Salt:in asennus Windowsille

Aloitin avaamalla Ubuntu Desktop koneeni ja asentamalla siihen 'salt-master' ja 'salt-minion' paketit:

    sudo apt update
    sudo apt install salt-master salt-minion

![image](https://user-images.githubusercontent.com/128583292/235765243-0c7887e6-d307-4576-8760-81e1a05a0f35.png)

Selvitin Master koneen IP osoitteen:

    ip -4 addr
    
![image](https://user-images.githubusercontent.com/128583292/235773963-a620c6c5-6a18-4536-9c01-c674c5ecc54a.png)

#### Yllä oleva Master IP osoite on 192.168.0.102



Latasin Windows 11 Enterprise VMware ympäristön suoraan Microsoftin sivulta https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/
 
![image](https://user-images.githubusercontent.com/128583292/235736725-3225a20f-dd10-4653-bb3c-9b92238ecd5c.png)

Tämän jälkeen avasin Win 11 virtuaalikoneen ja latasin Salt Minion:in linkistä: (https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html)

Lataamisen jälkeen ajoin asennusohjelman Windowsin Salt Minionille:

#### Asennusohjelmassa painetaan 'Next' niin kauan, kunnes pyydetään syöttämään 'Master IP' ja 'Minion Name', joiden syöttämisen jälkeen valitaan 'Install'.

![image](https://user-images.githubusercontent.com/128583292/235772826-327d91a2-0ee6-46c6-8539-9c6ae86bab19.png)
![image](https://user-images.githubusercontent.com/128583292/235775769-8a6f588e-754e-407f-9da2-7798a03f3adc.png)
![image](https://user-images.githubusercontent.com/128583292/235777059-731276d6-ef57-4760-9e36-39cfcf09dd15.png)

N. 2 minuutin asennuksen jälkeen, Salt Minion asentuu Windows koneelle. Nyt voidaan testata yhteyden Masterin kautta ottaa
yhteys minioniin.

Hyväksytin ensin Salt Minionin avaimen Master koneelta:

    sudo salt-key -A

![image](https://user-images.githubusercontent.com/128583292/235779083-c2d7fcf5-e2e8-4428-8f03-69a0ff166560.png)

Seuraavaksi testasin yhteyden:

    sudo salt minion1 test.ping
    sudo salt '*' cmd.run 'echo Hello!'

![image](https://user-images.githubusercontent.com/128583292/235780194-94df494a-af04-4128-9557-28b9c9413c75.png)

Yhteys Windows Minion ja Ubuntu Masterin välillä on testattu onnistuneesti!

# b) Saltin käyttö Windows Minion koneelta paikallisesti

Seuraavaksi käytetään Salt:ia paikallisesti, ilman masteria Windows Minion koneella.

Ensin avataan PowerShell administrator oikeuksilla:

### 'Windows' nappi -> kirjoita hakukenttään 'PowerShell' -> oik. klik. PS-ikonia -> Run as Administrator
![image](https://user-images.githubusercontent.com/128583292/235783107-b6bc8371-f2b5-4f47-a5e0-0bdc506537d6.png)
![image](https://user-images.githubusercontent.com/128583292/235783179-87e8145e-ce81-4775-a9d4-c360ac989db2.png)

Testasin paikallisia Salt komentoja:

    salt-call --local state.single cmd.run 'whoami'
    
    salt-call --local grains.item cpu_model

![image](https://user-images.githubusercontent.com/128583292/235791317-228f5e61-36c4-45a9-abc2-3b8f8bf4b6b5.png)
![image](https://user-images.githubusercontent.com/128583292/235792229-2927b0c2-f672-4e43-9847-4ffe74a6b606.png)

Kuten näkyy, komennot menevät läpi ja Salt näyttää toimivan paikallisesti.

# c) 'Hello windows!' tiedosto Windows Minion koneelle Saltilla

Tässä teen Hello_windows.txt tiedoston Master koneelta, jonka pusken Minion koneelle käyttäen idempotentti-tilaa.

Loin ensin Master koneelle uuden '/srv/salt/win' tiedostopolun, johon tein 'Hello_windows.txt' tiedoston ja sen lisäksi 'init.sls' tiedoston.

    sudo mkdir /srv/salt/win/
    
![image](https://user-images.githubusercontent.com/128583292/235812640-31cfb9e1-c236-4377-9cc8-683723752705.png)

    sudoedit Hello_windows.txt
    
![image](https://user-images.githubusercontent.com/128583292/235812730-54a5c1ba-5e7d-4e1e-bc4d-fb02a635eb66.png)

    sudoedit init.sls

![image](https://user-images.githubusercontent.com/128583292/235812882-18b27181-05b2-4216-824b-22f4cbe0e8c2.png)

Tallensin tiedostot ja ajoin tämän jälkeen 'state.apply' komentoa minionille:

    sudo salt minion1 state.apply 'win'
    
![image](https://user-images.githubusercontent.com/128583292/235811410-d0351c78-71fb-4415-a5ad-63bdb43f80dd.png)

Ajoin komennon vielä toisen kerran varmistaakseen idempotenttisuuden toimivuutta (ottamalla kaikki minionit tällä kertaa):

    sudo salt '*' state.apply 'win'

![image](https://user-images.githubusercontent.com/128583292/235813226-313fd7db-bbfc-4614-8f6e-424084186903.png)

Kävin tarkistamassa Minion koneen puolella tiedostoa, ja sieltähän se löytyy oikeasta paikasta:

![image](https://user-images.githubusercontent.com/128583292/235813599-70ff71f9-168b-445c-a1d6-2405c53298f4.png)


# d) Windows ohjelmien asennus Saltin kautta

Aloitin asentamalla Windowsiin minioniin Git paketinhallinnan saltin kautta:

    sudo salt-run winrepo.update_git_repos

Sain tulokseksi 'Permission Denied' errorin, joten muutin 'srv/salt/win' polun oikeudet:

    sudo chown root.salt /srv/salt/win
    sudo chmod ug+rwx /srv/salt/win
 
# Lähteet

Infra as Code course 2023, Karvinen T., https://terokarvinen.com/2023/palvelinten-hallinta-2023-kevat/

Control Windows with Salt, Karvinen T., https://terokarvinen.com/2018/control-windows-with-salt/

Salt minion windows '.exe' file, saltproject.io, https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html

Windows 11 Enterprise VM download, Microsoft, https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/


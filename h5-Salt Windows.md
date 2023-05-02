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

Tämän jälkeen avasin Win 11 virtuaalikoneen ja latasin Salt Minion:in, sekä .gpg avaimen linkistä: (https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html)

Lataamisen jälkeen ajoin asennusohjelman Windowsin Salt Minionille:

Asennusohjelmassa painetaan 'Next' niin kauan, kunnes pyydetään syöttämään 'Master IP' ja Minion Name, jonka syöttämisen jälkeen valitaan 'Install'.

![image](https://user-images.githubusercontent.com/128583292/235772826-327d91a2-0ee6-46c6-8539-9c6ae86bab19.png)
![image](https://user-images.githubusercontent.com/128583292/235775769-8a6f588e-754e-407f-9da2-7798a03f3adc.png)
![image](https://user-images.githubusercontent.com/128583292/235777059-731276d6-ef57-4760-9e36-39cfcf09dd15.png)



 

# a) Online

Aloitin luomalla omaan GitHub:iini uuden repositoryn 'sunny_summer' README.md ja GNU v3.0 lisenssillä:

![image](https://user-images.githubusercontent.com/128583292/232328050-da40f3fb-e971-4f4c-a7e6-a1382937ed99.png)

#### Tässä valmis repository:

![image](https://user-images.githubusercontent.com/128583292/232484057-b321555d-2bd3-4099-98e9-c8c59960e9f8.png)

# b) Dolly

Tässä vaiheessa kloonaan repositoryn SSH:n linkin kautta VBox Ubuntu Desktop koneeni kotihakemistoon komennolla:

    git clone git@github.com:danielz95/sunny_summer.git
    
![image](https://user-images.githubusercontent.com/128583292/232502264-986c85ca-6733-4427-868f-69244f04af88.png)

#### Repon muokkaaminen ja muokkauksien tallentaminen

Seuraavaksi loin repoon uuden testitiedoston 'test.txt' ja lisäsin siihen tekstiä:

![image](https://user-images.githubusercontent.com/128583292/232502596-8a1ba9e5-6af9-4d70-aced-c9d37c85f4c9.png)

Muutoksien tallentaminen ja puskeminen palvelimelle (GitHub) tapahtui komennolla:

    git add . && git commit; git pull && git push 
    
Vastauksena käyttäjää, mistä muutoksia halutaan vahvistaa ei tunnisteta:

![image](https://user-images.githubusercontent.com/128583292/232503900-a1cdacda-1692-45cf-aa8e-e292510ad1b5.png)

Tämän ratkaisin lisäämällä muokkaajan tunnistamiseen sähköpostiosoitteen ja käyttäjätunnuksen ajamalla komennot:

    git config --global user.email danielz95@github.com
    git config --global user.name danielz95

Ajoin komennon uudestaan, joka tällä kertaa 'push && pull' osiot onnistuivat:

![image](https://user-images.githubusercontent.com/128583292/232506836-d94ddd82-c075-4caa-a343-dedc6023b9ea.png)

Tarkistin vielä, että GitHub:iin on ilmestynyt myös tehdyt muutokset:

![image](https://user-images.githubusercontent.com/128583292/232509470-2e03e6e8-d31d-4b31-a2f2-94f88d50f803.png)
![image](https://user-images.githubusercontent.com/128583292/232509608-a56f61c6-7ba5-4337-8be3-408cfd119f4b.png)

# c) Doh!

Seuraavaksi teen muokkauksia 'sunny_summer' repoon, mutta huomaankin, etten haluakkaan tallentaa niitä.

![image](https://user-images.githubusercontent.com/128583292/232512492-448d0a56-c5ff-4ae4-aa8b-8d37d0db0552.png)

Tein muokkaukset 'README.md' tiedostoon ja se näkyy myös ajamalla komennon:

    git status

Peruutin muutokset ajamalla:

    git reset --hard

Tämän jälkeen katsoin statusta uudestaan, sekä 'README.md' tiedostoa ja huomaan, että muutokset ovat hävinneet.

![image](https://user-images.githubusercontent.com/128583292/232514277-c04e3a8e-227d-4d5c-bd57-dbe123846299.png)

# d) Tukki

Tässä tarkistin aikaisemmin b-tehtävässä tehdyn 'commit' toiminnon lokia komennolla:

    git log --patch

![image](https://user-images.githubusercontent.com/128583292/232508043-d06a4060-04a6-4f45-9e46-26f0d24da28d.png)

Tässä näkyy, kuka (Author) on tehnyt muutoksia, milloin ne on tehty (Date) ja mikä oli muutoksien tarkoitus (Commit Message) 

Alimmilla riveillä näkyy, mihin tiedostoihin muutokset koskevat ja mitä muutoksia kyseisiin tiedostoihin on tehty.


# h6 puolikas (Miniprojekti):n ensimmäinen versio
Tässä projektissa tulen asentamaan Google Chrome selaimen muokatuilla oletuskonfiguraatiolla eri käyttöjärjestelmille (Windows, Debian) Saltin avulla.

Oletuskonfiguraatiossa tulee olemaan Haaga-Helian kotisivu, sekä valmiiksi asennettuja kirjainmerkkejä (Haaga-Helian moodle, Tuudo ja 3amk sivustot) Asennetaan myös oletuksena BlockSite-laajennusta, mikä estää
tietyille sivustoille pääsyä.

## Isäntäkoneen specsit:
- CPU: AMD Ryzen 5 3600, 6C/12T @ 4,3 GHz
- GPU: Radeon RX 6800 XT
- RAM: Kingston HyperX 16GB 3000Mhz DDR4
- HDD: 1 TB Samsung QVO SSD

### Toteutan projektin VMWare ympäristössä jossa toimii:

- Salt Master : Linux Ubuntu Desktop 22.04 LTS
- Salt Minion 1: Windows 11 Enterprise
- Salt Minion 2: Linux Debian 11

### Tässä piti alunperin myös lisätä macOS yhtenä käyttöjärjestelmänä, mutta sen virtualisointi osoittautui todella hankalaksi ja tuskaiseksi. Suurimpana ongelmana oli, että macOS virtualisointi ei onnistunut omalla AMD Ryzen CPU:lla. Päätin tästä syystä jättää sen osion pois.

Tästä löytyy 'initial_preferences' tiedoston pohja, jonka olin ladannut <a href=https://storage.googleapis.com/support-kms-prod/Y2dvgj4vUhoxiiLqzWmhjpLW3b3rBaLWuW8L> Suoraan Googlelta </a>

Saltin hallinnoitiin löytyvät vinkit Windows:illa ja Linux:illa yhdellä .sls state tiedostolla löysin <a href=https://terokarvinen.com/2018/configure-windows-and-linux-with-salt-jinja-if-else-and-grains/>tästä </a>


# Lähteet
initial_preferencees documentation, Google Support, https://support.google.com/chrome/a/answer/187948?hl=en#zippy=%2Cstep-create-the-initial-preferences-file

Control Windows and Linux with single salt module, Karvinen. T 2018, https://terokarvinen.com/2018/configure-windows-and-linux-with-salt-jinja-if-else-and-grains/

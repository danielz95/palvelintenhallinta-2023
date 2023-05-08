# h6 puolikas (Miniprojekti):n ensimmäinen versio
Tässä projektissa tulen asentamaan Google Chrome selaimen muokatuilla oletuskonfiguraatiolla eri käyttöjärjestelmille (Windows, Debian) Saltin avulla.

Oletuskonfiguraatiossa tulee olemaan Haaga-Helian kotisivu, sekä valmiiksi asennettuja kirjainmerkkejä (Haaga-Helian moodle, Tuudo ja 3amk sivustot) Asennetaan myös oletuksena BlockSite-laajennusta, mikä estää
tietyille sivustoille pääsyä.

### Toteutan projektin VMWare ympäristössä jossa toimii:

- Salt Master : Linux Ubuntu Desktop 22.04 LTS
- Salt Minion 1: Windows 11 Enterprise
- Salt Minion 2: Linux Debian 11

### Tässä piti alunperin myös lisätä macOS yhtenä käyttöjärjestelmänä, mutta sen virtualisointi osoittautui todella hankalaksi ja tuskaiseksi. Päätin tästä syystä jättää sen osion pois.

Tästä löytyy 'initial_preferences' tiedoston pohja, jonka olin ladannut <a href=https://storage.googleapis.com/support-kms-prod/Y2dvgj4vUhoxiiLqzWmhjpLW3b3rBaLWuW8L> Suoraan Googlelta </a>


# Lähteet
initial_preferencees documentation, Google Support, https://support.google.com/chrome/a/answer/187948?hl=en#zippy=%2Cstep-create-the-initial-preferences-file

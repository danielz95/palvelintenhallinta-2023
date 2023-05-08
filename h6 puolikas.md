# h6 puolikas (Miniprojekti):n ensimmäinen versio
Tässä projektissa tulen asentamaan Google Chrome selaimen muokatuilla oletuskonfiguraatiolla eri käyttöjärjestelmille (Windows, Ubuntu, MacOS) Saltin avulla.

Oletuskonfiguraatiossa tulee olemaan Haaga-Helian kotisivu, sekä valmiiksi asennettuja kirjainmerkkejä (Haaga-Helian moodle, Tuudo ja 3amk sivustot) Asennetaan myös oletuksena BlockSite-laajennusta, mikä estää
tietyille sivustoille pääsyä.

###Toteutan projektin VMWare ympäristössä jossa toimii:

- Salt Master : Linux Ubuntu Desktop 22.04 LTS
- Salt Minion 1: Windows 11 Enterprise
- Salt Minion 2: MacOS Monterey
- Salt Minion 3: Linux Debian 11


Alla löytyy 'initial_preferences' tiedoston pohja, jonka olin ladannut <a href=https://storage.googleapis.com/support-kms-prod/Y2dvgj4vUhoxiiLqzWmhjpLW3b3rBaLWuW8L> Suoraan Googlelta </a>

    {
      "homepage": "http://www.google.com",
      "homepage_is_newtabpage": false,
      "browser": {
        "show_home_button": true
      },
      "session": {
        "restore_on_startup": 4,
        "startup_urls": [
          "http://www.google.com/ig"
        ]
      },
      "bookmark_bar": {
        "show_on_all_tabs": true
      },
      "sync_promo": {
        "show_on_first_run_allowed": false
      },
      "distribution": {
        "import_bookmarks_from_file": "bookmarks.html",
        "import_bookmarks": true,
        "import_history": true,
        "import_home_page": true,
        "import_search_engine": true,
        "ping_delay": 60,
        "suppress_first_run_bubble": true,
        "do_not_create_desktop_shortcut": true,
        "do_not_create_quick_launch_shortcut": true,
        "do_not_launch_chrome": true,
        "do_not_register_for_update_launch": true,
        "make_chrome_default": true,
        "make_chrome_default_for_user": true,
        "suppress_first_run_default_browser_prompt": true,
        "system_level": true,
        "verbose_logging": true
      },
      "first_run_tabs": [
        "http://www.example.com",
        "http://welcome_page",
        "http://new_tab_page"
      ]
    }


# Lähteet
initial_preferencees documentation, Google Support, https://support.google.com/chrome/a/answer/187948?hl=en#zippy=%2Cstep-create-the-initial-preferences-file

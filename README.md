## [Blackweb](http://www.maravento.com/p/blacklistweb.html)

**Blackweb** es un proyecto que recopila listas negras públicas de dominios (porno, descargas, drogas, malware, spyware, trackers, bots, redes sociales, warez, venta de armas, etc), para unificarlas y hacerlas compatibles con [Squid](http://www.squid-cache.org/)

**Blackweb** is a project that collects blacklists of domains (porn, downloads, drugs, malware, spyware, trackers, bots, social networks, warez, arms sales, etc.), to unify them and make them compatible with [Squid](http://www.squid-cache.org/)

### FICHA TECNICA / DATA SHEET
---

|lst|Black Domains|txt|tar.gz|Squid Tested|
| :---: | :---: | :---: | :---: | :---: |
|blackweb.txt|2.937.023|68,6 MB|12,9 MB|v3.5.x|

### DEPENDENCIAS / DEPENDENCIES
---
```
git squid bash tar zip wget subversion python idn2 xargs
```

### GIT CLONE
---
```
git clone --depth=1 https://github.com/maravento/blackweb.git
```

### MODO DE USO / HOW TO USE
---

**blackweb.txt** ya viene optimizada. Descárguela y descomprimala en la ruta de su preferencia y active la [REGLA de Squid](https://github.com/maravento/blackweb#regla-squid-cache--squid-cache-rule) / **blackweb.txt** is already optimized. Download it and unzip it in the path of your preference and activate [Squid RULE](https://github.com/maravento/blackweb#regla-squid-cache--squid-cache-rule)

####  Download and Checksum

```
wget -q -N https://raw.githubusercontent.com/maravento/blackweb/master/blackweb.tar.gz && cat blackweb.tar.gz* | tar xzf -
wget -q -N https://raw.githubusercontent.com/maravento/blackweb/master/checksum.md5
md5sum blackweb.txt | awk '{print $1}' && cat checksum.md5 | awk '{print $1}'
```
### REGLA [Squid-Cache](http://www.squid-cache.org/) / [Squid-Cache](http://www.squid-cache.org/) RULE
---

Edite: / Edit:
```
/etc/squid/squid.conf
```
Y agregue las siguientes líneas: / And add the following lines:

```
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
acl blackweb dstdomain -i "/path_to_lst/blackweb.txt"
http_access deny blackweb
```
#### Edición / Edition

**Blackweb** contiene millones de dominios bloqueados, por tanto se recomienda: / **Blackweb** contains millions of blocked domains, therefore it is recommended:

- Usar `whitedomains.txt` para excluir dominios (ejemplo: accounts.youtube.com [desde Feb 2014, Google utiliza el subdominio accounts.youtube.com para autenticar sus servicios](http://wiki.squid-cache.org/ConfigExamples/Streams/YouTube)) o falsos positivos / Use `whitedomains.txt` to exclude domains (e.g.: accounts.youtube.com [since Feb 2014, Google uses the subdomain accounts.youtube.com to authenticate its services](http://wiki.squid-cache.org/ConfigExamples/Streams/YouTube)) or false positives
- Usar `blackdomains.txt` para agregar dominios no incluidos en `blackweb.txt` (ejemplo: .youtube.com .googlevideo.com, .ytimg.com, etc.) / Use blackdomains.txt to add domains not included in `blackweb.txt` (e.g.: .youtube.com .googlevideo.com, .ytimg.com, etc)

```
acl whitedomains dstdomain -i "/path_to_lst/whitedomains.txt"
acl blackdomains dstdomain -i "/path_to_lst/blackdomains.txt"
acl blackweb dstdomain -i "/path_to_lst/blackweb.txt"
http_access allow whitedomains
http_access deny blackdomains
http_access deny blackweb
```

### ACTUALIZACIÓN / UPDATE
---

#### ⚠️ **ADVERTENCIA: ANTES DE CONTINUAR! / WARNING: BEFORE YOU CONTINUE!**

La actualización y depuración de `blackweb.txt` puede tardar y consumir muchos recursos de hardware y ancho de banda. No se recomienda ejecutarla en equipos en producción / Update and debugging of `blackweb.txt` can take and consume many hardware resources and bandwidth. It is not recommended to run it on production equipment

##### Actualización Blackweb / Blackweb Update

El proceso de actualización de `blackweb.txt` consta de varios pasos y es ejecutado en secuencia por el script `bwupdate.sh` / The update process of `blackweb.txt` consists of several steps and is executed in sequence by the script `bwupdate.sh`

```
wget -q -N https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/bwupdate.sh && sudo chmod +x bwupdate.sh && sudo ./bwupdate.sh
```

##### Captura de Listas Negras Públicas / Capture Public Blacklists

>Captura los dominios de las listas negras públicas descargadas (ver [FUENTES](https://github.com/maravento/blackweb#fuentes--sources)) y las unifica en un solo archivo / Capture domains from downloaded public blacklists (see [SOURCES](https://github.com/maravento/blackweb#fuentes--sources)) and unifies them in a single file

##### Depuración de Dominios / Domain Debugging

>Elimina dominios inactivos, fallidos, inválidos, superpuestos ('.sub.example.com' es un dominio de '.example.com') y excluye listas blancas (google, hotmail, yahoo, etc.) / Remove inactive, failed, invalid, overlapping domains ('.sub.example.com' is a subdomain of '.example.com') and excludes whitelists (google, hotmail, yahoo, etc.)

```
com
.com
.domain.com
domain.com
0.0.0.0 domain.com
127.0.0.1 domain.com
::1 domain.com
domain.com.co
foo.bar.subdomain.domain.com
.subdomain.domain.com.co
www.domain.com
www.foo.bar.subdomain.domain.com
domain.co.uk
xxx.foo.bar.subdomain.domain.co.uk
```
outfile:
```
.domain.com
.domain.com.co
.domain.co.uk
```

##### Validación TLD / TLD Validation

>Elimina dominios con TLD inválidos (con una lista de TLDs Public and Private Suffix: ccTLD, ccSLD, sTLD, uTLD, gSLD, gTLD, eTLD, etc., hasta 4to nivel 4LDs) / Remove domains with invalid TLDs (with a list of Public and Private Suffix TLDs: ccTLD, ccSLD, sTLD, uTLD, gSLD, gTLD, eTLD, etc., up to 4th level 4LDs)

```
domain.exe
domain.com
```
outfile:
```
.domain.com
```

##### Depuración Punycode-IDN / Debugging Punycode-IDN

>Elimina hostnames mayores a 63 caracteres ([RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)) y otros caracteres inadmisibles por [IDN](http://www.gnu.org/s/libidn/manual/html_node/Invoking-idn.html) y convierte dominios con caracteres internacionales (no ASCII) y usados para [ataques homográficos](https://es.qwerty.wiki/wiki/IDN_homograph_attack) al formato [Punycode/IDNA](https://www.charset.org/punycode) / Remove hostnames larger than 63 characters ([RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)) and other characters inadmissible by [IDN](http://www.gnu.org/s/libidn/manual/html_node/Invoking-idn.html) and convert domains with international characters (not ASCII) and used for [homologous attacks](https://es.qwerty.wiki/wiki/IDN_homograph_attack) to [Punycode/IDNA](https://www.charset.org/punycode) format

```
президент.рф
mañana.com
bücher.com
café.fr
köln-düsseldorfer-rhein-main.de
mūsųlaikas.lt
sendesık.com
```
outfile:
```
xn--d1abbgf6aiiy.xn--p1ai
xn--maana-pta.com
xn--bcher-kva.com
xn--caf-dma.fr
xn--kln-dsseldorfer-rhein-main-cvc6o.de
xn--mslaikas-qzb5f.lt
xn--sendesk-wfb.com
```

##### Búsqueda DNS / DNS Loockup

>La mayoría de las [FUENTES](https://github.com/maravento/blackweb#fuentes--sources) contienen millones de dominios inválidos o inexistentes (vea [internet live stats](https://www.internetlivestats.com/total-number-of-websites/)). Entonces se verifica cada dominio vía DNS y se excluyen de la lista (`fault.txt`). Este proceso puede tardar. Por defecto procesa en paralelo ≈ 6k a 12k dominios x min, en dependencia del hardware y ancho de banda / Most of the [SOURCES](https://github.com/maravento/blackweb#fuentes--sources) contain millions of invalid or nonexistent domains (see [internet live stats](https://www.internetlivestats.com/total-number-of-websites/)). Then, each domain is verified via DNS and are excluded from the list (`fault.txt`). This process may take. By default it processes in parallel ≈ 6k to 12k domains x min, depending on the hardware and bandwidth

```
HIT google.com
FAULT testfaultdomain.com
```
##### Bloqueo TLD / TLD Block

>Agrega Black TLDs para bloquear cualquier dominio que contenga alguno. Edite `blacktlds.txt` y agrege o elimine los TLDs que quiera bloquear / Add Black TLDs to block any domain that contains one. Edit `blacktlds.txt` and add or remove the TLDs you want to block

```
.adult
.porn
.xxx
.domain.adult
.domain.porn
.subdomain.domain.xxx
.domain.com
```
outfile:
```
.adult
.porn
.xxx
.domain.com
```

##### Correr Squid con Blackweb / Run Squid with Blackweb

>Corre Squid con Blackweb y cualquier error lo envía a `SquidError.txt` en su escritorio / Run Squid with Blackweb and any error sends it to `SquidError.txt` on your desktop

##### Verifique la ejecución / Check execution (/var/log/syslog):

Ejecución exitosa / Successful execution
```
Blackweb: Done 06/05/2019 15:47:14
```

##### Importante sobre Blackweb Update / Important about Blackweb Update

- El path por default de **blackweb** es `/etc/acl`. Puede cambiarlo por el de su preferencia / The default path of **blackweb** is `/etc/acl`. You can change it for your preference
- `bwupdate.sh` incluye listas de dominios relacionados con cloud/sync (Mega, Dropbox, Pcloud, iCloud, etc), soporte remoto (Teamviewer, Anydesk, logmein, etc) y telemetría (Puede contener falsos positivos). Están comentadas por defecto (excepto que sus dominios estén en las [FUENTES](https://github.com/maravento/blackweb#fuentes--sources)). Para bloquearlas o excluirlas debe activar la línea correspondiente en el script (# JOIN LIST), aunque no se recomienda para evitar conflictos o falsos positivos / `bwupdate.sh` includes lists of domains related to cloud/sync (Mega, Dropbox, Pcloud, iCloud, etc), remote support (Teamviewer, Anydesk, logmein, etc) and telemetry (May contain false positives). They are commented by default (unless their domains are in the [SOURCES](https://github.com/maravento/blackweb#fuentes--sources). To block or exclude them you must activate the corresponding line in the script (# JOIN LIST), although is not recommended to avoid conflicts or false positives

### FUENTES / SOURCES
---

##### Blacklists

###### Active

[Adaway](http://adaway.org/hosts.txt)

[adblockplus malwaredomains_full](https://easylist-downloads.adblockplus.org/malwaredomains_full.txt)

[ABPindo indonesianadblockrules](https://raw.githubusercontent.com/ABPindo/indonesianadblockrules/master/subscriptions/abpindo.txt)

[Anti-WebMiner](https://raw.githubusercontent.com/greatis/Anti-WebMiner/master/blacklist.txt)

[anudeepND Blacklist](https://github.com/anudeepND/blacklist) (included: [coinminer](https://raw.githubusercontent.com/anudeepND/blacklist/master/CoinMiner.txt), [adservers](https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt))

[BambenekConsulting](http://osint.bambenekconsulting.com/feeds/dga-feed.txt)

[betterwebleon dga-feed](https://raw.githubusercontent.com/betterwebleon/slovenian-list/master/filters.txt)

[Capitole - Direction du Système d'Information (DSI)](http://dsi.ut-capitole.fr/blacklists/download/)

[Carl Spam](http://www.carl.net/spam/access.txt)

[cedia.org.ec](https://mirror.cedia.org.ec) (included: [domains](https://mirror.cedia.org.ec/malwaredomains/domains.txt), [immortal_domains](https://mirror.cedia.org.ec/malwaredomains/immortal_domains.txt))

[chadmayfield](https://github.com/chadmayfield) (included: [porn_all](https://raw.githubusercontent.com/chadmayfield/my-pihole-blocklists/master/lists/pi_blocklist_porn_all.list), [porn top](https://raw.githubusercontent.com/chadmayfield/pihole-blocklists/master/lists/pi_blocklist_porn_top1m.list))

[CHEF-KOCH BarbBlock-filter-list](https://github.com/CHEF-KOCH/BarbBlock-filter-list)

[Cibercrime-Tracker](http://cybercrime-tracker.net/all.php)

[crazy-max WindowsSpyBlocker](https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt)

[Dawsey21 List](https://github.com/Dawsey21/Lists)

[Disconnect.me](https://disconnect.me/) (included: [simple_ad](https://s3.amazonaws.com/lists.disconnect.me/simple_ad.txt), [simple_malvertising](https://s3.amazonaws.com/lists.disconnect.me/simple_malvertising.txt), [simple_tracking](https://s3.amazonaws.com/lists.disconnect.me/simple_tracking.txt))

[dshield.org](http://www.dshield.org) (included: [Low](http://www.dshield.org/feeds/suspiciousdomains_Low.txt), [Medium](https://www.dshield.org/feeds/suspiciousdomains_Medium.txt), [High](https://www.dshield.org/feeds/suspiciousdomains_High.txt))

[ethanr dns-blacklists](https://bitbucket.org/ethanr/dns-blacklists/raw/8575c9f96e5b4a1308f2f12394abd86d0927a4a0/bad_lists/Mandiant_APT1_Report_Appendix_D.txt)

[firebog.net](firebog.net) (included: [AdguardDNS](https://v.firebog.net/hosts/AdguardDNS.txt), [Airelle-hrsk](https://v.firebog.net/hosts/Airelle-hrsk.txt), [Airelle-trc](https://v.firebog.net/hosts/Airelle-trc.txt), [BillStearns](https://v.firebog.net/hosts/BillStearns.txt), [Easylist](https://v.firebog.net/hosts/Easylist.txt), [Easyprivacy](https://v.firebog.net/hosts/Easyprivacy.txt), [Kowabit](https://v.firebog.net/hosts/Kowabit.txt), [Prigent-Ads](https://v.firebog.net/hosts/Prigent-Ads.txt), [Prigent-Malware](https://v.firebog.net/hosts/Prigent-Malware.txt), [Prigent-Phishing](https://v.firebog.net/hosts/Prigent-Phishing.txt), [Shalla-mal](https://v.firebog.net/hosts/Shalla-mal.txt), [WaLLy3K](https://v.firebog.net/hosts/static/w3kbl.txt))

[gfmaster adblock-korea](https://raw.githubusercontent.com/gfmaster/adblock-korea-contrib/master/filter.txt)

[Halt-and-Block-Mining](https://raw.githubusercontent.com/ruvelro/Halt-and-Block-Mining/master/HBmining.bat)

[hexxium](https://hexxiumcreations.github.io/threat-list/hexxiumthreatlist.txt)

[hostsfile.mine.nu](https://hostsfile.mine.nu/hosts0.txt)

[hosts-file.net](https://hosts-file.net) (included: [ad_servers](https://hosts-file.net/ad_servers.txt), [emd](https://hosts-file.net/emd.txt), [grm](https://hosts-file.net/grm.txt), [hosts](http://hosts-file.net/download/hosts.txt), [psh](https://hosts-file.net/psh.txt))

[Joelotz URL Blacklist](https://raw.githubusercontent.com/joelotz/URL_Blacklist/master/blacklist.csv)

[Joewein Blacklist](http://www.joewein.de/sw/bl-text.htm)

[KADhosts](https://raw.githubusercontent.com/azet12/KADhosts/master/KADhosts.txt)

[malc0de](http://malc0de.com/bl/)

[Malwaredomains](http://www.malware-domains.com/files/domains.zip)

[Malwaredomainlist Hosts](http://www.malwaredomainlist.com/hostslist/hosts.txt)

[Malware-domains](http://mirror1.malwaredomains.com/files/justdomains) (or [Lehigh Malwaredomains](http://malwaredomains.lehigh.edu/files/justdomains))

[margevicius easylistlithuania](http://margevicius.lt/easylistlithuania.txt)

[Matomo-org referrer-spam-blacklist](https://github.com/matomo-org/referrer-spam-blacklist/blob/master/spammers.txt)

[MESD blacklists](http://squidguard.mesd.k12.or.us/blacklists.tgz)

[mitchellkrogza](https://github.com/mitchellkrogza) (included: [Badd-Boyz-Hosts](https://raw.githubusercontent.com/mitchellkrogza/Badd-Boyz-Hosts/master/PULL_REQUESTS/domains.txt), [Hacked Malware Web Sites](https://raw.githubusercontent.com/mitchellkrogza/The-Big-List-of-Hacked-Malware-Web-Sites/master/.dev-tools/_strip_domains/domains.txt), [Nginx Ultimate Bad Bot Blocker](https://raw.githubusercontent.com/mitchellkrogza/nginx-ultimate-bad-bot-blocker/master/_generator_lists/bad-referrers.list), [The Big List of Hacked Malware Web Sites](https://github.com/mitchellkrogza/The-Big-List-of-Hacked-Malware-Web-Sites/blob/master/hacked-domains.list), [Ultimate Hosts Blacklist](https://github.com/mitchellkrogza/Ultimate.Hosts.Blacklist))

[Netlab360 DGA Domains](https://data.netlab.360.com/feeds/dga/dga.txt)

[notabug latvian-list](https://notabug.org/latvian-list/adblock-latvian/raw/master/lists/latvian-list.txt)

[Oleksiig Blacklist](https://raw.githubusercontent.com/oleksiig/Squid-BlackList/master/denied_ext.conf)

[openphish](https://openphish.com/feed.txt)

[Perflyst](https://github.com/Perflyst) (included: [android-tracking](https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/android-tracking.txt), [SmartTV](https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/SmartTV.txt))

[Quedlin blacklist](https://github.com/quedlin/blacklist/blob/master/domains)

[quidsup](https://gitlab.com/quidsup) (included: [notrack-blocklists](https://gitlab.com/quidsup/notrack-blocklists/raw/master/notrack-blocklist.txt), [notrack-malware](https://gitlab.com/quidsup/notrack-blocklists/raw/master/notrack-malware.txt), [trackers](https://raw.githubusercontent.com/quidsup/notrack/master/trackers.txt), [qmalware](https://raw.githubusercontent.com/quidsup/notrack/master/malicious-sites.txt))

[Ransomware Abuse](https://ransomwaretracker.abuse.ch/blocklist/) (included: [CryptoWall](https://ransomwaretracker.abuse.ch/downloads/CW_C2_DOMBL.txt), [Locky](https://ransomwaretracker.abuse.ch/downloads/LY_C2_DOMBL.txt), [Domain Blocklist](https://ransomwaretracker.abuse.ch/downloads/RW_DOMBL.txt), [Ransomware Abuse](https://ransomwaretracker.abuse.ch/downloads/RW_URLBL.txt) ,[URL Blocklist ](https://ransomwaretracker.abuse.ch/downloads/TC_C2_DOMBL.txt),[TorrentLocker](https://ransomwaretracker.abuse.ch/downloads/TL_C2_DOMBL.txt))

[reddestdream](https://reddestdream.github.io/Projects/MinimalHosts/etc/MinimalHostsBlocker/minimalhosts)

[securemecca.net and hostsfile.org](https://hostsfile.org/Downloads/hosts.txt)

[Shallalist.de](http://www.shallalist.de/Downloads/shallalist.tar.gz)

[Someonewhocares](http://someonewhocares.org/hosts/hosts)

[squidblacklist.org](https://www.squidblacklist.org/) (included: [dg-ads](https://www.squidblacklist.org/downloads/dg-ads.acl), [dg-malicious.acl](https://www.squidblacklist.org/downloads/dg-malicious.acl))

[StevenBlack](https://github.com/StevenBlack) (included: [add.2o7Net](https://raw.githubusercontent.com/StevenBlack/hosts/master/data/add.2o7Net/hosts), [add.Risk](https://raw.githubusercontent.com/StevenBlack/hosts/master/data/add.Risk/hosts), [fakenews-gambling-porn-social](https://raw.githubusercontent.com/StevenBlack/hosts/master/alternates/fakenews-gambling-porn-social/hosts), [hosts](https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts), [spam](https://raw.githubusercontent.com/StevenBlack/hosts/master/data/add.Spam/hosts), [uncheckyAds](https://raw.githubusercontent.com/StevenBlack/hosts/master/data/UncheckyAds/hosts))

[Stopforumspam Toxic Domains](https://www.stopforumspam.com/downloads/toxic_domains_whole.txt)

[tankmohit UnifiedHosts](https://raw.githubusercontent.com/tankmohit/UnifiedHosts/master/hosts.all)

[Taz SpamDomains](http://www.taz.net.au/Mail/SpamDomains)

[vokins yhosts](https://raw.githubusercontent.com/vokins/yhosts/master/hosts)

[Winhelp2002](http://winhelp2002.mvps.org/hosts.txt)

[YousList](https://raw.githubusercontent.com/yous/YousList/master/youslist.txt)

[Yoyo](http://pgl.yoyo.org/adservers/serverlist.php?hostformat=nohtml)

[zerodot1 CoinBlockerLists](https://gitlab.com/ZeroDot1/CoinBlockerLists) (included: [Host](https://zerodot1.gitlab.io/CoinBlockerLists/hosts), [host_browser](https://zerodot1.gitlab.io/CoinBlockerLists/hosts_browser), [host_optional](https://zerodot1.gitlab.io/CoinBlockerLists/hosts_optional), [list](https://zerodot1.gitlab.io/CoinBlockerLists/list.txt), [list_browser](https://zerodot1.gitlab.io/CoinBlockerLists/list_browser.txt), [list_browser_UBO](https://zerodot1.gitlab.io/CoinBlockerLists/list_browser_UBO.txt))

[Zeustracker](https://zeustracker.abuse.ch/blocklist.php?download=squiddomain)

###### Inactive

[UrlBlacklist](https://web.archive.org/web/*/http://urlblacklist.com) ([Server Down since July 24, 2017](https://groups.google.com/forum/#!topic/e2guardian/7WeHpD-54LE))

[Passwall SpamAssassin](http://www.passwall.com/blacklist.txt) ([Server Down since Dec 2016](https://web.archive.org/web/20161203014003/http://www.passwall.com/blacklist.txt))

[O365IPAddresses](https://support.content.office.net/en-us/static/O365IPAddresses.xml) (No longer support. [See This post](ocs.microsoft.com/es-es/office365/enterprise/urls-and-ip-address-ranges?redirectSourcePath=%252fen-us%252farticle%252fOffice-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2))

##### Work Lists

###### Internals

[Black TLDs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/blacktlds.txt)

[Black URLs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/blackurls.txt)

[White URLs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/whiteurls.txt)

[Fault URLs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/fault.txt)

[Invalid TLDs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/invalid.txt)

###### Externals

[ipv6-hosts](https://raw.githubusercontent.com/lennylxx/ipv6-hosts/master/hosts) (Partial)

[Ransomware Database](https://docs.google.com/spreadsheets/u/1/d/1TWS238xacAto-fLKh1n5uTsdijWdCEsGIM0Y0Hvmc5g/pubhtml#)

[University Domains and Names Data List](https://raw.githubusercontent.com/Hipo/university-domains-list/master/world_universities_and_domains.json)

###### Optionals

[CloudSync URLs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/cloudsync.txt)

[Remote URLs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/remote.txt)

[Telemetry URLs](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/lst/telemetry.txt)

##### Work Tools

###### Internals

[Debug Squid Errors](https://raw.githubusercontent.com/maravento/blackweb/master/bwupdate/tools/debug_error.py)

[Parse Domains](https://raw.githubusercontent.com/lsemel/python-parse-domain/master/tools/parse_domain.py) ([modified](https://github.com/maravento/blackweb/raw/master/bwupdate/tools/parse_domain.py))

###### Externals

[CTFR](https://github.com/UnaPibaGeek/ctfr)

[idn2](http://www.gnu.org/s/libidn/manual/html_node/Invoking-idn.html)

[curl](https://curl.haxx.se/docs/manpage.html)


### CONTRIBUCIONES / CONTRIBUTIONS
---

Agradecemos a todos aquellos que han contribuido a este proyecto. Los interesados pueden contribuir, enviándonos enlaces de nuevas listas, para ser incluidas en este proyecto / We thank all those who have contributed to this project. Those interested can contribute, sending us links of new lists, to be included in this project

Special thanks to: [Jhonatan Sneider](https://github.com/sney2002)

### DONACION / DONATE
---

BTC: 3M84UKpz8AwwPADiYGQjT9spPKCvbqm4Bc

### LICENCIAS / LICENCES
---

[![GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl.txt)

[![CreativeCommons](https://licensebuttons.net/l/by-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-sa/4.0/)
[maravento.com](http://www.maravento.com) is licensed under a [Creative Commons Reconocimiento-CompartirIgual 4.0 Internacional License](http://creativecommons.org/licenses/by-sa/4.0/).

© 2019 [Maravento Studio](http://www.maravento.com)

### EXENCION DE RESPONSABILIDAD / DISCLAIMER
---

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

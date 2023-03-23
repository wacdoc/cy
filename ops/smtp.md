# Adeiladwch eich gweinydd anfon post SMTP eich hun

## rhagymadrodd

Gall SMTP brynu gwasanaethau yn uniongyrchol gan werthwyr cwmwl, megis:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali gwthio e-bost cwmwl](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Gallwch hefyd adeiladu eich gweinydd post eich hun - anfon diderfyn, cost gyffredinol isel.

Isod, rydym yn dangos cam wrth gam sut i adeiladu ein gweinydd post ein hunain.

## Dewis gweinydd

Mae angen IP cyhoeddus ar y gweinydd SMTP hunangynhaliol gyda phorthladdoedd 25, 456, a 587 ar agor.

Mae cymylau cyhoeddus a ddefnyddir yn gyffredin wedi rhwystro'r porthladdoedd hyn yn ddiofyn, ac efallai y bydd yn bosibl eu hagor trwy gyhoeddi gorchymyn gwaith, ond mae'n drafferthus iawn wedi'r cyfan.

Rwy'n argymell prynu gan westeiwr sydd â'r porthladdoedd hyn ar agor ac sy'n cefnogi sefydlu enwau parth cefn.

Yma, rwy'n argymell [Contabo](https://contabo.com) .

Mae Contabo yn ddarparwr cynnal wedi'i leoli ym Munich, yr Almaen, a sefydlwyd yn 2003 gyda phrisiau cystadleuol iawn.

Os dewiswch Ewro fel arian cyfred prynu, bydd y pris yn rhatach (mae gweinydd gyda chof 8GB a 4 CPU yn costio tua 529 yuan y flwyddyn, ac mae'r ffi gosod gychwynnol yn rhad ac am ddim am flwyddyn).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Wrth osod archeb, mae'n `prefer AMD` , a bydd gan y gweinydd gyda CPU AMD berfformiad gwell.

Yn y canlynol, byddaf yn cymryd VPS Contabo fel enghraifft i ddangos sut i adeiladu eich gweinydd post eich hun.

## Cyfluniad system Ubuntu

Y system weithredu yma yw Ubuntu 22.04

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Os yw'r gweinydd ar ssh yn dangos `Welcome to TinyCore 13!` (fel y dangosir yn y ffigur isod), mae'n golygu nad yw'r system wedi'i gosod eto. Datgysylltwch ssh ac arhoswch am ychydig funudau i fewngofnodi eto.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

Pan fydd `Welcome to Ubuntu 22.04.1 LTS` yn ymddangos, mae'r cychwyniad wedi'i gwblhau, a gallwch barhau â'r camau canlynol.

### [Dewisol] Cychwyn yr amgylchedd datblygu

Mae'r cam hwn yn ddewisol.

Er hwylustod, rhoddais y gosodiad a chyfluniad system meddalwedd ubuntu yn [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) .

Rhedeg y gorchymyn canlynol i'w osod gydag un clic.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Defnyddwyr Tsieineaidd, defnyddiwch y gorchymyn canlynol yn lle hynny, a bydd yr iaith, parth amser, ac ati yn cael eu gosod yn awtomatig.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Mae Contabo yn galluogi IPV6

Galluogi IPV6 fel y gall SMTP hefyd anfon e-byst gyda chyfeiriadau IPV6.

golygu `/etc/sysctl.conf`

Addaswch neu ychwanegwch y llinellau canlynol

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

Dilynwch [y tiwtorial contabo: Ychwanegu cysylltedd IPv6 i'ch gweinydd](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

Golygu `/etc/netplan/01-netcfg.yaml` , ychwanegwch ychydig o linellau fel y dangosir yn y ffigur isod (mae gan ffeil ffurfweddu rhagosodedig Contabo VPS y llinellau hyn eisoes, dim ond dad-wneud sylwadau arnynt).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Yna mae `netplan apply` i wneud i'r cyfluniad addasedig ddod i rym.

Ar ôl i'r cyfluniad fod yn llwyddiannus, gallwch ddefnyddio `curl 6.ipw.cn` i weld cyfeiriad ipv6 eich rhwydwaith allanol.

## Cloniwch y storfa ffurfweddu ops

```
git clone https://github.com/wactax/ops.soft.git
```

## Cynhyrchwch dystysgrif SSL am ddim ar gyfer eich enw parth

Mae anfon post yn gofyn am dystysgrif SSL ar gyfer amgryptio a llofnodi.

Rydym yn defnyddio [acme.sh](https://github.com/acmesh-official/acme.sh) i gynhyrchu tystysgrifau.

Mae acme.sh yn offeryn llofnodi tystysgrif awtomataidd ffynhonnell agored,

Rhowch y warws cyfluniad ops.soft, rhedeg `./ssl.sh` , a bydd ffolder `conf` yn cael ei greu yn **y cyfeiriadur uchaf** .

Dewch o hyd i'ch darparwr DNS o [acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) , golygu `conf/conf.sh` .

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Yna rhedeg `./ssl.sh 123.com` i gynhyrchu tystysgrifau `123.com` a `*.123.com` ar gyfer eich enw parth.

Bydd y rhediad cyntaf yn gosod [acme.sh](https://github.com/acmesh-official/acme.sh) yn awtomatig ac yn ychwanegu tasg a drefnwyd ar gyfer adnewyddu awtomatig. Gallwch weld `crontab -l` , mae llinell o'r fath fel a ganlyn.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Mae'r llwybr ar gyfer y dystysgrif a gynhyrchir yn rhywbeth fel `/mnt/www/.acme.sh/123.com_ecc。`

Bydd adnewyddu tystysgrif yn galw sgript `conf/reload/123.com.sh` , golygu'r sgript hon, gallwch ychwanegu gorchmynion fel `nginx -s reload` i adnewyddu storfa tystysgrif cymwysiadau cysylltiedig.

## Adeiladu gweinydd SMTP gyda chasquid

Mae [chasquid](https://github.com/albertito/chasquid) yn weinydd SMTP ffynhonnell agored sydd wedi'i ysgrifennu yn iaith Go.

Yn lle'r rhaglenni gweinydd post hynafol fel Postfix a Sendmail, mae chasquid yn symlach ac yn haws ei ddefnyddio, ac mae hefyd yn haws ar gyfer datblygiad eilaidd.

Bydd Run `./chasquid/init.sh 123.com` yn cael ei osod yn awtomatig gydag un clic (yn lle 123.com gyda'ch enw parth anfon).

## Ffurfweddu Llofnod E-bost DKIM

Defnyddir DKIM i anfon llofnodion e-bost i atal llythyrau rhag cael eu trin fel sbam.

Ar ôl i'r gorchymyn redeg yn llwyddiannus, fe'ch anogir i osod y cofnod DKIM (fel y dangosir isod).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Ychwanegwch gofnod TXT i'ch DNS (fel y dangosir isod).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Gweld statws gwasanaeth a logiau

 `systemctl status chasquid` Gweld statws gwasanaeth.

Mae cyflwr gweithredu arferol fel y dangosir yn y ffigur isod

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 Gall `grep chasquid /var/log/syslog` neu `journalctl -xeu chasquid` weld y log gwallau.

## Ffurfweddiad enw parth gwrthdroi

Yr enw parth cefn yw caniatáu i'r cyfeiriad IP gael ei ddatrys i'r enw parth cyfatebol.

Gall gosod enw parth gwrthdro atal e-byst rhag cael eu hadnabod fel sbam.

Pan dderbynnir y post, bydd y gweinydd sy'n derbyn yn perfformio dadansoddiad enw parth gwrthdro ar gyfeiriad IP y gweinydd anfon i gadarnhau a oes gan y gweinydd anfon enw parth gwrthdro dilys.

Os nad oes gan y gweinydd sy'n anfon enw parth gwrthdro neu os nad yw'r enw parth cefn yn cyfateb i gyfeiriad IP y gweinydd anfon, gall y gweinydd sy'n derbyn adnabod yr e-bost fel sbam neu ei wrthod.

Ewch i [https://my.contabo.com/rdns](https://my.contabo.com/rdns) a ffurfweddu fel y dangosir isod

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Ar ôl gosod yr enw parth cefn, cofiwch ffurfweddu cydraniad blaen yr enw parth ipv4 ac ipv6 i'r gweinydd.

## Golygu enw gwesteiwr chasquid.conf

Addasu `conf/chasquid/chasquid.conf` i werth yr enw parth cefn.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Yna rhedeg `systemctl restart chasquid` i ailgychwyn y gwasanaeth.

## conf wrth gefn i ystorfa git

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Er enghraifft, rwy'n gwneud copi wrth gefn o'r ffolder conf i'm proses github fy hun fel a ganlyn

Creu warws preifat yn gyntaf

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Rhowch y cyfeiriadur conf a'i gyflwyno i'r warws

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Ychwanegu anfonwr

rhedeg

```
chasquid-util user-add i@wac.tax
```

Gall ychwanegu anfonwr

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Gwiriwch fod y cyfrinair wedi'i osod yn gywir

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Ar ôl ychwanegu'r defnyddiwr, bydd `chasquid/domains/wac.tax/users` yn cael ei ddiweddaru, cofiwch ei gyflwyno i'r warws.

## DNS ychwanegu cofnod SPF

Mae SPF ( Fframwaith Polisi Anfonwyr ) yn dechnoleg dilysu e-bost a ddefnyddir i atal twyll e-bost.

Mae'n gwirio hunaniaeth anfonwr post trwy wirio bod cyfeiriad IP yr anfonwr yn cyfateb i gofnodion DNS yr enw parth y mae'n honni ei fod, gan atal twyllwyr rhag anfon e-byst ffug.

Gall ychwanegu cofnodion SPF atal negeseuon e-bost rhag cael eu hadnabod fel sbam cymaint â phosibl.

Os nad yw eich gweinydd enw parth yn cefnogi math SPF, ychwanegwch gofnod math TXT.

Er enghraifft, mae'r SPF o `wac.tax` fel a ganlyn

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

SPF ar gyfer `_spf.wac.tax`

`v=spf1 a:smtp.wac.tax ~all`

Sylwch fy mod wedi `include:_spf.google.com` yma, mae hyn oherwydd byddaf yn ffurfweddu `i@wac.tax` fel y cyfeiriad anfon ym mlwch post Google yn ddiweddarach.

## Cyfluniad DNS DMARC

DMARC yw'r talfyriad o (Dilysu Neges yn Seiliedig ar Barth, Adrodd a Chydymffurfiaeth).

Fe'i defnyddir i ddal adlamiadau SPF (efallai oherwydd gwallau ffurfweddu, neu mae rhywun arall yn cymryd arno mai chi i anfon sbam).

Ychwanegu cofnod TXT `_dmarc` ,

Mae'r cynnwys fel a ganlyn

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Mae ystyr pob paramedr fel a ganlyn

### p (Polisi)

Yn dangos sut i drin e-byst sy'n methu dilysiad SPF (Fframwaith Polisi Anfonwr) neu DKIM (DomainKeys Identified Mail). Gellir gosod y paramedr p i un o dri gwerth:

* dim: Ni chymerir unrhyw gamau, dim ond y canlyniad dilysu sy'n cael ei fwydo'n ôl i'r anfonwr trwy'r mecanwaith adrodd e-bost.
* Cwarantîn: Rhowch y post nad yw wedi pasio'r dilysiad yn y ffolder sbam, ond ni fydd yn gwrthod y post yn uniongyrchol.
* gwrthod: Gwrthod yn uniongyrchol e-byst sy'n methu â dilysu.

### ar gyfer (Dewisiadau Methiant)

Yn nodi faint o wybodaeth a ddychwelwyd gan y mecanwaith adrodd. Gellir ei osod i un o'r gwerthoedd canlynol:

* 0: Adrodd canlyniadau dilysu ar gyfer pob neges
* 1: Dim ond rhoi gwybod am negeseuon sy'n methu â dilysu
* d: Dim ond adrodd am fethiannau dilysu enw parth
* s: dim ond adrodd am fethiannau dilysu SPF
* l: Rhowch wybod am fethiannau dilysu DKIM yn unig

### rua& ruf

* rua (Adrodd URI ar gyfer adroddiadau Cyfun): Cyfeiriad e-bost ar gyfer derbyn adroddiadau cyfun
* ruf (Adrodd URI ar gyfer adroddiadau Fforensig): cyfeiriad e-bost i dderbyn adroddiadau manwl

## Ychwanegu cofnodion MX i anfon e-byst ymlaen at Google Mail

Gan na allwn ddod o hyd i flwch post corfforaethol rhad ac am ddim sy'n cefnogi cyfeiriadau cyffredinol (Gall Catch-All, dderbyn unrhyw e-byst a anfonwyd at yr enw parth hwn, heb gyfyngiadau ar rhagddodiaid), defnyddiais chasquid i anfon pob e-bost ymlaen i fy mlwch post Gmail.

**Os oes gennych eich blwch post busnes taledig eich hun, peidiwch ag addasu'r MX a hepgor y cam hwn.**

Golygu `conf/chasquid/domains/wac.tax/aliases` , gosod blwch post anfon ymlaen

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` yn nodi pob e-bost, `i` yw rhagddodiad cyfeiriad e-bost y defnyddiwr anfon a grëwyd uchod. I anfon post ymlaen, mae angen i bob defnyddiwr ychwanegu llinell.

Yna ychwanegwch y cofnod MX (rwyf yn pwyntio'n uniongyrchol at gyfeiriad yr enw parth cefn yma, fel y dangosir yn y llinell gyntaf yn y ffigur isod).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Ar ôl i'r cyfluniad gael ei gwblhau, gallwch ddefnyddio cyfeiriadau e-bost eraill i anfon e-byst at `i@wac.tax` ac `any123@wac.tax` i weld a allwch chi dderbyn e-byst yn Gmail.

Os na, gwiriwch y log chasquid ( `grep chasquid /var/log/syslog` ).

## Anfonwch e-bost at i@wac.tax gyda Google Mail

Ar ôl i Google Mail dderbyn y post, roeddwn yn naturiol yn gobeithio ateb gyda `i@wac.tax` yn lle i.wac.tax@gmail.com.

Ewch i [https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) a chliciwch "Ychwanegu cyfeiriad e-bost arall".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Yna, nodwch y cod dilysu a dderbyniwyd gan yr e-bost yr anfonwyd ato.

Yn olaf, gellir ei osod fel y cyfeiriad anfonwr diofyn (ynghyd â'r opsiwn i ateb gyda'r un cyfeiriad).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Yn y modd hwn, rydym wedi cwblhau sefydlu'r gweinydd post SMTP ac ar yr un pryd yn defnyddio Google Mail i anfon a derbyn negeseuon e-bost.

## Anfonwch e-bost prawf i wirio a yw'r ffurfweddiad yn llwyddiannus

Rhowch `ops/chasquid`

Rhedeg `direnv allow` gosod dibyniaethau (mae direnv wedi'i osod yn y broses gychwynnol un allwedd flaenorol ac mae bachyn wedi'i ychwanegu at y gragen)

yna rhedeg

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Mae ystyr y paramedrau fel a ganlyn

* defnyddiwr: enw defnyddiwr SMTP
* pasio: cyfrinair SMTP
* i: derbyniwr

Gallwch anfon e-bost prawf.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Argymhellir defnyddio Gmail i dderbyn e-byst prawf i wirio a yw'r ffurfweddiadau'n llwyddiannus.

### Amgryptio safonol TLS

Fel y dangosir yn y ffigur isod, mae'r clo bach hwn, sy'n golygu bod y dystysgrif SSL wedi'i galluogi'n llwyddiannus.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Yna cliciwch ar "Dangos yr E-bost Gwreiddiol"

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Fel y dangosir yn y ffigur isod, mae tudalen bost wreiddiol Gmail yn dangos DKIM, sy'n golygu bod y ffurfweddiad DKIM yn llwyddiannus.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Gwiriwch y Derbyniwyd ym mhennyn yr e-bost gwreiddiol, a gallwch weld mai'r cyfeiriad anfonwr yw IPV6, sy'n golygu bod IPV6 hefyd wedi'i ffurfweddu'n llwyddiannus.

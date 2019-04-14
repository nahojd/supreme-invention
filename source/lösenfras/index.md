---
title: Lösenfrasgenerator
date: 2019-04-14 12:00:32
slug: lösenfras
fullwidth: false
---

Här kan du generera en lösenfras baserad på drygt 24 000 svenska ord.
En lösenfras ger en större säkerhet och är lättare att komma ihåg än ett vanligt lösenord
med slumpvisa tecken. Generatorn är inspirerad av denna <a href="http://xkcd.com/936/">XKCD-serie</a> och <a href="http://passphra.se">passphra.se</a>.

<textarea id="fras"></textarea>

<a href="#" id="genereraOm">Bättre fras, tack!</a>

Om du inte gillar lösenfrasen är det bara att klicka på länken ovan för att få ett nytt förslag.
Ett annat tips är att byta ordningen på orden eller kanske ändra böjningsformen på något så du har lättare att komma ihåg frasen.
Se dock till att du inte skapar en enkel, normal mening av den!

<aside class="info"><h2>Vad är det för ord som används?</h2><p>Ordlistan kommer från <a href="http://www.dsso.se">Den stora svenska ordlistan</a>, men är filtrerad så att alla ord med mer än två vokaler eller andra tecken än de vanliga a-ö är borttagna. Det ger <span class="antalOrdIListan"></span> ord att välja från. Det är egentligen lite väl många, och vissa är ganska... exotiska. Men jag hittade ingen lista med bara vanliga ord.</p><h2>Hur säker är min lösenfras?</h2><p>Din lösenfras innehåller <span id="antalTecken"></span> tecken i 30 varianter (a-ö + mellanslag). Det ger <span id="teckenkombinationer"></span> kombinationer vid en traditionell brute-force-attack. Om man istället går på orden innehåller ditt lösenord 4 ord av <span class="antalOrdIListan"></span> ord i listan. Det ger <span id="ordkombinationer"></span>. Om man jämför innehåller ett vanligt lösenord på 8 tecken med a-z, A-Z, 0-9 och ett tiotal skiljetecken ungefär 722204136308736 tänkbara kombinationer. Med andra ord är din lösenfras hyfsat säker.</p></aside>

<script>
    window.addEventListener('load', function() {
        var generator = {
            ordlista: {},
            init: function (callback) {
                $.getJSON('/images/losenfras/ord.txt', function (data) {
                    generator.ordlista = data;
                    callback();
                });
            },
            genereraFras: function () {
                var ordlista = generator.ordlista;
                var antalOrd = 4;
                var fras = $('#fras');
                fras.empty();

                for (var i = 1; i <= antalOrd; i++) {
                    var index = Math.floor(Math.random() * (ordlista.length + 1));
                    fras.append(ordlista[index]);
                    if (i < antalOrd)
                        fras.append(' ');
                }
                document.getElementById('fras').select()

                var antalTecken = fras.val().length;
                var teckenkombinationer = Math.pow(30, antalTecken);
                var ordkombinationer = Math.pow(ordlista.length, antalOrd);
                $('#antalTecken').html(antalTecken);
                $('#teckenkombinationer').html(teckenkombinationer);
                $('.antalOrdIListan').html(ordlista.length);
                $('#ordkombinationer').html(ordkombinationer);
            }
        };

        $(function () {
            generator.init(function () {
                generator.genereraFras();
            });

            $('#genereraOm').click(function () {
                //TODO: För gtag istället...
                //_gaq.push(['_trackEvent', 'L�senfras', 'Generera ny']);
                generator.genereraFras()
            });
        });
    });
</script>
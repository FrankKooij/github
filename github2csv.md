# Issues en commentaren exporteren vanuit een repository

## Inleiding

Dit stappenplan beschrijft hoe je met behulp van een Mac de issues en commentaren van een GitHub-repository kunt exporteren naar Excel. Als voorbeeld is de repository https://github.com/geonovum/imbag gebruikt, maar die kun je vervangen door elke andere repository waartoe je toegang hebt.

## Bronnen

Primaire bron:
- http://www.fullo.net/blog/2012/06/25/export-github-issues-as-csv-with-v3-api

Secundaire bronnen:
- http://webapps.stackexchange.com/questions/45258/export-print-list-of-github-repo-issues
- http://stackoverflow.com/questions/10757671/how-to-remove-line-breaks-no-characters-from-the-string
- http://stackoverflow.com/questions/4824621/is-a-new-line-n-or-r-n
- http://php.net/manual/en/function.addslashes.php

## Werkwijze

Maak een PHP-script in de gewenste werkmap door deze [tekst](http://www.fullo.net/blog/2012/06/25/export-github-issues-as-csv-with-v3-api) te plakken in een leeg bestand met de naam `github2csv.php`:

```php
<?php
// issues.php
$file = fopen('github.json', 'r'); 
$content = fread($file, filesize('github.json')); 
$issues = json_decode($content);
foreach ($issues as $i) { 
  echo $i->html_url.";".$i->user->login.";".$i->created_at.";".$i->updated_at.";\"".$i->title."\";\"".str_replace(array("\r", "\n"), '', $i->body_text)."\"".PHP_EOL;
}
?>
```

Haal de issues vanuit Terminal op bij GitHub:

```sh
curl https://api.github.com/repos/geonovum/imbag/issues?per_page=1000 > github.json -H "Accept: application/vnd.github.v3.full+json"

```

Converteer de issues vanuit Terminal naar CSV met het PHP-script:

```sh
php github2csv.php > github.csv
```

Converteer daarna de character encoding van UTF-8 naar die van Excel. Open hiertoe de CSV in [TextWrangler](https://itunes.apple.com/app/textwrangler/id404010395?mt=12) en sla ongewijzigd op met de gewenste bestandsnaam (`File` > `Save As...`) met Encoding: `Western (Windows Latin 1)` en Line breaks: `Unix (LF)`.

Haal alle commentaren vanuit Terminal op bij GitHub:

```sh
curl https://api.github.com/repos/geonovum/imbag/issues/comments?per_page=1000 > github.json -H "Accept: application/vnd.github.v3.full+json"
```

Converteer de commentaren vanuit Terminal naar CSV op dezelfde manier als de issues en met hetzelfde PHP-script.

Open de commentaren daarna in TextWrangler en sla op dezelfde manier op met de gewenste bestandsnaam en de character encoding van Excel.

Importeer de issues en commentaren allebei in Excel for Mac 2011 (`Bestand` > `Importeren...`) met de instellingen:

- Type bestand: `CSV-Bestand` > `Importeren`
- Bestandstype: `Gescheiden` > Oorspronkelijk bestand: `Westers (Windows Latin 1)` > `Volgende`
- Scheidingstekens: alleen `Puntkomma` > `Voltooien`

Integreer de issues en commentaren tenslotte in Excel tot één overzicht en voeg de titelregel toe:

```
html_url	login	created_at	updated_at	title	body_text
```

Sorteer dit overzicht op de kolom `html_url`.

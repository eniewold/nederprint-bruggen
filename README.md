# NederPrint-bruggen

Eenvoudige, gegeorefereerde 3D-modellen van Nederlandse bruggen, automatisch
gemaakt uit open data: de vorm uit de BGT (overbruggingsdelen, wegdelen,
water), het constructietype uit OpenStreetMap en het FIS van Rijkswaterstaat,
de hoogte verankerd op de PDOK 3D-terreintegels. [NederPrint](https://nederprint.nl)
zet ze op de juiste plek in een 3D-stadskaart en in de printexport, in plaats
van het platte brugvlak dat PDOK in het terrein legt.

Deze repo bevat alleen de modellen. De generator waarmee ze gemaakt en
bijgewerkt worden is onderdeel van NederPrint zelf en staat hier niet in; een
model wijzigen gaat dus via NederPrint, niet via een pull request op een GLB.

## Indeling

```
models/<plaats>/<plaats>-<map>/   één map per brug, gegroepeerd per woonplaats
  <plaats>-<map>.json             catalogusitem: naam, RD-positie, as, maaiveld, vervangen PDOK-objecten, herkomst
  <plaats>-<map>.glb              model in meters, Y omhoog, één node per onderdeel (dek, rijbaan, landhoofd, …)
docs/catalogus-formaat.md         het JSON- en GLB-formaat
AGENTS.md                         korte instructies voor AI-assistenten
```

`generated` in de JSON beschrijft de herkomst van elk model: de BGT-dekken, de
OSM- en FIS-bron, het gekozen brugtype, de gebruikte opties en de opdracht
waarmee het is gemaakt.

## Gebruik in NederPrint

De webshop haalt bij elke build de laatste stand van deze repo op (alleen
`models/**.json` en `models/**.glb`) en bouwt daar zelf kaarttegels (3D Tiles)
uit. Een push naar `master` staat na de volgende deploy live.

## Licentie en bronnen

CC BY-NC-SA 4.0, zie [LICENSE](LICENSE) en [CONTRIBUTING.md](CONTRIBUTING.md).
De modellen zijn afgeleid van de BGT en de 3D-terreintegels van PDOK (CC0),
OpenStreetMap (© OpenStreetMap-bijdragers, ODbL; alleen namen en
constructietypen) en het Fairway Information System van Rijkswaterstaat.

# Instructies voor AI-assistenten

Deze repo bevat alleen gegenereerde, gegeorefereerde 3D-modellen van
Nederlandse bruggen (`models/<plaats>/<plaats>-<map>/`, formaat in
[docs/catalogus-formaat.md](docs/catalogus-formaat.md)). De generator staat
niet in deze repo maar in NederPrint.

Kernregels:

- Wijzig een GLB of JSON nooit met de hand; de modellen worden alleen door de
  generator geschreven en bij een volgende run overschreven.
- Verwijder of hernoem geen mappen in `models/`: de mapnaam is de sleutel in
  NederPrint (kaart, export en de voorrang van landmarks).
- Bronnen zijn open data (PDOK/BGT CC0, OSM ODbL, FIS van Rijkswaterstaat).
  Neem niets over van Google, Mapbox of commerciële kaarten.
- Tijdelijke bestanden en controlebeelden horen buiten de repo.

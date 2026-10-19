# Catalogusformaat van de brugmodellen

Eén submap per brug, gegroepeerd per woonplaats, met een JSON (plaatsing en herkomst) en een GLB (het model). Dit
is hetzelfde formaat als de landmarkmodellen van NederPrint (`glbPlacementSchema`), uitgebreid met de vervangen
PDOK-terreinobjecten en de terreincorrecties. De webshop leest alleen deze twee bestanden per brug. Codeverwijzingen hieronder (`lib/server/…`, `glb-model.ts`) slaan op de
webshop van NederPrint, niet op deze repo.

## Catalogus: `models/<plaats>/<map>/<map>.json` + `<map>.glb`

Eén submap per brug, gegroepeerd per plaats (`leiden/leiden-koornbrug/`; `BRIDGES_DIR` overschrijft de hoofdmap).
De mapnaam van de brug begint met de plaats, want die naam is de sleutel in de URL, de mesh-ids en de
landmarkvoorrang, en brugnamen komen in veel plaatsen voor. De lezer (`readGlbCatalog`) kijkt één niveau
dieper in een map zonder eigen `<map>.json`; een platte map werkt ook nog (landmarks). De JSON volgt `glbPlacementSchema` uit
`lib/server/glb-model.ts`, uitgebreid in `lib/server/bridges/catalog.ts`:

```jsonc
{
  "name": "Middachterbrug", // weergavenaam
  "file": "middachterbrug.glb", // GLB in dezelfde map
  "className": "road", // klasse voor nodes zonder "klasse:"-voorvoegsel (standaard "building")
  "unitsPerMetre": 1, // glTF is in meters
  "crs": "EPSG:28992",
  "origin": [201458.54, 447721.37], // RD-coördinaat van de modeloorsprong (x, y)
  "xAxis": [1, 0], // RD-richting van de lokale +X-as (standaard oost)
  "groundOffsetMetres": 0, // verschuiving van z = 0 t.o.v. het bemonsterde maaiveld
  "groundHeight": 51.0, // ellipsoïdische hoogte van z = 0 als er geen maaiveldpunt in de uitsnede valt
  "groundSamplePoints": [[18.8, 6.5]], // modelcoördinaten (x, y) waar het PDOK-maaiveld wordt bemonsterd; de laagste geldt
  "replacesTerrain": ["P0025.4f7a58ed…"], // gml_id's van PDOK-terreinobjecten die wegvallen zolang het model er is
  "terrainCorrections": [/* zie hieronder */],
  "generated": {
    "deckBgtIds": ["P0025.4f7a58ed…"],
    "opties": { "type": "ophaalbrug" },
  }, // herkomst; deckBgtIds telt mee voor de landmark-voorrang; verder vrij (passthrough)
}
```

Plaatsing: het model staat met z = 0 op de **laagste** PDOK-hoogte op de `groundSamplePoints` die in de uitsnede
vallen (ellipsoïdisch, zoals de terreintegels; de export leest die hoogte uit de ingeladen PDOK-meshes met
`surfaceIndex`, hoogste vlak per punt). Valt geen punt in de uitsnede, dan geldt `groundHeight`. De kaart krijgt
`groundHeight` mee en zet het model er direct op zonder zelf te meten.

### GLB

- Meters, Y omhoog (glTF): modelcoördinaat (x, y, z) met Z omhoog wordt glTF (x, z, −y).
- Eén node per onderdeel. De nodenaam `klasse:label` geeft de modelklasse (`road`, `terrain`, `vegetation`,
  `separation`, `water`, `building`, …; zie `partIdentity` in `glb-model.ts`); zonder voorvoegsel geldt
  `className` uit de JSON. Het label wordt de objectnaam in de export (`<naam> – <label>`).
- `extras.attributes` op een node (alleen niet-lege tekstwaarden, bijvoorbeeld `bgt_functie`,
  `bgt_fysiekvoorkomen`) maakt dat de themakleurregels van de kaart op dat onderdeel werken, en dat de export
  onderdelen van één model samen opvult (`shareModelSupport` in `lib/server/three-mf.ts`).
- Meshes van een brug heten in de export `bridge-catalog-<map>-<n>` (`BRIDGE_MESH_PREFIX`); de export herkent
  catalogusmodellen via `isCatalogModelMesh`/`catalogModelKey` en snijdt ze als gesloten volume af.

### Terreincorrecties (`terrainCorrections`)

Rechthoeken in RD met een doelvlak, gedeeld door kaartshader en export (`lib/bridges/terrain-corrections.ts`,
`correctedTerrainHeight`):

```jsonc
{ "centre": [x, y], "axis": [ux, uy], "halfLength": 20, "halfWidth": 6,
  "height": 62.3, "gradient": [du, dv], "minLift": 0.1, "maxLift": 3.0 }
```

Een PDOK-terreinhoekpunt (klassen Road, Terrain, Forest, Separation; geen water) binnen de rechthoek dat tussen
`minLift` en `maxLift` onder het vlak `height + gradient·(u, v)` ligt, komt op het vlak; negatieve grenzen
verlagen. De eerste rechthoek die een hoekpunt raakt telt. Hoogte ellipsoïdisch. De kaartshader past er
hooguit 32 tegelijk toe. Ze gelden alleen zolang het brugmodel zichtbaar is.

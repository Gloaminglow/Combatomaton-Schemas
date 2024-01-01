# Combatomaton-Schemas
These are the XML schemas used to define content for mods in the 2D Platformer-shooter game _Combatomaton_. You can make a mod by creating a new folder in the `Data` folder and adding the appropriate XML files.

The basic structure is as follows (folders end with a slash):
```
Data
  Base (base game content)
  Mod_Name
    Abilities
    Armor
    Icons
    Levels
    Maps
    Models
    Music
    Sounds
    Traits
    Wapons
```

## Maps
Details for creating custom maps are in map-format.md. You can also look at the in-game maps in `Data/Base/Maps` in the game folder for examples.

## Other content
All other non-map content is defined by XML files found in the mod folder. This repository contains the XML schemas that those files have to conform to in order to be loaded correctly. The schemas aren't really human-readable and are instead meant for you to validate against to make sure the syntax is correct before trying to load it in the game.

### Abilities
Abilities are in `Data/(mod name)/Abilities` and must conform to the `ability.xsd` schema. Examples from the base game are in `Data/Base/Abilities`.
Art for ability icons goes in `Data/(mod name)/Icons`.

### Armor (Character heads, torsos, and arms. Legs are not yet implemented)
Armors are in `Data/(mod name)/Armor` and must conform to the `armor.xsd` schema. Examples from the base game are in `Data/Base/Armor`.
Art for armor goes in `Data/(mod name)/Models`.

### Levels
Levels are in `Data/(mod name)/Levels` and must conform to the `level.xsd` schema. Examples from the base game are in `Data/Base/Levels`.

### Music
Music XML and MP3 files are in `Data/(mod name)/Music` and the XML file must conform to the `music.xsd` schema. Examples from the base game are in `Data/Base/Music`.

### Sounds (sound effects)
Sound effects are .ogg files and are in `Data/(mod name)/Sounds`. Examples from the base game are in `Data/Base/Sounds`.

### Traits
Traits are in `Data/(mod name)/Traits` and must conform to the `trait.xsd` schema. Examples from the base game are in `Data/Base/Traits`.
Art for trait icons goes in `Data/(mod name)/Icons`.

### Weapons
Weapons are in `Data/(mod name)/Weapons` and must conform to the `weapon.xsd` schema. Examples from the base game are in `Data/Base/Weapon`.
Art for weapons goes in `Data/(mod name)/Models`.

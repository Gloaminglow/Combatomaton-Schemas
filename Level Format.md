# Data format
SVG file (xml), with each layer (`<g>` element) representing a separate part of the map
### Points:
Points are represented by SVG `<ellipse>` elements, where `cx` and `cy` determine
the location of the point.
### Classes:
The type of an svg object is determined by:
1. Its layer's `inkscape:label` attribute (layer name)
2. Its layer's ancestor `inkscape:label` attributes (group name)
Otherwise defaults to a foreground element if it doesn't match on any of the above rules.

## Platforms and Walls
### Required
### Details:
 - `<polygon />` or `<rect />`
 - Name `"collision"`, `"collisions"`, `"solid"`, or `"solids"`

Platforms and walls are SVG polygon elements that only define collisions and do not display in-game.
A map should have visual elements that indicate to players where the platforms and walls are.

The fill color determines the footstep sound when walking on a platform:
 - #000000 (black)   = No Sound (Default)
 - #ff0000 (red)     = Wood
 - #ffff00 (yellow)  = Earth
 - #00ff00 (green)   = Plant
 - #00ffff (cyan)    = Splash
 - #0000ff (blue)    = Wade
 - #ff00ff (magenta) = Stone
 - #ffffff (white)   = Metal

### Example:
```
<g inkscape:label="collision">
    <polygon fill="#ffff00" points="0,0 10,0 10,100 0,100" />
</g>
```
translates to
```
Platform {
    Points = [(0,0), (10,0), (10, 100), (0, 100)],
    Sound = Earth
}
```
### Example:
```
<g inkscpae:label="collision">
    <polygon fill="#00ffff" points="0,0 10,0 10,100 0,100" />
    <polygon fill="#000000" points="50,0 60,0 60,100 50,100" />
</g>
```
translates to
```
Platform {
    Points = [(0,0), (10,0), (10, 100), (0, 100)],
    Sound = Splash
},
Platform {
    Points = [(50,0), (60,0), (60, 100), (50, 100)],
    Sound = None
}
```
### Example:
```
<g inkscape:label="collision">
    <rect fill="#e0fafe" height="100" width="50" x="5" y="10" />
</g>
```
translates to
```
Platform {
    Points = [(5, 10), (55, 10), (55, 110), (5, 110)],
    Sound = Metal // #e0fafe is closest to #ffffff (White)
}
```



## Respawn Points
### Required:
 - Free-for-All points for Free-for-All game modes
 - At least one Team (for parsing map data)
### Game Data:
### Details
 - `<ellipse />`
 - Name `"respawn"` or `"respawns"`
 - Team determined by fill color `"fill"`

Respawn points are points that specify the respawn locations for a team or free-for-all players. 
They do not display in-game. The number of teams allowed on a map is determined by the 
team respawn points. The fill color (`"fill"` attribute) determines the team for a respawn point.

Free-for-all respawn points are `#000000` (black) fill color. Otherwise each respawn point of the same color
belongs to the same team. 

The colors used by respawn points for teams are also used by Objectives (capture-the-flag `Flag` and `Flag Return`)
for those same teams.

### Example:
```
<g inkscape:label="respawn">
    <ellipse cx="64" cy="176" fill="#60b075" rx="16" ry="16" />
    <ellipse cx="10" cy="10" fill="#000000" rx="16" ry="16" />
</g>
```
translates to
```
RespawnPoint {
    Position = (64, 176),
    Team = "60b075"
},
RespawnPoint {
    Position = (10, 10)
}
```



## Objectives
### Required:
 - At least one `ctrl` element for Control matches
 - At least one `flag` and one `return` element per team for Capture-the-Flag matches
### Details:
 - `<ellipse />`
 - Name `"objective"` or `"objectives"`
 - Type determined by color `"fill"` and `"stroke"`

Objectives are points that specify the location of a game objective. They do not
display in-game and instead use built-in objective sprites, depending on the objective type.

The type of an objective is either for Capture-the-Flag game modes, which has two types of objectives,
(`Flag`) and (`Flag Return`) or for Control and King-of-the-Hill which has one (`Control`). Capture-the-Flag
objectives must correspond to a team.

The type is determined by the object's color `"fill"` attribute, where Black (#000000)
is Control, and anything else is different teams for Capture-The-Flag. This color must match a team respawn point color.
`Flag Return` type objectives have a stroke defined (an outline), while `Flag` objectives do not. Stroke color does not matter.

### Note:
Maps that do not have `Control` objectives cannot be used for "Control" and "King of the Hill" gamemodes.

Maps that do not have `Flag` and `Flag Return` objectives cannot be used for "Capture the Flag" gamemodes. 
If not all teams have capture-the-flag objectives, the maximum number of teams for the map will be reduced for 
capture-the-flag only.

A map with no objectives can still be used for "Deathmatch" matches.

### Example:
```
<g inkscape:label="objective">
    <ellipse cx="10" cy="10" fill="#60b075" rx="16" ry="16">
    <ellipse cx="20" cy="25" fill="#60b075" stroke="#000000" rx="16" ry="16">
    <ellipse cx="15" cy="15" fill="#000000" rx="16" ry="16">
</g>
```
translates to
```
Objective {
    Position = (10, 10),
    Type = "flag",
    Team = "60b075"
},
Objective {
    Position = (20, 25),
    Type = "flag-return",
    Team = "60b075"
},
Objective {
    Position = (15, 15),
    Type = "control"
}
```



## Pickups
### Optional
### Details
 - `<ellipse />`
 - Name `"pickup"` or `"pickups"`
 - Resource determined by color `"fill"`

Pickup objects are points that specify the location and type of a pickup. They
do not display in-game and instead use built-in pickup sprites depending
on the pickup type.

The fill color determines the resource of the pickup:
 - #ff0000 (red)   = `health`
 - #0000ff (blue)  = `shield`
 - #00ff00 (green) = `energy`
 - #000000 (black) = `ammo`

### Example
```
<g inkscape:label="pickup">
    <ellipse  cx="64" cy="176" fill="#ff0000" rx="16" ry="16" />
</g>
```
translates to 
```
Pickup {
    Position = (64, 176),
    Type = "health"
}
```



## Waypoints
### Required: at least one
### Details:
 - `<ellipse />`
 - Name `"waypoint"`, `"waypoints"`, or in a `Paths` layer (`"path"`, `"paths"`, `"pathing"`)

Waypoints are points that the AI uses to pathfind. They do not display in-game.
They should be placed near respawn points, objectives, and platform edges
and they should be connected to other waypoints with `Paths`.

Waypoints can also be put in a `Paths` layer and will be parsed correctly.

### Example:
```
<g inkscape:label="waypoint>
    <ellipse cx="64" cy="176" fill="#e27970" rx="16" ry="16" />
</g>
```
translates to
```
Waypoint {
    Position = (64, 176)
}
```



## Paths
### Required: at least one
### Details:
 - `<polyline />`
 - Name `"paths"`, `"path"`, `"pathing"`, or in a `Waypoints` layer (`"waypoint"`, `"waypoints"`)
 - Jump and direction determined by color `"stroke"`

Paths are SVG polyline elements that dictate paths between `Waypoints`. They do not
display in-game and are only used by AI pathfinding. There must be waypoints at each 
path endpoint.
The stroke color specifies whether
the path is bidirectional or one-way, and whether the AI needs to jump or walk when taking
the path. 

The blue channel determines jump, and the red channel determines bidirectionality:
- #ff0000 (red)    = bidirectional walk
- #000000 (black)  = one-way walk (usually falling)
- #0000ff (blue)   = one-way jump
- #ff00ff (purple) = two-way jump

For one-way paths, the first point determines the start point. For paths with more than 2 points,
each set of two sequential points is used to make a path (see Example 3).

Paths can also be put in a `Waypoints` layer and will be parsed correctly. 

### Example 1:
```
<g inkscape:label="paths">
    <polyline fill="none" points="64,176 200,400" stroke="#000000" stroke-linecap="round" stroke-width="6" />
</g>
```
translates to (falling path)
```
Path {
    Start = waypoint closest to (64, 176),
    End = waypoint closest to (200, 400),
    Jump = false
}
```

### Example 2:
```
<g inkscape:label="paths">
    <polyline fill="none" points="64,176 200,400" stroke="#ff00ff" stroke-linecap="round" stroke-width="6" />
</g>
```
translates to 
```
Path {
    Start = waypoint closest to (64, 176),
    End = waypoint closest to (200, 400),
    Jump = true
},
// and the reverse path because it is bidirectional:
Path {
    Start = waypoint closest to (200, 400),
    End = waypoint closest to (64, 176),
    Jump = true
}
```

### Example 3:
```
<g inkscape:label="paths">
    <polyline fill="none" points="50,50 100,100 150,150" stroke="#000000" stroke-linecap="round" stroke-width="6" />
</g>
```
translates to (falling path)
```
Path {
    Start = waypoint closest to (50, 50),
    End = waypoint closest to (100, 100),
    Jump = false
},
Path {
    Start = waypoint closest to (100, 100),
    End = waypoint closest to (150, 150),
    Jump = false
}
```

## Status Zones
### Optional
### Details:
 - `<rect />`
 - Name `"zones"` or `"zone"`
 - Zone type determined by color `"fill"`

 Zones are areas in the game that affect players that enter them. They do not display in-game
 and a map should have visual elements to indicate to players where a zone is.
 Type `death` zones immediately kill players, while the other types afflict their
 corresponding status condition.

 Maps without a bottom platform should have `death` zones below the map to prevent players from
 falling out of the map.

 The object color (`"fill"` attribute) determines the zone type:
 - #000000 (black) = death zone
 - #ff0000 (red)   = `damaged` zone
 - #00ff00 (green) = `marked` zone
 - #0000ff (blue)  = `slowed` zone
 - #ffffff (white) = `disrupted` zone

### Example:
```
<rect fill="#000000 x="10" y="10" width="100" height="20" />
```
translates to
```
KillZone {
    Position = (10, 10),
    Width = 100,
    Height = 20
}
```

### Example:
```
<rect fill="#ff0000" x="10" y="10" width="100" height="20" />
```
translates to
```
StatusZone {
    Position = (10, 10),
    Width = 100,
    Height = 100,
    Status = Damaged
}
```


## Sound Zones
### Optional
### Details:
 - `<rect />`
 - Name `"sounds"` or `"sound zones"`
 - Sound determined by color `"fill"`

Sound zones are areas in the game that play a sound when the player enters them. They do not display in-game.
The fill color of the object determines the sound to play:
 - #000000 (black)   = No Sound
 - #ff0000 (red)     = Wood
 - #ffff00 (yellow)  = Earth
 - #00ff00 (green)   = Plant
 - #00ffff (cyan)    = Splash
 - #0000ff (blue)    = Wade
 - #ff00ff (magenta) = Stone
 - #ffffff (white)   = Metal

These sounds are the same as the `Platform` sounds.

### Example:
```
<g inkscape:label="sounds">
    <rect fill="#ff0000" x="10" y="10" width="100" height="20" />
</g>
```
translates to
```
SoundZone {
    Position = (10, 10),
    Width = 100,
    Height = 20,
    Sound = Wood
}
```


## Particle Zones
### Optional
### Details:
 - `<rect />`
 - Name `"particle zones"` or in a `Particle Emitters` layer (`"particles"`, `"particle emitters"`)
 - Particle type determined by outline `"stroke"`
 - Particle color determined by color `"fill"`

Particle zones are areas in the game that emit particles while a player is in them. 
Other than the particles they create, they do not display in-game.
The outline (`"stroke"` attribute) determines the particle shape and the color
(`"fill"` attribute ) determines the particle color. There are only eight colors
available (Black, Red, Yellow, Green, Cyan, Blue, Purple, White) and it picks the closest one.

Particle types:
 - #000000 (black)   = Sparkle
 - #ff0000 (red)     = Flame
 - #ffff00 (yellow)  = Unused (defaults to Star)
 - #00ff00 (green)   = Cloud
 - #00ffff (cyan)    = Unused (defaults to Sparkle)
 - #0000ff (blue)    = Bubble
 - #ff00ff (magenta) = Unused (defaults to Flame)
 - #ffffff (white)   = Star

 Particle colors:
 - #000000 (black)   = Black
 - #ff0000 (red)     = Red
 - #ffff00 (yellow)  = Orange
 - #00ff00 (green)   = Green
 - #00ffff (cyan)    = Cyan
 - #0000ff (blue)    = Blue
 - #ff00ff (magenta) = Purple
 - #ffffff (white)   = ???

 Particle zones can also be put in a `Particle Emitters` layer and will be parsed correctly.

### Example:
```
<g inkscape:label="particles">
    <rect fill="#0000ff" stroke="#0000ff" x="10" y="10" width="100" height="20" />
</g>
```
translates to
```
ParticleZone {
    Position = (10, 10),
    Width = 100,
    Height = 20,
    Particle = Bubble,
    Color = Blue
}
```



## Particle Emitters
### Optional
### Details:
 - `<ellipse />`
 - Name `"particles"`, `"particle emitters"`, or in a `Particle Zones` layer (`"particle zones"`)
 - Particle type determined by outline `"stroke"`
 - Particle color determined by color `"fill"`

Particle emitters are locations in the game that constantly emit particles.
Other than the particles they create, they do not display in-game.
The outline (`"stroke"` attribute) determines the particle shape and the color
(`"fill"` attribute ) determines the particle color. There are only eight colors
available (Black, Red, Yellow, Green, Cyan, Blue, Purple, White) and it picks the closest one.
It uses the same logic as `Particle Zones` (above).

Particle types:
 - #000000 (black)   = Sparkle
 - #ff0000 (red)     = Flame
 - #ffff00 (yellow)  = Unused (defaults to Star)
 - #00ff00 (green)   = Cloud
 - #00ffff (cyan)    = Unused (defaults to Sparkle)
 - #0000ff (blue)    = Bubble
 - #ff00ff (magenta) = Unused (defaults to Flame)
 - #ffffff (white)   = Star

 Particle colors:
 - #000000 (black)   = Black
 - #ff0000 (red)     = Red
 - #ffff00 (yellow)  = Orange
 - #00ff00 (green)   = Green
 - #00ffff (cyan)    = Cyan
 - #0000ff (blue)    = Blue
 - #ff00ff (magenta) = Purple
 - #ffffff (white)   = ???

 Particle emitters can also be put in a `Particle Zones` layer and will be parsed correctly.

### Example:
```
<g inkscape:label="particles">
    <rect fill="#0000ff" stroke="#0000ff" x="10" y="10" width="100" height="20" />
</g>
```
translates to
```
ParticleZone {
    Position = (10, 10),
    Width = 100,
    Height = 20,
    Particle = Bubble,
    Color = Blue
}
```



## Background
### Optional
### Details:
 - Any SVG element
 - Name `"background"`

Displayed behind everything else, as a Background. Visual only. Moves with the camera so that it does not appear to move on the screen; instead is a static background for the entire map. Will be scaled to cover the viewport (but the aspect ratio will remain the same).
If the map aspect ratio is very different from the viewport (game screen), significant portions of the Backgroudn will be cropped.



## Parallax Layers
### Optional
### Details:
 - Any SVG element
 - Name `"parallax=[depth]"`, where `depth` is a decimal number

Creates a layer with a parallax effect. Decimal and negative values are allowed.

Layers with positive depth appear in the background, while layers with negative depth
appear in the foreground.
Layers with depth 0 are displayed at the same level as players and other game objects, but behind them.

To get elements at the same level as players but in front of them, use elements that do not match a specific name
(see `Additional Visual Effects`).



## Foreground / HUD Overlay
### Optional
### Game Data: none
### Details:
 - Any SVG element
 - Name `"foreground"`

Creates a layer that renders directly on the screen, under the HUD but above everything else.
Like Background, moves with the camera. Objects in this layer do not show up in the minimap
or the map preview.



# Main-Map Only
### Optional
### Game Data: none
### Details:
 - Any SVG element
 - Name `"display"`, `"display only"`, or `"display-only"`

 Creates a visual layer that renders as a near foreground element (see `Additional Visual Effects`), but 
 does not show up in the minimap or the map preview. This is used for the text in the tutorial level.



## Additional Visual Effects
### Optional
### Game Data: none
### Details:
 - Any SVG element
 - all other SVG elements

Any SVG object that isn't one of the above categories is interpreted as a 
near foreground element. These Elements are drawn at the same perspective (parallax) level as players
and game objects, but display in front of them.

What a parallax effect with depth "negative zero" would be.



## Map Metadata
### Optional
### Details:
 - `<text />`
 - Name `"metadata"` or `"meta"`
 - Text is `"key:value"` for `key` = `name` | `description` | `unlock`

This element contains metadata about the map, which is displayed in the level selector. The `name` is a human-readable name for the map, and the `description` is the blurb shown under it in the level selector. The `unlock` is optional and determines which campaign level unlocks
the map for Quick Match (freeplay). The default is the map is always unlocked.

### Example:
```
<robot:data name="Building Map" description="Map description about a map with two sets of stairs." />
<g inkscape:label="metadata">
    <text fill="#000000" font-family="Times New Roman" font-size="80" x="0" y="0">name:Example Map</text>
    <text fill="#000000" font-family="Times New Roman" font-size="18" x="0" y="100">desc:Map description.</text>
    <text fill="#000000" font-family="Times New Roman" font-size="24" x="0" y="200">unlock:level-07</text>
</g>
```
translates to
```
Map {
    Name = "Example Map",
    Description = "Map description.",
    Unlock = "level-07"
}
```

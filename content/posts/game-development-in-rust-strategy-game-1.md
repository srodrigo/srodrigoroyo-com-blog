---
_cmplz_remote_scanned_post: "1"
_cmplz_scanned_post: "1"
_edit_last: "1"
_thumbnail_id: "920"
author: wpx_srodrigoroyo_admin
categories:
  - programming
cmplz_hide_cookiebanner: ""
cover:
  alt: game-development-in-rust-strategy-game-1
  image: /wp-content/uploads/2023/04/game-development-in-rust-strategy-game-1-e1681122216691.png
date: "2023-04-10T13:40:35+00:00"
guid: https://srodrigoroyo.com/?p=881
parent_post_id: null
post_id: "881"
rank_math_analytic_object_id: "20"
rank_math_focus_keyword: game development in rust,strategy game
rank_math_internal_links_processed: "1"
rank_math_og_content_image:
  check: 9dcd1f52b781ba800a2fc7746eba259c
  images:
    - 904
rank_math_primary_category: "6"
rank_math_seo_score: "90"
title: 'Game Development In Rust: Making A Strategy Game (Part 1 - The Battlefield)'
url: /game-development-in-rust-strategy-game-1/

---
I have bitten the sweet, poisoned apple of game development again. Rust is gaining some traction among game developers, and it is time to join the crowd. We will make a small game in Rust to see whether the hype is well deserved.

The game we are going to make is a **2D turned-based strategy game**. I wanted to start with something more classical, but I thought people were tired of the typical starting games. Strategy games have some tricky parts but simplify other areas, so hopefully, they will balance themselves out.

This is going to be a series of articles. In this first article, we will set up the project and create the battlefield. We are going to use the **Bevy engine**. In future series, we might explore other game engines or make our own with a couple of libraries.

NOTE: I am using Linux (Ubuntu 22.10) for this project. The terminal commands I include should work for you as long as you use `bash`. They should also work on other terminals and on MacOS. I might update this series of articles to include Windows in the future, although you could follow along on WSL2 (I haven’t tried running GUI applications on there, so bear with me). In any case, the commands are quite basic, so Windows users shouldn’t have many issues following along.

## Game Development In Rust: Making A Strategy Game (Part 1 - The Battlefield)

Our **2D turn-based strategy game** is going to be rather simple. This is the current list of features I am planning to include:

- There is a **static battlefield**. It would be cool to explore procedural generation later, but I can’t promise anything.
- There are **different units**. Each unit has a variety of stats (range, attack, defense, HP, etc.).
- There are **sound effects**. No music, probably, though (unless I can find a suitable track that people won’t hate after 5 minutes).
- The game supports **2 players**.
- There is a **simple UI**, enough to figure out the current state of the game.

As mentioned before, we are going to use [Bevy](https://bevyengine.org/).

### **Why Bevy?**

To keep it short, because:

- Bevy is currently the **most active Rust game engine**, and the one with the largest community. It has become the de facto game engine in Rust.
- Bevy is an **ECS-based** engine. Programming paradigms are like ice cream, everyone likes different flavors. But I think it is interesting to use non-classical engines that provide a less typical way of working and thinking. This is one of the things that makes us grow as programmers.
- Bevy's **declarative nature** fits very well with Rust's ownership model. Remember that [Rust is closer to ML languages](/getting-started-with-rust/) than to C++, despite being something in between.

A word of caution: Bevy is currently evolving. New minor, 0.x versions include breaking changes. We will use version 0.10 specifically to ensure we can run the project in a few months.

### **Creating the project**

I will create a folder in the [repository](https://github.com/srodrigo/strategy-game-rs) with all the chapters in this series. I could have used branches instead, but it makes the process of backporting improvements harder. You can find this chapter under `01-battlefield/strategy-game-rs`.

If you want to follow along, I recommend having a single folder.

On the terminal, create a new Rust project called `strategy-game-rs` and `cd` into it:

ShellScript

```
cargo init strategy-game-rs

cd strategy-game-rs
```

We are going to use **Bevy version 0.10**. Bevy is still in its early stages. Backward compatibility is not guaranteed. So we should protect our game from breaking by declaring at least the _minor_ version.

ShellScript

```
cargo add bevy@0.10
```

Just to test that everything was set up correctly, we can run the project:

ShellScript

```
cargo run
```

We should see a `Hello, world!` message.

While setting the above up, I encountered a few **issues** on Linux.

If you run into issues with `alsa`, install the missing library:

ShellScript

```
sudo apt install libasound2-dev
```

If you run into issues with `libudev`, install the missing library:

ShellScript

```
sudo apt install libudev-dev
```

The project should compile and run. If you find any other issues, let me know, and I will add them here, as it will depend on your current setup.

I haven’t checked on Windows or MacOS. If this series gets traction, I will try a run-through at least on Windows.

### **Creating a window with Bevy**

The first baby step towards our not-quite-yet award-winning strategy game is to create a window with Bevy.

Edit `src/main.rs` and replace the existing code with the following:

Rust

```
use bevy::prelude::*;

fn main() {
    App::new().run();
}
```

The code above doesn’t seem to do anything. This is expected. **Bevy apps don’t render anything by default**; not even a window.

Let’s try to see at least a console log. Create a `hello_bevy` function that prints a message, and add it as a `system` after `new()`:

Rust

```
use bevy::prelude::*;

fn hello_bevy() {
    println!("Hello Bevy!");
}

fn main() {
    App::new().add_system(hello_bevy).run();
}
```

If we run this program, we should see a `Hello Bevy!` printed out.

Congratulations, we have written our first game in Bevy!

Right?

_Readers_: ‘...’

Technically, yes. From a game developer's perspective, probably not. ‘ _Where are our graphics?!’_ \- I hear the crowd screaming.

Let’s show something on the screen for our satisfaction.

Bevy doesn’t render anything by default, acting as a serverless application. Bevy is a **declarative engine**, and it allows enabling features using **plugins**.

The rendering is declared via plugins as well. I won’t go into the details, but [`DefaultPlugins`](https://docs.rs/bevy/latest/bevy/struct.DefaultPlugins.html) is a convenient selection of plugins that are common to most games. It contains the window, event loop, and input plugins, among others, creating and running a **game loop** for us.

To show our window, we just need to add the `DefaultPlugins`:

Rust

```
use bevy::prelude::*;

fn hello_bevy() {
    println!("Hello Bevy!");
}

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_system(hello_bevy)
        .run();
}
```

If we run our program, Bevy will create an empty window and spam the terminal with `Hello Bevy!` until we close it.

Achievement unlocked, we created our first game window!

### **Rapid-fire introduction to Bevy**

Here is a personal challenge: explain the **minimum basics** of Bevy that are relevant to this series of articles, in the most concise way I can so:

- I won’t bug you with information unrelated to this project.
- You can follow along if you have never worked with an ECS-based engine.

Feel free to **skip this section if you are already familiar with Bevy** or at least with ECS-based engines. You can also refer to the [official guide](https://bevyengine.org/learn/book/getting-started/) if you prefer to look in more detail. I could have just provided a link and called it a day, but it felt unfair not to provide a quick explanation.

#### **ECS**

**Entities** are an ID that represents… an entity in the game. They have a list of components that define the entity.

**Components** are data associated with an entity.

Rust

```
#[derive(Component)]

struct Stats { hp: i32, };
```

**Resources** are data that is not associated with an entity. They act as singleton.

Rust

```
#[derive(Resource)]

struct Clock(Timer);
```

**Systems** are functionality related to a set of components and resources. They are plain functions that take component queries, resources, and commands as parameters.

Rust

```
fn some_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    stats_query: Query<&mut Stats>,
) {
    ...
}
```

**Worlds** contain entities, components, and resources, and provide operations to retrieve them.

#### **Commands**

**Commands** aren’t part of typical ECSs but are used in Rust to manipulate the entities, components, and resources. They can create, add, retrieve, or remove them.

#### **Plugins**

**Plugins** are a way to group systems and resources in a cohesive way.

Rust

```
impl Plugin for MagicPlugin {
    fn build(&self, app: &mut App) {
        app.add_system(fireball_system)
            .add_system(iceball_system)
            .add_system(lightning_system);
    }
}
```

#### **States**

**States** help implement the different states of the application (what screen are we at, what game mode is active, etc.).

Rust

```
enum GameState {
    Running,
    Paused,
}

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_state(AppState::Running);
}
```

#### **Events**

**Events** allow for inter-system communication. They use an `EventWriter` and an `EventReader` inside systems to send/receive data.

Rust

```
struct HiEvent(String);

fn say_hi_system(mut hi: EventWriter<HiEvent>) {
    hi.send(HiEvent("What a nice day today!"));
}

fn print_hi_system(mut hi: EventReader<HiEvent>) {
    for event in hi.iter() {
        println!("Received Hi {:?}", event.0);
    }
}
```

### **Creating the battlefield**

We are going to create the battlefield, where all the blood and tears will be spilled.

Our battlefield will be a **rectangle split into tiles**. We will have a tileset to choose from, so we’ll start by creating a static battlefield.

Our battlefield will look like this:

{{< figure src="/wp-content/uploads/2023/04/word-image-881-1-2.png" alt="Game Development In Rust: Making A Strategy Game (Part 1 - The Battlefield)" caption="Game Development In Rust: Making A Strategy Game (Part 1 - The Battlefield)" >}}

Forgive my lack of artistic skills, but I hope it does the job.

As I’m not a great artist and I didn’t want to waste time creating assets that look horrible, I’m using the [Fantasy Battle Pack](https://mattwalkden.itch.io/fantasy-battle-pack) assets, by Matt Walkden. It looks great, and you can download it for free (at the time of writing). It has a permissive license, so we can use it for our own enjoyment. I’m using the `26-10-22` version.

The tileset we are going to use is located in `Tiles/FullTileset.png`. It looks like this:

{{< figure src="/wp-content/uploads/2023/04/FullTileset.png" alt="Game Development In Rust - The tileset" caption="Game Development In Rust - The tileset" >}}

You can use a different assets pack if you prefer. The concepts to create the battlefield are exactly the same. There were a few good candidates, but I ultimately chose an assets pack that looks good and has **several sprite sheets** instead of a single, massive one.

NOTE: For my life, I tried to find a free assets pack without Sprite sheets. If you don’t know what sprite sheets are, they are a bundle of sprites that are typically related to each other. For example, the different sprites for animating a character. I wanted to avoid them because it’s easier to load single sprites files, but I failed. We’ll have to go with Sprite sheets, an industry standard anyway, so it isn't a bad thing to dive into even if it adds a bit more complexity.

Before we start writing any code, we need to **download and extract the assets** into an `assets` folder at the root of our project. Go ahead and create an `assets` folder at the project root (at the same level as `src/`). Then, extract the assets pack inside the new folder. We should see something like this:

```
strategy-game-rs/

├── assets/

│ ├── Effects/

│ ├── License and Information.rtf

│ ├── Sprite Sheets/

│ ├── Tiles/

│ └── UI Elements/

├── Cargo.lock

├── Cargo.toml

├── src/

│ └── main.rs
```

Before starting working on the battlefield, we can clean our program up, removing the `hello_bevy` function. Make sure that `src/main.rs` only has the following:

Rust

```
use bevy::prelude::*;

fn main() {
    App::new().add_plugins(DefaultPlugins).run();
}
```

To create the battlefield, we need:

- A grid where we will store the information.
- A visual representation of the grid.

**Splitting logic and delivery mechanisms** is usually a good practice in software development. In this case, the delivery mechanism is the visual representation of the grid. We will have other delivery mechanisms late (for example, the sound effects triggered on certain events).

We are going to create the system that will create the battlefield at **startup time**. I called it `create_battlefield_system`:

Rust

```
fn create_battlefield_system() {
    println!("creating battlefield");
}

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_startup_system(create_battlefield_system)
        .run();
}
```

NOTE: some people use the name `board` instead of `battlefield` or `grid`, so use the one you prefer.

`create_battlefield_system` will run once when the game starts up. We don’t want to create the battlefield twice. Bevy will then do its magic to render it along with the rest of the graphics. There is no such thing as a `render` function in Bevy.

#### **Drawing a single tile**

Now, we are going to create the actual battlefield. We are going to start by **loading one tile**, to get an idea about how Bevy handles sprite sheets.

Rust Tip: We will see `::default()` and especially `..default()` quite a bit. This is a **convention method** in Rust to provide default data when instantiating structs:

- `::default()` is useful for creating the default instance for a struct.
- `..default()` is a way to spread the default values we don’t want to specify when we instantiate a struct.

Our `create_battlefield_system` is going to take a few parameters. We are going to need:

- **Commands** to spawn objects such as a camera or sprite sheet bundles.
- **Asset Server** to load the assets.
- **Texture Atlases** to convert the sprite sheets into something that we can conveniently manipulate with code.

We are going to change our `create_battlefield_system` signature to include a few new parameters:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    ...
}
```

The first thing that we need is to load the tileset sprite sheet. `AssetServer` provides a method to load an image:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    let tiles_handle = asset_server.load("Tiles/FullTileset.png");
}
```

Next, we want to convert this sprite sheet into a [texture atlas](https://en.wikipedia.org/wiki/Texture_atlas), so we can index specific tiles later:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    let tiles_handle = asset_server.load("Tiles/FullTileset.png");
    let tiles_atlas =
        TextureAtlas::from_grid(tiles_handle, Vec2::new(16.0, 16.0), 20, 20, None, None);
}
```

You can refer to the [documentation](https://docs.rs/bevy/latest/bevy/sprite/struct.TextureAtlas.html#method.from_grid) for more detail, but we are just specifying:

- **Image handle**: our tiles handle.
- **Tile size**: the `Vec2` parameter.
- **Number of columns and rows** that the sprite sheet has.

Finally, we need to add this atlas to the list of texture atlases:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    let tiles_handle = asset_server.load("Tiles/FullTileset.png");
    let tiles_atlas =
        TextureAtlas::from_grid(tiles_handle, Vec2::new(16.0, 16.0), 20, 20, None, None);
    let tiles_atlas_handle = texture_atlases.add(tiles_atlas);
}
```

The `add` method returns a handle that we can use to reference the atlas.

Now that we have loaded the tiles, we want to display one of them. Bevy provides a few structs for this:

- `Camera2dBundle`: This is Bevy’s 2D camera
- `SpriteSheetBundle`: This is a bit more complicated. `SpriteSheetBundle` takes a texture atlas, a sprite, and a transform.

We only want to display a single tile, so we will add the following code to `create_battlefield_system`:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    let tiles_handle = asset_server.load("Tiles/FullTileset.png");
    let tiles_atlas =
        TextureAtlas::from_grid(tiles_handle, Vec2::new(16.0, 16.0), 20, 20, None, None);
    let tiles_atlas_handle = texture_atlases.add(tiles_atlas);

    commands.spawn(Camera2dBundle::default());

    commands.spawn(SpriteSheetBundle {
        texture_atlas: tiles_atlas_handle,
        sprite: TextureAtlasSprite::new(3),
        transform: Transform::from_scale(Vec3::splat(4.0)),
        ..default()
    });
}
```

Here, we are rendering the 4th tile (index `3`) in the texture atlas and setting a **4x scale** so it doesn’t look too tiny. We are not interested in other parameters, so we use `..default()` to let them take default values.

If everything went well, we should see something like this:

{{< figure src="/wp-content/uploads/2023/04/word-image-881-2-2.png" alt="Game Development In Rust: Battlefield - Single Tile" caption="Game Development In Rust: Battlefield - Single Tile" >}}

Here is the [source code](https://github.com/srodrigo/strategy-game-rs/commit/74f45e9b335b8f1e3a665e3b07563dcf38e1608a#diff-34722cb76ad4411e502832692a34a6268764b36df5445430b71bcf9f1f4ed769R1).

#### **Drawing a 2x2 grid**

We are going to render a grid of 2x2 tiles. This is the next baby step to render our _dream_ battlefield. We want to get an idea of how to iterate over a **multi-dimensional grid**, even if small. The final grid will work in the same way, just with more data.

Before anything, we are going to do some refactoring. We can extract a few **constants** to avoid some magic numbers:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    const NUM_COLUMNS: usize = 20;
    const NUM_ROWS: usize = 20;
    const TILE_SIZE: f32 = 16.0;

    let tiles_handle = asset_server.load("Tiles/FullTileset.png");
    let tiles_atlas = TextureAtlas::from_grid(
        tiles_handle,
        Vec2::new(TILE_SIZE, TILE_SIZE),
        NUM_COLUMNS,
        NUM_ROWS,
        None,
        None,
    );

    ...
}
```

We can also extract the scale as a constant:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    ...

    const SCALE: f32 = 4.0;
    commands.spawn(SpriteSheetBundle {
        texture_atlas: tiles_atlas_handle,
        sprite: TextureAtlasSprite::new(3),
        transform: Transform::from_scale(Vec3::splat(SCALE)),
        ..default()
    });
}
```

We might need to move the new constants into a different scope later. For now, they can remain local to `create_battlefield_system`.

With a cleaned-up base, we can start working on our 2x2 grid. We are going to need a few new concepts:

- **Tile types**: we will render a few different tiles so our battlefield doesn’t look too bland.
- **Tilemap**: we need to define the positions of the tiles in our 2D battlefield.

For now, we only need **4 different tiles**. I chose the first 4 tiles in the first row of the tiles sprite sheet.

{{< figure src="/wp-content/uploads/2023/04/word-image-881-3-2.png" alt="Game Development In Rust: Tileset - 2x2 Grid" caption="Game Development In Rust: Tileset - 2x2 Grid" >}}

To support tile types, we need to keep track of the index in the texture atlas we created earlier. There are a couple of ways of doing this. I created a `TileType` enum and a `Tile` struct that help convert between tile type and index:

Rust

```
use bevy::prelude::*;

enum TileType {
    Brown1,
    Brown2,
    Brown3,
    Brown4,
}

struct Tile {
    index: usize,
}

impl Tile {
    fn from_type(tile_type: TileType) -> Tile {
        match tile_type {
            TileType::Brown1 => Tile { index: 0 },
            TileType::Brown2 => Tile { index: 1 },
            TileType::Brown3 => Tile { index: 2 },
            TileType::Brown4 => Tile { index: 3 },
        }
    }
}
```

We implemented a `Tile` struct with an index that can be created from a `TileType`. [Pattern matching](/getting-started-with-rust/#pattern-matching) helps us not to miss handling any tile type. `index` is the actual index in the `(20, 20)` sprite sheet. Our brown tiles start at `(0, 0)` and go up until `(0, 3)` in the first row.

The next thing to do is to render the battlefield inside `create_battlefield_system`:

Rust

```
fn create_battlefield_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    ...

    const SCALE: f32 = 4.0;
    let tile_map: [[Tile; 2]; 2] = [
        [
            Tile::from_type(TileType::Brown1),
            Tile::from_type(TileType::Brown3),
        ],
        [
            Tile::from_type(TileType::Brown2),
            Tile::from_type(TileType::Brown4),
        ],
    ];

    for (y, row) in tile_map.iter().enumerate() {
        for (x, col) in row.iter().enumerate() {
            commands.spawn(SpriteSheetBundle {
                texture_atlas: tiles_atlas_handle.clone(),
                sprite: TextureAtlasSprite::new(col.index),
                transform: Transform {
                    translation: Vec3 {
                        x: x as f32 * TILE_SIZE * SCALE,
                        y: y as f32 * TILE_SIZE * SCALE,
                        z: 0.0,
                    },
                    scale: Vec3::splat(SCALE),
                    ..default()
                },
                ..default()
            });
        }
    }
}
```

We have defined `tile_map` as a 2x2 array, containing one tile of each type.

Then, we use **iterators** to go through each dimension with `iter().enumerate()`, retrieving both the index ( `y` or `x`) and the iterator ( `row`, `col`). We _spawn_ one `SpriteSheetBundle` for each tile at the `(x, y)` position:

- `texture_atlas` needs the atlas handle, but it can’t borrow it multiple times, so if we try to use it as it is, the Rust compiler will complain. The easiest solution is to `clone` it, so each iteration uses a fresh copy. This shouldn’t be a performance problem since we only create the battlefield once.
- `sprite` now gets the index in the inner iterator, `col.index`.
- `transform` uses `(x, y)`, which are the actual pixel positions of the tile in the sprite sheet, and multiplies it by the tile size and the scale.

This should be the result:

{{< figure src="/wp-content/uploads/2023/04/word-image-881-4-2.png" alt="Game Development In Rust: Battlefield - 2x2 Grid" caption="Game Development In Rust: Battlefield - 2x2 Grid" >}}

One more thing I did is to move `commands.spawn(Camera2dBundle::default())` at the top of `create_battlefield_system`. I like related code to be together when possible.

This is the final [code](https://github.com/srodrigo/strategy-game-rs/blob/12dc753df798e8683441cc609f4d04475b0aa8ed/01-battlefield/strategy-game-rs/src/main.rs) so far and the [diff](https://github.com/srodrigo/strategy-game-rs/commit/12dc753df798e8683441cc609f4d04475b0aa8ed) for this section.

#### **Drawing the final battlefield**

Now that we know how to create a 2x2 grid, the last thing to do is to create the actual battlefield. Since we found a way to render a 2D grid, this is a piece of cake. We _just_ need to **add a bunch of tiles**. I’ve highlighted below the ones that we are going to use.

{{< figure src="/wp-content/uploads/2023/04/word-image-881-5-2.png" alt="Game Development In Rust: Tileset - 13x6 Grid" caption="Game Development In Rust: Tileset - 13x6 Grid" >}}

I don't think there is a pretty way of naming tiles, so I did my best to make them readable. The naming convention I followed is:

- **Color** of the tile.
- **Mix of colors and positions**.
- **Column index**, starting at 1.
- I skipped the green tile in the brown-green middle row in column 5, as it is duplicated.

Rust

```
enum TileType {
    Brown1,
    Brown2,
    Brown3,
    Brown4,
    Green1,
    Green2,
    Green3,
    Green4,
    BrownGreenUpper1,
    BrownGreenUpper2,
    BrownGreenUpper3,
    BrownGreenUpper5,
    BrownGreenUpper7,
    BrownGreenMiddle1,
    BrownGreenMiddle3,
    BrownGreenMiddle4,
    BrownGreenMiddle6,
    BrownGreenLower1,
    BrownGreenLower2,
    BrownGreenLower3,
    BrownGreenLower5,
    BrownGreenLower7,
}
```

We need to calculate the **index** for each of the new tiles. We follow the typical `y * NUM_COLUMNS + x` formula.

Rust

```
impl Tile {
    fn from_type(tile_type: TileType) -> Tile {
        match tile_type {
            TileType::Brown1 => Tile { index: 0 },
            TileType::Brown2 => Tile { index: 1 },
            TileType::Brown3 => Tile { index: 2 },
            TileType::Brown4 => Tile { index: 3 },
            TileType::Green1 => Tile {
                index: 2 * NUM_COLUMNS,
            },
            TileType::Green2 => Tile {
                index: 2 * NUM_COLUMNS + 1,
            },
            TileType::Green3 => Tile {
                index: 2 * NUM_COLUMNS + 2,
            },
            TileType::Green4 => Tile {
                index: 2 * NUM_COLUMNS + 3,
            },
            TileType::BrownGreenUpper1 => Tile {
                index: 7 * NUM_COLUMNS,
            },
            TileType::BrownGreenUpper2 => Tile {
                index: 7 * NUM_COLUMNS + 1,
            },
            TileType::BrownGreenUpper3 => Tile {
                index: 7 * NUM_COLUMNS + 2,
            },
            TileType::BrownGreenUpper5 => Tile {
                index: 7 * NUM_COLUMNS + 4,
            },
            TileType::BrownGreenUpper7 => Tile {
                index: 7 * NUM_COLUMNS + 6,
            },
            TileType::BrownGreenMiddle1 => Tile {
                index: 8 * NUM_COLUMNS,
            },
            TileType::BrownGreenMiddle3 => Tile {
                index: 8 * NUM_COLUMNS + 2,
            },
            TileType::BrownGreenMiddle4 => Tile {
                index: 8 * NUM_COLUMNS + 3,
            },
            TileType::BrownGreenMiddle6 => Tile {
                index: 8 * NUM_COLUMNS + 5,
            },
            TileType::BrownGreenLower1 => Tile {
                index: 9 * NUM_COLUMNS,
            },
            TileType::BrownGreenLower2 => Tile {
                index: 9 * NUM_COLUMNS + 1,
            },
            TileType::BrownGreenLower3 => Tile {
                index: 9 * NUM_COLUMNS + 2,
            },
            TileType::BrownGreenLower5 => Tile {
                index: 9 * NUM_COLUMNS + 4,
            },
            TileType::BrownGreenLower7 => Tile {
                index: 9 * NUM_COLUMNS + 6,
            },
        }
    }
}
```

Since `NUM_COLUMNS` is not used in an outer scope, we can move it to the top of the file along with `NUM_ROWS`.

Rust

```
const NUM_COLUMNS: usize = 20;
const NUM_ROWS: usize = 20;
```

The remaining change is to play around with tile types and fill up a **13x6 grid**. I spent quite some time (more than I was hoping) coming up with a decent-looking battlefield (at least for my artistic level). The types of tiles don’t matter, as they are all ground and don’t have any special treatment.

This is what I came up with, which matches the image at the beginning of this section:

Rust

```
    const SCALE: f32 = 4.0;
    const BATTLEFIELD_WIDTH_IN_TILES: usize = 13;
    const BATTLEFIELD_HEIGHT_IN_TILES: usize = 6;
    let tile_map: [[Tile; BATTLEFIELD_WIDTH_IN_TILES]; BATTLEFIELD_HEIGHT_IN_TILES] = [
        [
            Tile::from_type(TileType::Brown1),
            Tile::from_type(TileType::BrownGreenLower1),
            Tile::from_type(TileType::BrownGreenLower2),
            Tile::from_type(TileType::BrownGreenLower2),
            Tile::from_type(TileType::BrownGreenLower3),
            Tile::from_type(TileType::BrownGreenLower5),
            Tile::from_type(TileType::Brown2),
            Tile::from_type(TileType::BrownGreenLower1),
            Tile::from_type(TileType::BrownGreenLower3),
            Tile::from_type(TileType::BrownGreenLower5),
            Tile::from_type(TileType::BrownGreenLower1),
            Tile::from_type(TileType::BrownGreenLower3),
            Tile::from_type(TileType::Brown4),
        ],
        [
            Tile::from_type(TileType::Brown3),
            Tile::from_type(TileType::BrownGreenMiddle1),
            Tile::from_type(TileType::Green2),
            Tile::from_type(TileType::BrownGreenUpper2),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::BrownGreenLower2),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::Green2),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::BrownGreenMiddle3),
            Tile::from_type(TileType::BrownGreenMiddle1),
            Tile::from_type(TileType::BrownGreenLower3),
        ],
        [
            Tile::from_type(TileType::BrownGreenMiddle4),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::BrownGreenMiddle3),
            Tile::from_type(TileType::BrownGreenLower5),
            Tile::from_type(TileType::BrownGreenMiddle1),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::Green4),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green2),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green2),
            Tile::from_type(TileType::BrownGreenUpper3),
        ],
        [
            Tile::from_type(TileType::BrownGreenMiddle4),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::Green4),
            Tile::from_type(TileType::Green2),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::BrownGreenMiddle3),
            Tile::from_type(TileType::BrownGreenUpper7),
            Tile::from_type(TileType::BrownGreenUpper1),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::BrownGreenMiddle6),
        ],
        [
            Tile::from_type(TileType::Brown2),
            Tile::from_type(TileType::BrownGreenUpper1),
            Tile::from_type(TileType::Green4),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::Green2),
            Tile::from_type(TileType::BrownGreenUpper2),
            Tile::from_type(TileType::Green1),
            Tile::from_type(TileType::Green3),
            Tile::from_type(TileType::BrownGreenLower2),
            Tile::from_type(TileType::Green4),
            Tile::from_type(TileType::Green2),
            Tile::from_type(TileType::BrownGreenMiddle6),
        ],
        [
            Tile::from_type(TileType::Brown1),
            Tile::from_type(TileType::Brown4),
            Tile::from_type(TileType::BrownGreenUpper5),
            Tile::from_type(TileType::BrownGreenUpper1),
            Tile::from_type(TileType::BrownGreenUpper3),
            Tile::from_type(TileType::BrownGreenUpper1),
            Tile::from_type(TileType::BrownGreenLower7),
            Tile::from_type(TileType::BrownGreenUpper3),
            Tile::from_type(TileType::BrownGreenUpper5),
            Tile::from_type(TileType::BrownGreenUpper1),
            Tile::from_type(TileType::BrownGreenUpper2),
            Tile::from_type(TileType::BrownGreenUpper3),
            Tile::from_type(TileType::Brown2),
        ],
    ];
```

We can also declare two new `BATTLEFIELD_WIDTH_IN_TILES` and `BATTLEFIELD_HEIGHT_IN_TILES` constants with the appropriate values, right before the `tile_map`.

I won’t paste the final code because it is a bit long, but you can find it [here](https://github.com/srodrigo/strategy-game-rs/blob/78d97454d00303031f955e5db4c6542f61142e48/01-battlefield/strategy-game-rs/src/main.rs) with the [diff](https://github.com/srodrigo/strategy-game-rs/commit/78d97454d00303031f955e5db4c6542f61142e48).

If you run the project, you will see that it renders a battlefield but doesn’t look quite right:

{{< figure src="/wp-content/uploads/2023/04/word-image-881-6-2.png" alt="Game Development In Rust: Battlefield - 13x6 Grid" caption="Game Development In Rust: Battlefield - 13x6 Grid" >}}

There are **a few issues**:

- The **battlefield is not centered**.
- The **tiles look blurry** and have some **“bleeding”** between them.
- The **window is too big** for what we really need.

Let’s fix'em all!

#### **Improving the battlefield**

Right now, our battlefield doesn’t even fit in the window. This is far from ideal.

Let’s center the battlefield. There are different approaches. We could:

1. **Move the camera** to the bottom-left (currently, the camera coordinates are set to the center of the window), or:
1. **Center the battlefield** and leave the camera as it is, or:
1. **Make the window bigger**: if you try resizing and making the window wider, you will see the whole battlefield.

Which approach to take is a matter of taste and context. Some game engines and frameworks (for example, [Pico-8](https://www.lexaloffle.com/pico-8.php)) go for option 1. Option 3 also involves manipulating the window size. The problem with these approaches is that we should set a standard fixed size and not allow for resizing as it doesn’t benefit our gameplay experience much.

**Option 2 is a cleaner approach for us**, even if it needs a bit more math. As a plus, if we later decide to make the battlefield larger, we don’t need to change anything apart from the dimensions of the battlefield to make it look good (centered).

To center the battlefield, we need to reposition it half its width to the left and half its height to the bottom. Something like this in pseudocode:

```
x - battlefield_width / 2 + tile_size / 2

y - battlefield_height / 2 + tile_size / 2
```

Note that we need to add half of the tile size back. Otherwise, it will look a bit off.

Let’s do this in Rust. We only need to change the `translation` vector to the following:

Rust

```
    const HALF_TILE_SIZE: f32 = TILE_SIZE / 2.0;
    const HALF_BATTLEFIELD_WIDTH_IN_PIXELS: f32 =
        BATTLEFIELD_WIDTH_IN_TILES as f32 * TILE_SIZE / 2.0;
    const HALF_BATTLEFIELD_HEIGHT_IN_PIXELS: f32 =
        BATTLEFIELD_HEIGHT_IN_TILES as f32 * TILE_SIZE / 2.0;
    const WIDTH_CENTER_OFFSET: f32 = HALF_BATTLEFIELD_WIDTH_IN_PIXELS - HALF_TILE_SIZE;
    const HEIGHT_CENTER_OFFSET: f32 = HALF_BATTLEFIELD_HEIGHT_IN_PIXELS - HALF_TILE_SIZE;

    for (y, row) in tile_map.iter().enumerate() {
        for (x, col) in row.iter().enumerate() {
            commands.spawn(SpriteSheetBundle {
                texture_atlas: tiles_atlas_handle.clone(),
                sprite: TextureAtlasSprite::new(col.index),
                transform: Transform {
                    translation: Vec3 {
                        x: SCALE * (x as f32 * TILE_SIZE - WIDTH_CENTER_OFFSET),
                        y: SCALE * (y as f32 * TILE_SIZE - HEIGHT_CENTER_OFFSET),
                        z: 0.0,
                    },
                    scale: Vec3::splat(SCALE),
                    ..default()
                },
                ..default()
            });
        }
    }
```

Extracting constants for the calculations is optional. We could just inline them, as it won’t make a big difference in performance. But I prefer to give them a name to make it easier to understand what is going on.

Note that we multiply everything by `SCALE` to keep things in place. We’ll change this shortly, as this is not ideal.

Now, our battlefield should be centered on the screen.

{{< figure src="/wp-content/uploads/2023/04/word-image-881-7-2.png" alt="Game Development In Rust: Battlefield - Centered" caption="Game Development In Rust: Battlefield - Centered" >}}

The tiles still look like they have got some “bleeding”. This is because we are scaling the sprites, but Bevy is rendering them with an algorithm that tries to smooth them out. We need to tell Bevy to use the `nearest` algorithm and disable the antialiasing. We do this by adding the following to the `App` instance:

- A new `Msaa::Off` resource to remove the aliasing.
- An `ImagePlugin` that uses `nearest` sampling.

Rust

```
fn main() {
    App::new()
        .insert_resource(Msaa::Off)
        .add_plugins(DefaultPlugins.set(ImagePlugin::default_nearest()))
        .add_startup_system(create_battlefield_system)
        .run();
}
```

This should fix the blurry sprites as well.

If we run the game, we should see something like this:

{{< figure src="/wp-content/uploads/2023/04/word-image-881-8-2.png" alt="Game Development In Rust: Battlefield - Fixed Blurriness" caption="Game Development In Rust: Battlefield - Fixed Blurriness" >}}

NOTE: We could have used a [plugin](https://github.com/drakmaniso/bevy_pixel_camera) that probably does the job better than my solution, but I didn’t want to introduce an extra crate just for this tweak.

#### **Improving the window**

The window is too big. I am not sure how large it will be later on when we add more functionality. We might need to scale the sprites down to make room for some UI. But, for now, we don’t need as much space. We can set a pretty standard `960x540` resolution with the `WindowPlugin`:

Rust

```
fn main() {
    App::new()
        .insert_resource(Msaa::Off)
        .add_plugins(
            DefaultPlugins
                .set(WindowPlugin {
                    primary_window: Some(Window {
                        resolution: WindowResolution::new(960.0, 540.0),
                        ..default()
                    }),
                    ..default()
                })
                .set(ImagePlugin::default_nearest()),
        )
        .add_startup_system(create_battlefield_system)
        .run();
}
```

Now, the window looks smaller compared to the battlefield:

{{< figure src="/wp-content/uploads/2023/04/word-image-881-9-2.png" alt="Game Development In Rust: Battlefield - Set Resolution" caption="Game Development In Rust: Battlefield - Set Resolution" >}}

We should add a proper title as well. We can do that by adding the `title` attribute:

Rust

```
fn main() {
    App::new()
        .insert_resource(Msaa::Off)
        .add_plugins(
            DefaultPlugins
                .set(WindowPlugin {
                    primary_window: Some(Window {
                        title: "Strategy Game in Rust".to_string(),
                        resolution: WindowResolution::new(960.0, 540.0),
                        ..default()
                    }),
                    ..default()
                })
                .set(ImagePlugin::default_nearest()),
        )
        .add_startup_system(create_battlefield_system)
        .run();
}
```

The last thing to do is to **adjust the scale once** instead of everywhere. We can do that by adjusting the window resolution:

Rust

```
fn main() {
    App::new()
        .insert_resource(Msaa::Off)
        .add_plugins(
            DefaultPlugins
                .set(WindowPlugin {
                    primary_window: Some(Window {
                        title: "Strategy Game in Rust".to_string(),
                        resolution: WindowResolution::new(960.0, 540.0)
                            .with_scale_factor_override(4.0),
                        ..default()
                    }),
                    ..default()
                })
                .set(ImagePlugin::default_nearest()),
        )
        .add_startup_system(create_battlefield_system)
        .run();
}
```

We can either move the `SCALE` constant into `main`, or we can inline it as it is used only once, and `with_scale_factor_override` reads well enough to figure out what the magic number is about (not all magic numbers are evil).

Don’t forget to add `window::WindowResolution` at the top of the file:

Rust

```
use bevy::{prelude::*, window::WindowResolution};
```

We will also need to remove the scale on the sprite sheet `transform`:

Rust

```
    for (y, row) in tile_map.iter().enumerate() {
        for (x, col) in row.iter().enumerate() {
            commands.spawn(SpriteSheetBundle {
                texture_atlas: tiles_atlas_handle.clone(),
                sprite: TextureAtlasSprite::new(col.index),
                transform: Transform {
                    translation: Vec3 {
                        x: x as f32 * TILE_SIZE - WIDTH_CENTER_OFFSET,
                        y: y as f32 * TILE_SIZE - HEIGHT_CENTER_OFFSET,
                        z: 0.0,
                    },
                    ..default()
                },
                ..default()
            });
        }
    }
```

If everything went well, we should see the same image I included at the beginning of this article:

{{< figure src="/wp-content/uploads/2023/04/word-image-881-10-2.png" alt="Game Development In Rust: Battlefield - Final" caption="Game Development In Rust: Battlefield - Final" >}}

Here is the final [code](https://github.com/srodrigo/strategy-game-rs/blob/eb6e1d9ff1d902b193a35080707a15f98fd6b38c/01-battlefield/strategy-game-rs/src/main.rs) and the [diff](https://github.com/srodrigo/strategy-game-rs/commit/eb6e1d9ff1d902b193a35080707a15f98fd6b38c) for this section.

## Conclusion

Apologies for the long article 🙂 I wanted to get the battlefield done to have a solid foundation for the rest of this series, so I couldn't find a logical way to split it up. I hope you managed to follow along if you chose to.

In the [next article](/game-development-in-rust-making-a-strategy-game-2/), we will render some characters (units). We will reuse some ideas to render the sprites, so it should be a digestible article before we get into animations and game logic.

---
_cmplz_remote_scanned_post: "1"
_cmplz_scanned_post: "1"
_edit_last: "1"
_thumbnail_id: "948"
author: wpx_srodrigoroyo_admin
categories:
  - programming
cmplz_hide_cookiebanner: ""
cover:
  alt: Game Development In Rust Part 2
  image: /wp-content/uploads/2023/05/strategy-game-in-rust-2.jpeg
date: "2023-05-14T16:17:25+00:00"
guid: https://srodrigoroyo.com/?p=936
parent_post_id: null
post_id: "936"
rank_math_analytic_object_id: "21"
rank_math_focus_keyword: game development in rust,strategy game
rank_math_internal_links_processed: "1"
rank_math_primary_category: "6"
rank_math_seo_score: "88"
title: 'Game Development In Rust: Making A Strategy Game (Part 2 - Adding The First Unit)'
url: /game-development-in-rust-making-a-strategy-game-2/

---
In [Part 1](/game-development-in-rust-strategy-game-1/) of Game Development In Rust: Making A Strategy Game, we created a basic battlefield to get us going. It is time to add some units so it doesn’t look that lonely.

This article aims to **load a unit in and render it** on the battlefield and improve our codebase to accommodate this new feature.

We are going to face a couple of interesting decisions along the way:

- Loading sprites that are not compact inside the sprite sheet.
- How to structure our systems.
- Dependencies between entities in our ECS.

## Game Development In Rust: Making A Strategy Game (Part 2 - Adding The First Unit)

As discussed, we will add a few units for our strategy game in Rust. We should start with adding a single unit. In the next article, we will attempt to clutter populate the battlefield with all sorts of warriors and wizards.

### Rendering the first unit

In our previous article, we created a _startup system_ to create the battlefield. We can do something similar and **create a startup system to spawn our units**. A `create_units_system` sounds suitable for this task.

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
        .add_startup_system(create_units_system)
        .run();
}
```

Rust

```
fn create_units_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
}
```

The way to spawn units is similar to the way to spawn tiles. We need to:

- Load the **sprite sheet** image.
- Create a **texture atlas**.
- Spawn the **sprite sheet bundle** at the desired position.

For now, we’ll only render a **static sprite without any animations**. Don’t worry, we will cover this in a later article.

You can add any unit you like from the assets. To keep it simple and standard, I’ll go for the **first archer variant** (I love archers), located in `assets/Sprite Sheets/Archer/Archer_Blue1.png`. It comes in a sprite sheet with three groups of sprites for different directions and animations. I will only render the sprite at the top left for now.

{{< figure src="/wp-content/uploads/2023/05/image-1.png" alt="Blue archer sprite sheet" caption="Blue archer sprite sheet" >}}

Game Development In Rust: Blue archer sprite sheet

If we inspect the sprite sheet, we will find something interesting. The first thing to notice is that, compared to the tiles sprite sheet, **the sprites aren’t aligned to the edges of the image**, but have an _offset_. The sprites also have some _padding_ between them. This is to give room for some animations and unit types that require wider or taller sprites.

For example, the _lance knight_ is quite bulky and has… well, a lance, so it needs more room than our archer. We will need to consider this when we load the sheet.

Even if we only need one sprite for now, we still need to load the first group of 12 sprites. We will use more of these sprites later, so they will be already accessible. We can safely ignore the other two groups that face down and up, as we won’t use them in this game.

Inside `create_units_system`, we can start by **loading the sprite sheet image**:

Rust

```
fn create_units_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
        let archer_blue_light_handle = asset_server.load("Sprite Sheets/Archer/Archer_Blue1.png");
}
```

The code above is similar to how we loaded the tiles sprite sheet. Please note I tested this on Linux and Windows but haven’t tried it on MacOS. I expect Bevy to be clever enough to handle the whitespace at the beginning of the file path. And MacOS is similar to Linux anyway. If this doesn’t work for you, feel free to rename the `Sprite Sheets` folder to something like `Sprite_Sheets`.

Next, we need to **create the texture atlas from the image**. Here is where the _offset_ and the _padding_ come into place. I suggest that we consider the following:

- The sprite sheet is 256x448 pixels. It is an 8x14 grid with 32x32 pixels size.
- The sprites vary in size, but we should be safe if we assume they are 32x32 pixels. The offset is between 4 and 8 pixels, but we can ignore it. The same goes for the padding.
- Half of the sprite sheet is empty (right-hand side). We can ignore it, as well as the last two groups of 12 sprites each.

So we will be loading a **4x4 grid of 32x32 sprites**. For now, we can render 32x32 sprites, since they’ve got transparency. Some sprites are centered and a bit smaller (almost 16x16), while others use up more space. So we need to treat them generically.

We can create a few constants with this information and call `TextureAtlas::from_grid` with the correct parameters.

Rust

```
fn create_units_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    const SPRITE_SIZE: f32 = 32.0;
    const NUM_COLUMNS: usize = 4;
    const NUM_ROWS: usize = 4;

    let archer_blue_light_handle = asset_server.load("Sprite Sheets/Archer/Archer_Blue1.png");
    let archer_atlas = TextureAtlas::from_grid(
        archer_blue_light_handle,
        Vec2::new(SPRITE_SIZE, SPRITE_SIZE),
        NUM_COLUMNS,
        NUM_ROWS,
        None,
        None,
    );

    let archer_atlas_handle = texture_atlases.add(archer_atlas);
}
```

We can index the sprites sequentially as we did with the tiles. I have also added the atlas handle, which we need to spawn the sprite(s).

The last step is to **spawn the** `SpriteSheetBundle`:

- We want to render the first sprite. The index will be `0`.
- We can define any position we want. For example, we can place the archer at `(0, 0)` _inside_ the battlefield grid.
- Since sprites are 32x32 and are a bit padded, we can adjust their position by adding an offset. I have chosen a quarter of the sprite size for each dimension (we might tweak this down the road).
- We should **give** `z` **a higher value than the tiles**. Normally, sprites rendered after tiles should display on top. However, I saw some weird behavior in Bevy as I wrote this article. Some sprites wouldn’t render on top of the tiles. It looks like Bevy doesn’t render sprites in the same order they are spawned, so we need to keep an eye on this. Not a big issue since it is a good idea to render the units in a different z layer regardless.

Rust

```
fn create_units_system(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    ...

    let archer_atlas_handle = texture_atlases.add(archer_atlas);

    commands.spawn(SpriteSheetBundle {
        texture_atlas: archer_atlas_handle,
        sprite: TextureAtlasSprite::new(0),
        transform: Transform {
            translation: Vec3 {
                x: 0.0 * SPRITE_SIZE + SPRITE_SIZE / 4.0 - WIDTH_CENTER_OFFSET,
                y: 0.0 * SPRITE_SIZE + SPRITE_SIZE / 4.0 - HEIGHT_CENTER_OFFSET,
                z: 1.0,
            },
            ..default()
        },
        ..default()
    });
}
```

If we try to compile our game, it won’t work. The compiler complains about `WIDTH_CENTER_OFFSET` and `HEIGHT_CENTER_OFFSET`. If you followed along with Part 1, we used these variables to center the tiles on the screen. In other words, we used these variables to create **relative coordinates for the battlefield**.

Later on, we will have a UI with the player turn and other elements. The battlefield will still be centered on the screen, but from our game logic perspective, it is the key element where all the action happens. So we want our units, or any other gameplay elements, to be inside the battlefield. This means we can make our lives easier if we take the battlefield position as the relative position for the rest of the gameplay elements.

There is an issue with the above idea. At the moment, we need to share the knowledge of where the battlefield position is. This is not ideal because we would need to modify every element on the battlefield if we render the battlefield somewhere else. Of course, we can make the position (or the adjustment in this case) global, so everything can use it to position themselves. But this creates _**coupling**_ we neither need nor should have.

For now, we can take the easy path and make `WIDTH_CENTER_OFFSET` and `HEIGHT_CENTER_OFFSET` global, so we don’t stay in “red” (compiler errors) for too long. But we will refactor the battlefield shortly to accommodate for placing units relative to its position.

Let’s go ahead and move the `WIDTH_CENTER_OFFSET` and `HEIGHT_CENTER_OFFSET` constants, and all of their dependencies in `create_battlefield_system` to the outermost scope at the top of the file. We can place them below the `NUM_COLUMNS` and `NUM_ROWS` constants to keep them together.

Rust

```
const NUM_COLUMNS: usize = 20;
const NUM_ROWS: usize = 20;
const BATTLEFIELD_WIDTH_IN_TILES: usize = 13;
const BATTLEFIELD_HEIGHT_IN_TILES: usize = 6;
const TILE_SIZE: f32 = 16.0;
const HALF_TILE_SIZE: f32 = TILE_SIZE / 2.0;
const HALF_BATTLEFIELD_WIDTH_IN_PIXELS: f32 = BATTLEFIELD_WIDTH_IN_TILES as f32 * TILE_SIZE / 2.0;
const HALF_BATTLEFIELD_HEIGHT_IN_PIXELS: f32 = BATTLEFIELD_HEIGHT_IN_TILES as f32 * TILE_SIZE / 2.0;
const WIDTH_CENTER_OFFSET: f32 = HALF_BATTLEFIELD_WIDTH_IN_PIXELS - HALF_TILE_SIZE;
const HEIGHT_CENTER_OFFSET: f32 = HALF_BATTLEFIELD_HEIGHT_IN_PIXELS - HALF_TILE_SIZE;
```

If we did everything correctly, we should see our blue archer positioned at the 1st column and 1st row (starting from the bottom-left, per Bevy’s coordinates system) on the battlefield.

{{< figure src="/wp-content/uploads/2023/05/image.png" alt="First unit in the battlefield" caption="First unit in the battlefield" >}}

Strategy Game: First unit in the battlefield

Here is the [diff](https://github.com/srodrigo/strategy-game-rs/commit/391ca0def465aa120ccb96e969cff5412b6cd68b) for this section.

### Refactoring the Battlefield

If you are like me, your programmer OCD must be between the middle and high range levels right now. There are a couple of issues with our code:

- Global constants related to the battlefield are also used to create units.
- Name clashes: Our `NUM_COLUMNS` and `NUM_ROWS` in `create_units_system` shadow the outer constants for the battlefield.

This tells us something. **The battlefield and the units don’t need to know about each other**, even if they are related.

My long-term plan is to move the battlefield system into its own module and do the same with the units one. We don’t want our battlefield constants to leak and be available for other domain concepts because they are implementation details. This might be a premature abstraction though, and we might change it as we go in later articles. But something is screaming about a lacking abstraction, and I think it is important to start thinking about our _domain_ structure.

For now, I suggest the following:

- Create an abstraction for the battlefield. We’ll discuss this in detail shortly.
- Make this abstraction available in our ECS.
- Use the **battlefield abstraction to position units relative to the battlefield**, without units knowing about the battlefield position.

There are different ways of approaching this problem. I went for the idea above, but YMMV. We could make the battlefield know about units, or we can make units know about the battlefield. And we need to do this in a way that implementation details of either aren’t shared.

In a non-ECS scenario, I would say that the battlefield should know about the units. This is because the battlefield represents a grid where units are placed and move around. With this approach, neither the battlefield nor the units depend on each other. We just use the battlefield to convert the position of the units.

Our ECS engine requires a different approach though. In my mind:

- The **battlefield** is asking to be a **_resource_**(more on this in a minute).
- The **units** and any other entities will be globally **accessible** from the systems, which will subscribe to the **_components_** they are interested in.
- The **systems** will use all this **data** to define the **behavior** of our game.

_Resources_ are a different element than _entities_ in an ECS:

- Entities are just an ID and don’t (or, rather, _shouldn’t_) contain any logic. There can be many entities with the same kind of components.
- **Resources are** usually **unique**. There won’t be two battlefields in a given scene.

I might be being naughty and breaking some ECS “purity” rules, but I’m going to suggest **making the battlefield a resource** (which should probably be anyway, as it is a unique piece of data) and adding some logic to it. One of the pieces in our puzzle needs to know about the battlefield position, and I can’t think of a better option than the battlefield itself.

Time (and future articles) will say whether this was the right decision 🙂 For the time being, we can make the battlefield encapsulate the knowledge about its dimensions, and provide a way to transform a position into coordinates relative to its position. This will help us remove dependencies with global constants and move the unit/battlefield dependency into the ECS, where it should live.

Let’s get our hands dirty. We need to create a new Battlefield struct and declare it as a Bevy resource.

Rust

```
#[derive(Resource)]
struct Battlefield {}

const NUM_COLUMNS: usize = 20;
...
```

As discussed, we want to provide the following:

- The necessary information to create the battlefield.
- A **function to convert from global coordinates to battlefield ones**. This function encapsulates the details of the battlefield position.

We can start by implementing a `default` method for our battlefield.

Rust

```
#[derive(Resource)]
struct Battlefield {}

impl Battlefield {
    pub fn default() -> Self {
        Self {}
    }
}
```

We don’t have any data yet, but we can start moving it into the struct. If we look at `create_battlefield_system`, we need:

- Tile size
- Tilemap

We can start moving this data into our new struct, which will have the nice side-effect of cleaning `create_battlefield_system` up quite a bit. In Part 1, we crammed everything into this function to keep things simple, but now it’s time to improve it bit by bit.

The most straightforward starting point is the tile size. We can add a `tile_size` field in our struct.

Rust

```
#[derive(Resource)]

struct Battlefield {
    tile_size: f32,
}

impl Battlefield {
    pub fn default() -> Self {
        Self { tile_size: 16.0 }
    }
}
```

Notice that we can get rid of the original `TILE_SIZE` constant (don’t remove it just yet though!). This has two benefits:

- Shorter code.
- We force any other parts of the code to use the battlefield if they want to know about the tile size, making dependencies more explicit.

If you are a _magic numbers_ purist, feel free to keep the constant. But I think `tile_size: 16.0` is self-explanatory in this particular case.

The next step in our epic refactoring is to **add the battlefield as a resource** for our `create_battlefield_system` system. We already have a resource in our game ( `Msaa::Off`), so we need to do something similar for our new battlefield.

Rust

```
fn main() {
    App::new()
        .insert_resource(Msaa::Off)
        .insert_resource(Battlefield::default())
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
        .add_startup_system(create_units_system)
        .run();
}
```

The good thing about using `default` is that we can add more fields in our struct and not worry about adding them when we create the instance. Since we only instantiate the battlefield once, this is okay. If we were to spawn multiple battlefields in multiple stages, using `new` and passing in some parameters would be preferred.

Next, we retrieve the new battlefield resource in the system and use it to access the tile size.

Rust

```
fn create_battlefield_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    commands.spawn(Camera2dBundle::default());

    let tiles_handle = asset_server.load("Tiles/FullTileset.png");
    let tiles_atlas = TextureAtlas::from_grid(
        tiles_handle,
        Vec2::new(battlefield.tile_size, battlefield.tile_size),
        NUM_COLUMNS,
        NUM_ROWS,
        None,
        None,
    );

    ...

    for (y, row) in tilemap.iter().enumerate() {
        for (x, col) in row.iter().enumerate() {
            commands.spawn(SpriteSheetBundle {
                texture_atlas: tiles_atlas_handle.clone(),
                sprite: TextureAtlasSprite::new(col.index),
                transform: Transform {
                    translation: Vec3 {
                        x: x as f32 * battlefield.tile_size - WIDTH_CENTER_OFFSET,
                        y: y as f32 * battlefield.tile_size - HEIGHT_CENTER_OFFSET,
                        z: 0.0,
                    },
                    ..default()
                },
                ..default()
            });
        }
    }
}
```

We can run `cargo run` to ensure we are on track and the code still compiles and behaves as before. Here is the [diff](https://github.com/srodrigo/strategy-game-rs/commit/c481bf5d0a55180c940e87569f3f2f145410cb16) for this step.

We can’t remove `TILE_SIZE` yet, as it is used by other constants. But now, this should be its only remaining usage.

The next step is similar, and we will **clean up** a big chunk of data in our `create_battlefield_system`. We are going to move the tilemap into the struct. Go ahead and add a new field and move the related constants for better co-location.

Rust

```
const BATTLEFIELD_WIDTH_IN_TILES: usize = 13;
const BATTLEFIELD_HEIGHT_IN_TILES: usize = 6;

#[derive(Resource)]
struct Battlefield {
    tile_size: f32,
    tilemap: [[Tile; BATTLEFIELD_WIDTH_IN_TILES]; BATTLEFIELD_HEIGHT_IN_TILES],
}
```

The type of the tilemap is quite long, but we’ll fix this in a second.

We also need to add the tilemap to the instantiation of the battlefield. Here is when things start getting interesting.

Rust

```
impl Battlefield {
    pub fn default() -> Self {
        Self {
            tile_size: 16.0,
            tilemap: [
                [
                    ...
                ],
            ],
        }
    }
}
```

For the sake of brevity and a small footprint, I’ve omitted the contents of the tilemap, but they are the same as before.

We also need to retrieve the tilemap from the battlefield resource in the system.

Rust

```
fn create_battlefield_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    ...

    for (y, row) in battlefield.tilemap.iter().enumerate() {
        for (x, col) in row.iter().enumerate() {
            commands.spawn(SpriteSheetBundle {
                ...
            });
        }
    }
}
```

Our system is starting to look much cleaner now that we are moving the data. If we compile and run, we should still see the battlefield rendering correctly. Here is the [diff](https://github.com/srodrigo/strategy-game-rs/commit/8f9a576bf26f833372b00ffe8d297991b7d089b8) of this refactoring.

One more thing we can do is to rename the `NUM_COLUMNS` and `NUM_ROWS` constants related to the battlefield sprite sheet, so they don’t clash with the ones in the units system. We don’t want this data to be in the Battlefield struct because it doesn’t quite belong there.

The actual dimensions of the battlefield are given by the tilemap, not by the sprite sheet. Since systems are responsible for loading the sprite sheets, we can leave it as it is for now and keep an eye out for opportunities to abstract this away.

I’ve chosen `BATTLEFIELD_NUM_COLUMNS` and `BATTLEFIELD_NUM_ROWS`. I’ll share the [diff](https://github.com/srodrigo/strategy-game-rs/commit/62fafb4f0f4f319438982ea7c082d1f98a341657) instead of pasting the code, as we are just renaming this constant.

**NOTE:** I’m doing quite a lot of refactorings without unit tests. This is intended. We are covering a lot of content here, and adding even more things to learn or go through into the mix would probably be too much. Learning Bevy, making a strategy game, and TDD at once can challenge even great developers. At some point, I would like to tackle the TDD/automated testing side, but I’ll leave it for now. Baby steps!

### Using the battlefield to position units

After all this arduous refactoring, we are now ready to move the responsibility of transforming the position of the units into the battlefield. Those `WIDTH_CENTER_OFFSET` and `HEIGHT_CENTER_OFFSET` constants are still bugging me too much to pardon them.

We are going to create a function called `to_battlefield_coordinates` in our `Battlefield` implementation. This function will take `(x, y, z)` and return a `Vec3` positioned relative to the battlefield coordinates.

After `new`, inside `impl Battlefield`, we can add the following:

Rust

```
    pub fn to_battlefield_coordinates(&self, x: f32, y: f32, z: f32) -> Vec3 {
        let half_tile_size: f32 = self.tile_size / 2.0;
        let half_battlefield_width_in_pixels: f32 =
            BATTLEFIELD_WIDTH_IN_TILES as f32 * self.tile_size / 2.0;
        let half_battlefield_height_in_pixels: f32 =
            BATTLEFIELD_HEIGHT_IN_TILES as f32 * self.tile_size / 2.0;
        let width_center_offset: f32 = half_battlefield_width_in_pixels - half_tile_size;
        let height_center_offset: f32 = half_battlefield_height_in_pixels - half_tile_size;

        return Vec3::new(x - width_center_offset, y - height_center_offset, z);
    }
```

We are just returning the same coordinates as before. Notice that we can now use `self.tile_size`, as it is part of the struct. We need to use `let` and lowercase variable names to keep `Clippy` happy. We could have still used constants and added them to the `Battlefield` implementation. You can do that if you prefer to.

Don’t forget to add `&self` to tell the compiler that this function belongs to `Battlefield` instances and doesn’t mutate the struct (in other words, it doesn’t use `mut&`).

We can now _finally_ replace the tiles' coordinates in `create_battlefield_system`.

Rust

```
                transform: Transform {
                    translation: battlefield.to_battlefield_coordinates(
                        x as f32 * battlefield.tile_size,
                        y as f32 * battlefield.tile_size,
                        0.0,
                    ),
                    ..default()
                },
```

And we can do the same with the archer’s coordinates. Remember to add the `battlefield` resource as a parameter of `create_units_system`.

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    ...

    commands.spawn(SpriteSheetBundle {
        texture_atlas: archer_atlas_handle,
        sprite: TextureAtlasSprite::new(0),
        transform: Transform {
            translation: battlefield.to_battlefield_coordinates(
                0.0 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                0.0 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                1.0,
            ),
            ..default()
        },
        ..default()
    });
}
```

Here is the [diff](https://github.com/srodrigo/strategy-game-rs/commit/a05fd8a975db37b1788f746ba78d249a64e46366) for this last bit of our refactoring.

**NOTE:** You might have noticed that I created a struct for the battlefield but didn’t create one for the units. We will change this in later articles, when we add _components_ to define different kinds of data for the entities in the ECS. For now, we can live with the `SPRITE_SIZE` constant.

### Creating the Tilemap abstraction

Remember `tilemap: [[Tile; BATTLEFIELD_WIDTH_IN_TILES]; BATTLEFIELD_HEIGHT_IN_TILES]`? I should have refactored this long ago, but I wanted to leave it in a separate section for discussion. These two constants are hanging around, and the multidimensional array looks a bit out of place.

There are different ways to model the tilemap.

One option would be to create a _type_ like this:

Rust

```
type Tilemap = [[Tile; BATTLEFIELD_WIDTH_IN_TILES]; BATTLEFIELD_HEIGHT_IN_TILES];
```

Depending on who you ask, this is a good improvement or unnecessary indirection. Still, it doesn’t solve the main issue: we are leaking the tilemap dimensions.

Instead of stopping at abstracting the array type, I’m going to create a new struct that will contain the dimensions and the data of the tilemap. I think this is a better way to ensure we can encapsulate both concepts. We are going to need the following:

- A **_type_** to define the **dimensions**.
- A **_struct_** to encapsulate the **tilemap data**. It will contain the data array and two fields to easily access the number of columns and rows without keeping constants around.

Rust

```
type TilemapDimensions = [[Tile; 13]; 6];

struct Tilemap {
    data: TilemapDimensions,
    num_columns: usize,
    num_rows: usize,
}

impl Tilemap {
    pub fn new(data: TilemapDimensions) -> Self {
        let num_columns = data.get(0).unwrap().len();
        let num_rows = data.len();

        Self {
            data,
            num_columns,
            num_rows,
        }
    }
}
```

We have inlined `BATTLEFIELD_WIDTH_IN_TILES` and `BATTLEFIELD_HEIGHT_IN_TILES` (feel free to keep them if you prefer, but they define a standard grid, so I think they aren’t strictly necessary), and we retrieve the dimensions from the data itself and store it in `num_columns` and `num_rows`.

We could instead create two functions in the implementation and read the `data` array every time we want to retrieve the dimensions. Since the data is immutable, this is not necessary. We can _memoize_ the dimensions during instance creation. Either way is fine.

We also have to change the Battlefield struct and implementation to reflect the new tilemap struct.

Rust

```
#[derive(Resource)]

struct Battlefield {
    tile_size: f32,
    tilemap: Tilemap,
}

impl Battlefield {
    pub fn default() -> Self {
        Self {
            tile_size: 16.0,
            tilemap: Tilemap::new([
                [
                    ...
                ],
                ...
            ]),
        }
    }

    pub fn to_battlefield_coordinates(&self, x: f32, y: f32, z: f32) -> Vec3 {
        let half_tile_size: f32 = self.tile_size / 2.0;
        let half_battlefield_width_in_pixels: f32 =
            self.tilemap.num_columns as f32 * self.tile_size / 2.0;
        let half_battlefield_height_in_pixels: f32 =
            self.tilemap.num_rows as f32 * self.tile_size / 2.0;
        let width_center_offset: f32 = half_battlefield_width_in_pixels - half_tile_size;
        let height_center_offset: f32 = half_battlefield_height_in_pixels - half_tile_size;

        return Vec3::new(x - width_center_offset, y - height_center_offset, z);
    }
}
```

Finally, we can retrieve the tilemap data from the battlefield system.

Rust

```
    for (y, row) in battlefield.tilemap.data.iter().enumerate() {
        ...
    }
```

And these are all the refactorings for now. You can find the changes above [here](https://github.com/srodrigo/strategy-game-rs/commit/3af76d31923a256b56f708bd1cf3cf5133de3bff), and the [complete diff](https://github.com/srodrigo/strategy-game-rs/compare/1014d04c771e9f6d78a4400ec14e14cbfa08f246...3af76d31923a256b56f708bd1cf3cf5133de3bff) for this article here.

There are probably more things that we could improve, but I’m happy enough so far 🙂 I don’t want to divert the attention too much, as we still have to add more units to our battlefield.

## Conclusion

We have built the foundations for adding units in our game. We have also created the `Battlefield` resource to help structure our game data. And we created a little abstraction for the tilemap. I’m confident that this is enough to carry on our exploration of how to make a strategy game.

In the [next article](/game-development-in-rust-making-a-strategy-game-3/), we will build on top and add a couple of different units for each player. We will create a couple of reusable functions, and have fun with references. Stay tuned!

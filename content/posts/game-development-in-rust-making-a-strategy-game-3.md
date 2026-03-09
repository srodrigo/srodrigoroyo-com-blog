---
_cmplz_remote_scanned_post: "1"
_cmplz_scanned_post: "1"
_edit_last: "1"
_thumbnail_id: "964"
author: wpx_srodrigoroyo_admin
categories:
  - programming
cmplz_hide_cookiebanner: ""
cover:
  alt: Game Development In Rust Part 3
  image: /wp-content/uploads/2023/05/strategy-game-in-rust-3.jpeg
date: "2023-05-16T09:14:46+00:00"
guid: https://srodrigoroyo.com/?p=950
parent_post_id: null
post_id: "950"
rank_math_analytic_object_id: "22"
rank_math_focus_keyword: game development in rust,strategy game
rank_math_internal_links_processed: "1"
rank_math_og_content_image:
  check: 32aa648f3ce5f83b9d3e260924cbc83c
  images:
    - 952
rank_math_primary_category: "6"
rank_math_seo_score: "87"
title: 'Game Development In Rust: Making A Strategy Game (Part 3 - Adding Different Unit Types)'
url: /game-development-in-rust-making-a-strategy-game-3/

---
In [Part 2](/game-development-in-rust-making-a-strategy-game-2/), we added our first unit and repurposed our codebase enough to keep adding more features.

This article is a direct continuation of the previous one. We will **add a couple of different units for each player**, positioning them on opposite sides of the battlefield.

## Game Development In Rust: Making A Strategy Game (Part 3 - Adding Different Unit Types)

Adding multiple units follows the same principle as adding a single one. We will build on top of our shiny `create_units_system` to delight our eyes with something that finally looks like a strategy game.

### Adding more units

The time to fill our battlefield with all sorts of cool units has arrived. I will be adding the ones I like and make sure there is some variety, but feel free to experiment and come up with your own dream teams.

Since this will be a 2 player game, we need two different teams. I’m going to pick **blue** units for player 1, and **red** ones for player 2.

We can start by adding a second type of unit. I like **wizards**, so I’ll go for the one in `Sprite Sheets/Wizard/Wizard_Blue3.png`. The code to load it is very similar to the code for the archer.

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    ...

    let wizard_blue_dark_handle = asset_server.load("Sprite Sheets/Wizard/Wizard_Blue3.png");
    let wizard_atlas = TextureAtlas::from_grid(
        wizard_blue_dark_handle,
        Vec2::new(SPRITE_SIZE, SPRITE_SIZE),
        NUM_COLUMNS,
        NUM_ROWS,
        None,
        None,
    );
    let wizard_atlas_handle = texture_atlases.add(wizard_atlas);

    commands.spawn(SpriteSheetBundle {
        texture_atlas: wizard_atlas_handle,
        sprite: TextureAtlasSprite::new(0),
        transform: Transform {
            translation: battlefield.to_battlefield_coordinates(
                2.0 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                1.0 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                1.0,
            ),
            ..default()
        },
        ..default()
    });
}
```

If we compile and run, we should see the new unit.

{{< figure src="/wp-content/uploads/2023/05/image-3.png" alt="Game Development In Rust - Wizard" caption="Game Development In Rust - Wizard" >}}

Game Development In Rust - Wizard

### Units housekeeping

I am delighted that now we can render multiple units. The problem is that **there is a lot of repetition**, and I’m not looking forward to having an unnecessarily lengthy `create_units_system` function. I’m not a fan of premature abstraction, and I typically like at least 3-4 occurrences of the duplicated code before I tackle it, but I think we are safe if we go ahead as the issue is clear at this point.

There are various degrees of complexity that we could go for to load a unit:

- We could have a file with the sprite sheet file path, the position, and the sprite index.
- We could just have a function that takes these parameters.
- A few shades in between that I won’t discuss here.

For the sake of simplicity and to avoid loading more files, I’ll go with the second option and create a **function to add a unit** given the sprite sheet file path, the position, and the sprite index. All the sprite sheets look the same, so we can reuse the same offsets and paddings. The different sprites for the animations and directions are also standard across all units (the artist did a great job in this regard). The only actual difference between loading an archer and loading a wizard is the name of the sprite sheet.

In fact, I would prefer to have two functions instead of one:

- One function to load the sprite sheet
- Another function to spawn the unit

The reason for this is that we need the following:

- Battlefield resource
- Commands
- Asset server
- Texture atlases
- The (column, row) where the unit will be placed on the battlefield
- Probably a few more things I'm overlooking

That’s a lot of parameters for a single function. So, for now, I’ll split it in two even if there is still a bit of repetition.

Let’s create the first function, called `load_unit`, with the following signature:

Rust

```
fn load_unit(
    sprite_sheet: &str,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) -> Handle<TextureAtlas> {
}
```

We can move all the constants inside this function together with the code to get the atlas handle for the archer and rename variables to have generic names.

Rust

```
fn load_unit(
    sprite_sheet: &str,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) -> Handle<TextureAtlas> {
    const SPRITE_SIZE: f32 = 32.0;
    const NUM_COLUMNS: usize = 4;
    const NUM_ROWS: usize = 4;

    let sprite_handle = asset_server.load(sprite_sheet);
    let texture_atlas = TextureAtlas::from_grid(
        sprite_handle,
        Vec2::new(SPRITE_SIZE, SPRITE_SIZE),
        NUM_COLUMNS,
        NUM_ROWS,
        None,
        None,
    );

    return texture_atlases.add(texture_atlas);
}
```

For now, we need to leave `SPRITE_SIZE`, as it is used by `commands.spawn`. We will tackle this shortly.

We can call our new function for the archer and the wizard.

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    const SPRITE_SIZE: f32 = 32.0;

    let archer_blue_light_atlas_handle = load_unit(
        "Sprite Sheets/Archer/Archer_Blue1.png",
        asset_server,
        texture_atlases,
    );

    ...

    let wizard_blue_dark_atlas_handle = load_unit(
        "Sprite Sheets/Wizard/Wizard_Blue3.png",
        asset_server,
        texture_atlases,
    );

    ...
}
```

If we try to compile this code, we will get an error when we try to load the wizard.

PowerShell

```
error[E0382]: use of moved value: asset_server
   --> src/main.rs:331:9
    |
304 |     asset_server: Res<AssetServer>,
    |     ------------ move occurs because asset_server has type `bevy::prelude::Res<'_, bevy::prelude::AssetServer>`, which does not implement the `Copy` trait
...
311 |         asset_server,
    |         ------------ value moved here
...
331 |         asset_server,
    |         ^^^^^^^^^^^^ value used here after move
    |
note: consider changing this parameter type in function load_unit to borrow instead if owning the value isn't necessary

   --> src/main.rs:279:19
    |
277 | fn load_unit(
    |    --------- in this function
278 |     sprite_sheet: &str,
279 |     asset_server: Res<AssetServer>,
    |                   ^^^^^^^^^^^^^^^^ this parameter takes ownership of the value error[E0382]: use of moved value: `texture_atlases`

   --> src/main.rs:332:9
    |
305 |     mut texture_atlases: ResMut<Assets<TextureAtlas>>,
    |     ------------------- move occurs because `texture_atlases` has type `bevy::prelude::ResMut<'_, bevy::prelude::Assets<bevy::prelude::TextureAtlas>>`, which does not implement the `Copy` trait
...
312 |         texture_atlases,
    |         --------------- value moved here
...
332 |         texture_atlases,
    |         ^^^^^^^^^^^^^^^ value used here after move
    |
note: consider changing this parameter type in function `load_unit` to borrow instead if owning the value isn't necessary

   --> src/main.rs:280:26
    |
277 | fn load_unit(
    |    --------- in this function
...
280 |     mut texture_atlases: ResMut<Assets<TextureAtlas>>,
    |                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ this parameter takes ownership of the value
```

Both `asset_server` and `texture_atlases` are _moved_ when we load the archer, so they can’t be used when we load the wizard.

One way of fixing this issue is to declare these parameters as **references**:

Rust

```
fn load_unit(
    sprite_sheet: &str,
    asset_server: &Res<AssetServer>,
    mut texture_atlases: &ResMut<Assets<TextureAtlas>>,
) -> Handle<TextureAtlas> {
   ...
}
```

We also need to pass the variables as references when we call the function.

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    const SPRITE_SIZE: f32 = 32.0;

    let archer_blue_light_atlas_handle = load_unit(
        "Sprite Sheets/Archer/Archer_Blue1.png",
        &asset_server,
        &texture_atlases,
    );

    ...

    let wizard_blue_dark_atlas_handle = load_unit(
        "Sprite Sheets/Wizard/Wizard_Blue3.png",
        &asset_server,
        &texture_atlases,
    );

    ...
}
```

But this still doesn’t work, we get an error when returning the atlas handle.

PowerShell

```
error[E0596]: cannot borrow *texture_atlases as mutable, as it is behind a & reference

   --> src/main.rs:298:12
    |
280 |     texture_atlases: &ResMut<Assets<TextureAtlas>>,
    |                      ----------------------------- help: consider changing this to be a mutable reference: `&mut bevy::prelude::ResMut<'_, bevy::prelude::Assets<bevy::prelude::TextureAtlas>>`
...
298 |     return texture_atlases.add(texture_atlas);
    |            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ `texture_atlases` is a `&` reference, so the data it refers to cannot be borrowed as mutable
```

Well, no one said it’d be easy 🙂

The Rust compiler is telling us that it can't borrow the reference to `texture_atlas` as **mutable**. We can fix this problem by changing the parameter from `&` to `&mut`.

Rust

```
fn load_unit(
    sprite_sheet: &str,
    asset_server: &Res<AssetServer>,
    mut texture_atlases: &mut ResMut<Assets<TextureAtlas>>,
) -> Handle<TextureAtlas> {
   ...
}
```

We also need to change the calls to this function, to pass mutable references.

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    const SPRITE_SIZE: f32 = 32.0;

    let archer_blue_light_atlas_handle = load_unit(
        "Sprite Sheets/Archer/Archer_Blue1.png",
        &asset_server,
        &mut texture_atlases,
    );

    ...

    let wizard_blue_dark_atlas_handle = load_unit(
        "Sprite Sheets/Wizard/Wizard_Blue3.png",
        &asset_server,
        &mut texture_atlases,
    );

    ...
}
```

Our code finally compiles and runs without any issues.

The above was a bit of hard work, but worth it. When we have 8-12 units, we will be glad that we are not copying and pasting the same code over and over. This is the [diff](https://github.com/srodrigo/strategy-game-rs/commit/ee0efeeb439f640b44f90adde2ada14d51362655) for the refactoring so far.

The next step is to do something similar for spawning a unit. We are going to need a function with the following signature:

Rust

```
fn spawn_unit(
    atlas_handle: Handle<TextureAtlas>,
    column_in_battlefield: usize,
    row_in_battlefield: usize,
    battlefield: Res<Battlefield>,
    mut commands: Commands,
) {
    ...
}
```

We are passing the atlas handle, the position on the battlefield, and the battlefield itself so it can transform the coordinates. We also need the `commands` instance to span the unit sprite.

The function body looks like this:

Rust

```
fn spawn_unit(
    atlas_handle: Handle<TextureAtlas>,
    column_in_battlefield: usize,
    row_in_battlefield: usize,
    battlefield: Res<Battlefield>,
    mut commands: Commands,
) {
    const SPRITE_SIZE: f32 = 32.0;

    commands.spawn(SpriteSheetBundle {
        texture_atlas: atlas_handle,
        sprite: TextureAtlasSprite::new(0),
        transform: Transform {
            translation: battlefield.to_battlefield_coordinates(
                column_in_battlefield as f32 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                row_in_battlefield as f32 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                1.0,
            ),
            ..default()
        },
        ..default()
    });
}
```

And we can replace the code in the system:

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    let archer_blue_light_atlas_handle = load_unit(
        "Sprite Sheets/Archer/Archer_Blue1.png",
        &asset_server,
        &mut texture_atlases,
    );
    spawn_unit(archer_blue_light_atlas_handle, 0, 0, battlefield, commands);

    let wizard_blue_dark_atlas_handle = load_unit(
        "Sprite Sheets/Wizard/Wizard_Blue3.png",
        &asset_server,
        &mut texture_atlases,
    );
    spawn_unit(wizard_blue_dark_atlas_handle, 2, 1, battlefield, commands);
}
```

We have a similar problem with `battlefield` and `commands` being _moved_ during the first function call, so we need to declare them as references.

Rust

```
fn spawn_unit(
    atlas_handle: Handle<TextureAtlas>,
    column_in_battlefield: usize,
    row_in_battlefield: usize,
    battlefield: &Res<Battlefield>,
    commands: &mut Commands,
) {
    ...
}

fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    let archer_blue_light_atlas_handle = load_unit(
        "Sprite Sheets/Archer/Archer_Blue1.png",
        &asset_server,
        &mut texture_atlases,
    );

    spawn_unit(
        archer_blue_light_atlas_handle,
        0,
        0,
        &battlefield,
        &mut commands,
    );

    let wizard_blue_dark_atlas_handle = load_unit(
        "Sprite Sheets/Wizard/Wizard_Blue3.png",
        &asset_server,
        &mut texture_atlases,
    );

    spawn_unit(
        wizard_blue_dark_atlas_handle,
        2,
        1,
        &battlefield,
        &mut commands,
    );
}
```

Our code should now compile and run correctly.

One more, optional, thing that we can do to make the code in the system more concise is to **inline the atlas handles**:

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    spawn_unit(
        load_unit(
            "Sprite Sheets/Archer/Archer_Blue1.png",
            &asset_server,
            &mut texture_atlases,
        ),
        0,
        20,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/Wizard/Wizard_Blue3.png",
            &asset_server,
            &mut texture_atlases,
        ),
        2,
        1,
        &battlefield,
        &mut commands,
    );
}
```

I find this way to be more **declarative**, decreasing the chances of using the wrong variable. Rust would catch some of these mistakes, but I still like reducing the human error variable as much as I can. But this is a matter of taste and style for the most part.

Note that we are duplicating `SPRITE_SIZE`. There are already quite a few parameters being passed into `spawn_unit`, so I would rather duplicate it than clutter the function signature even more. We could also move it as a global constant, especially since we might move `create_units_system` and its related functions into its own module, making this variable global to only that file.

I don’t like global constants and I’d rather duplicate it as long as it’s only in 2 functions (I would probably do this if all the code related to units was in a separate module instead of in the main file). Or we could assume that all the sprites will have the same size as one tile and move this knowledge into the battlefield. YMMV.

We could have chosen not to pass `commands` into `spawn_unit`, and return the `SpriteSheeBundle` instead, removing one parameter from the list. But I wanted the function to actually spawn the unit.

We could also have passed the number of columns and rows into `spawn_unit` as a tuple, saving one parameter. I’m not a fan of tuples as they are less readable to me than separate variables or structs. We could have created a struct as well, but I thought that would be overkill.

As said, there are many ways of designing these implementation details, and you might have different preferences. I might look at this code in 2 weeks and want to make some adjustments. For now, I’m happy enough. But feel free to implement your preferred variations if needed.

Here is the [diff](https://github.com/srodrigo/strategy-game-rs/commit/8a4d5aebe1da2774f0887c10c9dcf1bf7bfc3dbf) for this section.

### Building the teams

After all this hard work, it’s time to enjoy the rewards and populate our battlefield. Go wild and **add all sorts of units**. Our battlefield is getting a bit small to host 32x32 sprites. We will fix this later. These are the units that I added for now:

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    spawn_unit(
        load_unit(
            "Sprite Sheets/Archer/Archer_Blue1.png",
            &asset_server,
            &mut texture_atlases,
        ),
        0,
        0,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/Wizard/Wizard_Blue3.png",
            &asset_server,
            &mut texture_atlases,
        ),
        1,
        1,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/LanceKnight/LanceKnight_Blue.png",
            &asset_server,
            &mut texture_atlases,
        ),
        2,
        2,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/SwordFighter/SwordFighter_LongHair_Blue1.png",
            &asset_server,
            &mut texture_atlases,
        ),
        2,
        0,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/Archer/Archer_Red1.png",
            &asset_server,
            &mut texture_atlases,
        ),
        5,
        1,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/Wizard/Wizard_Red3.png",
            &asset_server,
            &mut texture_atlases,
        ),
        5,
        0,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/LanceKnight/LanceKnight_Red.png",
            &asset_server,
            &mut texture_atlases,
        ),
        4,
        2,
        &battlefield,
        &mut commands,
    );

    spawn_unit(
        load_unit(
            "Sprite Sheets/SwordFighter/SwordFighter_LongHair_Red1.png",
            &asset_server,
            &mut texture_atlases,
        ),
        3,
        1,
        &battlefield,
        &mut commands,
    );
}
```

If we compile and run, we’ll see that the red sprites are not facing the blue ones, which is unfortunate.

{{< figure src="/wp-content/uploads/2023/05/image-6.png" alt="Strategy Game: Red team, but not quite right" caption="Strategy Game: Red team, but not quite right" >}}

Strategy Game: Red team, but not quite right

We need to **flip the red sprites**. This is done by specifying the `rotation` attribute in the `transform` inside the `SpriteBundleSheet`:

- We need to rotate the sprites `180 degrees`, which is the same as `PI radians`.
- We need a flag to flip only the red sprites.

In `spawn_unit`, we can add a `flip` boolean and set the rotation to either `default` or `PI radians` for the `y` axis.

Rust

```
fn spawn_unit(
    atlas_handle: Handle<TextureAtlas>,
    column_in_battlefield: usize,
    row_in_battlefield: usize,
    flip: bool,
    battlefield: &Res<Battlefield>,
    commands: &mut Commands,
) {
    const SPRITE_SIZE: f32 = 32.0;

    commands.spawn(SpriteSheetBundle {
        texture_atlas: atlas_handle,
        sprite: TextureAtlasSprite::new(0),
        transform: Transform {
            translation: battlefield.to_battlefield_coordinates(
                column_in_battlefield as f32 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                row_in_battlefield as f32 * SPRITE_SIZE + SPRITE_SIZE / 4.0,
                1.0,
            ),
            rotation: if flip {
                Quat::from_rotation_y(std::f32::consts::PI)
            } else {
                Quat::default()
            },
            ..default()
        },
        ..default()
    });
}
```

We need to pass either `false` or `true`, depending on the sprite.

Rust

```
fn create_units_system(
    battlefield: Res<Battlefield>,
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    spawn_unit(
        load_unit(
            "Sprite Sheets/Archer/Archer_Blue1.png",
            &asset_server,
            &mut texture_atlases,
        ),
        0,
        0,
        false,
        &battlefield,
        &mut commands,
    );

    ...

    spawn_unit(
        load_unit(
            "Sprite Sheets/Archer/Archer_Red1.png",
            &asset_server,
            &mut texture_atlases,
        ),
        5,
        1,
        true,
        &battlefield,
        &mut commands,
    );

    ...
}
```

And so on.

If we run our game, the issue should be fixed.

{{< figure src="/wp-content/uploads/2023/05/image-2.png" alt="Strategy Game: Teams ready for the battle" caption="Strategy Game: Teams ready for the battle" >}}

Strategy Game: Teams ready for the battle

You can find the last diff [here](https://github.com/srodrigo/strategy-game-rs/commit/2ed5a61d437566345948ab56b30bce45c82ade6a) and the code for [this chapter](https://github.com/srodrigo/strategy-game-rs/compare/c1731600c31bc879bdd85226743ef47941eabc36...2ed5a61d437566345948ab56b30bce45c82ade6a).

## Conclusion

Our game is starting to look like an actual strategy game. Except for the lack of actual interaction from the player.

We will start solving this little “inconvenience” in a later article, where we will add a way to move units. I hope you are looking forward to it as much as I am!

---
title: Using nannou in Rust
publishDate: 06 Dec 2023
description: creative coding in Rust with the nannou library
---


# Simple app in nannou

The library [nannou](https://nannou.cc) is an "open-source creative-coding framework for Rust". Among the simplest graphics app one always find some sort of "projectile app" or "rocket app". Let's see how such an app looks like in **nannou**.

A nannou app features three components:

* the Model:
    The model function is returns a Model struct that contains the initial state of the app.

* the View (aka the 'rendering'):
  The view function is used to draw from the current state of the application

* the Update:
   Think of the update as a function that is repeatedly called within an infinite game loop.
   The **update** together with all user interaction takes the place of the **controller** in the MVC (Model-View-Controller).


## The main objects

The projectiles will be thrown from a little cannon (described by the angle at which it is pointing and its power).

```rust
struct Model {
    cannon: Cannon,
    projectiles: Vec<Projectile>,
}

struct Cannon {
    angle: f32,
    power: f32,
}

struct Projectile {
    position: Point2,
    velocity: Vec2,
}
```

# Model-View-Update
## The model

```rust
fn model(app: &App) -> Model {
    app.new_window()
       .size(800, 600)
       .view(view)
       .build()
       .unwrap();

    Model {
        cannon: Cannon {
            angle: PI / 4.0,
            power: 10.0,
        },
        projectiles: Vec::new(),
    }
}
```

## The view
```rust
fn view(app: &App, model: &Model, frame: Frame) {
    let draw = app.draw();
    draw.background().color(WHITE);

    draw_cannon(&draw, &model.cannon);
    draw_projectiles(&draw, &model.projectiles);

    draw.to_frame(app, &frame).unwrap();
}
```

## The update

```rust
fn update(app: &App, model: &mut Model, _update: Update) {
    if app.elapsed_frames() % 60 == 0 {
        fire_projectile(model);
    }

    update_projectiles(model);
}
```

# How it looks like




<html>
<body>
<p>
      <div style="text-align: center;">
    <img src="../assets/images/nannou_projectile.gif" alt="nannou projectile" width="50%" >
      </div>
</p>
</body>
</html>







# The entire app code

```rust

use nannou::prelude::*;

struct Model {
    cannon: Cannon,
    projectiles: Vec<Projectile>,
}

struct Cannon {
    angle: f32,
    power: f32,
}

struct Projectile {
    position: Point2,
    velocity: Vec2,
}

fn main() {
    nannou::app(model)
        .update(update)
        .run();
}

fn model(app: &App) -> Model {
    app.new_window()
       .size(800, 600)
       .view(view)
       .build()
       .unwrap();

    Model {
        cannon: Cannon {
            angle: PI / 4.0,
            power: 10.0,
        },
        projectiles: Vec::new(),
    }
}

fn update(app: &App, model: &mut Model, _update: Update) {
    if app.elapsed_frames() % 60 == 0 {
        fire_projectile(model);
    }

    update_projectiles(model);
}

fn fire_projectile(model: &mut Model) {
    let velocity = Vec2::new(
        model.cannon.power * model.cannon.angle.cos(),
        model.cannon.power * model.cannon.angle.sin(),
    );
    let projectile = Projectile {
        position: pt2(0.0, 0.0),
        velocity,
    };
    model.projectiles.push(projectile);
}

fn update_projectiles(model: &mut Model) {
    for projectile in &mut model.projectiles {
        projectile.velocity.y -= 0.1; // gravity
        projectile.position += projectile.velocity;
    }
}

fn view(app: &App, model: &Model, frame: Frame) {
    let draw = app.draw();
    draw.background().color(WHITE);

    draw_cannon(&draw, &model.cannon);
    draw_projectiles(&draw, &model.projectiles);

    draw.to_frame(app, &frame).unwrap();
}

fn draw_cannon(draw: &Draw, cannon: &Cannon) {
    draw.line()
        .start(pt2(0.0, 0.0))
        .end(pt2(
            50.0 * cannon.angle.cos(),
            50.0 * cannon.angle.sin(),
        ))
        .color(BLACK);
}

fn draw_projectiles(draw: &Draw, projectiles: &[Projectile]) {
    for projectile in projectiles {
        draw.ellipse()
            .xy(projectile.position)
            .radius(5.0)
            .color(RED);
    }
}

```







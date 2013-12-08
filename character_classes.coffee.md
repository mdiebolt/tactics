Character Classes
=================

Exploring various character classes here.

    Names = require "./names"

    module.exports =
      ShrubMage:
        spriteName: "kobold"
        abilities: [
          "Move"
          "Entanglement"
          "ShrubSight"
        ]

      Grunt:
        spriteName: "goblin"
        abilities: [
          "Move"
          "Melee"
          "Regeneration"
        ]

      Giant:
        spriteName: "hill_giant"
        movement: 3
        health: 5
        healthMax: 5
        name: Names.monster.rand()
        abilities: [
          "Move"
          "Stomp"
        ]
        sight: 5

      Knight:
        health: 4
        healthMax: 4
        spriteName: "human"

      Scout:
        actions: 3
        health: 2
        healthMax: 2
        spriteName: "thief"
        movement: 6
        sight: 9
        passives: [
          "EagleEye"
        ]

      Wizard:
        health: 2
        healthMax: 2
        spriteName: "wizard"
        abilities: [
          "Blink"
          "Fireball"
          "Farsight"
        ]

      Archer:
        spriteName: "elf_archer"
        abilities: [
          "Move"
          "Ranged"
        ]
Character
=========

Those little guys that run around.

    Resource = require "./resource"
    Action = require "./action"
    Ability = require "./ability"
    Names = require "./names"

    module.exports = (I={}, self=Core(I)) ->
      I.position = Point(I.position)
      I.sprite = Resource.sprite(I.sprite)

      Object.defaults I,
        actions: 2
        health: 3
        healthMax: 3
        movement: 4
        name: Names.male.rand()
        sight: 7
        abilities: [
          "Melee"
        ]

      self.attrAccessor(
        "actions"
        "health"
        "healthMax"
        "movement"
        "name"
        "position"
        "sprite"
        "sight"
      )

      Object.extend self,
        alive: ->
          I.health > 0

        damage: (amount) ->
          I.health -= amount

        heal: (amount) ->
          I.health += amount

        stateBasedActions: ->
          if I.health > I.healthMax
            I.health = I.healthMax

          if I.health <= 0
            I.actions = 0

        targettingAbility: Observable()

        updatePosition: (newPosition) ->
          self.position newPosition

Ready is called at the beginning of each turn. It resets the actions and processes
any status effects.

        ready: ->
          I.actions = 2

      abilities = I.abilities.concat("Move", "Wait", "Cancel").map (name) ->
        Ability.Abilities[name]

      actions = abilities.map (ability) ->
        action = Action
          name: ability.name()
          icon: ability.iconName()
          perform: ->
            self.targettingAbility(ability)

        action.active = Observable ->
          ability is self.targettingAbility()

        return action

      self.uiActions = ->
        actions

      self.resetTargetting = ->
        self.targettingAbility null

      return self
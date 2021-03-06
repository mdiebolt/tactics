Character UI
============

Methods for drawing components of the character ui.

    Action = require "./action"
    Ability = require "./ability"
    Resource = require "./resource"
    {Size} = require "./lib/util"

    tileSize = Size(32, 32)

    heartSprite = Resource.sprite("heart")
    heartEmptySprite = Resource.sprite("heart_empty")
    actionBarSprite = Resource.sprite("action_bar")

    drawHealth = (canvas, health, max) ->
      max.times (i) ->
        x = i * 10
        if i < health
          heartSprite.draw(canvas, x, 0)
        else
          heartEmptySprite.draw(canvas, x, 0)

    drawActions = (canvas, n) ->
      n.times (i) ->
        actionBarSprite.draw canvas, i * 16 + 1, 32

    module.exports = CharacterUI =

Draw tactical and include actions remaining.

      activeTactical: (canvas, character) ->
        CharacterUI.tactical(canvas, character)

        position = character.position()
        canvasPosition = position.scale(tileSize)

        canvas.withTransform Matrix.translation(canvasPosition.x, canvasPosition.y), (canvas) ->
          drawActions(canvas, character.actions())

Draw the tactical overlay, status, health, max health.

      tactical: (canvas, character) ->
        position = character.position()
        canvasPosition = position.scale(tileSize)

        canvas.withTransform Matrix.translation(canvasPosition.x, canvasPosition.y), (canvas) ->
          drawHealth(canvas, character.health(), character.healthMax())

      actions: (character) ->
        actions = character.abilities().map (abilityName) ->
          ability = Ability.Abilities[abilityName]

          action = Action
            cooldown: ability.cooldown()
            cost: ability.actionCost()
            disabledFor: character.cooldowns()[abilityName] || 0
            name: ability.name()
            description: ability.description()
            icon: ability.iconName()
            perform: ->
              character.targettingAbility(ability)

          if type = ability.costType
            action.costType = type

          action.active = ability is character.targettingAbility()

          action.disabled = !ability.canPay(character)

          return action

        if character.targettingAbility()
          actions.push Action
            name: "Cancel"
            description: "Cancel using the current action."
            icon: "cancel"
            perform: ->
              character.targettingAbility(null)

        actions

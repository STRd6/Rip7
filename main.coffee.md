Rip7
====

    require "./setup"
    
    Deck = require "./deck"

    cpuDeck = Deck()
    deck = Deck()
    hand = Observable deck.draw(7)

    validateIndices = (indices) ->
      indices.select (n) ->
        n?
      .length is 5

    ui =
      cards: hand
      lanes: Observable [0, 0, 0, 0, 0]
      activeIndex: Observable null
      chosenIndices: Observable []
      chooseLane: (laneIndex) ->
        if (cardIndex = ui.activeIndex())?
          
          existing = ui.chosenIndices().copy()
          
          if (index = existing.indexOf(cardIndex)) >= 0
            existing[index] = null

          existing[laneIndex] = cardIndex
          
          ui.chosenIndices existing

      resolveTurn: ->
        indices = ui.chosenIndices()

        if validateIndices(indices)
          console.log indices
        else
          console.log "invalid"

    $("body").append require("./template")(ui)

Rip7
====

    require "./setup"
    
    Deck = require "./deck"

    deck = Deck()
    hand = Observable deck.draw(7)

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

    $("body").append require("./template")(ui)

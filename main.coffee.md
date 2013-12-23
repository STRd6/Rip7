Rip7
====

    require "./setup"
    
    Deck = require "./deck"

    cpuDeck = Deck()
    deck = Deck()
    lanes = Observable [0, 0, 0, 0, 0]
    hand = Observable []
    aiHand = Observable []
    chosenIndices = Observable []
    handSize = 7
    
    cpuIndices = []

    cardRanks =
      T: 10
      J: 10
      Q: 10
      K: 10
      A: 1

    face = (card) ->
      card is "J" or 
      card is "Q" or
      card is "K"

    startTurn = ->
      aiHand cpuDeck.draw(handSize)
      hand deck.draw(handSize)
      chosenIndices []
      cpuIndices = [0...handSize].shuffle().slice(0, 5)

    computeLaneExchange = () ->
      lanes lanes.map (value, index) ->
        playerCard = chosenIndices.get(index)
        cpuCard = cpuIndices[index]
        if cpuCard is "0" or playerCard is "0"
          value # Joker Rule
        else if playerCard is "A" and face(cpuCard) # Ace Rule
          value + 1
        else if cpuCard is "A" and face(playerCard)
          value - 1
        else
          value + (cardRanks[playerCard] or parseInt(playerCard, 10)) - (cardRanks[cpuCard] or parseInt(cpuCard))

    validateIndices = (indices) ->
      indices.select (n) ->
        n?
      .length is 5

    startTurn()

    ui =
      cards: hand
      lanes: lanes
      activeIndex: Observable null
      chosenIndices: chosenIndices
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
          computeLaneExchange()
          startTurn()
          console.log indices
        else
          console.log "invalid"

    $("body").append require("./template")(ui)

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
    previousChoices = Observable []
    previousAiChoices = Observable []
    messages = Observable []
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

      if hand().length is 0
        # Reshuffle
        deck.shuffle()
        cpuDeck.shuffle()
        aiHand cpuDeck.draw(handSize)
        hand deck.draw(handSize)

      chosenIndices []
      cpuIndices = [0...aiHand().length].shuffle().slice(0, 5)

    checkForWinner = ->
      wonLaneCount = lanes.filter (value) ->
        value >= 10
      .length

      lostLaneCount = lanes.filter (value) ->
        value <= -10
      .length

      if wonLaneCount >= 3
        alert "You Win!"
      else if lostLaneCount >= 3
        alert "You Lose!"
      else
        # No winners

    notify = (message) ->
      messages.push message + "\n"

    unresolvedLaneCount = ->
      lanes.filter (value) ->
        -10 < value < 10
      .length

    computeLaneExchange = () ->
      lanes lanes.map (value, index) ->
        # Resolved lanes don't budge
        return value if value <= -10 or value >= 10

        playerCard = hand.get chosenIndices.get(index)
        cpuCard = aiHand.get cpuIndices[index]

        if (cpuCard is "0") or (playerCard is "0")
          notify "Joker stalls! #{playerCard} - #{cpuCard}"
          
          value # Joker Rule
        else if playerCard is "A" and face(cpuCard) # Ace Rule
          notify "Your 'A' aced opponents #{cpuCard}"

          value + 1
        else if cpuCard is "A" and face(playerCard)
          notify "CPUs 'A' aced your #{playerCard}"
          
          value - 1
        else
          playerValue = (cardRanks[playerCard] or parseInt(playerCard, 10))
          cpuValue = (cardRanks[cpuCard] or parseInt(cpuCard))
          
          delta = playerValue - cpuValue
          notify "Your #{playerCard} vs CPUs #{cpuCard} resulted in a change of #{delta}"
          
          value + delta

    validateIndices = (indices) ->
      indices.select (n) ->
        n?
      .length is unresolvedLaneCount()

    startTurn()

    ui =
      cards: hand
      lanes: lanes
      messages: messages
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
          checkForWinner()
          startTurn()
          console.log indices
        else
          console.log "invalid"

    $("body").append require("./template")(ui)

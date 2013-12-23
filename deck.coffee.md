Deck
====

    module.exports = ->
      cards = [2..9].concat(["A", "K", "Q", "J", "T"]).map (n) -> # Standard 52
        [0...4].map ->
          n.toString()
      .flatten()
      .concat ["0", "0"] # Jokers

      index = 0

      self = 
        draw: (n) ->
          result = cards.slice(index, index + n)

          index += n

          return result

        shuffle: ->
          index = 0
          cards = cards.shuffle()

        cards: ->
          cards

      self.shuffle()

      return self
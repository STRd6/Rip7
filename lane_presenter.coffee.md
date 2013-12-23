Lane Presenter
==============

    module.exports = (value, index, ui) ->
      click: ->
        return unless -10 < value < 10

        ui.chooseLane(index)

      assigned: ->
        ui.cards.get(ui.chosenIndices()[index]) or "None"

      classes: ->
        [
          "won" if value >= 10
          "lost" if value <= -10
        ].compact().join(" ")

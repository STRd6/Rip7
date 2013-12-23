Lane Presenter
==============

    module.exports = (lane, index, ui) ->
      click: ->
        ui.chooseLane(index)

      assigned: ->
        ui.cards.get ui.chosenIndices()[index]

      classes: ->
      
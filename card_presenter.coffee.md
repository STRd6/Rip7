Card Presenter
==============

    module.exports = (card, index, ui) ->
      classes: ->
        "active" if index is ui.activeIndex()
      click: ->
        ui.activeIndex(index)

        console.log card

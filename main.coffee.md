Rip7
====

    require "./setup"

    ui =
      cards: Observable [1, 2, 3, 4, 5, 6, 7]
      lanes: Observable [0, 0, 0, 0, 0]

    $("body").append require("./template")(ui)

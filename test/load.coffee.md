    describe 'Modules', ->
      list = [
          'middleware/local_redis.coffee.md'
        ]

      unit = (m) ->
        it "should load #{m}", ->
          M = require "../#{m}"
      for m in list
        unit m

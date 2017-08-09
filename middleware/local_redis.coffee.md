    @name = 'needy-toothpaste:local_redis'

Use notepack.io (the same toolset as socket.io-redis)

    Notepack = require 'notepack.io'

But try out ioredis (since it supports Promises natively)

    Redis = require 'ioredis'

    debug = (require 'tangible') @name

    @server_post = ->

      return unless @cfg.local_redis?

      client = new Redis @cfg.local_redis

      publish = (channel,data) ->
        client
        .publish channel, Notepack.encode data
        .catch (error) ->
          debug "publish: #{error.stack}"

      @cfg.statistics.on 'report', (report) ->
        publish 'huge-play:report', report

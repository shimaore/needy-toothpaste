    @name = 'needy-toothpaste:local_redis'

Try out ioredis (since it supports Promises natively)

    Redis = require 'ioredis'

    debug = (require 'tangible') @name

    @server_post = ->

      return unless @cfg.local_redis?

      {host} = @cfg

      client = new Redis @cfg.local_redis
      client.on 'connect', -> debug 'connect'
      client.on 'ready', -> debug 'ready'
      client.on 'error', (error) -> debug "error #{error.stack ? error.message}"
      client.on 'close', -> debug 'close'
      client.on 'reconnecting', -> debug 'reconnectin'
      client.on 'end', -> debug 'end'

      encode = switch process.env.NEEDY_TOOTHPASTE

Use notepack.io (the same toolset as socket.io-redis).

        when 'notepack.io'
          Notepack = require 'notepack.io'
          (data) -> Notepack.encode data

Default is to use JSON.

        else
          (data) -> JSON.stringify data

      publish = (channel,data) ->
        client
        .publish channel, encode data
        .catch (error) ->
          debug "publish: #{error.stack}"

      @cfg.statistics.on 'report', (report) ->
        publish 'huge-play:report', report

      @cfg.statistics.on 'queuer', (report) ->
        publish 'huge-play:queuer', report

      @cfg.statistics.on 'add', (data) ->
        publish 'huge-play:add',
          host: host
          key: data.key
          value: data.value.toJSON()

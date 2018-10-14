### sneakers

https://github.com/jondot/sneakers

https://github.com/jondot/sneakers/wiki

```
bundle
gem install sneakers

sneakers work Procesor --require boot.rb
redis-cli monitor

```

```ruby
source 'https://rubygems.org'
gem 'sneakers'
gem 'json'
gem 'redis'

require 'sneakers'
require 'redis'
require 'json'
$redis = Redis.new
class Processor
  include Sneakers::Worker
  from_queue :logs
  def work(msg)
    err = JSON.parse(msg)
    if err["type"] == "error"
      $redis.incr "procesor:#{err["error"]}"
    end
    ack!
  end
end

require 'bunny'
conn = Bunny.new
conn.start
ch = conn.create_channel
ch.default_exchange.publish({ type: 'error', message: 'HALP!', error: 'CODE001' }.to_json, routing_key: 'logs')

# boot.rb
require 'sneakers'
require 'redis'
require 'json'
require 'sneakers/metrics/logging_metrics'
Sneakers.configure(metrics: Sneakers::Metrics::LoggingMetrics.new)


```

```
{
  "type": "error",
  "message": "HALP!",
  "error": "CODE001"
}

```




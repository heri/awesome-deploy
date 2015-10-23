# Awesome-Deploy

Your guide to Ruby apps that can serve 1,000 requests per seconds, and beyond!


## Quick install

```
brew install caskroom/cask/brew-cask
brew cask install virtualbox
brew cask install vagrant
brew install ansible
```

```
cd /path/to/rails/project
git clone http://github.com/heri/awesome-deploy
cd awesome-deploy
vagrant up
```

## TODO

* Ruby style guide highlighting *slow* ruby code. See [here](https://github.com/bbatsov/ruby-style-guide)
* More recipes

## Measure

The first step is recognizing if your application is slow or fast

* [Miniprofiler](https://github.com/MiniProfiler/rack-mini-profiler) is a simple but effective mini-profiler
* NewRelic is a paid solution that will give you a lot of insights, esp. which requests are the slowest and where
* Google analytics can also help. For example, high bounce rates can mean either your site content is uninteresting, or it was too slow to load for them. Investigate!

## Application server

Get a fast server

* [puma](http://puma.io/) is built for speed and concurrency
* [Phusion Passenger](Fast & scalable) is a Fast & scalable server

The server can also be fine-tuned, depending on resources available and objectives.

## Ruby

* JRuby
* Rubinius

## Ruby code

It is very easy to create new objects and iterate through them with Ruby. What's less known are the consequences. Too much memory will be consumed. The Ruby heap will grow and eventually will take all the memory available.

Write code carefully. The best code is the one that does not exist. Here are different steps:

* Rails is a heavy framework requiring a lot of resources and only be used when programmer time is limited and if it has unique features for the use case. Other frameworks are available if you are making an API or a WS app.
* If you have to use Rails, consider [deleting middlewares](https://www.amberbit.com/blog/2014/2/14/putting-ruby-on-rails-on-a-diet/).
* Mutate the original object instead of creating new copies. For example, to remove whitespace, `strip!` will not create a new copy of the object compared to `strip`
* Tests and automated tests will surface poor code in your code. Make sure code coverage is maxed out!
* Instead of creating dedicated classes, modular code organization can give you a decent performance boost, especially for single atomic helper tasks. If you find yourself writing more than 15 lines to process a request, time to move it to a module
* Minimize the use of gems. Rails for example requires at least 40 gems. Consider if the extra overhead is worth it. Extra gems can also bring security issues, if they are from poor contributors.
* If the data is human-readable and correct but not beautiful, send them to the client-side. E.g.: date and time parsing, full names, currencies. Only process data that is private, business-sensitive
* Can you use memoization? There is a chance the same request has been processed before. Save the result in redis!
* Are you using over and over the standard map, each and blocks? Ruby has advanced methods such as `flat_map`, `reverse_each`, `each_key` that will not create new objects and will be an order or magnitude faster

## Database

* In Rails: Reduce the number of queries, especially [N+1 queries](https://github.com/flyerhzm/bullet)
* Adding indexes
* Consider in-memory sqlite vs PostgreSQL in the case of non-concurrent writes
* Scaling reads by tuning SQL, looking at slow query log, NewRelic, and dTrace to find outliers
* Routing background jobs with unique usage characteristics to dedicated replicas
* Optimizing DB schema for high performance applications. Understanding MVCC and it's impact on application concurrency.
* Scaling writes by replacing high-throughput append-only DB tables (log tables) with non-database alternatives (rsyslog + flat files)

## Caching

* Cache layers, one by one
* Put static files in CDNs, if possible

## Asynchronous processing

* Move long processes to a Sidekiq queue. Typically: image processing, video encoding, sending emails, uploading files etc.
* Use a client-side Javascript framework

## Client-Side

* Use React.js or another front-end framework
* Minify javascript and CSS
* Use async, whenever possible
* Display loader gifs and subtle animations to let the user the request is being processed

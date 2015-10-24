# Awesome-Deploy

Your guide to Ruby apps that can serve 10,000 requests per seconds, and beyond!

## Application server benchmarks

Don't believe the hype! The most performing are:

1. sinatra-unicorn. [Docker](https://github.com/heri/awesome-deploy/tree/master/sinatra-unicorn-docker)
2. puma-mri-grape
3. rails-unicorn. [Docker with nginx](https://github.com/heri/awesome-deploy/tree/master/rails-unicorn-docker-container)

Latency for sinatra-unicorn is esp. interesting 1.5 ms	0.2% (max 66.3 ms)

Numbers:

### JSON Serialization

* unicorn-grape	9,305	rqs/seconds
* puma-rbx-grape	117	rqs/seconds
* **puma-mri-grape	11,214	rqs/seconds**
* puma-jruby-grape	200	rqs/seconds
* torqbox-grape	9,694	rqs/seconds
* trinidad-grape	7,235	rqs/seconds
* thin-grape	2,246	rqs/seconds
* jruby-padrino	200	rqs/seconds
* rbx-padrino	116	rqs/seconds
* puma-padrino	5,232	rqs/seconds
* thin-padrino	1,092	rqs/seconds
* torqbox-padrino	6,447	rqs/seconds
* trinidad-padrino	5,329	rqs/seconds
* unicorn-padrino	6,765 rqs/seconds
* **rack	89,466 rqs/seconds**
* rack-rbx	119 rqs/seconds
* racket-ws	2,015 rqs/seconds
* rails-torqbox	2,379 rqs/seconds
* rails-unicorn	6,125 rqs/seconds
* rails-stripped-ruby	6,942 rqs/seconds
* rails-stripped-jruby	2,570 rqs/seconds
* puma-mri-rails	4,027 rqs/seconds
* puma-rbx-rails	116 rqs/seconds
* puma-jruby-rails	200 rqs/seconds
* trinidad-rails	2,377 rqs/seconds
* sinatra-rbx-puma	118 rqs/seconds
* sinatra-trinidad	9,589 rqs/seconds
* sinatra-puma	12,761 rqs/seconds
* sinatra-thin	3,195 rqs/seconds
* sinatra-jruby-puma	200 rqs/seconds
* **sinatra-unicorn	17,982 rqs/seconds**
* **sinatra-torqbox	18,340 rqs/seconds**

### Database query (reads)

* puma-jruby-grape	199 rqs/seconds
* puma-mri-grape	4,140 rqs/seconds
* unicorn-grape	4,203 rqs/seconds
* trinidad-grape	2,977 rqs/seconds
* puma-rbx-grape	115
* torqbox-padrino	3,807	rqs/seconds
* trinidad-padrino	3,462	rqs/seconds
* unicorn-padrino	3,691	rqs/seconds
* rbx-padrino	114 rqs/seconds
* thin-padrino	566 rqs/seconds
* jruby-padrino	200 rqs/seconds
* puma-padrino	4071 rqs/seconds
* rails-stripped-jruby	2,121 rqs/seconds
* **rails-stripped-ruby	4,540 rqs/seconds**
* rails-torqbox	2,428 rqs/seconds
* trinidad-rails	2,236 rqs/seconds
* rails-unicorn	4,264 rqs/seconds
* puma-jruby-rails	200 rqs/seconds
* puma-rbx-rails	114 rqs/seconds
* puma-mri-rails 3510 rqs/seconds
* sinatra-thin	1,284 rqs/seconds
* sinatra-trinidad	2,992 rqs/seconds
* **sinatra-unicorn	7,464 rqs/seconds**
* sinatra-jruby-puma	200 rqs/seconds
* sinatra-rbx-puma	116 rqs/seconds
* sinatra-puma	5,217 rqs/seconds
* sinatra-torqbox	2,818	rqs/seconds

### Database query (write)

* torqbox-grape	2,334 rqs/seconds
* trinidad-grape	2,602 rqs/seconds
* unicorn-grape	2,891	 rqs/seconds
* puma-jruby-grape	398 rqs/seconds
* puma-mri-grape	4,966 rqs/seconds
* puma-rbx-grape	228 rqs/seconds
* thin-grape 659  rqs/seconds
* puma-padrino	2,247 rqs/seconds
* jruby-padrino	717 rqs/seconds
* thin-padrino	319 rqs/seconds
* trinidad-padrino	2,827 rqs/seconds
* unicorn-padrino	2,034 rqs/seconds
* torqbox-padrino	3,114 rqs/seconds
* rbx-padrino 430 rqs/seconds
* rails-torqbox	1,889 rqs/seconds
* trinidad-rails	2,126 rqs/seconds
* rails-unicorn	3,446 rqs/seconds
* puma-jruby-rails	394 rqs/seconds
* puma-rbx-rails	227 rqs/seconds
* puma-mri-rails	3,960 rqs/seconds
* sinatra-torqbox	2,426 rqs/seconds
* **sinatra-puma	5,981 rqs/seconds**
* sinatra-jruby-puma	779 rqs/seconds
* sinatra-rbx-puma	459 rqs/seconds
* **sinatra-unicorn	5,612 rqs/seconds**
* sinatra-thin	779 rqs/seconds
* sinatra-trinidad	2,663 rqs/seconds


## Measure

Measure performance:

* [Miniprofiler](https://github.com/MiniProfiler/rack-mini-profiler) is a simple but effective mini-profiler
* NewRelic is a paid solution that will give you a lot of insights, esp. which requests are the slowest and where
* Google analytics can also help. For example, high bounce rates can mean either your site content is uninteresting, or it was too slow to load for them. Investigate!

## Ruby code

It is very easy to create new objects and iterate through them with Ruby. What's less known are the consequences. Too much memory will be consumed. The Ruby heap will grow and eventually will take all the memory available.

Write code carefully. The best code is the one that does not exist. Here are different steps:

* Rails is a heavy framework requiring a lot of resources and only be used when programmer time is limited and if it has unique features for the use case. For a lightweight site or an API (web service), Sinatra is a good choice as seen on the benchmarks above.
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

* Decouple long processes to a queue. Typically: image processing, video encoding, sending emails, uploading files, geo requests, etc. These can be in another app in go, java, or whatever tool is best for the job.
* Use a client-side Javascript framework

## Client-Side

* Use React.js or another front-end framework
* Minify javascript and CSS
* Use async, whenever possible
* Display loader gifs and subtle animations to let the user the request is being processed

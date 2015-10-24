#
#
FROM ubuntu:latest
RUN apt-get update
RUN apt-get install -y ruby1.9.1 rubygems git
RUN apt-get clean
RUN gem install bundler
RUN locale-gen en_US en_US.UTF-8
EXPOSE 8080

RUN git clone https://github.com/heri/awesome-deploy.git /opt/sinatra/
RUN cd /opt/sinatra && bundle install

WORKDIR /opt/sinatra
CMD ["unicorn","-d","-c", "unicorn.conf"]
